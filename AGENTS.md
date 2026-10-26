# cmbx agent notes

Work in a lane (`~/agents/scratch/lane-new <lane>`); never edit `~/agents/scratch/ws`.
Run from the lane root (`<lane>/ws`); `cbuild <lane> ...` builds there.

Build and test (quote the verdict line, never infer from a pipe):
- `cbuild <lane> fmt -p cmbx -- --check` — must be clean
- `cbuild <lane> test -p cmbx --locked` — lib `24`, doc `0` passed, exit 0; long, background it
- `cbuild <lane> clippy -p cmbx --locked --all-targets -- -D warnings` — exit 0
- `cbuild <lane> check -p cmbx --locked --target wasm32-unknown-unknown` — exit 0
- Full gate (first-party refresh, coverage, `.ci/test-clock-regression.py`
  frozen-clock control) runs in CI; see `.ci/verify.sh`, `docs/COVERAGE.md`.

Land: `cargo fmt` in touched repos, rebase on main (never force-push),
`cbuild <lane> xtask land --dry`, then `xtask land`.

Invariants (rule -> enforcing test):
- Auth/session mismatch never creates a fact
  -> `tests::authentication_response_and_session_mismatches_never_create_a_fact`
- Malformed or uncertain effects fail closed; no retry or fallback
  -> `tests::malformed_responses_and_possible_effects_fail_closed_without_retry`
- Cascade reserves failed costs and stops on pending; one dispatch
  reservation per concurrent prepared request
  -> `tests::configured_and_cost_order_cascades_reserve_failed_costs_and_stop_on_pending`,
  `tests::concurrent_prepared_requests_acquire_only_one_dispatch_reservation`
- Deadlines cover reservation, reply and fallback; rollback refused
  -> `tests::clock::deadlines_cover_reservation_reply_and_fallback_without_releasing_exposure`,
  `tests::clock::synchronous_registration_checks_commit_and_completion_time`
- Comparison identity is not uniqueness; parser preserves case and aliases
  -> `tests::maintained_mailbox_parser_preserves_local_part_case_and_aliases`,
  `tests::ambiguous_address_syntax_is_refused_and_international_domains_are_canonical`
- Private bytes stay redacted; errors carry no destinations or tokens
  -> `tests::complete_report_preserves_bounded_pending_refused_and_malformed_responses`,
  `tests::upstream_http_failure_bytes_are_explicit_private_and_stop_fallback`

Pointers: `src/flow.rs`, `src/identity.rs`, `src/twilio.rs`, `src/adapters.rs`,
`docs/CONTRACT.md`, `docs/PROVIDERS.md`, `.ci/test-clock-regression.py`.
Commits: explicit paths, imperative, author per `~/agents/AGENT.md`; no AI trailers.
`publish=false`; LGPL-3.0-only WITH LGPL-3.0-linking-exception; no GPL/AGPL-only deps.
