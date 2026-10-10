# Mailbox integrations, costs and credentials

| Integration | Implemented | Qualification limit |
|---|---|---|
| Twilio Verify v2 | Create/check, email through the service's configured SendGrid integration, correlated response validation, full-result option | Wire contract exercised with inert transports; live delivery not run |
| Other providers | Trusted `Adapter` port plus shared authenticated identity projection | No other concrete provider is implemented or enabled |

The adapter wraps maintained `http`, `url`, Serde and RustCrypto implementations;
Twilio publishes official SDKs for several languages but no Rust SDK in its
Verify guide. This small form/response integration avoids introducing another
language runtime or claiming an unmaintained SDK as maintained.

## Domain knowledge

`email_address` parses addresses; URL/IDNA normalization canonicalizes domain
names. Local-part case, dots and plus aliases are preserved; guessing provider
alias rules would merge unrelated mailboxes. Quoted local parts and address
literals are currently refused rather than given unqualified identity semantics.
Different aliases can reach one inbox; the fact is control of the verified
canonical address, not one-person or provider-wide mailbox uniqueness.

The host configures the SendGrid email integration on its Verify service. Its
SendGrid credential is not needed by this adapter: it remains in that external
service setup. The library neither registers an integration nor sends newsletters.

[Mailbox parser](https://docs.rs/email_address/latest/email_address/),
[Twilio Email integration](https://www.twilio.com/docs/verify/email).


## Credentials and transport

Inject a Twilio account SID (`AC...`), Verify service SID (`VA...`) and its auth
token explicitly. The token stays in private RAM; the Basic authorization header
is marked sensitive. IDs/configuration are separate from secrets. Credential
rotation keeps account/service identity and pending context stable. The stable
HMAC identity key is unrelated to that token and requires a planned migration
when changed. Do not forward either secret to a member.

The caller injects a trusted HTTPS transport. Authenticate the fixed
`verify.twilio.com` endpoint, bound time and response size, and disable redirects,
retries, request/response logging and caches. Only a definitively unstarted
transport can report `NotDispatched`; HTTP errors and ambiguous delivery retain
cost exposure. The adapter never converts arbitrary supplied JSON into a proof.

Owned private buffers are zeroized on drop. HTTP/header/parser libraries may
make ordinary memory copies; host swap/dumps are outside this crate's control.
Pending destination/session data remains in RAM only. Requested durable storage,
registration and cross-process operation identity belong to the caller.

## Cost and execution modes

There is no hard-coded tariff or assumption that a channel is cheapest. The host
supplies an exact request-bound quote or a defensible maximum, denomination,
source revision and expiry. Unknown, stale, mismatched or over-budget offers are
refused before dispatch. Budgets cover the whole cascade, including failed
attempts. Start and check are separately bound operations; account for both
transport and verification fees rather than treating the OTP check as free.
The library conservatively reports reserved exposure, not invented actual fees.

Twilio's pricing depends on channel/destination/account terms and may include
multiple components. Consult [Verify pricing](https://www.twilio.com/en-us/verify/pricing)
and the configured service tariff. Local preparation and planning cannot contact
quote endpoints. Provider-managed routing must fit the supplied maximum.

Real dispatch accepts explicit Production credentials/transport/permits.
Sandbox is refused: this adapter does not implement a qualified Verify sandbox,
and trial accounts must not be presumed free. [Twilio test credentials](https://www.twilio.com/docs/iam/test-credentials)
cover selected APIs; they do not justify labelling a Verify call free.
Simulation uses the shared pure planner and explicit dummy outcomes; it has no
transport or verified-result constructor. No live calls are authorized here.

## Evidence and upstream reference

Fixture tests use actual HTTP requests, form encoding, normalization, HMAC and
JSON parsing. They cover correlation, privacy, scoped limits, pending/rejected/
uncertain outcomes, changed quotes/modes and costed provider/channel fallback.
Live carrier/mail delivery and service provisioning are not executed or claimed
qualified. Maintained dependency ranges follow current compatible releases;
committed CI locks record the exact tested graph.

[Verification API](https://www.twilio.com/docs/verify/api/verification),
[verification-check API](https://www.twilio.com/docs/verify/api/verification-check).

## Extending the domain

`Adapter` is a trusted application-code boundary: an implementation must authenticate
its provider, independently establish control, bind the exact request/context and
supply an exclusive evidence expiry. `verify_with` checks the cost permit before
calling it and applies the same canonicalization, scoped HMAC and requested-data
projection as the built-in integration. An adapter is not a member-supplied JSON
object; accepting unauthenticated fields in one violates this contract.

`simulate` accepts explicit dummy outcomes and uses the real planner and
normalizer. Its `Simulated` output cannot be used as `Verified` or reserve a
registration. The open verification port currently accepts Production only; a
future provider sandbox must have distinct evidence and credential types.

## Provider and protocol arsenal

Primary documentation reviewed 2026-10-10. **Implemented** means the local
fixture-tested path described here, not live-provider qualification.
**Extension-compatible** means the existing trusted interface can carry the
integration; provider-specific authentication/correlation still needs work.
**Candidate** means researched only. **Unsupported** means the stated guarantee
must not be inferred. These are integration options, not activated services.

Every future network route needs an explicit injected credential, account/mode
binding, bounded cost quote with provenance and expiry, atomic host reservations,
fresh dispatch deadlines, rich private reports and negative fixtures. Unknown
prices block dispatch. Cost bases below identify what a host must obtain; they
are not current numeric quotations or permission to contact the provider.

| Ecosystem and source | Status / reuse path | Credentials and cost basis | Semantic limit |
|---|---|---|---|
| [Twilio Verify Email](https://www.twilio.com/docs/verify/email) | Implemented through the configured Verify/SendGrid service. | Inject Twilio account/service SID and auth token; SendGrid setup remains with the host/provider. Account tariff covers start/check and delivery. | Approved verification of the exact address; no alias merging or one-person inference. |
| [Vonage Verify v2](https://developer.vonage.com/en/verify/guides/verify-migration-guide) | Extension-compatible candidate: email workflow, maintained API/SDK. | Inject application JWT/appropriate supported credentials; quote the configured pricing model and complete workflow. | Authenticate completed verification for the same request and address. A channel switch requires compatible requested meaning. |
| [Infobip 2FA Email](https://www.infobip.com/docs/2fa-service) | Extension-compatible candidate: official PIN send/check API and SDKs. | Inject account base URL, scoped API credential, application and template IDs; configured email/verification tariff. | PIN ID and destination correlation are mandatory; a delivery receipt alone is insufficient. |
| [Stytch Email OTP](https://stytch.com/docs/api-reference/consumer/api/otp/via-email/send) | Extension-compatible candidate: official API with Rust SDK examples. | Project ID/secret and explicit test/live endpoint; host-verified project tariff for selected operations. | Sending is not authentication. Preserve method/address linkage and expiry; do not substitute a generic session/user ID for the mailbox. |
| [Amazon SES](https://docs.aws.amazon.com/ses/latest/dg/send-email-concepts-credentials.html) | Candidate delivery component: prefer official AWS Rust SDK. **Unsupported as standalone mailbox proof.** | Inject scoped AWS credentials/session tokens or separate SMTP credentials; region/account send tariff and any auxiliary charges. | SES sender/domain verification concerns the sender. Recipient control requires a separately designed challenge and atomic host state. |
| [Resend](https://resend.com/docs/api-reference/emails/send-email) | Candidate delivery component: official API/SDK. **Unsupported as standalone mailbox proof.** | Inject scoped API key and configured sender; account-plan/send quote with validity. | Send IDs and delivery webhooks do not establish recipient control. A complete verifier would need explicit challenge expiry and consumption. |

Mailbox delivery and mailbox verification are separate capabilities. Prefer
providers that own a well-defined verification ceremony; do not add our own OTP
service merely to label a mail sender as a ready verification integration.
