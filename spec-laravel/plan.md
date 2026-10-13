# Technical Plan — Laravel + React Translation

Each decision from the original spec translated to the challenge stack, with justification.

## D-01 · Runtime

| Python/.NET | Laravel/React |
|---|---|
| Python 3.12 / .NET 8 | **PHP 8.3 + Laravel 11** |

**Justification:** the challenge stack. Laravel 11 is the current LTS-compatible version with PHP 8.3 support.

## D-02 · HTTP layer

| Python/.NET | Laravel/React |
|---|---|
| FastAPI / Minimal API | **Routes in `routes/api.php` with `v1` prefix + invokable controllers** |

**Justification:** the contract requires `/api/v1` prefix. Laravel's `api.php` routes are automatically prefixed
with `api`; we add a `v1` group prefix. Invokable controllers (`__invoke`) keep each endpoint in one class,
matching the "thin controller" principle.

## D-03 · Persistence

| Python/.NET | Laravel/React |
|---|---|
| SQLAlchemy + Alembic / EF Core | **Laravel migrations on PostgreSQL 16, schema `library`** |

**Justification:** ADR-001 states the schema is owned by migrations. Laravel migrations are the equivalent of
Alembic revisions. The `library` schema is set via `search_path` in the database config and `CREATE SCHEMA` in
the first migration.

## D-04 · Concurrency control

| Python/.NET | Laravel/React |
|---|---|
| Version token + CHECK | **`UPDATE … WHERE id = ? AND available_copies = ?` (compare-and-set) inside `DB::transaction`; if 0 rows affected → 409** |

**Justification:** see gap H-01. `data-model.md` §3 has no `version` column, and the document wins (Art. X).
We use `available_copies` itself as the witness: the `UPDATE` only succeeds if the value hasn't changed since
read. The `ck_book_available_range` CHECK is the last barrier. See [ADR-005](adr/adr-005-testigo-de-concurrencia-sin-columna-version.md).

## D-05 · Clock

| Python/.NET | Laravel/React |
|---|---|
| UTC dates, Clock port | **`APP_TIMEZONE=UTC`, `timestamptz`; `Clock` interface with `SystemClock` and `FixedClock` (tests)** |

**Justification:** to test overdue loans without waiting 14 days. `FixedClock` is injected in tests; `SystemClock`
uses `now()`. The seeder uses `CLOCK_OFFSET_DAYS` (see H-05, [ADR-006](adr/adr-006-reloj-desplazable-para-el-sembrador.md)).

## D-06 · Report reader

| Python/.NET | Laravel/React |
|---|---|
| Report via read port | **`ReportReader` implemented with a single aggregated query (`GROUP BY book_id, book_title, category_name`)** |

**Justification:** ADR-004. The report is never persisted; it is computed by one SQL query that groups by the
frozen values in `loan_item`. No join to the live catalog.

## D-07 · Value objects

| Python/.NET | Laravel/React |
|---|---|
| Value objects | **`final readonly` PHP classes: `Isbn`, `Quantity`, `DocumentNumber`, `DateRange`** |

**Justification:** no table, no identity. They validate on construction and throw domain exceptions. PHP 8.3
`readonly` classes are the closest equivalent to Python's `frozen=True` dataclasses.

## D-08 · Cover storage

| Python/.NET | Laravel/React |
|---|---|
| Covers on disk/S3 | **`CoverStorage` using `Storage::disk('covers')`; base stores only `cover_key` (UUID)** |

**Justification:** opaque key. The `covers` disk is configured in `config/filesystems.php`. The base never stores
a path or bytes, only a UUID key. The binary is served through `GET /books/{id}/cover` which streams from storage.

## D-09 · Password hashing

| Python/.NET | Laravel/React |
|---|---|
| Argon2id / BCrypt | **`password_hash(..., PASSWORD_ARGON2ID)` behind `PasswordHasher` port** |

**Justification:** Art. IX. The hash is produced by PHP's native Argon2id. The `PasswordHasher` port hides the
mechanism; the adapter uses `password_hash` / `password_verify`. The hash never appears in logs or responses.

## D-10 · Initial admin

| Python/.NET | Laravel/React |
|---|---|
| Initial admin from env | **`php artisan library:bootstrap-admin` at container startup (idempotent)** |

**Justification:** the database cannot produce hashes. The command reads `ADMIN_USERNAME` and `ADMIN_PASSWORD`
from env, hashes the password, and inserts the user if not exists. It runs in the container entrypoint before
serving.

## D-11 · JWT

| Python/.NET | Laravel/React |
|---|---|
| JWT HS256 8h | **`firebase/php-jwt`, claims `sub` and `role`, `exp` 28800s, `JWT_SECRET`; custom `auth.jwt` middleware + `role:admin,librarian`** |

**Justification:** the contract requires JWT (Sanctum does not emit JWT by default). `firebase/php-jwt` is a
well-known library. The middleware validates the token, extracts `sub` and `role`, and attaches the user to the
request. Role middleware checks authorization per route.

## D-12 · Testing

| Python/.NET | Laravel/React |
|---|---|
| pytest / xUnit | **Pest (or PHPUnit): unit (domain), application with fakes, integration and contract with real Postgres** |

**Justification:** test pyramid. Unit tests for domain (no DB), application tests with fake ports (no DB),
integration tests with real Postgres (migrations + transactions), contract tests that hit every route and error
code. Pest's `arch()` function verifies architecture constraints (Art. I-III).

## Architecture articles

| Python/.NET | Laravel/React |
|---|---|
| import-linter / NetArchTest | **Pest Arch (`arch()->expect('App\Domain')->not->toUse([...])`)** |
| port_bindings.py / PortBindings.cs | **`app/Bootstrap/PortBindingsServiceProvider.php`: sole `$this->app->bind(Port::class, Adapter::class)`** |

**Justification:** Art. I-III. The domain has zero dependencies on Laravel or Eloquent. The application layer
only knows the domain. All port-to-adapter bindings live in one service provider.
