# Output Metadata Policy

> Canonical policy for output mint metadata (SPEC v1.1 Section 13.6).

## Phase 1 mandatory strategy

Phase 1 requires `metadata_strategy = MetaplexMetadataCPI`.

For SPL Token output mints created by REDAX in Phase 1:
- `create_campaign` includes a CPI to the Metaplex Token Metadata Program.
- The CPI creates a metadata account derived from the output mint.
- Metadata fields: `name` (<= 32 chars), `symbol` (<= 10 chars), `uri` (<= 200 chars).
- Update authority on the metadata account is `output_governance`.
- Metadata is created in the same transaction as the output mint and Campaign PDA.

## Input validation (Phase 1)

- `name` length <= 32 bytes
- `symbol` length <= 10 bytes
- `uri` length <= 200 bytes
- The program does not validate the off-chain JSON contents.

## Phase 2 strategies (planned)

- `Token2022MetadataExtension` (Token-2022 output)
- `CreatorDeclaredPostCampaign` (advanced, with explicit warning)

## Rejected in Phase 1

- `NoOnChainMetadata`
- `Token2022MetadataExtension`
- `CreatorDeclaredPostCampaign`
