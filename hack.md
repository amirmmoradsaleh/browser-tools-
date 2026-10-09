# Attack Paths & Threat Model

**Document:** `hack.md`  
**Version:** 1.0  
**Status:** Mandatory  
**Scope:** Entire application  
**Priority:** Critical

---

# 1. Purpose

This document models realistic attack paths against the application.

The objective is not to create a theoretical list of every possible vulnerability.

The objective is to answer:

1. What can an attacker reach?
2. What does the attacker want?
3. What trust boundary must be crossed?
4. How could the attack work?
5. What prevents it?
6. How do we test that prevention?
7. What happens if the prevention fails?

Threat modeling is treated as a continuous engineering activity rather than a one-time security exercise. OWASP recommends decomposing the application, identifying and ranking threats, defining mitigations, and reviewing/validating them repeatedly as the system changes.

---

# 2. Security Assets

The primary assets are:

```text
1. Administrator accounts
2. Administrator sessions
3. Password hashes
4. Password reset mechanisms
5. Database
6. CMS content
7. Draft content
8. Media Library
9. Object/file storage
10. Audit logs
11. Environment secrets
12. API keys
13. Encryption keys
14. Application source code
15. Production infrastructure
16. Public website integrity
17. User privacy
18. Browser processing isolation
```

---

# 3. Primary Attackers

The system must consider at least these attacker classes.

## Anonymous Internet Attacker

Capabilities:

- access public pages
- call public endpoints
- submit arbitrary HTTP requests
- inspect client-side code
- manipulate browser requests
- send malformed data
- attempt automated attacks

---

## Automated Attacker

Capabilities:

- high request volume
- credential stuffing
- brute force
- endpoint enumeration
- crawling
- fuzzing
- repeated malformed requests

---

## Malicious Administrator / Compromised Admin

Capabilities:

- authenticated CMS access
- content modification
- media manipulation
- access to privileged operations

This threat is important because administrator accounts have a much larger attack surface.

---

## Attacker With Stolen Session

Capabilities:

- impersonate the authenticated administrator
- access protected resources
- perform authorized operations

The application must therefore be able to invalidate sessions.

---

## Attacker With Source-Code Access

Potential capabilities:

- inspect architecture
- discover endpoints
- discover implementation weaknesses
- search for accidentally committed secrets

Therefore secrets must never depend on source-code secrecy.

---

# 4. Trust Boundaries

Primary trust boundaries:

```text
Internet
   ↓
Public Web Application

Browser
   ↓
Server

Anonymous User
   ↓
Admin Authentication

Authenticated Admin
   ↓
Authorization Boundary

Application
   ↓
PostgreSQL

Application
   ↓
Object Storage

Application
   ↓
Third-Party Services
```

Every boundary must validate data crossing it.

---

# 5. Attack Surface

The application's attack surface includes:

```text
Public pages
Tool pages
HTTP requests
API routes
Authentication
Admin routes
Admin APIs
Cookies
Query parameters
Route parameters
Request bodies
CMS forms
Page Builder
Blog editor
SEO fields
Media Library
File uploads
File downloads
Database
Object storage
Logs
Backups
Third-party integrations
Dependencies
Deployment configuration
```

OWASP defines attack surface broadly as the paths through which data or commands enter or leave the application, together with the code and valuable data protecting those paths.

---

# 6. Threat Categories

Threat analysis uses the following categories:

```text
Spoofing
Tampering
Repudiation
Information Disclosure
Denial of Service
Elevation of Privilege
```

These categories are used to review each major trust boundary.

---

# 7. Attack Path: Administrator Brute Force

## Target

```text
POST /api/auth/login
```

## Attack

Attacker repeatedly submits passwords against an administrator account.

## Objective

Gain administrator access.

## Controls

- rate limiting
- failed-attempt tracking
- temporary account lockout
- secure password hashing
- generic authentication errors
- monitoring

## Tests

```text
100+ failed requests
        ↓
Rate limit triggered
        ↓
Account protection triggered
        ↓
Valid password cannot bypass protection unexpectedly
```

---

# 8. Attack Path: Credential Stuffing

## Target

Administrator login.

## Attack

Attacker uses leaked username/password combinations from another service.

## Risk

Password reuse can compromise the administrator account.

## Controls

- strong password policy
- rate limiting
- login monitoring
- account protection
- optional future MFA

Credential stuffing and password spraying are distinct from ordinary brute force and require defenses against repeated credential attempts at scale.

## Future Security Enhancement

MFA may be introduced later if the project requires stronger administrator protection.

It must not be added outside the roadmap without approval.

---

# 9. Attack Path: Account Enumeration

## Target

Login and password-reset endpoints.

## Attack

Attacker submits different email addresses and compares responses.

## Objective

Discover valid administrator accounts.

## Controls

All authentication responses must be sufficiently generic.

Examples:

```text
Invalid email or password.
```

rather than:

```text
Account does not exist.
```

OWASP specifically recommends generic responses for incorrect credentials, nonexistent accounts, and locked/disabled accounts to reduce enumeration.

## Tests

Compare:

```text
existing account
non-existing account
locked account
disabled account
```

Responses should not provide an obvious enumeration signal.

---

# 10. Attack Path: Session Theft

## Target

Administrator session.

## Attack

Attacker obtains a valid session identifier.

## Objective

Impersonate administrator.

## Controls

- HTTPS
- Secure cookie
- HttpOnly
- SameSite
- session expiration
- session invalidation
- session rotation where required
- no session tokens in URLs
- no session tokens in localStorage

## Tests

```text
Steal/obtain session
        ↓
Use session
        ↓
Access admin
        ↓
Logout original session
        ↓
Reuse session
        ↓
DENIED
```

---

# 11. Attack Path: Session Fixation

## Attack

Attacker attempts to force or predict a pre-authentication session and have it become authenticated.

## Control

Generate/regenerate an authenticated session after successful login.

## Test

```text
Pre-auth session
        ↓
Login
        ↓
Authenticated session identifier changes
```

---

# 12. Attack Path: Logout Bypass

## Attack

Attacker reuses an old session after the victim logs out.

## Vulnerable Design

Only deleting the browser cookie.

## Required Design

```text
Logout
 ↓
Server session revoked
 ↓
Cookie expired
 ↓
Old session unusable
```

## Test

Reuse the old authenticated session after logout.

Expected:

```text
401 / authentication failure
```

or equivalent protected response.

---

# 13. Attack Path: Authorization Bypass

## Target

Admin API.

## Attack

Anonymous or low-privilege user directly calls:

```text
PATCH /api/admin/pages/123
```

## Objective

Modify protected content.

## Controls

Every protected API independently checks:

```text
authenticated?
      ↓
authorized?
      ↓
operation allowed?
```

## Test

Direct API calls must be rejected without valid authorization.

---

# 14. Attack Path: IDOR

## Example

Attacker changes:

```text
/api/admin/pages/100
```

to:

```text
/api/admin/pages/101
```

## Objective

Access or modify an object the attacker should not control.

## Controls

Object-level authorization.

## Test

For every protected object operation:

```text
valid authorized object → ALLOW
unauthorized object → DENY
nonexistent object → safe failure
```

---

# 15. Attack Path: Privilege Escalation

## Attack

Attacker modifies a client-controlled field:

```text
role=ADMIN
```

or:

```text
isAdmin=true
```

## Objective

Become administrator.

## Controls

Role must be server-controlled.

The client must never be able to assign its own role.

## Test

Manipulate:

- request body
- cookies
- local storage
- query parameters
- headers

Expected:

```text
Privilege unchanged.
```

---

# 16. Attack Path: XSS Through Page Builder

## Target

Page Builder.

## Attack

Attacker injects:

```text
<script>...</script>
```

or dangerous HTML attributes into block content.

## Objective

Execute JavaScript when another administrator or visitor opens the page.

## Controls

- structured blocks
- controlled rendering
- sanitization
- output encoding
- no arbitrary JavaScript
- server-side validation

## Test

Attempt payloads through every user-controlled block property.

---

# 17. Attack Path: Stored XSS Through Blog

## Target

Blog content.

## Attack

Malicious HTML/JavaScript is stored in a blog post.

## Objective

Execute code against visitors or administrators.

## Controls

- maintained HTML sanitization library
- restricted HTML
- safe rendering
- controlled embeds
- output encoding

For rich user-authored HTML, OWASP recommends a maintained HTML sanitization library rather than relying on regular-expression filtering.

---

# 18. Attack Path: XSS Through SEO Fields

## Target

SEO title, description, Open Graph fields or schema.

## Attack

Attacker inserts executable content.

## Controls

Each field must have an explicit type and output context.

Schema JSON must be validated.

SEO fields must never be treated as arbitrary HTML or JavaScript.

---

# 19. Attack Path: Malicious SVG

## Target

Media Library.

## Attack

Administrator uploads an SVG containing active content.

## Objective

Execute code when SVG is viewed.

## Controls

- SVG allowlist
- SVG sanitization
- controlled serving
- content-type handling
- safe rendering policy

SVG must be treated as potentially active content.

---

# 20. Attack Path: Malicious File Upload

## Target

CMS Media Library.

## Attack

Attacker uploads a malicious file.

Potential outcomes:

- parser exploit
- XSS
- malware distribution
- server-side execution
- storage abuse
- file overwrite

## Controls

- administrator authorization
- allowlisted file types
- MIME validation
- file-signature validation where appropriate
- file size limits
- generated storage names
- safe storage location
- non-executable storage
- content scanning where justified

OWASP recommends layered file-upload defenses rather than trusting a single extension or MIME check.

---

# 21. Attack Path: Filename Injection

## Attack

Upload filename:

```text
../../../../important-file
```

or equivalent encoded traversal.

## Objective

Write outside the intended storage location.

## Controls

- generated filenames
- no direct filesystem path construction
- path normalization
- path traversal tests
- storage abstraction

## Test

Submit filenames containing:

```text
../
..\
%2e%2e
null bytes
absolute paths
reserved names
```

Expected:

```text
Safe generated storage key.
```

---

# 22. Attack Path: File Overwrite

## Attack

Attacker attempts to upload a file with a name matching an existing file.

## Objective

Overwrite legitimate content.

## Control

Never use the original filename as the storage identifier.

Use generated IDs/keys.

---

# 23. Attack Path: File Type Spoofing

## Attack

Upload a malicious file while claiming:

```text
image/png
```

or:

```text
.pdf
```

## Objective

Bypass validation.

## Controls

- extension allowlist
- MIME inspection
- file-signature/content validation
- parser-level validation
- size limits

The `Content-Type` header is user-controlled and cannot be trusted as the sole file-security control.

---

# 24. Attack Path: Decompression Bomb

## Target

Image/PDF/archive processing.

## Attack

A small compressed file expands into an enormous amount of data.

## Objective

Exhaust:

- memory
- CPU
- storage
- processing time

## Controls

- file-size limits
- decoded-dimension limits
- page-count limits
- decompression limits
- processing time limits
- worker/resource isolation where applicable

---

# 25. Attack Path: ZIP Bomb

If archive processing is implemented:

## Attack

Upload a tiny archive with extreme decompression expansion.

## Controls

- compressed size limit
- decompressed size limit
- file-count limit
- nesting limit
- extraction path validation
- timeout
- memory limits

Archive processing must not be implemented without these controls.

---

# 26. Attack Path: Malicious PDF

## Target

PDF tools.

## Attack

Provide a malformed or malicious PDF.

## Potential Impact

- parser crash
- excessive memory consumption
- CPU exhaustion
- vulnerability exploitation

## Controls

- parser updates
- page limits
- file-size limits
- controlled parser execution
- validation
- failure isolation
- security testing

---

# 27. Attack Path: Malicious Image

## Target

Image tools.

## Attack

Provide a malformed image or extreme-dimension image.

## Objective

Crash or exhaust browser/server resources.

## Controls

- format validation
- decoded dimension limits
- file-size limits
- memory-aware processing
- worker isolation
- graceful failure

---

# 28. Attack Path: Browser Memory Exhaustion

## Target

Public image/PDF tools.

## Attack

User supplies an extremely large file or dimensions.

## Impact

Browser becomes unresponsive.

## Controls

Before processing:

```text
File Size
Dimensions
Page Count
Estimated Memory
```

must be checked where possible.

The application should reject clearly unreasonable workloads before allocating excessive memory.

---

# 29. Attack Path: Server-Side File Upload Through Public Tool

This is a critical product-specific attack path.

## Attack

An attacker identifies an API that unexpectedly accepts a public processing file.

## Objective

Turn a browser-only tool into a server-side upload mechanism.

## Risk

This violates the application's core privacy architecture and creates a new server-side attack surface.

## Controls

Public processing tools must:

- use browser APIs
- use workers where appropriate
- avoid upload endpoints
- avoid sending file bodies to the server
- avoid analytics payloads containing files
- avoid error-reporting payloads containing files

## Test

Inspect browser Network requests while processing files.

Expected:

```text
No request containing the user's processing file.
```

---

# 30. Attack Path: Analytics File Leakage

## Attack

A developer accidentally sends file metadata or content through analytics.

Example:

```text
analytics.track("conversion", {
    filename,
    file
})
```

## Risk

User file information leaves the browser.

## Controls

Analytics schema must explicitly prohibit:

```text
File
Blob
ArrayBuffer
File contents
Sensitive filenames
```

## Test

Network inspection during every public tool must verify that no processing file reaches analytics.

---

# 31. Attack Path: Error Reporting File Leakage

## Attack

A processing exception automatically serializes a `File`, Blob, buffer or filename into an error-reporting service.

## Controls

Error reporting must sanitize:

- exceptions
- request data
- tool state
- filenames
- processing metadata

## Test

Force processing errors and inspect outgoing network requests.

---

# 32. Attack Path: SSRF

If remote URL fetching is ever implemented:

## Attack

Attacker supplies:

```text
http://localhost
```

or internal/private network destinations.

## Objective

Access internal infrastructure.

## Controls

- URL validation
- IP range restrictions
- DNS rebinding protection
- redirect validation
- blocked private addresses
- blocked metadata endpoints
- outbound request policy

Remote fetching must not be added casually.

---

# 33. Attack Path: Open Redirect

## Attack

Attacker manipulates:

```text
?next=https://malicious.example
```

## Objective

Use the application for phishing.

## Controls

- internal-path allowlist
- validated redirect destinations
- reject arbitrary external redirects

---

# 34. Attack Path: SQL Injection

## Target

API or CMS inputs.

## Attack

Inject SQL through:

- search
- IDs
- filters
- query parameters
- CMS fields

## Controls

- Prisma safe query APIs
- parameterized queries
- server-side validation
- no string concatenation

## Test

Fuzz database-facing inputs with SQL injection payloads.

Expected:

```text
No query manipulation.
No database error leakage.
```

---

# 35. Attack Path: CSRF

## Target

Authenticated state-changing operations.

Examples:

```text
Change password
Delete media
Publish page
Delete page
Modify settings
```

## Objective

Cause an authenticated administrator's browser to perform an unintended action.

## Controls

- appropriate SameSite cookie configuration
- CSRF defenses appropriate to the authentication architecture
- origin/request validation where applicable
- re-authentication for highly sensitive operations

---

# 36. Attack Path: Clickjacking

## Target

Admin interface.

## Attack

Embed the admin application inside a malicious frame.

## Objective

Trick an administrator into clicking something.

## Controls

Appropriate frame/embedding protections and security headers.

---

# 37. Attack Path: Dependency Vulnerability

## Attack

Known vulnerability exists in:

- image library
- PDF library
- parser
- framework
- authentication package
- CMS package

## Controls

- dependency review
- vulnerability scanning
- controlled upgrades
- lockfiles
- security advisories
- regression testing

Particular attention must be given to file-processing dependencies because untrusted files are directly processed by them.

---

# 38. Attack Path: Malicious Client Modification

## Attack

Attacker modifies JavaScript in DevTools.

Examples:

```text
isAdmin = true
```

or:

```text
canDelete = true
```

## Objective

Bypass UI restrictions.

## Control

All security decisions occur server-side.

---

# 39. Attack Path: API Parameter Tampering

## Attack

Attacker modifies:

```text
userId
role
pageId
mediaId
status
published
```

## Control

Sensitive values must be derived from authenticated server state where possible.

Never trust client-provided ownership or permission values.

---

# 40. Attack Path: Draft Content Exposure

## Attack

Attacker discovers an unpublished page URL or API endpoint.

## Objective

Read confidential content.

## Controls

- publication-state checks
- server-side authorization
- noindex for drafts where appropriate
- sitemap exclusion
- public API filtering

---

# 41. Attack Path: Draft Content in Search Engines

## Attack

Draft content accidentally appears in:

- sitemap
- public HTML
- structured data
- public API

## Control

Only published content may enter public indexing flows.

---

# 42. Attack Path: Media Access Control Bypass

## Attack

Attacker modifies a media identifier in a request.

## Objective

Access private media.

## Controls

- media visibility state
- authorization
- object-level access control
- signed/controlled URLs where appropriate

---

# 43. Attack Path: Media URL Guessing

## Attack

Attacker enumerates predictable media URLs.

## Objective

Discover private files.

## Controls

Private media must not rely on obscurity.

Use authorization-controlled access or sufficiently protected storage mechanisms.

---

# 44. Attack Path: Audit Log Tampering

## Attack

Administrator or compromised process attempts to modify/delete evidence.

## Objective

Hide malicious actions.

## Controls

- restricted audit-log write access
- append-oriented architecture where appropriate
- separate permissions
- controlled deletion
- monitoring

Audit logs must not be treated as ordinary CMS content.

---

# 45. Attack Path: Log Injection

## Attack

Attacker supplies malicious strings designed to manipulate log output.

Example:

```text
filename = "normal\nFAKE ADMIN LOGIN SUCCESS"
```

## Controls

- structured logging
- log encoding
- sanitized untrusted values
- no raw request-body logging

---

# 46. Attack Path: Sensitive Data in Logs

## Attack

An error handler logs:

```text
password
session cookie
API key
reset token
```

## Impact

Secondary credential compromise.

## Controls

Explicit log redaction.

Security-sensitive authentication successes/failures and authorization failures should be logged, while secrets and unnecessary sensitive values must remain excluded.

---

# 47. Attack Path: Secret Leakage Through Git

## Attack

Developer commits:

```text
.env
DATABASE_URL
AUTH_SECRET
API_KEY
```

## Objective

Attacker obtains production credentials.

## Controls

- `.gitignore`
- secret scanning
- CI checks
- environment-based secrets
- credential rotation

---

# 48. Attack Path: Environment Variable Exposure

## Attack

Private environment variable is accidentally exposed through client-side code.

## Controls

Only explicitly public configuration may cross the client boundary.

The server must own private environment variables.

---

# 49. Attack Path: Prototype Pollution / Object Manipulation

## Attack

Attacker supplies malicious JSON/object structures.

## Objective

Modify security-sensitive application state.

## Controls

- schema validation
- explicit property selection
- safe object merging
- no trust in client-provided privilege fields

---

# 50. Attack Path: Resource Exhaustion Through APIs

## Attack

Attacker sends:

```text
huge JSON
large arrays
deeply nested objects
expensive filters
repeated requests
```

## Objective

Consume server resources.

## Controls

- body size limits
- array limits
- nesting limits
- query limits
- pagination
- rate limiting
- timeouts

---

# 51. Attack Path: Repeated Expensive Operations

## Target

Admin APIs and any future server-side processing.

## Attack

Repeatedly invoke expensive operations.

## Controls

- authentication
- authorization
- rate limiting
- resource limits
- operation-specific quotas where required

---

# 52. Attack Path: Cache Poisoning / Private Data Leakage

## Attack

Incorrect caching causes:

```text
private admin response
```

to be returned to another request.

## Controls

- correct cache-control
- separation of public/private rendering
- no caching of sensitive responses
- cache-key review

---

# 53. Attack Path: Third-Party Script Compromise

## Attack

A third-party script included on the site becomes malicious or compromised.

## Risk

Script can access application data available to its execution context.

## Controls

- minimize third-party scripts
- review every integration
- Content Security Policy
- avoid exposing sensitive data to third-party scripts
- isolate admin from unnecessary third-party scripts

---

# 54. Attack Path: Supply-Chain Compromise

## Attack

A dependency/package becomes compromised.

## Controls

- dependency minimization
- lockfiles
- version review
- vulnerability scanning
- update review
- reproducible builds where practical

---

# 55. Attack Path: Database Credential Compromise

## Attack

Attacker obtains application database credentials.

## Impact

Potential access to:

- admin data
- content
- sessions
- audit logs

## Controls

- least-privilege DB user
- private network access
- secret management
- credential rotation
- monitoring
- backups

---

# 56. Attack Path: Backup Compromise

## Attack

Attacker gains access to backups.

## Impact

Potential historical data exposure.

## Controls

- encrypted backups
- restricted access
- separate credentials
- retention limits
- restore testing
- backup monitoring

---

# 57. Attack Path: Privileged Administrator Compromise

## Attack

Administrator account is compromised.

## Impact

Attacker can potentially:

- modify pages
- inject malicious content
- upload files
- modify SEO
- alter site behavior
- access private CMS information

## Controls

- strong authentication
- account protection
- session revocation
- audit logs
- re-authentication for sensitive actions
- future MFA if approved

---

# 58. Attack Path: Stored Malicious Content

A compromised administrator can intentionally publish malicious content.

Controls should limit technical damage through:

- structured page blocks
- restricted HTML
- sanitized media
- no arbitrary server-side code
- no arbitrary JavaScript execution

CMS privilege must not equal server-code execution.

---

# 59. Attack Path: Admin-to-Server Code Execution

This is a critical boundary.

A CMS administrator must not automatically be able to:

```text
execute server code
read environment variables
execute shell commands
modify application source
access database credentials
```

CMS functionality must remain data-driven.

---

# 60. Attack Path: Path Traversal Through CMS

Potential targets:

- media deletion
- media retrieval
- import/export
- file management

Controls:

- generated storage keys
- path normalization
- object-storage abstraction
- authorization
- no raw filesystem paths from clients

---

# 61. Attack Path: Unsafe Import

If page/content import is implemented:

## Attack

Malicious structured content is imported.

## Controls

Imported data must pass the same validation and sanitization rules as manually entered content.

Import must not create a bypass around security controls.

---

# 62. Attack Path: Unsafe Export

Exports can expose more information than intended.

Controls:

- authorization
- explicit export schema
- minimum necessary data
- no secrets
- no password hashes unless absolutely required
- controlled download lifetime

---

# 63. Attack Path: Repeated Login From Multiple Locations

Suspicious login behavior may indicate:

- credential stuffing
- stolen credentials
- session compromise

The system should log sufficient authentication events to support investigation.

OWASP recommends monitoring authentication successes and failures because repeated failures and unusual patterns can indicate attacks.

---

# 64. Attack Path: Password Reset Abuse

## Attack

Attacker repeatedly requests password resets for an account.

## Objective

Spam, enumerate accounts, or create denial of service.

## Controls

- rate limiting
- generic responses
- single-use tokens
- short expiration
- security logging

---

# 65. Attack Path: Reset Token Theft

## Attack

Attacker obtains a reset token.

## Objective

Take over account.

## Controls

- HTTPS
- short-lived token
- single-use token
- secure token generation
- no token logging
- invalidation after use

---

# 66. Attack Path: Stale Session After Password Reset

## Attack

Attacker already has a valid session.

Victim resets password.

## Risk

Attacker's existing session remains valid.

## Control

Invalidate existing sessions according to the approved password-reset policy.

---

# 67. Attack Path: Race Condition in Authorization

## Attack

Attacker exploits a timing window between:

```text
permission check
```

and:

```text
database modification
```

## Controls

- server-side atomic operations
- transactions where required
- fresh authorization checks
- correct database constraints

---

# 68. Attack Path: Race Condition in Publishing

## Attack

Multiple requests simultaneously publish/delete/update the same content.

## Controls

- transactional updates
- revision/version handling
- server-side state validation

---

# 69. Attack Path: Denial of Service Through Public Endpoints

## Attack

Attacker floods public APIs/pages.

## Controls

- rate limiting where appropriate
- caching
- infrastructure-level protection
- efficient server rendering
- bounded request processing

Public pages should not expose unnecessary expensive server operations.

---

# 70. Attack Path: Denial of Service Through Authentication

## Attack

Attacker triggers repeated failed authentication attempts.

## Risk

Account lockout becomes a denial-of-service mechanism.

## Controls

Lockout must be designed so that an attacker cannot trivially and permanently disable the administrator account.

---

# 71. Attack Path: Dependency-Based Parser Exploit

File-processing libraries are particularly sensitive because they process attacker-controlled content.

Controls:

- maintained libraries
- security updates
- input limits
- parser isolation where appropriate
- malformed-file tests
- regression tests

---

# 72. Attack Path: Browser-Side Tool Escape

A public tool must not accidentally execute arbitrary content while processing a file.

Potential risks:

- malicious SVG
- HTML masquerading as another format
- dangerous browser APIs
- unsafe preview rendering

Controls:

- format-specific validation
- safe preview strategy
- sanitization where required
- no direct injection into DOM

---

# 73. Attack Path: Object URL Misuse

Browser tools may create:

```text
blob:
```

URLs.

The application must:

- revoke unnecessary object URLs
- avoid exposing them unnecessarily
- avoid keeping references indefinitely
- avoid injecting untrusted content into unsafe contexts

---

# 74. Attack Path: Cross-Origin Data Leakage

Public processing must remain local.

Third-party origins must not receive user file data through:

- fetch
- analytics
- images
- iframes
- scripts
- error reporting

unless explicitly approved.

---

# 75. Attack Path: Malicious Tool Configuration

If tool configuration is loaded from CMS/database:

## Attack

Attacker manipulates tool configuration.

## Risk

Unexpected processing behavior or security bypass.

## Controls

- schema validation
- allowlisted tool configuration
- versioned tool definitions
- server-side authorization for configuration changes

---

# 76. Attack Path: Tool Registry Tampering

The Tool Registry controls available processing tools.

It must not allow arbitrary runtime code to be injected through database content.

Tool implementation must remain application code.

CMS data may configure approved options, but must not define executable processing logic.

---

# 77. Attack Path: Unauthorized Tool Publication

If CMS controls whether tools appear publicly:

## Attack

Unauthorized administrator publishes an incomplete or unsafe tool.

## Controls

Tool publication must have a documented lifecycle:

```text
PLANNED
IMPLEMENTED
TESTED
SECURITY CHECKED
DOCUMENTED
APPROVED
PUBLISHED
```

---

# 78. Attack Path: Unapproved Feature Introduction

An AI developer adds a feature because it seems useful.

## Risk

New functionality introduces an undocumented attack surface.

## Control

`PROJECT_RULES.md` prohibits feature creep.

Every new feature must be:

1. justified
2. documented
3. assigned to the roadmap
4. security-reviewed
5. tested
6. approved

---

# 79. Attack Path: Security Regression

A previously secure component becomes insecure after a later change.

## Controls

- regression tests
- security checklist
- architecture review
- attack-path review
- CI testing

Every security fix must produce a regression test where practical.

---

# 80. Attack Path: Incorrect Security Assumption

Example:

> "The button is hidden, so the endpoint is protected."

This is invalid.

Security assumptions must be verified at the actual trust boundary.

---

# 81. Attack Path: Client Validation Bypass

## Attack

Attacker disables JavaScript or manually modifies requests.

## Control

Server-side validation for all server-controlled operations.

OWASP explicitly identifies client-only validation as insufficient because it can be bypassed.

---

# 82. Attack Path: Oversized Request

## Attack

Send an extremely large request body.

## Controls

- request size limits
- body parsing limits
- endpoint-specific limits
- rate limiting

---

# 83. Attack Path: Deep JSON

## Attack

Send deeply nested JSON to cause parser/resource exhaustion.

## Controls

- schema validation
- nesting limits
- request-size limits
- parser limits

---

# 84. Attack Path: Regex DoS

## Attack

Supply input designed to cause catastrophic regex backtracking.

## Controls

- bounded input length
- safe regex patterns
- non-backtracking approaches where available
- validation timeouts where appropriate

OWASP specifically recommends bounded input length and avoiding regex patterns that can cause excessive backtracking.

---

# 85. Attack Path: Public API Enumeration

## Attack

Attacker discovers undocumented API routes and calls them directly.

## Controls

Every API endpoint must have:

- documented purpose
- authentication requirement
- authorization requirement
- input schema
- output schema
- rate-limit policy
- security tests

---

# 86. Attack Path: Information Disclosure

Potential leakage through:

- error messages
- stack traces
- response headers
- source maps
- debug endpoints
- logs
- public configuration
- database errors

Controls:

- production-safe errors
- controlled headers
- no debug endpoints
- secret scanning
- configuration review

---

# 87. Attack Path: Source Map Leakage

Production source maps can make application internals easier to inspect.

This is not automatically a vulnerability, but their exposure must be an intentional deployment decision.

They must never contain secrets.

---

# 88. Attack Path: Security Header Misconfiguration

Potential weaknesses:

- missing CSP
- weak framing policy
- missing HSTS
- unsafe content types
- excessive browser permissions

Controls:

Security headers must be tested in production/staging.

---

# 89. Attack Path: CORS Misconfiguration

If CORS is required:

- use explicit origins
- avoid wildcard origins for privileged APIs
- avoid reflecting arbitrary `Origin`
- review credentialed requests

CORS must not become an authorization mechanism.

---

# 90. Attack Path: Public Database Exposure

The PostgreSQL database must never be intentionally exposed directly to the public Internet unless infrastructure architecture explicitly requires it and has compensating controls.

Normal architecture:

```text
Internet
   ↓
Application
   ↓
PostgreSQL
```

Not:

```text
Internet
   ↓
PostgreSQL
```

---

# 91. Attack Path: Object Storage Exposure

Object storage must not expose private CMS files through unrestricted bucket access.

Controls:

- private storage where appropriate
- controlled public objects
- authorization
- generated storage keys
- access policies

---

# 92. Attack Path: Backup Exposure

Backups must not be publicly accessible.

They must not be placed in:

```text
/public
```

or any publicly served directory.

---

# 93. Attack Path: Configuration Exposure

Configuration endpoints must never expose:

```text
DATABASE_URL
AUTH_SECRET
API_KEY
PRIVATE_KEY
```

or equivalent secrets.

---

# 94. Attack Path: Admin URL Discovery

Changing the admin URL is not a substitute for authentication.

Even if:

```text
/admin
```

is changed to another path:

```text
/control-panel
```

the endpoint must remain protected.

Security must never depend on obscurity.

---

# 95. Attack Path: Unauthorized Content Publishing

An authenticated but unauthorized user must not be able to change:

```text
draft → published
```

without appropriate permission.

Publishing is an authorization-controlled action.

---

# 96. Attack Path: Revision Restore Abuse

Restoring a revision changes production content.

Therefore revision restore must require:

- authentication
- authorization
- server-side validation
- audit logging

---

# 97. Attack Path: Media Deletion Abuse

Deleting media may break multiple pages.

The operation must:

- require authorization
- validate media ownership/scope
- handle references safely
- record the operation
- not accept arbitrary storage paths

---

# 98. Attack Path: Navigation Tampering

Navigation controls what users can reach.

Unauthorized changes could:

- redirect visitors
- hide important pages
- create phishing links

Therefore navigation editing requires authorization and URL validation.

---

# 99. Attack Path: Malicious External Links

CMS users may insert external links.

Controls should validate:

- URL schemes
- dangerous schemes such as `javascript:`
- malformed URLs
- optional target/rel behavior

---

# 100. Attack Path: Unsafe Embed

If embeds are supported:

## Attack

Administrator inserts malicious iframe/script.

## Control

Only explicitly approved embed providers should be supported.

Arbitrary scripts must not be permitted.

---

# 101. Attack Path: SEO Poisoning

An attacker compromises CMS access and changes:

- canonical URLs
- titles
- descriptions
- structured data
- redirects

## Impact

SEO damage or phishing.

## Controls

- authorization
- audit logs
- validation
- controlled redirects
- change history

---

# 102. Attack Path: Sitemap Poisoning

Unauthorized or malformed content enters the sitemap.

Controls:

- generate sitemap from published content
- validate URLs
- exclude drafts
- exclude unauthorized resources

---

# 103. Attack Path: Robots Configuration Abuse

An unauthorized user changes robots configuration to:

```text
Disallow: /
```

or manipulates indexing behavior.

Controls:

- authorization
- audit logs
- validation
- preview before publication where appropriate

---

# 104. Attack Path: Malicious Metadata

Metadata such as:

```text
title
alt
caption
description
```

can become an XSS or content-injection vector if rendered unsafely.

All output contexts must be handled appropriately.

---

# 105. Attack Path: Race in Media Deletion

Two requests simultaneously delete or modify the same media.

Controls:

- transaction/locking where necessary
- safe idempotent deletion
- storage/database consistency handling

---

# 106. Attack Path: Orphaned Sensitive Files

Database record is deleted but underlying private media remains accessible.

Controls:

```text
Database deletion
+
Storage deletion
+
Access verification
```

must be considered as one logical operation.

---

# 107. Attack Path: Failed Cleanup

Temporary files remain after a processing failure.

Controls:

- automatic cleanup
- cleanup jobs
- TTL
- monitoring
- retry-safe cleanup

Public processing should avoid this problem by not sending files to the server in the first place.

---

# 108. Attack Path: Error-Induced Data Leakage

A malformed file triggers an exception containing:

```text
original filename
file metadata
internal path
stack trace
```

and that information reaches an external service.

Controls:

- sanitized errors
- safe user messages
- redacted telemetry

---

# 109. Attack Path: Malicious HTTP Headers

Attackers can manipulate request headers.

The server must not blindly trust headers such as:

```text
X-User
X-Role
X-Forwarded-User
```

unless they originate from a trusted infrastructure boundary that has been explicitly configured and verified.

---

# 110. Attack Path: Host Header Abuse

Host-related input must not be blindly used to generate:

- password reset URLs
- canonical URLs
- redirects
- absolute links

Allowed hosts should be controlled where necessary.

---

# 111. Attack Path: Password Hash Exposure

Even though password hashes are not plaintext passwords, their exposure creates a serious offline cracking risk.

Controls:

- database access restrictions
- least privilege
- backups protection
- no API exposure
- no logging
- secure password hashing

---

# 112. Attack Path: Session Database Exposure

If session records are compromised:

## Risk

Attackers may attempt session takeover.

## Controls

- secure session design
- protected session storage
- minimal session data
- session revocation
- short/appropriate expiration

---

# 113. Attack Path: Audit Log Exposure

Audit logs may reveal:

- administrator activity
- internal IDs
- security events
- operational information

Controls:

- admin-only access
- least privilege
- no public API
- retention policy

---

# 114. Attack Path: Insider Access

An infrastructure operator may have access to:

- database
- backups
- object storage
- logs

Controls:

- least privilege
- separate credentials
- auditability
- encrypted sensitive storage where appropriate
- minimal retained data

---

# 115. Attack Path: Compromised Third-Party Service

If an external analytics or monitoring service is compromised:

## Risk

Data sent to it may become exposed.

## Control

Do not send unnecessary sensitive data.

The best mitigation is data minimization.

---

# 116. Attack Path: Third-Party Script Data Access

A third-party script executing in the public page context may access data available to that page.

Therefore:

- minimize third-party scripts
- never expose processing files to them
- avoid sensitive data in public page state
- review integrations

---

# 117. Attack Path: Malicious Dependency Update

A dependency update introduces malicious code.

Controls:

- review dependency changes
- lock versions
- inspect major dependency changes
- run tests
- scan dependencies
- review install scripts where relevant

---

# 118. Attack Path: CI/CD Secret Exposure

CI logs can accidentally reveal secrets.

Controls:

- secret masking
- restricted CI permissions
- no secret echoing
- protected production deployment credentials
- environment separation

---

# 119. Attack Path: Deployment Configuration Drift

Production configuration differs from security requirements.

Examples:

```text
HTTPS disabled
database exposed
secure cookies disabled
debug enabled
weak headers
```

Controls:

- deployment checklist
- staging verification
- automated configuration checks
- production security review

---

# 120. Attack Path: Security Control Disabled During Debugging

A developer temporarily disables:

```text
authentication
CSRF
CSP
rate limiting
authorization
```

and forgets to restore it.

Controls:

- environment-specific configuration
- CI checks
- security tests
- code review
- no permanent debug bypasses

---

# 121. Attack Path: Test Endpoint Left in Production

Examples:

```text
/api/test
/api/debug
/api/admin-test
```

Controls:

- production route inventory
- automated route checks
- deployment review
- no debug routes in production

---

# 122. Attack Path: Hidden Administrative Endpoint

A hidden endpoint is still an endpoint.

Security through obscurity is not sufficient.

Every administrative endpoint must use the same authentication and authorization boundary.

---

# 123. Attack Path: API Version Bypass

If the application introduces:

```text
/api/v1
/api/v2
```

old versions must not retain weaker security controls indefinitely.

Every active API version must follow the current security requirements.

---

# 124. Attack Path: Stale Security Dependency

A parser or authentication dependency becomes outdated.

Controls:

- dependency inventory
- vulnerability monitoring
- scheduled security updates
- regression testing

---

# 125. Attack Path: Missing Security Regression

A vulnerability is fixed but later code reintroduces it.

Required process:

```text
Vulnerability
   ↓
Fix
   ↓
Regression Test
   ↓
Future CI
   ↓
Prevent Reintroduction
```

---

# 126. Attack Path: Documentation Drift

Implementation no longer matches:

```text
security.md
auth.md
data.md
architecture.md
```

This creates false security assumptions.

Controls:

Any security-relevant architecture change must update the relevant documentation before approval.

---

# 127. Attack Surface Change Rule

Every new feature must answer:

```text
What new attack surface does this feature introduce?
```

Examples:

### New public API

Adds:

```text
HTTP entry point
Input validation
Rate limiting
Authorization considerations
```

### New file upload

Adds:

```text
Parser
Storage
File validation
Malicious-file risk
```

### New third-party integration

Adds:

```text
External trust boundary
Data transfer
Dependency risk
```

### New admin role

Adds:

```text
Authorization complexity
Privilege escalation risk
```

OWASP recommends revisiting threat analysis when changes materially alter the attack surface.

---

# 128. Security Risk Ranking

Each identified threat should be classified:

```text
CRITICAL
HIGH
MEDIUM
LOW
```

Priority should consider:

```text
Likelihood
×
Impact
×
Exposure
```

Exact numerical scoring is optional; the important requirement is consistent prioritization.

---

# 129. Critical Security Findings

Any of the following blocks approval:

- authentication bypass
- authorization bypass
- administrator takeover
- arbitrary server-side code execution
- major secret exposure
- public processing files unexpectedly uploaded
- unrestricted private media access
- severe stored XSS
- severe SQL injection
- severe path traversal
- critical dependency vulnerability affecting exposed functionality

---

# 130. High Security Findings

High-severity findings normally block production release.

Examples:

- significant IDOR
- persistent XSS with meaningful impact
- session compromise
- serious CSRF
- privilege escalation
- major information disclosure
- dangerous file-upload bypass

---

# 131. Medium Security Findings

Medium issues must be fixed before final approval unless explicitly documented and accepted.

Examples:

- weak rate limiting
- limited information disclosure
- incomplete security headers
- low-impact validation gaps

---

# 132. Low Security Findings

Low-risk findings may be scheduled according to priority but must remain documented.

They must not be silently forgotten.

---

# 133. Attack Simulation Requirements

Security testing should simulate attacker behavior rather than only testing happy paths.

Examples:

```text
Unauthenticated request
Malformed request
Modified request
Repeated request
Oversized request
Unauthorized object ID
Modified role
Stolen session
Malicious file
Malicious SVG
Malicious HTML
Malicious URL
Unexpected state transition
```

---

# 134. Public Tool Security Test

For every browser-based tool:

```text
Open tool
↓
Select file
↓
Start processing
↓
Inspect Network
↓
Inspect requests
↓
Verify file is not uploaded
↓
Verify third-party requests
↓
Verify result
```

This test is mandatory because browser-only processing is a core product promise.

---

# 135. Tool-Specific Attack Review

Every tool must document:

```text
Input
Output
Parser
Processing engine
Browser APIs
Worker usage
Memory risk
Maximum input
Potential malicious input
Error handling
Third-party interaction
Security tests
```

---

# 136. File-Processing Attack Matrix

Initial categories:

| Tool Type | Primary Risks |
|---|---|
| Image conversion | malformed image, memory exhaustion |
| Image compression | decompression bomb, CPU exhaustion |
| Image resize | extreme dimensions, memory exhaustion |
| Image crop | malformed coordinates, resource abuse |
| PDF merge | malformed PDFs, page explosion |
| PDF split | malformed PDFs, excessive page count |
| PDF compression | parser/resource exhaustion |
| PDF to image | huge pages, memory exhaustion |
| Image to PDF | excessive input count/size |
| Batch processing | multiplicative resource consumption |
| ZIP processing | ZIP bombs, path traversal |

Each concrete tool must later receive its own security test specification.

---

# 137. CMS Attack Matrix

| CMS Area | Primary Risks |
|---|---|
| Pages | XSS, IDOR, unauthorized editing |
| Page Builder | XSS, malicious blocks |
| Revisions | unauthorized restore |
| Media | malicious files, XSS, access bypass |
| Blog | stored XSS |
| Navigation | malicious redirects |
| SEO | poisoning, unsafe schema |
| Settings | privilege escalation |
| Users | account takeover |
| Audit Logs | tampering, disclosure |

---

# 138. Authentication Attack Matrix

| Attack | Required Defense |
|---|---|
| Brute force | Rate limiting + account protection |
| Credential stuffing | Rate limiting + monitoring |
| Enumeration | Generic responses |
| Session theft | Secure cookies + HTTPS |
| Session fixation | Session regeneration |
| Logout bypass | Server-side revocation |
| Privilege escalation | Server-side authorization |
| Reset abuse | Rate limiting + short-lived tokens |
| Reset theft | Secure token lifecycle |
| Password compromise | Strong password hashing |

---

# 139. Security Testing Order

Security tests should prioritize:

```text
1. Authentication
2. Authorization
3. Session management
4. File handling
5. XSS
6. Input validation
7. API security
8. Data access
9. Resource exhaustion
10. Deployment configuration
```

This prioritization follows the highest-risk trust boundaries first.

---

# 140. Attack Path Review Before Approval

Before approving a major feature:

```text
Feature
 ↓
Attack Surface
 ↓
Threats
 ↓
Mitigations
 ↓
Security Tests
 ↓
Regression Tests
 ↓
Approval
```

No major feature should skip this sequence.

---

# 141. Security Incident Trigger

An incident investigation must begin when evidence suggests:

- administrator compromise
- credential compromise
- session theft
- unauthorized CMS modification
- secret exposure
- unexpected public file upload
- database exposure
- malicious dependency
- persistent XSS exploitation
- unauthorized private-media access

The detailed response process belongs in `incident-response.md`.

---

# 142. Emergency Controls

The architecture should support emergency actions such as:

```text
Disable administrator account
Revoke all sessions
Rotate secrets
Disable affected tool
Disable affected API
Remove malicious media
Rollback published content
```

Emergency controls must themselves require appropriate authorization.

---

# 143. Security Kill Switches

A feature-specific emergency disable mechanism may be introduced only where justified.

Examples:

```text
Disable PDF processing
Disable SVG uploads
Disable a vulnerable tool
Disable a third-party integration
```

Such controls must fail safely.

---

# 144. No Silent Security Bypass

The application must never silently fall back from a secure mechanism to an insecure one.

Example:

Bad:

```text
Secure processing unavailable
↓
Automatically upload file to server
```

Correct:

```text
Browser processing unavailable
↓
Tell user the tool is unsupported in this browser
```

unless an explicitly approved alternative architecture exists.

---

# 145. Privacy Attack Path

The most important product-specific attack is:

```text
User thinks file stays local
        ↓
Application accidentally uploads file
        ↓
Third-party/server receives file
```

This is both a privacy and security failure.

Therefore every public tool must explicitly test:

```text
"No user processing file leaves the browser."
```

---

# 146. Security Invariant

The following invariant must remain true:

> A public browser-based processing tool must never send the user's processing file to the server merely to perform the advertised operation.

If this invariant cannot be maintained for a particular tool, that tool must not be presented as browser-only.

---

# 147. Threat Model Maintenance

`hack.md` must be updated when:

- a new API is introduced
- authentication changes
- authorization changes
- a new upload capability appears
- a new parser is added
- a new third-party service is added
- a new storage system is added
- a new administrator role is added
- the Page Builder changes
- the processing architecture changes
- deployment architecture changes

---

# 148. Required Security Review Questions

Before approval, answer:

```text
1. What can an anonymous attacker access?
2. What can an authenticated administrator access?
3. What can a compromised administrator access?
4. What new endpoints exist?
5. What new data enters the system?
6. What new data leaves the system?
7. What new parsers process untrusted input?
8. What new trust boundaries exist?
9. What happens if the client is malicious?
10. What happens if a session is stolen?
11. What happens if the database is compromised?
12. What happens if a dependency is compromised?
13. What happens if the feature receives malicious input?
14. What happens if the feature is called directly through HTTP?
15. What regression tests prevent the identified attacks?
```

---

# 149. Attack Surface Approval Checklist

- [ ] Entry points identified
- [ ] Exit points identified
- [ ] Trust boundaries identified
- [ ] Assets identified
- [ ] Attackers identified
- [ ] Threats identified
- [ ] High-risk paths identified
- [ ] Mitigations defined
- [ ] Security tests written
- [ ] Security tests passed
- [ ] Regression tests added
- [ ] Documentation updated
- [ ] No unresolved critical findings
- [ ] No unresolved high findings blocking release

---

# 150. Non-Negotiable Attack Rules

1. Assume every client request can be malicious.
2. Assume every uploaded file can be malicious.
3. Assume every client-side security value can be modified.
4. Assume every public endpoint will eventually be discovered.
5. Assume an attacker will call APIs directly.
6. Assume filenames are malicious input.
7. Assume MIME types can be spoofed.
8. Assume an administrator session can eventually be stolen.
9. Assume dependencies can contain vulnerabilities.
10. Assume logs can become an attack target.
11. Assume backups can become an attack target.
12. Assume CMS content can become an XSS vector.
13. Assume a new feature increases the attack surface.
14. Never rely on obscurity as the primary security control.
15. Never treat a passing UI test as proof of security.
16. Never approve a feature with an unresolved critical security path.

---

# 151. Final Security Model

The application must be designed under the following assumption:

```text
Everything outside the trusted server boundary is hostile.
```

Therefore:

```text
Browser
   ↓
UNTRUSTED

Internet
   ↓
UNTRUSTED

Uploaded Content
   ↓
UNTRUSTED

Client State
   ↓
UNTRUSTED

HTTP Headers
   ↓
UNTRUSTED

User Input
   ↓
UNTRUSTED
```

The server, database, and protected infrastructure form the controlled security boundary, but even internal data must be validated when crossing subsystem boundaries.

---

# 152. Final Attack-Model Principle

> Do not ask whether an attacker can use the UI to perform an action. Ask whether an attacker can construct an HTTP request, malicious file, malicious payload, stolen session, or compromised dependency that reaches the same underlying operation.

The security architecture must defend the actual operation, not merely the intended user interface.
