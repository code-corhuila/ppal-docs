# Authentication — MVP Scope

## Current decision

The PantryPal MVP explicitly excludes user accounts, login, logout, token-based authentication, and protected routes. Do not implement an authentication flow or add authentication screens for this MVP. This aligns with ADR-002 — MVP Scope.

Because the demonstration API has no user identity or account isolation, it must remain in a controlled development/demo environment. Do not expose it as a public multi-user production service or store sensitive personal data.

## If authentication is added in a future scope

Authentication is not a hidden assumption or a requirement for the current MVP. Reconsider it only through an explicit scope change and architecture/security review. If a future version adds authentication:

- Store access and refresh tokens only in platform secure storage (Keychain / Keystore).
- Never store tokens in plain preferences, local files, or unencrypted databases.
- Attach access tokens via a single HTTP interceptor, not separately in every request.
- Refresh an expired access token transparently on a 401, retry the original request once, and serialize concurrent refreshes.
- On refresh failure, clear credentials safely and route the user through the agreed sign-out/login flow.
- Never log tokens or credentials; do not embed long-lived secrets in the mobile application.
- Treat biometric unlock as a gate to stored tokens, not as a replacement for them.

These are future design constraints, not current MVP functionality.
