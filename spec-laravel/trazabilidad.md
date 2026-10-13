# Traceability — Manager Library (Laravel + React)

HU/CA to endpoint to class to test to status.

## HU-01 · Login

| CA | Endpoint | Class | Test | Status |
|---|---|---|---|---|
| CA-01.1 | `POST /auth/login` | `LoginService`, `JwtAuth` | `test_login_returns_token_with_8h_expiry` | pending |
| CA-01.2 | `POST /auth/login` | `LoginService`, `ErrorMapper` | `test_invalid_credentials_returns_401_without_hint` | pending |
| CA-01.3 | all except login/health | `JwtAuth`, `RequireRole` | `test_missing_token_returns_401`, `test_insufficient_role_returns_403` | pending |

## HU-02 · Create librarians

| CA | Endpoint | Class | Test | Status |
|---|---|---|---|---|
| CA-02.1 | `POST /users` | `CreateLibrarianService` | `test_admin_creates_librarian` | pending |
| CA-02.2 | `POST /users` | `CreateLibrarianService` | `test_duplicate_username_returns_409` | pending |
| CA-02.3 | `POST /users` | `CreateLibrarianService` | `test_admin_role_via_api_returns_422` | pending |

## HU-03 · Manage books

| CA | Endpoint | Class | Test | Status |
|---|---|---|---|---|
| CA-03.1 | `POST /books` | `CreateBookService` | `test_create_book_sets_available_equal_total` | pending |
| CA-03.2 | `POST /books` | `CreateBookService` | `test_duplicate_isbn_returns_409` | pending |
| CA-03.3 | `PUT /books/{id}` | `UpdateBookService` | `test_total_reduction_never_makes_available_negative` | pending |
| CA-03.4 | `PUT /books/{id}/cover` | `UploadCoverService` | `test_upload_cover_returns_opaque_key`, `test_oversized_cover_returns_422` | pending |
| CA-03.5 | `DELETE /books/{id}` | `DeleteBookService` | `test_book_with_open_loans_returns_409`, `test_delete_sets_deleted_at` | pending |

## HU-04 · Search books

| CA | Endpoint | Class | Test | Status |
|---|---|---|---|---|
| CA-04.1 | `GET /books` | `SearchBooksService` | `test_search_filters_and_paginates` | pending |
| CA-04.2 | `GET /books` | `SearchBooksService` | `test_response_includes_total` | pending |
| CA-04.3 | `GET /books/{id}` | `SearchBooksService` | `test_missing_or_deleted_book_returns_404` | pending |

## HU-05 · Manage members

| CA | Endpoint | Class | Test | Status |
|---|---|---|---|---|
| CA-05.1 | `POST /members` | `CreateMemberService` | `test_duplicate_document_returns_409` | pending |
| CA-05.2 | `GET /members` | member list | `test_list_and_search_members` | pending |
| CA-05.3 | — | — | `test_no_delete_member_endpoint` | pending |

## HU-06 · Lend books

| CA | Endpoint | Class | Test | Status |
|---|---|---|---|---|
| CA-06.1 | `POST /loans` | `LendBooksService` | `test_loan_has_1_to_3_items` | pending |
| CA-06.2 | `POST /loans` | `LendBooksService` | `test_duplicate_book_in_loan_returns_422` | pending |
| CA-06.3 | `POST /loans` | `LendBooksService` | `test_no_copies_returns_409_and_registers_nothing` | pending |
| CA-06.4 | `POST /loans` | `LendBooksService` | `test_fourth_book_returns_422_with_count` | pending |
| CA-06.5 | `POST /loans` | `LendBooksService` | `test_member_with_overdue_returns_409` | pending |
| CA-06.6 | `POST /loans` | `LendBooksService` | `test_due_at_is_14_days_and_values_frozen` | pending |
| CA-06.7 | — | — | `test_no_update_or_delete_loan_endpoints` | pending |

## HU-07 · Return item

| CA | Endpoint | Class | Test | Status |
|---|---|---|---|---|
| CA-07.1 | `POST /loans/{loan_id}/items/{item_id}/return` | `ReturnItemService` | `test_return_sets_returned_at_and_increment_available` | pending |
| CA-07.2 | same | `ReturnItemService` | `test_double_return_returns_409_and_never_double_counts` | pending |
| CA-07.3 | same | `ReturnItemService` | `test_available_never_exceeds_total` | pending |
| CA-07.4 | same | `ReturnItemService` | `test_all_items_returned_marks_loan_closed` | pending |

## HU-08 · View and list loans

| CA | Endpoint | Class | Test | Status |
|---|---|---|---|---|
| CA-08.1 | `GET /loans/{id}` | loan detail | `test_detail_includes_items_and_status` | pending |
| CA-08.2 | `GET /loans` | loan list | `test_list_filters_by_date_status_member` | pending |
| CA-08.3 | — | — | `test_status_is_calculated_not_stored` | pending |

## HU-09 · Overdue loans

| CA | Endpoint | Class | Test | Status |
|---|---|---|---|---|
| CA-09.1 | `GET /loans/overdue` | `OverdueService` | `test_overdue_lists_unreturned_past_due` | pending |
| CA-09.2 | `GET /loans/overdue` | `OverdueService` | `test_overdue_ordered_by_days_late_desc` | pending |

## HU-10 · Most-borrowed report

| CA | Endpoint | Class | Test | Status |
|---|---|---|---|---|
| CA-10.1 | `GET /reports/most-borrowed` | `MostBorrowedReportService` | `test_report_groups_by_frozen_values` | pending |
| CA-10.2 | same | `MostBorrowedReportService` | `test_invalid_range_returns_422`, `test_empty_range_returns_empty_rows` | pending |
| CA-10.3 | same | — | `test_report_has_no_members_or_librarians` | pending |
| CA-10.4 | same | `MostBorrowedReportService` | `test_report_preserves_old_title_after_rename` | pending |
| CA-10.5 | same | `RequireRole` | `test_report_requires_admin_role` | pending |
