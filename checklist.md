# Project Security & Quality Checklist

**Document Version:** 1.0  
**Status:** Mandatory  
**Applies To:** Entire Project  
**Authority:** `PROJECT_RULES.md`

---

# 1. Purpose

This document is the mandatory operational checklist for the entire project.

It converts the requirements defined in:

- `PROJECT_RULES.md`
- `PRD.md`
- `architecture.md`
- `security.md`
- `auth.md`
- `data.md`
- `hack.md`

into concrete checks that must be performed before implementation approval, feature approval, deployment, and release.

This document is not optional documentation.

A feature is not considered complete merely because its code works.

A feature must satisfy:

1. Product requirements
2. Architecture requirements
3. Security requirements
4. Privacy requirements
5. Testing requirements
6. Documentation requirements
7. Performance requirements
8. Accessibility requirements
9. Roadmap requirements

---

# 2. Checklist Status Rules

Every checklist item must have one of these states:

- `[ ]` Not checked
- `[x]` Checked
- `[PASS]` Tested and passed
- `[FAIL]` Tested and failed
- `[N/A]` Explicitly determined not applicable

Important:

> Checked does not mean passed.

A checkbox can only become `[PASS]` after the relevant behavior has actually been tested.

A feature cannot be `APPROVED` while a required item is `[FAIL]`.

---

# 3. Absolute Project Rules

Before any implementation work:

- [ ] `PROJECT_RULES.md` has been read.
- [ ] `PRD.md` has been read.
- [ ] Relevant architecture documentation has been read.
- [ ] Relevant feature documentation has been read.
- [ ] Current roadmap phase has been identified.
- [ ] Current task exists in the approved roadmap.
- [ ] Task does not introduce unauthorized scope.
- [ ] Existing implementation has been inspected.
- [ ] Existing functionality has not been duplicated.
- [ ] Existing security controls have been inspected.
- [ ] Existing tests have been inspected.

If any required source document is missing or contradictory:

**STOP implementation.**

Do not silently invent a solution.

---

# 4. Roadmap Lock Checklist

Before starting work:

- [ ] Task belongs to the current roadmap phase.
- [ ] No future-phase feature is being implemented early.
- [ ] No feature outside the roadmap has been added.
- [ ] No architecture change has been made without documentation.
- [ ] No technology has been introduced without justification.
- [ ] No unnecessary dependency has been added.
- [ ] No unnecessary backend service has been introduced.
- [ ] No unnecessary database entity has been introduced.
- [ ] No unnecessary abstraction has been introduced.

If a requirement appears to require scope expansion:

- [ ] Problem documented.
- [ ] Reason documented.
- [ ] Product impact documented.
- [ ] Security impact documented.
- [ ] Architecture impact documented.
- [ ] Testing impact documented.
- [ ] Roadmap impact documented.
- [ ] Explicit approval obtained before implementation.

---

# 5. Privacy Checklist

## Public File Processing

For every public file-processing tool:

- [ ] Processing occurs in the user's browser whenever technically feasible.
- [ ] User files are not uploaded to the application server for processing.
- [ ] No hidden upload endpoint exists for the tool.
- [ ] No form submission uploads the file.
- [ ] No analytics system receives the file.
- [ ] No error-reporting system receives the file.
- [ ] No third-party service receives the file.
- [ ] No debugging system stores the file.
- [ ] No server-side temporary copy is created.
- [ ] No server-side database record is created for the processing file.
- [ ] Browser memory is released after processing.
- [ ] Temporary browser objects are released when no longer needed.
- [ ] Object URLs are revoked.
- [ ] Workers are terminated when no longer required.
- [ ] Cancelled processing cleans up resources.
- [ ] Failed processing cleans up resources.
- [ ] Downloaded output is generated locally whenever possible.

## Privacy Verification

- [ ] Browser Network panel inspected.
- [ ] No unexpected file request detected.
- [ ] No request body contains user file data.
- [ ] No third-party request contains user file data.
- [ ] Error flows tested.
- [ ] Cancel flows tested.
- [ ] Multiple-file flows tested.

---

# 6. File Input Checklist

For every tool accepting files:

- [ ] Accepted file extensions are explicitly defined.
- [ ] Accepted MIME types are explicitly defined.
- [ ] Unsupported formats are rejected.
- [ ] Empty files are handled.
- [ ] Extremely small files are handled.
- [ ] Large files are handled safely.
- [ ] Maximum file size is defined.
- [ ] Maximum number of files is defined.
- [ ] Filename length is bounded.
- [ ] Filename is treated as untrusted input.
- [ ] MIME type is treated as untrusted metadata.
- [ ] File contents are validated where required.
- [ ] File signature/magic bytes are validated where required.
- [ ] Malformed files are rejected safely.
- [ ] Duplicate files are handled correctly.
- [ ] Files with unusual Unicode names are tested.
- [ ] Files with special characters are tested.
- [ ] Files with multiple extensions are tested.
- [ ] Files with misleading extensions are tested.
- [ ] File-processing libraries receive validated input.
- [ ] Errors do not expose internal paths.
- [ ] Errors do not expose stack traces to users.
- [ ] Processing limits prevent browser resource exhaustion.

Client-side validation must not be considered a security boundary where server-side validation is applicable. OWASP explicitly recommends validating inputs at the receiving trust boundary and treating filenames and file metadata as untrusted.

---

# 7. File Processing Security Checklist

For each processing engine:

- [ ] Parser/library is documented.
- [ ] Parser/library version is documented.
- [ ] Known security limitations are documented.
- [ ] Supported formats are documented.
- [ ] Unsupported formats are rejected.
- [ ] Malformed input is tested.
- [ ] Oversized input is tested.
- [ ] Repeated processing is tested.
- [ ] Concurrent processing is tested.
- [ ] Cancellation is tested.
- [ ] Memory cleanup is tested.
- [ ] Worker termination is tested where applicable.
- [ ] Infinite-loop scenarios are considered.
- [ ] Decompression amplification is considered.
- [ ] Recursive structures are considered.
- [ ] Parser crashes do not crash the entire application.
- [ ] Processing exceptions are handled.
- [ ] User receives safe error messages.
- [ ] Internal implementation details are not exposed.

---

# 8. Browser Resource Protection

Because the product performs processing locally:

- [ ] Maximum file size exists.
- [ ] Maximum number of simultaneous files exists.
- [ ] Maximum batch size exists.
- [ ] Large image dimensions are handled.
- [ ] Extremely high-resolution images are handled.
- [ ] Memory-intensive operations are tested.
- [ ] Worker usage is evaluated.
- [ ] UI remains responsive during processing.
- [ ] Processing can be cancelled where practical.
- [ ] Browser tab does not become permanently unresponsive.
- [ ] Failed processing releases memory.
- [ ] Completed processing releases unnecessary memory.
- [ ] Multiple consecutive jobs do not cause uncontrolled memory growth.

---

# 9. XSS Checklist

For all user-controlled or administrator-controlled content:

- [ ] HTML is not inserted unsafely.
- [ ] Rich text is sanitized.
- [ ] URLs are validated.
- [ ] JavaScript URLs are rejected.
- [ ] Dangerous HTML attributes are rejected.
- [ ] User-generated content is escaped where appropriate.
- [ ] Blog content is sanitized.
- [ ] Page-builder content is sanitized.
- [ ] SEO fields are safely rendered.
- [ ] Image metadata is not blindly rendered.
- [ ] Filenames are safely displayed.
- [ ] SVG content is treated as potentially active content.
- [ ] Inline scripts cannot be injected through content fields.
- [ ] Stored XSS is tested.
- [ ] Reflected XSS is tested.
- [ ] DOM-based XSS is tested.

---

# 10. CSRF Checklist

For state-changing authenticated operations:

- [ ] CSRF protection strategy is documented.
- [ ] POST/PUT/PATCH/DELETE operations are protected as required.
- [ ] Authentication cookies use appropriate attributes.
- [ ] SameSite policy is configured appropriately.
- [ ] Origin/Referer checks are considered where appropriate.
- [ ] Cross-origin requests are restricted.
- [ ] Admin actions cannot be triggered cross-site.
- [ ] Media management cannot be triggered cross-site.
- [ ] Page publishing cannot be triggered cross-site.
- [ ] User/password operations cannot be triggered cross-site.

---

# 11. SQL Injection Checklist

- [ ] Prisma parameterization is used correctly.
- [ ] Raw SQL is avoided unless necessary.
- [ ] Any raw SQL has been reviewed.
- [ ] User-controlled strings are never concatenated into SQL.
- [ ] Search filters are validated.
- [ ] Sort fields are allowlisted.
- [ ] Pagination parameters are bounded.
- [ ] Dynamic table/column selection is allowlisted.
- [ ] Database errors are not exposed to users.

---

# 12. Authentication Checklist

See `auth.md` for full requirements.

- [ ] Login exists only where required.
- [ ] Public tools do not require unnecessary accounts.
- [ ] Passwords are never stored plaintext.
- [ ] Password hashing uses an appropriate modern password-hashing approach.
- [ ] Password verification is secure.
- [ ] Login rate limiting exists.
- [ ] Repeated failures trigger appropriate protection.
- [ ] Account lockout/protection behavior is implemented.
- [ ] Authentication errors do not enable easy account enumeration.
- [ ] Sessions are securely generated.
- [ ] Sessions are server-verifiable.
- [ ] Session cookies are secure.
- [ ] Session cookies use HttpOnly where appropriate.
- [ ] Session cookies use appropriate SameSite configuration.
- [ ] Logout invalidates the session.
- [ ] Session invalidation is tested.
- [ ] Password reset flow is protected.
- [ ] Password reset tokens are protected.
- [ ] Password reset tokens expire.
- [ ] Password reset tokens cannot be reused.
- [ ] Admin authentication is separately reviewed.

---

# 13. Authorization Checklist

For every protected resource:

- [ ] Authentication is checked.
- [ ] Authorization is checked.
- [ ] Authorization occurs server-side.
- [ ] Role requirements are defined.
- [ ] Resource ownership is checked where required.
- [ ] Direct object references are protected.
- [ ] IDOR testing is performed.
- [ ] Admin-only endpoints reject normal users.
- [ ] Normal users cannot access admin routes.
- [ ] Normal users cannot manipulate admin data.
- [ ] Client-side UI hiding is not used as the security mechanism.
- [ ] Unauthorized requests return safe responses.

---

# 14. Session Security Checklist

- [ ] Session identifiers are unpredictable.
- [ ] Session fixation is prevented.
- [ ] Logout invalidates the server-side session.
- [ ] Expired sessions are rejected.
- [ ] Revoked sessions are rejected.
- [ ] Session expiration behavior is documented.
- [ ] Session renewal behavior is documented.
- [ ] Session cookies are not accessible to JavaScript when unnecessary.
- [ ] Session data does not contain unnecessary sensitive information.
- [ ] Concurrent session behavior is defined.
- [ ] Suspicious session activity can be investigated.

---

# 15. API Security Checklist

For every API endpoint:

- [ ] Endpoint purpose documented.
- [ ] HTTP method documented.
- [ ] Authentication requirement documented.
- [ ] Authorization requirement documented.
- [ ] Request schema documented.
- [ ] Response schema documented.
- [ ] Input validation exists.
- [ ] Input size limits exist.
- [ ] Rate limits exist where needed.
- [ ] Error behavior documented.
- [ ] Sensitive fields excluded.
- [ ] Internal errors are not exposed.
- [ ] CORS behavior is intentional.
- [ ] CSRF protection exists where relevant.
- [ ] Logging behavior is defined.
- [ ] Abuse cases are tested.

---

# 16. SSRF Checklist

For any functionality that accepts URLs:

- [ ] URL input is actually required.
- [ ] URL schemes are allowlisted.
- [ ] Dangerous schemes are rejected.
- [ ] Internal addresses are protected.
- [ ] Localhost access is prevented where applicable.
- [ ] Private network access is prevented where applicable.
- [ ] Redirect behavior is controlled.
- [ ] DNS rebinding risks are considered.
- [ ] Metadata service access is blocked where applicable.
- [ ] Network timeouts exist.
- [ ] Response-size limits exist.

If a feature does not require URL fetching:

- [ ] URL fetching is not implemented.

---

# 17. Open Redirect Checklist

For redirects:

- [ ] Redirect destinations are allowlisted where possible.
- [ ] User-controlled redirect URLs are not blindly trusted.
- [ ] External redirects are intentional.
- [ ] Protocol-relative URLs are handled safely.
- [ ] JavaScript URLs are rejected.
- [ ] Redirect parameters are validated.
- [ ] Login/logout redirects are tested.

---

# 18. Path Traversal Checklist

- [ ] User input never directly becomes a filesystem path.
- [ ] `../` traversal is rejected or neutralized safely.
- [ ] Encoded traversal is tested.
- [ ] Windows path separators are tested.
- [ ] Null-byte attacks are tested.
- [ ] Absolute paths are rejected.
- [ ] System paths are inaccessible.
- [ ] File identifiers are mapped to internal records.
- [ ] Internal filesystem paths are never exposed to users.

---

# 19. Media Library Checklist

The Media Library is server-backed and must remain separate from public browser processing.

- [ ] Media Library requires appropriate authentication.
- [ ] Media Library requires appropriate authorization.
- [ ] Upload permissions are restricted.
- [ ] Allowed media types are defined.
- [ ] File size limits exist.
- [ ] Filename rules exist.
- [ ] File content validation exists.
- [ ] Stored filenames are generated safely.
- [ ] Storage is outside executable web locations where appropriate.
- [ ] Media URLs are controlled.
- [ ] Unauthorized media access is prevented.
- [ ] Media deletion requires authorization.
- [ ] Media replacement requires authorization.
- [ ] Media metadata is validated.
- [ ] Alt text is safely handled.
- [ ] Media search is access-controlled.
- [ ] Direct object access is tested.
- [ ] Deleted media is actually removed according to retention policy.

OWASP recommends layered file-upload defenses rather than relying on a single extension or MIME-type check.

---

# 20. SVG Security Checklist

SVG requires special treatment.

- [ ] SVG is explicitly classified as potentially active content.
- [ ] SVG upload policy is documented.
- [ ] SVG sanitization strategy exists where SVG is accepted.
- [ ] Embedded scripts are rejected.
- [ ] Dangerous event handlers are rejected.
- [ ] External resource references are controlled.
- [ ] SVG preview behavior is tested.
- [ ] SVG stored XSS is tested.
- [ ] SVG used as an image is isolated appropriately.

---

# 21. Page Builder Checklist

For every block:

- [ ] Block has a unique type.
- [ ] Block schema is documented.
- [ ] Block version is defined.
- [ ] Block input is validated.
- [ ] Block output is safely rendered.
- [ ] Block cannot execute arbitrary JavaScript.
- [ ] URLs are validated.
- [ ] Media references are validated.
- [ ] Responsive settings are validated.
- [ ] Unsupported properties are ignored safely.
- [ ] Duplicate operation works.
- [ ] Copy operation works.
- [ ] Paste operation works.
- [ ] Delete operation works.
- [ ] Move operation works.
- [ ] Undo works.
- [ ] Redo works.
- [ ] Preview works.
- [ ] Draft rendering works.
- [ ] Published rendering works.
- [ ] Invalid block data does not break the page.

---

# 22. Page Revision Checklist

- [ ] Published revision is identifiable.
- [ ] Draft revision is identifiable.
- [ ] Previous revisions are preserved according to policy.
- [ ] Revision data is immutable.
- [ ] Restore operation is authorized.
- [ ] Restore operation is logged.
- [ ] Draft content cannot unintentionally become public.
- [ ] Revision access is protected.
- [ ] Deleted content does not unexpectedly reappear.

---

# 23. Blog Checklist

For every blog post:

- [ ] Title validated.
- [ ] Slug validated.
- [ ] Excerpt validated.
- [ ] Content sanitized.
- [ ] Featured image authorized.
- [ ] Category validated.
- [ ] Tags validated.
- [ ] Author information controlled.
- [ ] Draft status protected.
- [ ] Publish action protected.
- [ ] Delete action protected.
- [ ] SEO metadata validated.
- [ ] Schema generated safely.
- [ ] Canonical URL validated.
- [ ] Related posts references validated.
- [ ] Preview cannot bypass authorization.

---

# 24. SEO Checklist

For every indexable page:

- [ ] SEO title defined.
- [ ] Meta description defined.
- [ ] Canonical URL defined.
- [ ] Robots behavior defined.
- [ ] Open Graph metadata defined.
- [ ] Social metadata defined.
- [ ] Structured data defined where appropriate.
- [ ] JSON-LD is valid.
- [ ] JSON-LD cannot inject executable HTML/JS.
- [ ] Breadcrumbs are valid where used.
- [ ] Sitemap behavior is correct.
- [ ] Robots behavior is correct.
- [ ] Draft pages are not unintentionally indexed.
- [ ] Admin pages are not indexed.
- [ ] Private pages are not indexed.
- [ ] Duplicate URLs are controlled.

---

# 25. JSON-LD Checklist

- [ ] Schema type is approved.
- [ ] Schema properties are validated.
- [ ] URLs are validated.
- [ ] Text values are safely serialized.
- [ ] User-controlled values cannot break the JSON structure.
- [ ] `<script>` context is safely handled.
- [ ] Invalid schema does not break page rendering.
- [ ] Schema output is tested.

---

# 26. Database Checklist

- [ ] Prisma schema matches documented architecture.
- [ ] Database migrations are version-controlled.
- [ ] Foreign keys are defined where appropriate.
- [ ] Unique constraints exist where required.
- [ ] Required fields are enforced.
- [ ] Sensitive fields are minimized.
- [ ] Password hashes are protected.
- [ ] Session records are protected.
- [ ] Audit records are protected.
- [ ] Database credentials are not committed.
- [ ] Database access is restricted.
- [ ] Production database is not directly exposed unnecessarily.
- [ ] Backup policy exists.
- [ ] Restore procedure is documented.
- [ ] Destructive migrations are reviewed.

---

# 27. Data Lifecycle Checklist

For each important data type:

- [ ] Storage location documented.
- [ ] Purpose documented.
- [ ] Access rules documented.
- [ ] Retention period documented.
- [ ] Deletion condition documented.
- [ ] Backup behavior documented.
- [ ] Recovery behavior documented.
- [ ] Sensitive information identified.
- [ ] Unnecessary data collection removed.

For public processing files:

- [ ] Server storage is not used for normal processing.
- [ ] No persistent server record exists.
- [ ] Browser-side temporary data has a defined lifecycle.

---

# 28. Logging Checklist

- [ ] Authentication events are logged appropriately.
- [ ] Authorization failures are logged appropriately.
- [ ] Security-sensitive actions are logged.
- [ ] Admin actions are logged.
- [ ] Important file-management actions are logged.
- [ ] Logs contain enough context for investigation.
- [ ] Logs do not contain passwords.
- [ ] Logs do not contain session tokens.
- [ ] Logs do not contain API secrets.
- [ ] Logs do not contain complete uploaded file contents.
- [ ] Logs do not unnecessarily contain sensitive personal information.
- [ ] User-controlled log values cannot inject misleading log entries.
- [ ] Log access is protected.
- [ ] Log retention is defined.

Security logging should record meaningful events without turning logs into a secondary source of sensitive data. OWASP's logging guidance specifically includes upload validation and file lifecycle events as useful security events.

---

# 29. Secrets Checklist

- [ ] No API keys in source code.
- [ ] No passwords in source code.
- [ ] No private keys in source code.
- [ ] No production credentials in Git.
- [ ] `.env` files are excluded where appropriate.
- [ ] Example environment files contain placeholders only.
- [ ] Client-side environment variables contain only intentionally public values.
- [ ] Server-only secrets remain server-side.
- [ ] Build output does not contain secrets.
- [ ] Logs do not expose secrets.
- [ ] Error messages do not expose secrets.
- [ ] Git history has been checked when a secret leak is suspected.

---

# 30. Dependency Checklist

Before adding a dependency:

- [ ] Dependency is actually necessary.
- [ ] Existing project functionality cannot solve the problem.
- [ ] Package is actively maintained or justified.
- [ ] License is acceptable.
- [ ] Security history has been reviewed.
- [ ] Dependency size is acceptable.
- [ ] Browser/server compatibility is known.
- [ ] Dependency does not unnecessarily increase attack surface.
- [ ] Version is pinned/controlled appropriately.
- [ ] Lockfile is updated.
- [ ] Existing tests pass after installation.

---

# 31. Dependency Security

- [ ] Dependency vulnerabilities are checked.
- [ ] Vulnerable dependencies are evaluated.
- [ ] Critical vulnerabilities are not ignored.
- [ ] Security patches are applied where appropriate.
- [ ] Transitive dependencies are considered.
- [ ] Dependency updates are tested.
- [ ] Unexpected dependency behavior is investigated.

---

# 32. Error Handling Checklist

For every user-facing error:

- [ ] Error is understandable.
- [ ] Error does not expose stack trace.
- [ ] Error does not expose filesystem path.
- [ ] Error does not expose SQL.
- [ ] Error does not expose environment variables.
- [ ] Error does not expose secrets.
- [ ] Error does not expose internal architecture.
- [ ] Error provides an appropriate next action where useful.
- [ ] Error state is testable.
- [ ] Unexpected errors are logged safely.

---

# 33. Rate Limiting Checklist

For every abuse-sensitive endpoint:

- [ ] Rate limit requirement evaluated.
- [ ] Authentication endpoint protected.
- [ ] Password reset endpoint protected.
- [ ] Admin-sensitive operations protected.
- [ ] API endpoints protected where required.
- [ ] Expensive operations protected.
- [ ] Rate-limit response is safe.
- [ ] Rate limiting cannot be trivially bypassed.
- [ ] Rate limits do not block legitimate normal use unnecessarily.

---

# 34. DoS / Resource Exhaustion Checklist

- [ ] Request size limits exist.
- [ ] File size limits exist.
- [ ] Batch limits exist.
- [ ] Processing complexity limits exist.
- [ ] Pagination limits exist.
- [ ] Search limits exist.
- [ ] Database query limits are considered.
- [ ] Image dimension limits exist where necessary.
- [ ] ZIP/decompression risks are considered.
- [ ] Recursive processing is bounded.
- [ ] Expensive endpoints are rate-limited.
- [ ] Browser processing cannot intentionally consume unlimited memory.

---

# 35. Compression / Archive Checklist

If archive processing is implemented:

- [ ] Archive formats are explicitly supported.
- [ ] Archive size limits exist.
- [ ] Extracted size limits exist.
- [ ] File-count limits exist.
- [ ] Path traversal is prevented.
- [ ] Nested archive depth is limited.
- [ ] Symlink behavior is controlled.
- [ ] Suspicious file types are handled.
- [ ] Processing cannot exhaust available resources.

---

# 36. Download Checklist

For generated downloads:

- [ ] Correct filename generated.
- [ ] Filename is safe.
- [ ] Content type is correct.
- [ ] Content-Disposition behavior is intentional.
- [ ] Generated content is validated.
- [ ] Output is not corrupted.
- [ ] Download does not expose internal paths.
- [ ] Download does not expose other users' data.
- [ ] Object URLs are revoked after use where applicable.
- [ ] Downloaded content does not contain unexpected active content.

---

# 37. Tool-Specific Checklist

Every tool must have its own checklist.

## Tool Definition

- [ ] Tool ID defined.
- [ ] Tool name defined.
- [ ] Tool category defined.
- [ ] Supported input formats defined.
- [ ] Supported output formats defined.
- [ ] Options defined.
- [ ] Limits defined.
- [ ] Browser support defined.
- [ ] Unsupported cases defined.
- [ ] Error states defined.
- [ ] Processing engine defined.

## Tool Implementation

- [ ] UI implemented.
- [ ] Validation implemented.
- [ ] Processing implemented.
- [ ] Progress implemented where useful.
- [ ] Cancellation implemented where useful.
- [ ] Result validation implemented.
- [ ] Download implemented.
- [ ] Cleanup implemented.

## Tool Testing

- [ ] Happy path tested.
- [ ] Invalid input tested.
- [ ] Unsupported format tested.
- [ ] Empty file tested.
- [ ] Large file tested.
- [ ] Malformed file tested.
- [ ] Multiple files tested where applicable.
- [ ] Cancel tested.
- [ ] Failure tested.
- [ ] Browser compatibility tested.
- [ ] Memory behavior tested.
- [ ] Security reviewed.
- [ ] Performance reviewed.
- [ ] Documentation written.

---

# 38. Tool Approval Checklist

A tool can only become `APPROVED` when:

- [PASS] Implementation complete
- [PASS] Unit tests
- [PASS] Integration tests
- [PASS] E2E tests
- [PASS] Security review
- [PASS] Performance review
- [PASS] Browser compatibility review
- [PASS] Documentation
- [PASS] Regression tests

Then:

- [ ] Tool marked `APPROVED`.

If any required test fails:

**Tool remains unapproved.**

---

# 39. Testing Checklist

For every feature:

- [ ] Unit tests written.
- [ ] Integration tests written.
- [ ] E2E tests written where applicable.
- [ ] Security tests written.
- [ ] Edge cases tested.
- [ ] Error cases tested.
- [ ] Regression tests added.
- [ ] Browser compatibility tested.
- [ ] Performance tested where relevant.
- [ ] Accessibility tested where relevant.

Testing must validate actual behavior, not merely implementation existence.

---

# 40. Accessibility Checklist

Target: **WCAG 2.2 AA**

- [ ] Keyboard navigation works.
- [ ] Focus state is visible.
- [ ] Focus order is logical.
- [ ] Interactive controls have accessible names.
- [ ] Form fields have labels.
- [ ] Error messages are understandable.
- [ ] Color is not the only source of information.
- [ ] Contrast is acceptable.
- [ ] Images have appropriate alternative text.
- [ ] Decorative images are correctly treated.
- [ ] Modal dialogs are accessible.
- [ ] Dropdowns are accessible.
- [ ] Tool processing states are announced appropriately.
- [ ] Download results are understandable.
- [ ] Reduced-motion preference is respected.

---

# 41. UI/UX Checklist

- [ ] Main action is immediately understandable.
- [ ] Tool page requires minimal interaction.
- [ ] Drag-and-drop works where appropriate.
- [ ] File picker works.
- [ ] Empty state is clear.
- [ ] Processing state is clear.
- [ ] Success state is clear.
- [ ] Error state is clear.
- [ ] Download action is obvious.
- [ ] Mobile layout works.
- [ ] Desktop layout works.
- [ ] Tablet layout works.
- [ ] Light mode works.
- [ ] Dark mode works.
- [ ] System theme behavior works.
- [ ] Animations do not block functionality.
- [ ] Animations do not create excessive CPU usage.
- [ ] Reduced-motion behavior works.

---

# 42. Performance Checklist

- [ ] Initial page load is acceptable.
- [ ] JavaScript is code-split appropriately.
- [ ] Heavy processing libraries are lazy-loaded where possible.
- [ ] Workers are used where beneficial.
- [ ] Images are optimized.
- [ ] Unnecessary dependencies are removed.
- [ ] Unnecessary requests are removed.
- [ ] Large assets are not loaded unnecessarily.
- [ ] CMS pages do not generate excessive queries.
- [ ] Database queries are reviewed.
- [ ] Core Web Vitals are reviewed.
- [ ] Tool processing performance is measured.

---

# 43. Browser Compatibility Checklist

Required browsers:

- [ ] Chrome
- [ ] Firefox
- [ ] Safari
- [ ] Edge

For each relevant tool:

- [ ] File API support verified.
- [ ] Worker support verified.
- [ ] Web APIs required by the tool verified.
- [ ] Download behavior verified.
- [ ] Large-file behavior verified.
- [ ] Unsupported browser behavior is graceful.

---

# 44. CMS Checklist

- [ ] Admin authentication works.
- [ ] Admin authorization works.
- [ ] Dashboard works.
- [ ] Pages can be created.
- [ ] Pages can be edited.
- [ ] Pages can be duplicated.
- [ ] Pages can be published.
- [ ] Pages can be unpublished where supported.
- [ ] Blocks can be created.
- [ ] Blocks can be edited.
- [ ] Blocks can be duplicated.
- [ ] Blocks can be copied.
- [ ] Blocks can be pasted.
- [ ] Blocks can be reordered.
- [ ] Blocks can be deleted.
- [ ] Revisions work.
- [ ] Media Library works.
- [ ] Blog works.
- [ ] SEO works.
- [ ] Audit logging works.

---

# 45. Admin Security Checklist

- [ ] Admin routes protected server-side.
- [ ] Admin APIs protected server-side.
- [ ] Admin pages cannot be accessed by unauthenticated users.
- [ ] Normal users cannot access admin functionality.
- [ ] Admin actions require appropriate permissions.
- [ ] Sensitive actions are logged.
- [ ] Password operations are protected.
- [ ] Session handling is secure.
- [ ] Admin errors do not leak internal information.

---

# 46. Audit Log Checklist

- [ ] Login events recorded where appropriate.
- [ ] Failed authentication events recorded.
- [ ] Authorization failures recorded.
- [ ] Page publication recorded.
- [ ] Page deletion recorded.
- [ ] Media changes recorded.
- [ ] Blog publication recorded.
- [ ] Role/permission changes recorded.
- [ ] Security-sensitive settings changes recorded.
- [ ] Logs cannot be modified by ordinary users.
- [ ] Logs do not contain secrets.

---

# 47. Documentation Checklist

Before approval:

- [ ] Feature documentation exists.
- [ ] Architecture documentation updated.
- [ ] Security documentation updated.
- [ ] Testing documentation updated.
- [ ] API documentation updated if applicable.
- [ ] Tool specification updated if applicable.
- [ ] Known limitations documented.
- [ ] Browser limitations documented.
- [ ] Configuration documented.
- [ ] Data lifecycle documented if relevant.

---

# 48. Code Quality Checklist

- [ ] TypeScript types are explicit where needed.
- [ ] No unnecessary `any`.
- [ ] No dead code.
- [ ] No duplicated logic.
- [ ] No unused dependencies.
- [ ] No unused imports.
- [ ] No unexplained security bypass.
- [ ] No commented-out abandoned implementation.
- [ ] Naming is consistent.
- [ ] Module boundaries are respected.
- [ ] Business logic is separated appropriately.
- [ ] Security-sensitive logic is centralized where practical.
- [ ] Error handling is consistent.

---

# 49. Git Checklist

Before commit:

- [ ] Only intended files changed.
- [ ] No secrets included.
- [ ] No temporary files included.
- [ ] No generated junk included.
- [ ] No debug code included.
- [ ] Tests pass.
- [ ] Lint passes.
- [ ] Type checking passes.
- [ ] Documentation changes included where required.
- [ ] Commit is logically scoped.

---

# 50. CI Checklist

CI should verify where applicable:

- [ ] Install succeeds.
- [ ] Type checking succeeds.
- [ ] Lint succeeds.
- [ ] Unit tests pass.
- [ ] Integration tests pass.
- [ ] E2E tests pass.
- [ ] Security checks pass.
- [ ] Build succeeds.
- [ ] No secrets are detected.
- [ ] Required documentation exists.

---

# 51. Pre-Deployment Checklist

- [ ] Production environment variables configured.
- [ ] Secrets configured securely.
- [ ] Database configured.
- [ ] Database migration reviewed.
- [ ] Backup verified.
- [ ] Build passes.
- [ ] Tests pass.
- [ ] Security review passes.
- [ ] Authentication tested.
- [ ] Authorization tested.
- [ ] Public tools tested.
- [ ] File privacy tested.
- [ ] CMS tested.
- [ ] SEO tested.
- [ ] Error handling tested.
- [ ] Monitoring available.
- [ ] Rollback procedure known.

---

# 52. Production Smoke Test

Immediately after deployment:

- [ ] Homepage loads.
- [ ] Tool pages load.
- [ ] File picker works.
- [ ] Browser processing works.
- [ ] No processing file is uploaded.
- [ ] Download works.
- [ ] Authentication works.
- [ ] Logout works.
- [ ] Admin dashboard works.
- [ ] CMS pages load.
- [ ] Blog loads.
- [ ] Sitemap loads.
- [ ] Robots configuration works.
- [ ] No critical browser errors occur.
- [ ] No critical server errors occur.

---

# 53. Regression Checklist

After every significant change:

- [ ] Existing tools still work.
- [ ] Existing pages still work.
- [ ] Existing CMS functionality still works.
- [ ] Authentication still works.
- [ ] Authorization still works.
- [ ] Public processing remains browser-only.
- [ ] No unexpected network upload was introduced.
- [ ] Existing security controls remain active.
- [ ] Existing tests pass.
- [ ] New tests cover the changed behavior.

---

# 54. Security Regression Checklist

After security-related changes:

- [ ] Authentication tested.
- [ ] Authorization tested.
- [ ] Session handling tested.
- [ ] CSRF tested.
- [ ] XSS tested.
- [ ] Input validation tested.
- [ ] File validation tested.
- [ ] Path traversal tested.
- [ ] IDOR tested.
- [ ] Rate limiting tested.
- [ ] Logging tested.
- [ ] Error handling tested.
- [ ] Existing security tests pass.

---

# 55. Final Definition of Done

A feature is **DONE** only when all applicable requirements below are satisfied.

## Product

- [ ] Requirement implemented.
- [ ] Requirement behaves as documented.
- [ ] No unnecessary functionality added.

## Architecture

- [ ] Correct module used.
- [ ] Architecture respected.
- [ ] No unauthorized architecture change.

## Security

- [ ] Security review completed.
- [ ] Relevant attack paths reviewed.
- [ ] Required controls implemented.
- [ ] Security tests pass.

## Privacy

- [ ] Privacy requirements verified.
- [ ] Public processing remains browser-first.
- [ ] No accidental upload detected.

## Testing

- [ ] Unit tests pass.
- [ ] Integration tests pass.
- [ ] E2E tests pass where applicable.
- [ ] Regression tests pass.
- [ ] Performance checks pass where applicable.

## UX

- [ ] Desktop works.
- [ ] Mobile works.
- [ ] Light mode works.
- [ ] Dark mode works.
- [ ] Accessibility reviewed.
- [ ] Reduced motion reviewed.

## Documentation

- [ ] Relevant documentation updated.
- [ ] Known limitations documented.
- [ ] Test results documented.

Only after all applicable requirements pass:

**Status = APPROVED**

---

# 56. Mandatory AI Work Report

Every significant implementation task must end with a report containing:

```text
Task:
[task name]

Roadmap Phase:
[phase]

Changed:
[list]

Added:
[list]

Removed:
[list]

Security Changes:
[list]

Privacy Changes:
[list]

Tests Run:
[list]

Tests Passed:
[list]

Tests Failed:
[list]

Known Issues:
[list]

Documentation Updated:
[list]

Regression Status:
PASS / FAIL

Security Status:
PASS / FAIL

Approval Status:
APPROVED / NOT APPROVED
```

The report must never claim a test passed if it was not actually executed.

---

# 57. Mandatory Stop Conditions

Implementation must stop immediately when:

- [ ] Required documentation is missing.
- [ ] Requirements are ambiguous and cannot be resolved from project documents.
- [ ] Roadmap scope would be exceeded.
- [ ] Security boundary would be weakened.
- [ ] Public user files would unexpectedly reach the server.
- [ ] Authentication could be bypassed.
- [ ] Authorization could be bypassed.
- [ ] A critical security vulnerability is discovered.
- [ ] Required tests fail.
- [ ] Regression is detected.
- [ ] Production data could be endangered.
- [ ] Secrets are exposed.
- [ ] A destructive migration is unsafe.
- [ ] Architecture changes without approval are required.

Do not continue by silently bypassing the problem.

---

# 58. Release Approval Checklist

Before a production release:

- [ ] All roadmap-required features are complete.
- [ ] All approved tools are tested.
- [ ] All critical security findings are resolved.
- [ ] No known critical regression exists.
- [ ] Privacy invariant verified.
- [ ] Authentication verified.
- [ ] Authorization verified.
- [ ] Database verified.
- [ ] Backups verified.
- [ ] Deployment verified.
- [ ] Rollback procedure verified.
- [ ] Documentation complete.
- [ ] Final QA complete.

Final status:

```text
RELEASE STATUS:
APPROVED / NOT APPROVED
```

---

# 59. Security Priority

When requirements conflict, use this priority:

1. User safety
2. Privacy
3. Security
4. Data integrity
5. Correctness
6. Reliability
7. Accessibility
8. Performance
9. Usability
10. Visual polish

Visual quality must never override security, privacy, correctness, or accessibility.

---

# 60. Final Project Rule

No feature is approved because it:

- looks good,
- compiles,
- runs locally,
- passes one test,
- works in one browser,
- works with one file,
- or appears functional.

A feature is approved only when it satisfies the complete applicable checklist and its required tests have actually passed.

The project process remains:

**Understand → Plan → Implement → Test → Review → Fix → Retest → Document → Approve**

No shortcut.

---

# 61. External Security Baseline

This checklist should be periodically compared against current security standards and guidance, especially OWASP ASVS and OWASP guidance for input validation, file handling, authentication, API security, and browser security.

The OWASP ASVS is explicitly intended as a basis for testing web-application security controls and providing secure-development requirements.

For file handling, the project must continue to use defense-in-depth rather than relying on a single file extension or MIME-type check.

---

# 62. Checklist Ownership

This document applies to:

- Developers
- AI coding agents
- Reviewers
- QA
- Security review
- Deployment
- Future maintainers

No implementation agent may ignore this checklist because a task appears small.

Small changes can introduce:

- security regressions,
- privacy regressions,
- authorization failures,
- broken processing,
- data corruption,
- performance regressions,
- accessibility regressions.

Therefore the checklist remains mandatory throughout the entire project lifecycle.

---

# 63. Final Invariant

The following statements must remain true throughout the project:

```text
Public file processing is browser-first.

Public processing files are not uploaded to the server merely to perform processing.

Public users do not need an account merely to use public tools.

Authentication is never authorization.

Authorization is always enforced server-side.

Passwords are never stored in plaintext.

Secrets are never committed to source control.

User-controlled input is never inherently trusted.

Uploaded files are never inherently trusted.

SVG is treated as potentially active content.

Client-side validation is never treated as the only security boundary where server validation applies.

A hidden UI control is not a security control.

A passing build is not a passing security review.

A passing test is not automatically an approval.

A feature with failing required tests is not approved.

A feature outside the roadmap is not implemented.

Every significant system is documented.

Every approved tool has independent testing evidence.

Security and privacy are invariants, not optional features.
```

**End of `checklist.md`**
