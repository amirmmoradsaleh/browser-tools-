# Testing Strategy & Quality Assurance

**Document Version:** 1.0  
**Status:** Mandatory  
**Applies To:** Entire Project  
**Authority:** `PROJECT_RULES.md`

---

# 1. Purpose

This document defines the complete testing strategy for the project.

Testing is not a final phase performed only before deployment.

Testing must occur throughout the lifecycle:

```text
Requirements
    ↓
Design
    ↓
Implementation
    ↓
Unit Testing
    ↓
Integration Testing
    ↓
Security Testing
    ↓
E2E Testing
    ↓
Performance Testing
    ↓
Accessibility Testing
    ↓
Regression Testing
    ↓
Production Verification
```

The project must use multiple testing techniques.

No single test type is sufficient to establish correctness or security.

OWASP's testing guidance explicitly treats security testing as a systematic process for validating the effectiveness of application security controls rather than relying on one technique or one final penetration test.

---

# 2. Testing Principles

The project follows these rules:

1. Test behavior, not implementation details.
2. Test before declaring a feature complete.
3. Test negative cases, not only successful cases.
4. Test security boundaries explicitly.
5. Test privacy boundaries explicitly.
6. Test failure behavior.
7. Test edge cases.
8. Test browser compatibility.
9. Test performance for expensive operations.
10. Test accessibility.
11. Test regressions after changes.
12. Document test results.
13. Never claim a test passed unless it actually ran.
14. Never hide failed tests.
15. Never skip required tests because a feature appears simple.

---

# 3. Testing Status Model

Every feature and tool uses the following lifecycle:

```text
PLANNED
    ↓
IMPLEMENTED
    ↓
UNIT TESTED
    ↓
INTEGRATION TESTED
    ↓
E2E TESTED
    ↓
SECURITY CHECKED
    ↓
PERFORMANCE CHECKED
    ↓
ACCESSIBILITY CHECKED
    ↓
DOCUMENTED
    ↓
APPROVED
```

A feature may not skip mandatory stages.

If a stage is not applicable:

```text
N/A
```

must be explicitly documented with a reason.

---

# 4. Test Result States

Every test case must have one status:

```text
NOT RUN
PASS
FAIL
BLOCKED
SKIPPED
N/A
```

Definitions:

### NOT RUN

The test has not been executed.

### PASS

The test was executed and the expected result occurred.

### FAIL

The test was executed and the expected result did not occur.

### BLOCKED

The test cannot currently be executed because of an external dependency or environment problem.

### SKIPPED

The test was intentionally skipped and requires documented justification.

### N/A

The test genuinely does not apply.

---

# 5. Test Evidence

For important tests, record sufficient evidence to reproduce the result.

Evidence may include:

- Test output
- Screenshot
- Browser console output
- Network inspection
- HTTP response
- Database state
- Automated test report
- Performance measurement
- Security scanner result
- Manual verification notes

Evidence must not contain:

- Passwords
- Session tokens
- API secrets
- Private keys
- Sensitive user data
- Complete private files unnecessarily

---

# 6. Test Environment

Testing should use separate environments where appropriate:

```text
Development
    ↓
Testing / CI
    ↓
Staging
    ↓
Production
```

Production data must not be used casually for development or testing.

Test data must be synthetic whenever possible.

---

# 7. Test Categories

The project uses:

1. Static analysis
2. Type checking
3. Linting
4. Unit tests
5. Component tests
6. Integration tests
7. API tests
8. Database tests
9. File-processing tests
10. Security tests
11. Privacy tests
12. E2E tests
13. Browser compatibility tests
14. Accessibility tests
15. Performance tests
16. Regression tests
17. Deployment tests
18. Production smoke tests
19. Recovery tests

---

# 8. Static Analysis

Before approval:

- [ ] TypeScript type checking passes.
- [ ] ESLint passes.
- [ ] Formatting checks pass.
- [ ] No unexpected compiler warnings exist.
- [ ] No unused imports exist.
- [ ] No dead code exists where detectable.
- [ ] No obvious unsafe APIs are used.
- [ ] No secrets are detected.
- [ ] No debug code remains.

---

# 9. Type Safety

The project uses TypeScript.

Tests must verify:

- [ ] Application compiles.
- [ ] Type errors are resolved.
- [ ] Tool configuration is type-safe.
- [ ] Tool registry is type-safe.
- [ ] Block definitions are type-safe.
- [ ] API request types are validated.
- [ ] Database access types are correct.
- [ ] Processing results are typed.
- [ ] Error states are represented.
- [ ] Nullable values are handled intentionally.

Avoid using:

```typescript
any
```

unless explicitly justified.

---

# 10. Unit Testing

Unit tests verify isolated logic.

Examples:

- File validation
- Format detection
- Size validation
- Tool configuration
- Processing options
- Filename generation
- Metadata handling
- Image dimension calculations
- PDF page calculations
- Batch processing logic
- Progress calculations
- Cancellation state
- Error classification
- SEO validation
- Schema generation
- Slug generation
- Permission checks
- Authentication utilities
- Data transformation
- Page-builder block validation

---

# 11. Unit Test Requirements

A unit-tested function should cover:

### Normal case

```text
Valid input → expected output
```

### Boundary case

```text
Minimum input
Maximum input
```

### Invalid case

```text
Invalid input → controlled error
```

### Unexpected case

```text
Malformed or unusual input → controlled behavior
```

### Security case

```text
Malicious input → rejected or safely handled
```

---

# 12. Unit Test Example Structure

Each test should communicate:

```text
Given
When
Then
```

Example:

```text
Given a valid PNG file
When the converter validates the file
Then validation succeeds
```

Another:

```text
Given an unsupported executable file
When the image tool validates the file
Then validation fails safely
```

---

# 13. Component Testing

UI components should be tested for behavior.

Examples:

- Upload/drop zone
- File picker
- Tool configuration panel
- Progress indicator
- Error state
- Success state
- Download button
- Theme switcher
- Modal
- Dropdown
- Page-builder block
- Media selector
- Blog editor
- SEO editor

Test:

- [ ] Rendering
- [ ] User interaction
- [ ] Keyboard interaction
- [ ] Disabled state
- [ ] Loading state
- [ ] Error state
- [ ] Empty state
- [ ] Accessibility behavior

---

# 14. File Processing Testing

File processing is a core product capability.

Every processing tool must have dedicated tests.

Test categories:

```text
Valid file
Invalid file
Unsupported file
Empty file
Very small file
Large file
Malformed file
Multiple files
Duplicate files
Unusual filename
Unicode filename
Special characters
Maximum supported dimensions
Minimum supported dimensions
Cancelled processing
Failed processing
Successful processing
```

---

# 15. File Format Testing

For every supported format:

- [ ] Valid sample exists.
- [ ] Minimal valid sample exists where practical.
- [ ] Representative real-world sample exists.
- [ ] Malformed sample exists.
- [ ] Unsupported sample exists.
- [ ] Output can be opened by an independent compatible application where practical.
- [ ] Output metadata is correct.
- [ ] Output extension is correct.
- [ ] Output MIME type is correct.
- [ ] Output content is correct.

---

# 16. Image Tool Testing

For image tools test:

### Input

- PNG
- JPEG/JPG
- WebP
- GIF where supported
- SVG where supported
- Other explicitly supported formats

### Conditions

- Small image
- Large image
- High-resolution image
- Transparent image
- Image without transparency
- Different aspect ratios
- Very wide image
- Very tall image
- Corrupted image
- Invalid image

---

# 17. Image Converter Tests

For each conversion:

```text
Input
→ Validation
→ Conversion
→ Output validation
→ Download
```

Verify:

- [ ] Correct dimensions.
- [ ] Correct format.
- [ ] Correct transparency behavior.
- [ ] Correct quality behavior.
- [ ] Correct filename.
- [ ] Output is readable.
- [ ] Output is not unexpectedly corrupted.

---

# 18. Image Compressor Tests

Verify:

- [ ] Compression actually occurs.
- [ ] Output remains valid.
- [ ] Quality setting works.
- [ ] Size target behavior works where supported.
- [ ] Extremely large files are handled safely.
- [ ] Compression failure is handled.
- [ ] Result does not unexpectedly increase size without explanation.
- [ ] Memory is released.

---

# 19. Image Resizer Tests

Test:

- [ ] Width only.
- [ ] Height only.
- [ ] Width + height.
- [ ] Maintain aspect ratio.
- [ ] Do not maintain aspect ratio where supported.
- [ ] Upscaling.
- [ ] Downscaling.
- [ ] Minimum dimensions.
- [ ] Maximum dimensions.
- [ ] Invalid dimensions.
- [ ] Zero dimensions.
- [ ] Negative dimensions.
- [ ] Extremely large dimensions.

---

# 20. Image Crop Tests

Test:

- [ ] Manual crop.
- [ ] Preset aspect ratio.
- [ ] Crop within bounds.
- [ ] Crop at edges.
- [ ] Invalid crop coordinates.
- [ ] Very small crop.
- [ ] Full-image crop.
- [ ] Large image crop.
- [ ] Output correctness.

---

# 21. Metadata Removal Tests

Verify:

- [ ] Metadata detection works.
- [ ] Metadata removal works.
- [ ] Image remains valid.
- [ ] Visual content remains intact.
- [ ] Supported metadata types are documented.
- [ ] Unsupported metadata behavior is documented.

---

# 22. Base64 Tool Testing

For Image → Base64:

- [ ] Valid image succeeds.
- [ ] Invalid image fails safely.
- [ ] MIME type is correct.
- [ ] Output is complete.
- [ ] Large image behavior is bounded.

For Base64 → Image:

- [ ] Valid Base64 succeeds.
- [ ] Invalid Base64 fails safely.
- [ ] Invalid MIME type is rejected.
- [ ] Excessive input is rejected.
- [ ] Generated file is valid.

---

# 23. Batch Processing Tests

Test:

- [ ] One file.
- [ ] Multiple files.
- [ ] Maximum allowed files.
- [ ] More than maximum files.
- [ ] Mixed valid/invalid files.
- [ ] Duplicate files.
- [ ] Different formats.
- [ ] One processing failure among successful files.
- [ ] Cancellation.
- [ ] ZIP generation where supported.
- [ ] Individual downloads.
- [ ] Memory cleanup.

---

# 24. PDF Testing

Every PDF tool must have PDF-specific tests.

Test:

- [ ] One-page PDF.
- [ ] Multi-page PDF.
- [ ] Large PDF.
- [ ] Password-protected PDF where applicable.
- [ ] Corrupted PDF.
- [ ] PDF with images.
- [ ] PDF with text.
- [ ] PDF with unusual page sizes.
- [ ] PDF with rotated pages.
- [ ] Invalid page numbers.
- [ ] Empty page selection.
- [ ] Excessive page selection.

---

# 25. PDF Merge Testing

Test:

- [ ] Two PDFs.
- [ ] Multiple PDFs.
- [ ] Maximum allowed PDFs.
- [ ] Different page sizes.
- [ ] Different orientations.
- [ ] Large PDFs.
- [ ] Corrupted input.
- [ ] Mixed valid/invalid input.
- [ ] Output page order.
- [ ] Output readability.

---

# 26. PDF Split Testing

Test:

- [ ] Single-page extraction.
- [ ] Multiple-page extraction.
- [ ] First page.
- [ ] Last page.
- [ ] Middle pages.
- [ ] Invalid page range.
- [ ] Duplicate page selection.
- [ ] Entire document.
- [ ] Output correctness.

---

# 27. PDF Compression Testing

Verify:

- [ ] Valid PDF remains valid.
- [ ] Output size is measured.
- [ ] Compression options work.
- [ ] Quality tradeoff is documented.
- [ ] Large PDF does not crash processing.
- [ ] Failed compression is handled.
- [ ] Output can be opened.

---

# 28. PDF to Image Testing

Test:

- [ ] One page.
- [ ] Multiple pages.
- [ ] Large page.
- [ ] Different page orientations.
- [ ] Different output formats.
- [ ] Resolution options.
- [ ] Invalid PDF.
- [ ] Memory usage.
- [ ] Batch output.

---

# 29. Image to PDF Testing

Test:

- [ ] One image.
- [ ] Multiple images.
- [ ] Different dimensions.
- [ ] Different aspect ratios.
- [ ] Different image formats.
- [ ] Large images.
- [ ] Page ordering.
- [ ] Output page dimensions.
- [ ] Output readability.

---

# 30. Tool Registry Testing

The tool registry is a core architectural system.

Verify:

- [ ] Every tool has unique ID.
- [ ] Every tool has valid metadata.
- [ ] Unsupported tool IDs are rejected.
- [ ] Tool categories are valid.
- [ ] Input formats are valid.
- [ ] Output formats are valid.
- [ ] Tool configuration is validated.
- [ ] Tool cannot execute an unregistered processor.
- [ ] Disabled tools cannot execute.
- [ ] Tool documentation exists.

---

# 31. Processing Engine Testing

Test the processing lifecycle:

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

And failure paths:

```text
VALIDATING → FAILED
READY → FAILED
PROCESSING → FAILED
PROCESSING → CANCELLED
```

Verify:

- [ ] Invalid transitions are prevented.
- [ ] Cancellation works.
- [ ] Failure state is recoverable.
- [ ] Completion state contains valid output.
- [ ] Resources are cleaned up.
- [ ] UI reflects state correctly.

---

# 32. Worker Testing

For Web Workers:

- [ ] Worker starts.
- [ ] Worker receives valid input.
- [ ] Worker rejects invalid messages.
- [ ] Worker returns valid result.
- [ ] Worker handles errors.
- [ ] Worker can be cancelled/terminated.
- [ ] Worker does not leak memory.
- [ ] Worker cannot access unnecessary application state.
- [ ] Worker communication is type-safe.

---

# 33. Cancellation Testing

Every cancellable operation must test:

```text
Cancel immediately
Cancel during processing
Cancel near completion
Cancel after error
Cancel multiple times
```

Verify:

- [ ] Processing stops.
- [ ] UI returns to safe state.
- [ ] Partial output is not presented as complete.
- [ ] Resources are released.
- [ ] Worker is terminated where applicable.
- [ ] No hidden background operation continues.

---

# 34. Download Testing

Test:

- [ ] Correct output.
- [ ] Correct filename.
- [ ] Correct extension.
- [ ] Correct MIME type.
- [ ] Single download.
- [ ] Multiple downloads.
- [ ] ZIP download where supported.
- [ ] Large output.
- [ ] Failed output.
- [ ] Cancelled output.

---

# 35. Privacy Testing

Privacy testing is mandatory for the core product.

For every public tool:

1. Open browser developer tools.
2. Open Network panel.
3. Select a test file.
4. Run processing.
5. Inspect requests.
6. Inspect request payloads.
7. Inspect request URLs.
8. Inspect third-party requests.
9. Inspect error behavior.
10. Repeat with invalid input.

Verify:

- [ ] File bytes are never uploaded.
- [ ] File contents are not sent to analytics.
- [ ] File contents are not sent to error tracking.
- [ ] File contents are not sent to third-party APIs.
- [ ] File metadata is not unnecessarily transmitted.
- [ ] Failed processing does not upload the file.
- [ ] Cancelled processing does not upload the file.

This test is a release blocker.

---

# 36. Security Testing

Security testing must include:

- Authentication
- Session management
- Authorization
- Input validation
- Output encoding
- XSS
- CSRF
- SQL injection
- IDOR
- Path traversal
- SSRF
- Open redirect
- File upload
- SVG
- Resource exhaustion
- Rate limiting
- Secrets
- Error handling
- Logging
- Configuration
- Dependency security

OWASP WSTG currently organizes web security testing across areas including identity, authentication, authorization, session management, input validation, error handling, business logic and client-side testing.

---

# 37. Authentication Testing

Test:

### Login

- [ ] Correct credentials.
- [ ] Wrong password.
- [ ] Wrong username.
- [ ] Empty credentials.
- [ ] Malformed credentials.
- [ ] Repeated failed attempts.
- [ ] Rate limit.
- [ ] Account protection.
- [ ] Generic failure behavior.

### Logout

- [ ] Logout succeeds.
- [ ] Session becomes invalid.
- [ ] Protected pages cannot be accessed using old session.
- [ ] Old session cannot call protected APIs.

### Password Reset

- [ ] Valid reset request.
- [ ] Invalid token.
- [ ] Expired token.
- [ ] Reused token.
- [ ] Invalid user.
- [ ] Rate limit.
- [ ] Session behavior after password change.

---

# 38. Authorization Testing

Create test identities:

```text
Unauthenticated
Normal User
Administrator
```

Then test every protected operation.

Verify:

- [ ] Unauthenticated user is rejected.
- [ ] Normal user cannot access admin functionality.
- [ ] Admin can access allowed functionality.
- [ ] Direct API access is protected.
- [ ] Direct object IDs cannot bypass authorization.
- [ ] Modified request parameters cannot bypass authorization.
- [ ] Modified URLs cannot bypass authorization.

---

# 39. XSS Testing

Test:

- [ ] Stored XSS.
- [ ] Reflected XSS.
- [ ] DOM XSS.
- [ ] Blog content.
- [ ] Page-builder text.
- [ ] SEO fields.
- [ ] Alt text.
- [ ] Filenames.
- [ ] URLs.
- [ ] SVG.
- [ ] Query parameters.

Expected result:

```text
Malicious input is treated as data, not executable code.
```

---

# 40. CSRF Testing

For every state-changing endpoint:

- [ ] Request without required protection is rejected.
- [ ] Cross-origin request is rejected where applicable.
- [ ] Authentication cookies behave correctly.
- [ ] Admin state-changing operations are protected.
- [ ] Password operations are protected.
- [ ] Media operations are protected.

---

# 41. IDOR Testing

For resources with IDs:

```text
Resource A
Resource B
```

Authenticate as a user authorized for A.

Attempt:

```text
Request resource B
```

Verify:

```text
Access denied
```

Test:

- Pages
- Revisions
- Media
- Blog posts
- Categories
- Tags
- Settings
- User records
- Audit records

---

# 42. File Security Testing

Test:

- [ ] Wrong extension.
- [ ] Wrong MIME type.
- [ ] Fake MIME type.
- [ ] Double extension.
- [ ] Malformed file.
- [ ] Oversized file.
- [ ] Dangerous filename.
- [ ] Unicode filename.
- [ ] Path traversal.
- [ ] Malicious SVG.
- [ ] Archive bomb where archives are supported.
- [ ] Recursive archive where applicable.

---

# 43. API Testing

Every API must have tests for:

### Authentication

```text
No session
Invalid session
Expired session
Valid session
```

### Authorization

```text
Insufficient permission
Correct permission
```

### Input

```text
Valid
Invalid
Missing
Too large
Unexpected type
Unexpected field
```

### Output

```text
Correct status
Correct schema
No sensitive information
```

---

# 44. Database Testing

Test:

- [ ] Create.
- [ ] Read.
- [ ] Update.
- [ ] Delete.
- [ ] Unique constraints.
- [ ] Foreign keys.
- [ ] Invalid references.
- [ ] Transaction behavior.
- [ ] Rollback behavior.
- [ ] Concurrent operations.
- [ ] Migration behavior.
- [ ] Sensitive field protection.

---

# 45. CMS Testing

Test:

### Pages

- [ ] Create.
- [ ] Edit.
- [ ] Duplicate.
- [ ] Draft.
- [ ] Publish.
- [ ] Unpublish.
- [ ] Delete.
- [ ] Restore revision.

### Blocks

- [ ] Add.
- [ ] Edit.
- [ ] Duplicate.
- [ ] Copy.
- [ ] Paste.
- [ ] Move.
- [ ] Delete.
- [ ] Undo.
- [ ] Redo.

### Media

- [ ] Upload.
- [ ] Search.
- [ ] Filter.
- [ ] Rename.
- [ ] Delete.
- [ ] Copy URL.
- [ ] Metadata editing.

### Blog

- [ ] Create.
- [ ] Edit.
- [ ] Draft.
- [ ] Publish.
- [ ] Delete.
- [ ] Categories.
- [ ] Tags.
- [ ] Featured image.
- [ ] SEO.
- [ ] Schema.

---

# 46. SEO Testing

Test:

- [ ] Title.
- [ ] Description.
- [ ] Canonical.
- [ ] Robots.
- [ ] Open Graph.
- [ ] Social metadata.
- [ ] JSON-LD.
- [ ] Breadcrumbs.
- [ ] Sitemap.
- [ ] Robots configuration.
- [ ] Draft page indexing.
- [ ] Admin indexing.
- [ ] Duplicate content controls.

---

# 47. Accessibility Testing

Accessibility tests must include:

- [ ] Keyboard-only navigation.
- [ ] Screen-reader-compatible structure.
- [ ] Focus visibility.
- [ ] Focus order.
- [ ] Labels.
- [ ] Buttons.
- [ ] Form errors.
- [ ] Dialogs.
- [ ] Drag-and-drop alternatives.
- [ ] Processing states.
- [ ] Download states.
- [ ] Color contrast.
- [ ] Reduced motion.

Automated accessibility tools may be used, but automated testing does not replace manual accessibility testing.

---

# 48. Responsive Testing

Test at minimum:

```text
Mobile
Tablet
Desktop
Large Desktop
```

Verify:

- [ ] No horizontal overflow.
- [ ] Tool UI remains usable.
- [ ] File picker remains accessible.
- [ ] Drag-and-drop has mobile alternative.
- [ ] Buttons remain accessible.
- [ ] Progress state remains visible.
- [ ] Download action remains visible.
- [ ] CMS remains usable on supported screen sizes.

---

# 49. Theme Testing

Test:

```text
Light
Dark
System Light
System Dark
```

Verify:

- [ ] Text readable.
- [ ] Controls readable.
- [ ] Borders visible.
- [ ] Icons visible.
- [ ] Images remain appropriate.
- [ ] Processing states remain clear.
- [ ] Error/success states remain distinguishable.
- [ ] No flash of incorrect theme where avoidable.

---

# 50. Animation Testing

Verify:

- [ ] Animations do not block interaction.
- [ ] Animations do not delay tool usage unnecessarily.
- [ ] Animations do not cause excessive CPU usage.
- [ ] Animations do not cause layout instability.
- [ ] Reduced-motion preference is respected.
- [ ] Processing animations reflect actual state.
- [ ] Animation failure does not break functionality.

---

# 51. Performance Testing

Performance testing applies to:

- Initial page load
- Tool loading
- File processing
- Large files
- Batch processing
- CMS pages
- Database queries
- Blog pages
- Admin dashboard

Measure where appropriate:

- Load time
- JavaScript size
- Memory usage
- CPU usage
- Processing time
- Database query time
- API response time
- Largest supported file behavior

---

# 52. Browser Performance Tests

For browser processing:

Test:

```text
Small file
Medium file
Large file
Multiple files
Repeated processing
```

Monitor:

- CPU
- Memory
- UI responsiveness
- Worker activity
- Garbage collection behavior where observable
- Processing time

Verify that repeated operations do not produce uncontrolled resource growth.

---

# 53. Concurrency Testing

Where multiple operations can occur:

- [ ] Two files processed simultaneously.
- [ ] Multiple files processed simultaneously.
- [ ] Multiple tools opened simultaneously.
- [ ] Multiple browser tabs tested.
- [ ] Cancel one job while another continues.
- [ ] Error in one job does not corrupt another.
- [ ] Shared state remains correct.

---

# 54. E2E Testing

E2E tests validate complete user flows.

Core flow:

```text
Open website
→ Select tool
→ Select file
→ Configure
→ Process
→ Verify result
→ Download
```

Test complete flows for every approved tool.

---

# 55. Public Tool E2E Template

Each tool must have:

```text
Test ID:
Tool:
Browser:
Input:
Configuration:
Expected Result:
Actual Result:
Status:
Evidence:
```

---

# 56. Authentication E2E

Test:

```text
Open login
→ Enter valid credentials
→ Login
→ Access protected area
→ Logout
→ Attempt protected access
→ Verify rejection
```

Also test failure scenarios.

---

# 57. CMS E2E

Test:

```text
Login
→ Dashboard
→ Create page
→ Add block
→ Edit block
→ Save draft
→ Preview
→ Publish
→ Open public page
→ Edit page
→ Publish revision
```

---

# 58. Blog E2E

Test:

```text
Login
→ Create post
→ Add content
→ Add featured image
→ Configure SEO
→ Save draft
→ Preview
→ Publish
→ Open public post
```

---

# 59. Privacy E2E

A mandatory end-to-end privacy scenario:

```text
Open public tool
→ Select local file
→ Start processing
→ Monitor Network
→ Verify no file upload
→ Complete processing
→ Download output
→ Verify no server-side processing request
```

Repeat for:

- Successful processing
- Failed processing
- Cancelled processing
- Batch processing

---

# 60. Negative Testing

Every important feature must have negative tests.

Examples:

```text
Invalid file
Invalid format
Invalid option
Missing field
Too-large input
Too-many files
Malformed request
Expired session
Unauthorized resource
Invalid ID
Invalid URL
Invalid JSON
Unexpected data type
```

Expected behavior:

```text
Reject safely
```

Not:

```text
Crash
```

---

# 61. Boundary Testing

Test minimum and maximum values.

Examples:

```text
0
1
minimum - 1
minimum
minimum + 1
maximum - 1
maximum
maximum + 1
```

Apply to:

- File size
- Number of files
- Image dimensions
- PDF pages
- Pagination
- Text lengths
- Batch size
- API payload size
- Rate limits

---

# 62. Error Recovery Testing

Test:

- [ ] Processing error.
- [ ] Browser interruption.
- [ ] Worker crash.
- [ ] Invalid input.
- [ ] Download failure.
- [ ] Network failure for server-backed features.
- [ ] Database failure.
- [ ] Expired session.
- [ ] Unexpected API response.

Verify the application returns to a safe state.

---

# 63. Regression Testing

Every bug fix must add or update a regression test where practical.

Process:

```text
Bug found
↓
Reproduce
↓
Create regression test
↓
Fix
↓
Run regression test
↓
Run relevant suite
↓
Verify no unrelated regression
```

A fixed bug without a reasonable regression test should be explicitly justified.

---

# 64. Security Regression

Every security bug must produce:

- [ ] Reproduction case.
- [ ] Security test.
- [ ] Fix.
- [ ] Retest.
- [ ] Regression test.
- [ ] Documentation update if required.
- [ ] Threat-model update if required.

---

# 65. Dependency Update Testing

When dependencies change:

- [ ] Install succeeds.
- [ ] Build succeeds.
- [ ] Type checking passes.
- [ ] Unit tests pass.
- [ ] Integration tests pass.
- [ ] E2E tests pass.
- [ ] Security checks pass.
- [ ] Relevant tools are manually tested.
- [ ] Browser processing is tested if processing libraries changed.

---

# 66. Database Migration Testing

Before production:

- [ ] Migration tested on clean database.
- [ ] Migration tested on representative database.
- [ ] Existing data remains valid.
- [ ] Rollback strategy defined where possible.
- [ ] Destructive operations reviewed.
- [ ] Application compatibility checked.
- [ ] Backup verified before migration.

---

# 67. Deployment Testing

Before production:

```text
Build
↓
Deploy to staging
↓
Run smoke tests
↓
Run E2E
↓
Run security checks
↓
Run performance checks
↓
Approve
↓
Production
```

---

# 68. Production Smoke Tests

After deployment:

- [ ] Homepage.
- [ ] Tool page.
- [ ] Public file processing.
- [ ] Download.
- [ ] Authentication.
- [ ] Logout.
- [ ] Admin dashboard.
- [ ] Page rendering.
- [ ] Blog.
- [ ] Sitemap.
- [ ] Robots.
- [ ] Error handling.
- [ ] Monitoring.

---

# 69. Production Privacy Verification

After deployment:

- [ ] Public tool tested.
- [ ] Network requests inspected.
- [ ] No unexpected file upload.
- [ ] Third-party requests inspected.
- [ ] Error reporting inspected.
- [ ] Analytics inspected.
- [ ] CDN behavior reviewed where relevant.

This is a mandatory release check because the product's primary differentiator depends on the privacy invariant remaining true.

---

# 70. Test Data

Test data should include:

### Normal

- Typical images
- Typical PDFs
- Typical documents where supported

### Edge

- Very small files
- Very large files
- Unusual dimensions
- Unicode filenames
- Long filenames
- Special characters

### Malformed

- Corrupted files
- Truncated files
- Invalid structures
- Incorrect headers

### Security

- XSS payloads
- Path traversal strings
- Invalid IDs
- Oversized payloads
- Malicious SVG test cases
- Invalid URLs

Do not use real private user files unless explicitly required and properly controlled.

---

# 71. Test File Library

The project should maintain a controlled test corpus.

Suggested structure:

```text
tests/
  fixtures/
    images/
    pdf/
    malformed/
    security/
    unicode/
    large/
  unit/
  integration/
  e2e/
  security/
  performance/
```

Test fixtures should be documented.

---

# 72. Test Naming

Test names must describe behavior.

Prefer:

```text
rejects unsupported image format
```

over:

```text
testImageFunction
```

Prefer:

```text
does not upload file during browser processing
```

over:

```text
privacyTest
```

---

# 73. Test Isolation

Tests should be isolated whenever possible.

A test should not depend unnecessarily on:

- Another test
- Manual browser state
- Developer-specific configuration
- Existing database records
- Production data
- Previous test execution

---

# 74. Deterministic Testing

Tests should be reproducible.

Avoid unnecessary dependency on:

- Current time
- Random values
- Network availability
- External services
- Browser-specific timing
- Undocumented environment state

When randomness is necessary:

- [ ] Seed behavior where possible.
- [ ] Record the relevant seed.
- [ ] Make failures reproducible.

---

# 75. External Service Testing

If a feature depends on an external service:

- [ ] Service dependency documented.
- [ ] Mock/stub strategy defined.
- [ ] Failure behavior tested.
- [ ] Timeout behavior tested.
- [ ] Retry behavior tested where applicable.
- [ ] Rate limit behavior tested.
- [ ] External data is not required for unrelated unit tests.

Public file processing should not depend on an external processing API.

---

# 76. Test Coverage

Coverage metrics are useful but are not the definition of quality.

The project must not use:

```text
High coverage = automatically safe
```

Instead evaluate:

- Critical business logic
- Security boundaries
- Privacy boundaries
- Error paths
- Edge cases
- Core processing logic
- Authorization logic

Critical paths must have meaningful tests regardless of raw percentage.

---

# 77. Security Verification Baseline

The project will use OWASP ASVS as a security verification reference.

ASVS organizes verification into areas including:

- Architecture and threat modeling
- Authentication
- Session management
- Access control
- Validation/sanitization/encoding
- Cryptography
- Error handling/logging
- Data protection
- Communication
- Malicious code
- Business logic
- Files/resources
- API/web services
- Configuration

The project does not blindly implement every ASVS requirement. Relevant controls must be selected based on the application's actual architecture and risks.

Target baseline:

```text
OWASP ASVS Level 2
```

for the application's general security verification, with higher rigor applied to particularly sensitive components where justified.

---

# 78. Security Testing Lifecycle

Security testing occurs at:

```text
Requirements
↓
Threat Modeling
↓
Architecture
↓
Implementation
↓
Automated Tests
↓
Manual Security Review
↓
E2E Security Tests
↓
Deployment Review
↓
Production Monitoring
```

Security testing is not postponed until the final release.

OWASP's testing framework explicitly places security activities before development, during design and development, during deployment, and during maintenance/operations.

---

# 79. Threat-Based Testing

Tests must be derived from:

- `hack.md`
- `security.md`
- `auth.md`
- `architecture.md`

For every significant threat:

```text
Threat
↓
Attack path
↓
Security control
↓
Test
↓
Expected result
↓
Evidence
```

If a documented attack path has no corresponding test, it must be reviewed.

---

# 80. Privacy Threat Testing

Privacy tests must specifically verify:

```text
User file
↓
Browser
↓
Processing
↓
Browser output
↓
Download
```

and prevent:

```text
User file
↓
Server
```

unless a future feature explicitly requires server processing and has been separately approved.

---

# 81. Test Automation Strategy

Automate stable, repeatable tests.

Priority:

### High

- Unit tests
- Validation tests
- Tool registry tests
- Authentication tests
- Authorization tests
- Privacy network tests where feasible
- Core E2E tests

### Medium

- Browser compatibility
- Accessibility
- Performance

### Manual

- Visual quality
- Complex UX behavior
- Exploratory testing
- Some security assessments

---

# 82. Exploratory Testing

Exploratory testing is allowed and encouraged.

It should investigate:

- Unexpected user behavior
- Unusual files
- Strange navigation
- Rapid repeated actions
- Cancellation
- Back/forward navigation
- Multiple tabs
- Browser refresh during processing
- Theme changes during processing
- Resize during processing
- Interrupted downloads

Findings must be documented.

---

# 83. Browser Refresh Testing

During processing:

- [ ] Refresh immediately.
- [ ] Refresh during processing.
- [ ] Refresh near completion.
- [ ] Navigate away.
- [ ] Return to tool.
- [ ] Verify no corrupted persistent state.
- [ ] Verify no unexpected server upload.

---

# 84. Multiple Tab Testing

Test:

```text
Tab A → Tool
Tab B → Tool
```

and:

```text
Tab A → Admin
Tab B → Admin
```

Verify:

- [ ] Sessions behave correctly.
- [ ] State does not unexpectedly leak.
- [ ] One tab cannot bypass authorization.
- [ ] Logout behavior is understood.
- [ ] Processing remains isolated.

---

# 85. Browser Back/Forward Testing

Test:

- [ ] Back from tool.
- [ ] Forward to tool.
- [ ] Back from processing.
- [ ] Forward after processing.
- [ ] Back from admin page.
- [ ] Back after logout.

Verify sensitive states are not exposed through stale UI or browser navigation.

---

# 86. Network Failure Testing

For server-backed features:

- [ ] Slow connection.
- [ ] Request timeout.
- [ ] Server error.
- [ ] Connection interruption.
- [ ] Retry.
- [ ] Duplicate request.

Verify safe recovery.

Public browser-only processing should remain functional without requiring a network request for the actual file transformation.

---

# 87. Memory Leak Testing

For browser tools:

Run repeated processing:

```text
10 operations
50 operations
100 operations
```

where practical.

Observe:

- [ ] Memory does not grow uncontrollably.
- [ ] Workers terminate.
- [ ] Object URLs are released.
- [ ] Temporary arrays/buffers are released.
- [ ] UI remains responsive.

---

# 88. Large File Testing

Each applicable tool must define:

```text
Maximum supported size
Recommended size
Known limitations
```

Test:

```text
Below limit
At limit
Above limit
```

Expected behavior above the limit:

```text
Safe rejection
```

not browser instability.

---

# 89. Large Image Testing

For image tools:

- [ ] Large file size.
- [ ] Large pixel dimensions.
- [ ] Large decoded memory footprint.
- [ ] Multiple large images.
- [ ] Large batch.

The decoded image size, not only the compressed file size, must be considered when evaluating browser memory risk.

---

# 90. PDF Resource Testing

For PDF tools:

- [ ] Large page count.
- [ ] Large embedded images.
- [ ] Large file size.
- [ ] Multiple PDFs.
- [ ] Repeated processing.

Verify safe failure when resource limits are exceeded.

---

# 91. Test Failure Handling

When a test fails:

1. Record failure.
2. Identify affected component.
3. Determine severity.
4. Reproduce.
5. Fix root cause.
6. Add regression coverage.
7. Re-run failed test.
8. Re-run related tests.
9. Re-run regression suite.
10. Document result.

Never simply delete or weaken the test to make CI pass.

---

# 92. Severity Classification

### Critical

- Authentication bypass
- Authorization bypass
- Arbitrary code execution
- Major privacy violation
- Public file upload against the product invariant
- Production secret exposure
- Severe data corruption

### High

- Stored XSS
- IDOR
- Significant session vulnerability
- Major file-processing vulnerability
- Significant data exposure

### Medium

- Limited information disclosure
- Moderate denial of service
- Limited authorization weakness

### Low

- Minor information leakage
- Low-impact UI/security issue

Severity must consider actual exploitability and impact.

---

# 93. Release Blockers

The release is blocked by:

- [ ] Critical security failure.
- [ ] High-impact privacy failure.
- [ ] Authentication bypass.
- [ ] Authorization bypass.
- [ ] Critical data corruption.
- [ ] Core tool failure.
- [ ] Failed mandatory regression.
- [ ] Secrets exposed.
- [ ] Unresolved critical production error.

---

# 94. Definition of Tested

A feature is `TESTED` only when:

- [ ] Required automated tests ran.
- [ ] Required manual tests ran.
- [ ] Required security tests ran.
- [ ] Required edge cases ran.
- [ ] Required browser tests ran.
- [ ] Results were recorded.
- [ ] Failures were resolved or explicitly accepted according to project rules.

---

# 95. Definition of Passed

A feature is `PASSED` only when:

```text
All mandatory tests
        ↓
Executed
        ↓
Expected behavior observed
        ↓
No unresolved mandatory failures
```

---

# 96. Definition of Approved

A feature is `APPROVED` only when:

```text
Implemented
+
Tested
+
Passed
+
Security Checked
+
Privacy Checked
+
Performance Checked
+
Accessibility Checked
+
Documented
+
Regression Safe
```

---

# 97. Test Report Template

Every major feature should produce:

```text
Feature:
[Name]

Version:
[Version]

Environment:
[Environment]

Date:
[Date]

Tests Run:
[Number]

Tests Passed:
[Number]

Tests Failed:
[Number]

Tests Blocked:
[Number]

Security Tests:
PASS / FAIL

Privacy Tests:
PASS / FAIL

Performance Tests:
PASS / FAIL

Accessibility Tests:
PASS / FAIL

Regression Tests:
PASS / FAIL

Known Issues:
[List]

Evidence:
[List]

Final Status:
APPROVED / NOT APPROVED
```

---

# 98. Tool Test Report Template

```text
Tool:
[Tool Name]

Tool ID:
[ID]

Input Formats:
[List]

Output Formats:
[List]

Unit Tests:
PASS / FAIL

Integration Tests:
PASS / FAIL

E2E Tests:
PASS / FAIL

Security:
PASS / FAIL

Privacy:
PASS / FAIL

Performance:
PASS / FAIL

Accessibility:
PASS / FAIL

Browser Compatibility:
PASS / FAIL

Regression:
PASS / FAIL

Documentation:
COMPLETE / INCOMPLETE

Approval:
APPROVED / NOT APPROVED
```

---

# 99. Test Matrix

Every tool should maintain a matrix similar to:

| Test Area | Required | Status |
|---|---:|---|
| Unit | Yes | |
| Integration | Yes | |
| E2E | Yes | |
| Security | Yes | |
| Privacy | Yes | |
| Performance | Yes | |
| Accessibility | Yes | |
| Browser | Yes | |
| Regression | Yes | |
| Documentation | Yes | |

---

# 100. Phase-Based Testing

Testing must follow the roadmap.

## Phase 0 — Foundation

Test:

- [ ] Documentation consistency.
- [ ] Architecture consistency.
- [ ] Security model.
- [ ] Data model.
- [ ] Testing model.
- [ ] Roadmap integrity.

## Phase 1 — Technical Foundation

Test:

- [ ] Application starts.
- [ ] Database connects.
- [ ] Prisma works.
- [ ] Authentication foundation works.
- [ ] Base UI works.
- [ ] Theme works.
- [ ] CI works.

## Phase 2 — File Processing Engine

Test:

- [ ] File validation.
- [ ] Processing lifecycle.
- [ ] Workers.
- [ ] Cancellation.
- [ ] Memory management.
- [ ] Downloads.
- [ ] Privacy.

## Phase 3 — Initial File Tools

Test each tool independently.

## Phase 4 — Image Tools

Test each image tool independently.

## Phase 5 — PDF Tools

Test each PDF tool independently.

## Phase 6 — CMS

Test:

- [ ] Pages.
- [ ] Blocks.
- [ ] Revisions.
- [ ] Media.
- [ ] Authorization.

## Phase 7 — Blog

Test:

- [ ] Posts.
- [ ] Categories.
- [ ] Tags.
- [ ] Publishing.
- [ ] SEO.

## Phase 8 — SEO

Test:

- [ ] Metadata.
- [ ] Schema.
- [ ] Sitemap.
- [ ] Robots.
- [ ] Indexing behavior.

## Phase 9 — Security Hardening

Run:

- [ ] Full security checklist.
- [ ] Attack-path review.
- [ ] Authentication review.
- [ ] Authorization review.
- [ ] File security review.
- [ ] Dependency review.

## Phase 10 — Performance & Accessibility

Run:

- [ ] Performance tests.
- [ ] Accessibility tests.
- [ ] Browser tests.
- [ ] Large-file tests.

## Phase 11 — Final QA

Run:

- [ ] Full regression.
- [ ] Full E2E.
- [ ] Security.
- [ ] Privacy.
- [ ] Performance.
- [ ] Accessibility.
- [ ] Production simulation.

## Phase 12 — Production

Run:

- [ ] Deployment verification.
- [ ] Smoke tests.
- [ ] Privacy verification.
- [ ] Monitoring verification.
- [ ] Backup verification.
- [ ] Rollback verification.

---

# 101. Testing Rules for AI Coding Agents

Any AI coding agent working on this project must:

1. Read `PROJECT_RULES.md`.
2. Read relevant architecture documentation.
3. Read relevant feature documentation.
4. Read `testing.md`.
5. Identify affected tests.
6. Implement the change.
7. Run tests.
8. Fix failures.
9. Run tests again.
10. Run relevant regression tests.
11. Report actual results.
12. Update documentation.
13. Never claim unexecuted tests passed.
14. Never remove tests simply because they fail.
15. Never weaken validation solely to make a test pass.

---

# 102. AI Testing Stop Rule

The AI must stop and report instead of continuing when:

```text
Required test cannot be executed
AND
the result materially affects approval.
```

It must report:

```text
Test:
[Name]

Status:
BLOCKED

Reason:
[Reason]

Impact:
[Impact]

Approval:
NOT APPROVED
```

---

# 103. Final QA Checklist

Before final approval:

- [ ] Build passes.
- [ ] Type checking passes.
- [ ] Lint passes.
- [ ] Unit tests pass.
- [ ] Integration tests pass.
- [ ] E2E tests pass.
- [ ] Security tests pass.
- [ ] Privacy tests pass.
- [ ] Performance tests pass.
- [ ] Accessibility tests pass.
- [ ] Browser tests pass.
- [ ] Regression tests pass.
- [ ] Documentation complete.
- [ ] No critical known issue exists.
- [ ] No high-risk unresolved issue exists without explicit approval.

---

# 104. Final Testing Principle

Testing is a continuous verification process.

The project must not rely on:

```text
"It works on my machine."
```

or:

```text
"The build passes."
```

or:

```text
"The UI looks correct."
```

as evidence of completion.

The required standard is:

```text
Correct
+
Secure
+
Private
+
Reliable
+
Performant
+
Accessible
+
Tested
+
Documented
```

Only then:

```text
APPROVED
```

---

# 105. External Testing References

The project uses the following external security-testing references:

- OWASP Web Security Testing Guide (WSTG) as the primary conceptual reference for web security testing methodology.
- OWASP Application Security Verification Standard (ASVS) as a verification baseline for application security controls.

These references supplement the project's own requirements and do not override `PROJECT_RULES.md`, `PRD.md`, or approved architecture documentation.

---

# 106. Final Invariant

```text
No required test is silently skipped.

No failed test is reported as passed.

No untested security boundary is considered secure.

No untested privacy boundary is considered private.

No tool is approved without independent testing.

No regression is accepted silently.

No production release occurs while a release-blocking test is failing.

Testing evidence must reflect what actually happened.
```

**End of `testing.md`**
