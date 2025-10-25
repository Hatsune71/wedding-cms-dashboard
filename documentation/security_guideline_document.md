# Security Guidelines for Codeguide Starter Fullstack (Wedding CMS Dashboard)

A comprehensive set of security controls and best practices tailored to the **"Codeguide Starter Fullstack"** repository, designed to harden your multi-role dashboard and CMS for wedding templates by integrating security by design across the entire stack.

---

## 1. Authentication & Access Control

1. Enforce Strong Authentication:
   - Continue using **Better Auth** but verify that session tokens are cryptographically secure and rotate them on privilege changes (e.g., role escalation).
   - Enforce multi-factor authentication (MFA) for **admin** accounts via Time-based One-Time Passwords (TOTP) or SMS/email OTPs.

2. Password Policy & Storage:
   - Require a minimum password length (≥12 characters) with complexity rules (upper/lowercase, digits, symbols).
   - Store hashed passwords using **Argon2** or **bcrypt** with per-user salts.

3. Role-Based Access Control (RBAC):
   - Add a `role` enum column (`ADMIN`, `USER`) in `db/schema/auth.ts`.
   - On sign-up, default to `USER`. Provide a secured admin UI or CLI script to assign `ADMIN` role.
   - Implement server-side checks in **Next.js middleware** (`middleware.ts`) that:
     - Validate session presence and expiration.
     - Route users to `/dashboard/admin` only if `role === 'ADMIN'`.
     - Route ordinary users to `/dashboard/weddings`.

4. Session Management & Cookies:
   - Set `Secure; HttpOnly; SameSite=Lax` on all authentication cookies.
   - Enforce idle-timeout (e.g., 15 min) and absolute expiration (e.g., 12 hours).
   - Invalidate sessions server-side on password/role changes or logout.

5. Protect Against Common Attacks:
   - Rate-limit login and password-reset endpoints (e.g., using **next-rate-limit**).
   - Implement account lockout after five failed login attempts per user/IP.
   - Monitor for brute-force patterns and alert admins.

---

## 2. Input Handling & Processing

1. Schema-First Validation:
   - Use **Zod** or **Yup** to validate every API payload (`/app/api/weddings/**`), including types, lengths, formats.
   - Define explicit schemas for create/update wedding templates before passing data to Drizzle ORM.

2. Prevent Injection:
   - Leverage Drizzle ORM’s parameterized queries to avoid SQL injection.
   - Never concatenate raw strings into SQL or shell commands.

3. Sanitize & Encode Outputs:
   - Escape any user-provided content rendered in React components. Use libraries like **DOMPurify** if rendering HTML.

4. File Uploads (if applicable):
   - Restrict file types by MIME and extension (e.g., `.jpg`, `.png`, `.pdf`).
   - Limit file size (e.g., ≤5 MB).
   - Store uploads outside the webroot and serve via a signed URL or proxy.

5. Redirect & Open-Redirect Protection:
   - Maintain an allow-list of safe redirect targets.
   - Reject or default to `/dashboard` on invalid `returnUrl` parameters.

---

## 3. Data Protection & Privacy

1. Transport Encryption:
   - Enforce TLS 1.2+ (prefer TLS 1.3) on all public endpoints.
   - Redirect HTTP → HTTPS at the load-balancer or reverse proxy.

2. At-Rest Encryption:
   - Enable PostgreSQL Transparent Data Encryption (TDE) or encrypt sensitive columns (e.g., PII) using application-level AES-256.

3. Secret Management:
   - Move database credentials, JWT secrets, and third-party API keys into a secrets manager (e.g., AWS Secrets Manager, HashiCorp Vault).
   - Do **not** commit secrets in `.env` or source control.

4. Logging & Information Leakage:
   - Avoid logging sensitive values (passwords, full tokens, credit cards).
   - Mask or hash PII in logs, and sanitize stack traces before returning errors to clients.

5. Data Privacy Compliance:
   - Provide data-export and data-deletion endpoints for GDPR/CCPA compliance.
   - Log user consent and offer clear cookie consent banners.

---

## 4. API & Service Security

1. Secure API Endpoints:
   - Impose authentication and authorization on every Next.js API route.
   - Enforce correct HTTP verbs: GET for reading, POST for creating, PUT/PATCH for updates, DELETE for removals.

2. Rate Limiting & Throttling:
   - Apply global and per-endpoint rate limits (e.g., 100 requests per minute per IP).

3. CORS Configuration:
   - Restrict `Access-Control-Allow-Origin` to your trusted domains only.
   - Explicitly set `Access-Control-Allow-Credentials: true` when sharing cookies across subdomains.

4. API Versioning:
   - Namespace endpoints (e.g., `/api/v1/weddings`) to allow non-breaking future changes.

5. Minimal Response Payloads:
   - Return only necessary fields (omit internal IDs, salts, or other metadata).

---

## 5. Web Application Security Hygiene

1. Security Headers (via `next-secure-headers` or custom middleware):
   - `Strict-Transport-Security: max-age=63072000; includeSubDomains; preload`
   - `Content-Security-Policy` restricting scripts to your own domain and SRI-protected CDNs.
   - `X-Frame-Options: DENY` (or CSP `frame-ancestors 'none'`).
   - `X-Content-Type-Options: nosniff`.
   - `Referrer-Policy: strict-origin-when-cross-origin`.

2. CSRF Protection:
   - Use synchronizer tokens or double-submit cookies for all state-changing requests.

3. Secure Client Storage:
   - Avoid storing tokens in `localStorage`. Rely on `HttpOnly` cookies.

4. Subresource Integrity (SRI):
   - Add integrity hashes for any third-party `<script>` or `<link>` tags.

---

## 6. Infrastructure & Configuration Management

1. Docker Hardening:
   - Use minimal base images (e.g., `node:18-slim`).
   - Run containers as non-root users.
   - Mount secrets/secrets-files via read-only volumes.

2. Server & Network:
   - Close unused ports; expose only HTTP(S) and database ports to trusted networks.
   - Disable default credentials, remove unused packages and services.

3. TLS/SSL Settings:
   - Disable SSLv3, TLS 1.0/1.1, weak cipher suites.
   - Enable OCSP stapling.

4. Environment Segregation:
   - Separate development, staging, and production configurations.
   - Disable debug logging and dev-only features in production.

---

## 7. Dependency Management

1. Lockfile Discipline:
   - Commit `package-lock.json` or `yarn.lock` to ensure deterministic installs.

2. Vulnerability Scanning:
   - Integrate SCA tools (e.g., **Snyk**, **Dependabot**, **npm audit**) into CI pipeline.

3. Minimal Footprint:
   - Review dependencies regularly and remove unused packages.
   - Pin dependency versions and avoid wildcards.

4. Upgrade Strategy:
   - Subscribe to security advisories for major dependencies (Next.js, React, Drizzle ORM).
   - Schedule quarterly dependency upgrades and test thoroughly.

---

## 8. Testing, Monitoring & Incident Response

1. Automated Security Testing:
   - Include unit tests for authorization logic (e.g., ensure `USER` cannot access admin endpoints).
   - Run dynamic analysis (DAST) against staging environment.

2. Logging & Monitoring:
   - Centralize logs (e.g., ELK Stack, Datadog); monitor for suspicious patterns.
   - Implement alerts on repeated auth failures, high error rates, or unusual rate-limit triggers.

3. Incident Response:
   - Define runbook for security incidents (containment, root cause analysis, remediation, disclosure).
   - Perform regular tabletop exercises.

---

**Adhering to these guidelines** ensures that your wedding CMS dashboard remains secure, resilient, and compliant—providing a trustworthy foundation for both administrators and end users.
