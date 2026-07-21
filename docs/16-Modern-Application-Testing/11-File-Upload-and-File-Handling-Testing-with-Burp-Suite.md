# File Upload & File Handling Testing with Burp Suite

> **Module 16 — Modern Application Testing**

> **Progress**

```text
████████████░░░ 12 / 15 Files
```

---

## Overview

* File upload functionality allows users to submit files that an application stores, processes, transforms, scans, displays, or makes available to other users.

* Common examples include:

  * Profile pictures.
  * Documents.
  * Attachments.
  * Identity verification files.
  * CSV imports.
  * PDF uploads.
  * Support-ticket attachments.
  * Media files.
  * Backup files.
  * Configuration imports.

* File handling creates a complex attack surface because an uploaded file may pass through several components:

  ```text
  Client

  ↓

  Upload Endpoint

  ↓

  Validation

  ↓

  Storage

  ↓

  Processing

  ↓

  Retrieval / Rendering

  ↓

  Other Users or Systems
  ```

* Security testing therefore requires more than checking whether a file can be uploaded.

* A professional assessment investigates:

  ```text
  What Files Are Accepted?

  How Are They Validated?

  Where Are They Stored?

  How Are They Renamed?

  Who Can Access Them?

  How Are They Served?

  Are They Processed?

  Can They Affect the Server or Other Users?
  ```

* Burp Suite helps capture multipart requests, modify metadata, test validation boundaries, replay uploads, inspect retrieval behavior, and compare application responses.

---

## Learning Objectives

* By the end of this lesson, you will be able to:

  * Understand the complete file-upload lifecycle.
  * Identify file upload and file handling attack surfaces.
  * Capture multipart upload requests in Burp Suite.
  * Understand `multipart/form-data`.
  * Distinguish filename, extension, MIME type, and file-content validation.
  * Establish legitimate upload baselines.
  * Test extension validation safely.
  * Test MIME-type validation.
  * Test content-signature validation.
  * Analyze filename handling and normalization.
  * Investigate server-side file renaming.
  * Test multiple-extension behavior.
  * Analyze file retrieval and download behavior.
  * Test file authorization controls.
  * Understand active versus passive file content.
  * Investigate image, document, archive, and import processing.
  * Understand parser and transformation risks.
  * Test overwrite and collision behavior.
  * Identify metadata-related risks.
  * Understand storage and execution separation.
  * Recognize file-upload race and asynchronous-processing considerations.
  * Validate impact without causing harmful execution.
  * Build a repeatable file-handling testing methodology.

---

## File Upload Attack Surface

* File upload functionality is not a single endpoint.

* Think of it as a lifecycle:

  ```text
  Select File

  ↓

  Client Validation

  ↓

  Upload Request

  ↓

  Server Validation

  ↓

  Storage

  ↓

  Processing

  ↓

  Retrieval

  ↓

  Rendering / Download

  ↓

  Deletion / Replacement
  ```

* Every stage may introduce different security risks.

---

## Core Security Questions

```text
What File Types Are Intended?

What File Types Are Actually Accepted?

Does the Server Validate Content?

Does the Server Trust the Filename?

Does the Server Trust Content-Type?

Can Uploaded Files Execute?

Can Uploaded Files Affect Other Users?

Can Files Be Accessed Without Authorization?

Can Existing Files Be Overwritten?

Are Uploaded Files Processed by Parsers?

Are Files Renamed Safely?

Are Files Stored Outside Executable Locations?
```

---

## Start With Intended Behavior

* Before testing, understand the legitimate feature.

* Record:

  ```text
  Feature

  Allowed File Types

  Maximum Size

  Minimum Size

  Filename Rules

  Number of Files

  Authentication Requirement

  Processing Behavior

  Retrieval Method

  Visibility

  Deletion Behavior
  ```

---

## File Upload Test Card

```text
Feature:

Endpoint:

User:

Role:

Allowed File Types:

Maximum Size:

Filename Rules:

Upload Method:

Storage Behavior:

Processing:

Retrieval URL:

Authorization:

Content-Disposition:

Content-Type on Retrieval:

Renamed?:

Publicly Accessible?:

Executable?:

Notes:
```

---

## Capturing an Upload in Burp

* Use:

  ```text
  Proxy

  ↓

  Intercept or HTTP History

  ↓

  Perform Legitimate Upload

  ↓

  Locate Upload Request

  ↓

  Send to Repeater
  ```

* Always begin with a valid file.

---

## Example Multipart Request

```http
POST /api/profile/avatar HTTP/1.1
Host: example.test
Content-Type: multipart/form-data; boundary=----Boundary123

------Boundary123
Content-Disposition: form-data; name="avatar"; filename="profile.png"
Content-Type: image/png

[FILE CONTENT]
------Boundary123--
```

---

## Understanding `multipart/form-data`

* Important components include:

  ```text
  Boundary

  Form Field Name

  Filename

  Content-Type

  File Content

  Additional Parameters
  ```

* Each may influence validation.

---

## Filename

* Example:

  ```http
  filename="profile.png"
  ```

* The filename may be used for:

  ```text
  Validation

  Storage

  Display

  Logging

  Download Names
  ```

* Never assume the server stores the exact submitted filename.

---

## Content-Type

* Example:

  ```http
  Content-Type: image/png
  ```

* This is client-controlled request metadata unless independently verified.

---

## Important Principle

```text
Client-Supplied Content-Type

≠

Verified File Type
```

---

## File Content

* The server may inspect:

  ```text
  Magic Bytes

  File Signature

  Parser Output

  Image Dimensions

  Document Structure

  Metadata

  Encoding
  ```

* Strong validation often combines multiple controls.

---

## File Extension

* Examples:

  ```text
  .png

  .jpg

  .pdf

  .txt

  .csv
  ```

* Extensions describe expected file type but do not prove actual content.

---

## Important Principle

```text
Extension

≠

Content
```

---

## Four Important File-Type Signals

```text
Filename Extension

MIME Type

File Signature

Actual Parsed Content
```

* Applications may validate one or several of these.

---

## Establish a Baseline

* Upload a legitimate permitted file.

* Record:

  ```text
  Request

  Response

  Filename

  File Size

  MIME Type

  Returned Identifier

  Retrieval URL

  Processing Time

  Final Stored Name
  ```

---

## Baseline Workflow

```text
Create Safe Valid File

↓

Upload Normally

↓

Record Request

↓

Record Response

↓

Locate Stored File

↓

Retrieve File

↓

Observe Headers

↓

Verify Authorization

↓

Record Final State
```

---

## Extension Validation

* Suppose an application states:

  ```text
  Allowed:

  .jpg

  .png
  ```

* Test whether server-side validation actually enforces the allowed set.

---

## Safe Extension Testing

* Use harmless text content and controlled filenames.

* Examples:

  ```text
  test.txt

  test.invalid

  test.jpg
  ```

* The objective is to understand validation logic, not to execute harmful content.

---

## Extension Test Matrix

| Filename       | Purpose                |
| -------------- | ---------------------- |
| `test.jpg`     | Valid baseline         |
| `test.txt`     | Disallowed common type |
| `test.invalid` | Unknown extension      |
| `test`         | No extension           |
| `test.JPG`     | Case handling          |

---

## Case Sensitivity

* Validation may treat:

  ```text
  file.jpg

  file.JPG

  file.JpG
  ```

* Differently.

* Determine whether normalization is consistent.

---

## Multiple Extensions

* Filenames may contain:

  ```text
  report.txt.jpg

  image.jpg.txt
  ```

* Security question:

  ```text
  Which Extension Does Validation Use?

  Which Extension Does Storage or Serving Logic Use?
  ```

---

## Important Principle

```text
Validation Interpretation

Must Match

Storage and Execution Interpretation
```

* Different components interpreting filenames differently can create security problems.

---

## Trailing Characters

* Systems may normalize filenames differently.

* Examples may include:

  ```text
  file.jpg.

  file.jpg[space]
  ```

* Test only harmless files to understand normalization.

---

## Filename Normalization

* Applications may:

  ```text
  Lowercase Names

  Remove Spaces

  Replace Special Characters

  Generate UUIDs

  Strip Extensions

  Normalize Unicode

  Truncate Long Names
  ```

* Record the final stored name.

---

## MIME-Type Validation

* A multipart upload may contain:

  ```http
  Content-Type: image/png
  ```

* Modify only the MIME type while keeping the file unchanged.

---

## Example

### Baseline

```http
Content-Type: image/png
```

### Controlled Change

```http
Content-Type: text/plain
```

* Observe whether validation depends solely on this client-controlled value.

---

## MIME-Type Test Matrix

```text
Valid Extension + Correct MIME

Valid Extension + Incorrect MIME

Invalid Extension + Expected MIME

Unknown Extension + Generic MIME
```

* Use harmless content.

---

## Weak Validation Pattern

```text
Client Says:

Content-Type: image/png

↓

Server Trusts Header

↓

File Accepted Without Content Verification
```

* Whether this becomes exploitable depends on storage, processing, and serving behavior.

---

## File Signatures

* Many formats contain recognizable signatures or structural markers.

* Examples include:

  ```text
  PNG

  JPEG

  PDF

  ZIP
  ```

* Servers may inspect these to verify file type.

---

## Magic Bytes

* Magic bytes are characteristic bytes near the beginning of many file formats.

* Security testing may determine whether the server checks:

  ```text
  Extension Only

  MIME Only

  Signature

  Full Parsing
  ```

---

## Important Principle

```text
Magic-Byte Check

≠

Complete Safe File Validation
```

* A file may have a valid signature while containing malformed or unexpected structures later.

---

## Content Validation

* Stronger systems may parse the uploaded file.

* Example:

  ```text
  Upload Image

  ↓

  Image Decoder Opens File

  ↓

  Dimensions Verified

  ↓

  Image Re-Encoded

  ↓

  Safe Output Stored
  ```

* Re-encoding can remove some unsafe metadata or malformed content, depending on implementation.

---

## File Processing

* Uploaded files may be processed by:

  ```text
  Image Libraries

  PDF Parsers

  Office Document Parsers

  Archive Extractors

  Media Transcoders

  OCR Engines

  Antivirus Scanners

  CSV Parsers

  XML Parsers

  Background Workers
  ```

* Processing significantly expands the attack surface.

---

## Processing Pipeline

```text
Upload

↓

Temporary Storage

↓

Validation

↓

Parser / Processor

↓

Transformation

↓

Permanent Storage

↓

Retrieval
```

* Investigate each stage.

---

## Image Uploads

* Image features may:

  ```text
  Resize

  Crop

  Compress

  Rotate

  Re-Encode

  Extract Metadata
  ```

* Observe whether the uploaded file is returned unchanged or transformed.

---

## Image Test Questions

```text
Is the File Re-Encoded?

Are Metadata Fields Removed?

Are Dimensions Limited?

Are Extremely Large Dimensions Rejected?

Is the Original File Retained?

Does Processing Occur Synchronously?
```

---

## Image Metadata

* Images may contain metadata such as:

  ```text
  EXIF

  Camera Information

  Timestamps

  GPS Metadata

  Comments
  ```

* Applications should consider whether sensitive metadata should be preserved or removed.

---

## Metadata Privacy Test

```text
Create Controlled Image With Non-Sensitive Test Metadata

↓

Upload

↓

Retrieve Processed Image

↓

Check Whether Metadata Remains
```

* Use synthetic metadata, not real personal location data.

---

## Document Uploads

* Documents may include:

  ```text
  PDF

  DOCX

  XLSX

  ODT
  ```

* Security concerns may involve:

  ```text
  Active Content

  External References

  Embedded Objects

  Metadata

  Parser Vulnerabilities

  Unsafe Rendering
  ```

---

## Passive vs Active Content

* Passive content primarily represents data.

* Active content may cause interpretation or execution in a particular environment.

* Security depends on:

  ```text
  File Type

  Browser Behavior

  Server Configuration

  Content-Disposition

  Rendering Context
  ```

---

## Safe Testing Principle

```text
Prove Unsafe Handling

Without

Executing Harmful Server-Side Code
```

---

## Archive Uploads

* Applications may accept:

  ```text
  ZIP

  TAR

  Other Archive Formats
  ```

* Risks arise when archives are extracted server-side.

---

## Archive Processing Questions

```text
Are Files Extracted?

Where Are They Extracted?

Are Paths Normalized?

Are Nested Archives Allowed?

Are Extraction Limits Enforced?

Are Symlinks Handled Safely?

Is Total Expanded Size Limited?
```

---

## Archive Expansion

* A small compressed archive can potentially expand significantly.

* Security controls may include:

  ```text
  Compressed Size Limit

  Expanded Size Limit

  File Count Limit

  Nesting Limit

  Processing Timeout
  ```

* Do not test resource-exhaustion behavior against production systems without explicit authorization.

---

## CSV Imports

* CSV upload features may trigger:

  ```text
  Data Import

  Spreadsheet Export

  Formula Interpretation

  Bulk Account Creation

  Configuration Changes
  ```

* Investigate both upload validation and downstream usage.

---

## Import Workflow

```text
Upload File

↓

Parse Rows

↓

Validate Fields

↓

Preview

↓

Confirm

↓

Create / Update Records
```

* Test whether required validation occurs again at final processing.

---

## Preview vs Import

* A preview step may validate data differently from the final import step.

* Security question:

  ```text
  Does Final Processing Revalidate the File and Parsed Data?
  ```

---

## XML-Based Files

* Some document formats contain XML internally.

* If an application parses XML content, XML parser security may become relevant.

* Treat XML-specific testing according to the application's actual processing behavior and authorization.

---

## Filename Injection Risks

* User-controlled filenames may appear in:

  ```text
  HTML

  Logs

  Emails

  Headers

  Download Dialogs

  Administrative Interfaces
  ```

* Test whether filenames are safely encoded for each output context.

---

## Safe Filename Tests

* Use harmless markers such as:

  ```text
  test-file-123.txt

  quote-test-"123".txt

  unicode-test-✓.txt
  ```

* Observe normalization and output encoding.

---

## Path Components in Filenames

* Browsers normally submit a filename rather than a trusted server path.

* Security testing should determine whether server-side code safely isolates the filename from filesystem paths.

---

## Important Principle

```text
User Filename

Should Not Determine

Arbitrary Server Filesystem Location
```

---

## Server-Side Renaming

* Secure systems often generate server-controlled names.

* Example:

  ```text
  User Upload:

  profile.png

  ↓

  Stored As:

  8c7f2a31-....png
  ```

* This can reduce filename collision and path-manipulation risks.

---

## Renaming Questions

```text
Is the Name Random?

Is the Extension Preserved?

Is the Original Name Stored Separately?

Can the Final Name Be Predicted?

Can Two Uploads Collide?
```

---

## File Collisions

* If files are stored under predictable names:

  ```text
  avatar.jpg
  ```

* Repeated uploads may:

  ```text
  Overwrite

  Create Versions

  Reject

  Generate New Name
  ```

* Determine intended behavior.

---

## Overwrite Testing

* Use only files belonging to your controlled account.

* Example:

  ```text
  Upload File A

  ↓

  Upload File B With Same Name

  ↓

  Retrieve Stored File

  ↓

  Observe Result
  ```

---

## Cross-User Overwrite

* Security question:

  ```text
  Can User A Influence a File Owned by User B?
  ```

* This overlaps with authorization testing.

* Use two controlled accounts only.

---

## Storage Location

* Uploaded files may be stored in:

  ```text
  Local Filesystem

  Object Storage

  CDN

  Database

  Temporary Directory
  ```

* Storage architecture affects security behavior.

---

## Storage vs Execution

* A key security principle is separating user uploads from executable application code.

```text
Application Code

≠

User-Controlled Upload Storage
```

* User-controlled files should not gain server-side execution merely because they were uploaded.

---

## Public Storage

* Files stored in public object storage may be accessible through direct URLs.

* Security question:

  ```text
  Is Public Access Intended?
  ```

* Public accessibility is not automatically a vulnerability.

---

## Retrieval Testing

* After uploading a file, determine:

  ```text
  How Is It Retrieved?

  Direct URL?

  Authenticated Endpoint?

  Signed URL?

  CDN?

  API Download?
  ```

---

## Retrieval Request

```http
GET /api/files/731 HTTP/1.1
Host: example.test
Authorization: Bearer ...
```

* Test authorization separately from upload validation.

---

## File Authorization

* Use controlled accounts:

  ```text
  User A Uploads File

  ↓

  User B Attempts Retrieval
  ```

* Compare with the intended access model.

---

## File Object IDs

* Retrieval may use:

  ```text
  Numeric ID

  UUID

  Filename

  Storage Key

  Signed Token
  ```

* Object unpredictability does not replace authorization.

---

## Important Principle

```text
Hard-to-Guess File URL

≠

Access Control
```

---

## Signed URLs

* Applications may generate temporary signed URLs.

* Test:

  ```text
  Expiration

  Scope

  Object Binding

  User Binding if Intended

  Reuse Behavior
  ```

* Determine intended sharing semantics before reporting.

---

## Content-Disposition

* File responses may include:

  ```http
  Content-Disposition: attachment
  ```

* Or:

  ```http
  Content-Disposition: inline
  ```

* This influences browser handling.

---

## Inline Rendering

* `inline` content may be rendered directly by the browser when supported.

* Security depends on:

  ```text
  Content-Type

  Browser Sniffing

  CSP

  File Content

  Serving Origin
  ```

---

## Attachment Handling

* `attachment` generally instructs the browser to download the file.

* This can reduce some browser-rendering risks but does not solve every file-handling problem.

---

## Content-Type on Retrieval

* Compare:

  ```text
  Upload Content-Type

  vs

  Retrieval Content-Type
  ```

* The server may recalculate or preserve it.

---

## MIME Sniffing

* Browsers may sometimes infer content differently from the declared MIME type.

* Defensive headers may include:

  ```http
  X-Content-Type-Options: nosniff
  ```

* Evaluate behavior in context.

---

## Separate Upload Origin

* Some architectures serve uploads from a separate domain or origin.

* Conceptually:

  ```text
  Main Application:

  app.example.test

  Uploaded Content:

  uploads.example-cdn.test
  ```

* This can reduce impact if untrusted content is isolated appropriately.

---

## Same-Origin Risks

* Serving user-controlled active content from the application's trusted origin may increase browser-side risk.

* Analyze:

  ```text
  Origin

  Cookies

  Authentication

  CSP

  Content-Type

  Content-Disposition
  ```

---

## File Replacement

* Applications may support:

  ```text
  Replace Avatar

  Replace Document

  Update Attachment
  ```

* Security questions:

  ```text
  Does Old URL Still Work?

  Is Authorization Preserved?

  Is Cache Invalidated?

  Can Another User Replace the File?
  ```

---

## File Deletion

* Test:

  ```text
  Delete File

  ↓

  Retrieve Old URL

  ↓

  Verify Expected Behavior
  ```

* Consider CDN or cache delay before concluding that deletion failed.

---

## Orphaned Files

* An application may delete a database reference while leaving the underlying file accessible.

* Security relevance depends on whether the file should become inaccessible after deletion.

---

## Caching

* Uploaded files may be cached by:

  ```text
  Browser

  CDN

  Reverse Proxy

  Object Storage
  ```

* This can complicate replacement and deletion tests.

---

## Cache-Aware Testing

```text
Upload File A

↓

Retrieve

↓

Replace With File B

↓

Retrieve Using Fresh Request

↓

Check Cache Headers

↓

Compare Content
```

---

## File Size Validation

* Applications may enforce maximum upload sizes.

* Test around the documented or observed boundary.

* Example:

  ```text
  Limit = 5 MB
  ```

* Test:

  ```text
  Slightly Below

  Exactly At Limit

  Slightly Above
  ```

---

## Avoid Resource Exhaustion

* Do not upload extremely large files to test availability limits unless explicitly authorized.

* Boundary testing is usually sufficient.

---

## Multiple File Upload

* Some endpoints accept multiple files.

* Test:

  ```text
  Maximum File Count

  Per-File Size

  Total Request Size

  Mixed File Types

  Partial Failure Behavior
  ```

---

## Mixed Validation

* Example:

  ```text
  File 1 = Valid

  File 2 = Invalid
  ```

* Determine whether:

  ```text
  Entire Request Fails

  Valid File Is Stored

  Partial State Remains
  ```

---

## Partial Upload Failures

* Verify whether failed multi-file operations leave:

  ```text
  Orphaned Files

  Incomplete Records

  Unexpected Public URLs
  ```

---

## Chunked Uploads

* Large files may be uploaded in chunks.

* Workflow:

  ```text
  Initialize Upload

  ↓

  Upload Chunk 1

  ↓

  Upload Chunk 2

  ↓

  Complete Upload
  ```

* Each stage may require authorization and integrity checks.

---

## Chunked Upload Questions

```text
Can Chunks Be Reordered?

Can Chunks Belonging to Another Upload Be Reused?

Can Completion Be Triggered Early?

Are Chunk Numbers Validated?

Is Final File Integrity Verified?

Can Another User Access the Upload ID?
```

---

## Temporary Files

* Processing may create temporary files.

* Security questions include:

  ```text
  Are Temporary Files Publicly Accessible?

  Are They Deleted?

  Are Names Predictable?

  Do They Inherit Unsafe Permissions?
  ```

---

## Asynchronous Processing

* Uploads may return:

  ```text
  202 Accepted
  ```

* While processing continues in the background.

* Example:

  ```text
  Upload

  ↓

  Pending Scan

  ↓

  Processing

  ↓

  Available
  ```

---

## Processing-State Testing

* Determine whether files become accessible:

  ```text
  Before Validation

  Before Antivirus Scan

  Before Transformation

  Before Approval
  ```

* This may create a race or workflow flaw.

---

## Important Invariant

```text
Unvalidated File

Must Not Become Available

Before Required Security Processing Completes
```

---

## Antivirus and Malware Scanning

* Some systems scan uploaded files.

* Do not upload real malware.

* Where authorized, use recognized harmless antivirus test artifacts or platform-approved test mechanisms.

---

## Important Safety Rule

```text
Security-Control Testing

Does Not Require

Real Malicious Payloads
```

---

## File Processing Errors

* Malformed files may trigger parser errors.

* Observe:

  ```text
  HTTP Status

  Error Message

  Stack Trace

  Parser Name

  Internal Path

  Processing State
  ```

* Avoid aggressive malformed-input fuzzing unless explicitly authorized.

---

## Information Disclosure

* File-processing errors may reveal:

  ```text
  Internal Paths

  Library Names

  Versions

  Storage Buckets

  Temporary Directories

  Stack Traces
  ```

* Validate whether the information is genuinely sensitive and useful.

---

## File Import Authorization

* Import endpoints may perform powerful actions.

* Example:

  ```text
  Upload CSV

  ↓

  Create 500 Records
  ```

* Test:

  ```text
  Who Can Import?

  What Objects Can Be Created?

  Are Imported Fields Authorization-Checked?

  Is Tenant Context Enforced?
  ```

---

## Mass Assignment Through Imports

* Bulk imports may expose fields not normally editable through the UI.

* Example conceptual fields:

  ```text
  role

  status

  owner_id

  tenant_id
  ```

* Test only with harmless controlled records.

---

## Import Invariant

```text
Bulk Import

Must Enforce

The Same Authorization and Business Rules

As Individual Operations
```

---

## Duplicate Imports

* Replaying an import may create:

  ```text
  Duplicate Users

  Duplicate Orders

  Duplicate Credits

  Duplicate Configuration
  ```

* Determine whether duplicate handling matches intended behavior.

---

## File Upload Race Conditions

* Race conditions may occur when:

  ```text
  Upload

  ↓

  Temporary File Available

  ↓

  Validation Happens Later
  ```

* Or:

  ```text
  Check Filename

  ↓

  Concurrent Upload

  ↓

  Store File
  ```

---

## Safe Race Testing

* Use:

  ```text
  Disposable Files

  Controlled Account

  Minimal Request Count

  Non-Executable Content
  ```

* Verify final state.

---

## Overwrite Race

* Two concurrent uploads using the same logical filename may create inconsistent state.

* Security relevance depends on ownership and expected collision handling.

---

## File Handling Test Matrix

| Dimension     | Questions                                       |
| ------------- | ----------------------------------------------- |
| Extension     | Are only intended extensions accepted?          |
| MIME          | Is client-supplied MIME trusted?                |
| Content       | Is actual file structure validated?             |
| Filename      | Is it normalized safely?                        |
| Storage       | Is upload storage isolated?                     |
| Processing    | What parser or transformer handles it?          |
| Retrieval     | How is the file served?                         |
| Authorization | Who can access it?                              |
| Rendering     | Inline or attachment?                           |
| Replacement   | Can files be overwritten?                       |
| Deletion      | Does access end when expected?                  |
| Limits        | Are size/count limits enforced?                 |
| Async         | Is file accessible before validation?           |
| Imports       | Are authorization and business rules preserved? |

---

## File Handling Testing Workflow

```text
Define Scope

↓

Identify Upload Features

↓

Understand Intended File Policy

↓

Upload Valid Baseline File

↓

Capture Request

↓

Record Multipart Structure

↓

Record Response and File ID

↓

Locate Retrieval Mechanism

↓

Verify Storage / Serving Behavior

↓

Test Extension Validation

↓

Test MIME Validation

↓

Test Content Validation

↓

Test Filename Handling

↓

Test Size and Count Boundaries

↓

Test Authorization

↓

Test Replacement / Deletion

↓

Analyze Processing Pipeline

↓

Review Async States

↓

Review Imports / Archives if Applicable

↓

Verify Final State

↓

Validate Security Impact

↓

Document Evidence
```

---

## Step 1 — Inventory Upload Features

* Search for:

  ```text
  Avatar

  Attachment

  Document

  Import

  Upload

  Media

  Verification

  Backup

  Configuration
  ```

---

## Step 2 — Determine Intended Policy

* Record:

  ```text
  Allowed Types

  Size Limit

  Count Limit

  Visibility

  Processing

  Retention
  ```

---

## Step 3 — Upload a Valid File

* Never begin with malformed or unusual files.

---

## Step 4 — Capture the Request

* Identify:

  ```text
  Endpoint

  Method

  Multipart Field

  Filename

  MIME

  Other Parameters
  ```

---

## Step 5 — Retrieve the File

* Determine:

  ```text
  URL

  Authentication

  Headers

  Final Filename

  Content-Type

  Content-Disposition
  ```

---

## Step 6 — Change One Variable

* Examples:

  ```text
  Extension Only

  MIME Only

  Filename Only

  Size Only
  ```

---

## Step 7 — Compare Validation Layers

* Determine whether the server relies on:

  ```text
  Extension

  MIME

  Signature

  Parser
  ```

---

## Step 8 — Analyze Storage

* Determine whether files are:

  ```text
  Renamed

  Public

  Private

  Same-Origin

  Separate-Origin

  Executable
  ```

---

## Step 9 — Test Authorization

* Use two controlled accounts.

---

## Step 10 — Analyze Processing

* Determine whether the file is:

  ```text
  Re-Encoded

  Parsed

  Extracted

  Scanned

  Converted
  ```

---

## Step 11 — Test Lifecycle Operations

* Review:

  ```text
  Replace

  Delete

  Retry

  Failed Processing

  Duplicate Upload
  ```

---

## Step 12 — Verify Final State

* Do not rely only on the upload response.

---

## Example — MIME Validation

### Baseline

```text
Filename:

profile.png

MIME:

image/png

Content:

Valid PNG
```

### Test

```text
Filename:

profile.png

MIME:

text/plain

Content:

Same Valid PNG
```

### Observation

* If accepted:

  ```text
  MIME Header May Not Be Required
  ```

* This alone is not a vulnerability.

* Determine actual security impact.

---

## Example — Extension-Only Validation

### Test Files

```text
safe.txt

safe.jpg
```

* Both contain the same harmless text.

* If only `.jpg` is accepted, the server may be relying partly or entirely on filename extension.

* Further safe validation analysis is required before drawing conclusions.

---

## Example — Authorization

```text
User A

↓

Uploads private-document.pdf

↓

Receives File ID 731
```

* Controlled test:

  ```text
  User B

  ↓

  GET /api/files/731
  ```

* Compare with intended access policy.

---

## Example — Replacement

```text
Upload:

avatar-A.png

↓

Record URL

↓

Replace Avatar:

avatar-B.png

↓

Request Old URL
```

* Determine:

  ```text
  Old File Deleted?

  Old File Still Accessible?

  Cache Serving Old Version?

  URL Reused?
  ```

---

## Example — Async Validation

```text
Upload File

↓

Response:

processing
```

* Controlled test:

  ```text
  Attempt Retrieval Before Processing Completes
  ```

* Security question:

  ```text
  Is Unvalidated Content Accessible Prematurely?
  ```

---

## Example — Import Authorization

```text
Normal UI:

User Can Create Records Only in Own Tenant
```

* Import feature:

  ```text
  CSV Contains tenant_id
  ```

* Security question:

  ```text
  Does Import Processing Independently Enforce Tenant Ownership?
  ```

* Use only controlled tenant identifiers.

---

## Evidence Requirements

* Strong file-handling evidence should include:

  ```text
  Feature

  Intended File Policy

  User / Role

  Valid Baseline

  Original Request

  Modified Request

  Changed Variable

  Upload Response

  Stored Filename

  Retrieval URL

  Retrieval Headers

  Authorization Behavior

  Processing Behavior

  Final State

  Reproduction Steps

  Security Impact
  ```

---

## Proving Impact

* Weak observation:

  ```text
  Server Accepts Unexpected Extension
  ```

* Stronger finding:

  ```text
  Server Accepts Unexpected File Type

  +

  Stores It in Security-Relevant Context

  +

  Serves It Unsafely or Processes It Dangerously

  +

  Meaningful Impact Is Demonstrated
  ```

---

## Important Principle

```text
Upload Bypass

≠

Automatically Critical Vulnerability
```

* Severity depends on what happens after upload.

---

## Another Important Principle

```text
Accepted

Stored

Processed

Served

Executed

Are Different Security States
```

* Determine exactly which state was reached.

---

## False Positive Causes

* Common causes include:

  ```text
  File Accepted but Re-Encoded Safely

  File Stored but Never Publicly Accessible

  Unexpected Extension Intentionally Supported

  Public File Access Intended

  Old File Served From Cache Temporarily

  Async Validation Still Pending

  Duplicate Filename Automatically Renamed

  MIME Header Ignored Because Content Is Properly Parsed
  ```

---

## False Positive Control

```text
Understand Intended Policy

↓

Establish Valid Baseline

↓

Change One Variable

↓

Upload

↓

Inspect Stored Result

↓

Retrieve

↓

Inspect Headers

↓

Verify Authorization

↓

Wait for Processing if Needed

↓

Confirm Reproducibility

↓

Demonstrate Meaningful Impact
```

---

## File Upload Testing Checklist

### Discovery

* Identify:

  ```text
  Upload Endpoints

  Import Features

  Attachments

  Avatars

  Documents

  Media
  ```

---

### Request

* Record:

  ```text
  Method

  Content-Type

  Boundary

  Field Name

  Filename

  MIME

  Parameters
  ```

---

### Validation

* Test safely:

  ```text
  Extension

  Case

  MIME

  Signature

  Actual Parsing

  Size

  Count
  ```

---

### Filename

* Review:

  ```text
  Renaming

  Normalization

  Multiple Extensions

  Collisions

  Special Characters
  ```

---

### Storage

* Determine:

  ```text
  Public / Private

  Same Origin / Separate Origin

  Predictable / Random Name

  Executable / Non-Executable
  ```

---

### Retrieval

* Review:

  ```text
  Authorization

  Content-Type

  Content-Disposition

  Cache Headers

  Signed URLs
  ```

---

### Processing

* Identify:

  ```text
  Re-Encoding

  Parsing

  Extraction

  Scanning

  Conversion

  Background Jobs
  ```

---

### Lifecycle

* Test:

  ```text
  Replace

  Delete

  Retry

  Duplicate

  Failure

  Async State
  ```

---

### Imports

* Review:

  ```text
  Field Authorization

  Tenant Boundaries

  Duplicate Handling

  Preview vs Final Processing
  ```

---

## File Upload Investigation Journal

```text
Application:

Date:

Scope:

Feature:

Endpoint:

User:

Role:

Allowed Types:

Size Limit:

Count Limit:

Baseline File:

Baseline Extension:

Baseline MIME:

Baseline Size:

Multipart Field:

Additional Parameters:

Upload Response:

Returned File ID:

Stored Filename:

Retrieval URL:

Retrieval Authentication:

Retrieval Content-Type:

Content-Disposition:

Cache Headers:

Public / Private:

Same Origin?:

Server Renames File?:

Extension Tests:

MIME Tests:

Signature Tests:

Filename Tests:

Case Tests:

Multiple Extension Tests:

Size Tests:

Count Tests:

Authorization Tests:

Replacement Tests:

Deletion Tests:

Collision Tests:

Processing Behavior:

Re-Encoding:

Metadata Behavior:

Archive Processing:

Import Processing:

Async Processing:

Temporary Accessibility:

Validated Findings:

False Positives:

Evidence:

Next Steps:
```

---

## Professional Tips

* Treat file upload as a complete lifecycle rather than a single request.
* Begin every test with a legitimate baseline file.
* Capture the exact multipart request before modifying anything.
* Change one variable at a time.
* Distinguish filename extension, MIME type, file signature, and actual parsed content.
* Never assume `Content-Type` proves the real file type.
* Record whether the server renames uploaded files.
* Determine where and how the file is stored.
* Separate upload acceptance from storage, processing, serving, and execution.
* Verify whether uploaded files are served from the application's trusted origin or an isolated origin.
* Inspect retrieval `Content-Type` and `Content-Disposition`.
* Test authorization independently from upload validation.
* Use two controlled accounts for cross-user file access tests.
* Determine whether private files use real authorization rather than merely unpredictable URLs.
* Test replacement and deletion behavior.
* Account for caching when testing old file URLs.
* Use boundary tests instead of extremely large files.
* Avoid resource-exhaustion testing without explicit authorization.
* Investigate whether images are re-encoded and whether metadata survives.
* Review archives only when the application actually extracts them.
* Review bulk imports for authorization and business-rule enforcement.
* Check whether preview and final import stages apply the same validation.
* Investigate asynchronous processing states.
* Verify that files do not become accessible before required validation or scanning completes.
* Never use real malware to test antivirus controls.
* Use harmless proof files and synthetic metadata.
* Verify final stored and served behavior before assigning severity.
* Report actual impact, not merely an unexpected accepted extension.

---

## Common Mistakes

* Treating file upload as only an extension-bypass test.
* Starting with malicious or malformed content instead of a baseline.
* Changing filename, MIME type, and content simultaneously.
* Assuming the client-supplied MIME type is authoritative.
* Assuming a valid extension proves valid content.
* Assuming acceptance means execution.
* Reporting an unexpected extension as critical without proving impact.
* Ignoring server-side renaming.
* Ignoring retrieval behavior.
* Ignoring file authorization.
* Treating unpredictable URLs as access control.
* Ignoring `Content-Disposition`.
* Ignoring same-origin serving risks.
* Ignoring caches during replacement and deletion tests.
* Uploading extremely large files unnecessarily.
* Performing resource-exhaustion testing without authorization.
* Testing real scarce storage or production resources aggressively.
* Ignoring asynchronous validation.
* Ignoring temporary file accessibility.
* Ignoring image metadata.
* Ignoring parser and transformation behavior.
* Ignoring archive extraction.
* Ignoring bulk import authorization.
* Ignoring preview-versus-final-processing differences.
* Uploading real malware.
* Failing to verify actual stored state.
* Failing to reproduce from a clean baseline.
* Failing to distinguish unusual behavior from meaningful security impact.

---

## Practice Tasks

### Task 1 — Select an Authorized Upload Feature

* Use:

  ```text
  Deliberately Vulnerable Lab

  Local Application

  System You Own

  or

  Explicitly Authorized Target
  ```

---

### Task 2 — Upload a Valid Baseline

* Record:

  ```text
  Filename

  Extension

  MIME

  Size

  Response
  ```

---

### Task 3 — Capture the Request

* Send the multipart request to Repeater.

---

### Task 4 — Map Multipart Structure

* Identify:

  ```text
  Boundary

  Field Name

  Filename

  MIME

  File Content

  Other Parameters
  ```

---

### Task 5 — Test Extension Validation

* Use harmless files.

* Compare:

  ```text
  Allowed Extension

  Disallowed Extension

  No Extension

  Different Case
  ```

---

### Task 6 — Test MIME Validation

* Keep content unchanged.

* Modify only the MIME type.

---

### Task 7 — Observe Content Validation

* Determine whether the application checks:

  ```text
  Extension

  MIME

  Signature

  Parser
  ```

---

### Task 8 — Test Filename Handling

* Use safe filenames containing:

  ```text
  Spaces

  Mixed Case

  Unicode

  Multiple Extensions
  ```

* Record normalization.

---

### Task 9 — Retrieve the File

* Record:

  ```text
  URL

  Content-Type

  Content-Disposition

  Authentication Requirement
  ```

---

### Task 10 — Test Authorization

* Use two controlled accounts.

* Attempt access according to the intended permission model.

---

### Task 11 — Test Replacement

* Replace one controlled file.

* Check old and new URLs.

---

### Task 12 — Test Deletion

* Delete the file.

* Verify whether access ends as intended.

* Account for caching.

---

### Task 13 — Observe Processing

* Determine whether the file is:

  ```text
  Re-Encoded

  Converted

  Parsed

  Scanned

  Processed Asynchronously
  ```

---

### Task 14 — Build a File Handling Matrix

* Document each stage:

  ```text
  Upload

  Validation

  Storage

  Processing

  Retrieval

  Authorization

  Deletion
  ```

---

## Interview Questions

1. Why is file upload functionality considered a broad attack surface?
2. What stages exist in the file-upload lifecycle?
3. How do you capture a file upload request in Burp Suite?
4. What is `multipart/form-data`?
5. What information is contained in `Content-Disposition` for a file part?
6. Why is the client-supplied `Content-Type` not trustworthy by itself?
7. What is the difference between a file extension and actual file content?
8. What are the four important signals used to identify file type?
9. Why should you establish a valid upload baseline first?
10. How would you test extension validation safely?
11. Why should extension case handling be tested?
12. What security issues can arise from multiple extensions?
13. What is filename normalization?
14. How would you test MIME-type validation?
15. What are magic bytes?
16. Why are magic-byte checks alone insufficient?
17. What is full file-content validation?
18. Why can re-encoding uploaded images improve security?
19. What security risks can image metadata create?
20. What types of components may process uploaded files?
21. Why do file parsers expand the attack surface?
22. What risks are associated with archive extraction?
23. What controls help limit archive expansion risks?
24. What security issues may arise from CSV import functionality?
25. Why should preview and final import processing both enforce validation?
26. What risks can user-controlled filenames create?
27. Why should filenames not determine arbitrary server filesystem paths?
28. Why is server-side file renaming useful?
29. What is a file collision?
30. How would you safely test overwrite behavior?
31. Why should user-upload storage be separated from executable application code?
32. Is public file storage automatically a vulnerability?
33. How do you test authorization for uploaded files?
34. Why are unpredictable file URLs not a replacement for access control?
35. What are signed URLs?
36. What is the difference between `Content-Disposition: inline` and `attachment`?
37. Why does retrieval `Content-Type` matter?
38. What is MIME sniffing?
39. Why might applications serve uploads from a separate origin?
40. What should be tested when replacing or deleting uploaded files?
41. How can caching affect file-handling tests?
42. How should file-size limits be tested safely?
43. What security issues can arise in multi-file uploads?
44. What security questions apply to chunked uploads?
45. Why can asynchronous file processing create security problems?
46. Why should unvalidated files not become accessible before required scanning completes?
47. How can file imports bypass normal authorization or business rules?
48. Why does accepting an unexpected file extension not automatically prove a critical vulnerability?
49. What evidence is required for a strong file-upload vulnerability report?
50. Describe your complete methodology for testing file upload and file handling using Burp Suite.

---

## Quick Revision

* File lifecycle:

  ```text
  Select

  ↓

  Upload

  ↓

  Validate

  ↓

  Store

  ↓

  Process

  ↓

  Retrieve

  ↓

  Delete / Replace
  ```

* File-type signals:

  ```text
  Extension

  MIME

  Signature

  Parsed Content
  ```

* Important:

  ```text
  Content-Type

  ≠

  Verified File Type
  ```

* Also:

  ```text
  Extension

  ≠

  Content
  ```

* Multipart:

  ```text
  Boundary

  Field Name

  Filename

  Content-Type

  File Content
  ```

* Core tests:

  ```text
  Extension

  MIME

  Content

  Filename

  Size

  Authorization

  Storage

  Processing

  Retrieval
  ```

* Security states:

  ```text
  Accepted

  ≠

  Stored

  ≠

  Processed

  ≠

  Served

  ≠

  Executed
  ```

* Access control:

  ```text
  Unpredictable URL

  ≠

  Authorization
  ```

* Storage principle:

  ```text
  Application Code

  ≠

  User Upload Storage
  ```

* Async invariant:

  ```text
  Unvalidated File

  Must Not Become Available

  Before Required Validation Completes
  ```

* Professional workflow:

  ```text
  Identify Upload

  ↓

  Understand Policy

  ↓

  Establish Baseline

  ↓

  Capture Request

  ↓

  Test Validation Layers

  ↓

  Analyze Filename and Storage

  ↓

  Analyze Processing

  ↓

  Test Retrieval Authorization

  ↓

  Test Lifecycle Operations

  ↓

  Verify Final State

  ↓

  Validate Impact
  ```

---

## Key Takeaways

* File upload security must be evaluated across the entire lifecycle: **upload → validation → storage → processing → retrieval → replacement/deletion**.
* Begin with a legitimate file and establish a complete baseline before modifying the request.
* Burp Suite allows precise inspection and modification of multipart upload requests.
* Filename extension, client-supplied MIME type, file signature, and actual parsed content are different signals and should not be treated as equivalent.
* Client-controlled `Content-Type` headers do not prove the actual file type.
* Extension-only or MIME-only validation may be weak, but a validation bypass becomes security-relevant only when meaningful downstream impact exists.
* Filename normalization must be consistent between validation, storage, processing, and serving components.
* Server-generated filenames can reduce collision and path-manipulation risks.
* Upload acceptance, storage, processing, serving, and execution are separate security states.
* User-controlled uploads should be isolated from executable application code and handled as untrusted content.
* Retrieval security is as important as upload validation; private files require proper authorization.
* Unpredictable URLs are not a substitute for access control.
* Retrieval headers such as `Content-Type`, `Content-Disposition`, and `X-Content-Type-Options` influence browser-side handling.
* Serving untrusted content from an isolated origin can reduce some browser-side risks.
* Images, documents, archives, CSV files, XML-based formats, and other uploaded content may trigger complex server-side parsers and transformations.
* Image re-encoding and metadata handling should be investigated rather than assuming the original file is stored unchanged.
* Archive extraction requires safe path handling and limits on file count, nesting, and expanded size.
* Bulk imports must enforce the same authorization, tenant isolation, and business rules as individual application operations.
* Replacement, deletion, caching, duplicate filenames, and file collisions are important parts of the file lifecycle.
* Asynchronous processing can create temporary security gaps if files become accessible before validation, scanning, or transformation finishes.
* File-processing security tests should use harmless proof files; real malware or destructive payloads are unnecessary.
* Strong findings prove not merely that an unusual file was accepted, but that the application's subsequent storage, processing, serving, authorization, or execution behavior creates meaningful security impact.
* The professional file-handling workflow is **identify upload features → understand intended policy → establish valid baseline → capture multipart request → test extension/MIME/content validation independently → analyze filename normalization and storage → investigate processing → test retrieval and authorization → test replacement/deletion and asynchronous states → verify final behavior → validate impact → document evidence**.
