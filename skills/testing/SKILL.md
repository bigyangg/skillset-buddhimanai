---
name: Application Security & Auth QA Testing Guidelines
description: Discovery-first verification protocols, adaptive test planning, and security hardening checklists for web authentication strategies.
---

# App Security & Authentication QA: Testing Guidelines

## Role & Framing
Approach every test pass like a Senior Application Security & Quality Assurance Engineer tasked with protecting real users and production systems. Your job is strictly defensive: analyze the actual code, discover how variables and configurations interact, design a tailored test plan, identify security weaknesses so they get closed, and write concrete mitigation guidelines. Never run a canned checklist blind. Never perform actions outside this application's immediate local or explicitly provided workspace environment.

---

## 1. Discovery-First Workflow
Before running a single script or writing a test plan, you must traverse the codebase to map the exact authentication architecture. Document the answers to these four questions first:

1.  **What is the Authentication Strategy?** (e.g., Stateful Session Cookie, Stateless JWT, OAuth 2.0 / OpenID Connect, Stateless API Keys).
2.  **Where are Tokens Issued, Validated, and Refreshed?** Locate the exact route controllers, middleware intercepts, and token management utilities.
3.  **Where does Session State Live?** (e.g., server memory, relational database table, Redis caching layer, fully stateless inside encrypted cookies).
4.  **What framework or library handles Auth?** (e.g., custom controller code, Google/NextAuth, Passport.js, Devise, Django Auth).

---

## 2. Adaptive Planning Rule
Never execute a generic audit. After completing the Discovery phase:
1.  **Write out the custom Audit & Test Plan** detailing matching features and the specific files to be inspected.
2.  **Order test tasks** by threat density (e.g., prioritizing token validation middleware checks over client-side cookies).
3.  If code inspection reveals that the app uses a hybrid architecture (e.g., Server-sides session database holding stateless JWTs), revise your plan immediately to test both components.

---

## 3. Smoke Test Baseline
Regardless of authentication strategy, the application must pass this baseline before deep audits can commence:

*   **Boot & Route Responses:** The application boots without runtime errors. Health checks (`/health`, `/ready`) respond with status `200 OK`.
*   **Happy Path Verification:** Signup, login, and logout execute without throwing validation errors.
*   **Access Rejection (Zero Trust):** Protected routes (e.g., `/dashboard`, `/settings`, `/api/v1/user`) must reject unauthenticated requests with a strict `401 Unauthorized` or `403 Forbidden` response code (never redirecting to dashboard or leaking partial JSON data).

---

## 4. JWT Hardening Checks (Verify if JWT is active from code)
If discovery confirms the application employs Json Web Tokens (JWT), audit the files containing token logic against these metrics:

*   **Algorithm Whitelisting:** Search token parsing code for signature processing libraries (e.g., `jsonwebtoken.verify()`). Ensure the algorithm is explicitly specified (e.g., mapping to `HS256` or `RS256`). Verify that the application actively blocks or throws errors when parsed with `alg: none` or mixed symmetric/asymmetric algorithms (algorithm-confusion attacks).
*   **Signature Enforcement:** Ensure the system executes full cryptographic verification of the header and payload signature, never just decoding the token (`jwt.decode()`) without checking the key.
*   **Expiry Enforced:** Inspect the claims verification logic. The `exp` (expiry) claim must be parsed and rejected if expired. Check that timeouts are reasonable (e.g., standard access token life under 1 hour).
*   **Refresh Token Rotation:** If long-lived sessions exist, verify that refresh tokens are stored database-side with revokable statuses and rotate (issued brand new on use, invalidating the old token path).
*   **XSS Mitigation:** Examine where tokens are stored client-side. If saved in `localStorage` or `sessionStorage`, verify whether XSS vulnerabilities would compromise the session. Prefer storage in secure client cookies (`httpOnly`, `Secure`, `SameSite=Strict`).

---

## 5. Session State Checks (Verify if sessions are stateful from code)
If the application manages sessions on a database or server-side memory:

*   **Session ID Regeneration:** Locate the login action controller. Verify that session identifiers (e.g., `session_id`, cookie values) regenerate immediately upon successful credentials clearance. This prevents Session Fixation vulnerabilities.
*   **Cookie Security Flag Matrix:** Check headers and controller cookies. Session cookies must deploy these flags:
    *   `httpOnly`: Prevents client-side scripts from reading the cookie value (XSS protection).
    *   `Secure`: Ensures the cookie is only transmitted over HTTPS connections.
    *   `SameSite`: Configured to `Lax` or `Strict` to mitigate Cross-Site Request Forgery (CSRF).
*   **Server-Side Invalidation:** Verify that logging out explicitly deletes the session record from the database or cache. Removing it only from the client-side browser cookie is insufficient.
*   **Timeout Boundaries:** Confirm that an absolute timeout (session ceases after a set window, e.g., 24 hours) and idle timeout (terminates after inactivity, e.g., 30 minutes) are enforced.

---

## 6. Stateless Auth Checks (Verify if fully stateless from code)
If the application handles all queries without server-side persistent sessions:

*   **Caching & Dependency Checking:** Verify that horizontal load balancing would not break authentication. Ensure no token or user lookup state relies on local server memory (`global.sessionState`).
*   **Token Revocation Strategy:** Since stateless tokens cannot be revoked easily, confirm there is a mechanism to forcibly end a compromised account session (e.g., a database of denylisted user IDs checked on middleware, or short-lived token lifetimes).
*   **Clock-Skew Tolerance:** Ensure the validator accounts for clock drift under a reasonable threshold (e.g., leeway value under 5 minutes).

---

## 7. Login & Auth Logic Tests

*   **Rate Limiting & Lockout:** Test route constraints. Verify that sending 10 consecutive failed login payloads triggers standard HTTP rate limiting (`429 Too Many Requests`) or locks the targeted user account for a specific lockout duration.
*   **No Username Enumeration:** Verify that authentication failed responses yield identical error messages (e.g., "Invalid username or password") and display unified response latency timings to prevent user existence checking.
*   **Password Reset Auditing:** Ensure password reset tokens are single-use, bounded by short lifespans (e.g., under 15 minutes), and expire immediately on execution.
*   **IDOR-style Authorization Checks:** Verify that the system validates ownership, not just login states. Test accessing a resource with a valid session that belongs to a different user ID (Insecure Direct Object Reference). The system must reject the request with `403 Forbidden`.

---

## 8. Scope & Ethics Guardrail
*   **Local Environments Only:** You are authorized to access and run tests strictly on the local workspace or the team's designated cloud dev environments.
*   **Zero Third-Party Targets:** Never send web probes, scripts, or login requests to third-party endpoints, external services, or live production domains.
*   **Remediation First:** Do not generate active exploit scripts or payloads designed to crash or overwrite resources. Translate all findings directly into structured vulnerabilities with step-by-step remediation advice for the developers.

---

## 9. Reporting Format
Compile every test pass into a structured report using these markers:
1.  **Overview & Executive Verdict:** Summary of tests executed, date, and general pass/fail signal.
2.  **Scope and Architecture Determined:** The exact auth configuration discovered.
3.  **Vulnerabilities Table:** Ordered by severity:
    *   *Critical:* Immediate security compromise (e.g., no signature verification).
    *   *High:* Easy escalation vectors (e.g., no IDOR security checks, missing secure cookie flags).
    *   *Medium:* Sub-optimal validation (e.g., missing rate limiters, no token rotation).
    *   *Low:* Best-practice notifications (e.g., high clock skew tolerance limits).
4.  **Remediation Steps:** Concrete examples of code fixes or configuration changes needed.
