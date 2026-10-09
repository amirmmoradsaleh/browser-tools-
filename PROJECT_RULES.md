# PROJECT RULES

**Project:** Privacy-First File Tools Platform  
**Document:** `PROJECT_RULES.md`  
**Version:** 1.1  
**Status:** Mandatory Project Governance  
**Priority:** Highest project-level execution authority

---

## 1. Purpose

This document defines the mandatory rules governing product development, architecture, implementation, testing, security, documentation, and release.

These rules apply to every person, AI agent, developer, tool, and automated process working on the project.

No implementation may knowingly violate these rules.

---

# 2. Source-of-Truth Hierarchy

When documents conflict, authority is resolved in this order:

1. `PROJECT_RULES.md`
2. `PRD.md`
3. `roadmap.md`
4. Approved Architecture Documentation
5. Approved Feature / Technical Specifications
6. Implementation

Lower-level implementation must not silently override higher-level requirements.

If a conflict is discovered, stop the affected implementation, document the conflict, resolve it at the appropriate documentation level, then continue.

---

# 3. Roadmap Lock

`roadmap.md` is the official execution boundary of the project.

The project must follow the phases in the exact defined order.

No phase may be skipped.

No later phase may be implemented early merely because its code appears useful or convenient.

A phase may begin only after the previous phase has reached `APPROVED`.

### Roadmap changes

The roadmap must not be changed casually.

Any proposed roadmap change must document:

- Reason for change
- Problem being solved
- Affected phase
- Affected scope
- Dependencies
- Security impact
- Testing impact
- Documentation impact
- Regression impact
- Why the existing roadmap is insufficient

The change requires explicit project-level approval before implementation.

---

# 4. Scope Control

Every requested feature or change must be classified as:

- `REQUIRED`
- `OPTIONAL`
- `FUTURE`
- `REJECTED`

Only `REQUIRED` scope may be implemented during the current approved phase unless an explicit decision authorizes otherwise.

### No feature creep

Before adding functionality not explicitly required by the current scope, document:

1. The problem
2. Why it matters
3. Proposed solution
4. Product fit
5. Dependencies
6. Security implications
7. Testing requirements
8. Documentation impact
9. Whether it changes the roadmap

Do not implement speculative functionality.

---

# 5. Privacy Invariant

The central privacy rule is:

> Public user files must be processed locally in the user's browser whenever the approved tool architecture allows it.

Public processing files must not be uploaded merely because server-side processing is easier.

No public processing file may be accidentally sent through:

- Application APIs
- Server actions
- Database writes
- Object storage
- Analytics
- Error-reporting systems
- Third-party processing services
- Debugging telemetry
- Logging
- Unapproved external APIs

If a tool cannot meet the privacy requirement, implementation must stop until its architecture is explicitly reviewed and approved.

---

# 6. Public Users vs Administrators

The public product and administrator system are separate security boundaries.

### Public users

- Do not require an account for public file-processing tools.
- Process files locally whenever supported.
- Must not receive administrative capabilities.
- Must not access CMS resources.

### Administrators

- Authenticate through the approved authentication system.
- Must pass server-side authorization checks.
- May access only resources permitted by their role/permissions.
- Must not bypass authorization through client-side UI manipulation.

Hiding an interface element is never considered authorization.

---

# 7. Authentication Rules

Authentication must follow the approved authentication specification.

Mandatory rules include:

- Passwords must never be stored in plaintext.
- Passwords must use an approved password-hashing mechanism.
- Sessions must be securely generated.
- Sessions must be validated server-side.
- Logout must invalidate the session.
- Expired sessions must be rejected.
- Disabled accounts must be rejected.
- Secure cookies must be used where cookies carry authentication state.
- Session fixation must be prevented.
- Repeated failed login attempts must be protected.
- Brute-force and credential-stuffing defenses must exist.
- Rate limiting must be applied where required.
- Password reset flows must be secure.
- Authentication errors must not expose unnecessary sensitive information.

---

# 8. Authorization Rules

Authorization must always be enforced server-side.

Every protected server route, API operation, mutation, and administrative action must independently verify authorization.

Never rely on:

- Hidden buttons
- Client-side route guards alone
- Client-side role values
- Browser state
- UI visibility

IDOR and privilege-escalation paths must be explicitly tested.

---

# 9. Secrets and Configuration

Secrets must never be committed to the repository.

This includes:

- Passwords
- API keys
- Database credentials
- Session secrets
- Encryption keys
- Object-storage credentials
- Third-party service secrets
- Production credentials

Public environment variables must contain only values explicitly safe for client exposure.

Server-only secrets must never be imported into client bundles.

Example secrets must be clearly fake and non-functional.

---

# 10. Data Minimization

Collect and store only data required by the approved product scope.

Do not create storage for speculative future features.

Sensitive data must have:

- A documented purpose
- A documented location
- Access controls
- Retention rules
- Deletion rules

Public processing files are not application data and must not be persisted by the server.

---

# 11. Public Processing File Lifecycle

For public browser processing:

1. File enters browser memory.
2. File is validated locally.
3. Processing occurs locally.
4. Result is validated locally.
5. Download is initiated locally.
6. Browser-side temporary resources are released.

The application must not persist the user's processing file on the server.

Object URLs, buffers, worker resources, and temporary browser resources must be cleaned up according to the processing specification.

---

# 12. Media Library Boundary

The Media Library is an administrator-managed server-backed content system.

Public processing files and Media Library assets are different data classes.

A public processing workflow must never silently add a user's file to the Media Library.

Media Library uploads require explicit administrator action and must pass upload-security validation.

---

# 13. Tool Independence

Every public processing tool is an independently testable unit.

Each tool must have:

- Unique ID
- Name
- Category
- Supported input formats
- Supported output formats
- Options
- Validation rules
- Processing implementation
- Error behavior
- Limits
- Security requirements
- Performance requirements
- Unit tests
- Integration tests
- E2E tests
- Security tests
- Performance tests
- Documentation

Tool approval lifecycle:

```text
PLANNED
→ IMPLEMENTED
→ UNIT TESTED
→ INTEGRATION TESTED
→ E2E TESTED
→ SECURITY CHECKED
→ PERFORMANCE CHECKED
→ DOCUMENTED
→ APPROVED
```

A tool is not approved because it appears to work manually.

---

# 14. Testing Rules

Testing is part of implementation, not a final optional step.

Required testing levels must be used according to scope:

- Unit
- Integration
- E2E
- Security
- Performance
- Memory/resource
- Accessibility
- Browser compatibility
- Regression

A failed mandatory test blocks approval.

Never report a test as passed if it was not actually executed or its result cannot be verified.

---

# 15. Regression Rules

Changes must not break previously approved functionality.

After changes to shared systems, run the relevant regression suite.

High-risk shared areas include:

- Processing engine
- Tool registry
- Authentication
- Authorization
- UI primitives
- Theme system
- File validation
- Worker system
- Download system
- CMS block system
- SEO generation

---

# 16. Security Rules

Security must be considered throughout development.

Mandatory protections include appropriate defenses against:

- XSS
- CSRF
- SQL injection
- SSRF
- IDOR
- Path traversal
- Malicious uploads
- Malicious SVG
- Session hijacking
- Brute force
- Credential stuffing
- Privilege escalation
- Open redirects
- API abuse
- Denial of service
- Dependency vulnerabilities
- Secret exposure

Do not treat security as a feature added only during the security-hardening phase.

---

# 17. File Processing Security

File processing must treat user-controlled files as untrusted input.

Validation must not rely only on file extensions or MIME types.

Where applicable, use file signatures and structural validation.

Processing must enforce:

- Size limits
- Resource limits
- Memory limits
- Batch limits
- Processing time limits where practical
- Safe parsing
- Safe output generation

Malformed and adversarial files must be tested.

---

# 18. XSS and Content Security

All user-controlled content must be treated as untrusted.

This includes:

- Page-builder content
- Blog content
- Titles
- Descriptions
- URLs
- Metadata
- Filenames
- Media metadata
- SVG content
- Query parameters

Use safe rendering and sanitization strategies appropriate to the content type.

Never use unsafe HTML rendering without an explicit security review.

---

# 19. Logging Rules

Logs must never contain:

- Passwords
- Session tokens
- API keys
- Authentication secrets
- Complete user file contents
- Sensitive personal information
- Unnecessary file metadata that could create privacy risk

Errors should contain enough information for diagnosis without exposing sensitive information.

---

# 20. Error Handling

Errors are classified into:

- User/input errors
- Capability errors
- Processing errors
- Resource-limit errors
- Authentication errors
- Authorization errors
- System errors
- Security events

User-facing messages must be understandable and safe.

Internal diagnostics must not be exposed unnecessarily to users.

---

# 21. Page Builder Rules

The Page Builder is intentionally lightweight.

It is not an Elementor-level general-purpose visual editor.

The system must use structured blocks rather than arbitrary DOM manipulation.

Blocks must be:

- Typed
- Validated
- Versioned where required
- Serializable
- Migratable when their schema changes

Copy/paste must copy structured block data, not arbitrary DOM.

Revisions must remain immutable after publication/saving according to the approved revision model.

---

# 22. CMS Rules

All CMS mutations require:

1. Authentication
2. Server-side authorization
3. Input validation
4. Security validation
5. Auditability where required
6. Safe persistence
7. Appropriate tests

Client-side controls do not replace server-side enforcement.

---

# 23. SEO Rules

SEO functionality must be generated from structured, validated data.

JSON-LD must be valid.

SEO output must not introduce:

- XSS
- Invalid markup
- Unsafe URLs
- Duplicate/conflicting canonical directives
- Accidental indexing of restricted content

Sitemap and robots output must reflect the approved indexing model.

---

# 24. UI / UX Priority

The priority order is:

1. Clarity
2. Usability
3. Performance
4. Accessibility
5. Visual quality
6. Animation

Visual polish must never justify:

- Broken functionality
- Poor accessibility
- Slow interaction
- Excessive bundle size
- Confusing UX

---

# 25. Animation Rules

Animations should support understanding and perceived quality.

They must:

- Not block essential interaction
- Not cause unnecessary performance cost
- Respect reduced-motion preferences
- Remain usable on lower-end devices
- Not interfere with keyboard or screen-reader workflows

---

# 26. Responsive Rules

The product must work across:

- Desktop
- Tablet
- Mobile

Important workflows must be usable without relying on hover.

Touch targets, focus states, dialogs, file inputs, progress states, and downloads must be tested on mobile.

---

# 27. Theme Rules

The product must support:

- Light
- Dark
- System preference

Theme behavior must be consistent across public and admin interfaces where applicable.

Theme switching must not produce unusable contrast or broken components.

---

# 28. Accessibility Rules

Target:

**WCAG 2.2 AA**

Accessibility applies to:

- Navigation
- Forms
- File upload
- Processing controls
- Progress
- Errors
- Dialogs
- CMS
- Blog editor
- Page Builder
- Theme controls

Keyboard operation and reduced motion are mandatory considerations.

---

# 29. Browser Processing Architecture

Browser processing should use Web Workers when processing could block the main thread.

The main UI thread must remain responsive where practical.

Capability detection must occur before relying on unsupported browser features.

Unsupported browsers or capabilities must receive safe, understandable feedback.

Do not silently switch to server-side processing of public files.

---

# 30. Performance Rules

Avoid unnecessary:

- Large client bundles
- Duplicate libraries
- Global state managers
- Abstractions
- Network requests
- Re-renders
- Server processing for browser-capable tasks
- Premature microservices
- Queues without a documented need
- Separate services without a documented need

Use code splitting and lazy loading where appropriate.

Performance must be measured, not assumed.

---

# 31. Architecture Rules

The default architecture is a modular monolith unless approved documentation requires otherwise.

Do not introduce microservices merely for perceived scalability.

Do not create multiple state-management systems without a documented reason.

Do not duplicate domain logic between client and server unnecessarily.

Architecture changes require documentation updates before or together with implementation.

---

# 32. Dependencies

Every significant dependency must have a documented reason.

Before adding a dependency, evaluate:

- Necessity
- Maintenance
- Security
- Bundle impact
- License
- Browser compatibility
- Server compatibility
- Long-term fit

Do not add libraries for functionality already adequately provided by the platform or existing approved dependencies.

---

# 33. Git and CI Rules

Commits must not intentionally include:

- Secrets
- Credentials
- Production data
- Temporary debugging dumps
- Unnecessary generated artifacts

CI must validate the project according to the current phase.

A local success that CI rejects is not a completed implementation.

---

# 34. Documentation Rules

Every significant architecture, security, data, testing, feature, or operational decision must be documented.

Documentation must be synchronized with implementation.

Required project documentation includes, at minimum:

- `PRD.md`
- `PROJECT_RULES.md`
- `roadmap.md`
- `architecture.md`
- `security.md`
- `auth.md`
- `data.md`
- `hack.md`
- `testing.md`
- `incident-response.md`
- `checklist.md`

Additional phase/feature specifications must be added when required by the roadmap.

---

# 35. AI Development Rules

Any AI agent working on this repository must:

1. Read `PROJECT_RULES.md`.
2. Read the relevant PRD section.
3. Read `roadmap.md`.
4. Read the approved architecture/specifications relevant to the task.
5. Confirm the requested work belongs to the current phase.
6. Avoid duplicating existing functionality.
7. Implement only approved scope.
8. Run the required tests.
9. Report failed tests honestly.
10. Update documentation when architecture or behavior changes.
11. Never silently change project rules.
12. Never silently change the roadmap.
13. Never claim approval without meeting the approval criteria.

---

# 36. Implementation State Rules

The following states must not be conflated:

- `PLANNED`
- `IMPLEMENTED`
- `TESTED`
- `PASSED`
- `VERIFIED`
- `DOCUMENTED`
- `APPROVED`

Code existing in the repository does not mean it is tested.

A passing unit test does not mean the feature is approved.

Approval requires the complete applicable gate.

---

# 37. Work Reporting

Significant implementation work must report:

- Changed
- Added
- Removed
- Tests Run
- Tests Passed
- Tests Failed
- Known Issues
- Documentation Updated
- Approval Status

Never hide failed tests or known limitations.

---

# 38. Required Development Process

All significant work follows:

```text
Understand
→ Plan
→ Implement
→ Test
→ Review
→ Fix
→ Retest
→ Document
→ Verify
→ Approve
```

Skipping a mandatory stage requires explicit documentation and approval.

---

# 39. Final Project Rule

The project's goal is not merely to produce working code.

The goal is to produce a:

- Functional
- Privacy-first
- Secure
- Tested
- Accessible
- Performant
- Maintainable
- Documented
- Production-ready

system.

Quality, privacy, security, testing, and documentation are mandatory parts of the product.
