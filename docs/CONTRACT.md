# Mailbox gate boundary

The reserved mailbox gate checks control of one mailbox for authentication
only. It does not own user messaging, notifications or newsletters. An address
must never reach cvld or persisted member state; any low-entropy deduplication
fingerprint must be keyed and scoped to its community, using maintained
cryptographic libraries.

One invocation checks one provider result. `cfbk` owns provider ordering and
fallback under `crbk` requirements. No confidence ranking, raw-factor logging,
local security primitive or accepting placeholder is allowed.

No implementation or qualified adapter exists yet. Tests are fixture-only and
do not authorize live provider calls, registration, spending or outreach.
Empty production coverage must remain a blocking failure until the actual
adapter and its adversarial verification tests exist.
