# cmbx

**Mailbox-control admission gate for cvld.**

`cmbx` will prove that a member controls an email mailbox. It is an authentication gate only; `cvld` receives an opaque proof (keyed hash for deduplication), never the address.

Status: name reserved, no implementation yet.

## Boundaries

What it does:

- Proves mailbox control and returns an opaque gate proof with its trust anchor.

What it never does:

- Authentication only: never used for messaging, notifications or newsletters.
- The email address never reaches `cvld`; low-entropy identifiers are keyed-hashed per tenant.
- Raw factor data never reaches `cvld`: it receives only an opaque proof with its trust anchor. No raw data in output, logs, errors or fixtures.
- No own security primitives: a thin wrapper around the established best-practice library for the factor.

## License

Copyright 2026 Julian Y. Richard Corbet. Licensed under
[LGPL-3.0-only](LICENSES/LGPL-3.0-only.txt)
[WITH LGPL-3.0-linking-exception](LICENSES/LGPL-3.0-linking-exception.txt).
See [LICENSE.md](LICENSE.md).
