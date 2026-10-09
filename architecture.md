# SYSTEM ARCHITECTURE

**Project:** Privacy-First File Tools Platform  
**Status:** Phase 0 — Architecture Specification  
**Version:** 0.1

---

# 1. Architecture Objective

The application must provide two fundamentally different capabilities:

1. Browser-based file processing for public users.
2. Server-backed CMS and administration for authorized administrators.

These systems must remain logically separated while sharing the same application foundation.

---

# 2. High-Level Architecture

```text
                         APPLICATION
                              │
             ┌────────────────┴────────────────┐
             │                                 │
       PUBLIC PLATFORM                    ADMIN PLATFORM
             │                                 │
      ┌──────┴──────┐                  ┌───────┴────────┐
      │             │                  │                │
   Website       Tools              CMS              Security
      │             │                  │                │
      │        Browser Engine      Pages              Auth
      │             │              Blocks             RBAC
      │        Web Workers         Media              Audit
      │             │              Blog
      │        Local Files         SEO
      │
      └───────────────┐
                      │
                 Next.js App
                      │
             ┌────────┴─────────┐
             │                  │
        Server Layer       Client Layer
             │                  │
        PostgreSQL          Browser APIs
        Prisma              Web Workers
        Auth                File APIs
        CMS                 IndexedDB
        APIs                Web Crypto
```

---

# 3. Technology Stack

## Frontend

- TypeScript
- React
- Next.js App Router
- Modern CSS/UI architecture
- Web APIs
- Web Workers

## Backend

- Next.js server-side capabilities
- Server Actions and/or Route Handlers where appropriate
- Authentication layer
- Authorization layer

## Database

- PostgreSQL
- Prisma ORM

## Storage

Object storage for administrator-managed media.

## Testing

Testing technology will be selected during Phase 1 based on current ecosystem stability and project requirements.

---

# 4. Next.js Architecture

The application will use the Next.js App Router.

The App Router is the current Next.js routing architecture and supports Server Components and modern React capabilities.

The application should use the separation between:

```text
Server Components
Client Components
Server Actions
Route Handlers
```

according to the responsibility of each feature.

Client Components must not be introduced unnecessarily.

---

# 5. Conceptual Application Structure

```text
src/
│
├── app/
│   ├── (public)/
│   ├── (tools)/
│   ├── (blog)/
│   ├── admin/
│   ├── api/
│   ├── login/
│   └── ...
│
├── components/
│   ├── ui/
│   ├── layout/
│   ├── public/
│   ├── admin/
│   └── tools/
│
├── features/
│   ├── tools/
│   ├── auth/
│   ├── cms/
│   ├── media/
│   ├── blog/
│   ├── seo/
│   └── analytics/
│
├── lib/
│   ├── db/
│   ├── auth/
│   ├── security/
│   ├── validation/
│   ├── seo/
│   └── utilities/
│
├── workers/
│   └── ...
│
└── types/
```

The exact directory structure may change during implementation if a documented architectural reason exists.

---

# 6. Public Platform

The public platform contains:

```text
Homepage
Tool Categories
Tool Pages
Blog
Static Pages
SEO Content
```

Public pages should be optimized for:

- SEO
- Performance
- Accessibility
- Fast navigation

---

# 7. Tool Architecture

Tools must be isolated from the CMS.

Conceptually:

```text
features/
└── tools/
    ├── core/
    ├── registry/
    ├── validation/
    ├── processing/
    ├── workers/
    ├── download/
    └── implementations/
```

Each tool implementation should have a predictable interface.

---

# 8. Tool Registry

The Tool Registry is the authoritative list of available tools.

Conceptually:

```typescript
ToolDefinition {
  id
  name
  category
  description
  inputFormats
  outputFormats
  options
  processor
  validator
  limits
  capabilities
}
```

The registry can later be used by:

- Tool pages
- Navigation
- Related tools
- SEO
- Sitemap
- Dashboard
- Testing
- Analytics

---

# 9. Browser Processing Engine

Public processing follows:

```text
File Input
    ↓
Browser Validation
    ↓
Tool Configuration
    ↓
Processing Engine
    ↓
Web Worker
    ↓
Result
    ↓
Validation
    ↓
Download
```

The main UI thread should remain responsive.

---

# 10. Web Worker Architecture

CPU-intensive processing should run in Web Workers when appropriate.

Conceptually:

```text
Main Thread
    │
    ├── UI
    ├── Upload State
    ├── Progress
    └── User Interaction
            │
            ↓
       Worker Manager
            │
            ↓
       Web Worker
            │
            ├── Decode
            ├── Process
            ├── Encode
            └── Return Result
```

Workers must not have unnecessary access to application state.

---

# 11. Processing Lifecycle

Every processing operation should have a defined state.

```text
IDLE
↓
VALIDATING
↓
READY
↓
PROCESSING
↓
COMPLETED
```

Alternative states:

```text
CANCELLED
FAILED
UNSUPPORTED
```

The UI must map these states consistently.

---

# 12. Processing Cancellation

Long-running operations must support cancellation where technically possible.

Cancellation must:

- Stop active processing
- Release temporary resources
- Reset UI state
- Avoid leaving broken worker instances
- Avoid leaking object URLs or memory

---

# 13. Batch Processing

Batch tools must support multiple input files where the specific tool allows it.

Conceptually:

```text
Files
 ↓
Validation
 ↓
Processing Queue
 ↓
Worker Pool / Sequential Processing
 ↓
Results
 ↓
ZIP / Individual Downloads
```

The exact concurrency model must be determined through performance testing.

The system must not process files concurrently merely because concurrency is possible.

Memory usage is the controlling constraint.

---

# 14. Browser Memory Management

File processing can consume significant memory.

The system must account for:

- Original file
- Decoded representation
- Intermediate representation
- Output
- Worker memory

Large files must not cause uncontrolled memory allocation.

Temporary resources must be released after completion or cancellation.

---

# 15. File Size Limits

Each tool must define its own practical limits where necessary.

Limits must be based on:

- Browser capabilities
- Processing complexity
- Memory consumption
- Performance testing

Limits must not be arbitrary.

---

# 16. Download Architecture

The browser should receive the processing result directly.

Conceptually:

```text
Worker Result
↓
Blob / File
↓
Object URL
↓
Download
↓
Resource Cleanup
```

For multiple outputs:

```text
Results
↓
ZIP Creation
↓
Download
↓
Cleanup
```

---

# 17. CMS Architecture

The CMS is server-backed.

```text
Admin Browser
    ↓
Authentication
    ↓
Authorization
    ↓
Server Layer
    ↓
Prisma
    ↓
PostgreSQL
```

CMS operations must never rely solely on client-side permissions.

---

# 18. Page Builder Architecture

The Page Builder uses structured blocks.

```text
Page
│
├── Block
├── Block
├── Block
└── Block
```

A page is not stored merely as arbitrary HTML.

Structured data allows:

- Editing
- Validation
- Copy/Paste
- Revisions
- Migration
- Responsive configuration
- SEO integration

---

# 19. Block Schema

Conceptually:

```typescript
Block {
  id
  type
  version
  content
  settings
  styles
  responsive
}
```

The `version` field allows future migrations.

---

# 20. Block Registry

Blocks should be registered similarly to tools.

```text
Block Registry
│
├── Hero
├── Heading
├── Text
├── Image
├── Button
├── Tool Grid
├── Feature Grid
├── FAQ
├── Blog Grid
└── CTA
```

The registry defines:

- Block type
- Editor configuration
- Renderer
- Validation
- Default values

---

# 21. Page Rendering

Published pages should render from validated structured content.

Conceptually:

```text
Published Page
↓
Page Data
↓
Block Validation
↓
Block Renderer
↓
HTML / React Output
```

Invalid blocks should not cause the entire website to fail.

---

# 22. Page Revisions

Conceptually:

```text
Page
│
├── Current Draft
├── Published Revision
├── Revision 1
├── Revision 2
└── Revision 3
```

Revision data must be immutable once published.

---

# 23. Media Architecture

The Media Library has two layers:

```text
Media Metadata
        ↓
PostgreSQL

Actual File
        ↓
Object Storage
```

The database should not be used as a binary file repository unless a specific future requirement justifies it.

---

# 24. Media Access

Media access must distinguish between:

- Public assets
- Restricted assets

Public website assets may use direct URLs.

Restricted assets must require appropriate authorization.

---

# 25. Blog Architecture

Blog posts use structured content.

Conceptually:

```text
Post
├── Metadata
├── SEO
├── Featured Media
├── Content
├── Categories
├── Tags
└── Publication State
```

---

# 26. SEO Architecture

SEO data is a reusable system.

Conceptually:

```text
Content Entity
      ↓
SEO Configuration
      ↓
Metadata Generator
      ↓
HTML Metadata
      +
JSON-LD
      +
Sitemap
```

SEO should not be duplicated across page, blog, and tool implementations.

---

# 27. Authentication Architecture

Authentication protects administrative routes.

Conceptually:

```text
Request
↓
Authentication
↓
Session Validation
↓
Authorization
↓
Resource Access
```

Next.js's current App Router documentation explicitly distinguishes authentication from authorization and demonstrates protecting dashboard routes through the framework's request-level mechanisms.

The exact authentication implementation will be finalized during Phase 1 after reviewing the current stable authentication ecosystem.

---

# 28. Database Architecture

Prisma provides the application data-access layer.

Conceptually:

```text
Server Code
    ↓
Domain/Data Layer
    ↓
Prisma
    ↓
PostgreSQL
```

Components should not directly construct arbitrary database queries.

---

# 29. Data Ownership

Each database entity must have a clear owner.

Example:

```text
Page
→ CMS

Post
→ Blog

Media
→ Media System

Tool
→ Tool Registry

SEO
→ SEO System

AuditLog
→ Security System
```

This reduces accidental cross-module coupling.

---

# 30. Module Boundaries

The primary modules are:

```text
Auth
Tools
CMS
Pages
Blocks
Media
Blog
SEO
Security
Analytics
Settings
```

Modules may use shared infrastructure but should not directly manipulate another module's internal implementation.

---

# 31. API Architecture

API endpoints are only required where server communication is necessary.

Do not create API endpoints for operations that can safely remain entirely client-side.

For protected operations:

```text
Request
↓
Validation
↓
Authentication
↓
Authorization
↓
Business Logic
↓
Database / Storage
```

Input validation must occur server-side.

---

# 32. Validation

Validation exists at multiple boundaries:

```text
UI Validation
      ↓
Client Processing Validation
      ↓
Server Validation
      ↓
Database Constraints
```

Client validation improves UX.

Server validation provides security.

Database constraints provide integrity.

---

# 33. Error Architecture

The application should use consistent error categories.

```text
ValidationError
AuthorizationError
AuthenticationError
NotFoundError
ProcessingError
StorageError
DatabaseError
SystemError
```

Internal errors must not leak implementation details.

---

# 34. Security Boundary

The security boundary is the server.

Client-side code is considered untrusted.

Never trust:

- Client role
- Client permissions
- Client IDs
- Client filenames
- Client MIME types
- Client metadata
- Client-provided URLs

Server-side validation remains mandatory.

---

# 35. Content Security

The CMS introduces potentially dangerous content.

Therefore:

- HTML must be controlled
- URLs must be validated
- Media references must be validated
- SVG must receive special treatment
- Custom scripts must not be allowed by default

Arbitrary JavaScript execution from CMS content is outside the initial product scope.

---

# 36. Caching

Caching should be introduced selectively.

Potential candidates:

- Published pages
- Blog posts
- Tool metadata
- Static assets
- SEO data

Draft CMS content must not accidentally become publicly cached.

Cache invalidation must be defined before caching is introduced into a module.

---

# 37. Rendering Strategy

Different content types may use different rendering strategies.

```text
Static content
→ Static / cached rendering

Dynamic CMS content
→ Appropriate server rendering

Interactive tools
→ Client Components

Admin
→ Protected dynamic application
```

The implementation should use the least expensive rendering strategy that satisfies the feature.

---

# 38. Code Splitting

Tool-specific processing libraries must not be loaded globally.

Example:

```text
Homepage
→ No PDF processing library

Image Compressor
→ Load image compression dependencies

PDF Merge
→ Load PDF dependencies
```

This is mandatory for keeping the public application lightweight.

---

# 39. Dependency Isolation

Heavy or specialized dependencies should remain isolated inside the feature that requires them.

Avoid:

```text
Global Bundle
→ All converters
→ All PDF libraries
→ All image libraries
→ All processing engines
```

Prefer:

```text
Tool
→ Required dependencies only
```

---

# 40. Observability

The application should provide enough observability to diagnose:

- Processing failures
- Application errors
- Authentication failures
- CMS errors
- Storage failures
- Performance problems

Observability must not capture user file contents.

---

# 41. Analytics Boundary

Product analytics may measure application behavior, but must not transmit public user processing files.

Allowed examples:

```text
Tool opened
Tool completed
Tool failed
Processing duration
Browser type
```

File contents and sensitive file data are outside analytics scope.

---

# 42. Deployment Architecture

The deployment architecture must support:

```text
Development
↓
Staging
↓
Production
```

Production must not be used as the primary development/testing environment.

---

# 43. Environment Configuration

Configuration must be separated by environment.

Sensitive values must be injected through environment configuration or a secrets manager.

The source repository must contain only safe configuration examples.

---

# 44. Backup Architecture

Database backups must be independent from the application runtime.

Media storage must have its own backup strategy where required.

Backup restoration must be periodically verified.

---

# 45. Architecture Decision Rule

If two architectural approaches can satisfy the same requirement:

1. Prefer the simpler approach.
2. Prefer the approach with fewer dependencies.
3. Prefer the approach with fewer security risks.
4. Prefer the approach with lower maintenance cost.
5. Prefer the approach that preserves browser-side processing.
6. Prefer the approach that is easier to test.

Complexity requires justification.

---

# 46. Architecture Change Rule

A material architecture change requires:

```text
Reason
Current approach
Proposed approach
Advantages
Disadvantages
Security impact
Performance impact
Migration impact
Testing impact
```

The change must be documented before implementation.

---

# 47. Architecture Completion Criteria

Phase 0 architecture is considered complete when:

- Module boundaries are defined.
- Public processing architecture is defined.
- Browser-processing privacy model is defined.
- CMS architecture is defined.
- Database responsibility is defined.
- Storage responsibility is defined.
- Authentication boundary is defined.
- Authorization boundary is defined.
- Testing boundaries are defined.
- Security boundaries are defined.
- Documentation structure is defined.

Only then does Phase 1 begin.
