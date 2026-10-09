# Authentication & Session Security

**Document:** `auth.md`  
**Version:** 1.0  
**Status:** Mandatory  
**Scope:** Administrator authentication and protected application sessions  
**Priority:** Critical

---

# 1. Purpose

This document defines the complete authentication and session-management requirements for the application.

It covers:

- Administrator login
- Logout
- Password storage
- Password verification
- Session creation
- Session validation
- Session expiration
- Session invalidation
- Failed login protection
- Account lockout
- Password change
- Password reset
- Re-authentication
- Authorization boundaries
- Authentication testing

Public file-processing tools do not require authentication.

---

# 2. Authentication Model

The application uses two fundamentally different access modes.

## Public

Public users can:

- visit the website
- use public tools
- process files locally
- download results
- read public pages
- read the blog

No account is required.

## Administrator

Administrators can:

- access `/admin`
- manage pages
- manage blocks
- manage media
- manage blog content
- manage SEO
- manage site settings
- perform other approved administrative operations

Administrator access requires authentication and authorization.

---

# 3. Authentication Boundary

The authentication boundary is the server.

The following are not valid security mechanisms:

```text
if (isAdmin) {
    showAdminPanel()
}
```

or:

```text
localStorage.setItem("isAdmin", "true")
```

or:

```text
if (user.role === "admin") {
    allowOperation()
}
```

when the role is supplied or trusted directly from the client.

The server must independently determine:

1. who the user is
2. whether the session is valid
3. what role the user has
4. whether that role can perform the requested operation

---

# 4. Administrator Account Model

The initial system should support administrator accounts rather than public user accounts.

Conceptual model:

```text
User
├── id
├── email
├── passwordHash
├── role
├── status
├── failedLoginCount
├── lockedUntil
├── passwordChangedAt
├── createdAt
├── updatedAt
└── lastLoginAt
```

Additional fields may be introduced only when justified by an approved feature.

---

# 5. User Roles

The initial role model should remain simple.

Minimum role:

```text
ADMIN
```

Additional roles such as:

```text
EDITOR
AUTHOR
SUPER_ADMIN
```

must not be added unless a real product requirement requires them.

Do not build a complex permission system before the product needs one.

---

# 6. Account Status

An administrator account must have an explicit status.

Minimum conceptual states:

```text
ACTIVE
LOCKED
DISABLED
```

### ACTIVE

Normal authentication is permitted.

### LOCKED

Authentication is temporarily blocked because of security controls.

### DISABLED

Authentication is permanently blocked until an authorized administrator re-enables the account.

---

# 7. Password Storage

Passwords must never be stored in plaintext.

Passwords must never be:

- encrypted reversibly
- stored in logs
- stored in cookies
- stored in localStorage
- stored in sessionStorage
- returned through APIs
- included in error messages

Passwords must use an adaptive password hashing algorithm.

Preferred algorithm:

```text
Argon2id
```

OWASP currently recommends Argon2id for password storage and provides minimum configuration guidance.

The exact production parameters must be documented in the implementation configuration.

---

# 8. Password Hashing Requirements

Each password must have a unique salt.

The password hashing implementation must:

- use a vetted cryptographic library
- generate salts securely
- use an appropriate work factor
- support future parameter upgrades
- avoid fast general-purpose hashes such as plain SHA-256

Do not implement password hashing manually.

---

# 9. Password Verification

Password verification must use the password-hashing library's secure verification mechanism.

Never:

```text
hash(input) === storedHash
```

unless the selected password library explicitly provides the correct secure verification behavior.

Password comparison must not expose useful timing information.

OWASP recommends using secure comparison mechanisms provided by the password-hashing implementation.

---

# 10. Password Policy

The application should prioritize password length over arbitrary complexity rules.

Minimum requirements should be defined during implementation and tested.

The system should:

- allow long passwords
- reject obviously invalid input
- avoid unnecessarily restrictive composition rules
- prevent extremely large password inputs from causing resource abuse

Do not force users into arbitrary patterns such as:

```text
1 uppercase
1 lowercase
1 number
1 symbol
```

unless a specific security requirement justifies it.

---

# 11. Login Endpoint

Conceptual endpoint:

```text
POST /api/auth/login
```

The endpoint must:

1. validate the request
2. normalize the email appropriately
3. locate the account
4. check account status
5. verify the password
6. apply failed-attempt protection
7. create a secure session
8. establish the authenticated cookie
9. record the login event
10. return only the minimum necessary response

The response must never contain:

- password hash
- password
- session secret
- internal security data

---

# 12. Login Error Messages

Authentication failures must not unnecessarily reveal whether an account exists.

Avoid:

```text
Email does not exist.
```

and:

```text
Password is incorrect.
```

Prefer a generic response such as:

```text
Invalid email or password.
```

This reduces account-enumeration risk.

---

# 13. Failed Login Attempts

Failed login attempts must be tracked.

The system must protect against:

- brute-force attacks
- credential stuffing
- automated password guessing

Protection should combine multiple controls rather than relying on a single IP-based limit.

Possible signals include:

- account
- IP
- time window
- request rate
- authentication endpoint

The implementation must avoid creating an easy permanent account-denial mechanism.

---

# 14. Account Lockout

The system must support temporary account lockout after repeated failed authentication attempts.

Example conceptual policy:

```text
Failed attempts
      ↓
Threshold reached
      ↓
Temporary lock
      ↓
Cooldown
      ↓
Authentication available again
```

Exact thresholds and durations must be configurable and documented.

Permanent lockout based solely on repeated failed login attempts should not be used because attackers could intentionally trigger it against legitimate accounts.

---

# 15. Rate Limiting

Login requests must be rate-limited.

Rate limiting must be applied server-side.

It must not rely exclusively on JavaScript.

Rate-limit responses should not expose internal security configuration.

The system should use layered protection where appropriate:

```text
IP-based protection
+
Account-based protection
+
Global endpoint protection
```

---

# 16. Session Creation

After successful authentication, the server creates a new authenticated session.

Conceptual flow:

```text
Credentials
   ↓
Verification
   ↓
Authentication Success
   ↓
Create Session
   ↓
Set Secure Cookie
   ↓
Authenticated Requests
```

The session identifier must be:

- cryptographically random
- unpredictable
- sufficiently long
- meaningless by itself
- free of user information

OWASP recommends cryptographically secure, unpredictable session identifiers and notes that a session ID should not contain sensitive information or PII.

---

# 17. Session Storage

The preferred architecture is server-side session state.

Conceptually:

```text
Browser
   │
   │ session cookie
   ↓
Server
   │
   │ session lookup
   ↓
Database / Session Store
```

The browser does not hold the user's authorization state as trusted data.

---

# 18. Session Cookie

The authenticated session must use a secure cookie.

The cookie should use appropriate security attributes, including:

```text
HttpOnly
Secure
SameSite
```

The exact `SameSite` policy must match the application's deployment and authentication architecture.

Session cookies must not be exposed to normal client-side JavaScript.

OWASP specifically recommends cookies for session identifiers and warns against storing authentication/session tokens in Web Storage because JavaScript can access them.

---

# 19. No Authentication Tokens in localStorage

The application must not store:

```text
session IDs
JWTs
refresh tokens
authentication tokens
passwords
```

in:

```text
localStorage
sessionStorage
```

unless an explicitly reviewed architecture requires a different mechanism.

For this project, the default policy is:

> Authentication state is managed through secure server-controlled cookies.

---

# 20. Session ID Content

A session identifier must not contain:

```text
email
user ID
role
password
permissions
```

Example of bad design:

```text
admin@example.com:admin:12345
```

A session ID should be an opaque random value.

---

# 21. Session Identifier Entropy

Session identifiers must be generated using a cryptographically secure random generator.

The system must not use:

```text
Math.random()
timestamp
incrementing IDs
user ID
email hash
```

as a session secret.

OWASP recommends at least 128 bits of entropy for custom session identifiers, with stronger values appropriate where applicable.

---

# 22. Session Storage Security

If raw session secrets are stored server-side, access to the session store must be tightly restricted.

A preferred architecture is:

```text
Session ID / public identifier
+
Random secret verifier
```

with only a one-way verifier stored server-side when appropriate.

The exact implementation must be documented before coding.

---

# 23. Session Expiration

Sessions must have server-enforced expiration.

The application must not rely on:

```text
closing browser
```

as the security mechanism for ending a session.

At minimum, the implementation should define:

- idle timeout
- absolute lifetime
- logout invalidation

Exact values must be chosen during implementation based on the administrative nature of the account.

---

# 24. Session Invalidation on Logout

Logout must invalidate the session server-side.

The system must not only delete the browser cookie.

Required behavior:

```text
Logout
 ↓
Invalidate server session
 ↓
Expire authentication cookie
 ↓
Future request
 ↓
Authentication rejected
```

This is mandatory because deleting a browser cookie does not invalidate a stolen copy of the session identifier.

---

# 25. Session Invalidation After Password Change

When an administrator changes their password, existing sessions should be invalidated according to the approved session policy.

At minimum, the password-changing session must be handled safely.

For high-security operations, requiring re-authentication is appropriate. OWASP recommends re-authentication for sensitive account changes.

---

# 26. Session Invalidation After Account Disable

If an account becomes:

```text
DISABLED
```

all active sessions for that account must be invalidated.

The disabled account must not remain authenticated through an existing session.

---

# 27. Session Invalidation After Lockout

A security-sensitive lockout event should be able to invalidate active sessions when required by the security policy.

This is particularly important if lockout occurs as part of an account-compromise response.

---

# 28. Session Fixation Protection

Authentication must create a fresh authenticated session.

The application must not allow an attacker-controlled pre-authentication session identifier to become the authenticated administrator session.

OWASP identifies session ID regeneration as a required defense against session fixation.

---

# 29. Authentication Over HTTPS

Production authentication must operate exclusively over HTTPS.

This includes:

- login
- authenticated pages
- admin APIs
- logout
- password changes
- password reset

TLS must protect the entire authenticated session, not only the login request.

---

# 30. Admin Route Protection

All admin routes must be protected server-side.

Example:

```text
/admin
/admin/pages
/admin/media
/admin/blog
/admin/seo
/admin/settings
```

must verify authentication before returning protected content.

Protection must happen before sensitive data is exposed.

---

# 31. Admin API Protection

Every protected admin API must independently verify authentication.

Example:

```text
POST /api/admin/pages
PATCH /api/admin/pages/:id
DELETE /api/admin/media/:id
POST /api/admin/blog
```

must not rely on the browser having previously loaded `/admin`.

---

# 32. Authorization Check

After authentication:

```text
Authenticated?
      ↓
Role/Permission Check
      ↓
Operation Allowed?
      ↓
Execute
```

Authentication alone does not authorize an operation.

---

# 33. Direct API Access

An administrator endpoint must remain secure if called directly using:

- browser developer tools
- curl
- Postman
- custom scripts
- another HTTP client

The API must never depend on the intended UI flow.

---

# 34. Logout

Logout must:

1. authenticate the current session
2. invalidate the server-side session
3. expire the authentication cookie
4. prevent reuse of the previous session
5. redirect or return an appropriate response
6. record the security event where appropriate

After logout:

```text
previous session = invalid
```

---

# 35. Multiple Sessions

The system should support multiple administrator sessions unless a later security requirement explicitly disables them.

Each login creates a distinct session.

Conceptually:

```text
Admin Account
 ├── Session A
 ├── Session B
 └── Session C
```

The architecture should allow future functionality such as:

```text
Sign out all sessions
```

without requiring a redesign.

---

# 36. Session Revocation

The server must be able to revoke:

- one session
- all sessions for a user
- all sessions after a critical security event

This capability is important for incident response.

---

# 37. Password Change

Password change requires:

1. authenticated session
2. current password verification
3. new password validation
4. secure hashing
5. password update
6. session-security handling
7. security event logging

The current password should be verified before allowing a sensitive password change. OWASP explicitly recommends this.

---

# 38. Password Reset

Password reset is an administrative security feature and must be implemented only when required.

If implemented, it must use:

- single-use reset tokens
- short expiration
- cryptographically secure randomness
- server-side validation
- token invalidation after use
- rate limiting
- generic account responses
- secure HTTPS delivery

Reset tokens must never be logged.

---

# 39. Password Reset Token Storage

Reset tokens should not be stored as reusable plaintext secrets when a secure verifier-based design is practical.

Conceptually:

```text
Reset Token
     ↓
Hash / Verify
     ↓
Database
```

The database should contain enough information to verify the reset request without exposing a reusable reset secret.

---

# 40. Password Reset Enumeration

The password-reset flow must not reveal whether an email address belongs to an administrator account.

Bad:

```text
No account exists for this email.
```

Preferred:

```text
If the account exists, further instructions will be provided.
```

---

# 41. Password Reset Session Handling

After a successful password reset:

- the reset token must become invalid
- existing sessions should be reviewed/invalidate according to security policy
- the account should be considered potentially compromised
- security events should be recorded

---

# 42. Re-authentication

The system should require current authentication credentials again before particularly sensitive actions.

Examples:

- changing password
- changing administrator email
- changing security settings
- managing administrator accounts
- changing highly sensitive system configuration

Re-authentication reduces the impact of an unattended or temporarily compromised session.

---

# 43. Email Changes

If administrator email changes are supported:

1. require authentication
2. require re-authentication where appropriate
3. validate the new email
4. protect the operation against CSRF
5. invalidate/review existing sessions if necessary
6. log the change
7. require verification if email verification is part of the approved architecture

---

# 44. Account Creation

Administrator accounts must not have a public registration page.

Initial administrator creation must happen through a controlled setup process.

The system must never expose:

```text
/admin/register
```

as an unrestricted public registration mechanism.

---

# 45. Default Credentials

Production must never use:

```text
admin / admin
admin / password
admin@example.com / password
```

or any predictable default password.

If an initial administrator must be created during deployment, the setup process must require a securely generated or explicitly supplied credential.

---

# 46. Account Enumeration

Authentication endpoints must minimize information that reveals:

- whether an email exists
- whether an account is locked
- whether an account is disabled
- whether the password was correct

Different internal states may be recorded server-side while the external response remains appropriately generic.

---

# 47. Timing Considerations

Authentication responses should avoid obvious timing differences that allow attackers to distinguish:

```text
existing account
```

from:

```text
non-existing account
```

The implementation must avoid unnecessary early exits that create measurable account-enumeration signals.

---

# 48. Authentication Logging

Security events may be logged:

```text
login success
login failure
logout
account lock
password change
password reset request
password reset completion
session revocation
```

Logs must never contain:

```text
password
password hash
session token
reset token
authentication secret
```

---

# 49. Login Audit Information

Where appropriate, audit records may include:

- user ID
- event type
- timestamp
- success/failure
- relevant security context

IP addresses and user-agent information should only be retained when justified by the security/operational requirement and documented under `data.md`.

---

# 50. Brute-Force Protection Testing

The authentication system must be tested against:

- repeated wrong passwords
- rapid login attempts
- distributed attempts
- repeated attempts against one account
- repeated attempts against many accounts
- lockout bypass
- rate-limit bypass
- account enumeration

---

# 51. Session Security Testing

Tests must verify:

### Test A — Valid Session

```text
Login
↓
Access admin
↓
Allowed
```

### Test B — No Session

```text
Request admin
↓
Rejected / redirected
```

### Test C — Logout

```text
Login
↓
Logout
↓
Reuse previous session
↓
Rejected
```

### Test D — Expired Session

```text
Expired session
↓
Admin request
↓
Rejected
```

### Test E — Disabled Account

```text
Authenticated user
↓
Account disabled
↓
Existing session
↓
Rejected
```

### Test F — Invalid Session

```text
Random / malformed session
↓
Admin request
↓
Rejected
```

---

# 52. Authorization Testing

For every protected operation:

```text
No session          → DENY
Invalid session     → DENY
Disabled account    → DENY
Wrong role          → DENY
Valid role          → ALLOW
```

These cases must be automated wherever practical.

---

# 53. Cookie Testing

Authentication cookies must be tested for:

- `HttpOnly`
- `Secure`
- appropriate `SameSite`
- appropriate expiration
- correct domain
- correct path
- absence of sensitive data
- invalidation after logout

---

# 54. Browser Storage Testing

Automated or manual security checks must verify that authentication secrets are not stored in:

```text
localStorage
sessionStorage
IndexedDB
URL parameters
DOM attributes
visible page data
```

unless explicitly approved by the security architecture.

---

# 55. Authentication API Security

Authentication APIs must have:

- request validation
- rate limiting
- safe error handling
- secure cookies
- CSRF protection where applicable
- audit logging
- appropriate cache controls
- no secret leakage

---

# 56. Cache Control

Authenticated or sensitive responses must not be cached in a way that could expose private data.

Sensitive authenticated responses should use appropriate cache-control policies.

OWASP recommends `Cache-Control: no-store` for responses containing session identifiers or sensitive session information.

---

# 57. Authentication Data Model

The implementation should use dedicated models rather than embedding all authentication state into unrelated tables.

Conceptually:

```text
User
Session
PasswordResetToken
AuditLog
```

The exact Prisma schema must be documented in the database/data architecture before implementation.

---

# 58. Authentication Data Ownership

Authentication data belongs to the authentication subsystem.

Other subsystems must not directly modify:

```text
passwordHash
session
lockedUntil
failedLoginCount
```

without going through the approved authentication service.

This prevents authentication rules from being duplicated across the application.

---

# 59. Authentication Service Boundary

Authentication logic should have a clear service boundary.

Conceptually:

```text
features/auth/
├── login
├── logout
├── session
├── password
├── reset
├── authorization
└── security
```

Exact folder structure may vary according to the approved architecture.

Authentication logic must not be scattered throughout unrelated UI components.

---

# 60. No Client-Side Security Decisions

The following are UX state, not security state:

```text
isAdmin
role
permissions
authenticated
```

The client may receive information necessary to render the UI, but the server remains authoritative.

---

# 61. Authentication Dependency Rule

A third-party authentication library may be used if it:

- is actively maintained
- has appropriate security characteristics
- fits the architecture
- does not undermine the privacy model
- does not introduce unnecessary complexity

Do not add an authentication library solely because it is popular.

The selected authentication architecture must be documented before implementation.

---

# 62. Authentication Definition of Done

Authentication is not complete until:

- [ ] Passwords are securely hashed
- [ ] Login works
- [ ] Invalid credentials are rejected
- [ ] Generic authentication errors exist
- [ ] Rate limiting works
- [ ] Brute-force protection works
- [ ] Account lockout works
- [ ] Secure session creation works
- [ ] Secure cookies are configured
- [ ] Session expiration works
- [ ] Logout invalidates the server session
- [ ] Session fixation protection is tested
- [ ] Admin routes are protected
- [ ] Admin APIs are protected
- [ ] Authorization is server-side
- [ ] Password change works securely
- [ ] Password reset is secure if implemented
- [ ] Disabled accounts cannot authenticate
- [ ] Existing sessions are revoked where required
- [ ] Authentication secrets are absent from browser storage
- [ ] Authentication secrets are absent from logs
- [ ] Security tests pass
- [ ] Regression tests pass
- [ ] Documentation is updated

---

# 63. Authentication Approval States

Authentication implementation must use:

```text
PLANNED
↓
IMPLEMENTED
↓
UNIT TESTED
↓
INTEGRATION TESTED
↓
SECURITY TESTED
↓
E2E TESTED
↓
DOCUMENTED
↓
APPROVED
```

A working login screen is not equivalent to approved authentication.

---

# 64. Non-Negotiable Rules

1. Never store plaintext passwords.
2. Never store authentication tokens in localStorage.
3. Never trust client-side authentication state.
4. Never trust client-supplied roles.
5. Never allow public administrator registration.
6. Never use predictable session identifiers.
7. Never rely on browser closure to invalidate sessions.
8. Never invalidate only the browser cookie during logout.
9. Never expose reset tokens in logs.
10. Never expose passwords or password hashes.
11. Never bypass server-side authorization.
12. Never disable brute-force protection for convenience.
13. Never use predictable default administrator credentials.
14. Never approve authentication with failing security tests.
15. Never change authentication architecture silently.

---

# 65. Relationship With Other Security Documents

```text
PROJECT_RULES.md
       ↓
security.md
       ↓
auth.md
       ↓
data.md
       ↓
hack.md
       ↓
checklist.md
```

`security.md` defines the general security requirements.

`auth.md` defines authentication and session-specific requirements.

`data.md` will define:

- what data is stored
- where it is stored
- retention
- deletion
- ownership
- sensitive data handling

`hack.md` will define attack paths and corresponding mitigations.

`checklist.md` will provide the final implementation/security verification checklist.

---

# 66. Final Authentication Principle

The authentication system must follow this rule:

> The browser can request authentication. The server decides authentication. The server owns the session. The server decides authorization. Logout destroys the server-side session. No client-side value can grant administrator access.
