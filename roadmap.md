# Project Roadmap

**Project:** Privacy-First File Tools Platform  
**Document:** `roadmap.md`  
**Version:** 1.0  
**Status:** Approved — Execution Boundary  
**Scope:** Entire Project

---

## 1. Purpose

This document defines the fixed execution roadmap for the project.

The roadmap is the project's execution boundary. Development proceeds sequentially. A later phase must not be implemented before the required previous phase has been completed and approved.

This document does not replace `PRD.md`. The PRD defines product requirements; this document defines the execution sequence and phase gates.

---

## 2. Source of Truth

Project authority is defined by `PROJECT_RULES.md`.

Within the roadmap itself:

1. Phase order is mandatory.
2. Phase scope is mandatory.
3. Phase completion requires the defined acceptance gates.
4. Implementation details may be refined in approved technical documents without changing phase scope.
5. A change to the roadmap requires explicit project-level approval and documentation.

---

## 3. Global Execution Rules

Every phase follows:

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

A phase is not complete merely because code exists.

### 3.1 Approval Rule

A phase can become `APPROVED` only when:

- Required implementation is complete.
- Required tests pass.
- Security requirements are verified.
- Performance requirements are verified where applicable.
- Accessibility requirements are verified where applicable.
- Browser compatibility is verified where applicable.
- Documentation is synchronized.
- Regression testing passes.
- No blocking known issue remains.

### 3.2 Tool Approval Rule

Every public processing tool is approved independently.

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

A failed mandatory test prevents approval.

---

# Phase 0 — Product Foundation

**Objective:** Establish the product definition, architecture, security model, data policy, testing model, governance rules, and documentation foundation.

### Scope

- `PRD.md`
- `PROJECT_RULES.md`
- `architecture.md`
- `security.md`
- `auth.md`
- `data.md`
- `hack.md`
- `testing.md`
- `incident-response.md`
- `checklist.md`
- `roadmap.md`

### Acceptance

- Product scope defined.
- Privacy model defined.
- Architecture defined.
- Security model defined.
- Authentication and authorization requirements defined.
- Data lifecycle defined.
- Threat model defined.
- Testing strategy defined.
- Incident response defined.
- Project governance defined.
- Roadmap approved.
- Documentation references are consistent.

**Gate:** Phase 0 APPROVED.

---

# Phase 1 — Technical Foundation

**Objective:** Build the stable application foundation required by all later phases.

## 1.1 Repository & Tooling

- Repository structure
- Package manager
- TypeScript configuration
- Linting
- Formatting
- Git configuration
- Development scripts
- Base CI configuration

## 1.2 Next.js Application Foundation

- Next.js App Router
- Application layout
- Public/admin route boundaries
- Server/client boundaries
- Error boundaries
- Loading states
- Not-found handling

## 1.3 Environment Configuration

- Development configuration
- Test configuration
- Production configuration
- Environment validation
- Secret handling
- Public vs private environment variables

## 1.4 Database & Prisma

- PostgreSQL connection
- Prisma configuration
- Initial schema foundation
- Migration workflow
- Database client architecture
- Development/test database strategy

## 1.5 Security Foundation

- Security headers
- Secure defaults
- Input-validation foundation
- Error-safety foundation
- Secret-handling foundation
- Logging restrictions
- Security utility layer

## 1.6 Authentication

- Admin login
- Password hashing
- Session creation
- Session validation
- Logout
- Session invalidation
- Password reset foundation
- Brute-force protection
- Rate limiting
- Account protection
- Secure cookies

## 1.7 Authorization

- Server-side authorization
- Permission model
- Protected route checks
- Protected API checks
- Unauthorized/forbidden handling

## 1.8 Error & Logging Foundation

- Error taxonomy
- Safe user errors
- Internal diagnostics
- Structured logging
- Sensitive-data redaction

## 1.9 UI / Design System

- Typography
- Spacing
- Layout primitives
- Buttons
- Inputs
- Cards
- Dialogs
- Alerts
- Progress indicators
- Tool UI primitives
- Admin UI primitives

## 1.10 Theme System

- Light mode
- Dark mode
- System preference
- Theme persistence
- No flash on initial load
- Theme accessibility

## 1.11 Accessibility Foundation

- Keyboard navigation
- Focus management
- Focus-visible states
- Semantic HTML
- Screen-reader foundations
- Contrast
- Reduced motion

## 1.12 API Foundation

- API conventions
- Validation
- Authentication boundary
- Authorization boundary
- Error responses
- Rate-limit integration

## 1.13 Testing Foundation

- Unit test framework
- Integration test framework
- E2E framework
- Test utilities
- Fixtures
- Coverage strategy

## 1.14 CI

- Install
- Typecheck
- Lint
- Unit tests
- Integration tests
- Build
- E2E where environment permits
- Security/dependency checks

## 1.15 Phase Verification

- Full Phase 1 test suite
- Security verification
- Responsive verification
- Light/Dark verification
- Documentation synchronization
- Regression verification

**Gate:** Phase 1 APPROVED.

---

# Phase 2 — File Processing Engine

**Objective:** Build the reusable browser-first processing engine before implementing individual public tools.

## 2.1 Engine Core Types & Contracts

- Tool contract
- Input/output contract
- Processing context
- Result contract
- Error contract
- Progress contract
- Cancellation contract
- Capability contract

## 2.2 Input & Validation

- File validation
- Size limits
- Type validation
- Extension handling
- File-signature validation where applicable
- Multiple-file validation
- Validation errors

## 2.3 Format Detection

- MIME detection
- Extension detection
- Signature detection where applicable
- Conflict handling
- Unknown-format handling

## 2.4 Processing Lifecycle

```text
Idle
→ Validating
→ Preparing
→ Processing
→ Finalizing
→ Validating Result
→ Ready
```

Also define:

- Cancelled
- Failed
- Unsupported
- Resource-limited

## 2.5 Browser Capability Detection

- Required Web API detection
- Worker support
- File API support
- Blob/stream capabilities where required
- Capability-specific errors
- Graceful degradation

## 2.6 Web Worker System

- Worker lifecycle
- Message protocol
- Job isolation
- Worker errors
- Worker termination
- Worker reuse policy
- Main-thread fallback only where explicitly approved

## 2.7 Progress System

- Determinate progress
- Indeterminate progress
- Progress events
- Stage labels
- UI progress mapping

## 2.8 Cancellation System

- User cancellation
- Abort propagation
- Worker cancellation
- Cleanup after cancellation
- Cancellation race handling

## 2.9 Memory Management

- Blob lifecycle
- Object URL lifecycle
- Buffer cleanup
- Worker memory handling
- Large-file limits
- Batch memory limits

## 2.10 Result Validation

- Output existence
- Output type
- Output size
- Output readability
- Output integrity where practical
- Invalid-result handling

## 2.11 Download System

- Safe downloads
- Filename generation
- Object URL cleanup
- Individual downloads
- ZIP downloads where applicable

## 2.12 Batch Processing

- Queueing
- Per-file state
- Partial failures
- Progress
- Cancellation
- Result collection
- Batch limits

## 2.13 Error Handling Integration

- User errors
- Capability errors
- Processing errors
- Resource errors
- Unexpected errors
- Safe technical diagnostics

## 2.14 Engine Orchestration

- Tool registry integration
- Lifecycle orchestration
- Worker orchestration
- Progress orchestration
- Cancellation orchestration
- Result handling

## 2.15 Engine Testing

- Unit tests
- Integration tests
- E2E tests
- Worker tests
- Cancellation tests
- Memory tests
- Privacy/network tests
- Error-path tests

## 2.16 Privacy & Security Verification

Verify that public processing files are not sent to:

- Application APIs
- Database
- Object storage
- Analytics
- Error-reporting systems
- Third-party processors
- Debugging/telemetry systems

## 2.17 Performance & Memory Verification

- Small files
- Medium files
- Large files within limits
- Batch workloads
- Main-thread responsiveness
- Worker behavior
- Memory cleanup

## 2.18 Accessibility & Browser Verification

- Chrome
- Firefox
- Safari
- Edge
- Desktop
- Mobile
- Keyboard operation
- Screen-reader relevant states
- Reduced motion
- Light mode
- Dark mode

## 2.19 Full Regression

Run the complete Phase 1 regression suite plus all Phase 2 tests.

## 2.20 Documentation Synchronization

Update all affected architecture, security, testing, and processing documentation.

**Gate:** Phase 2 APPROVED.

---

# Phase 3 — Initial Format Conversion Tools

**Objective:** Establish the first production-quality format-conversion tool family on top of the approved processing engine.

## 3.1 Tool Registry

- Tool identifiers
- Categories
- Metadata
- Capability requirements
- Input/output definitions
- Tool status

## 3.2 Tool Specification

Every tool receives an independent specification containing:

- Purpose
- Supported inputs
- Supported outputs
- Options
- Limits
- Validation
- Processing architecture
- Errors
- Privacy behavior
- Security considerations
- Performance considerations
- Test plan
- Documentation requirements

## 3.3 Initial Conversion Tools

Implement only conversions that meet the browser-first reliability requirement and are explicitly approved by the tool matrix.

Each tool is implemented and approved independently.

## 3.4 Individual Approval

No next tool begins its approval cycle until the current tool reaches `APPROVED`.

**Gate:** Phase 3 APPROVED.

---

# Phase 4 — Image Tools

**Objective:** Implement the complete approved image-processing family.

### Tools

- Image Converter
- Image Compressor
- Image Resizer
- Image Cropper
- Image Rotator
- Image Flipper
- Image Optimizer
- Image Metadata Removal
- Image to Base64
- Base64 to Image
- Batch Image Processing

### Requirements

Every image tool must independently pass:

- Unit tests
- Integration tests
- E2E tests
- Security tests
- Performance tests
- Memory tests
- Browser tests
- Accessibility tests
- Documentation review

**Gate:** Phase 4 APPROVED.

---

# Phase 5 — PDF Tools

**Objective:** Implement the complete approved PDF-processing family.

### Tools

- PDF Merge
- PDF Split
- PDF Compress
- PDF Rotate
- PDF Page Extraction
- PDF Page Reordering
- PDF Page Deletion
- PDF to Image
- Image to PDF

### Requirements

Every PDF tool must independently pass:

- Unit tests
- Integration tests
- E2E tests
- Security tests
- Performance tests
- Memory tests
- Browser tests
- Accessibility tests
- Malformed-PDF tests
- Resource-limit tests
- Documentation review

**Gate:** Phase 5 APPROVED.

---

# Phase 6 — CMS

**Objective:** Build the administration and content-management system.

## Scope

- CMS architecture
- Admin dashboard
- Pages
- Page Builder
- Blocks
- Block registry
- Block validation
- Create/edit/delete blocks
- Duplicate
- Copy/paste
- Reusable blocks
- Responsive preview
- Undo/redo
- Draft/publish
- Page revisions
- Revision restore
- Media Library
- Media folders
- Media metadata
- Navigation
- Tool management

### Security Requirements

- Server-side authorization
- CSRF protection where applicable
- XSS protection
- SVG security
- File upload security
- IDOR protection
- Audit logging
- Rate limiting
- Safe rich-text handling

**Gate:** Phase 6 APPROVED.

---

# Phase 7 — Blog

**Objective:** Build the complete blog management system.

### Scope

- Blog editor
- Posts
- Categories
- Tags
- Slugs
- Excerpts
- Featured images
- Drafts
- Publishing
- Related content
- Content validation
- Blog SEO
- Structured data

### Security

- Stored-XSS protection
- Safe links
- Safe media
- Safe embeds
- Publication authorization
- Draft isolation

**Gate:** Phase 7 APPROVED.

---

# Phase 8 — SEO

**Objective:** Implement the production SEO system across the public website.

## Scope

- SEO manager
- SEO title
- Meta description
- Canonical
- Robots directives
- Open Graph
- Social metadata
- JSON-LD
- Schema validation
- Breadcrumbs
- Sitemap
- Robots.txt
- Indexing controls
- Internal linking support
- SEO integration for tools
- SEO integration for pages
- SEO integration for blog content

### Structured Data

Initial supported types:

- WebSite
- WebPage
- Organization
- Article
- BlogPosting
- FAQPage
- BreadcrumbList
- SoftwareApplication
- HowTo

**Gate:** Phase 8 APPROVED.

---

# Phase 9 — Security Hardening

**Objective:** Perform the full security audit after the major application surfaces exist.

## Scope

- Threat-model review
- Authentication audit
- Authorization audit
- Session audit
- File-processing security audit
- Upload security audit
- XSS testing
- CSRF testing
- SSRF review
- IDOR testing
- Path traversal testing
- Rate-limit testing
- API abuse testing
- Dependency audit
- Secret audit
- Security-header review
- CSP review
- Privacy/network inspection
- Security regression tests
- Incident-response verification

### Blocking Findings

- Critical: blocks immediately.
- High: blocks release.
- Medium: must be fixed unless explicitly accepted and documented.
- Low: tracked and prioritized.

**Gate:** Phase 9 APPROVED.

---

# Phase 10 — Performance & Accessibility

**Objective:** Optimize the complete product without changing approved functionality.

## Performance

- Core Web Vitals
- Initial bundle
- Code splitting
- Lazy loading
- Worker optimization
- Processing performance
- Memory behavior
- Image loading
- Caching
- Mobile performance

## Accessibility

- WCAG 2.2 AA review
- Keyboard navigation
- Focus management
- Screen readers
- Contrast
- Forms
- Upload workflows
- Processing states
- Error states
- Reduced motion
- Light/Dark modes

## Compatibility

- Chrome
- Firefox
- Safari
- Edge
- Desktop
- Tablet
- Mobile

**Gate:** Phase 10 APPROVED.

---

# Phase 11 — Final QA

**Objective:** Validate the complete product as a production candidate.

## Scope

- Full E2E testing
- Full regression testing
- All approved tools
- CMS
- Blog
- SEO
- Security
- Performance
- Accessibility
- Browser compatibility
- Privacy verification
- Backup/restore verification
- Production build
- Production-like environment
- Critical user journeys
- Error-path testing
- Documentation verification

No new feature development is allowed in Final QA unless it is required to fix a discovered defect or an explicitly approved release blocker.

**Gate:** Final QA APPROVED.

---

# Phase 12 — Production

**Objective:** Deploy the approved system safely.

## Scope

- Production infrastructure
- PostgreSQL
- Object storage
- Environment configuration
- Secrets management
- Database migrations
- Backups
- Restore verification
- Monitoring
- Error tracking
- Domain
- HTTPS
- Security headers
- Production deployment
- Smoke tests
- Production privacy verification
- Launch
- Post-launch verification
- Final documentation

Production launch requires explicit production approval.

**Gate:** Production APPROVED.

---

# 4. Roadmap Change Policy

The roadmap must not be changed casually.

A proposed change must document:

1. Reason for change
2. Problem being solved
3. Affected phase
4. Affected scope
5. Dependencies
6. Security impact
7. Testing impact
8. Documentation impact
9. Regression impact
10. Why the existing roadmap is insufficient

The change must be explicitly approved before implementation.

---

# 5. Phase State Model

Every phase uses:

```text
PLANNED
→ IN PROGRESS
→ IMPLEMENTATION COMPLETE
→ TESTING
→ VERIFICATION
→ DOCUMENTATION COMPLETE
→ APPROVED
```

A failed test moves the affected scope back to the appropriate implementation/fix state.

`IMPLEMENTATION COMPLETE` does not mean `APPROVED`.

---

# 6. Project Completion Rule

The project is complete only when:

- Phases 0–12 are approved.
- All required tools are approved individually.
- All mandatory security checks pass.
- Full regression passes.
- Production deployment passes.
- Production smoke tests pass.
- Documentation is synchronized with implementation.
- No unresolved release-blocking issue remains.
