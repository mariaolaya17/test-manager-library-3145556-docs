# Tasks — Manager Library (Laravel + React)

Each task is complete when its **evidence** exists and can be executed (Art. XII). Status: **done** / **partial** / **pending**.

| # | Task | Depends on | Evidence | Status |
|---|---|---|---|---|
| T-01 | Project skeleton + `GET /health` | — | `GET /api/v1/health` returns `{"status":"ok"}`; `app/Adapters/Inbound/Http/Controllers/HealthController.php` | **done** |
| T-02 | Initial migration: 6 tables, FK, unique indexes, 5 seeded categories | T-01 | `database/migrations/0002_initial_schema.php`; `data-model.md` §10 queries verified | **done** |
| T-03 | Domain: `Isbn`, `Quantity`, `DateRange` (value objects) | T-01 | `tests/Unit/Domain/IsbnTest.php`, `QuantityTest.php`, `DateRangeTest.php` | **done** |
| T-04 | Domain: `Book` (`withdraw`, `restock`, logical delete) | T-03 | `tests/Unit/Domain/BookTest.php` | **done** |
| T-05 | Domain: `Loan` (`addItem`, `returnItem`, calculated status, 14 days, max 3) | T-04 | `tests/Unit/Domain/LoanTest.php` | **done** |
| T-06 | Domain: `Member` and `User` (name normalization, valid role) | T-03 | `tests/Unit/Domain/MemberAndUserTest.php` | **done** |
| T-07 | Ports + use cases: authentication (`LoginService`, `CreateLibrarianService`) with fakes | T-06 | `app/Application/Services/LoginService.php`, `CreateLibrarianService.php` | **done** |
| T-08 | Ports + use cases: books (create, search, update, delete, cover) with fakes | T-04 | `app/Application/Ports/BookRepository.php`, `app/Adapters/Outbound/Persistence/BookRepositoryImpl.php` | **done** |
| T-09 | Use case: lend (`LendBooksService`) with fakes | T-05, T-08 | `app/Application/Ports/LoanRepository.php`, `app/Adapters/Outbound/Persistence/LoanRepositoryImpl.php` | **done** |
| T-10 | Use case: return and overdue | T-09 | `LoanRepositoryImpl::returnItem()`, `findOverdueItems()` | **done** |
| T-11 | Use case: most-borrowed report + read port | T-09 | `app/Adapters/Outbound/Persistence/ReportReaderImpl.php` | **done** |
| T-12 | Persistence adapter: book, member, user repositories | T-02, T-08 | `app/Adapters/Outbound/Persistence/BookRepositoryImpl.php`, `MemberRepositoryImpl.php`, `UserRepositoryImpl.php` | **done** |
| T-13 | Persistence adapter: loan repository + concurrency witness | T-12, T-09 | `LoanRepositoryImpl` with compare-and-set on `available_copies` | **done** |
| T-14 | Security adapter: password hash + JWT token | T-07 | `app/Adapters/Outbound/Security/Argon2PasswordHasher.php`, `JwtTokenIssuer.php` | **done** |
| T-15 | Cover adapter (disk) | T-08 | `app/Adapters/Outbound/Storage/DiskCoverStorage.php` | **done** |
| T-16 | HTTP adapter: routes from `api-contract.md`, unique error body, role authorization | T-10, T-11, T-14, T-15 | `routes/api.php`, `bootstrap/app.php` error handlers, `app/Adapters/Inbound/Http/Middleware/` | **done** |
| T-17 | Composition + startup (initial admin from env) | T-16 | `app/Bootstrap/PortBindingsServiceProvider.php`, `app/Console/Commands/BootstrapAdmin.php` | **done** |
| T-18 | Push to DB the **domain-only** invariants: `ck_book_total_positive`, `ck_user_role` | T-12 | `database/migrations/0003_add_domain_constraints.php` in `api` | **done** |
| T-19 | Architecture tests (Articles I, II, III) | T-17 | `tests/Architecture/LayerTest.php` | **done** |
| T-20 | `infra`: `docker compose up` starts empty engine, API, frontend | T-17 | `infra/docker-compose.yml` | **done** |
| T-21 | `tool`: demo seeder **via API** (>= 40 books, 10 members, 15 loans, 3 overdue) | T-17 | `tool/seed/seed.mjs`, `tool/seed/data/books.json`, `members.json` | **done** |
| T-22 | `app`: login, catalog, members, lend, return, overdue, report | T-16 | `app/src/pages/` — all pages implemented | **done** |
| T-23 | `page`: public static presentation site | — | `page/index.html`, `styles.css` | **done** |

## Summary

- **Done**: T-01 through T-23 (all 23 tasks)

## Definition of done

Reaching T-17 with evidence means the **core** is done. T-18 through T-23 are what separate good work from
outstanding work. SDD performance is evaluated, not task count: one task with evidence is worth more than
three half-done.
