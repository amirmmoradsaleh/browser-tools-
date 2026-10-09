# PROJECT CHECKLIST

**Project:** Privacy-First File Tools Platform  
**Document:** `checklist.md`  
**Version:** 1.1  
**Purpose:** Mandatory cross-project verification checklist

---

# 1. Documentation Foundation

- [ ] `PRD.md` exists and is current.
- [ ] `PROJECT_RULES.md` exists and is current.
- [ ] `roadmap.md` exists and matches the approved execution roadmap.
- [ ] `architecture.md` exists and matches the implemented architecture.
- [ ] `security.md` exists using the canonical lowercase filename.
- [ ] `auth.md` exists and matches authentication behavior.
- [ ] `data.md` exists and matches actual data storage/lifecycle.
- [ ] `hack.md` exists and reflects current attack paths.
- [ ] `testing.md` exists and matches the actual testing strategy.
- [ ] `incident-response.md` exists and matches the operational response model.
- [ ] Phase-specific documentation exists where required.
- [ ] No required document is missing.
- [ ] No required document has a conflicting filename.
- [ ] Cross-document terminology is consistent.
- [ ] Cross-document requirements are consistent.
- [ ] Documentation reflects the current implementation.

---

# 2. Source-of-Truth Verification

- [ ] `PROJECT_RULES.md` is treated as the highest project-level execution authority.
- [ ] `PRD.md` is treated as the product requirements authority.
- [ ] `roadmap.md` is treated as the execution boundary.
- [ ] Approved architecture documents are followed.
- [ ] Approved feature specifications are followed.
- [ ] Implementation does not silently override documentation.
- [ ] Documentation conflicts are resolved before affected implementation continues.

---

# 3. Roadmap Verification

- [ ] Phase order matches `roadmap.md`.
- [ ] No phase has been skipped.
- [ ] No later-phase functionality has been implemented early without approval.
- [ ] Current work belongs to the current phase.
- [ ] Previous phase is `APPROVED` before current phase begins.
- [ ] Roadmap has not been changed silently.
- [ ] Any roadmap change has documented justification.
- [ ] Any roadmap change has explicit project-level approval.
- [ ] Roadmap and PRD remain synchronized at the appropriate level.

---

# 4. Scope Control

For every significant feature/change:

- [ ] Classified as `REQUIRED`, `OPTIONAL`, `FUTURE`, or `REJECTED`.
- [ ] Current implementation contains only approved current-phase scope.
- [ ] Feature creep has been rejected or documented.
- [ ] Security impact considered.
- [ ] Testing impact considered.
- [ ] Documentation impact considered.
- [ ] Dependency impact considered.
- [ ] Regression impact considered.

---

# 5. Privacy Verification

## Public Processing

- [ ] Public processing occurs in the browser whenever supported.
- [ ] Public processing files are not uploaded to the application server.
- [ ] Public processing files are not stored in PostgreSQL.
- [ ] Public processing files are not stored in object storage.
- [ ] Public processing files are not sent to analytics.
- [ ] Public processing files are not sent to error-reporting services.
- [ ] Public processing files are not sent to third-party processors.
- [ ] Public processing files are not included in logs.
- [ ] Public processing files are not included in debug telemetry.
- [ ] No hidden API/network request transmits the file.
- [ ] Network inspection has been performed for representative tools.
- [ ] Privacy behavior has regression coverage.

## Browser Resource Lifecycle

- [ ] Object URLs are released.
- [ ] Buffers are released when no longer required.
- [ ] Workers are terminated or reused according to specification.
- [ ] Temporary browser resources are cleaned up.
- [ ] Cancellation performs cleanup.

---

# 6. Public User / Admin Boundary

- [ ] Public tools do not require an account unless explicitly approved.
- [ ] Public users cannot access admin routes.
- [ ] Public users cannot access CMS APIs.
- [ ] Public users cannot access admin-only data.
- [ ] Admin functionality requires authentication.
- [ ] Admin functionality requires server-side authorization.
- [ ] Client-side hiding is not used as authorization.
- [ ] IDOR protection is tested.
- [ ] Privilege escalation is tested.

---

# 7. Authentication Checklist

- [ ] Passwords are never stored in plaintext.
- [ ] Approved password hashing is used.
- [ ] Login validation is server-side.
- [ ] Sessions are securely generated.
- [ ] Sessions are securely stored.
- [ ] Session expiration works.
- [ ] Logout invalidates the session.
- [ ] Old sessions are rejected after invalidation.
- [ ] Disabled accounts are rejected.
- [ ] Session fixation protection exists.
- [ ] Secure cookie attributes are configured.
- [ ] Authentication endpoints are rate-limited.
- [ ] Brute-force protection exists.
- [ ] Credential-stuffing protection is considered.
- [ ] Password reset is secure.
- [ ] Authentication errors do not leak unnecessary information.
- [ ] Authentication security tests pass.

---

# 8. Authorization Checklist

- [ ] Every protected route checks authorization server-side.
- [ ] Every protected API operation checks authorization server-side.
- [ ] Every privileged mutation checks authorization.
- [ ] Role/permission checks cannot be bypassed through client requests.
- [ ] Direct URL access is tested.
- [ ] Direct API access is tested.
- [ ] IDOR scenarios are tested.
- [ ] Privilege escalation scenarios are tested.
- [ ] Unauthorized requests return safe responses.

---

# 9. Secrets & Environment

- [ ] No real secrets are committed.
- [ ] No passwords are committed.
- [ ] No production credentials are committed.
- [ ] No API keys are committed.
- [ ] Server-only secrets cannot enter client bundles.
- [ ] Environment variables are validated.
- [ ] Public environment variables are explicitly safe for exposure.
- [ ] Example environment files contain fake values only.
- [ ] Secret scanning is performed where available.
- [ ] Secret exposure procedures are documented.

---

# 10. File Processing Security

For every public file-processing tool:

- [ ] File input is treated as untrusted.
- [ ] Extension is not the only validation mechanism.
- [ ] MIME/type validation is performed where applicable.
- [ ] File signature validation is used where applicable.
- [ ] Size limits exist.
- [ ] Memory/resource limits exist.
- [ ] Batch limits exist.
- [ ] Malformed files are tested.
- [ ] Adversarial files are tested where applicable.
- [ ] Processing cannot unexpectedly upload the file.
- [ ] Processing errors are safely handled.
- [ ] Worker errors are handled.
- [ ] Cancellation is safe.
- [ ] Result validation exists.
- [ ] Output type is validated.
- [ ] Output integrity/readability is checked where practical.

---

# 11. XSS / Content Security

- [ ] User-controlled text is safely rendered.
- [ ] Rich text is sanitized according to its approved model.
- [ ] Blog content is protected against stored XSS.
- [ ] Page Builder content is protected against XSS.
- [ ] URLs are validated.
- [ ] Redirect targets are validated.
- [ ] SVG uploads are handled safely.
- [ ] Filenames are treated as untrusted.
- [ ] Metadata is treated as untrusted.
- [ ] Unsafe HTML rendering is avoided or explicitly reviewed.
- [ ] CSP/security headers are configured as approved.

---

# 12. Other Security Checks

- [ ] CSRF protections are present where applicable.
- [ ] SQL injection defenses are present.
- [ ] ORM/query usage is safe.
- [ ] SSRF paths are reviewed.
- [ ] Path traversal is tested.
- [ ] Open redirects are tested.
- [ ] API abuse/rate limiting is tested.
- [ ] Denial-of-service/resource exhaustion paths are reviewed.
- [ ] Dependency vulnerabilities are checked.
- [ ] Security headers are checked.
- [ ] Session security is checked.
- [ ] Audit logging exists where required.

---

# 13. Database & Data Lifecycle

- [ ] PostgreSQL is the approved relational data store.
- [ ] Prisma usage matches approved architecture.
- [ ] Migrations are versioned.
- [ ] Sensitive data has documented storage locations.
- [ ] Access to sensitive data is restricted.
- [ ] Data retention rules are documented.
- [ ] Deletion rules are documented.
- [ ] Public processing files are not persisted server-side.
- [ ] Media Library data is separated from public processing data.
- [ ] Backups are configured where required.
- [ ] Restore procedures are documented and tested where required.

---

# 14. Tool Approval Checklist

Every tool must pass all applicable stages:

### Specification

- [ ] Tool has unique ID.
- [ ] Tool has defined purpose.
- [ ] Input formats defined.
- [ ] Output formats defined.
- [ ] Options defined.
- [ ] Limits defined.
- [ ] Validation defined.
- [ ] Error behavior defined.
- [ ] Privacy behavior defined.
- [ ] Security requirements defined.
- [ ] Performance requirements defined.
- [ ] Test plan defined.

### Implementation

- [ ] Tool is implemented.
- [ ] Tool is registered correctly.
- [ ] UI is implemented.
- [ ] Processing implementation exists.
- [ ] Errors are handled.
- [ ] Progress is handled where applicable.
- [ ] Cancellation is handled where applicable.

### Verification

- [ ] Unit tests pass.
- [ ] Integration tests pass.
- [ ] E2E tests pass.
- [ ] Security tests pass.
- [ ] Performance tests pass.
- [ ] Memory/resource tests pass where applicable.
- [ ] Browser tests pass.
- [ ] Accessibility tests pass.
- [ ] Privacy/network inspection passes.
- [ ] Regression tests pass.
- [ ] Documentation is complete.
- [ ] Tool is explicitly marked `APPROVED`.

---

# 15. Processing Engine

- [ ] Tool contracts are defined.
- [ ] Input validation is centralized appropriately.
- [ ] Format detection is implemented.
- [ ] Processing lifecycle is implemented.
- [ ] Capability detection is implemented.
- [ ] Worker protocol is tested.
- [ ] Progress events are tested.
- [ ] Cancellation is tested.
- [ ] Memory cleanup is tested.
- [ ] Result validation is tested.
- [ ] Download handling is tested.
- [ ] Batch processing is tested.
- [ ] Error classification is implemented.
- [ ] Engine orchestration is tested.
- [ ] Engine does not violate the privacy invariant.

---

# 16. CMS Checklist

- [ ] Admin authentication is required.
- [ ] Server-side authorization is enforced.
- [ ] Page management works.
- [ ] Page Builder uses structured blocks.
- [ ] Blocks are validated.
- [ ] Blocks are versioned where required.
- [ ] Duplicate works.
- [ ] Copy/paste works using structured data.
- [ ] Reusable blocks work where approved.
- [ ] Undo/redo works where approved.
- [ ] Draft/publish works.
- [ ] Revisions are preserved.
- [ ] Revision restore is protected.
- [ ] Media Library works.
- [ ] Media metadata works.
- [ ] Media uploads are secured.
- [ ] Public processing files cannot enter Media Library accidentally.
- [ ] Navigation management is protected.
- [ ] Tool management is protected.
- [ ] Auditability is implemented where required.

---

# 17. Blog Checklist

- [ ] Posts can be created.
- [ ] Posts can be edited.
- [ ] Posts can be deleted according to authorization rules.
- [ ] Draft state works.
- [ ] Publish state works.
- [ ] Categories work.
- [ ] Tags work.
- [ ] Slugs are validated.
- [ ] Featured images are secured.
- [ ] Related content works where approved.
- [ ] Stored-XSS protections pass.
- [ ] Blog SEO works.
- [ ] Blog structured data works.
- [ ] Publication authorization is enforced.

---

# 18. SEO Checklist

- [ ] SEO title works.
- [ ] Meta description works.
- [ ] Canonical URL works.
- [ ] Robots directives work.
- [ ] Open Graph metadata works.
- [ ] Social metadata works.
- [ ] JSON-LD is valid.
- [ ] Schema output is appropriate to the page.
- [ ] Breadcrumbs work.
- [ ] Sitemap works.
- [ ] Robots.txt works.
- [ ] Restricted content is not accidentally indexed.
- [ ] Tool pages have appropriate SEO data.
- [ ] CMS pages have appropriate SEO data.
- [ ] Blog pages have appropriate SEO data.
- [ ] No unsafe user-controlled content enters structured data.

---

# 19. UI / UX Checklist

- [ ] UI is clear.
- [ ] Core workflows require minimal unnecessary interaction.
- [ ] Responsive behavior works.
- [ ] Mobile workflows work.
- [ ] Desktop workflows work.
- [ ] Loading states exist.
- [ ] Empty states exist where applicable.
- [ ] Error states exist.
- [ ] Success states exist.
- [ ] Processing progress is understandable.
- [ ] Downloads are understandable.
- [ ] Buttons and controls have clear states.
- [ ] Animations do not block interaction.
- [ ] Reduced-motion mode works.
- [ ] Light mode works.
- [ ] Dark mode works.
- [ ] System theme works.

---

# 20. Accessibility Checklist

Target: **WCAG 2.2 AA**

- [ ] Keyboard navigation works.
- [ ] Focus is visible.
- [ ] Focus order is logical.
- [ ] Dialog focus is managed.
- [ ] Forms have accessible labels.
- [ ] Errors are accessible.
- [ ] Processing states are accessible.
- [ ] Progress states are accessible.
- [ ] Color is not the only status indicator.
- [ ] Contrast is acceptable.
- [ ] Touch targets are usable.
- [ ] Hover-only interaction is avoided for essential workflows.
- [ ] Reduced motion is respected.
- [ ] Screen-reader relevant content is exposed correctly.

---

# 21. Performance Checklist

- [ ] Production build succeeds.
- [ ] Initial bundle is reviewed.
- [ ] Code splitting is used where appropriate.
- [ ] Lazy loading is used where appropriate.
- [ ] Main-thread blocking is minimized.
- [ ] Web Workers are used where appropriate.
- [ ] Large-file processing is tested.
- [ ] Batch processing is tested.
- [ ] Memory usage is tested.
- [ ] Object URLs are released.
- [ ] Unnecessary network requests are removed.
- [ ] Core Web Vitals are reviewed.
- [ ] Mobile performance is reviewed.

---

# 22. Browser Compatibility

Verify supported functionality on:

- [ ] Chrome
- [ ] Firefox
- [ ] Safari
- [ ] Edge

And where applicable:

- [ ] Desktop
- [ ] Tablet
- [ ] Mobile

Browser-specific failures must be documented and resolved or explicitly approved.

---

# 23. Error Handling

- [ ] User errors are distinguishable from system errors.
- [ ] Capability errors are handled.
- [ ] Processing errors are handled.
- [ ] Resource-limit errors are handled.
- [ ] Authentication errors are handled.
- [ ] Authorization errors are handled.
- [ ] Unexpected errors are safely handled.
- [ ] User messages do not expose secrets or internal details.
- [ ] Logs contain enough diagnostic information without sensitive data.

---

# 24. Logging & Observability

- [ ] Passwords are never logged.
- [ ] Tokens are never logged.
- [ ] API keys are never logged.
- [ ] File contents are never logged.
- [ ] Sensitive personal information is not unnecessarily logged.
- [ ] Security events are logged appropriately.
- [ ] Operational errors are observable.
- [ ] Logs are safe for production.
- [ ] Error reporting does not receive public processing files.

---

# 25. Dependency & Supply Chain

- [ ] Dependencies have documented reasons.
- [ ] Unnecessary dependencies are removed.
- [ ] Dependency vulnerabilities are checked.
- [ ] Lockfile is committed and consistent.
- [ ] Licenses are reviewed where required.
- [ ] Client bundle impact is reviewed.
- [ ] Unmaintained/high-risk packages are avoided.
- [ ] Dependency updates are regression-tested.

---

# 26. CI / Build

- [ ] Install succeeds.
- [ ] Typecheck passes.
- [ ] Lint passes.
- [ ] Unit tests pass.
- [ ] Integration tests pass.
- [ ] Build passes.
- [ ] E2E tests pass where environment supports them.
- [ ] Security/dependency checks pass.
- [ ] CI result is consistent with local verification.

---

# 27. Incident Response

- [ ] Incident severity can be classified.
- [ ] Security incidents have a documented response path.
- [ ] Privacy incidents have a documented response path.
- [ ] Secret exposure response exists.
- [ ] Account compromise response exists.
- [ ] Data integrity incident response exists.
- [ ] File-processing security incident response exists.
- [ ] Evidence preservation process exists.
- [ ] Containment process exists.
- [ ] Recovery process exists.
- [ ] Post-incident review process exists.

---

# 28. Final Phase Approval

Before approving any phase:

- [ ] All required implementation is complete.
- [ ] All mandatory tests pass.
- [ ] Security checks pass.
- [ ] Performance checks pass where applicable.
- [ ] Accessibility checks pass where applicable.
- [ ] Browser checks pass where applicable.
- [ ] Regression tests pass.
- [ ] Documentation is synchronized.
- [ ] No blocking known issue remains.
- [ ] Work report is complete.
- [ ] Phase status is explicitly set to `APPROVED`.

---

# 29. Release / Production Checklist

Before production:

- [ ] All roadmap phases are approved.
- [ ] All public tools are individually approved.
- [ ] Full regression passes.
- [ ] Security hardening is approved.
- [ ] Performance/accessibility phase is approved.
- [ ] Production configuration is validated.
- [ ] Secrets are configured securely.
- [ ] Database migrations are verified.
- [ ] Backups exist.
- [ ] Restore process has been verified.
- [ ] Monitoring is active.
- [ ] Error reporting is configured safely.
- [ ] Domain/HTTPS is configured.
- [ ] Production smoke tests pass.
- [ ] Production privacy/network verification passes.
- [ ] Documentation is final and synchronized.
- [ ] Production launch is explicitly approved.

---

# 30. Mandatory AI Verification

Before an AI agent makes significant changes:

- [ ] It has read `PROJECT_RULES.md`.
- [ ] It has checked `PRD.md`.
- [ ] It has checked `roadmap.md`.
- [ ] It has checked relevant approved specifications.
- [ ] It confirmed the task belongs to the current phase.
- [ ] It checked for existing implementation before creating duplicates.
- [ ] It followed current architecture.
- [ ] It ran required tests.
- [ ] It reported test failures honestly.
- [ ] It updated required documentation.
- [ ] It did not silently change roadmap or project rules.

---

# 31. Work Completion Record

For significant work, record:

```text
Changed:
Added:
Removed:

Tests Run:
Tests Passed:
Tests Failed:

Known Issues:

Documentation Updated:

Security Verification:

Privacy Verification:

Regression Verification:

Approval Status:
```

Never mark `APPROVED` until all mandatory gates are satisfied.
