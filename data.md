# Data Storage, Retention & Deletion Policy

**Document:** `data.md`  
**Version:** 1.0  
**Status:** Mandatory  
**Scope:** Entire application  
**Priority:** Critical

---

# 1. Purpose

This document defines:

- what data the application stores
- why each data type is stored
- where it is stored
- who can access it
- how long it is retained
- when it is deleted
- how deletion is handled
- what data must never be stored

The application must follow:

> If data is not required for the product to function, it should not be collected or stored.

Data minimization is a core security principle. OWASP explicitly recommends minimizing storage of sensitive information because information that is not stored cannot later be exposed through a storage breach.

---

# 2. Data Classification

All application data must belong to one of these categories:

```text
PUBLIC
INTERNAL
CONFIDENTIAL
SENSITIVE
SECRET
```

## PUBLIC

Information intentionally exposed to everyone.

Examples:

- published pages
- published blog posts
- public tool descriptions
- public images
- public SEO metadata

---

## INTERNAL

Application information that is not intended for public users.

Examples:

- internal tool configuration
- internal system settings
- non-sensitive operational metadata

---

## CONFIDENTIAL

Information that should only be accessible to authorized administrators.

Examples:

- draft pages
- unpublished blog posts
- internal audit information
- administrator account metadata

---

## SENSITIVE

Information that could cause meaningful harm if exposed.

Examples:

- administrator email addresses
- security audit information
- session-related metadata
- potentially identifying operational information

---

## SECRET

Information that must never be exposed to users or stored in ordinary application content.

Examples:

- passwords
- password reset secrets
- session secrets
- API keys
- encryption keys
- database credentials
- private signing keys

---

# 3. Data Ownership

Every stored data type must have a clear owner.

Conceptually:

```text
Authentication
    ↓
Users
Sessions
Password security

CMS
    ↓
Pages
Blocks
Revisions

Media
    ↓
Media metadata
Media files

Blog
    ↓
Posts
Categories
Tags

SEO
    ↓
SEO configuration
Structured data

Security
    ↓
Audit logs
Security events
```

One subsystem must not directly modify another subsystem's protected data without going through its approved service boundary.

---

# 4. Database

PostgreSQL is the primary structured-data database.

Prisma is the application's database access layer.

The database stores metadata and structured application state.

The database must not be treated as a general-purpose file storage system.

---

# 5. PostgreSQL Data

The initial conceptual database contains entities such as:

```text
User
Session
PasswordResetToken
Page
PageRevision
Block
BlockTemplate
Media
MediaFolder
Post
Category
Tag
Tool
ToolCategory
SEO
Schema
AuditLog
Setting
```

The final Prisma schema must be documented separately before implementation.

---

# 6. User Data

The `User` entity may contain only information required for administrator authentication and management.

Initial fields may include:

```text
id
email
passwordHash
role
status
failedLoginCount
lockedUntil
passwordChangedAt
lastLoginAt
createdAt
updatedAt
```

Additional fields require justification.

Do not create generic profile fields merely because they might be useful later.

---

# 7. Password Data

The application stores:

```text
passwordHash
```

It must never store:

```text
password
```

Passwords must be securely hashed before persistence.

Password hashing requirements are defined in `auth.md`.

---

# 8. Session Data

Session data may include:

```text
id
userId
sessionVerifier / equivalent secure representation
createdAt
expiresAt
lastUsedAt
revokedAt
```

The exact schema depends on the selected authentication implementation.

Session secrets must not be stored in logs.

Session data is security-sensitive and must be protected accordingly.

---

# 9. Password Reset Data

If password reset is implemented, the database may store:

```text
id
userId
tokenVerifier
expiresAt
usedAt
createdAt
```

The actual reset secret must not be stored as a reusable plaintext value where a secure verifier-based architecture is possible.

Expired and consumed reset records must be removed according to the retention policy.

---

# 10. Public Processing Files

This is one of the most important rules in the entire project.

Files processed by public tools must **not** be stored on the application server.

Normal flow:

```text
User File
   ↓
Browser
   ↓
Browser Processing
   ↓
Result
   ↓
Browser Download
```

No database record should be created merely because a user processed a file.

No server-side file archive should be created for public processing.

---

# 11. Public Processing Metadata

The application should not persist unnecessary metadata about files processed by public tools.

Avoid storing:

```text
original filename
file contents
file hash
file size
file history
conversion history
processed timestamp
```

unless a future approved feature explicitly requires a specific item.

---

# 12. Browser Temporary Data

Browser-side processing may temporarily use:

- memory
- Blob objects
- ArrayBuffers
- temporary worker memory
- browser-managed temporary resources

The application should release these resources when they are no longer needed.

Examples:

```text
URL.revokeObjectURL(...)
```

when applicable.

Large buffers should not remain referenced after processing completes.

---

# 13. Browser Storage

The public tools should avoid persistent browser storage unless required.

Do not store user processing files in:

```text
localStorage
sessionStorage
IndexedDB
Cache Storage
```

unless a specific approved feature requires it.

If browser persistence is introduced, its:

- purpose
- data type
- retention
- deletion behavior
- privacy impact

must be documented.

---

# 14. CMS Media

CMS administrator-uploaded media is different from public processing files.

CMS media may be stored server-side.

Conceptually:

```text
Media
├── Database metadata
└── Object/File Storage
       └── actual file
```

The database should primarily store metadata.

---

# 15. Media Metadata

Media metadata may include:

```text
id
filename
storageKey
mimeType
size
width
height
altText
title
caption
description
folderId
createdAt
updatedAt
```

Only fields required by the Media Library should be implemented.

---

# 16. Media File Storage

Actual media files should be stored in controlled object/file storage rather than directly inside PostgreSQL.

The storage layer must prevent:

- arbitrary filesystem access
- path traversal
- unauthorized replacement
- unauthorized deletion
- accidental public exposure

---

# 17. Public vs Private Media

Every media object must have a clearly defined visibility state.

Conceptually:

```text
PUBLIC
PRIVATE
```

Public media may be referenced by published pages.

Private media must require appropriate authorization.

The application must never infer visibility merely from the filename or URL.

---

# 18. Deleted Media

When media is deleted:

1. database metadata must be removed or marked according to the approved deletion strategy
2. underlying storage must be handled
3. references must be checked where necessary
4. cached copies must be considered
5. deletion should be auditable

The audit log should record the deletion event without copying the deleted file contents. OWASP recommends recording deletion actions without putting sensitive contents into the log.

---

# 19. Page Data

Pages may contain:

```text
id
slug
title
status
publishedAt
createdAt
updatedAt
```

and structured page-builder content.

Page content must be stored as structured data rather than arbitrary executable HTML.

---

# 20. Page Builder Data

A block may contain:

```text
id
type
version
content
settings
styles
responsive
```

Only defined properties should be accepted.

Unknown or malformed block structures must fail safely.

---

# 21. Page Revisions

Page revisions may store historical page content.

Example:

```text
Page
 ├── Current version
 ├── Revision 1
 ├── Revision 2
 └── Revision 3
```

Revision storage is necessary for:

- rollback
- content recovery
- publishing safety
- administrator auditing

However, revisions must not grow without control.

A retention policy must be implemented.

---

# 22. Revision Retention

The system should retain a reasonable number of revisions rather than keeping unlimited history forever.

The exact retention policy must be configurable.

Example policy:

```text
Recent revisions → retained
Old redundant revisions → eligible for cleanup
```

The exact number must be decided during CMS implementation.

---

# 23. Draft Content

Draft pages and posts are confidential application content.

They must not accidentally appear in:

- public pages
- sitemap
- RSS
- search indexes
- public APIs
- structured data
- public media references

unless explicitly published.

---

# 24. Blog Data

Blog records may include:

```text
id
title
slug
excerpt
content
status
featuredMediaId
publishedAt
createdAt
updatedAt
```

Categories and tags should be stored as structured relationships.

---

# 25. Blog Deletion

Deleting a blog post must define what happens to:

- the post
- revisions
- featured media
- categories
- tags
- internal references
- SEO metadata
- structured data

Deletion must not silently delete unrelated resources.

---

# 26. SEO Data

SEO metadata may include:

```text
title
description
canonical
robots
openGraph
social metadata
schema
```

SEO content belongs to the page/content system.

It must not contain:

- passwords
- API keys
- private credentials
- executable secrets

---

# 27. Schema Data

Structured data may be stored as validated structured JSON.

Examples:

```text
WebSite
WebPage
Organization
Article
BlogPosting
FAQPage
BreadcrumbList
SoftwareApplication
HowTo
```

Only approved schema types should be supported initially.

---

# 28. Settings

System settings may contain:

```text
site title
site description
navigation configuration
feature flags
tool configuration
SEO defaults
UI configuration
```

Settings must be classified according to sensitivity.

Security secrets must not be stored as ordinary CMS settings.

---

# 29. Environment Secrets

The following belong outside the database unless a dedicated secret-management architecture is explicitly approved:

```text
DATABASE_PASSWORD
AUTH_SECRET
API_KEYS
ENCRYPTION_KEYS
PRIVATE_KEYS
STORAGE_CREDENTIALS
```

They should be supplied through secure environment/secret-management mechanisms.

Next.js documentation explicitly warns that environment files containing secrets must be excluded from source control.

---

# 30. API Keys

API keys must:

- remain server-side
- never be sent to public users
- never be embedded in client bundles
- never be placed in public page content
- never be logged
- never be committed to Git

If a third-party API requires browser exposure of a public key, that key must be explicitly classified as public and must not provide privileged access.

---

# 31. Audit Logs

Audit logs may contain:

```text
id
actorUserId
eventType
targetType
targetId
timestamp
result
securityContext
```

They should not contain complete sensitive objects.

For example:

```text
GOOD:
Deleted media 8f72...

BAD:
Deleted media: [entire file contents]
```

---

# 32. Logging Data Minimization

Logs must avoid unnecessary personal or sensitive data.

OWASP recommends avoiding direct logging of session identifiers, access tokens, authentication passwords, encryption keys and unnecessary sensitive personal information.

---

# 33. IP Address Retention

IP addresses are potentially identifying operational data.

The application must not retain IP addresses indefinitely merely because they are available.

If IP retention is required for:

- authentication security
- abuse prevention
- incident investigation

the purpose and retention period must be documented.

If the data is no longer necessary, it should be removed or appropriately minimized.

---

# 34. User-Agent Data

User-agent data should only be retained when necessary for:

- security investigation
- debugging
- operational monitoring

It must not become an unnecessary permanent user profile.

---

# 35. Analytics Data

Analytics must follow data minimization.

The analytics system must not receive public processing files.

It must not receive:

- file contents
- raw binary data
- file buffers
- sensitive filenames
- administrator secrets

Tool analytics should preferably describe the action rather than the user's file.

Example:

```text
tool_opened: image-compressor
```

rather than:

```text
uploaded_file: passport.jpg
```

---

# 36. Third-Party Data Transfer

Before sending any data to a third-party service, determine:

1. What data is sent?
2. Why is it sent?
3. Is it necessary?
4. Is it sensitive?
5. Can the feature work without it?
6. How long does the third party retain it?
7. Does the third party use it for other purposes?

No public processing file may be sent to a third party unless explicitly approved.

---

# 37. Error Reporting

Error-reporting systems must not automatically capture:

- file contents
- passwords
- authentication tokens
- session cookies
- database credentials
- private CMS content
- sensitive filenames

Errors must be sanitized before external reporting.

---

# 38. Backups

Backups are copies of application data and therefore inherit the sensitivity of the original data.

Backups must be:

- access-controlled
- encrypted where appropriate
- monitored
- tested for restoration
- retained according to a defined policy
- deleted when they reach their retention limit

A deleted database record must not be assumed to have disappeared from every backup immediately.

---

# 39. Backup Retention

The system must define:

```text
backup frequency
backup retention
backup encryption
backup access
backup deletion
restore testing
```

These settings must be documented before production deployment.

---

# 40. Deletion Model

Deletion must be classified into:

```text
LOGICAL DELETION
PHYSICAL DELETION
EXPIRATION
ANONYMIZATION
```

The correct strategy depends on the data type.

---

# 41. Logical Deletion

Logical deletion may be used when operational recovery is required.

Example:

```text
deletedAt
```

However, logical deletion does not mean the data has been permanently deleted.

It must not be described to users as permanent deletion unless the underlying retention policy supports that claim.

---

# 42. Physical Deletion

Physical deletion means removing the data from active storage.

This may include:

- database record
- object storage
- temporary storage
- relevant indexes
- derived copies

Backups may remain until their documented retention period expires.

---

# 43. Expiration

Some data should expire automatically.

Examples:

- sessions
- password reset tokens
- temporary processing metadata if ever introduced
- rate-limit records
- temporary security records

Expired data must not remain indefinitely.

---

# 44. Data Deletion Jobs

Where data has a defined expiration period, automated cleanup should be used.

Examples:

```text
Expired Sessions
      ↓
Cleanup Job
      ↓
Deleted
```

Cleanup jobs must be:

- idempotent
- safe to retry
- observable
- tested

---

# 45. Public Processing Data Deletion

Because public files are processed locally, the preferred deletion strategy is:

```text
No server storage
```

rather than:

```text
Store → process → delete
```

The first architecture is preferable because there is no server-side file to accidentally retain.

---

# 46. Temporary Server Files

If a future approved feature requires temporary server-side files:

1. define why server processing is required
2. define maximum lifetime
3. store outside public web paths
4. generate non-user-controlled names
5. restrict permissions
6. delete automatically
7. test cleanup failures
8. document the exception to the browser-first architecture

---

# 47. Data Retention Matrix

Initial policy:

| Data | Location | Retention |
|---|---|---|
| Public page | PostgreSQL | Until changed/deleted |
| Published blog post | PostgreSQL | Until changed/deleted |
| Draft content | PostgreSQL | Until deleted/published |
| Page revisions | PostgreSQL | Configured revision policy |
| Admin user | PostgreSQL | While account exists |
| Password hash | PostgreSQL | While credential exists |
| Active session | PostgreSQL/session store | Until expiry/revocation |
| Password reset token | PostgreSQL | Until used/expired |
| CMS media metadata | PostgreSQL | While media exists |
| CMS media file | Object storage | While media exists |
| Public processing file | User browser | Temporary during processing |
| Public processing history | None by default | Not stored |
| Audit log | PostgreSQL/log store | Defined security retention |
| Rate-limit state | Server-side store | Short-lived |
| Temporary server file | Temporary storage | Minimum necessary |

---

# 48. Data Access Rules

Access must follow least privilege.

### Public users

Can access:

- published public content
- public tools
- public media

Cannot access:

- database
- admin content
- draft content
- audit logs
- sessions
- security settings

### Administrators

Can access only data permitted by their role.

### Application services

Must access only the tables/resources required for their operation.

---

# 49. Database Credentials

Database credentials must:

- not be public
- not be committed to Git
- not be returned by APIs
- not appear in logs
- use least privilege
- be rotated when necessary

---

# 50. Database Backups and Sensitive Data

Database backups may contain:

- password hashes
- administrator emails
- sessions
- audit logs
- unpublished content

Therefore backup access must be treated as privileged access.

---

# 51. Data Encryption

Sensitive data should use encryption at rest where appropriate and supported by the deployment architecture.

However:

> Encryption is not a reason to store unnecessary data.

The first protection is not collecting or storing unnecessary information.

OWASP similarly recommends minimizing sensitive storage before relying on encryption as the primary mitigation.

---

# 52. Encryption Key Separation

Encryption keys must not be stored alongside the encrypted data without appropriate protection.

Keys must have:

- controlled access
- rotation strategy
- backup/recovery strategy
- revocation procedure
- documented lifecycle

OWASP treats key lifecycle management, including compromise, revocation and destruction, as a distinct security concern.

---

# 53. Data Export

If an administrative export feature is introduced, exported data must be treated as sensitive.

Exports must:

- require authorization
- be generated securely
- not expose unrelated records
- avoid unnecessary data
- be protected from unauthorized access
- have controlled lifetime

---

# 54. Data Import

Imported CMS data must be validated before entering the database.

Imported content must not bypass:

- authorization
- validation
- XSS protections
- schema validation
- media security
- SEO validation

---

# 55. Data Integrity

Important data must not be trusted merely because it came from the database.

Application logic must enforce valid states.

Examples:

```text
publishedAt cannot imply publication if status = DRAFT
disabled user cannot have an active valid session
deleted media cannot remain publicly addressable through application metadata
```

---

# 56. Data Consistency

Operations affecting multiple resources should use appropriate database transactions where required.

Example:

```text
Publish Page
 ├── Update page
 ├── Create revision
 ├── Update publication metadata
 └── Update related SEO state
```

If these operations must succeed together, the implementation should use a transaction or another explicitly documented consistency strategy.

---

# 57. Cascading Deletion

Cascading deletion must never be enabled blindly.

Before deleting a record, determine whether related data should:

- also be deleted
- remain
- become orphaned
- be reassigned

Example:

Deleting a media file must not automatically delete every page that references it unless that behavior is explicitly designed.

---

# 58. Orphan Detection

The CMS should eventually be able to detect orphaned resources where useful.

Examples:

- media with no references
- unused media folders
- orphaned revisions
- orphaned tags

Cleanup must be explicit and safe.

---

# 59. Data in URLs

Sensitive information must never be placed in URLs.

Never place:

```text
password
session token
reset token
API key
private content
```

in query parameters or paths.

URLs can appear in:

- browser history
- server logs
- analytics
- referrer headers
- monitoring systems

---

# 60. Data in Client HTML

Sensitive information must not be unnecessarily embedded into HTML sent to the browser.

Examples:

```text
passwordHash
session secret
database credentials
private API keys
```

must never be exposed.

---

# 61. Data in JavaScript Bundles

Production JavaScript bundles must not contain:

- private API keys
- database credentials
- authentication secrets
- encryption keys
- administrator passwords

Public configuration must be clearly separated from secrets.

---

# 62. Data in Error Messages

Error messages must not expose:

- SQL queries
- filesystem paths
- database credentials
- tokens
- internal secrets
- private content

---

# 63. Data in Logs

Logs must be treated as a separate data store with their own:

- access control
- retention
- security
- deletion
- backup policy

Logging does not override data-minimization requirements.

---

# 64. Data in Development

Development environments must avoid unnecessary production data.

Developers should use:

```text
synthetic data
test accounts
dummy media
sanitized fixtures
```

instead of real sensitive user data.

---

# 65. Test Data

Test fixtures must not contain:

- real passwords
- real API keys
- real administrator credentials
- real personal information
- real user files

Secrets used during testing must be disposable.

---

# 66. Git Repository

The repository must never contain:

```text
.env
.env.production
database passwords
API keys
private keys
password reset secrets
production credentials
user uploads
production database dumps
```

Next.js documentation specifically recommends ensuring environment files containing database secrets are ignored by Git.

---

# 67. Data Breach Consideration

If sensitive data is exposed:

1. identify affected data
2. identify affected systems
3. revoke compromised credentials/secrets
4. invalidate affected sessions
5. contain the breach
6. investigate logs
7. fix the vulnerability
8. add regression tests
9. review data retention
10. document the incident

Data minimization reduces the potential impact of such an incident.

---

# 68. Data Deletion Verification

Deletion must be tested.

A deletion test should verify:

```text
Active record
      ↓
Delete
      ↓
Record unavailable
      ↓
Related storage handled
      ↓
Public access unavailable
      ↓
Audit event created where required
```

Deletion must not be considered successful merely because the UI says "Deleted."

---

# 69. Data Retention Review

Retention policies must be reviewed whenever:

- a new data type is introduced
- a new third-party service is added
- analytics changes
- a new CMS feature is added
- authentication changes
- a new storage provider is added
- a new logging system is added

---

# 70. New Data Type Rule

Before adding any new persistent data field, document:

```text
1. What is the data?
2. Why is it needed?
3. Who can access it?
4. Where is it stored?
5. How long is it retained?
6. How is it deleted?
7. What happens if it is exposed?
8. Can the feature work without storing it?
```

If these questions cannot be answered, the field should not be added.

---

# 71. Data Security Definition of Done

A data-related feature is complete only when:

- [ ] All stored data has a documented purpose
- [ ] Data classification exists
- [ ] Storage location is defined
- [ ] Access control is defined
- [ ] Retention is defined
- [ ] Deletion behavior is defined
- [ ] Sensitive data is minimized
- [ ] Secrets are not stored incorrectly
- [ ] Logs do not expose sensitive data
- [ ] Backups are considered
- [ ] Browser storage is reviewed
- [ ] Third-party transfer is reviewed
- [ ] Security tests pass
- [ ] Documentation is updated

---

# 72. Non-Negotiable Data Rules

1. Do not store public processing files on the server.
2. Do not store unnecessary user data.
3. Do not store plaintext passwords.
4. Do not store secrets in the database as ordinary content.
5. Do not store secrets in the client.
6. Do not store authentication tokens in logs.
7. Do not store sensitive data in URLs.
8. Do not retain temporary data indefinitely.
9. Do not expose draft CMS content publicly.
10. Do not assume database deletion removes backup copies immediately.
11. Do not add persistent fields without documenting their purpose.
12. Do not send public processing files to third parties.
13. Do not treat encryption as justification for unnecessary collection.
14. Do not describe logical deletion as permanent deletion.
15. Do not approve a data subsystem without a tested retention and deletion strategy.

---

# 73. Relationship With Other Documents

```text
PROJECT_RULES.md
       ↓
security.md
       ↓
auth.md
       ↓
data.md
       ↓
hack.md
       ↓
checklist.md
```

`security.md` defines general security.

`auth.md` defines authentication and session security.

`data.md` defines data lifecycle and storage.

`hack.md` will map realistic attack paths against the architecture and specify mitigations.

`checklist.md` will combine these requirements into an executable verification checklist.

---

# 74. Final Data Principle

The application must follow this rule:

> Do not collect what is unnecessary. Do not store what is unnecessary. Do not retain what is no longer necessary. Do not expose what must remain private.

For this specific product, the strongest privacy mechanism is architectural:

> Public file processing should happen in the browser so the server never receives the user's processing files in the first place.
