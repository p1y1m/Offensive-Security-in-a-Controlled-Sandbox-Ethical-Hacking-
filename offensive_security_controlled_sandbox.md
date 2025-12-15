# Offensive Security in a Controlled Sandbox (Ethical Hacking)

**Author:** Pedro Yáñez Meléndez  
**Date:** 2025-12-15

## What this is
A focused set of offensive-security checks paired with the corresponding defensive controls, implemented with local Flask services + local data in a controlled sandbox. Each technique includes a clear “unsafe vs safe” contrast and a quick verification step.

## What you can show on GitHub
- Practical implementations of common web/security weaknesses and the correct mitigations
- Repeatable validation using HTTP status codes, headers, and deterministic responses
- Clean, readable Python services and client-side checks (requests)

## Techniques covered

### 1) Password guessing defenses
- Build a login endpoint and generate repeated failed attempts
- Enforce: **throttling** (429), **backoff** (`Retry-After`), **lockout** (423)
- Verify by observing response codes/headers and reviewing security logs

### 2) Session hardening
- Rotate session state on successful login (clear + new session id)
- Set cookie policy flags (HttpOnly + SameSite; Secure in HTTPS deployments)
- Verify by inspecting `Set-Cookie` and confirming session changes after re-login

### 3) Object-level authorization (IDOR)
- Implement record retrieval by id
- Show insecure access (no ownership check) vs enforced access control (403)
- Verify by logging in as one user and attempting to fetch another user’s record

### 4) SQL injection prevention
- Create a SQLite-backed search endpoint
- Compare string-built SQL vs parameterized queries (`?`)
- Verify using crafted input that changes logic in the insecure version, while the secure version stays correct

### 5) XSS prevention
- Render user input into an HTML response
- Compare unsafe rendering vs escaping output (`escape()`)
- Verify by checking whether `<script>` appears as raw markup or as escaped text

### 6) CSRF tokens
- Implement a state-changing action (e.g., transfer) gated by a per-session token
- Enforce `X-CSRF-Token` matching the session token (403 when missing/incorrect)
- Verify by calling the secure endpoint with and without the correct token

### 7) Path traversal protection
- Implement a download endpoint that takes a filename parameter
- Compare naive path join vs path resolve + base-directory enforcement (403)
- Verify by attempting `../` path escapes and confirming they are blocked

### 8) File upload restrictions
- Implement upload + retrieval endpoints
- Compare unrestricted save vs: allowlisted extensions, sanitized filename, randomized storage name, safe serving as attachment
- Verify by attempting to upload a disallowed extension and confirming it is rejected

## Assets and paths
- Workspace root: `/content/offsec_workspace/`
- Logs: `/content/offsec_workspace/logs/`
- SQLite DB: `/content/offsec_workspace/data/app.db`
- Uploads: `/content/offsec_workspace/uploads/`

## Verification signals to highlight
- **401** unauthenticated, **403** forbidden, **423** locked, **429** throttled
- `Retry-After` header for enforced backoff
- Response diffs between insecure vs secure endpoints (counts/blocks/escaped output)
