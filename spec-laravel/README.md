# Manager Library — Laravel + React Translation

This document set is the **translation** of the Manager Library spec (originally written for Python and .NET)
to the stack required by this challenge: **PHP 8.3 + Laravel 11** (backend) and **React 18 + Vite** (frontend),
on **PostgreSQL 16**.

The original specs in [`spec-python/`](../spec-python/) and [`spec-.net/](../spec-.net/) remain untouched and
are the source of authority. This folder records how the technical decisions were translated, what gaps were
found, and how each task is evidenced.

---

## What stays the same (business invariant)

Everything defined in the original spec about **what the system does** is preserved exactly:

- Actors: `admin`, `librarian`. Members are data, not users.
- HU-01 through HU-10 with their acceptance criteria (CA-xx).
- Business rules RN-1 through RN-6.
- Non-functional requirements RNF-1 through RNF-5.
- The HTTP contract in `api-contract.md`: routes, bodies, error codes, pagination format.
- The data model in `data-model.md`: 6 tables, types, nullability, constraints, indexes, seed.
- The 12 articles of `constitution.md`.

## What changes (technology translation)

| Decision | Python/.NET | Laravel/React |
|---|---|---|
| D-01 | Python 3.12 / .NET 8 | PHP 8.3 + Laravel 11 |
| D-02 | FastAPI / Minimal API | Routes in `routes/api.php` with `v1` prefix + invokable controllers |
| D-03 | SQLAlchemy+Alembic / EF Core | Laravel migrations on PostgreSQL 16, schema `library` |
| D-04 | Version token + CHECK | `UPDATE … WHERE available_copies = ?` (compare-and-set) in `DB::transaction` |
| D-05 | UTC dates, Clock port | `APP_TIMEZONE=UTC`, `timestamptz`; `Clock` interface with `SystemClock` and `FixedClock` |
| D-06 | Report via read port | `ReportReader` with a single aggregated query (`GROUP BY book_id, book_title, category_name`) |
| D-07 | Value objects | `final readonly` PHP classes: `Isbn`, `Quantity`, `DocumentNumber`, `DateRange` |
| D-08 | Covers on disk/S3 | `CoverStorage` using `Storage::disk('covers')`; base stores only `cover_key` (UUID) |
| D-09 | Argon2id / BCrypt | `password_hash(..., PASSWORD_ARGON2ID)` behind `PasswordHasher` port |
| D-10 | Initial admin from env | `php artisan library:bootstrap-admin` at container startup (idempotent) |
| D-11 | JWT HS256 8h | `firebase/php-jwt`, claims `sub` and `role`, `exp` 28800s, `JWT_SECRET`; custom `auth.jwt` middleware |
| D-12 | pytest / xUnit | Pest (or PHPUnit): unit (domain), application with fakes, integration and contract with real Postgres |
| Art. I-III | import-linter / NetArchTest | Pest Arch (`arch()->expect('App\Domain')->not->toUse([...])`) |
| Art. III | port_bindings.py / PortBindings.cs | `app/Bootstrap/PortBindingsServiceProvider.php`: sole composition point |
| Frontend | React (Vite) | React 18 + Vite + React Router; HTTP client with Bearer token |

## Documents in this folder

| Document | Purpose |
|---|---|
| [`plan.md`](plan.md) | D-01…D-12 translated with justification |
| [`architecture.md`](architecture.md) | Hexagonal layers in Laravel, real folder structure, diagram |
| [`decisiones-y-huecos.md`](decisiones-y-huecos.md) | H-01…H-12: gaps found in the spec, with the decision taken |
| [`trazabilidad.md`](trazabilidad.md) | HU/CA → endpoint → class → test → status |
| [`tasks.md`](tasks.md) | T-01…T-23 with status and evidence links |
| [`adr/`](adr/) | ADR-005…007: new decisions for the Laravel stack |
