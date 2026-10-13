# ADR-005 · Concurrency witness without a version column

- **Status:** accepted
- **Context:** ADR-002 (from the original spec) describes optimistic concurrency using a "version token per book".
  However, `data-model.md` §3 defines the `book` table without a `version` column, and the data model document
  wins over any other (Art. X). We need a mechanism to prevent two librarians from lending the last copy
  simultaneously (RNF-2).
- **Decision:** Use `available_copies` itself as the concurrency witness via compare-and-set. The `UPDATE`
  statement includes `WHERE id = ? AND available_copies = ?` inside a `DB::transaction`. If zero rows are
  affected, the transaction rolls back and the server returns **409 `BOOK_NOT_AVAILABLE`**. The `CHECK`
  constraint `ck_book_available_range` (`0 <= available_copies <= total_copies`) remains the last barrier.
- **Alternatives considered:** adding a `version` column (contradicts `data-model.md` §3, which is the
  authoritative document); pessimistic locking with `SELECT … FOR UPDATE` (serializes the entire library for a
  rare case); in-memory counter (does not survive multiple processes).
- **Consequences:** no schema change is needed. The compare-and-set on `available_copies` provides the same
  guarantee as a version column. If the CHECK fires, **something wrote outside the adapter**: it is a defect,
  not a business case.
- **How to verify:** concurrent test with two simultaneous requests on a book with one copy: exactly one
  receives 201, the other receives 409.
