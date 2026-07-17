# Authentication & Session Security Checklist

Run this checklist once codebase discovery has identified the active authentication strategy.

---

## 1. Discovery Phase (Mandatory Start)
*   [ ] **Identify Strategy:** Auth is [JWT / Session-Cookie / Stateless API / OAuth].
*   [ ] **Locate Library:** Identified Auth package (e.g. NextAuth, Passport, Devise, custom).
*   [ ] **Map State:** Verified session state storage (Redis, DB, Server Memory, or stateless).
*   [ ] **Document Routes:** mapped login, signup, token reissue, and logout endpoints.

---

## 2. Smoke Test Phase
*   [ ] **Boot Validation:** App boots and health endpoints return `200 OK`.
*   [ ] **Auth Paths Clean:** Signup, login and logout succeed on valid actions.
*   [ ] **Access Guard:** Accessing protected pathways without credentials rejects with `401` or `403`.

---

## 3. JWT Strategy Audits
*   [ ] **Allowed Algorithms:** Alg signature claims verified via strict whitelist.
*   [ ] **Signatures Checked:** Verification checks signatures, never accepts blank/unverified payloads.
*   [ ] **Expiry Claims (exp):** Expiry values parsed and validated against current server time.
*   [ ] **Token Lifetime:** Access lifetime limited to under 1 hour.
*   [ ] **Rotation Rules:** Refresh tokens rotate on use and database tracking supports revocation.
*   [ ] **Secure Storage:** Tokens kept away from `localStorage` if XSS is a risk vector.

---

## 4. Stateful Session Audits
*   [ ] **ID Regeneration:** Session ID regenerated immediately on successful login.
*   [ ] **httpOnly Enabled:** Session cookies set with the `httpOnly` flag to block manual script accesses.
*   [ ] **Secure Enabled:** Session cookies set with `Secure` flag (HTTPS enforced).
*   [ ] **SameSite Mapping:** SameSite configured to `Strict` or `Lax` to prevent CSRF.
*   [ ] **Server Eviction:** Logouts trigger removal of session entries from database/cache repository.
*   [ ] **Session Lifespan:** Idle timeout and absolute lifetimes are declared and enforced.

---

## 5. Fully Stateless Audits
*   [ ] **Horizontal Readiness:** Auth state does not reside in local server memory.
*   [ ] **Revocation Plan:** Check for short token lifetimes or active user status checks on request.
*   [ ] **Leeway Checked:** Tolerance boundaries for clock skew are under 5 minutes.

---

## 6. Logic & Interface Validation
*   [ ] **Login Rate Limiting:** Repeated invalid login attempts triggers `429` rate limiting.
*   [ ] **No Enumeration:** Failed logins return identical text and response latency profiles.
*   [ ] **Password Reset:** Reset tokens are single-use, timed-out under 15 minutes, and invalidated on reset.
*   [ ] **IDOR Check:** Intercepting user resource requests using a different validated session returns `403 Forbidden`.
