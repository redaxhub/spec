# Roadmap

> Advisory roadmap derived from SPEC v1.1 Section 13.10.

## Phase 1 (Q4 2026 mainnet target)

- Add all struct fields for Phase 2 compatibility.
- Enforce Phase 1 constraints:
  - `merger_type == SingleProjectMigration`
  - `output_mint_mode == ProgramCreatedOutputMint`
  - `metadata_strategy == MetaplexMetadataCPI`
  - `freeze_authority == None`
- Activate the Discovery Policy in the canonical UI.

## Phase 2 (Q1-Q2 2027)

- Activate `LegacyProjectAttestation`.
- Enable multi-project and community-led campaign types.
- Enable ExistingOutputMint mode.
- Enable Token-2022 metadata extension and CreatorDeclaredPostCampaign strategy.
- Transition Discovery Policy to Verified Tier governance.

## Phase 3

- Subject to legal clearance.
