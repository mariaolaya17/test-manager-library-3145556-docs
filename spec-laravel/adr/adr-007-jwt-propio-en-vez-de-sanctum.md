# ADR-007 · Custom JWT instead of Sanctum

- **Status:** accepted
- **Context:** The API contract (§4) requires a signed JWT with 8-hour expiry, containing `sub` and `role`
  claims. Laravel's default authentication package, Sanctum, does not emit JWT tokens by default — it uses
  opaque tokens or session-based auth. We need JWT specifically.
- **Decision:** Use `firebase/php-jwt` to issue and validate JWT HS256 tokens. A custom `auth.jwt` middleware
  validates the `Authorization: Bearer <token>` header, extracts `sub` and `role`, and attaches the
  authenticated user to the request. A `role:admin,librarian` middleware enforces per-route authorization.
  The `TokenIssuer` port hides the implementation; the adapter uses `firebase/php-jwt`.
- **Alternatives considered:** Sanctum with a custom token format (Sanctum tokens are opaque, not JWT);
  Laravel Passport (OAuth2 overhead, not needed for a simple login); rolling our own JWT (security risk).
- **Consequences:** the `JWT_SECRET` environment variable must be set (Art. IX). Tokens expire in 28800
  seconds (8 hours). The middleware returns 401 for missing/invalid/expired tokens and 403 for insufficient
  role. No personal data is stored in the token beyond `sub` (user UUID) and `role`.
- **How to verify:** `POST /auth/login` returns a valid JWT; `GET /api/v1/books` with the token succeeds;
  without the token returns 401; with a `librarian` token on an admin-only route returns 403.
