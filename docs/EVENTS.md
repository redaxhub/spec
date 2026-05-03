# REDAX Hub Events v1.1

> Canonical event schema for off-chain indexing. Phase 2 events are documented
> placeholders until activated.

### Output Governance & Attestation Events

These events are emitted by the REDAX program for off-chain indexing.

```rust
#[event]
pub struct CampaignTypeSet {
    pub campaign: Pubkey,
    pub merger_type: u8,
    pub output_mint_mode: u8,
    pub metadata_strategy: u8,
    pub set_at: i64,
}

#[event]
pub struct OutputGovernanceSet {
    pub campaign: Pubkey,
    pub output_governance: Pubkey,
    pub authority_policy: u8,
    pub set_at: i64,
}

#[event]
pub struct ProtocolFeeVaultDerived {
    pub campaign: Pubkey,
    pub protocol_fee_vault: Pubkey,
    pub treasury_policy: u8,
    pub derived_at: i64,
}

#[event]
pub struct OutputMintMetadataCreated {
    pub campaign: Pubkey,
    pub output_mint: Pubkey,
    pub metadata_account: Pubkey,
    pub metadata_uri_hash: [u8; 32],
    pub update_authority: Pubkey,
    pub created_at: i64,
}

#[event]
pub struct LegacyProjectAttested {
    pub campaign: Pubkey,
    pub legacy_mint: Pubkey,
    pub project_owner: Pubkey,
    pub attestation_authority_source: u8,
    pub evidence_hash: [u8; 32],
    pub signed_at: i64,
}

#[event]
pub struct LegacyProjectAttestationVerifiedReviewed {
    pub campaign: Pubkey,
    pub legacy_mint: Pubkey,
    pub reviewed_by_multisig: Pubkey,
    pub reviewed_at: i64,
}

#[event]
pub struct LegacyProjectAttestationRevoked {
    pub campaign: Pubkey,
    pub legacy_mint: Pubkey,
    pub project_owner: Pubkey,
    pub revoked_at: i64,
}

#[event]
pub struct OfficialMergerActivated {
    pub campaign: Pubkey,
    pub required_attestations_count: u8,
    pub received_attestations_count: u8,
    pub activated_at: i64,
}

#[event]
pub struct AuthorityPolicyLocked {
    pub campaign: Pubkey,
    pub authority_policy: u8,
    pub locked_at: i64,
}

#[event]
pub struct DisclaimerHashSet {
    pub campaign: Pubkey,
    pub disclaimer_hash: [u8; 32],
    pub set_at: i64,
}
```

### Event Emission Rules

- `CampaignTypeSet` - Emitted once per campaign at successful `create_campaign`.
  Includes `output_mint_mode` and `metadata_strategy`.
- `OutputGovernanceSet` - Emitted once per campaign at `create_campaign`.
- `ProtocolFeeVaultDerived` - Emitted once per campaign at `create_campaign` to
  make the renamed vault address discoverable.
- `OutputMintMetadataCreated` - Phase 1 onwards. Emitted after Metaplex metadata
  CPI succeeds in `create_campaign`. Phase 2 may emit a different event for
  Token2022MetadataExtension or CreatorDeclaredPostCampaign strategies.
- `LegacyProjectAttested` - Phase 2 only. Emitted on every successful
  `submit_legacy_attestation`.
- `LegacyProjectAttestationVerifiedReviewed` - Phase 2 only. Emitted when REDAX
  multisig reviews off-chain attestation sources (3, 4, 5).
- `LegacyProjectAttestationRevoked` - Phase 2 only.
- `OfficialMergerActivated` - Phase 2 only. Emitted when
  `official_attestation_status` transitions to `FullyAttested`.
- `AuthorityPolicyLocked` - Emitted once per campaign at `create_campaign`.
- `DisclaimerHashSet` - Phase 2 only. Emitted at `create_campaign` if
  `disclaimer_hash != 0`.

### On Failed Transaction Analytics (NOT a canonical event)

When a `create_campaign` attempt fails due to a Phase 1 enforcement boundary
(e.g., `merger_type != SingleProjectMigration`,
`output_mint_mode != ProgramCreatedOutputMint`,
`metadata_strategy != MetaplexMetadataCPI`, or `freeze_authority != None`), the
program emits a `msg!()` log line:

```
REDAX_PHASE2_FEATURE_REJECTED: campaign_attempt={pubkey} feature={feature_name} attempted_value={value}
```

Where `feature_name` is one of: `merger_type`, `output_mint_mode`,
`metadata_strategy`, `freeze_authority`.

This log line is captured in transaction failure logs but is NOT emitted as a
canonical Anchor event. Failed Solana transactions roll back all state changes
including emitted events; therefore using `emit!()` for rejection paths is
technically incorrect. Off-chain indexers that wish to track Phase 2 feature
demand MUST query failed transactions on the program ID and parse log lines
matching the pattern above.

This is an intentional design choice:

- Successful state changes use canonical events (chain-persistent).
- Failed transaction analytics use log lines (transaction-receipt only).
- Off-chain indexers handle each appropriately.

### Off-chain consumer requirements

Any indexer consuming canonical events MUST:

- Reconstruct campaign state from event stream alone (event-sourced model)
- Phase 2: Detect missing `LegacyProjectAttested` events for
  `OfficialMultiProjectMerger` campaigns
- Phase 2: Index `LegacyProjectAttestationRevoked` events and reflect in UI
  immediately

Any indexer that wishes to track Phase 2 feature demand MAY:

- Query failed transactions on the REDAX program ID
- Parse `REDAX_PHASE2_FEATURE_REJECTED` log lines from transaction logs
- Aggregate by feature_name to inform Phase 2 prioritization
