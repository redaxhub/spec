# Phase 1 Discovery Policy

> Canonical reference for how the REDAX Hub UI surfaces campaigns during
> Phase 1. Status: Phase 1 specification freeze. Source of truth: SPEC v1.1
> Section 13.7 and UI_REQUIREMENTS.md.

## The problem

Program-level restrictions (e.g., `merger_type` must be
`SingleProjectMigration`) prevent Phase 2 features from being used in Phase 1.
They do NOT prevent permissionless creation of campaigns: anyone paying 0.5 SOL
can create a `SingleProjectMigration` campaign for any legacy mint, including
impersonation campaigns for popular tokens they do not own.

If the canonical REDAX UI surfaces all on-chain campaigns by default, a single
bad actor can destroy protocol reputation in a single transaction.

## The policy

During Phase 1 (until Verified Tier governance is active in Phase 2), the
canonical REDAX UI (redaxhub.com) operates as a curated discovery layer.

### Default discovery feed

Only campaigns that have undergone manual review by the REDAX team appear in the
default feed. These are labeled "REDAX Reviewed".

### Direct-link access

Users who navigate directly to a campaign address (deep link or share URL) for
an unreviewed campaign see a non-dismissible warning before any campaign details
render.

### Search and listing

Any UI surface that lists multiple campaigns displays a review-status badge for
each campaign:

- REDAX Reviewed (Phase 1 pilot)
- Unreviewed (proceed with caution)

Default sort places reviewed campaigns above unreviewed.

## What this is NOT

This policy is NOT:

- A program-level restriction. The on-chain program does not maintain a
  "reviewed" flag in Phase 1.
- A guarantee of campaign safety. REDAX-reviewed status reflects process review,
  not outcome guarantee.
- Permanent. In Phase 2, this policy transitions to Verified Tier governance
  with on-chain attestation.

## What this IS

This policy IS:

- A UI-layer trust signal during pre-launch.
- A reputation safeguard for REDAX before Verified Tier is operational.
- A balance between permissionless on-chain layer and curated default user
  experience.

## Third-party UIs

Third-party REDAX-compatible UIs MAY adopt different discovery policies. To
claim REDAX-compatible status (used in marketing or in Verified Tier eligibility
in Phase 2), they MUST adhere to this Discovery Policy during Phase 1.

## Phase 2 transition

In Phase 2:

- "REDAX Reviewed" pilot label is replaced by "Verified" (Verified Tier criteria
  met).
- Campaigns that fail Verified Tier display "Unverified".
- The unreviewed-campaign warning banner remains for direct-link to campaigns
  without Verified status.

## Cross-references

- SPEC v1.1 Section 13.7
- UI_REQUIREMENTS.md - Phase 1 Discovery Policy section
- INVARIANTS.md - INV-OG-15
- THREAT_MODEL.md - TM-OG-13
