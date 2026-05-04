# REDAX Hub Threat Model v1.1

> Attack vectors and defense layers for the Phase 1 and Phase 2 protocol design.

### Output Governance & Multi-Project Merger Threat Vectors

#### TM-OG-1 - Impersonation campaign

- **Attack Scenario:** Adversary opens a SingleProjectMigration campaign for a
  legacy mint they do not own.
- **Defense Layer:** Phase 1: only SingleProjectMigration. Discovery Policy
  keeps unreviewed campaigns out of default UI. Phase 2: optional
  `LegacyProjectAttestation`.
- **Phase:** Both

#### TM-OG-2 - Fake official merger

- **Attack Scenario:** Adversary opens OfficialMultiProjectMerger with own
  attestation but no co-mergee attestation.
- **Defense Layer:** Phase 1: rejected by program. Phase 2: R-OG-5 enforcement.
- **Phase:** Phase 2

#### TM-OG-3 - Disguised acquisition

- **Attack Scenario:** A project invites holders of another project to convert
  into "merger" output that is solo-owned.
- **Defense Layer:** `merger_type` field separation. UI displays attestation
  list explicitly.
- **Phase:** Both

#### TM-OG-4 - Multisig signer collusion

- **Attack Scenario:** "Multisig" output_governance where all signers
  controlled by one party.
- **Defense Layer:** Phase 2 Verified Tier multisig diversity review. UI
  displays signer count and multisig age.
- **Phase:** Phase 2

#### TM-OG-5 - Post-campaign authority transfer rugpull

- **Attack Scenario:** `TransferToGovernance` set; output_governance
  compromised, allowing unbounded mint.
- **Defense Layer:** Default `Revoked`. R-OG-11 disqualifies
  TransferToGovernance from Verified Tier. UI warns.
- **Phase:** Both

#### TM-OG-6 - Disclaimer hiding

- **Attack Scenario:** Frontend hides Unofficial disclaimer in footer or
  tooltip.
- **Defense Layer:** UI Requirements "Prohibited UI Behaviors."
- **Phase:** Phase 2

#### TM-OG-7 - Attestation revocation race

- **Attack Scenario:** Project owner submits attestation, then attempts revoke
  before campaign start; adversary races to convert.
- **Defense Layer:** `revoked` flag enforced pre-campaign-start only. Phase 2
  timelock: 24h between final attestation and campaign activation.
- **Phase:** Phase 2

#### TM-OG-8 - Treasury claim dispute

- **Attack Scenario:** `campaign_admin` and `output_governance` are different
  parties; admin claims protocol_fee_vault.
- **Defense Layer:** R-OG-10 separation. Treasury policy locked at creation.
  `campaign_admin` cannot withdraw `protocol_fee_vault` (program-enforced).
- **Phase:** Both

#### TM-OG-9 - Fake community vote evidence

- **Attack Scenario:** Adversary opens CommunityLedMigration with fabricated
  vote URI/hash.
- **Defense Layer:** Phase 2 Verified Tier manual review. UI displays vote tally
  + Realms verification.
- **Phase:** Phase 2

#### TM-OG-10 - Cross-campaign authority confusion

- **Attack Scenario:** Same `output_governance` reused across multiple
  campaigns; one compromise affects all.
- **Defense Layer:** Per-campaign isolation. UI warns if entered governance
  address is already used.
- **Phase:** Both

#### TM-OG-11 - Treasury terminology confusion

- **Attack Scenario:** Holder confuses `protocol_fee_vault` with project's
  treasury.
- **Defense Layer:** Naming refactor: the REDAX protocol fee vault was
  previously referred to as campaign treasury. UI explicit labels. R-OG-1
  documented.
- **Phase:** Both

#### TM-OG-12 - ExistingOutputMint mint authority hijack

- **Attack Scenario:** Phase 2 ExistingOutputMint mode: campaign creator
  transfers mint_authority to Campaign PDA, but reverts at last moment.
- **Defense Layer:** Phase 1: ExistingOutputMint rejected. Phase 2: program
  checks `mint_authority == Campaign PDA` at every convert.
- **Phase:** Phase 2

#### TM-OG-13 - Permissionless scam in default UI feed

- **Attack Scenario:** Adversary creates a permissionless SingleProjectMigration
  campaign and expects to be discovered by holders via default UI.
- **Defense Layer:** Phase 1 Discovery Policy: canonical UI only surfaces
  REDAX-reviewed campaigns by default. Direct-link access requires
  acknowledgment of unreviewed status (R-OG-12).
- **Phase:** Phase 1

#### TM-OG-14 - Freeze authority post-launch hijack

- **Attack Scenario:** Hypothetical: if Campaign PDA held freeze authority, a
  logic bug or upgrade exploit could allow holders' tokens to be frozen.
- **Defense Layer:** Phase 1: `freeze_authority MUST be None` (R-OG-14). Class
  of attacks structurally eliminated. Phase 2 evaluation only with explicit
  justification.
- **Phase:** Phase 1
