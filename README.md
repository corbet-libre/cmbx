# cmbx

Reusable mailbox verification, opaque comparison identities and caller-defined
registration limits. `cvld` is one consumer; explicit callers can request complete
upstream responses in RAM. The library has no database connection.

The first concrete adapter implements Twilio Verify email through the service's configured SendGrid integration. Maintained
libraries normalize identifiers and construct authenticated requests. Shared
`cfbk` plans caller-selected provider/channel cascades under explicit cost bounds;
unknown costs cannot authorize dispatch. Independent provider checks establish
control, never one-human uniqueness.

See [the contract](docs/CONTRACT.md) and [provider, credential and cost notes](docs/PROVIDERS.md).
Live provider flows remain unexecuted; remote checks and independent review are
required before qualification.

## License

Copyright 2026 Julian Y. Richard Corbet. Licensed under
[LGPL-3.0-only](LICENSES/LGPL-3.0-only.txt)
[WITH LGPL-3.0-linking-exception](LICENSES/LGPL-3.0-linking-exception.txt).
See [LICENSE.md](LICENSE.md).

## Continuous verification

Dependency updates follow main and are tested against one CI-resolved lockfile.
Line and branch coverage target 100%; failures remain blocking. See
[the coverage contract](docs/COVERAGE.md) for measurement and exclusions.
