# Authorization & Access-Control Testing in Modern Applications

> **Module 16 — Modern Application Testing**

> **Progress**

```text
█████████░░░░░░ 9 / 15 Files
```

---

## Overview

* Authorization determines what an authenticated or unauthenticated user is allowed to access or perform.

* Modern applications often expose authorization boundaries across:

  * Web interfaces.
  * REST APIs.
  * GraphQL APIs.
  * WebSocket connections.
  * Mobile backends.
  * Administrative interfaces.
  * Multi-tenant environments.
  * Internal application services.

* Authentication may correctly identify a user while authorization still fails.

* A professional access-control assessment asks:

  ```text
  Who Is Making the Request?

  ↓

  What Resource Is Being Accessed?

  ↓

  Who Owns the Resource?

  ↓

  What Role Does the User Have?

  ↓

  Which Tenant Does the User Belong To?

  ↓

  What Action Is Being Performed?

  ↓

  Should This Exact Combination Be Allowed?
  ```

* Burp Suite allows these authorization decisions to be tested systematically by preserving legitimate baselines and changing one security-relevant variable at a time.

---

## Learning Objectives

* By the end of this lesson, you will be able to:

  * Distinguish authentication from authorization.
  * Understand horizontal and vertical access control.
  * Understand object-level and function-level authorization.
  * Identify authorization boundaries in modern applications.
  * Build user, role, object, action, and tenant matrices.
  * Test IDOR and BOLA systematically.
  * Test BFLA and privileged functionality.
  * Analyze multi-tenant isolation.
  * Test authorization across REST, GraphQL, and WebSockets.
  * Identify client-side-only access controls.
  * Test hidden and directly accessible functionality.
  * Analyze ownership and relationship-based authorization.
  * Test state-changing operations safely.
  * Recognize mass-assignment authorization risks.
  * Avoid false positives caused by public or intentionally shared resources.
  * Build a repeatable authorization-testing workflow.
  * Collect strong evidence for access-control findings.

---

## Authentication vs Authorization

* Authentication asks:

  ```text
  Who Are You?
  ```

* Authorization asks:

  ```text
  What Are You Allowed to Access or Do?
  ```

---

## Important Principle

```text
Valid Authentication

≠

Valid Authorization
```

* A user may possess a completely valid session or token and still gain unauthorized access because the application fails to enforce the correct permission.

---

## Authorization Decision Model

* A useful mental model is:

  ```text
  Subject

  +

  Action

  +

  Object

  +

  Context

  →

  Authorization Decision
  ```

* Where:

  ```text
  Subject = User / Service / Role

  Action = Read / Create / Update / Delete / Approve

  Object = Account / File / Order / Message / User

  Context = Tenant / Ownership / State / Relationship
  ```

---

## Example

```text
User A

+

Read

+

Invoice 501

+

Invoice Belongs to User B

↓

Deny
```

* The server should independently enforce this decision.

---

## Access-Control Categories

* Common authorization categories include:

  ```text
  Horizontal Access Control

  Vertical Access Control

  Object-Level Authorization

  Function-Level Authorization

  Tenant Isolation

  Relationship-Based Authorization

  State-Based Authorization
  ```

---

## Horizontal Access Control

* Horizontal access control separates users with similar privilege levels.

* Example:

  ```text
  User A

  vs

  User B
  ```

* Both may have:

  ```text
  Role = Standard User
  ```

* But User A should not automatically access User B's private resources.

---

## Horizontal Privilege Escalation

* A horizontal privilege escalation occurs when one user can access another user's unauthorized data or functionality at the same general privilege level.

* Example:

  ```text
  User A

  →

  User B's Private Invoice
  ```

---

## Vertical Access Control

* Vertical access control separates privilege levels.

* Example:

  ```text
  Standard User

  Moderator

  Administrator
  ```

* A standard user should not automatically gain administrative functionality.

---

## Vertical Privilege Escalation

* Example:

  ```text
  Standard User

  →

  Administrative User Management
  ```

* If the backend fails to enforce the required role, vertical privilege escalation may exist.

---

## Object-Level Authorization

* Object-level authorization determines whether a user can access a specific resource.

* Example:

  ```http
  GET /api/orders/501
  ```

* Security question:

  ```text
  Is the Current User Authorized to Access Order 501?
  ```

---

## IDOR

* IDOR stands for:

  ```text
  Insecure Direct Object Reference
  ```

* It commonly occurs when an application exposes an object identifier but fails to verify whether the requesting user is authorized to access the referenced object.

---

## Example IDOR Pattern

* User A legitimately accesses:

  ```http
  GET /api/invoices/501
  ```

* User B's controlled invoice is:

  ```text
  502
  ```

* Controlled test:

  ```http
  GET /api/invoices/502
  ```

* Security question:

  ```text
  Does the Server Verify Ownership or Permission?
  ```

---

## BOLA

* BOLA stands for:

  ```text
  Broken Object Level Authorization
  ```

* The term is commonly used in API security.

* Conceptually:

  ```text
  IDOR

  and

  BOLA
  ```

* Often describe closely related object-authorization failures.

---

## Object Identifiers

* Authorization-relevant identifiers may appear as:

  ```text
  Numeric IDs

  UUIDs

  Usernames

  Email Addresses

  Slugs

  Filenames

  Order Numbers

  Account Numbers

  GraphQL IDs

  Encoded References
  ```

---

## Important Principle

```text
Hard-to-Guess Identifier

≠

Authorization
```

* A UUID may reduce guessability.

* It does not replace a server-side permission check.

---

## Function-Level Authorization

* Function-level authorization determines whether a user may perform a particular operation.

* Example:

  ```http
  POST /api/admin/users/42/disable
  ```

* Security question:

  ```text
  Is the Current User Allowed to Perform This Function?
  ```

---

## BFLA

* BFLA stands for:

  ```text
  Broken Function Level Authorization
  ```

* It occurs when users can access functions outside their intended privilege level.

---

## Examples

```text
Standard User

→

Delete Another User
```

```text
Viewer

→

Modify Configuration
```

```text
Support Agent

→

Perform Administrator Action
```

---

## Authorization Dimensions

* Modern access-control testing should map several dimensions:

  ```text
  Identity

  Role

  Tenant

  Object

  Ownership

  Action

  State

  Relationship
  ```

---

## Authorization Matrix

| User   | Role  | Object   | Owner  | Action | Expected          |
| ------ | ----- | -------- | ------ | ------ | ----------------- |
| User A | User  | A-Object | User A | Read   | Allow             |
| User A | User  | B-Object | User B | Read   | Deny              |
| User A | User  | B-Object | User B | Update | Deny              |
| Admin  | Admin | B-Object | User B | Read   | Depends on policy |

* The matrix converts vague testing into explicit security hypotheses.

---

## Why Two Controlled Accounts Matter

* Two accounts allow reliable testing of:

  ```text
  User A Object

  vs

  User B Object
  ```

* Without known ownership, an object-access test may be ambiguous.

---

## Recommended Controlled Identities

* Where authorized, useful test identities may include:

  ```text
  User A

  User B

  Low-Privilege User

  Higher-Privilege User

  Different-Tenant User
  ```

* Only create or use accounts permitted by the testing scope.

---

## Object Ownership Map

| Object    | Owner  | Tenant   | Sensitivity |
| --------- | ------ | -------- | ----------- |
| Profile A | User A | Tenant 1 | Medium      |
| Invoice A | User A | Tenant 1 | High        |
| Profile B | User B | Tenant 1 | Medium      |
| Invoice C | User C | Tenant 2 | High        |

* Known ownership is essential for strong authorization evidence.

---

## Start With a Legitimate Baseline

* Example:

  ```text
  User A

  ↓

  GET /api/orders/501

  ↓

  200 OK

  ↓

  Order 501 Belongs to User A
  ```

* Record:

  ```text
  User

  Role

  Tenant

  Object

  Owner

  Action

  Response

  State
  ```

---

## Change One Authorization Variable

* Preferred:

  ```text
  Same User

  Same Token

  Same Method

  Same Endpoint Pattern

  Same Parameters

  Change Only Object ID
  ```

* This creates strong evidence.

---

## Avoid Multi-Variable Tests

* Avoid simultaneously changing:

  ```text
  User

  +

  Token

  +

  Object ID

  +

  HTTP Method

  ```

* If the response changes, you will not know which variable caused it.

---

## Burp Authorization Workflow

```text
Proxy

↓

Capture Legitimate Request

↓

Send to Repeater

↓

Label User / Role / Tenant

↓

Record Object Ownership

↓

Duplicate Request

↓

Change One Authorization Variable

↓

Send

↓

Compare Response

↓

Verify Data or State

↓

Document Evidence
```

---

## Testing Read Access

* Example:

  ```http
  GET /api/documents/501
  ```

* Controlled test:

  ```text
  User A Credential

  +

  User B Controlled Document ID
  ```

* Determine whether unauthorized content is returned.

---

## Testing Update Access

* Example:

  ```http
  PATCH /api/profile/501
  ```

* Security question:

  ```text
  Can User A Modify User B's Controlled Profile?
  ```

* Use harmless test data.

---

## Testing Delete Access

* Delete operations can cause irreversible impact.

* Use only:

  ```text
  Disposable Controlled Objects
  ```

* Or avoid destructive confirmation when authorization does not permit it.

---

## State Verification

* A successful-looking response is not enough.

* After an update request:

  ```text
  Modified Request

  ↓

  Response

  ↓

  Fetch Object Again

  ↓

  Verify Actual State
  ```

---

## Important Principle

```text
HTTP 200

≠

Action Succeeded
```

* Likewise:

  ```text
  HTTP 403

  ≠

  No Side Effect Occurred
  ```

* Verify actual state where safe.

---

## Response Comparison

* Compare:

  ```text
  Status Code

  Response Length

  Response Body

  Headers

  Object Data

  Error Message

  State Change
  ```

* Burp Comparer can help when differences are subtle.

---

## User ID Parameters

* Common authorization-relevant fields:

  ```text
  user_id

  account_id

  owner_id

  customer_id

  member_id
  ```

* Never assume a server validates these correctly simply because they are hidden from the UI.

---

## Nested Object References

* Example:

  ```http
  GET /api/users/42/orders/501
  ```

* Security questions:

  ```text
  Does Order 501 Belong to User 42?

  Is the Current User Authorized for User 42?

  Does the Server Validate Both Relationships?
  ```

---

## Body-Based Object References

* Authorization identifiers may appear in JSON:

  ```json
  {
    "account_id": 42,
    "document_id": 501
  }
  ```

* Test each security-relevant relationship independently.

---

## Header-Based Authorization Context

* Some applications use headers such as:

  ```text
  X-User-ID

  X-Account-ID

  X-Tenant-ID

  X-Organization-ID
  ```

* These values should not automatically be trusted merely because the client sends them.

---

## Cookie-Based Context

* Authorization context may also appear in cookies.

* Example:

  ```text
  active_workspace=91
  ```

* Security question:

  ```text
  Does Changing Client-Controlled Context Change Authorization Improperly?
  ```

---

## Multi-Tenant Applications

* Multi-tenant systems separate organizations or customers.

* Example:

  ```text
  Tenant A

  ├── User A
  └── User B

  Tenant B

  ├── User C
  └── User D
  ```

---

## Tenant Isolation

* A user in Tenant A should not normally access private Tenant B resources.

* Test:

  ```text
  Cross-User Same Tenant

  and

  Cross-Tenant
  ```

* These are different authorization boundaries.

---

## Tenant Authorization Matrix

| User    | Tenant   | Object Tenant | Expected                              |
| ------- | -------- | ------------- | ------------------------------------- |
| User A  | Tenant 1 | Tenant 1      | Depends on role/ownership             |
| User A  | Tenant 1 | Tenant 2      | Deny                                  |
| Admin A | Tenant 1 | Tenant 1      | Depends on policy                     |
| Admin A | Tenant 1 | Tenant 2      | Usually deny unless explicitly global |

---

## Tenant Identifiers

* Look for:

  ```text
  tenant_id

  organization_id

  workspace_id

  company_id

  team_id

  project_id
  ```

---

## Active Tenant Switching

* Some applications allow users to belong to multiple organizations.

* Example:

  ```text
  User

  ↓

  Select Workspace

  ↓

  Client Sends workspace_id
  ```

* Security question:

  ```text
  Does the Server Verify Membership in the Selected Workspace?
  ```

---

## Role-Based Access Control

* RBAC stands for:

  ```text
  Role-Based Access Control
  ```

* Permissions are assigned based on roles.

* Example:

  ```text
  Viewer

  Editor

  Manager

  Administrator
  ```

---

## Role Matrix

| Action       | Viewer | Editor | Admin |
| ------------ | :----: | :----: | :---: |
| Read         |   Yes  |   Yes  |  Yes  |
| Create       |   No   |   Yes  |  Yes  |
| Update       |   No   |   Yes  |  Yes  |
| Delete       |   No   |  Maybe |  Yes  |
| Manage Users |   No   |   No   |  Yes  |

* Compare actual behavior against intended policy.

---

## Hidden UI Controls

* A UI may hide administrative buttons from low-privilege users.

* Example:

  ```text
  Admin UI:

  [Delete User]

  Standard User UI:

  Button Hidden
  ```

* Security question:

  ```text
  Does the Backend Also Enforce the Restriction?
  ```

---

## Important Principle

```text
Hidden Button

≠

Access Control
```

* Client-side visibility is not a security boundary.

---

## Direct Endpoint Access

* A low-privilege user may manually request a hidden endpoint.

* Example:

  ```http
  POST /api/admin/users/42/disable
  ```

* The server must enforce authorization independently.

---

## Forced Browsing

* Forced browsing refers to directly requesting functionality or resources not linked in the current user's interface.

* Examples:

  ```text
  /admin

  /internal

  /manage-users

  /api/admin/settings
  ```

* Discovery alone is not a vulnerability.

* Unauthorized access or action must be demonstrated.

---

## HTTP Method Authorization

* Different methods may have different authorization requirements.

* Example:

  ```text
  GET /api/users/42

  PATCH /api/users/42

  DELETE /api/users/42
  ```

* Test each relevant operation separately.

---

## Method Switching

* If:

  ```text
  GET
  ```

* Is permitted, do not assume:

  ```text
  PUT

  PATCH

  DELETE
  ```

* Are equally protected.

---

## Alternate Endpoint Versions

* Modern applications may expose:

  ```text
  /api/v1/users/42

  /api/v2/users/42

  /graphql

  /legacy/users/42
  ```

* Authorization may differ between implementations.

---

## Legacy Endpoints

* Old endpoints may remain reachable after newer interfaces are introduced.

* Security question:

  ```text
  Does the Legacy Endpoint Enforce the Same Authorization Policy?
  ```

---

## REST Authorization

* REST authorization commonly depends on:

  ```text
  Endpoint

  Method

  Object ID

  User

  Role

  Tenant
  ```

---

## REST Test Example

```text
User A

↓

GET /api/orders/501

↓

Own Object

↓

200 OK
```

* Controlled variation:

  ```text
  Change 501 → User B Controlled Order
  ```

* Keep the credential unchanged.

---

## GraphQL Authorization

* GraphQL often uses one endpoint:

  ```text
  POST /graphql
  ```

* Authorization occurs deeper inside:

  ```text
  Query

  Mutation

  Field

  Resolver

  Object
  ```

---

## GraphQL Object Authorization

* Example:

  ```graphql
  query {
    user(id: "42") {
      email
    }
  }
  ```

* Security question:

  ```text
  Is the Current User Authorized to Access User 42?
  ```

---

## GraphQL Field Authorization

* A user may legitimately access an object but not every field.

* Example:

  ```text
  User Profile

  ├── displayName       Allowed
  ├── avatar            Allowed
  ├── privateEmail      Restricted
  └── internalRole      Restricted
  ```

* Test sensitive fields separately.

---

## GraphQL Mutation Authorization

* Example:

  ```graphql
  mutation {
    deleteUser(id: "42")
  }
  ```

* Security question:

  ```text
  Is the Current Role Allowed to Execute This Mutation?
  ```

---

## WebSocket Authorization

* WebSocket authorization may apply to:

  ```text
  Connection

  Message

  Action

  Subscription

  Object

  Channel
  ```

---

## Connection Authorization

* A user may be allowed to establish a connection but not perform every operation over it.

* Therefore:

  ```text
  Connected

  ≠

  Authorized for Every Message
  ```

---

## Subscription Authorization

* Example:

  ```json
  {
    "action": "subscribe",
    "channel": "account-42"
  }
  ```

* Security question:

  ```text
  Is the Connected User Authorized to Receive Events for Account 42?
  ```

---

## Message-Level Authorization

* Example:

  ```json
  {
    "action": "delete_message",
    "message_id": 781
  }
  ```

* Verify authorization for the exact message action and object.

---

## Relationship-Based Authorization

* Some permissions depend on relationships rather than simple ownership.

* Example:

  ```text
  User

  →

  Member of Team

  →

  Team Owns Project

  →

  Project Contains Document
  ```

* Authorization may depend on the entire chain.

---

## Relationship Test

```text
User A

↓

Member of Team 1

↓

Requests Team 2 Document

↓

Server Should Validate Relationship
```

---

## Parent-Child Authorization

* Example:

  ```text
  Organization

  ↓

  Project

  ↓

  Document
  ```

* Security question:

  ```text
  Does the Server Verify That the Requested Child Object
  Actually Belongs to the Authorized Parent?
  ```

---

## Indirect Object References

* An object may be referenced indirectly.

* Example:

  ```json
  {
    "invoice_number": "INV-2026-501"
  }
  ```

* The authorization requirement remains the same.

---

## Encoded IDs

* Identifiers may be:

  ```text
  Base64 Encoded

  Hashed

  Obfuscated

  UUID-Based
  ```

* Encoding or obscurity does not replace authorization.

---

## Public vs Private Objects

* Before reporting object access, determine whether the resource is intended to be:

  ```text
  Public

  Shared

  Organization-Wide

  Private

  Role-Restricted
  ```

* Incorrect assumptions about intended visibility cause false positives.

---

## Shared Resources

* Example:

  ```text
  Document Shared With User A
  ```

* User A accessing the document is not an IDOR even if User A is not the owner.

* Authorization may be relationship-based.

---

## Ownership vs Permission

```text
Not Owner

≠

Not Authorized
```

* Always distinguish:

  ```text
  Ownership

  from

  Permission
  ```

---

## State-Based Authorization

* Some actions are allowed only in particular workflow states.

* Example:

  ```text
  Draft

  ↓

  Submitted

  ↓

  Approved
  ```

* A user may have permission to edit:

  ```text
  Draft
  ```

* But not:

  ```text
  Approved
  ```

---

## Workflow Authorization

* Security question:

  ```text
  Does the Server Enforce Both User Permission
  and Current Object State?
  ```

---

## Example

```text
User Is Editor

+

Document Is Approved

↓

Edit Should Be Denied
```

* If the server checks only the role but ignores state, authorization may fail.

---

## Approval Workflows

* Sensitive workflows may require:

  ```text
  Requester

  Reviewer

  Approver
  ```

* Security questions:

  ```text
  Can the Requester Approve Their Own Request?

  Can Required Review Be Skipped?

  Can an Unauthorized Role Change Status Directly?
  ```

---

## Mass Assignment and Authorization

* APIs may accept JSON objects with many properties.

* Example:

  ```json
  {
    "display_name": "Test User",
    "role": "admin"
  }
  ```

* If `role` should be server-controlled, accepting it from an ordinary user may create an authorization vulnerability.

---

## Client-Controlled Privilege Fields

* Look for:

  ```text
  role

  is_admin

  permissions

  account_type

  owner_id

  tenant_id

  status
  ```

* Do not blindly add arbitrary fields.

* First infer likely fields from legitimate application behavior, schemas, responses, or client code.

---

## Property-Level Authorization

* A user may be authorized to update an object but not every property.

* Example:

  ```text
  Allowed:

  display_name

  avatar

  timezone
  ```

* Restricted:

  ```text
  role

  account_status

  billing_plan

  owner_id
  ```

---

## Important Principle

```text
Authorized to Update Object

≠

Authorized to Update Every Property
```

---

## Create Operations

* Authorization also applies during object creation.

* Example:

  ```json
  {
    "project_name": "Demo",
    "organization_id": 91
  }
  ```

* Security question:

  ```text
  Can the User Create an Object Inside an Unauthorized Organization?
  ```

---

## Delete Operations

* Delete permissions may differ from read or update permissions.

* Example:

  ```text
  Can Read

  ≠

  Can Delete
  ```

---

## Bulk Operations

* Modern APIs may support:

  ```text
  Bulk Delete

  Bulk Update

  Batch Actions

  Multi-Select Operations
  ```

* Authorization must be checked for every affected object.

---

## Bulk Authorization Example

```json
{
  "document_ids": [501, 502, 503]
}
```

* Security question:

  ```text
  Does the Server Verify Authorization for Every Document?
  ```

---

## Partial Authorization Failures

* A bulk request may contain:

  ```text
  Authorized Object

  +

  Unauthorized Object
  ```

* Observe whether the server:

  ```text
  Rejects Entire Request

  Processes Only Authorized Objects

  Processes Everything
  ```

* Interpret according to intended behavior.

---

## Export Functions

* Export functionality may expose large amounts of data.

* Examples:

  ```text
  CSV Export

  PDF Export

  Report Download

  Backup

  Data Archive
  ```

* Authorization must be checked at the export operation and underlying data level.

---

## File Authorization

* File access may use:

  ```text
  File ID

  Storage Key

  Filename

  Signed URL

  Download Token
  ```

* Security question:

  ```text
  Is the Requesting User Authorized to Access This File?
  ```

---

## Signed URLs

* Signed URLs may intentionally grant temporary access.

* Review:

  ```text
  Expiration

  Scope

  Resource Binding

  Permission

  Reusability
  ```

* Possession of a valid signed URL may itself represent authorization by design.

---

## Administrative APIs

* Administrative functionality may exist even when no visible admin interface is exposed.

* Look for legitimate evidence of:

  ```text
  /admin

  /manage

  /internal

  Privileged GraphQL Mutations

  Privileged WebSocket Actions
  ```

* Test only endpoints discovered within authorized scope.

---

## Client-Side Role Checks

* JavaScript may contain logic such as:

  ```text
  if user.role == "admin"
  ```

* This may control UI rendering.

* It should not be the only access-control enforcement.

---

## Frontend Route Guards

* Single-page applications may block navigation to routes client-side.

* Example:

  ```text
  /admin/settings
  ```

* Direct backend requests still require server-side authorization.

---

## Parameter-Based Role Switching

* Be cautious with parameters such as:

  ```text
  role=admin

  admin=true

  account_type=premium
  ```

* A client-supplied value should not independently grant privilege.

---

## Authorization Caching

* Applications may cache authorization decisions.

* Security questions include:

  ```text
  What Happens After Role Change?

  What Happens After User Removal?

  What Happens After Tenant Membership Revocation?
  ```

---

## Permission Revocation

* Example:

  ```text
  User Has Project Access

  ↓

  Access Removed

  ↓

  Existing Session Continues
  ```

* Verify whether subsequent requests reflect the updated authorization state.

---

## Long-Lived Connections

* WebSockets and long-lived sessions may retain stale authorization state.

* Review behavior after:

  ```text
  Role Change

  Tenant Removal

  Account Disablement

  Logout
  ```

---

## Authorization and Caching Layers

* Modern architectures may involve:

  ```text
  Browser

  CDN

  API Gateway

  Backend

  Microservice
  ```

* Authorization should not be bypassed because different layers interpret identity inconsistently.

---

## Microservice Trust Boundaries

* Internal services may receive identity information through headers or tokens.

* Example:

  ```text
  X-User-ID

  X-Role

  X-Tenant-ID
  ```

* External clients should not be able to forge trusted internal identity context.

---

## Proxy-Injected Headers

* Security-sensitive headers may be intended to be inserted by:

  ```text
  Reverse Proxy

  API Gateway

  Identity-Aware Proxy
  ```

* Test whether client-supplied versions are ignored, overwritten, or safely handled.

---

## Authorization Hypothesis Template

```text
Given:

[User / Role / Tenant]

When:

[Action]

Against:

[Object / Function]

The Server Should:

[Allow / Deny]

Because:

[Ownership / Role / Tenant / Relationship / State]
```

---

## Example Hypothesis

```text
Given:

User A in Tenant 1

When:

Reading Invoice 502

Against:

Invoice Owned by User B in Tenant 2

The Server Should:

Deny

Because:

User A Has No Ownership, Sharing Relationship, or Tenant Permission
```

---

## Authorization Test Card

```text
Test ID:

Endpoint / Operation:

Protocol:

Method:

User:

Role:

Tenant:

Object:

Object Owner:

Object Tenant:

Action:

Expected Permission:

Baseline Request:

Modified Variable:

Modified Request:

Response:

Final State:

Security Rule:

Actual Result:

Impact:

Evidence:

Conclusion:
```

---

## Access-Control Testing Workflow

```text
Define Scope

↓

Create Controlled Identities

↓

Map Roles and Tenants

↓

Map Sensitive Functions

↓

Map Objects and Ownership

↓

Capture Legitimate Baselines

↓

Build Authorization Matrix

↓

Define Security Hypothesis

↓

Change One Authorization Variable

↓

Send Controlled Request

↓

Compare Response

↓

Verify Data / State

↓

Repeat Across Protocols

↓

Validate Reproducibility

↓

Document Evidence
```

---

## Step 1 — Define Identities

* Record:

  ```text
  User A

  User B

  Roles

  Tenants

  Memberships
  ```

---

## Step 2 — Map Roles

* Identify intended permissions for:

  ```text
  Standard Users

  Editors

  Managers

  Administrators
  ```

* Where applicable.

---

## Step 3 — Map Objects

* Record:

  ```text
  Object Type

  Object ID

  Owner

  Tenant

  Sensitivity
  ```

---

## Step 4 — Map Actions

* For each object, identify:

  ```text
  Create

  Read

  Update

  Delete

  Share

  Approve

  Export

  Manage
  ```

---

## Step 5 — Build Matrix

* Combine:

  ```text
  User

  +

  Role

  +

  Tenant

  +

  Object

  +

  Action
  ```

* Define expected behavior before testing.

---

## Step 6 — Capture Baseline

* Perform the action legitimately.

* Preserve the exact request and expected result.

---

## Step 7 — Modify One Variable

* Change only:

  ```text
  Object ID

  or

  Role Context

  or

  Tenant ID

  or

  Action
  ```

* Keep other variables stable.

---

## Step 8 — Compare

* Analyze:

  ```text
  Status

  Body

  Data

  Headers

  State
  ```

---

## Step 9 — Verify Ownership

* Confirm the tested object truly belongs to the other controlled user or tenant.

---

## Step 10 — Verify State

* For state-changing requests, independently verify whether the action occurred.

---

## Step 11 — Test Protocol Variants

* Compare authorization across:

  ```text
  REST

  GraphQL

  WebSocket

  Alternate API Versions
  ```

---

## Step 12 — Test Revocation

* Where relevant, remove a permission or membership and verify subsequent access.

---

## Step 13 — Validate Impact

* Determine exactly what unauthorized capability exists.

---

## Example — Horizontal IDOR

### Baseline

```text
User A

↓

GET /api/invoices/501

↓

Invoice 501 Belongs to User A

↓

200 OK
```

### Controlled Test

```text
User A

↓

GET /api/invoices/502

↓

Invoice 502 Belongs to Controlled User B
```

### Vulnerable Result

```text
200 OK

+

User B Private Invoice Returned
```

### Security Rule

```text
Users May Read Only Their Own Private Invoices
```

* This provides strong evidence of broken object-level authorization.

---

## Example — Vertical Privilege Escalation

### Baseline

```text
Administrator

↓

POST /api/admin/users/42/disable

↓

Action Succeeds
```

### Controlled Test

```text
Standard User

↓

Same Endpoint

↓

Controlled Disposable User
```

### Vulnerable Result

```text
Action Succeeds
```

* This may demonstrate broken function-level authorization.

---

## Example — Cross-Tenant Access

```text
User A

Tenant 1

↓

GET /api/projects/901

↓

Project 901 Belongs to Tenant 2
```

* If private Tenant 2 data is returned without a legitimate relationship, tenant isolation may be broken.

---

## Example — Property-Level Authorization

### Legitimate Request

```json
{
  "display_name": "User A"
}
```

### Observed Response or Schema

```json
{
  "display_name": "User A",
  "role": "user"
}
```

### Controlled Hypothesis

```text
Can a Standard User Modify the Server-Controlled Role Property?
```

* Test only on your controlled account.

* Verify actual privilege after the request rather than relying only on the response.

---

## Example — GraphQL Field Authorization

### Baseline

```graphql
query {
  me {
    id
    displayName
  }
}
```

### Controlled Test

* Request a sensitive field legitimately discovered in the schema or application:

  ```graphql
  query {
    me {
      id
      displayName
      internalRole
    }
  }
  ```

* Determine whether the field is intended for the current role.

---

## Example — WebSocket Subscription

### Baseline

```json
{
  "action": "subscribe",
  "channel": "user-42"
}
```

* Channel belongs to controlled User A.

### Controlled Test

```text
Change:

user-42

to

user-73
```

* Where `user-73` belongs to controlled User B.

* Determine whether User A receives User B's private events.

---

## Example — Permission Revocation

```text
User Has Access to Project

↓

Capture Valid Request

↓

Remove User From Project

↓

Replay Request

↓

Verify Access Is Denied
```

* This tests whether authorization state updates correctly.

---

## Evidence Requirements

* Strong authorization evidence should include:

  ```text
  User Identity

  Role

  Tenant

  Object

  Object Owner

  Object Tenant

  Intended Permission

  Baseline Request

  Modified Request

  Single Changed Variable

  Response

  Final State

  Unauthorized Data or Action

  Reproduction Steps

  Security Impact
  ```

---

## Strong IDOR Evidence

```text
User A

+

User B Controlled Object

+

Same Request Except Object ID

+

Unauthorized Private Data Returned
```

* This is substantially stronger than:

  ```text
  Changed Random ID

  +

  Received 200
  ```

---

## Anomaly vs Vulnerability

* Anomaly:

  ```text
  Different Object ID Returns Data
  ```

* Validated vulnerability:

  ```text
  Object Belongs to Another Controlled User

  +

  Resource Is Not Public or Shared

  +

  Requesting User Lacks Permission

  +

  Server Returns Sensitive Data or Performs Unauthorized Action
  ```

---

## False Positive Causes

* Common causes include:

  ```text
  Public Resource

  Shared Resource

  Organization-Wide Permission

  Administrator Role

  Cached Response

  Incorrect Object Ownership Assumption

  Stale Session

  Test Data Confusion
  ```

---

## False Positive Control

```text
Confirm User

↓

Confirm Role

↓

Confirm Tenant

↓

Confirm Object Owner

↓

Confirm Sharing State

↓

Confirm Intended Permission

↓

Restore Baseline

↓

Change One Variable

↓

Repeat

↓

Verify Data / State

↓

Confirm Security Impact
```

---

## Authorization Testing Checklist

### Identity

* Record:

  ```text
  User

  Role

  Tenant

  Memberships
  ```

---

### Objects

* Record:

  ```text
  Object Type

  ID

  Owner

  Tenant

  Visibility

  Sensitivity
  ```

---

### Horizontal Access

* Test:

  ```text
  User A → User A Object

  User A → User B Object

  User B → User A Object
  ```

---

### Vertical Access

* Test:

  ```text
  Standard User → Standard Function

  Standard User → Privileged Function

  Higher Role → Privileged Function
  ```

---

### Tenant Isolation

* Test:

  ```text
  Same Tenant

  Cross Tenant

  Multi-Membership
  ```

---

### Actions

* Review:

  ```text
  Create

  Read

  Update

  Delete

  Share

  Export

  Approve

  Manage
  ```

---

### Properties

* Review:

  ```text
  User-Editable Fields

  Server-Controlled Fields

  Role Fields

  Ownership Fields

  Tenant Fields

  Status Fields
  ```

---

### REST

* Review:

  ```text
  Methods

  IDs

  Nested Resources

  API Versions

  Bulk Operations
  ```

---

### GraphQL

* Review:

  ```text
  Queries

  Mutations

  Objects

  Fields

  Resolvers
  ```

---

### WebSockets

* Review:

  ```text
  Connection

  Messages

  Actions

  Subscriptions

  Channels
  ```

---

### State

* Review:

  ```text
  Workflow State

  Permission Changes

  Membership Revocation

  Role Changes
  ```

---

## Authorization Investigation Journal

```text
Application:

Date:

Scope:

Users:

Roles:

Tenants:

Memberships:

Object Types:

Objects:

Object Owners:

Object Tenants:

Public Objects:

Shared Objects:

Sensitive Objects:

Functions:

Privileged Functions:

REST Endpoints:

GraphQL Operations:

WebSocket Actions:

Authorization Matrix:

Horizontal Tests:

Vertical Tests:

Object-Level Tests:

Function-Level Tests:

Tenant Tests:

Property-Level Tests:

Bulk Operations:

File Access:

Export Functions:

Workflow-State Tests:

Permission Revocation:

Role Changes:

Cross-Protocol Differences:

High-Priority Hypotheses:

Validated Findings:

False Positives:

Evidence:

Next Steps:
```

---

## Professional Tips

* Always know which user, role, and tenant generated each request.
* Use at least two controlled accounts when testing horizontal authorization.
* Record object ownership before changing identifiers.
* Define the expected authorization rule before sending the modified request.
* Change one authorization variable at a time.
* Test read, update, delete, create, export, share, and approval permissions separately.
* Do not assume unpredictable UUIDs provide authorization.
* Distinguish ownership from legitimate sharing or delegated permission.
* Test same-tenant and cross-tenant boundaries independently.
* Verify backend controls instead of trusting hidden UI elements.
* Test privileged endpoints directly only when they are legitimately discovered and within scope.
* Check authorization across REST, GraphQL, WebSockets, and alternate API versions.
* Test sensitive GraphQL fields as well as entire objects.
* Test WebSocket subscription authorization, not only connection authentication.
* Consider property-level authorization when APIs accept complex objects.
* Infer potential restricted properties from legitimate evidence rather than blindly guessing fields.
* Verify actual state after update or delete operations.
* Use disposable objects for destructive tests.
* Test authorization after role, membership, or permission revocation when appropriate.
* Treat `200 OK` as evidence of a response, not proof of successful exploitation.
* Confirm whether a resource is public, shared, or private before reporting IDOR.
* Build authorization matrices to prevent random testing.
* Preserve exact baseline and modified requests for strong reports.

---

## Common Mistakes

* Confusing authentication with authorization.
* Testing random object IDs without knowing ownership.
* Reporting every accessible object as IDOR.
* Ignoring public or intentionally shared resources.
* Assuming `403` means no backend side effect occurred.
* Assuming `200` means an unauthorized action succeeded.
* Changing several variables at once.
* Using uncontrolled third-party accounts or objects.
* Testing only GET requests.
* Ignoring create, update, delete, share, export, and approval actions.
* Ignoring property-level authorization.
* Blindly adding fields without evidence that the application recognizes them.
* Assuming hidden UI functionality is protected.
* Ignoring direct API access.
* Ignoring alternate API versions.
* Ignoring GraphQL field-level authorization.
* Ignoring WebSocket subscription authorization.
* Treating UUID unpredictability as an access-control mechanism.
* Ignoring tenant boundaries.
* Failing to distinguish same-tenant from cross-tenant authorization.
* Ignoring relationship-based permissions.
* Ignoring workflow state.
* Performing destructive tests on real data.
* Failing to verify final state.
* Failing to retest after permission revocation.
* Reporting anomalies without demonstrating unauthorized data access or action.

---

## Practice Tasks

### Task 1 — Select an Authorized Application

* Use:

  ```text
  Deliberately Vulnerable Lab

  System You Own

  or

  Explicitly Authorized Target
  ```

---

### Task 2 — Create an Identity Matrix

* Record at least:

  ```text
  User A

  User B
  ```

* If available, also record:

  ```text
  Different Roles

  Different Tenants
  ```

---

### Task 3 — Map Five Objects

* Record:

  ```text
  Object Type

  ID

  Owner

  Tenant

  Visibility
  ```

---

### Task 4 — Map Actions

* For each object, identify:

  ```text
  Read

  Create

  Update

  Delete

  Share

  Export
  ```

* Where available.

---

### Task 5 — Build an Authorization Matrix

* Define expected:

  ```text
  Allow

  or

  Deny
  ```

* Before testing.

---

### Task 6 — Test Horizontal Authorization

* Using controlled accounts:

  ```text
  User A Credential

  +

  User B Controlled Object
  ```

* Modify only the object identifier.

---

### Task 7 — Test Vertical Authorization

* Compare a privileged action between:

  ```text
  Higher-Privilege Account

  and

  Lower-Privilege Controlled Account
  ```

---

### Task 8 — Test Tenant Isolation

* If the lab supports tenants, compare:

  ```text
  Same-Tenant Object

  vs

  Different-Tenant Controlled Object
  ```

---

### Task 9 — Test Property Authorization

* Identify one legitimate server-controlled or privilege-sensitive property.

* Determine whether an ordinary user can modify it.

---

### Task 10 — Test a State-Changing Operation

* Use a harmless disposable object.

* Record:

  ```text
  Initial State

  Request

  Response

  Final State
  ```

---

### Task 11 — Review GraphQL

* If available, test authorization at:

  ```text
  Query

  Mutation

  Field
  ```

* Levels.

---

### Task 12 — Review WebSockets

* If available, test:

  ```text
  Message Actions

  Object IDs

  Subscription Channels
  ```

---

### Task 13 — Test Permission Revocation

* Remove a controlled permission or membership.

* Verify whether access changes correctly.

---

### Task 14 — Generate Security Hypotheses

* Write at least five questions such as:

  ```text
  Can User A read User B's private object?

  Can a standard user call an administrator function?

  Can Tenant 1 access Tenant 2 data?

  Can a user modify a server-controlled privilege field?

  Does a WebSocket subscription verify channel authorization?
  ```

---

## Interview Questions

1. What is authorization?
2. What is the difference between authentication and authorization?
3. What is horizontal access control?
4. What is vertical access control?
5. What is horizontal privilege escalation?
6. What is vertical privilege escalation?
7. What is object-level authorization?
8. What is IDOR?
9. What is BOLA?
10. What is BFLA?
11. Why are UUIDs not a replacement for authorization?
12. Why are two controlled accounts useful when testing IDOR?
13. What information should you record about an object before testing authorization?
14. What is an authorization matrix?
15. Why should you define expected behavior before sending a test request?
16. Why should only one authorization variable be changed at a time?
17. How would you test horizontal authorization in Burp Repeater?
18. How would you test vertical authorization?
19. How do you verify whether an unauthorized update actually succeeded?
20. Why does `200 OK` not necessarily prove an authorization vulnerability?
21. Why does `403 Forbidden` not always prove no side effect occurred?
22. What is tenant isolation?
23. How would you test cross-tenant authorization?
24. What is RBAC?
25. Why are hidden buttons not a security control?
26. What is forced browsing?
27. Why should different HTTP methods be tested separately?
28. Why can legacy API versions create authorization risks?
29. How is authorization tested differently in GraphQL?
30. What is GraphQL field-level authorization?
31. How do you test GraphQL mutation authorization?
32. How is authorization tested in WebSockets?
33. What is subscription authorization?
34. What is relationship-based authorization?
35. What is parent-child authorization?
36. What is property-level authorization?
37. How can mass assignment lead to privilege escalation?
38. Why should privilege-sensitive properties be inferred from legitimate application evidence?
39. How does authorization apply to create operations?
40. Why must bulk operations authorize every affected object?
41. What authorization concerns exist around file downloads?
42. What are signed URLs, and why are they not automatically vulnerabilities?
43. Why should permission revocation be tested?
44. How can long-lived WebSocket connections retain stale authorization?
45. What risks exist when backend services trust client-supplied identity headers?
46. What is the difference between object ownership and object permission?
47. How can public or shared resources create false-positive IDOR reports?
48. What evidence is required for a strong access-control vulnerability report?
49. How do you distinguish an authorization anomaly from a validated vulnerability?
50. Describe your complete methodology for testing authorization in a modern application using Burp Suite.

---

## Quick Revision

* Authentication:

  ```text
  Who Are You?
  ```

* Authorization:

  ```text
  What May You Access or Do?
  ```

* Decision:

  ```text
  Subject

  +

  Action

  +

  Object

  +

  Context

  →

  Allow / Deny
  ```

* Horizontal:

  ```text
  User A

  →

  User B Resource
  ```

* Vertical:

  ```text
  Standard User

  →

  Administrator Function
  ```

* Object-level:

  ```text
  Can This User Access This Exact Object?
  ```

* Function-level:

  ```text
  Can This User Perform This Exact Action?
  ```

* Tenant:

  ```text
  Tenant A

  ≠

  Tenant B
  ```

* Important rule:

  ```text
  Hard-to-Guess ID

  ≠

  Authorization
  ```

* Another important rule:

  ```text
  Hidden UI

  ≠

  Server-Side Access Control
  ```

* Property-level:

  ```text
  Can Update Object

  ≠

  Can Update Every Property
  ```

* Strong IDOR test:

  ```text
  Known User A

  +

  Known User B Object

  +

  Same Request

  +

  Change Only Object ID

  +

  Unauthorized Data / Action
  ```

* Workflow:

  ```text
  Map Users

  ↓

  Map Roles / Tenants

  ↓

  Map Objects / Ownership

  ↓

  Map Actions

  ↓

  Build Matrix

  ↓

  Capture Baseline

  ↓

  Change One Variable

  ↓

  Compare

  ↓

  Verify State

  ↓

  Validate Impact
  ```

---

## Key Takeaways

* Authorization determines whether a specific subject may perform a specific action against a specific object in a particular context.
* Authentication and authorization must be tested independently.
* Horizontal authorization separates users at similar privilege levels, while vertical authorization separates privilege levels.
* IDOR and BOLA commonly involve missing object-level authorization checks.
* BFLA involves access to functions outside the user's intended privileges.
* Strong authorization testing requires known users, known object ownership, known roles, known tenants, and clearly defined expected permissions.
* Two controlled accounts make horizontal authorization testing substantially more reliable.
* Change one authorization variable at a time to create clear evidence.
* UUIDs, encoded identifiers, and obscure object references do not replace server-side authorization.
* Access control must be enforced by the backend rather than only through hidden buttons, frontend route guards, or client-side role checks.
* Read, create, update, delete, share, export, approval, and administrative actions may each have different authorization requirements.
* Multi-tenant systems require explicit testing of both same-tenant and cross-tenant boundaries.
* Ownership and authorization are not identical because resources may legitimately be public, shared, delegated, or relationship-controlled.
* Property-level authorization matters when users can update objects but should not control privilege-sensitive fields.
* Bulk operations must correctly authorize every affected object.
* REST, GraphQL, WebSockets, legacy APIs, and alternate versions may enforce authorization differently and should be compared.
* GraphQL requires testing at query, mutation, object, field, and resolver boundaries.
* WebSockets require authorization at message, action, object, and subscription levels, not merely when the connection is established.
* Workflow state, role changes, membership removal, and permission revocation can alter authorization and should be tested where relevant.
* State-changing authorization tests require independent verification of the final server state.
* A valid access-control finding requires proof that the tested user lacks legitimate permission and can still access unauthorized data or perform an unauthorized action.
* The professional authorization workflow is **map identities → map roles and tenants → map objects and ownership → map actions → build an authorization matrix → capture legitimate baselines → define a security hypothesis → change one authorization variable → compare behavior → verify data or state → validate impact → document evidence**.
