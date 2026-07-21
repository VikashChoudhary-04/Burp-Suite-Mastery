# API Security Challenge

> **Module 18 — Capstone, Interview & Quick Reference**

> **Progress**

```text
█████████░░░░░░ 9 / 15 Files
```

---

## Overview

* Modern applications rely heavily on APIs for:

  * Web applications.
  * Mobile applications.
  * Single-page applications.
  * Microservices.
  * Third-party integrations.
  * Partner platforms.
  * Internal services.

* APIs expose application data and business operations directly through structured requests.

* This makes API security testing more than endpoint discovery.

* A professional API assessment examines:

```text
Discovery

↓

Authentication

↓

Authorization

↓

Object Access

↓

Property Access

↓

Input Handling

↓

Business Logic

↓

Rate Limits

↓

Versioning

↓

Data Exposure

↓

Operational Security
```

* Common API weaknesses include:

  * Broken Object Level Authorization.
  * Broken Function Level Authorization.
  * Broken Object Property Level Authorization.
  * Weak authentication.
  * Token handling flaws.
  * Excessive data exposure.
  * Mass assignment.
  * Improper inventory management.
  * Unrestricted resource consumption.
  * Business logic abuse.
  * Unsafe third-party API consumption.
  * Inconsistent security across API versions.

---

## Objectives

* By completing this challenge, you should be able to:

  * Discover API attack surface.
  * Build an endpoint inventory.
  * Identify API versions.
  * Analyze authentication mechanisms.
  * Test token lifecycle behavior.
  * Test object-level authorization.
  * Test function-level authorization.
  * Test property-level authorization.
  * Identify mass-assignment candidates.
  * Analyze excessive data exposure.
  * Test input validation.
  * Test HTTP methods.
  * Analyze pagination and filtering.
  * Test rate limits safely.
  * Review resource-consumption controls.
  * Analyze API business logic.
  * Test version inconsistencies.
  * Review GraphQL where present.
  * Review API documentation exposure.
  * Build a repeatable API testing methodology.

---

# Challenge Scenario

* You are assessing an authorized application exposing:

```text
Web Application

↓

REST API

↓

Authentication Service

↓

User API

↓

Project API

↓

Billing API

↓

Administrative API
```

* Example endpoints may include:

```text
/api/v1/users

/api/v1/users/{id}

/api/v1/projects

/api/v1/projects/{id}

/api/v1/orders

/api/v1/files

/api/v2/profile

/graphql
```

* Your task is to map the API and systematically test its security boundaries.

---

# Challenge Deliverables

* Produce:

```text
API-Inventory.md

API-Endpoint-Matrix.md

API-Authorization-Matrix.md

API-Property-Matrix.md

API-Version-Comparison.md

Rate-Limit-Notes.md

Validated-Findings/

Evidence/

API-Security-Summary.md
```

---

# Phase 1 — Discover the API Surface

* Start by identifying API traffic from normal application use.

---

## Sources

```text
Burp HTTP History

JavaScript Files

Browser Developer Tools

Mobile Traffic

API Documentation

OpenAPI / Swagger

GraphQL

Application Routes

Error Messages

Historical Endpoints
```

---

# Phase 2 — Search JavaScript for API Routes

* Frontend JavaScript may reveal:

```text
/api/

/v1/

/v2/

graphql

users

projects

admin

internal
```

* Record discovered paths even if they are not currently used by the UI.

---

# Phase 3 — Identify API Documentation

* Look for documentation such as:

```text
OpenAPI

Swagger UI

API Reference

Postman Collections

GraphQL Schema Documentation
```

* Documentation can reveal:

```text
Endpoints

Methods

Parameters

Schemas

Authentication

Deprecated Versions

Administrative Functions
```

* Documentation exposure is not automatically a vulnerability.

* Use it to improve attack-surface understanding.

---

# Phase 4 — Build the Endpoint Inventory

| Method | Endpoint             | Auth   | Purpose        | Object  | Version |
| ------ | -------------------- | ------ | -------------- | ------- | ------- |
| GET    | `/api/v1/users/{id}` | Bearer | User profile   | User    | v1      |
| POST   | `/api/v1/projects`   | Bearer | Create project | Project | v1      |
| PATCH  | `/api/v1/users/{id}` | Bearer | Update user    | User    | v1      |
| DELETE | `/api/v1/files/{id}` | Bearer | Delete file    | File    | v1      |

---

## Record

```text
Method

Path

Authentication

Parameters

Request Body

Response Fields

Object Identifiers

Required Role

Version

Observed Errors
```

---

# Phase 5 — Classify Endpoints

* Group endpoints into:

```text
Public

Authenticated User

Privileged User

Administrative

Internal-Looking

Partner

Legacy

Deprecated
```

---

# Phase 6 — Identify API Versions

* Look for:

```text
/api/v1/

/api/v2/

/v3/

Accept-Version

API-Version Header

Version Query Parameter
```

---

## Ask

```text
Which Version Is Current?

Which Versions Still Respond?

Do Old Versions Enforce the Same Security?

Are Deprecated Endpoints Still Reachable?
```

---

# Phase 7 — Map Authentication

* Determine how the API authenticates clients.

---

## Common Mechanisms

```text
Session Cookie

Bearer Token

JWT

API Key

OAuth Access Token

Signed Request

Mutual TLS
```

---

## Record

```text
Token Location

Token Format

Expiration

Refresh Mechanism

Revocation

Scope

Audience

Issuer

Session Binding
```

---

# Phase 8 — Establish Authentication Baselines

* For each protected endpoint compare:

```text
Valid Authentication

No Authentication

Invalid Authentication

Expired Authentication

Revoked Authentication
```

---

## Record

```text
Status Code

Response Body

Error Type

Data Returned

Side Effects
```

---

# Phase 9 — Test Token Lifecycle

* Review:

```text
Issuance

Use

Expiration

Refresh

Rotation

Revocation

Logout
```

---

## Questions

```text
Does Logout Invalidate the Token?

Can an Old Refresh Token Be Reused?

Does Password Change Revoke Sessions?

Does Role Change Affect Existing Tokens?

Are Tokens Properly Scoped?
```

---

# Phase 10 — Build Controlled Identities

* Use at least:

```text
User A

User B
```

* Where authorized, also use:

```text
Privileged User

Administrator Test Account
```

---

## Map

```text
Identity

Role

Tenant

Owned Objects

Accessible Functions
```

---

# Phase 11 — Test Broken Object Level Authorization

* BOLA occurs when a user can access another user's object by changing an object reference.

---

## Candidate Identifiers

```text
user_id

account_id

project_id

order_id

file_id

invoice_id

message_id

UUID

Slug
```

---

## Workflow

```text
User A Creates Object A

↓

User B Creates Object B

↓

Capture User A Request for Object A

↓

Authenticate as User B

↓

Request Object A

↓

Verify Authorization
```

---

# Test Every Action

```text
Read

Update

Delete

Download

Share

Approve

Export

Archive
```

---

# Core Rule

```text
Hard-to-Guess Object ID

≠

Authorization
```

---

# Phase 12 — Test Nested Object References

* APIs may use:

```text
/users/{user_id}/projects/{project_id}
```

* Test whether authorization validates:

```text
User

Project

Relationship Between Them
```

---

## Ask

```text
Does Project Belong to User?

Does User Belong to Tenant?

Is Parent-Child Relationship Enforced?
```

---

# Phase 13 — Test Object IDs in Request Bodies

* Authorization-sensitive references may appear in JSON:

```text
{
  "owner_id": "123",
  "project_id": "456"
}
```

* Do not test only path parameters.

---

## Check

```text
Path IDs

Query IDs

JSON IDs

Headers

Cookies

GraphQL Variables
```

---

# Phase 14 — Test Broken Function Level Authorization

* BFLA occurs when a user can invoke functionality intended for a more privileged role.

---

## Candidate Functions

```text
/admin/users

/users/{id}/disable

/orders/{id}/refund

/projects/{id}/approve

/reports/export

/system/config
```

---

## Workflow

```text
Privileged User Performs Action

↓

Capture Request

↓

Switch to Lower-Privilege Controlled User

↓

Replay Request

↓

Verify Server-Side Authorization
```

---

# Core Rule

```text
Hidden Button

≠

Access Control
```

---

# Phase 15 — Test HTTP Method Authorization

* An endpoint may protect one method but not another.

---

## Compare

```text
GET

POST

PUT

PATCH

DELETE
```

---

## Example

```text
GET /api/users/123

→ Protected
```

* But verify independently:

```text
PATCH /api/users/123

DELETE /api/users/123
```

---

# Phase 16 — Test Broken Object Property Level Authorization

* APIs often expose objects with multiple properties.

---

## Example

```text
{
  "id": 123,
  "name": "User",
  "email": "user@example.test",
  "role": "user",
  "credit": 100,
  "verified": false
}
```

* Different properties may require different authorization.

---

## Questions

```text
Which Properties Can the User Read?

Which Can the User Modify?

Which Are Server Controlled?

Which Are Privileged?
```

---

# Phase 17 — Build the Property Matrix

| Property   | Returned | User Editable | Admin Only | Sensitive |
| ---------- | -------- | ------------- | ---------- | --------- |
| `name`     | Yes      | Yes           | No         | No        |
| `email`    | Yes      | Limited       | No         | Yes       |
| `role`     | Maybe    | No            | Yes        | Yes       |
| `credit`   | Maybe    | No            | Yes        | Yes       |
| `verified` | Maybe    | No            | Yes        | Yes       |

---

# Phase 18 — Test Mass Assignment

* Mass assignment occurs when backend frameworks automatically bind client-supplied properties to server-side objects without appropriate allowlisting.

---

## Workflow

```text
Capture Normal Update

↓

Identify Known Object Properties

↓

Add One Controlled Property

↓

Send Request

↓

Retrieve Object Again

↓

Verify Persistent State
```

---

## Candidate Properties

```text
role

admin

verified

owner_id

tenant_id

status

balance

credit

plan

permissions
```

---

# Important

```text
Property Accepted in Response

≠

Property Persisted
```

* Always verify the stored object state.

---

# Phase 19 — Test Excessive Data Exposure

* APIs may return more information than the client needs.

---

## Review Responses For

```text
Email

Phone

Internal IDs

Roles

Permissions

Billing Data

Tokens

Secrets

Debug Fields

Internal Metadata

Personal Information
```

---

## Ask

```text
Does the Client Need This Field?

Should This User See It?

Is Sensitive Data Filtered Server-Side?
```

---

# Phase 20 — Compare List and Detail Endpoints

* Different endpoints may expose different data.

---

## Example

```text
GET /api/users

GET /api/users/{id}

GET /api/search/users
```

* Compare returned properties and authorization.

---

# Phase 21 — Test Filtering and Search

* APIs may support:

```text
?search=

?filter=

?sort=

?status=

?owner=

?fields=
```

---

## Test

```text
Unauthorized Object Discovery

Sensitive Field Selection

Filter Bypass

Cross-Tenant Results

Unexpected Query Operators
```

---

# Phase 22 — Test Pagination

* Parameters may include:

```text
page

limit

offset

cursor

per_page
```

---

## Test Boundaries

```text
0

Negative

Maximum

Maximum + 1

Large Controlled Value
```

---

## Observe

```text
Resource Usage

Data Exposure

Limit Enforcement

Response Size
```

---

# Phase 23 — Test Field Selection

* Some APIs support:

```text
?fields=id,name,email
```

* Or GraphQL-style field selection.

---

## Ask

```text
Can Sensitive Fields Be Requested Directly?

Does Field Selection Bypass Normal Response Filtering?
```

---

# Phase 24 — Test Input Validation

* Test:

```text
Query Parameters

Path Parameters

JSON

Headers

Cookies

GraphQL Variables
```

---

## Include

```text
Type Changes

Missing Required Fields

Null

Arrays

Objects

Unexpected Properties

Boundary Values

Malformed Structured Input
```

---

# Phase 25 — Test Content-Type Handling

* Compare supported formats such as:

```text
application/json

application/x-www-form-urlencoded

multipart/form-data

application/xml
```

---

## Ask

```text
Do Alternate Parsers Enforce Equivalent Validation?

Does Authorization Change?

Does Property Filtering Change?
```

---

# Phase 26 — Test HTTP Method Handling

* Use `OPTIONS` where appropriate to understand supported methods.

* Compare expected methods with actual behavior.

---

## Ask

```text
Are Unexpected Methods Accepted?

Do Alternate Methods Reach the Same Handler?

Are Security Controls Consistent?
```

---

# Phase 27 — Test Rate Limits

* Identify sensitive operations:

```text
Login

Password Reset

OTP

Registration

Invitation

Search

Export

Verification

Resource Creation
```

---

## Establish Baseline

```text
Normal Request Rate

↓

Observe Headers

↓

Perform Small Controlled Burst

↓

Record Threshold

↓

Verify Recovery
```

---

## Look For

```text
429 Too Many Requests

Retry-After

RateLimit Headers

Temporary Lock

CAPTCHA

Backoff
```

---

# Important

* Do not perform high-volume denial-of-service testing.

* Use the minimum request volume necessary to understand enforcement.

---

# Phase 28 — Determine Rate-Limit Scope

* Ask whether limits are based on:

```text
Account

Session

Token

IP

Endpoint

Tenant

Destination Object
```

---

## Test Controlled Variations

```text
Same User / New Session

Same User / New Token

Different Controlled User

Different Endpoint
```

* Do not attempt infrastructure-scale bypass techniques.

---

# Phase 29 — Test Resource Consumption

* API requests may trigger expensive operations.

---

## Candidates

```text
Large Pagination

Complex Search

Report Generation

File Conversion

Bulk Operations

GraphQL Queries

Exports
```

---

## Evaluate Safely

```text
Are Limits Present?

Are Timeouts Present?

Are Query Complexity Limits Present?

Are Result Sizes Bounded?
```

---

# Phase 30 — Test API Business Logic

* APIs often expose business operations more directly than the UI.

---

## Candidates

```text
Apply Coupon

Create Order

Approve Request

Transfer Resource

Invite User

Change Subscription

Claim Reward
```

---

## Test

```text
Replay

Sequence Changes

Boundary Values

Stale Requests

Duplicate Submission

Controlled Concurrency
```

---

# Phase 31 — Compare API and UI Enforcement

* Capture an action through the UI.

* Then reproduce it directly through the API.

---

## Ask

```text
Does the UI Prevent Something

That the API Still Accepts?
```

---

# Core Rule

```text
Frontend Restriction

≠

API Security Control
```

---

# Phase 32 — Test API Version Differences

* Compare equivalent operations across versions.

---

## Example

```text
/api/v1/users/{id}

/api/v2/users/{id}
```

---

## Compare

```text
Authentication

Authorization

Returned Fields

Accepted Properties

Validation

Rate Limits

Error Handling
```

---

# Version Comparison Matrix

| Control            | v1       | v2       |
| ------------------ | -------- | -------- |
| Authentication     | Required | Required |
| Object auth        | Test     | Test     |
| Property filtering | Test     | Test     |
| Rate limiting      | Test     | Test     |
| Sensitive fields   | Test     | Test     |

---

# Phase 33 — Test Deprecated Endpoints

* Old endpoints may remain reachable after frontend migration.

---

## Sources

```text
Old JavaScript

Documentation

Archived Requests

Mobile Clients

Error Messages

Historical URLs
```

---

## Ask

```text
Is Authentication Still Enforced?

Are Old Authorization Bugs Still Present?

Does the Endpoint Expose Legacy Fields?

Can Deprecated Functions Still Modify State?
```

---

# Phase 34 — Review Error Handling

* API errors may expose:

```text
Stack Traces

Database Details

Internal Paths

Framework Versions

Object Existence

Internal Service Names

Debug Information
```

---

## Compare

```text
Valid Object

Invalid Object

Unauthorized Object

Nonexistent Object
```

* Determine whether response differences leak sensitive information.

---

# Phase 35 — Review CORS in API Context

* Identify whether browser-based cross-origin access is possible.

---

## Review

```text
Access-Control-Allow-Origin

Access-Control-Allow-Credentials

Allowed Methods

Allowed Headers
```

---

## Important

* CORS is primarily a browser-enforced policy.

* A permissive header alone is not sufficient to establish impact.

* Determine whether sensitive authenticated API responses can actually be read cross-origin.

---

# Phase 36 — Review GraphQL Surface

* If GraphQL is present, identify:

```text
Endpoint

Authentication

Queries

Mutations

Subscriptions

Types

Fields
```

---

## Common Endpoint

```text
/graphql
```

---

# Phase 37 — Build a GraphQL Operation Inventory

| Operation       | Type     | Auth  | Object | Sensitive |
| --------------- | -------- | ----- | ------ | --------- |
| `user`          | Query    | User  | User   | Yes       |
| `updateProfile` | Mutation | User  | User   | Yes       |
| `deleteUser`    | Mutation | Admin | User   | Critical  |

---

# Phase 38 — Test GraphQL Authorization

* Authorization must apply to:

```text
Operations

Objects

Fields

Relationships
```

---

## Test

```text
User A Object

↓

Query as User B

↓

Request Sensitive Fields

↓

Attempt Controlled Mutation
```

---

# Core Rule

```text
GraphQL Schema Permission

≠

Object Authorization
```

---

# Phase 39 — Test GraphQL Field Exposure

* GraphQL allows clients to request selected fields.

---

## Ask

```text
Can Users Request Sensitive Fields

That the Normal UI Never Requests?
```

---

# Phase 40 — Test GraphQL Complexity Safely

* Look for controls on:

```text
Query Depth

Aliases

Nested Relationships

Batching

Pagination

Result Size
```

---

* Do not attempt denial-of-service.

* Use small controlled queries to determine whether reasonable limits exist.

---

# Phase 41 — Review API Keys

* If API keys exist, determine:

```text
Where Generated?

Where Displayed?

How Scoped?

How Revoked?

Expiration?

Rotation?

Permissions?
```

---

## Test

```text
Revoked Key

Old Key

Low-Privilege Key

Wrong Scope

Deleted User's Key
```

---

# Phase 42 — Review Webhooks

* APIs may send or receive webhooks.

---

## Map

```text
Registration

Secret

Signature

Delivery

Retry

Replay Protection
```

---

## Ask

```text
Are Incoming Webhooks Authenticated?

Are Signatures Verified?

Are Timestamps Checked?

Can Events Be Replayed?

Are Outbound Destinations Restricted?
```

---

* Use only controlled webhook endpoints during authorized testing.

---

# Phase 43 — Review Third-Party API Consumption

* Applications may consume external data.

---

## Ask

```text
Is External Data Trusted?

Is Response Schema Validated?

Are Redirects Followed?

Are Timeouts Applied?

Are Errors Handled Safely?
```

---

# Phase 44 — Build the API Authorization Matrix

| Endpoint       | Action | User A Own | User B Object | Admin Required |
| -------------- | ------ | ---------- | ------------- | -------------- |
| `/users/{id}`  | Read   | Allow      | Deny          | No             |
| `/users/{id}`  | Update | Allow      | Deny          | No             |
| `/users/{id}`  | Delete | Policy     | Deny          | Maybe          |
| `/admin/users` | List   | Deny       | Deny          | Yes            |

---

# Phase 45 — Validate Candidate Findings

* Every candidate must establish the failed security rule.

---

## Validation Workflow

```text
Define Expected Rule

↓

Capture Authorized Baseline

↓

Change One Security Variable

↓

Replay Request

↓

Verify Persistent or Sensitive Effect

↓

Repeat With Control

↓

Determine Impact
```

---

# Example — BOLA

```text
User A Owns Object A

↓

User B Owns Object B

↓

User B Requests Object A

↓

Server Returns Object A

↓

Cross-User Access Confirmed
```

---

# Example — Mass Assignment

```text
Normal User Updates Profile

↓

Adds Privileged Property

↓

Server Accepts Request

↓

Object Retrieved Again

↓

Privileged Property Persisted

↓

Security Impact Confirmed
```

---

# Example — BFLA

```text
Admin Performs Privileged Action

↓

Request Captured

↓

Normal User Replays Equivalent Request

↓

Action Completes

↓

Privilege Boundary Failure Confirmed
```

---

# Phase 46 — Determine Impact

* Potential impact includes:

```text
Cross-User Data Access

Cross-Tenant Data Access

Unauthorized Modification

Unauthorized Deletion

Privilege Escalation

Administrative Function Access

Sensitive Data Exposure

Financial Abuse

Account Compromise

Resource Exhaustion

Business Logic Abuse
```

---

# Phase 47 — Identify Root Cause

* Common causes include:

```text
Missing Object Authorization

Missing Function Authorization

Unsafe Automatic Property Binding

Excessive Response Serialization

Weak Token Lifecycle Controls

Inconsistent API Version Security

Missing Rate Limits

Client-Side-Only Restrictions

Insufficient Input Validation

Legacy Endpoint Exposure
```

---

# Phase 48 — Collect Evidence

* Preserve:

```text
Endpoint

Method

Authentication Context

User / Role

Owned Object

Target Object

Baseline Request

Test Request

Response

Persistent State

Security Impact
```

---

## Evidence Structure

```text
F001/
│
├── 01-context.md
├── 02-user-a-request.txt
├── 03-user-a-response.txt
├── 04-user-b-request.txt
├── 05-user-b-response.txt
├── 06-final-state.png
└── notes.md
```

---

# Capstone Challenge

## Stage 1 — Discovery

* Identify at least:

```text
25 API Endpoints
```

* Where the target exposes enough API surface.

---

## Stage 2 — Inventory

* Record:

```text
Method

Path

Authentication

Role

Object

Parameters

Version
```

---

## Stage 3 — Authentication

* Test:

```text
No Token

Invalid Token

Expired Token

Logout / Revocation

Role Changes
```

* Where applicable.

---

## Stage 4 — Object Authorization

* Use controlled users to test:

```text
Read

Update

Delete

Download

Share
```

---

## Stage 5 — Function Authorization

* Identify privileged endpoints and test lower-privilege access safely.

---

## Stage 6 — Property Authorization

* Build a property matrix.

* Test sensitive fields for:

```text
Read Access

Write Access

Mass Assignment
```

---

## Stage 7 — Input and Parsing

* Test:

```text
Types

Null

Arrays

Objects

Unexpected Properties

Content Types

HTTP Methods
```

---

## Stage 8 — Limits

* Review:

```text
Pagination

Rate Limits

Resource Limits

Query Complexity
```

---

## Stage 9 — Versioning

* Compare current and legacy versions.

---

## Stage 10 — GraphQL

* If present, test:

```text
Operations

Objects

Fields

Mutations

Complexity Controls
```

---

## Stage 11 — Validation

* Classify every candidate:

```text
Confirmed

Rejected

Needs More Evidence
```

---

# Challenge Success Criteria

* You should be able to answer:

```text
Which APIs Exist?

Which Versions Exist?

How Is Authentication Performed?

How Are Tokens Managed?

Which Objects Exist?

Who Owns Each Object?

Which Actions Can Each Role Perform?

Which Properties Can Each Role Read?

Which Properties Can Each Role Modify?

Are Server-Controlled Fields Protected?

Are Responses Overexposing Data?

Are Limits Enforced?

Are Legacy Versions Secure?

Are GraphQL Fields Authorized?

Are Security Controls Consistent Across Interfaces?
```

---

# API Security Testing Workflow

```text
Discover API

↓

Build Endpoint Inventory

↓

Identify Versions

↓

Map Authentication

↓

Create Controlled Identities

↓

Map Objects

↓

Test Object Authorization

↓

Test Function Authorization

↓

Test Property Authorization

↓

Test Mass Assignment

↓

Review Data Exposure

↓

Test Input Handling

↓

Test Methods / Content Types

↓

Test Pagination / Limits

↓

Test Business Logic

↓

Compare API Versions

↓

Review GraphQL / Webhooks / Keys

↓

Validate Findings

↓

Determine Impact

↓

Collect Evidence

↓

Report
```

---

# Common Mistakes

* Testing only endpoints visible in the UI.
* Ignoring JavaScript-discovered endpoints.
* Ignoring API documentation.
* Ignoring old API versions.
* Testing only GET requests.
* Testing object IDs only in URL paths.
* Ignoring object references in JSON.
* Assuming UUIDs provide authorization.
* Testing only read access for BOLA.
* Ignoring update, delete, download, share, and export.
* Confusing authentication with authorization.
* Assuming hidden admin UI means protected admin API.
* Ignoring property-level authorization.
* Reporting mass assignment before verifying persistence.
* Ignoring excessive data exposure.
* Ignoring list endpoints.
* Ignoring search and filter endpoints.
* Ignoring pagination boundaries.
* Sending excessive traffic during rate-limit testing.
* Treating absence of `429` alone as proof of a vulnerability.
* Ignoring rate-limit scope.
* Ignoring resource-consumption controls.
* Ignoring business logic because the API is technically valid.
* Ignoring content-type differences.
* Ignoring alternate HTTP methods.
* Ignoring GraphQL field-level authorization.
* Performing unsafe GraphQL complexity tests.
* Ignoring API key revocation.
* Ignoring webhook replay protection.
* Reporting documentation exposure without meaningful impact.
* Failing to use controlled accounts.
* Failing to verify persistent state.
* Failing to identify the exact failed security boundary.

---

# Professional Tips

* Treat API discovery as a dedicated reconnaissance phase.
* Build an endpoint inventory before deep testing.
* Group endpoints by object and business function.
* Record every object identifier.
* Test authorization with at least two controlled identities.
* Separate authentication, object authorization, function authorization, and property authorization.
* Test every CRUD action independently.
* Do not assume GET and DELETE share the same authorization logic.
* Test IDs in paths, queries, bodies, headers, and GraphQL variables.
* Build a property matrix for important objects.
* Identify which fields should be server controlled.
* Verify mass-assignment results by retrieving the object again.
* Compare list, detail, search, and export responses for excessive data.
* Test input types rather than only malicious strings.
* Compare alternate content types and methods.
* Test pagination at controlled boundaries.
* Keep rate-limit tests small and measurable.
* Determine the scope and reset behavior of limits.
* Compare API enforcement with UI restrictions.
* Compare equivalent operations across API versions.
* Treat deprecated endpoints as active attack surface until proven inaccessible.
* Review GraphQL authorization at operation, object, relationship, and field levels.
* Use safe query-complexity tests.
* Review API-key scope, expiration, rotation, and revocation.
* Test webhook authenticity and replay protection using controlled infrastructure.
* Validate actual security impact before reporting.

---

# Practice Tasks

1. Select an authorized API-backed application.

2. Browse normal application functionality through Burp.

3. Identify all `/api/` traffic.

4. Search JavaScript for API paths.

5. Identify API documentation.

6. Identify API versions.

7. Build an endpoint inventory.

8. Classify endpoints by authentication requirement.

9. Classify endpoints by role.

10. Identify all object identifiers.

11. Create User A and User B controlled objects.

12. Test read authorization.

13. Test update authorization.

14. Test delete authorization.

15. Test download authorization.

16. Test nested object relationships.

17. Test object IDs in JSON bodies.

18. Identify privileged API functions.

19. Replay privileged requests with lower privilege.

20. Test HTTP methods independently.

21. Build a property matrix.

22. Identify server-controlled fields.

23. Test one additional property at a time.

24. Verify whether modified properties persist.

25. Compare list and detail responses.

26. Review responses for excessive data.

27. Test search and filtering.

28. Test field-selection functionality.

29. Test pagination boundaries.

30. Test missing fields.

31. Test null values.

32. Test unexpected types.

33. Test arrays and objects.

34. Test unexpected properties.

35. Compare supported content types.

36. Review `OPTIONS` responses where useful.

37. Identify rate-sensitive endpoints.

38. Establish a normal rate baseline.

39. Perform a small controlled burst.

40. Record rate-limit behavior.

41. Determine rate-limit scope.

42. Identify expensive API operations.

43. Review resource limits safely.

44. Test API business workflows.

45. Compare API behavior with UI restrictions.

46. Compare v1 and v2 endpoints where available.

47. Test deprecated endpoints.

48. Review API errors.

49. Review CORS behavior in context.

50. Identify GraphQL if present.

51. Build a GraphQL operation inventory.

52. Test GraphQL object authorization.

53. Test GraphQL field authorization.

54. Test mutations.

55. Review query complexity controls safely.

56. Review API key lifecycle where present.

57. Review webhook security where present.

58. Build the API authorization matrix.

59. Validate every suspicious result.

60. Determine actual impact.

61. Identify root cause.

62. Collect minimal sufficient evidence.

63. Restore controlled test state.

64. Write the final API security summary.

---

# Interview Questions

1. What is API security testing?
2. How do you discover hidden API endpoints?
3. What information should an API endpoint inventory contain?
4. How do you identify API versions?
5. Why should deprecated APIs be tested?
6. What authentication mechanisms are commonly used by APIs?
7. How do you test API authentication?
8. What should be tested in a token lifecycle?
9. What is BOLA?
10. How do you test BOLA?
11. Why do UUIDs not replace authorization?
12. Why should every object action be tested independently?
13. What are nested-object authorization issues?
14. Where can object references appear besides URL paths?
15. What is BFLA?
16. How do you test function-level authorization?
17. Why is hiding an admin button insufficient?
18. What is object property-level authorization?
19. What is mass assignment?
20. How do you test mass assignment?
21. Why must persistence be verified after property modification?
22. What is excessive data exposure?
23. How do you identify excessive response fields?
24. Why should list and detail endpoints be compared?
25. What security issues can exist in filtering and search?
26. How do you test pagination safely?
27. What input types should be tested in JSON APIs?
28. Why should alternate content types be tested?
29. Why should HTTP methods be tested independently?
30. How do you test API rate limits safely?
31. Why does absence of HTTP 429 not automatically prove missing rate limiting?
32. How do you determine rate-limit scope?
33. What is unrestricted resource consumption?
34. How do you test resource limits without causing denial of service?
35. Why should API business logic be tested separately from the UI?
36. What security differences may exist between API versions?
37. How can API error handling leak information?
38. How does CORS relate to API security?
39. What is GraphQL?
40. How does GraphQL authorization differ from REST authorization testing?
41. What is field-level authorization in GraphQL?
42. Why can GraphQL query complexity be security sensitive?
43. How should GraphQL complexity be tested safely?
44. What should be reviewed in API key security?
45. What security controls should webhooks use?
46. What risks exist when consuming third-party APIs?
47. What evidence should an API vulnerability report contain?
48. What are common API security root causes?
49. How do you prioritize API endpoints during a pentest?
50. Walk me through your complete API security testing methodology.

---

# Quick Revision

* API mapping:

```text
Discover

↓

Inventory

↓

Classify

↓

Test
```

* Authorization layers:

```text
Authentication

↓

Function

↓

Object

↓

Property
```

* BOLA:

```text
User B

↓

Object A

↓

Should Deny
```

* BFLA:

```text
Low Privilege

↓

Privileged Function

↓

Should Deny
```

* Property authorization:

```text
Object Accessible

≠

Every Property Accessible or Editable
```

* Mass assignment:

```text
Extra Property

↓

Backend Binding

↓

Persistent Unauthorized State?
```

* Versioning:

```text
v1 Security

vs

v2 Security
```

* Core principle:

```text
Frontend Restriction

≠

API Security Control
```

---

# API Security Quick Checklist

```text
DISCOVERY

[ ] Burp history
[ ] JavaScript
[ ] Documentation
[ ] OpenAPI / Swagger
[ ] Versions
[ ] Deprecated endpoints
[ ] GraphQL


AUTHENTICATION

[ ] No authentication
[ ] Invalid authentication
[ ] Expired token
[ ] Revocation
[ ] Logout
[ ] Refresh
[ ] Scope


OBJECT AUTHORIZATION

[ ] Read
[ ] Create relationships
[ ] Update
[ ] Delete
[ ] Download
[ ] Share
[ ] Nested objects
[ ] Cross-tenant


FUNCTION AUTHORIZATION

[ ] Admin functions
[ ] Privileged methods
[ ] Hidden endpoints
[ ] Role changes


PROPERTY AUTHORIZATION

[ ] Sensitive fields
[ ] Server-controlled fields
[ ] Mass assignment
[ ] Persistence
[ ] Excessive exposure


INPUT

[ ] Query
[ ] Path
[ ] JSON
[ ] Headers
[ ] Types
[ ] Null
[ ] Arrays
[ ] Objects
[ ] Extra properties


PROTOCOL

[ ] Methods
[ ] Content types
[ ] CORS
[ ] Errors


LIMITS

[ ] Pagination
[ ] Rate limits
[ ] Resource consumption
[ ] Query complexity
[ ] Bulk operations


VERSIONING

[ ] Current
[ ] Legacy
[ ] Deprecated
[ ] Equivalent controls


GRAPHQL

[ ] Queries
[ ] Mutations
[ ] Objects
[ ] Fields
[ ] Relationships
[ ] Complexity


OPERATIONS

[ ] API keys
[ ] Webhooks
[ ] Third-party APIs


VALIDATION

[ ] Controlled identities
[ ] Baseline
[ ] One-variable change
[ ] Reproducible
[ ] Persistent effect
[ ] Security boundary
[ ] Impact
```

---

# Key Takeaways

* API security testing begins with comprehensive discovery and inventory, not isolated endpoint fuzzing.
* APIs should be classified by authentication, role, object, business function, and version.
* Authentication, function-level authorization, object-level authorization, and property-level authorization are distinct security boundaries.
* BOLA testing requires controlled identities and objects and should cover read, update, delete, download, share, and other relevant actions.
* UUIDs and unpredictable identifiers do not replace server-side authorization.
* Object references may appear in URL paths, queries, JSON bodies, headers, cookies, and GraphQL variables.
* BFLA occurs when lower-privilege users can invoke functions intended for more privileged roles.
* Each HTTP method should be tested independently because authorization may differ between handlers.
* Property-level authorization determines which fields a user may read or modify even when they legitimately have access to the containing object.
* Mass-assignment testing should add controlled properties one at a time and verify persistent state afterward.
* Excessive data exposure should be assessed by comparing the returned fields with the user's actual business need and authorization.
* List, detail, search, filter, export, and field-selection endpoints may expose different data.
* API input testing should include types, nulls, arrays, objects, additional properties, malformed structures, and alternate content types.
* Rate-limit testing should use minimal controlled traffic and evaluate threshold, scope, reset behavior, and persistent effects.
* Resource-consumption testing should assess limits without attempting denial-of-service.
* APIs expose business logic directly and should be tested for replay, sequence manipulation, stale state, duplicate submission, and controlled concurrency.
* UI restrictions do not replace API-side security enforcement.
* Current, legacy, and deprecated API versions should be compared because security controls may differ.
* GraphQL authorization must be evaluated at operation, object, relationship, and field levels.
* GraphQL query complexity should be assessed safely without resource-exhaustion testing.
* API keys require appropriate scope, expiration, rotation, storage, and revocation controls.
* Webhooks require authentication, integrity verification, replay protection, and safe destination handling.
* Third-party API responses should be treated as untrusted external input.
* Strong API findings identify the exact failed security boundary, reproduce it with controlled identities, verify the resulting data or state, and demonstrate meaningful impact.
* The complete methodology is **discover → inventory → classify → map authentication → create controlled identities → map objects and properties → test object, function, and property authorization → test input and protocol handling → test limits and business logic → compare versions → review GraphQL and operational integrations → validate impact → collect evidence → report**.
