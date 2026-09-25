# Agent instructions

Write all code comments and documentation in English.

## Product boundary

- Mailbox-control gate for cvld: proof of control over an email mailbox, for authentication only.
- Proves mailbox control and returns an opaque gate proof with its trust anchor.
- Authentication only: never used for messaging, notifications or newsletters.
- The email address never reaches `cvld`; low-entropy identifiers are keyed-hashed per tenant.
- Raw factor data never reaches `cvld`: it receives only an opaque proof with its trust anchor. No raw data in output, logs, errors or fixtures.
- No own security primitives: a thin wrapper around the established best-practice library for the factor.
- This crate is LGPL-3.0-only WITH LGPL-3.0-linking-exception. Do not add implementation code available only under the full GPL or AGPL.
- Tests use fixtures only; no live calls to third-party providers.

## Quality boundary

- `cargo fmt --check`, `cargo clippy --all-targets` (no warnings),
  `cargo test` — all green before every commit. No local workstation
  builds; use GHA, Crow as fallback.
