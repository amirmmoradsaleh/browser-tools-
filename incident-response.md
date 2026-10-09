# Incident Response & Security Incident Management

**Document Version:** 1.0  
**Status:** Mandatory  
**Applies To:** Entire Project  
**Authority:** `PROJECT_RULES.md`

---

# 1. Purpose

This document defines how the project detects, contains, investigates, resolves, documents, and learns from security and privacy incidents.

The purpose is to minimize:

- User harm
- Privacy impact
- Data loss
- Data corruption
- Unauthorized access
- Service disruption
- Security exposure
- Recovery time
- Repeated exploitation

Incident response is part of the product's security architecture.

It is not an optional operational document.

NIST's current incident-response guidance is SP 800-61 Rev. 3, which integrates incident response into broader cybersecurity risk management and uses Detect, Respond, and Recover as the core incident-response functions, supported by continuous improvement.

---

# 2. Incident Response Principles

The project follows these principles:

1. Protect users first.
2. Protect privacy first when user data may be exposed.
3. Contain active attacks quickly.
4. Preserve evidence before destroying it when practical.
5. Do not hide security incidents.
6. Do not modify evidence unnecessarily.
7. Do not make unsupported assumptions.
8. Do not publicly disclose unverified information.
9. Do not blame individuals before facts are established.
10. Fix the root cause, not only the visible symptom.
11. Add regression tests after security fixes.
12. Update documentation after significant incidents.
13. Feed lessons learned back into the architecture and threat model.

---

# 3. Incident Definition

A security incident is any event that may compromise:

- Confidentiality
- Integrity
- Availability
- Authentication
- Authorization
- Privacy
- Secrets
- Infrastructure
- Application security
- User safety

Examples:

- Unauthorized admin access
- Authentication bypass
- Authorization bypass
- Account takeover
- Session theft
- Secret exposure
- Database compromise
- Malicious file-processing exploit
- XSS exploitation
- SQL injection
- SSRF
- IDOR
- Data leakage
- Unexpected user-file upload
- Malicious dependency compromise
- Production configuration exposure
- Denial of service
- Data corruption

---

# 4. Privacy Incident Definition

A privacy incident is any event in which user files or personal data may have been exposed, transmitted, retained, or accessed contrary to the project's documented privacy model.

For this project, the following is especially critical:

```text id="3kvx9n"
Public user file
        ↓
Unexpected server upload
```

This is a potentially critical privacy incident because browser-only processing is a core product invariant.

---

# 5. Incident Severity

## Critical

Examples:

- Authentication bypass
- Admin authorization bypass
- Arbitrary code execution
- Major database compromise
- Major user-data exposure
- User files unexpectedly uploaded/stored by public tools
- Production secrets exposed
- Large-scale account takeover
- Critical supply-chain compromise

Required response:

```text id="f2q6ks"
Immediate containment
Immediate investigation
Immediate remediation
Executive/owner notification
Release/deployment freeze where appropriate
Post-incident review
```

---

## High

Examples:

- Stored XSS with meaningful impact
- IDOR exposing private resources
- Significant session vulnerability
- Serious file-processing vulnerability
- Important admin security weakness
- Significant unauthorized media access
- Large-scale denial of service

Required response:

```text id="2q1v8r"
Rapid containment
Investigation
Fix
Retest
Security review
```

---

## Medium

Examples:

- Limited information disclosure
- Moderate authorization weakness
- Limited resource exhaustion
- Security misconfiguration with constrained impact

Required response:

```text id="q31j7p"
Investigate
Prioritize
Fix
Regression test
```

---

## Low

Examples:

- Minor information disclosure
- Low-impact configuration issue
- Low-risk UI security weakness

Required response:

```text id="7r7k0n"
Document
Prioritize
Fix according to release plan
```

Severity must be determined using actual impact and exploitability, not only the vulnerability category.

---

# 6. Incident Lifecycle

The project's incident-response lifecycle is:

```text id="a4k2d8"
PREPARE
   ↓
DETECT
   ↓
TRIAGE
   ↓
CONTAIN
   ↓
INVESTIGATE
   ↓
ERADICATE
   ↓
RECOVER
   ↓
VERIFY
   ↓
LEARN
   ↓
IMPROVE
```

This is compatible with the current NIST approach in which preparation/risk-management activities support the incident-response functions, while Detect, Respond, Recover and continuous improvement form the operational response model.

---

# 7. Preparation

Preparation must exist before an incident occurs.

## Required

- [ ] Security documentation exists.
- [ ] `security.md` exists.
- [ ] `auth.md` exists.
- [ ] `data.md` exists.
- [ ] `hack.md` exists.
- [ ] `checklist.md` exists.
- [ ] `testing.md` exists.
- [ ] `incident-response.md` exists.
- [ ] Logging exists.
- [ ] Monitoring exists where appropriate.
- [ ] Backup strategy exists.
- [ ] Recovery strategy exists.
- [ ] Secret rotation strategy exists.
- [ ] Deployment rollback procedure exists.
- [ ] Emergency access procedure exists.
- [ ] Security contact path is known.

---

# 8. Incident Detection Sources

Potential detection sources:

- Application logs
- Authentication logs
- Authorization failures
- Audit logs
- Monitoring
- Error monitoring
- Dependency alerts
- Security scans
- CI/CD alerts
- Database alerts
- Infrastructure alerts
- User reports
- Developer reports
- Security researchers
- Automated anomaly detection
- Manual security testing

No single source should be considered complete.

---

# 9. Detection Signals

Potential indicators include:

### Authentication

- Sudden login-failure increase
- Repeated login attempts
- Unusual login patterns
- Unexpected successful login
- Password reset abuse
- Session anomalies

### Authorization

- Repeated denied-resource requests
- Attempts to access sequential object IDs
- Admin endpoint probing
- Permission errors from unexpected users

### File Processing

- Unusual processing failures
- Browser crashes associated with specific files
- Unexpected file transmission
- Parser crashes
- Abnormal memory usage
- Repeated malicious inputs

### Infrastructure

- Unexpected CPU increase
- Unexpected memory increase
- Unusual traffic
- Database anomalies
- Unexpected processes
- Unexpected configuration changes

---

# 10. Detection of Privacy Violations

A privacy violation must be investigated if:

- Network traffic contains user file bytes.
- A public tool calls a server-side processing endpoint unexpectedly.
- Analytics receives file data.
- Error reporting receives file data.
- Third-party services receive user file contents.
- Server logs contain user file contents.
- Temporary server files appear unexpectedly.
- Public processing files appear in persistent storage.

---

# 11. Initial Triage

When an incident is detected, answer:

```text id="p8t1kw"
What happened?
When did it happen?
What system is affected?
Who may be affected?
What data may be affected?
Is the attack active?
Is exploitation confirmed?
What is the likely severity?
What immediate action is required?
```

Do not begin with assumptions about the attacker.

---

# 12. First Response Checklist

When a credible incident is reported:

- [ ] Record time of discovery.
- [ ] Record reporter/source.
- [ ] Preserve relevant logs.
- [ ] Preserve relevant system state where practical.
- [ ] Determine whether the incident is active.
- [ ] Determine whether users are currently at risk.
- [ ] Determine affected systems.
- [ ] Determine affected data.
- [ ] Assign initial severity.
- [ ] Begin containment if necessary.
- [ ] Create incident record.

---

# 13. Incident Record

Every significant incident must have an incident record.

Template:

```text id="v4v4r5"
Incident ID:
IR-[YYYY]-[NUMBER]

Detected:
[Date / Time]

Reported By:
[Source]

Severity:
Critical / High / Medium / Low

Status:
Open / Investigating / Contained / Recovering / Resolved / Closed

Affected Systems:
[List]

Affected Features:
[List]

Potentially Affected Users:
[Unknown / Estimate / Confirmed]

Potentially Affected Data:
[List]

Initial Description:
[Description]

Current Impact:
[Impact]

Containment:
[Actions]

Investigation:
[Findings]

Root Cause:
[Cause]

Remediation:
[Actions]

Verification:
[Tests]

Follow-up:
[Actions]

Owner:
[Responsible person/team]

Closed:
[Date / Time]
```

---

# 14. Incident Status

An incident progresses through:

```text id="x0l9pp"
OPEN
↓
TRIAGED
↓
CONTAINING
↓
CONTAINED
↓
INVESTIGATING
↓
REMEDIATING
↓
RECOVERING
↓
VERIFIED
↓
CLOSED
```

A status change must be documented.

---

# 15. Containment

The first operational goal during an active incident is to reduce ongoing impact.

Possible containment actions:

- Disable affected endpoint
- Disable affected tool
- Disable affected feature
- Revoke compromised credentials
- Rotate secrets
- Terminate compromised sessions
- Disable affected account
- Restrict traffic
- Block malicious requests
- Disable vulnerable dependency path
- Roll back deployment
- Isolate affected infrastructure
- Temporarily disable public processing for an affected tool

Containment must be proportionate to the incident.

---

# 16. Emergency Kill Switches

The architecture should support emergency disabling of high-risk functionality where practical.

Potential controls:

```text id="4wz1oh"
Tool disabled
API endpoint disabled
Admin action disabled
Upload disabled
Media upload disabled
Blog publishing disabled
Account login disabled
Public processing feature disabled
```

Emergency controls must fail safely.

They must not:

- Bypass authentication
- Disable authorization
- Expose data
- Create a new attack path

---

# 17. Public Tool Kill Switch

Because file-processing tools are core functionality, each production tool should be capable of being disabled independently where practical.

Example:

```text id="0p5x6k"
Tool:
PDF Merge

Status:
ACTIVE / DISABLED

Reason:
Security incident

Disabled At:
[Time]

Expected Re-enable:
[Unknown / Time]
```

The rest of the application should continue operating where safe.

---

# 18. Privacy Incident Containment

If public files may have reached the server unexpectedly:

Immediately consider:

- [ ] Disable affected tool.
- [ ] Stop further transmission.
- [ ] Disable affected API endpoint.
- [ ] Preserve relevant logs.
- [ ] Determine whether files were stored.
- [ ] Determine whether third parties received data.
- [ ] Determine retention period.
- [ ] Determine affected time window.
- [ ] Determine potentially affected users.
- [ ] Prevent further collection.
- [ ] Begin root-cause analysis.

Do not delete evidence before determining what must be preserved.

---

# 19. Secret Exposure Response

If an API key, password, token, private key, or other secret is exposed:

1. Assume the secret is compromised.
2. Stop relying on secrecy of the exposed value.
3. Revoke it immediately where possible.
4. Generate a replacement.
5. Update the application securely.
6. Verify the replacement.
7. Search for continued use of the old secret.
8. Review logs for misuse.
9. Remove the secret from the source location.
10. Review Git history if applicable.
11. Document the incident.

OWASP's current secrets-management guidance specifically emphasizes rapid response to secret exposure, including immediate revocation and documented containment procedures.

---

# 20. Authentication Incident

If authentication may be compromised:

- [ ] Identify affected accounts.
- [ ] Identify affected sessions.
- [ ] Invalidate suspicious sessions.
- [ ] Force session invalidation if required.
- [ ] Review login logs.
- [ ] Review password-reset activity.
- [ ] Review failed login patterns.
- [ ] Determine whether credentials were exposed.
- [ ] Require password reset if justified.
- [ ] Rotate relevant secrets.
- [ ] Review authentication code.
- [ ] Add regression tests.

---

# 21. Authorization Incident

If authorization is bypassed:

- [ ] Identify vulnerable endpoint.
- [ ] Identify affected resource type.
- [ ] Identify affected roles.
- [ ] Identify possible accessed resources.
- [ ] Disable vulnerable endpoint if necessary.
- [ ] Fix server-side authorization.
- [ ] Add IDOR/privilege tests.
- [ ] Test all related endpoints.
- [ ] Review similar authorization logic.
- [ ] Perform regression testing.

A single authorization bug should trigger a review of similar code paths.

---

# 22. XSS Incident

If XSS is discovered:

- [ ] Identify source.
- [ ] Identify affected rendering context.
- [ ] Determine stored/reflected/DOM classification.
- [ ] Determine affected users.
- [ ] Remove malicious content.
- [ ] Disable affected content path if required.
- [ ] Fix sanitization/encoding.
- [ ] Review similar rendering paths.
- [ ] Add regression tests.
- [ ] Review existing stored content.
- [ ] Review session exposure if applicable.

---

# 23. File-Processing Incident

If a malicious file causes unexpected behavior:

- [ ] Identify file type.
- [ ] Identify parser/library.
- [ ] Identify affected tool.
- [ ] Preserve safe test sample.
- [ ] Determine exploitability.
- [ ] Disable affected tool if required.
- [ ] Identify vulnerable dependency.
- [ ] Update or replace dependency.
- [ ] Add malicious fixture.
- [ ] Add regression test.
- [ ] Retest supported formats.
- [ ] Review related tools using the same parser.

---

# 24. Database Incident

If database compromise or unauthorized access is suspected:

- [ ] Determine affected database.
- [ ] Determine affected tables.
- [ ] Determine affected records.
- [ ] Determine access path.
- [ ] Restrict compromised access.
- [ ] Rotate credentials.
- [ ] Preserve relevant logs.
- [ ] Review audit logs.
- [ ] Verify database integrity.
- [ ] Verify backups.
- [ ] Check for unauthorized modifications.
- [ ] Restore from known-good backup if necessary.
- [ ] Validate application integrity after recovery.

---

# 25. Data Integrity Incident

If data was modified incorrectly or maliciously:

- [ ] Stop further modification if required.
- [ ] Identify affected records.
- [ ] Determine modification window.
- [ ] Preserve evidence.
- [ ] Compare against backups/revisions.
- [ ] Restore correct data.
- [ ] Verify relationships.
- [ ] Verify published content.
- [ ] Verify media references.
- [ ] Verify SEO data.
- [ ] Add prevention controls.

---

# 26. Availability Incident

For denial-of-service or severe resource exhaustion:

- [ ] Identify affected service.
- [ ] Identify traffic source.
- [ ] Identify expensive endpoint.
- [ ] Apply rate limiting.
- [ ] Disable abusive feature if necessary.
- [ ] Scale resources if appropriate.
- [ ] Reduce expensive operations.
- [ ] Verify database health.
- [ ] Verify recovery.
- [ ] Review root cause.
- [ ] Add resource limits.

---

# 27. Supply-Chain Incident

If a dependency is compromised:

- [ ] Identify package.
- [ ] Identify affected version.
- [ ] Determine whether deployed.
- [ ] Determine whether code executed.
- [ ] Determine whether secrets were accessible.
- [ ] Determine whether production was affected.
- [ ] Remove/upgrade dependency.
- [ ] Rotate potentially exposed secrets.
- [ ] Rebuild from trusted source.
- [ ] Review CI/CD logs.
- [ ] Review build artifacts.
- [ ] Review deployment integrity.
- [ ] Add dependency-control measures.

The CI/CD pipeline itself must be treated as a critical production asset because compromise of the pipeline can provide access to source, artifacts, secrets, or deployment systems.

---

# 28. Investigation

The investigation should determine:

### What

- What vulnerability was exploited?
- What behavior occurred?
- What systems were affected?

### When

- When did exploitation begin?
- When was it detected?
- When did containment occur?

### Who

- Which accounts were involved?
- Which systems were involved?
- Which users may be affected?

### How

- What was the initial entry point?
- What trust boundary was crossed?
- What security control failed?
- Was there lateral movement?

### Impact

- What data was accessed?
- What data was changed?
- What service was disrupted?
- What privacy impact exists?

---

# 29. Evidence Preservation

Potential evidence:

- Application logs
- Authentication logs
- Authorization logs
- Audit logs
- Deployment history
- Git commits
- CI/CD logs
- Database audit records
- Infrastructure logs
- Network metadata
- Error logs
- Browser/network evidence where available
- Security scanner output

Do not collect more user data than necessary.

---

# 30. Evidence Integrity

Evidence should be:

- Time-stamped
- Identified
- Access-controlled
- Protected against unnecessary modification
- Stored separately where appropriate
- Associated with the incident ID

Do not place sensitive evidence into normal public logs.

---

# 31. Investigation Timeline

Every significant incident should maintain:

```text id="2t4o5c"
[Time] Detection
[Time] Initial triage
[Time] Severity assigned
[Time] Containment started
[Time] Containment completed
[Time] Root cause identified
[Time] Fix implemented
[Time] Verification started
[Time] Service restored
[Time] Incident closed
```

---

# 32. Root Cause Analysis

Do not stop at:

```text id="3ew8n4"
"The attacker exploited XSS."
```

Ask:

```text id="g9h8dr"
Why was XSS possible?
Why was unsafe content accepted?
Why was it not sanitized?
Why did testing not detect it?
Why did code review not detect it?
Why did architecture allow the path?
```

The goal is to identify systemic weaknesses.

---

# 33. Five-Why Analysis

For significant incidents:

```text id="d4e7o6"
Problem:
[Incident]

Why 1:
[Reason]

Why 2:
[Reason]

Why 3:
[Reason]

Why 4:
[Reason]

Why 5:
[Reason]

Root Cause:
[Root cause]
```

---

# 34. Eradication

Eradication means removing the cause of the incident.

Examples:

- Remove vulnerable code.
- Update vulnerable dependency.
- Remove compromised account.
- Rotate credentials.
- Remove malicious content.
- Fix authorization logic.
- Fix input validation.
- Remove insecure configuration.
- Patch infrastructure.
- Remove persistence mechanism.

---

# 35. Recovery

Recovery must verify that the system is safe before normal operation resumes.

Verify:

- [ ] Vulnerability fixed.
- [ ] Compromise removed.
- [ ] Credentials rotated where needed.
- [ ] Sessions invalidated where needed.
- [ ] Data integrity verified.
- [ ] Backups verified.
- [ ] Tests pass.
- [ ] Security tests pass.
- [ ] Privacy tests pass.
- [ ] Monitoring restored.
- [ ] Relevant feature manually verified.

---

# 36. Recovery Verification

Do not immediately assume:

```text id="r5x0gt"
Patch applied = incident resolved
```

Instead:

```text id="w4s9jh"
Patch
↓
Test exploit
↓
Verify exploit fails
↓
Run regression suite
↓
Review related attack paths
↓
Verify monitoring
↓
Restore service
```

---

# 37. Re-enabling a Disabled Tool

A disabled tool can only be re-enabled when:

- [ ] Root cause identified.
- [ ] Vulnerability fixed.
- [ ] Relevant security tests pass.
- [ ] Regression tests pass.
- [ ] Privacy tests pass.
- [ ] Performance impact reviewed.
- [ ] Production behavior verified.
- [ ] Re-enable decision documented.

---

# 38. Communication

Incident communication must be:

- Accurate
- Timely
- Controlled
- Evidence-based
- Appropriate to the audience

Do not speculate publicly.

Do not disclose:

- Exploit details unnecessarily
- Credentials
- Tokens
- Private user data
- Internal secrets
- Sensitive infrastructure information

---

# 39. User Impact Assessment

For each incident determine:

```text id="8o5qtu"
Affected users:
[Unknown / Number / Range]

Affected data:
[Type]

Exposure:
[Possible / Confirmed]

Duration:
[Time range]

Integrity impact:
[Yes / No / Unknown]

Availability impact:
[Yes / No / Unknown]

Privacy impact:
[Yes / No / Unknown]
```

---

# 40. Privacy Exposure Assessment

If user files may have been exposed:

Determine:

- [ ] Which tool?
- [ ] Which version?
- [ ] Which deployment?
- [ ] Which dates?
- [ ] Which browsers if relevant?
- [ ] Which request path?
- [ ] Whether file bytes were transmitted.
- [ ] Whether files were stored.
- [ ] Whether files were logged.
- [ ] Whether third parties received them.
- [ ] Whether backups contain them.
- [ ] Whether deletion occurred.
- [ ] Whether exposure is confirmed or only suspected.

---

# 41. Third-Party Exposure

If an external service received data unexpectedly:

- [ ] Identify service.
- [ ] Identify transmitted data.
- [ ] Identify transmission time.
- [ ] Identify affected users if possible.
- [ ] Review provider logs where available.
- [ ] Request deletion where appropriate.
- [ ] Disable integration if required.
- [ ] Remove unnecessary integration path.
- [ ] Document findings.

---

# 42. Incident Classification Matrix

| Incident | Typical Severity | Immediate Action |
|---|---|---|
| Authentication bypass | Critical | Contain immediately |
| Authorization bypass | Critical/High | Restrict affected resource |
| Public file upload violation | Critical | Disable affected tool |
| Secret exposure | Critical/High | Revoke and rotate |
| Stored XSS | High | Remove/fix content path |
| IDOR | High | Restrict endpoint |
| SQL injection | Critical/High | Contain immediately |
| Malicious SVG exploit | High | Disable affected path |
| Dependency compromise | Critical/High | Isolate and rebuild |
| Data corruption | High/Critical | Stop writes and recover |
| DoS | Medium/High | Rate-limit/contain |
| Minor information disclosure | Low/Medium | Fix and monitor |

Severity is ultimately determined by actual impact.

---

# 43. Security Incident Testing

After remediation:

- [ ] Original exploit reproduced.
- [ ] Original exploit now fails.
- [ ] Regression test added.
- [ ] Related attack paths tested.
- [ ] Similar code paths reviewed.
- [ ] Full relevant security suite passed.

---

# 44. Privacy Incident Testing

After a privacy incident:

- [ ] Original transmission reproduced in controlled environment.
- [ ] Fix verified.
- [ ] Network requests inspected.
- [ ] Third-party requests inspected.
- [ ] Error paths inspected.
- [ ] Cancellation paths inspected.
- [ ] Batch paths inspected.
- [ ] All affected tools retested.
- [ ] Privacy invariant verified.

---

# 45. Post-Incident Review

Every Critical and High incident requires a post-incident review.

Review:

1. What happened?
2. Why did it happen?
3. Why was it possible?
4. Why was it not detected earlier?
5. Which control failed?
6. Which test failed to detect it?
7. What was the impact?
8. What was done to contain it?
9. What fixed it?
10. What prevents recurrence?

---

# 46. Lessons Learned

Lessons must produce concrete actions.

Bad:

```text id="zqj2x7"
"Be more careful."
```

Good:

```text id="x5ph5g"
Add authorization regression suite for all media endpoints.
```

Every significant lesson should become one or more:

- Code changes
- Tests
- Documentation changes
- Monitoring improvements
- Architecture changes
- Process changes

---

# 47. Threat Model Update

After a significant security incident:

- [ ] `hack.md` reviewed.
- [ ] Attack path added or updated.
- [ ] Risk classification updated.
- [ ] Security controls updated.
- [ ] Test coverage updated.
- [ ] `checklist.md` updated where necessary.
- [ ] `testing.md` updated where necessary.

---

# 48. Architecture Review After Incident

Ask:

- Did architecture enable the vulnerability?
- Was a trust boundary incorrect?
- Was a module boundary violated?
- Was sensitive logic duplicated?
- Was a security check performed in the wrong layer?
- Was server-side authorization missing?
- Was browser/server separation violated?

If architecture contributed:

- [ ] Architecture change documented.
- [ ] Migration plan created.
- [ ] Tests added.
- [ ] Regression risk evaluated.

---

# 49. Documentation Update

After significant incidents, update relevant documents:

- `security.md`
- `auth.md`
- `data.md`
- `hack.md`
- `checklist.md`
- `testing.md`
- `architecture.md`
- Feature documentation
- Tool documentation

Do not leave known attack paths undocumented.

---

# 50. Incident Closure Criteria

An incident can be closed only when:

- [ ] Threat contained.
- [ ] Root cause identified or sufficiently understood.
- [ ] Vulnerability fixed.
- [ ] Exploit no longer works.
- [ ] Regression test exists.
- [ ] Relevant security tests pass.
- [ ] Relevant privacy tests pass.
- [ ] Data integrity verified.
- [ ] Recovery verified.
- [ ] Monitoring restored.
- [ ] Documentation updated.
- [ ] Follow-up actions assigned.

---

# 51. Unresolved Incidents

An incident must remain open if:

- Root cause remains unknown.
- Exploitation may still be active.
- Vulnerability remains exploitable.
- User impact remains unknown and material.
- Recovery is incomplete.
- Evidence has not been sufficiently reviewed.
- Required remediation is incomplete.

Do not close incidents merely because immediate symptoms disappeared.

---

# 52. Incident Metrics

Track where practical:

- Time to detection
- Time to triage
- Time to containment
- Time to remediation
- Time to recovery
- Number of affected users
- Number of affected systems
- Number of repeated incidents
- Number of vulnerabilities discovered
- Number of incidents caused by regressions

The purpose of metrics is improvement, not blame.

---

# 53. Recurring Incident Analysis

If the same class of incident occurs more than once:

- [ ] Investigate systemic cause.
- [ ] Review architecture.
- [ ] Review testing.
- [ ] Review monitoring.
- [ ] Review development process.
- [ ] Review documentation.
- [ ] Add stronger preventive controls.

Repeated incidents indicate that the previous remediation was insufficient.

---

# 54. Emergency Deployment

For critical vulnerabilities:

An emergency deployment may bypass normal release cadence but must not bypass security verification.

Minimum emergency process:

```text id="r4z4cz"
Identify
↓
Contain
↓
Implement minimal safe fix
↓
Run targeted tests
↓
Run security test
↓
Deploy
↓
Verify
↓
Run broader regression
↓
Document
```

The emergency process must not become a normal shortcut.

---

# 55. Emergency Rollback

Rollback may be required when:

- A deployment introduces critical vulnerability.
- Data corruption occurs.
- Authentication breaks.
- Authorization breaks.
- Privacy invariant is violated.
- Core processing becomes unsafe.

Rollback must be verified afterward.

---

# 56. Rollback Checklist

- [ ] Previous known-good version identified.
- [ ] Rollback artifact verified.
- [ ] Database compatibility reviewed.
- [ ] Configuration compatibility reviewed.
- [ ] Rollback executed.
- [ ] Smoke tests passed.
- [ ] Security tests passed.
- [ ] Privacy tests passed.
- [ ] Monitoring checked.
- [ ] Incident record updated.

---

# 57. Backup Recovery

If recovery requires backup restoration:

- [ ] Correct backup identified.
- [ ] Backup integrity checked.
- [ ] Backup date verified.
- [ ] Restoration tested where possible.
- [ ] Data consistency verified.
- [ ] Application compatibility verified.
- [ ] Security state verified.
- [ ] Missing data identified.
- [ ] Recovery documented.

---

# 58. Compromised Session Response

If session compromise is suspected:

- [ ] Identify session mechanism.
- [ ] Identify affected sessions.
- [ ] Invalidate sessions.
- [ ] Review authentication logs.
- [ ] Review authorization logs.
- [ ] Determine session theft mechanism.
- [ ] Fix vulnerability.
- [ ] Add regression tests.
- [ ] Verify old sessions cannot authenticate.

---

# 59. Compromised Administrator Response

If an administrator account is suspected compromised:

Immediately consider:

- [ ] Disable account.
- [ ] Invalidate active sessions.
- [ ] Reset credentials.
- [ ] Rotate associated secrets.
- [ ] Review recent admin actions.
- [ ] Review media changes.
- [ ] Review page changes.
- [ ] Review blog changes.
- [ ] Review settings changes.
- [ ] Review user/role changes.
- [ ] Review audit logs.

---

# 60. Malicious Content Response

If malicious content is published:

- [ ] Unpublish content.
- [ ] Preserve evidence.
- [ ] Identify publication path.
- [ ] Identify affected pages/users.
- [ ] Review authentication events.
- [ ] Review authorization events.
- [ ] Fix content validation/sanitization.
- [ ] Review similar content.
- [ ] Add regression tests.

---

# 61. Audit Log Integrity

Audit logs are important during incidents.

Therefore:

- [ ] Ordinary users cannot modify audit logs.
- [ ] Administrators cannot casually erase historical audit records.
- [ ] Log access is restricted.
- [ ] Sensitive logs are protected.
- [ ] Log retention is defined.
- [ ] Log timestamps are reliable.
- [ ] Log correlation is possible.

---

# 62. Monitoring After Recovery

After resolving an incident:

Monitor the affected area more closely.

Check:

- Authentication anomalies
- Authorization failures
- Error rates
- Unexpected traffic
- Resource usage
- File-processing failures
- Database anomalies
- Suspicious admin actions

Continue enhanced monitoring until confidence is restored.

---

# 63. No Silent Fixes

Never:

```text id="x1b8yd"
Discover vulnerability
↓
Patch silently
↓
Delete evidence
↓
Continue
```

Instead:

```text id="4h4f88"
Discover
↓
Record
↓
Assess
↓
Contain
↓
Fix
↓
Test
↓
Document
↓
Improve
```

---

# 64. No Evidence Destruction

Do not delete logs, files, database records, deployment artifacts, or other potentially relevant evidence solely to make the incident disappear.

Evidence retention must balance:

- Investigation needs
- Privacy
- Data minimization
- Legal/organizational requirements
- Storage constraints

---

# 65. User File Evidence

Because public files are intended to remain local:

If a file is unexpectedly transmitted:

- Do not casually copy or redistribute the file.
- Preserve only what is necessary.
- Prefer metadata and controlled test fixtures for investigation.
- Restrict access to any required evidence.
- Follow the documented data-retention policy.
- Delete unnecessary copies after investigation.

---

# 66. Third-Party Incident

If a vendor or dependency reports a security incident:

- [ ] Identify affected component.
- [ ] Identify affected versions.
- [ ] Check whether used by project.
- [ ] Check whether deployed.
- [ ] Check exposure window.
- [ ] Review vendor advisory.
- [ ] Update or disable component.
- [ ] Rotate credentials if necessary.
- [ ] Run relevant security tests.
- [ ] Document outcome.

---

# 67. Vulnerability Disclosure

If an external security researcher reports a vulnerability:

Record:

- Report date
- Vulnerability
- Affected component
- Reproduction information
- Severity
- Reporter contact where provided
- Remediation
- Verification
- Disclosure decision

Do not dismiss a report merely because exploitation has not yet been observed.

---

# 68. Vulnerability Disclosure Response

Initial response:

```text id="d5f2be"
Receive report
↓
Acknowledge
↓
Validate
↓
Assess severity
↓
Contain if required
↓
Fix
↓
Test
↓
Communicate resolution
```

---

# 69. Security Contact

The production project should maintain a dedicated security-reporting path.

Requirements:

- [ ] Security contact exists.
- [ ] Security reports are not mixed with normal feature requests where avoidable.
- [ ] Reports are handled confidentially.
- [ ] Report tracking exists.
- [ ] Response ownership is defined.

---

# 70. Incident Communication Record

For significant incidents:

```text id="c0p0l9"
Communication:
[Description]

Audience:
[Internal / Users / Vendor / Other]

Time:
[Time]

Approved By:
[Owner]

Content:
[Summary]
```

Do not publish unverified technical claims.

---

# 71. Legal and Regulatory Review

If an incident may involve personal data or legal obligations:

- [ ] Identify applicable requirements.
- [ ] Preserve relevant evidence.
- [ ] Escalate to appropriate legal/privacy support.
- [ ] Determine notification requirements.
- [ ] Record decisions.

This document does not replace legal advice.

---

# 72. Incident Playbook: Public File Privacy Violation

```text id="t4q7gr"
DETECT
↓
Confirm unexpected file transmission
↓
Identify affected tool
↓
DISABLE TOOL
↓
Preserve logs
↓
Determine exposure window
↓
Determine whether files were stored
↓
Determine whether third parties received data
↓
FIX TRANSMISSION PATH
↓
ADD PRIVACY TEST
↓
RUN FULL TOOL TEST
↓
RUN NETWORK INSPECTION
↓
VERIFY NO UPLOAD
↓
RESTORE TOOL
↓
DOCUMENT
```

---

# 73. Incident Playbook: Authentication Bypass

```text id="w1r0v8"
DETECT
↓
Confirm bypass
↓
DISABLE affected endpoint
↓
Invalidate affected sessions
↓
Review access logs
↓
Identify affected accounts
↓
Fix authentication logic
↓
Add regression tests
↓
Test related authentication paths
↓
Verify no bypass
↓
Restore service
↓
Document
```

---

# 74. Incident Playbook: Authorization Bypass

```text id="h0v7w6"
DETECT
↓
Identify resource
↓
Identify affected roles
↓
Restrict endpoint
↓
Review accessed resources
↓
Fix server-side authorization
↓
Test IDOR
↓
Test privilege escalation
↓
Test related endpoints
↓
Verify
↓
Restore
```

---

# 75. Incident Playbook: Secret Exposure

```text id="q3t1o5"
DETECT
↓
Assume secret compromised
↓
REVOKE
↓
ROTATE
↓
Update deployment
↓
Verify new secret
↓
Search for misuse
↓
Review Git history
↓
Remove exposure
↓
Run security tests
↓
Document
```

---

# 76. Incident Playbook: Malicious Dependency

```text id="j2e4g8"
DETECT
↓
Identify dependency/version
↓
Determine deployment exposure
↓
Disable/remove dependency
↓
Revoke exposed secrets
↓
Rebuild from trusted source
↓
Review CI/CD
↓
Review artifacts
↓
Deploy clean build
↓
Verify
↓
Document
```

---

# 77. Incident Playbook: Database Compromise

```text id="z7p3s2"
DETECT
↓
Restrict database access
↓
Preserve evidence
↓
Identify access path
↓
Rotate credentials
↓
Determine affected records
↓
Verify integrity
↓
Restore if necessary
↓
Patch entry point
↓
Test
↓
Monitor
↓
Document
```

---

# 78. Incident Playbook: XSS

```text id="n5v2k4"
DETECT
↓
Identify injection point
↓
Remove malicious content
↓
Restrict vulnerable path
↓
Fix sanitization/encoding
↓
Review related paths
↓
Add regression tests
↓
Review stored content
↓
Verify
↓
Restore
```

---

# 79. Incident Playbook: DoS / Resource Exhaustion

```text id="r6c1x9"
DETECT
↓
Identify expensive operation
↓
Rate-limit
↓
Disable abusive path if necessary
↓
Restore service capacity
↓
Add resource limits
↓
Test maximum inputs
↓
Test repeated inputs
↓
Verify
↓
Monitor
```

---

# 80. Incident Response Roles

For a small project, one person may perform multiple roles.

Required responsibilities:

### Incident Owner

Responsible for overall incident handling.

### Technical Responder

Responsible for investigation and remediation.

### Security Reviewer

Responsible for security verification.

### Release Owner

Responsible for deployment/recovery decisions.

### Communication Owner

Responsible for controlled communication.

Roles may be combined, but responsibilities must remain clear.

---

# 81. Separation of Duties

For Critical incidents:

Where practical:

- [ ] Person implementing the fix is not the only person verifying it.
- [ ] Security verification is independently reviewed.
- [ ] Production deployment is independently checked.
- [ ] Recovery is independently verified.

---

# 82. Incident Decision Rules

When uncertain:

### If user safety is uncertain

Prefer containment.

### If privacy exposure is uncertain

Treat exposure as possible until disproven.

### If a credential is exposed

Treat it as compromised.

### If authorization is uncertain

Deny access.

### If a parser is suspected vulnerable

Restrict affected processing until verified.

### If deployment integrity is uncertain

Prefer known-good rebuild/rollback.

---

# 83. Fail-Secure Principle

When the application cannot safely determine whether an operation is authorized:

```text id="q0n1e5"
DENY
```

When the application cannot safely determine whether an uploaded file is acceptable:

```text id="r3f2u7"
REJECT
```

When the application cannot safely determine whether a public processing request is private:

```text id="k4s5v6"
DO NOT TRANSMIT
```

The system should fail securely rather than silently weaken a security boundary.

---

# 84. Incident Response Testing

Incident response itself must be tested.

At least periodically, simulate:

- [ ] Secret exposure.
- [ ] Authentication compromise.
- [ ] Authorization bypass.
- [ ] Malicious file.
- [ ] Public file upload violation.
- [ ] Database recovery.
- [ ] Deployment rollback.
- [ ] Dependency compromise.

Verify the team/process can execute the playbook.

---

# 85. Tabletop Exercise

A tabletop exercise should simulate:

```text id="q5x9z0"
Incident detected
↓
Who responds?
↓
Who decides severity?
↓
Who disables the tool?
↓
Who investigates?
↓
Who deploys the fix?
↓
Who verifies?
↓
Who communicates?
↓
Who closes the incident?
```

Record gaps discovered during the exercise.

---

# 86. Post-Incident Action Items

Each significant incident should generate:

```text id="c7v3m1"
Action:
[Action]

Reason:
[Reason]

Owner:
[Owner]

Priority:
Critical / High / Medium / Low

Due:
[Date]

Status:
Open / In Progress / Complete

Verification:
[Evidence]
```

---

# 87. Continuous Improvement

Incident response must feed back into:

```text id="b3x7k9"
Security
Architecture
Testing
Monitoring
Documentation
Development Rules
Roadmap
```

NIST's current guidance explicitly emphasizes continuous improvement, with lessons from incident-response and risk-management activities feeding back into future security work.

---

# 88. Incident Closure Report

Template:

```text id="9w4t2m"
# Incident Closure Report

Incident ID:
IR-[YYYY]-[NUMBER]

Severity:
[Severity]

Summary:
[Summary]

Timeline:
[Timeline]

Affected Systems:
[List]

Affected Users:
[Number / Unknown]

Affected Data:
[List]

Root Cause:
[Cause]

Attack Path:
[Path]

Containment:
[Actions]

Remediation:
[Actions]

Security Tests:
PASS / FAIL

Privacy Tests:
PASS / FAIL

Regression Tests:
PASS / FAIL

Recovery:
[Result]

Lessons Learned:
[List]

Documentation Updated:
[List]

Follow-up Actions:
[List]

Final Status:
CLOSED
```

---

# 89. Closure Requirements

An incident may not be marked `CLOSED` unless:

```text id="u8d5f0"
Containment complete
+
Root cause understood
+
Fix deployed
+
Exploit retested
+
Security tests passed
+
Privacy tests passed where relevant
+
Regression tests passed
+
Recovery verified
+
Documentation updated
+
Follow-up actions assigned
```

---

# 90. Critical Incident Rule

For Critical incidents:

```text id="w5r8y1"
No silent resolution.
No undocumented remediation.
No untested emergency fix.
No immediate re-enable without verification.
No deletion of evidence to simplify investigation.
No false statement that the incident is resolved.
```

---

# 91. Security Incident Invariants

The following must remain true:

```text id="p7s4x2"
Security incidents are recorded.

Critical incidents are contained immediately.

Compromised secrets are revoked.

Compromised sessions are invalidated.

Authorization failures are investigated.

Public file privacy violations are treated as critical until assessed.

Security fixes receive regression tests.

Incident findings update the threat model when appropriate.

Incident findings update testing when appropriate.

Incident findings update architecture when appropriate.

Incident findings are not hidden by deleting evidence.

No incident is considered resolved merely because the symptom disappeared.
```

---

# 92. Relationship With Other Documents

This document depends on:

```text id="r8h2k5"
PROJECT_RULES.md
PRD.md
architecture.md
security.md
auth.md
data.md
hack.md
checklist.md
testing.md
```

When an incident reveals a weakness:

- `hack.md` → update attack path
- `security.md` → update security control
- `auth.md` → update authentication/session rule
- `data.md` → update data lifecycle
- `checklist.md` → add prevention check
- `testing.md` → add regression/security test
- `architecture.md` → update architecture if required

---

# 93. External Baseline

This project uses **NIST SP 800-61 Rev. 3** as the current external incident-response reference. Rev. 3 was finalized in April 2025 and supersedes Rev. 2.

OWASP guidance is also used for application-security incident handling, especially around secrets, logging, secure code review, and security monitoring.

These external references supplement the project documentation and do not override:

1. `PROJECT_RULES.md`
2. `PRD.md`
3. Approved architecture
4. Approved feature documentation

---

# 94. Final Incident Response Principle

The project must assume that security failures are possible.

The goal is therefore not:

```text id="e8f1w3"
"Nothing will ever go wrong."
```

The goal is:

```text id="m2k6r4"
Detect quickly
→
Contain safely
→
Investigate accurately
→
Fix correctly
→
Verify completely
→
Recover safely
→
Learn
→
Improve
```

A mature system is not defined only by preventing attacks.

It is also defined by how safely and transparently it responds when prevention fails.

**End of `incident-response.md`**
