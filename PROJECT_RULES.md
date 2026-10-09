# PROJECT RULES

**Project:** Privacy-First File Tools Platform  
**Status:** Mandatory  
**Priority:** Highest  
**Applies To:** All development, testing, documentation, security, deployment, and maintenance activities

---

# 1. Purpose

This document defines the mandatory rules for developing and maintaining the project.

These rules apply to:

- Architecture
- Frontend
- Backend
- Database
- File processing
- CMS
- Authentication
- Security
- SEO
- Testing
- Documentation
- Deployment

The development agent must treat this document as a permanent project constraint.

---

# 2. Source of Truth

The following hierarchy defines project authority:

```text
1. PROJECT_RULES.md
2. PRD.md
3. Approved Architecture Documentation
4. Approved Feature Documentation
5. Implementation
```

If implementation conflicts with the PRD or project rules, the implementation is considered incorrect.

If two documents conflict, the higher-level document takes precedence until the conflict is explicitly resolved.

---

# 3. Roadmap Lock

The approved roadmap is immutable during normal development.

Development must proceed sequentially through the approved phases.

The development agent must not:

- Skip a required phase
- Start future-phase features early
- Add unrelated features
- Expand scope without approval
- Replace planned architecture without documenting the reason

A technically interesting feature is not automatically an approved feature.

---

# 4. Scope Control

Every proposed feature must be classified as one of:

```text
REQUIRED
OPTIONAL
FUTURE
REJECTED
```

Only `REQUIRED` items belonging to the current phase may be implemented without additional scope approval.

`OPTIONAL` and `FUTURE` items must not be silently implemented.

`REJECTED` items must not be implemented.

---

# 5. No Feature Creep

The project must not accumulate functionality simply because it appears useful.

Before implementing a feature, its purpose must be documented:

```text
Problem
Why the problem matters
Why the feature solves it
Why the feature belongs in this product
Dependencies
Security implications
Testing requirements
```

If a feature does not provide a sufficiently clear product benefit, it must not be added.

---

# 6. Privacy Invariant

The most important technical invariant of the product is:

> Public user files must be processed locally in the user's browser whenever the tool is designed to support browser-side processing.

A public conversion file must not be uploaded to the application backend merely for processing.

This rule applies to:

- Images
- PDFs
- Documents
- Audio
- Video
- Other supported file types

where browser-side processing is technically feasible.

---

# 7. No Accidental File Upload

The implementation must be reviewed for accidental file transmission through:

- API calls
- Fetch requests
- Form submissions
- Analytics
- Error reporting
- Logging
- Third-party libraries
- Browser telemetry
- Debugging tools

A file-processing tool must not send file contents to a remote service unless the PRD explicitly approves that architecture for that specific tool.

---

# 8. Public User vs Administrator

The architecture must maintain a strict separation between:

```text
Public User
        ↓
Public Tools

Administrator
        ↓
Dashboard / CMS
```

Public users must not require an account for normal tool usage.

Administrative functionality requires authentication and authorization.

---

# 9. Authentication Rules

Authentication must follow `auth.md`.

At minimum:

- Passwords must never be stored in plaintext.
- Sessions must be securely managed.
- Logout must invalidate the session.
- Failed login attempts must be rate-limited.
- Repeated failed authentication must trigger account protection.
- Password reset must be protected.
- Authentication cookies must use appropriate security attributes.
- Authentication state must be validated server-side.

The client interface must never be treated as the security boundary.

---

# 10. Authorization Rules

Every protected server-side operation must verify authorization.

The following pattern is prohibited:

```text
Hide admin button
→ Assume user is authorized
```

The correct pattern is:

```text
Request
→ Authentication check
→ Authorization check
→ Resource access check
→ Operation
```

Unauthorized requests must be rejected server-side.

---

# 11. Secrets Management

The following must never be committed to Git:

- API keys
- Database credentials
- Passwords
- Session secrets
- Encryption keys
- Storage credentials
- SMTP credentials
- OAuth secrets
- Private certificates

Secrets must be provided through an appropriate environment or secret-management mechanism.

`.env` files containing secrets must not be committed.

A public repository must never contain production credentials.

---

# 12. Database Rules

PostgreSQL is the project's database.

Prisma is the project's ORM.

Database access must occur through the approved server-side data layer.

The application must protect against:

- SQL injection
- Unauthorized access
- IDOR
- Privilege escalation
- Unsafe queries
- Accidental destructive operations

Database migrations must be version controlled.

---

# 13. Data Minimization

The system must store only data that has a documented purpose.

Before introducing a new database field, document:

```text
Purpose
Why it is required
Who can access it
How long it is retained
When it is deleted
```

Unnecessary personal data must not be collected.

---

# 14. User Processing Data

Public processing files must not be persisted by the application.

Temporary browser data must have a defined lifecycle.

After processing is completed or cancelled, temporary resources should be released where technically possible.

Examples:

- Blob URLs
- ArrayBuffers
- Worker memory
- IndexedDB temporary data
- Browser caches created specifically for processing

---

# 15. Media Library Separation

The Media Library is an administrator-managed website asset system.

It must remain separate from public file processing.

```text
Public Processing File
→ Browser
→ Process
→ Download
→ Destroy temporary state

Admin Media
→ Upload
→ Object Storage
→ CMS
```

A public processing file must never automatically become a Media Library asset.

---

# 16. Tool Independence

Every public tool must be independently identifiable.

A tool must have:

- Unique ID
- Input specification
- Output specification
- Processing implementation
- Validation
- Error handling
- Test suite
- Documentation
- Approval status

Tools must not depend on undocumented behavior from other tools.

Shared infrastructure is allowed.

Hidden coupling is not.

---

# 17. Tool Completion Rule

A tool is not complete when the UI appears to work.

A tool is complete only after:

```text
Implementation
↓
Unit Tests
↓
Integration Tests
↓
E2E Tests
↓
Security Review
↓
Performance Review
↓
Documentation
↓
Regression Test
↓
APPROVED
```

If any required stage fails:

```text
Status = NOT APPROVED
```

---

# 18. Regression Rule

Whenever shared infrastructure changes, all affected existing tools must be tested.

Examples:

Changing:

- File engine
- Worker system
- Download system
- Validation system
- Shared UI
- Theme system
- Browser compatibility layer

must trigger appropriate regression tests.

An existing approved tool must not silently become broken because a new tool was added.

---

# 19. Testing Before Approval

No feature may receive final approval without passing its defined tests.

Tests must verify both:

```text
Expected behavior
```

and:

```text
Failure behavior
```

For example, a converter must be tested with:

- Valid file
- Invalid file
- Corrupted file
- Unsupported file
- Empty input
- Large input where applicable
- Cancelled processing
- Multiple files where applicable
- Download result

---

# 20. Security Testing

Security must not be postponed until the final phase.

Security checks must occur throughout development.

Relevant areas include:

```text
Authentication
Authorization
File handling
CMS
Media Library
Admin API
Database
User input
HTML rendering
SVG handling
URL handling
Sessions
Secrets
Dependencies
```

---

# 21. XSS Prevention

User-controlled content must never be trusted.

This includes:

- Blog content
- Page Builder content
- Block fields
- URLs
- Titles
- Descriptions
- Media metadata
- Custom HTML where supported

Any HTML rendering capability must have an explicitly documented security model.

---

# 22. File Upload Security

Administrator file uploads must be validated.

Validation should consider:

- File extension
- MIME type
- Actual file signature where applicable
- File size
- Filename
- Storage path
- Dangerous formats
- SVG content
- Executable content

Uploaded files must not be able to escape the intended storage boundary.

---

# 23. SVG Security

SVG files must receive special security treatment.

The implementation must consider:

- Embedded scripts
- External references
- Event handlers
- Malicious XML
- Unexpected content

SVG handling must not introduce XSS or related attacks.

---

# 24. Logging Rules

Logs must never contain:

- Passwords
- Session secrets
- API keys
- Authentication tokens
- Full private file contents
- Sensitive personal information

Logs should contain only information necessary for:

- Debugging
- Security
- Auditing
- System monitoring

---

# 25. Error Handling

Errors must be classified.

```text
Expected User Error
System Error
Security Error
Unknown Error
```

User-facing messages must be safe and understandable.

Internal implementation details must not be exposed to users.

Examples of information that must not leak:

- Database structure
- File system paths
- Stack traces
- Secrets
- Internal service credentials
- Private server configuration

---

# 26. Page Builder Rules

The Page Builder must remain intentionally smaller than a general-purpose visual website builder.

Do not introduce unnecessary:

- Layout engines
- Design editors
- Animation editors
- Complex scripting systems
- Arbitrary code execution

unless explicitly added to the roadmap.

Blocks must remain structured and predictable.

---

# 27. Block Architecture

Blocks should have a consistent structure.

Conceptually:

```text
Block
├── id
├── type
├── content
├── settings
├── styles
└── responsive configuration
```

Block data must be versionable.

The system must be able to migrate older block structures if the schema changes.

---

# 28. Copy/Paste Rules

Copy/paste must use a structured representation rather than relying on raw DOM manipulation.

A copied block should preserve:

- Content
- Configuration
- Styling
- Relevant settings

while generating a new unique block identity when pasted where required.

---

# 29. Page Revision Rules

Publishing a page must preserve its previous state.

The system should maintain enough information to restore an earlier revision.

Destructive operations must require appropriate authorization.

---

# 30. SEO Rules

SEO configuration must not be hard-coded into individual page implementations.

SEO should be managed through the CMS data model.

The system must support:

- Title
- Description
- Canonical
- Robots
- Open Graph
- Structured data
- Sitemap behavior

SEO features must generate valid output.

---

# 31. Schema Rules

Structured data must be generated as valid JSON-LD.

The system must avoid:

- Invalid JSON
- Unsupported schema structures
- Automatically generated misleading data
- Duplicate conflicting schemas

Schema types must correspond to actual page content.

---

# 32. UI Rules

The UI must prioritize:

1. Clarity
2. Usability
3. Performance
4. Accessibility
5. Visual quality

in that order.

Visual effects must not compromise the first four.

---

# 33. Animation Rules

Animations must be purposeful.

Animations must not:

- Delay essential actions
- Prevent interaction
- Cause layout instability
- Create excessive CPU usage
- Break keyboard navigation
- Ignore reduced-motion preferences

---

# 34. Responsive Rules

Every public feature must be evaluated at:

```text
Mobile
Tablet
Desktop
```

A desktop-only implementation is not considered complete unless the feature is explicitly defined as desktop-only.

---

# 35. Dark Mode Rules

Dark Mode and Light Mode are functional product requirements.

Both modes must be tested for:

- Contrast
- Readability
- Buttons
- Forms
- Upload states
- Error states
- Empty states
- Dashboard
- Page Builder
- Blog editor
- Modals
- Tables

---

# 36. Dependency Rules

New dependencies must have a documented reason.

Before adding a dependency, evaluate:

```text
Purpose
Bundle impact
Security history
Maintenance status
License
Browser compatibility
Alternative implementation
```

Do not add a library for functionality that can reasonably be implemented with existing project infrastructure.

---

# 37. Architecture Rules

Architecture must remain proportional to the product.

Avoid:

- Premature microservices
- Unnecessary abstraction
- Duplicate frameworks
- Multiple state-management systems without justification
- Unnecessary queues
- Unnecessary services
- Unnecessary infrastructure

The application should begin as a well-structured modular application.

---

# 38. Performance Rules

Performance must be considered during implementation.

Avoid:

- Loading every processing library on initial page load
- Blocking the main thread with heavy processing
- Loading unused assets
- Unnecessary network requests
- Excessive animation
- Large unoptimized images

Processing libraries should be loaded only when required.

---

# 39. Browser Processing Rules

Browser capabilities must be detected where required.

The application must not assume that every browser supports every API.

Unsupported functionality must result in:

```text
Detection
→ Clear explanation
→ Safe fallback where available
```

rather than an uncontrolled runtime failure.

---

# 40. Documentation Rules

Every significant architectural or functional decision must be documented.

Documentation must describe:

- What the system does
- Why it exists
- How it works
- Security considerations
- Testing requirements
- Known limitations

Documentation must be updated when implementation changes materially.

---

# 41. No Silent Architectural Changes

The development agent must not silently change:

- Framework
- Database
- ORM
- Authentication architecture
- Processing architecture
- Storage architecture
- Security model
- Page Builder architecture

Any material architectural change requires documentation and approval.

---

# 42. Git Rules

The repository must maintain a clean history.

Commits should represent logical changes.

Do not commit:

- Secrets
- Build artifacts
- Temporary files
- Local environment files
- Debug output
- User-uploaded processing files

---

# 43. CI Rules

Before merging a significant change, the project should verify:

```text
TypeScript
Lint
Unit Tests
Integration Tests
Build
Security Checks
```

The production build must not depend on local development state.

---

# 44. Production Rules

Production must use:

- Production environment variables
- Secure cookies
- Appropriate security headers
- Database backups
- Monitoring
- Error tracking
- Tested deployment process

Development credentials must never be reused in production.

---

# 45. Backup Rules

Important application data must have a backup strategy.

Backup policy must define:

- What is backed up
- Frequency
- Retention
- Storage location
- Encryption
- Restore procedure

A backup is not considered reliable until restoration has been tested.

---

# 46. Incident Response

Security incidents must have a documented response procedure.

At minimum:

```text
Detect
↓
Contain
↓
Investigate
↓
Remediate
↓
Verify
↓
Document
```

Security incidents must not be hidden through deletion of logs or evidence.

---

# 47. AI Development Rules

The development AI must:

- Read the relevant documentation before modifying a system.
- Follow the roadmap.
- Follow PROJECT_RULES.md.
- Check existing implementation before creating duplicate functionality.
- Avoid unnecessary refactoring.
- Run relevant tests after changes.
- Report failed tests accurately.
- Never claim a test passed without actually running it.
- Never claim a feature is complete when required validation is missing.
- Document material architectural decisions.

---

# 48. AI Must Not Assume Success

The development AI must distinguish between:

```text
Implemented
Tested
Passed
Approved
```

These are not equivalent.

For example:

```text
Implemented = code exists

Tested = test was executed

Passed = test succeeded

Approved = all required validation succeeded
```

---

# 49. Work Reporting

Every development task should produce a concise technical report containing:

```text
Changed
Added
Removed
Tests Run
Tests Passed
Tests Failed
Known Issues
Documentation Updated
Approval Status
```

The AI must not hide failed tests or known limitations.

---

# 50. Final Project Rule

The project prioritizes correctness over speed.

The following is prohibited:

```text
Fast implementation
→ Skip tests
→ Mark complete
```

The required process is:

```text
Understand
↓
Plan
↓
Implement
↓
Test
↓
Review
↓
Fix
↓
Retest
↓
Document
↓
Approve
```

No shortcut changes the definition of completion.
