# Tasks — Manager Library (Laravel + React)

Each task is complete when its **evidence** exists and can be executed (Art. XII). Status: **done** / **partial** / **pending**.

| # | Task | Depends on | Evidence |
|---|---|---|---|
| T-01 | Project skeleton + `GET /health` | — | `GET /api/v1/health` returns `{"status":"ok"}`; container starts |
| T-02 | Initial migration: 6 tables, FK, unique indexes, 5 seeded categories | T-01 | `data-model.md` §10 queries return what is documented; no column with `DEFAULT` |
| T-03 | Domain: `Isbn`, `Quantity`, `DateRange` (value objects) | T-01 | Unit tests: 13-digit ISBN, quantity > 0, end >= start |
| T-04 | Domain: `Book` (`withdraw`, `restock`, logical delete) | T-03 | Tests: never < 0, never > total |
| T-05 | Domain: `Loan` (`addItem`, `returnItem`, calculated status, 14 days, max 3) | T-04 | Tests with fake `Clock`: `open`, `overdue`, `closed` |
| T-06 | Domain: `Member` and `User` (name normalization, valid role) | T-03 | `"Ana "` becomes `ana`; `admin` role via API rejected |
| T-07 | Ports + use cases: authentication (`LoginService`, `CreateLibrarianService`) with fakes | T-06 | HU-01 and HU-02 pass against fakes |
| T-08 | Ports + use cases: books (create, search, update, delete, cover) with fakes | T-04 | HU-03 and HU-04 pass against fakes |
| T-09 | Use case: lend (`LendBooksService`) with fakes | T-05, T-08 | HU-06 complete: limits, overdue, no copies |
| T-10 | Use case: return and overdue | T-09 | HU-07 and HU-09; double return returns `ITEM_ALREADY_RETURNED` |
| T-11 | Use case: most-borrowed report + read port | T-09 | HU-10; old title preserved after rename (ADR-004) |
| T-12 | Persistence adapter: book, member, user repositories | T-02, T-08 | Integration tests against real PostgreSQL |
| T-13 | Persistence adapter: loan repository + concurrency witness | T-12, T-09 | Two loans of last copy: one 201, one 409 (RNF-2) |
| T-14 | Security adapter: password hash + JWT token | T-07 | Password never in logs or responses (RNF-3) |
| T-15 | Cover adapter (disk) | T-08 | Upload 2 MB PNG ok; 3 MB returns 422; no cover returns `cover_key` null |
| T-16 | HTTP adapter: routes from `api-contract.md`, unique error body, role authorization | T-10, T-11, T-14, T-15 | Contract test covers all routes and all codes from §2 |
| T-17 | Composition + startup (initial admin from env) | T-16 | System starts with empty DB and admin can log in |
| T-18 | Push to DB the **domain-only** invariants: `ck_book_total_positive`, `ck_user_role` | T-12 | An `INSERT` via `psql` that violates them fails; `data-model.md` §4 updated |
| T-19 | Architecture tests (Articles I, II, III) | T-17 | Pest Arch test passes and fails if a forbidden import is introduced |
| T-20 | `infra`: `docker compose up` starts empty engine, API, frontend | T-17 | One command; DB has no tables until migrations run |
| T-21 | `tool`: demo seeder **via API** (>= 40 books, 10 members, 15 loans, 3 overdue) | T-17 | After running, `/loans/overdue` returns 3 lines and report has rows |
| T-22 | `app`: login, catalog, members, lend, return, overdue, report | T-16 | Manual end-to-end walkthrough |
| T-23 | `page`: public static presentation site | — | Opens without API; correct on mobile and desktop |

## Definition of done

Reaching T-17 with evidence means the **core** is done. T-18 through T-23 are what separate good work from
outstanding work. SDD performance is evaluated, not task count: one task with evidence is worth more than
three half-done.
