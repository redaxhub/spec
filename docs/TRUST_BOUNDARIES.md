# Trust Boundaries

> Canonical boundaries derived from SPEC v1.1 Section 13.

## Protocol vs project

- The REDAX protocol owns and controls the `protocol_fee_vault` only.
- The project's own treasury is `project_treasury`, controlled by `output_governance` and kept off-protocol.
- `output_governance` has no authority over `protocol_fee_vault`.

## Campaign PDA boundary

- The Campaign PDA is a bounded mint authority. It cannot mint beyond the Total Output Cap.
- The Campaign PDA cannot change economic parameters after creation, including `treasury_policy`, `output_governance`, or `authority_policy`.

## UI boundary (Phase 1)

- Discovery is a UI-layer policy in Phase 1. The protocol allows permissionless campaign creation.
- The canonical UI must surface only reviewed campaigns by default and must warn on direct-link access to unreviewed campaigns.

## Attestation boundary (Phase 2)

- `LegacyProjectAttestation` defines on-chain proof of project consent.
- Off-chain sources require Verified Tier review before contributing to activation.
