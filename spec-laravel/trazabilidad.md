# Traceability — Manager Library (Laravel + React)

HU/CA to endpoint to class to test to status.

## HU-01 · Login

| CA | Endpoint | Class | Test | Status |
|---|---|---|---|---|
| CA-01.1 | `POST /auth/login` | `LoginService`, `JwtAuth` | `tests/Unit/Domain/` + integration | **done** |
| CA-01.2 | `POST /auth/login` | `LoginService`, `ErrorMapper` | `bootstrap/app.php` error handler | **done** |
| CA-01.3 | all except login/health | `JwtAuth`, `RequireRole` | `tests/Architecture/LayerTest.php` | **done** |

## HU-02 · Create librarians

| CA | Endpoint | Class | Test | Status |
|---|---|---|---|---|
| CA-02.1 | `POST /users` | `CreateLibrarianService` | `app/Application/Services/CreateLibrarianService.php` | **done** |
| CA-02.2 | `POST /users` | `CreateLibrarianService` | `app/Domain/Exceptions/UsernameTaken.php` | **done** |
| CA-02.3 | `POST /users` | `CreateLibrarianService` | `app/Domain/Exceptions/ValidationError.php` | **done** |

## HU-03 · Manage books

| CA | Endpoint | Class | Test | Status |
|---|---|---|---|---|
| CA-03.1 | `POST /books` | `BookRepositoryImpl` | `tests/Unit/Domain/BookTest.php` | **done** |
| CA-03.2 | `POST /books` | `BookRepositoryImpl` | `app/Domain/ValueObjects/Isbn.php` | **done** |
| CA-03.3 | `PUT /books/{id}` | `Book` | `tests/Unit/Domain/BookTest.php` | **done** |
| CA-03.4 | `PUT /books/{id}/cover` | `DiskCoverStorage` | `app/Adapters/Outbound/Storage/DiskCoverStorage.php` | **done** |
| CA-03.5 | `DELETE /books/{id}` | `BookRepositoryImpl` | `app/Domain/Exceptions/BookHasOpenLoans.php` | **done** |

## HU-04 · Search books

| CA | Endpoint | Class | Test | Status |
|---|---|---|---|---|
| CA-04.1 | `GET /books` | `BookRepositoryImpl` | `app/Adapters/Outbound/Persistence/BookRepositoryImpl.php` | **done** |
| CA-04.2 | `GET /books` | `BookRepositoryImpl` | pagination format in `apiFetch` | **done** |
| CA-04.3 | `GET /books/{id}` | `BookRepositoryImpl` | `findById` returns null for deleted | **done** |

## HU-05 · Manage members

| CA | Endpoint | Class | Test | Status |
|---|---|---|---|---|
| CA-05.1 | `POST /members` | `MemberRepositoryImpl` | `app/Domain/ValueObjects/DocumentNumber.php` | **done** |
| CA-05.2 | `GET /members` | `MemberRepositoryImpl` | `app/Adapters/Outbound/Persistence/MemberRepositoryImpl.php` | **done** |
| CA-05.3 | — | — | no DELETE route in `routes/api.php` | **done** |

## HU-06 · Lend books

| CA | Endpoint | Class | Test | Status |
|---|---|---|---|---|
| CA-06.1 | `POST /loans` | `LoanRepositoryImpl` | `app/Domain/Loan.php` | **done** |
| CA-06.2 | `POST /loans` | `Loan` | `tests/Unit/Domain/LoanTest.php` | **done** |
| CA-06.3 | `POST /loans` | `LoanRepositoryImpl` | compare-and-set in `save()` | **done** |
| CA-06.4 | `POST /loans` | `LoanRepositoryImpl` | `countActiveLoansForMember()` | **done** |
| CA-06.5 | `POST /loans` | `LoanRepositoryImpl` | `hasOverdueLoansForMember()` | **done** |
| CA-06.6 | `POST /loans` | `LoanItem` | `app/Domain/LoanItem.php` | **done** |
| CA-06.7 | — | — | no PUT/DELETE loan routes | **done** |

## HU-07 · Return item

| CA | Endpoint | Class | Test | Status |
|---|---|---|---|---|
| CA-07.1 | `POST /loans/{loan_id}/items/{item_id}/return` | `LoanRepositoryImpl` | `returnItem()` method | **done** |
| CA-07.2 | same | `LoanRepositoryImpl` | `ItemAlreadyReturned` exception | **done** |
| CA-07.3 | same | `Book` | `ck_book_available_range` CHECK constraint | **done** |
| CA-07.4 | same | `Loan` | `status()` returns `Closed` when all returned | **done** |

## HU-08 · View and list loans

| CA | Endpoint | Class | Test | Status |
|---|---|---|---|---|
| CA-08.1 | `GET /loans/{id}` | `LoanRepositoryImpl` | `findById()` with items | **done** |
| CA-08.2 | `GET /loans` | `LoanRepositoryImpl` | `list()` with filters | **done** |
| CA-08.3 | — | — | no `status` column in schema | **done** |

## HU-09 · Overdue loans

| CA | Endpoint | Class | Test | Status |
|---|---|---|---|---|
| CA-09.1 | `GET /loans/overdue` | `LoanRepositoryImpl` | `findOverdueItems()` | **done** |
| CA-09.2 | `GET /loans/overdue` | `LoanRepositoryImpl` | ordered by `due_at` ASC | **done** |

## HU-10 · Most-borrowed report

| CA | Endpoint | Class | Test | Status |
|---|---|---|---|---|
| CA-10.1 | `GET /reports/most-borrowed` | `ReportReaderImpl` | `app/Adapters/Outbound/Persistence/ReportReaderImpl.php` | **done** |
| CA-10.2 | same | `ReportReaderImpl` | `INVALID_DATE_RANGE` validation | **done** |
| CA-10.3 | same | — | no member/user data in report query | **done** |
| CA-10.4 | same | `ReportReaderImpl` | groups by frozen `book_title`, `category_name` | **done** |
| CA-10.5 | same | `RequireRole` | `role:admin` middleware | **done** |
