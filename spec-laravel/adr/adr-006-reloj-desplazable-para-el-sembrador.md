# ADR-006 · Offsettable clock for the seeder

- **Status:** accepted
- **Context:** T-21 requires the seeder to create 3 overdue loans. However, HU-06 says the loan date is set by
  the server's clock. The API cannot lend "in the past" — the `loaned_at` is always `now()`. We need a way to
  create overdue loans for demonstration purposes without changing production behavior.
- **Decision:** The `Clock` port (D-05) supports a configurable offset via `CLOCK_OFFSET_DAYS` environment
  variable. Default is `0` (no offset). The seeder temporarily sets `CLOCK_OFFSET_DAYS=-20` for a "past phase"
  where it creates 3 loans that will be overdue, then resets to `0` for the normal phase. See
  [ADR-005](adr-005-testigo-de-concurrencia-sin-columna-version.md) for the concurrency mechanism.
- **Alternatives considered:** directly inserting loans with past dates via SQL (violates Art. V — the seeder
  must use the API only); adding a `loaned_at` override in the API (pollutes the contract); waiting 14 days
  (not feasible).
- **Consequences:** the `Clock` port already exists for testing. The offset is a demo-only feature, disabled by
  default. Production behavior is unchanged. The seeder runs in two phases: past (offset −20 days) and normal
  (offset 0).
- **How to verify:** after running the seeder, `GET /loans/overdue` returns exactly 3 lines from the past phase.
