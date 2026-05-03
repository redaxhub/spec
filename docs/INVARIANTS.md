# REDAX Hub Invariants v1.1

> Security and correctness invariants for Campaign and related accounts.

### Output Governance Invariants

These invariants MUST hold for all Campaign and LegacyProjectAttestation
accounts at all times.

- **INV-OG-1** - In Phase 1, an Campaign with
  `merger_type != SingleProjectMigration` MUST be rejected at `create_campaign`
  with error `Phase2FeatureNotEnabled`. The rejection MUST emit a `msg!()` log
  line for off-chain analytics; no canonical Anchor event is emitted (failed
  transactions roll back state).

- **INV-OG-2** - Every Campaign PDA MUST have a non-zero `output_governance`
  value.

- **INV-OG-3** - The REDAX `protocol_treasury` (or any address derived from
  `ProtocolConfig.owner_multisig`) MUST NEVER be assigned as
  `output_governance`. Program enforces this at `create_campaign`.

- **INV-OG-4** - The Campaign PDA, when acting as `mint_authority`, MUST NOT
  mint amounts exceeding `total_out_cap - total_out_minted`. Bound applies
  regardless of `authority_policy` value.

- **INV-OG-5** - Before any holder `convert_exact_in` transaction, the canonical
  REDAX UI MUST display: `merger_type`, `output_mint_mode`, `metadata_strategy`,
  `output_governance`, `campaign_admin`, `authority_policy`,
  `protocol_fee_vault` address, and review status. Validated via Verified Tier
  compliance review.

- **INV-OG-6** - The `authority_policy` field MUST NOT change after campaign
  creation. Update attempts MUST fail with `AuthorityPolicyImmutable`.

- **INV-OG-7** - A `LegacyProjectAttestation` is valid only if signed by an
  authorized source per R-OG-6 table. Phase 2 program MUST validate sources 0-2
  on-chain when allowlist applies; sources 3-5 require
  `verified_tier_reviewed = true` before contributing to
  `received_attestations_count`.

- **INV-OG-8** - For campaigns with
  `merger_type IN {UnofficialMigrationOffer, CommunityLedMigration}`,
  `disclaimer_hash` MUST be non-zero at convert time. (Phase 2 only.)

- **INV-OG-9** - For Phase 2 `OfficialMultiProjectMerger`, `output_governance`
  MUST be a non-zero address; multisig/DAO nature verified via Verified Tier
  review.

- **INV-OG-10** - `campaign_admin` MUST NOT be able to: withdraw
  `protocol_fee_vault`, change `output_governance`, change `authority_policy`,
  change `merger_type`, change `output_mint_mode`, change `metadata_strategy`,
  change `disclaimer_hash`, change `required_attestations_count`, or modify
  Metaplex metadata.

- **INV-OG-11** - Any Campaign with `authority_policy == TransferToGovernance`
  MUST have `verified_tier == None`. Phase 2 `grant_verified` MUST reject
  TransferToGovernance campaigns.

- **INV-OG-12** - In Phase 1, only
  `output_mint_mode == ProgramCreatedOutputMint` MUST be accepted. Other values
  rejected with `Phase2FeatureNotEnabled`.

- **INV-OG-13** - In Phase 2 `ExistingOutputMint` mode, the program MUST verify
  `output_mint.mint_authority == Campaign PDA` at every convert.

- **INV-OG-14** - `protocol_fee_vault` MUST be controlled exclusively by the
  Campaign PDA per locked `treasury_policy`. `output_governance` has NO
  authority over `protocol_fee_vault`.

- **INV-OG-15** - During Phase 1, the canonical REDAX UI MUST default to
  surfacing only REDAX-reviewed campaigns. Direct-link access to unreviewed
  campaigns MUST be permitted but preceded by the unreviewed-campaign warning
  banner specified in UI_REQUIREMENTS. Phase 1 review status is a UI-layer
  property; the on-chain program does not maintain a "reviewed" flag (Phase 2
  introduces `verified_tier` for this purpose).

- **INV-OG-16** - In Phase 1 ProgramCreatedOutputMint mode, the output mint's
  `freeze_authority` MUST be `None`. Program MUST verify this at
  `create_campaign` and at every convert (defense-in-depth: detects
  post-creation tampering attempts even though Phase 1 program logic does not
  allow such tampering).

- **INV-OG-17** - In Phase 1, only `metadata_strategy == MetaplexMetadataCPI`
  MUST be accepted. The Metaplex Token Metadata account MUST be created in the
  same transaction as the output mint and Campaign PDA. Update authority on the
  metadata account MUST be `output_governance`.
