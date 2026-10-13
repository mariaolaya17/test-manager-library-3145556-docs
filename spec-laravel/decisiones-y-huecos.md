# Gaps and Ambiguities — Decisions Made

Each gap found while reading the spec critically, with the decision taken and its justification. This is what
demonstrates SDD: the spec is not blindly followed; it is read, questioned, and where it is silent or
contradictory, a decision is made and documented.

---

## H-01 · Concurrency witness: no `version` column in data-model.md

**Where:** ADR-002 and D-04 mention a "version token per book", but `data-model.md` §3 has no `version` column,
and the document wins (Art. X).

**Decision:** Use `available_copies` itself as the witness (compare-and-set). The `UPDATE` statement only
succeeds if the value hasn't changed since read. No column is added. See
[ADR-005](adr/adr-005-testigo-de-concurrencia-sin-columna-version.md).

**Justification:** adding a `version` column would contradict `data-model.md` §3, which is the authoritative
document. The compare-and-set on `available_copies` achieves the same optimistic concurrency guarantee.

---

## H-02 · GET /health: base path vs /api/v1

**Where:** T-01 and CA-01.3 reference `GET /health`, but the contract §1 says base is `/api/v1`.

**Decision:** Expose both `/api/v1/health` and `/health`.

**Justification:** the contract says "todo endpoint salvo el inicio de sesión y `/health`", implying `/health`
is at the root. But the base path is `/api/v1`. Exposing both satisfies both readings.

---

## H-03 · GET /books/{id}/cover: mentioned but not in endpoint table

**Where:** contract §3.4 mentions `cover_key` and says "para pintar la portada se usa `GET /books/{id}/cover`",
but the endpoint table doesn't list it.

**Decision:** Implement it (authenticated; binary or 404).

**Justification:** the text explicitly describes the endpoint. The table is incomplete. We implement what the
text says.

---

## H-04 · /loans/overdue vs /loans/{id} collision

**Where:** contract §3.6 lists both `GET /loans/overdue` and `GET /loans/{id}`.

**Decision:** Declare `overdue` before `{id}` in routes and restrict `{id}` to UUID.

**Justification:** Laravel matches routes in declaration order. By declaring `overdue` first and constraining
`{id}` to UUID regex, there is no ambiguity.

---

## H-05 · Seeder must create 3 overdue loans, but the API dates the loan with its own clock

**Where:** T-21 requires 3 overdue loans, but HU-06 says the loan date is set by the server's clock. You cannot
lend "in the past".

**Decision:** Clock with configurable offset only in demo (`CLOCK_OFFSET_DAYS`, disabled by default): the seeder
runs a phase with the API at −20 days. See
[ADR-006](adr/adr-006-reloj-desplazable-para-el-sembrador.md).

**Justification:** the `Clock` port already exists (D-05). Adding an offset for demo purposes does not affect
production behavior (default is 0). The seeder temporarily sets the offset to create overdue loans.

---

## H-06 · CA-06.4 "422 with current count": where does the count go?

**Where:** spec and contract say "422 con el conteo actual".

**Decision:** In `message` and in `details`: `[{field:"member_id", issue:"active_loans=3"}]`.

**Justification:** the error body has a `details` array for exactly this kind of structured information. The
message is in Spanish for the user; the details are structured for the client.

---

## H-07 · Laravel validation returns its own 422 format

**Where:** RNF-4 requires the unique error body for all errors.

**Decision:** Global handler in `bootstrap/app.php` that translates `ValidationException`,
`AuthenticationException`, `ModelNotFound`, `NotFoundHttp`, and `Throwable` to the unique body.

**Justification:** without this, Laravel returns its own error format. The handler ensures RNF-4 compliance.

---

## H-08 · Schema `library` and extensions: who creates them?

**Where:** ADR-001 says the schema is owned by migrations, but the schema itself must exist first.

**Decision:** The first migration runs `CREATE SCHEMA IF NOT EXISTS library`; infra only creates the engine,
user, and volume.

**Justification:** the schema is part of the DDL owned by migrations (Art. V). Infra creates only the empty
database and user.

---

## H-09 · PUT /books/{id} does not include ISBN

**Where:** contract §3.4 says PUT replaces title, author, category, and total — but not ISBN.

**Decision:** ISBN is immutable after creation.

**Justification:** the contract explicitly lists what PUT replaces. ISBN is not in the list. Making it immutable
avoids the complexity of re-checking uniqueness against other active books.

---

## H-10 · Filter `status` in GET /loans on a non-stored status

**Where:** HU-08 says list by status, but Art. VII says status is calculated, not stored.

**Decision:** Calculate in SQL with `EXISTS` over `loan_item` (`returned_at IS NULL`, `due_at < now`).

**Justification:** the status is derived from the items. A SQL `EXISTS` subquery computes it without storing
anything.

---

## H-11 · Report `from`/`to` are dates "both inclusive" and instants are UTC

**Where:** contract §3.7 says `from` and `to` are dates, both inclusive.

**Decision:** `loaned_at >= from 00:00Z AND loaned_at < (to + 1 day) 00:00Z`.

**Justification:** "both inclusive" means the entire day of `to` is included. Using `< (to + 1 day)` is the
standard way to express a date range in UTC.

---

## H-12 · Books given de baja in past loans: GET /loans/{id} must still work

**Where:** ADR-003 says books are logically deleted, but past loans reference them.

**Decision:** The lines use the frozen values; no join to the catalog.

**Justification:** `loan_item` stores `book_title`, `isbn`, and `category_name` as frozen copies (ADR-004). The
loan detail query reads from `loan_item` directly, never joining to `book`. A logically deleted book does not
affect past loans.
