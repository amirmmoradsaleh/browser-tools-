# Security Policy & Security Requirements

**Document:** `security.md`  
**Version:** 1.0  
**Status:** Mandatory  
**Scope:** Entire application  
**Priority:** Critical

---

## 1. Purpose

This document defines the mandatory security requirements for the entire application.

Security is not a final QA step.

Security requirements must be considered during:

- architecture
- implementation
- testing
- deployment
- maintenance
- dependency updates
- feature development
- CMS development
- file-processing development

No feature is considered complete if its security requirements have not been reviewed and tested.

---

# 2. Security Principles

The application must follow these principles:

1. **Least Privilege**
2. **Defense in Depth**
3. **Secure by Default**
4. **Data Minimization**
5. **Zero Trust Between Client and Server**
6. **Server-Side Authorization**
7. **No Secrets in Client Code**
8. **No Sensitive Data in Logs**
9. **Input Validation**
10. **Output Encoding**
11. **Dependency Security**
12. **Fail Securely**
13. **Privacy by Design**
14. **Security Testing Before Approval**

---

# 3. Privacy Architecture

The most important product security/privacy requirement is:

> Public file-processing tools must process user files locally in the user's browser whenever technically feasible.

A public processing file must not be uploaded to the application server merely because the application needs to process it.

The architecture must prevent accidental uploads through:

- API requests
- forms
- analytics
- error reporting
- debugging systems
- third-party services
- logging
- telemetry
- background requests

The browser processing architecture must remain separate from server-side CMS functionality.

---

# 4. Public User vs Administrator Security Boundary

The application has two fundamentally different trust levels.

## Public User

Public users:

- do not need an account to use public tools
- can process files locally
- can download their processed files
- must not access CMS functionality
- must not access administrative APIs
- must not access private media
- must not access database operations

## Administrator

Administrators:

- require authentication
- require authorization
- can access CMS functionality according to their role
- can modify site content
- can manage media
- can manage blog content
- can manage SEO configuration
- can access protected APIs

The client interface must never be treated as the security boundary.

Hiding an admin button is not authorization.

Every protected server operation must independently verify authorization.

---

# 5. Authentication Security

Authentication requirements are defined in greater detail in `auth.md`.

Mandatory requirements:

- Passwords must never be stored in plaintext.
- Passwords must be securely hashed.
- Authentication credentials must be validated.
- Sessions must be securely managed.
- Authentication cookies must use secure cookie settings.
- Logout must invalidate the relevant session.
- Repeated failed login attempts must be rate-limited.
- Brute-force protection must exist.
- Credential stuffing protection must exist.
- Password reset flows must be protected.
- Authentication errors must not unnecessarily reveal whether an account exists.
- Authentication secrets must never be exposed to the browser.

Password hashing and server-side authentication are consistent with the security approach documented by Next.js.

---

# 6. Authorization

Authentication answers:

> Who is this user?

Authorization answers:

> What is this user allowed to do?

These must never be treated as the same operation.

Authorization must be checked server-side for every protected operation.

Examples:

- editing a page
- publishing a page
- deleting media
- modifying navigation
- editing SEO settings
- creating blog posts
- deleting blog posts
- modifying system settings
- managing users
- viewing audit logs

A user must not gain permission merely by:

- changing a request parameter
- modifying browser state
- modifying JavaScript
- calling an API directly
- changing an object ID
- bypassing the UI

---

# 7. Session Security

Sessions must:

- use secure random identifiers
- be invalidated on logout
- expire according to defined policies
- use secure cookie attributes
- prevent session fixation
- prevent session hijacking where technically feasible
- avoid storing sensitive session information in client-accessible storage when unnecessary

Session information must not be exposed in:

- URLs
- logs
- analytics
- client error messages

---

# 8. Secrets Management

Secrets must never be committed to source control.

Examples:

- database credentials
- authentication secrets
- API keys
- encryption keys
- object-storage credentials
- deployment credentials
- third-party service credentials
- private signing keys

Secrets must be supplied through secure environment configuration or an approved secret-management system.

Never:

```text
NEXT_PUBLIC_SECRET_KEY
```

Never place private secrets inside client-side JavaScript.

Anything exposed through a `NEXT_PUBLIC_*` environment variable must be treated as public.

---

# 9. Environment Separation

The project must have clearly separated environments where applicable:

- Development
- Testing
- Staging
- Production

Production credentials must never be reused casually in development.

Development databases must not contain unnecessary production data.

Production secrets must not be committed to:

- Git
- GitHub
- documentation
- screenshots
- example configuration
- test fixtures

---

# 10. Input Validation

All externally controlled input must be considered untrusted.

This includes:

- form fields
- query parameters
- route parameters
- request bodies
- headers
- cookies
- uploaded files
- filenames
- MIME types
- metadata
- CMS block data
- HTML content
- URLs
- JSON
- API parameters

Validation must occur on the server for server-controlled operations.

Client-side validation improves UX but does not replace server-side validation.

OWASP specifically recommends validating all input sources and using positive validation where possible.

---

# 11. Output Encoding and XSS Protection

The application must protect against:

- reflected XSS
- stored XSS
- DOM-based XSS

User-controlled content must never be inserted into HTML or executable contexts without appropriate sanitization/encoding.

Special attention is required for:

- blog content
- page-builder blocks
- CMS fields
- HTML blocks
- SVG
- URLs
- metadata
- filenames
- imported content

Arbitrary JavaScript must not be permitted through the page builder by default.

The page builder must use structured blocks instead of arbitrary DOM injection.

---

# 12. HTML / Rich Text Security

If rich text is supported:

- HTML must be sanitized.
- Dangerous tags must be removed.
- Dangerous attributes must be removed.
- JavaScript URLs must be rejected.
- Event-handler attributes must be rejected.
- Dangerous embedded content must be rejected.
- Sanitization must happen server-side before publishing.
- Published content must be rendered through a controlled rendering path.

The CMS must not become an arbitrary code execution interface.

---

# 13. SVG Security

SVG files must be treated as potentially active content.

The application must not blindly trust uploaded SVG files.

Security controls must address:

- embedded scripts
- event handlers
- external references
- dangerous URLs
- embedded HTML
- malicious SVG payloads

If SVG is allowed in the Media Library, it must pass an explicit sanitization policy.

---

# 14. File Security

File handling is a major security boundary because the application deals directly with user-provided files.

The application must validate:

- file size
- file type
- MIME type
- file signature where applicable
- extension
- processing capability
- malformed input

The application must never trust the filename extension alone.

OWASP identifies malicious files, parser vulnerabilities, oversized files, file overwrites and active client-side content as important upload threats.

---

# 15. Public File Processing

Public file processing must remain browser-first.

Example:

```text
User File
   ↓
Browser
   ↓
Validation
   ↓
Processing Engine
   ↓
Web Worker
   ↓
Processed File
   ↓
Browser Download
```

The server must not receive the user's processing file unless a future tool has been explicitly approved as requiring server-side processing.

If a tool requires server-side processing, it must receive separate security approval and documentation.

---

# 16. CMS Media Upload Security

CMS media uploads are different from public file processing.

Administrator-uploaded media may be stored server-side.

Such files must:

- be validated
- have controlled filenames
- be stored outside the web root where appropriate
- use controlled access mechanisms
- not overwrite arbitrary files
- not allow path traversal
- not execute as server-side code
- be protected against malicious content

OWASP recommends storing uploaded files outside the web root where possible and applying additional controls to publicly retrievable files.

---

# 17. Filename Security

User-controlled filenames must never be used directly as filesystem paths.

The application must protect against:

- `../`
- absolute paths
- encoded traversal
- null-byte attacks
- path separator manipulation
- reserved filenames
- filesystem-specific attacks

Stored filenames should use application-generated identifiers where appropriate.

Original filenames may be retained only as metadata.

---

# 18. Path Traversal

Any user-controlled path must be rejected unless it is explicitly required and securely constrained.

Never construct filesystem paths directly from untrusted input.

Example of prohibited logic:

```text
/storage/uploads/${userInput}
```

without strict validation and safe path resolution.

---

# 19. SQL Injection

All database queries must use Prisma's safe query mechanisms or parameterized queries.

Never concatenate untrusted input into SQL.

Unsafe:

```text
SELECT * FROM users WHERE email = '${email}'
```

Safe database access must rely on parameterization or Prisma APIs designed for safe querying.

---

# 20. IDOR / Object-Level Authorization

The application must protect against insecure direct object references.

Example:

```text
/admin/pages/123
```

must not imply that anyone who knows `123` can access or modify page 123.

Every object access must verify:

1. authenticated identity
2. required role/permission
3. ownership or administrative scope where applicable
4. requested operation

---

# 21. CSRF Protection

State-changing authenticated operations must be protected against CSRF where applicable.

Examples:

- changing password
- changing account settings
- creating content
- editing pages
- publishing content
- deleting media
- modifying site configuration

Security controls must be appropriate to the authentication/session architecture.

---

# 22. SSRF Protection

Any feature that retrieves remote URLs must be treated as potentially dangerous.

Examples:

- remote image import
- URL-based media import
- external content fetching
- metadata retrieval
- webhook integrations

If remote fetching is introduced, it must explicitly protect against:

- localhost access
- private IP ranges
- internal services
- cloud metadata endpoints
- DNS rebinding
- redirects to restricted destinations
- arbitrary internal port scanning

Remote fetching is not permitted merely because a URL is syntactically valid.

---

# 23. Open Redirect Protection

User-controlled redirect URLs must not allow arbitrary external redirects.

Redirect destinations should use:

- approved internal paths
- allowlists
- validated absolute URLs where explicitly required

Never trust a `redirect`, `next`, or similar parameter without validation.

---

# 24. Rate Limiting

Rate limiting must exist for security-sensitive server endpoints.

At minimum:

- login
- password reset
- authentication attempts
- administrative APIs
- expensive server operations
- abuse-prone endpoints

Public browser-only processing does not remove the need to protect server APIs.

Rate limits must be documented and tested.

---

# 25. Denial-of-Service Protection

The application must protect against abusive resource consumption.

Relevant resources include:

- CPU
- memory
- database connections
- storage
- network
- request volume
- file-processing resources

Browser-side processing reduces server-side file-processing exposure, but administrative and server APIs remain protected resources.

---

# 26. Browser Processing Resource Protection

Browser processing can still cause client-side resource exhaustion.

Tools must define reasonable limits for:

- maximum file size
- maximum number of files
- maximum image dimensions
- maximum PDF page count
- maximum batch size
- memory-intensive operations

The UI must warn users before operations that may consume significant resources.

Processing should use Web Workers when appropriate so heavy processing does not unnecessarily block the main UI thread.

---

# 27. ZIP / Archive Security

If archive processing is introduced, it must explicitly address:

- ZIP bombs
- excessive decompression ratios
- nested archives
- path traversal
- huge file counts
- excessive memory consumption

Archive extraction must never allow files to escape the intended destination.

---

# 28. PDF Security

PDF files must be treated as untrusted input.

PDF processing must account for:

- malformed PDFs
- parser vulnerabilities
- excessive page counts
- excessive memory usage
- malicious embedded content
- JavaScript inside PDFs where relevant
- malformed object structures

Only the capabilities required by the specific PDF tool should be supported.

---

# 29. Image Security

Image processing must account for:

- malformed image files
- decompression bombs
- extremely large dimensions
- unexpected formats
- malformed metadata
- malicious payloads
- memory exhaustion

The image-processing implementation must enforce resource limits.

---

# 30. Dependency Security

Dependencies must be:

- intentionally selected
- actively maintained where possible
- reviewed for security implications
- kept reasonably up to date
- scanned for known vulnerabilities

A dependency must not be added simply because it provides a convenient small feature if the feature can be implemented safely without it.

The project must periodically review dependency security.

Next.js explicitly recommends production applications use a current Active or Maintenance LTS release, and its 2026 release process includes regular security releases.

---

# 31. Third-Party Services

Third-party services must not receive user processing files unless explicitly approved.

Examples:

- analytics
- crash reporting
- advertising
- fonts
- CDN services
- external APIs
- monitoring

A third-party integration must be reviewed for:

- data collection
- privacy implications
- security
- cookies
- network requests
- file exposure
- compliance implications

---

# 32. Logging Security

Logs must never contain:

- passwords
- authentication tokens
- session identifiers
- API secrets
- private keys
- complete user files
- sensitive file contents
- unnecessary personal information

Errors should contain enough technical context to debug the problem without exposing sensitive information.

---

# 33. Error Handling

Production errors must not reveal:

- stack traces to normal users
- database structure
- internal paths
- secrets
- environment variables
- SQL statements
- authentication internals
- server configuration

Users should receive safe, understandable error messages.

Detailed diagnostics may be recorded internally according to the logging policy.

---

# 34. Content Security Policy

The application should use a strong Content Security Policy appropriate to its architecture.

The policy must be reviewed whenever the application adds:

- external scripts
- analytics
- advertising
- embedded content
- third-party services
- dynamic content

CSP must not be weakened merely to make an unsafe integration work.

---

# 35. Security Headers

Production responses should use appropriate security headers, including where applicable:

- Content-Security-Policy
- Strict-Transport-Security
- X-Content-Type-Options
- Referrer-Policy
- Permissions-Policy
- appropriate frame/embedding protections

Exact configuration must be tested against the actual deployment environment.

---

# 36. Transport Security

Production traffic must use HTTPS.

Sensitive authentication and administrative operations must never rely on plaintext HTTP.

Secure cookies must be used in production.

---

# 37. Database Security

The database must:

- use authenticated connections
- restrict network access
- use least-privilege credentials
- avoid public exposure where possible
- use migrations
- maintain backups
- avoid storing unnecessary data

The application database user should not automatically receive unnecessary administrative database privileges.

---

# 38. Data Minimization

The application should collect and store only data that is necessary.

Public file-processing tools should not create server-side records for every processed file.

The system must not store:

- uploaded processing files
- unnecessary file contents
- unnecessary personal information
- unnecessary activity history

---

# 39. Admin Security

Administrative functionality must be treated as high-value functionality.

Admin operations must have:

- authentication
- authorization
- CSRF protection where applicable
- rate limiting where applicable
- audit logging
- secure session handling
- server-side validation

High-risk operations should receive additional confirmation where appropriate.

Examples:

- deleting media
- deleting pages
- publishing major content
- modifying security settings
- modifying administrator accounts

---

# 40. Audit Logging

Security-relevant administrator actions should be auditable.

Examples:

- login
- logout
- failed login
- password change
- page creation
- page modification
- page publication
- page deletion
- media upload
- media deletion
- blog publication
- configuration changes
- role changes

Audit logs must not contain passwords, tokens or sensitive file contents.

---

# 41. Page Builder Security

The page builder must not become an arbitrary code execution system.

Blocks must be structured data.

A block should contain controlled properties such as:

```text
id
type
version
content
settings
styles
responsive
```

The following must not be accepted by default:

```text
arbitrary JavaScript
arbitrary server-side code
arbitrary event handlers
arbitrary executable HTML
```

Block types must be registered through the block registry.

Unknown block types must fail safely.

---

# 42. Blog Security

Blog content must be treated as CMS-controlled content.

The system must protect against:

- stored XSS
- malicious links
- unsafe HTML
- unsafe embeds
- malicious SVG
- unsafe media
- unauthorized publication

Draft content must not accidentally become publicly accessible.

---

# 43. SEO Security

SEO fields are user-controlled CMS content and must be validated.

Examples:

- title
- description
- canonical URL
- Open Graph values
- schema JSON
- robots directives

JSON-LD must be validated as structured data.

SEO fields must not provide an arbitrary JavaScript execution mechanism.

---

# 44. JSON Security

All structured JSON received from clients must be validated against expected schemas.

Do not assume JSON is safe merely because it is syntactically valid.

This applies to:

- page-builder data
- block configuration
- API payloads
- settings
- tool configuration
- SEO configuration

---

# 45. Prototype Pollution / Unsafe Object Handling

The application must avoid unsafe merging of untrusted objects.

Security-sensitive properties such as:

```text
role
permissions
userId
isAdmin
status
```

must never be accepted blindly from client-controlled objects.

Sensitive fields must be explicitly controlled by server logic.

---

# 46. Race Conditions

Security-sensitive operations must account for race conditions.

Examples:

- concurrent session invalidation
- permission changes
- publishing
- deleting content
- changing passwords
- changing administrator roles

Security decisions must not rely on stale client state.

---

# 47. Security Testing

Every security-sensitive feature must have explicit tests.

Testing should include, where applicable:

- authentication tests
- authorization tests
- input validation tests
- XSS tests
- CSRF tests
- SQL injection tests
- IDOR tests
- path traversal tests
- file validation tests
- SVG security tests
- rate-limit tests
- session tests
- permission escalation tests
- API abuse tests

Security testing is part of the feature's Definition of Done.

OWASP ASVS is used as the security verification baseline for the project.

---

# 48. Security Review Requirement

Before a feature can become `APPROVED`, the developer must answer:

1. What user-controlled input exists?
2. What server-side operations exist?
3. What sensitive data is involved?
4. What permissions are required?
5. Can an unauthorized user access the operation?
6. Can the feature upload or expose files?
7. Can the feature execute or render user-controlled content?
8. Can the feature consume excessive resources?
9. Can the feature expose secrets?
10. What security tests were performed?

If these questions cannot be answered, the feature is not approved.

---

# 49. Vulnerability Severity

Security findings should be classified at minimum as:

### Critical

Can result in:

- remote code execution
- authentication bypass
- administrator takeover
- major data exposure
- severe system compromise

**Status:** Immediate blocking issue.

### High

Can result in:

- privilege escalation
- unauthorized data access
- significant account compromise
- serious persistent XSS
- major security boundary bypass

**Status:** Blocks release.

### Medium

Security weakness with meaningful but limited impact.

**Status:** Must be fixed before final approval unless explicitly documented and accepted.

### Low

Limited-impact security issue.

**Status:** Track and fix according to priority.

---

# 50. Security Incident Response

If a security vulnerability is discovered:

1. Stop affected deployment if necessary.
2. Identify affected component.
3. Determine exploitability.
4. Determine affected users/data.
5. Contain the issue.
6. Fix the vulnerability.
7. Add a regression test.
8. Review related components.
9. Deploy the fix.
10. Document the incident.
11. Review whether the architecture needs modification.

Security incidents must not be hidden simply because the vulnerability is inconvenient to fix.

---

# 51. Security Documentation Requirements

Every security-sensitive subsystem must have documentation.

Required documents include:

```text
security.md
auth.md
data.md
hack.md
checklist.md
architecture.md
testing.md
incident-response.md
PROJECT_RULES.md
PRD.md
roadmap.md
```

Feature-specific security requirements must be documented in the relevant feature documentation.

---

# 52. Security Checklist Before Approval

Before marking any feature `APPROVED`:

- [ ] Inputs identified
- [ ] Inputs validated
- [ ] Outputs safely rendered
- [ ] Authentication requirements reviewed
- [ ] Authorization requirements reviewed
- [ ] Sensitive data identified
- [ ] Secrets reviewed
- [ ] Logging reviewed
- [ ] Error handling reviewed
- [ ] File handling reviewed
- [ ] Resource limits reviewed
- [ ] Rate limiting reviewed
- [ ] XSS risks tested
- [ ] CSRF risks tested where applicable
- [ ] IDOR risks tested where applicable
- [ ] Path traversal tested where applicable
- [ ] Dependency risks reviewed
- [ ] Security tests passed
- [ ] Regression tests added
- [ ] Documentation updated

---

# 53. Non-Negotiable Security Rules

The following rules cannot be overridden for convenience:

1. Never store plaintext passwords.
2. Never expose private secrets to the client.
3. Never trust client-side authorization.
4. Never bypass server-side authorization.
5. Never upload public processing files to the server unnecessarily.
6. Never log passwords or tokens.
7. Never trust filenames.
8. Never trust MIME types alone.
9. Never render unsanitized user-controlled HTML.
10. Never execute user-controlled JavaScript.
11. Never allow arbitrary filesystem paths.
12. Never concatenate untrusted input into SQL.
13. Never disable security controls just to make a feature work.
14. Never approve a feature with failing security tests.
15. Never silently weaken an existing security boundary.
16. Never introduce a new security-sensitive dependency without review.
17. Never modify the security architecture without updating the relevant documentation.

---

# 54. Relationship With PROJECT_RULES.md

`PROJECT_RULES.md` has higher execution priority than this document.

However, no implementation may intentionally violate this document unless the change has been explicitly documented and approved at the project level.

If a conflict exists:

```text
PROJECT_RULES.md
        ↓
PRD.md
        ↓
Approved Architecture
        ↓
security.md
        ↓
Feature Documentation
        ↓
Implementation
```

---

# 55. Security Definition of Done

A security-sensitive feature is complete only when:

```text
IMPLEMENTED
    ↓
SECURITY REVIEWED
    ↓
SECURITY TESTED
    ↓
VULNERABILITIES FIXED
    ↓
REGRESSION TESTED
    ↓
DOCUMENTED
    ↓
APPROVED
```

`IMPLEMENTED` does not mean `SECURE`.

`TESTED` does not automatically mean `APPROVED`.

A feature with unresolved blocking security findings cannot enter `APPROVED` status.

---

# 56. Mandatory Development Rule

Before modifying any security-sensitive code, the developer must read:

```text
PROJECT_RULES.md
security.md
auth.md
data.md
hack.md
checklist.md
architecture.md
testing.md
```

If one of these documents does not yet exist, the developer must follow the currently available security documentation and create/update the missing document before the relevant subsystem is approved.

---

# 57. Final Security Principle

The application is a privacy-first file-processing platform.

Therefore:

> The system must minimize trust in the server, minimize stored user data, minimize exposed attack surface, and make security properties enforceable through architecture and tests rather than relying on developer intention.
