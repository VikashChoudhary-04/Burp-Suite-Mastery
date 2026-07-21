# Authorization Challenge

> **Module 18 — Capstone, Interview & Quick Reference**

> **Progress**

```text
█████░░░░░░░░░░ 5 / 15 Files
```

---

## Overview

* Authorization determines:

```text
Who

Can Perform

Which Action

On Which Resource

Under Which Conditions
```

* Authentication establishes identity.

* Authorization determines what that identity is allowed to do.

```text
Authentication

↓

Who Are You?

↓

Authorization

↓

What Are You Allowed to Do?
```

* Authorization weaknesses are especially important because they may allow attackers to:

  * Access another user's data.
  * Modify another user's resources.
  * Delete objects they do not own.
  * Invoke administrative functionality.
  * Cross tenant boundaries.
  * Modify protected properties.
  * Download unauthorized files.
  * Perform restricted workflow actions.
  * Escalate privileges.

* This challenge requires systematic testing across:

```text
Actor

×

Object

×

Action

×

Role

×

Tenant
```

---

## Objectives

* By completing this challenge, you should be able to:

  * Build an authorization model.
  * Identify actors and roles.
  * Identify security-sensitive objects.
  * Track object ownership.
  * Identify tenant boundaries.
  * Build a role-permission matrix.
  * Establish authorization baselines.
  * Test horizontal authorization.
  * Test vertical authorization.
  * Test object-level authorization.
  * Test function-level authorization.
  * Test property-level authorization.
  * Test cross-tenant isolation.
  * Test authorization across HTTP methods.
  * Test hidden and direct endpoints.
  * Test APIs independently from the UI.
  * Test file authorization.
  * Test bulk and batch operations.
  * Analyze indirect object references.
  * Use Burp Repeater for controlled authorization testing.
  * Validate access-control findings.
  * Distinguish information disclosure from meaningful unauthorized access.
  * Collect strong authorization evidence.

---

# Challenge Scenario

* Assume the authorized application contains:

```text
User A

User B

Organization Admin

Application Admin
```

* The application contains objects such as:

```text
Profiles

Projects

Documents

Files

Invitations

Orders

API Keys
```

* Your objective is to determine whether the server consistently enforces authorization across all relevant identities, objects, actions, roles, and tenants.

---

# Challenge Deliverables

* Produce:

```text
Authorization-Model.md

Role-Permission-Matrix.md

Object-Ownership-Inventory.md

Authorization-Test-Matrix.md

Cross-Tenant-Test-Matrix.md

Validated-Findings/

Evidence/

Authorization-Summary.md
```

---

# Phase 1 — Build the Authorization Model

* Before testing, identify the authorization dimensions.

---

## Actors

```text
Unauthenticated

User A

User B

Privileged User

Organization Admin

Application Admin
```

---

## Objects

```text
User

Profile

Organization

Project

Document

File

Order

Invoice

Invitation

API Key
```

---

## Actions

```text
Create

Read

Update

Delete

Share

Approve

Export

Download

Invite

Assign

Revoke
```

---

## Context

```text
Role

Ownership

Tenant

Object State

Relationship

Subscription

Workflow State
```

---

# Authorization Formula

```text
Authorization Decision

=

Actor

+

Action

+

Object

+

Context
```

* A secure application should make this decision on the server for every protected operation.

---

# Phase 2 — Identify Roles

* Record every relevant role.

---

## Example

```text
Public

Standard User

Team Member

Project Owner

Organization Admin

Application Admin
```

---

## Role Template

```text
Role:

Authentication Required:

Tenant Scope:

Visible Features:

Allowed Actions:

Restricted Actions:

Administrative Capabilities:
```

---

# Phase 3 — Build the Role-Permission Matrix

| Action              | Public | User  | Org Admin | App Admin |
| ------------------- | ------ | ----- | --------- | --------- |
| View public page    | Allow  | Allow | Allow     | Allow     |
| View own profile    | Deny   | Allow | Allow     | Allow     |
| Edit own profile    | Deny   | Allow | Allow     | Allow     |
| Manage organization | Deny   | Deny  | Allow     | Allow     |
| Manage all users    | Deny   | Deny  | Deny      | Allow     |

---

## Purpose

* The matrix defines:

```text
Expected Behavior
```

* Later tests compare it with:

```text
Actual Server Behavior
```

---

# Phase 4 — Build the Object-Ownership Inventory

* Authorization testing becomes unreliable when ownership is unknown.

---

## Create Controlled Objects

```text
User A

↓

Creates Project A-101
```

```text
User B

↓

Creates Project B-202
```

---

## Inventory

| Object    | ID  | Owner  | Tenant   | Sensitive | Supported Actions |
| --------- | --- | ------ | -------- | --------- | ----------------- |
| Project A | 101 | User A | Tenant A | Yes       | R/W/D             |
| Project B | 202 | User B | Tenant A | Yes       | R/W/D             |
| Project C | 303 | User C | Tenant B | Yes       | R/W/D             |

---

# Core Rule

```text
Known Identity

+

Known Ownership

+

Known Expected Permission

=

Reliable Authorization Test
```

---

# Phase 5 — Establish Positive Controls

* First prove legitimate access works.

---

## Example

```text
User A

↓

GET /api/projects/101

↓

200 OK
```

* This establishes:

```text
User A Owns Project 101

and

User A Can Read Project 101
```

---

# Phase 6 — Establish Negative Expectations

* Define what should be denied before testing.

---

## Example

```text
User A

↓

GET /api/projects/202

↓

Expected:

403 / 404 / Equivalent Denial
```

* Project 202 belongs to User B.

---

# Phase 7 — Test Horizontal Authorization

* Horizontal authorization separates users with similar privilege levels.

---

## Example

```text
User A

→

User A Object

Allowed
```

```text
User A

→

User B Object

Expected Denied
```

---

## Test Operations

```text
Read

Update

Delete

Download

Export

Share

Comment

Approve

Archive
```

---

# Horizontal Test Matrix

| Actor  | Target      | Action | Expected |
| ------ | ----------- | ------ | -------- |
| User A | A's Project | Read   | Allow    |
| User A | B's Project | Read   | Deny     |
| User A | B's Project | Update | Deny     |
| User A | B's Project | Delete | Deny     |

---

# Phase 8 — Test Vertical Authorization

* Vertical authorization separates privilege levels.

---

## Example

```text
Standard User

↓

Administrative Function

↓

Expected Denied
```

---

## Test

```text
User Management

Role Management

Configuration

Billing Administration

Audit Logs

Organization Settings

Administrative APIs
```

---

# Important Principle

```text
Hidden Button

≠

Authorization Control
```

* Removing an administrative button from the UI does not secure the underlying endpoint.

---

# Phase 9 — Test Direct Endpoint Access

* Capture an authorized privileged request.

---

## Example

```text
POST /admin/users/104/disable
```

* Then test the same function using a lower-privileged controlled account.

```text
Standard User Session

↓

POST /admin/users/104/disable

↓

Expected Denial
```

---

# Phase 10 — Test Object-Level Authorization

* Object-level authorization determines whether an actor may interact with a specific resource.

---

## Example

```text
GET /api/invoices/501
```

* Ask:

```text
Who Owns Invoice 501?

Who Should Be Able to Read It?

Can Another Controlled User Reference It?

Can Another Tenant Reference It?
```

---

# Object-Level Test Pattern

```text
Baseline Request

↓

Identify Object Reference

↓

Change Object Reference Only

↓

Send as Same Actor

↓

Observe

↓

Verify Ownership

↓

Verify Result
```

---

# Phase 11 — Identify Direct Object References

* References may appear in:

```text
URL Paths

Query Parameters

JSON

Form Data

Headers

Cookies

GraphQL Variables

WebSocket Messages
```

---

## Examples

```text
/api/users/104

?account_id=77

{"project_id":731}

X-Organization-ID: 42
```

---

# Important Principle

```text
UUID

≠

Authorization
```

* An unpredictable identifier may reduce guessing.

* It does not replace server-side access control.

---

# Phase 12 — Test Read Authorization

* Read operations may expose sensitive information.

---

## Test

```text
Profile

Documents

Invoices

Orders

Messages

Files

API Keys

Audit Data
```

---

## Verify

* [ ] Response belongs to the target object.
* [ ] Target object belongs to another controlled identity.
* [ ] Data is not intentionally shared.
* [ ] Access is outside expected permissions.

---

# Phase 13 — Test Update Authorization

* Update authorization can be more severe than unauthorized reading.

---

## Example

```text
PATCH /api/projects/202
```

```text
{
  "name": "Modified"
}
```

* Test using User A against User B's controlled object.

---

## Verify Final State

```text
Unauthorized Request

↓

Response

↓

Reload as Legitimate Owner

↓

Was Object Actually Modified?
```

---

# Core Rule

```text
HTTP 200

≠

Successful Unauthorized Modification
```

* Verify persistent state.

---

# Phase 14 — Test Delete Authorization

* Destructive testing requires caution.

* Use only controlled disposable objects.

---

## Workflow

```text
User B

↓

Creates Disposable Object
```

```text
User A

↓

Attempts Delete
```

```text
User B

↓

Verifies Final State
```

---

## Stop

* Do not delete:

  * Real-user data.
  * Production-critical objects.
  * Shared resources.
  * Objects outside explicit authorization.

---

# Phase 15 — Test Function-Level Authorization

* Function-level authorization protects privileged actions.

---

## Examples

```text
/admin/users

/admin/settings

/api/admin/reports

/api/users/{id}/role

/api/organizations/{id}/billing
```

---

## Test

```text
Privileged Request

↓

Capture

↓

Replace Session With Lower-Privilege Session

↓

Replay

↓

Expected Denial
```

---

# Phase 16 — Test Property-Level Authorization

* A user may be authorized to modify an object but not every property.

---

## Example

```text
PATCH /api/profile
```

```text
{
  "display_name": "Alice",
  "role": "admin",
  "verified": true
}
```

---

## Ask

```text
Can the User Modify display_name?

Can the User Modify role?

Can the User Modify verified?

Does the Server Ignore or Reject Protected Fields?
```

---

# Property Matrix

| Property       | User Editable | Admin Editable | Security Sensitive |
| -------------- | ------------- | -------------- | ------------------ |
| `display_name` | Yes           | Yes            | No                 |
| `email`        | Conditional   | Yes            | Yes                |
| `role`         | No            | Yes            | Yes                |
| `verified`     | No            | Yes            | Yes                |

---

# Phase 17 — Test Mass Assignment

* APIs may automatically bind client-supplied properties to backend objects.

---

## Workflow

```text
Capture Legitimate Update

↓

Identify Additional Properties

↓

Add One Sensitive Property

↓

Send

↓

Verify Final State
```

---

## High-Value Properties

```text
role

is_admin

permissions

owner_id

tenant_id

verified

balance

status

plan

approved
```

---

# Phase 18 — Test Cross-Tenant Authorization

* Multi-tenant systems require strict isolation.

---

## Example

```text
Tenant A
└── User A
    └── Project 101

Tenant B
└── User B
    └── Project 202
```

* Test:

```text
User A

↓

Reference Project 202

↓

Expected Denial
```

---

# Cross-Tenant Dimensions

```text
Tenant ID

Organization ID

Workspace ID

Team ID

Object ID

Account ID
```

---

# Cross-Tenant Test Matrix

| Actor   | Actor Tenant | Target      | Target Tenant | Expected                      |
| ------- | ------------ | ----------- | ------------- | ----------------------------- |
| User A  | A            | Project 101 | A             | Allow                         |
| User A  | A            | Project 202 | B             | Deny                          |
| Admin A | A            | Project 202 | B             | Deny unless explicitly global |

---

# Phase 19 — Test Tenant Context Parameters

* Applications may send tenant context explicitly.

---

## Examples

```text
X-Tenant-ID: 100
```

```text
?organization_id=100
```

```text
{
  "workspace_id": 100
}
```

---

## Ask

```text
Does the Server Derive Tenant From the Session?

or

Trust Client-Supplied Tenant Context?
```

---

# Phase 20 — Test Authorization Across HTTP Methods

* Authorization may be correct for one method and missing for another.

---

## Test

```text
GET

POST

PUT

PATCH

DELETE
```

* According to supported functionality.

---

## Example

```text
GET /api/projects/202

→ Denied
```

* But:

```text
PATCH /api/projects/202

→ Must Also Be Denied
```

---

# Phase 21 — Test Alternate Endpoints

* The same action may exist through multiple routes.

---

## Example

```text
/api/v1/projects/202

/api/v2/projects/202

/projects/202

/mobile/projects/202
```

---

## Ask

```text
Do All Paths Enforce the Same Authorization?
```

---

# Phase 22 — Test APIs Independently

* Do not assume UI restrictions apply to APIs.

---

## Test

```text
REST

GraphQL

WebSockets

Legacy APIs

Mobile APIs
```

---

## Compare

```text
UI Permission

vs

Direct API Permission
```

---

# Phase 23 — Test GraphQL Authorization

* If GraphQL is used, authorization may need to be enforced at:

```text
Query

Mutation

Object

Field

Resolver
```

---

## Example

```text
User

↓

Allowed to Query Own Project

↓

Requests Sensitive Nested Field

↓

Field-Level Authorization Required
```

---

# Phase 24 — Test File Authorization

* Files often use separate delivery mechanisms.

---

## Test

```text
Upload

View

Download

Preview

Delete

Share

Export
```

---

## Example

```text
/files/8821
```

* Determine:

```text
Who Owns File 8821?

Is Authentication Required?

Is Authorization Checked?

Does a Direct Storage URL Bypass Controls?
```

---

# Phase 25 — Test Export Authorization

* Export endpoints may expose more data than normal views.

---

## Examples

```text
/export/project/101

/api/reports/export

/download/invoice/501
```

---

## Test

* [ ] Object ownership.
* [ ] Tenant boundary.
* [ ] Role requirements.
* [ ] Exported data scope.
* [ ] Hidden fields.

---

# Phase 26 — Test Bulk and Batch Operations

* Bulk endpoints may implement different authorization logic.

---

## Examples

```text
POST /api/projects/bulk-delete
```

```text
{
  "ids": [101, 202, 303]
}
```

---

## Test

```text
Authorized Object

+

Unauthorized Controlled Object

↓

Same Batch
```

* Determine whether authorization is enforced per object.

---

# Phase 27 — Test Nested Resources

* Authorization may fail when objects are accessed through parent-child routes.

---

## Example

```text
/organizations/10/projects/101
```

* Ask:

```text
Does Project 101 Actually Belong to Organization 10?

Can the Parent ID Be Changed?

Can the Child ID Be Changed?

Are Both Relationships Validated?
```

---

# Phase 28 — Test Relationship-Based Authorization

* Some permissions depend on relationships.

---

## Examples

```text
Owner

Member

Collaborator

Viewer

Approver

Manager
```

---

## Test Transitions

```text
Owner

→ Share With Member
```

```text
Member

→ Attempt Owner Action
```

```text
Removed Member

→ Replay Previous Request
```

---

# Phase 29 — Test Authorization After Relationship Changes

* Permissions may become stale.

---

## Example

```text
User A Added to Project

↓

Session Created

↓

User A Removed

↓

Old Request Replayed
```

---

## Expected

```text
Current Authorization State

↓

Enforced Immediately or According to Defined Policy
```

---

# Phase 30 — Test Workflow Authorization

* Authorization may depend on object state.

---

## Example

```text
Draft

↓

Submitted

↓

Approved
```

* Ask:

```text
Can a User Approve Their Own Submission?

Can a User Skip Required Roles?

Can a Completed Object Be Modified?

Can a Rejected Object Be Forced to Approved?
```

---

# Phase 31 — Use Burp Repeater Systematically

* Organize Repeater tabs by authorization case.

---

## Example

```text
AUTHZ-HORIZONTAL/
├── Own-Read
├── Other-Read
├── Other-Update
└── Other-Delete

AUTHZ-VERTICAL/
├── User-Admin-Endpoint
└── User-Role-Change

AUTHZ-TENANT/
├── Same-Tenant
└── Cross-Tenant
```

---

# Controlled Mutation Rule

```text
Baseline Request

↓

Change One Authorization Variable

↓

Send

↓

Compare

↓

Verify Final State
```

---

# Authorization Variables

```text
Session

Object ID

Tenant ID

Role

HTTP Method

Endpoint

Property

Workflow State
```

---

# Phase 32 — Interpret Responses Carefully

* Authorization failures may appear as:

```text
401 Unauthorized

403 Forbidden

404 Not Found

Redirect

Generic Error

Empty Response
```

* Secure systems may intentionally hide object existence.

---

## Do Not Judge Only By

```text
Status Code
```

* Also verify:

```text
Response Data

State Change

Side Effects

Object Ownership

Final State
```

---

# Phase 33 — Distinguish Existence Disclosure

* Different responses may reveal whether an object exists.

---

## Example

```text
Existing Unauthorized Object

→ 403
```

```text
Nonexistent Object

→ 404
```

* This may disclose object existence.

* However:

```text
Object Existence Disclosure

≠

Full Unauthorized Object Access
```

* Report impact accurately.

---

# Phase 34 — Validate Candidate Findings

* Authorization findings require strong evidence.

---

## Validation Workflow

```text
Identify Actor

↓

Identify Target Object

↓

Prove Ownership

↓

Establish Expected Permission

↓

Capture Positive Control

↓

Perform Unauthorized Test

↓

Verify Result

↓

Confirm Security Boundary Failure

↓

Determine Impact

↓

Identify Root Cause
```

---

# Example — Strong IDOR Evidence

```text
User A Owns Project 101

User B Owns Project 202

↓

User A Requests Project 101

↓

Allowed

↓

User A Changes Only ID to 202

↓

Server Returns Project 202 Data

↓

User B Confirms Ownership

↓

Unauthorized Cross-User Read Confirmed
```

---

# Example — Strong Unauthorized Update Evidence

```text
User B Creates Controlled Object

↓

User A Sends Update Request

↓

Server Reports Success

↓

User B Reloads Object

↓

Unauthorized Change Is Visible
```

---

# Phase 35 — Determine Root Cause

* Common authorization root causes include:

```text
Missing Object Ownership Check

Missing Role Check

Missing Tenant Check

Client-Supplied Authorization Context Trusted

Inconsistent Authorization Across Endpoints

Missing Property-Level Authorization

Stale Permission State

Authorization Applied Only in UI
```

---

# Phase 36 — Collect Evidence

* Preserve:

```text
Actor Identity

Actor Role

Actor Tenant

Target Object

Target Owner

Target Tenant

Baseline Request

Unauthorized Request

Response

Final State

Impact
```

---

## Strong Evidence Structure

```text
F001/
│
├── 01-user-a-identity.png
├── 02-user-b-object-ownership.png
├── 03-baseline-request.txt
├── 04-unauthorized-request.txt
├── 05-unauthorized-response.txt
├── 06-final-state.png
└── notes.md
```

---

# Capstone Challenge

## Stage 1 — Build the Model

* Create:

```text
Authorization-Model.md
```

* Record:

  * Actors.
  * Roles.
  * Objects.
  * Actions.
  * Tenants.
  * Relationships.

---

## Stage 2 — Role Matrix

* Create:

```text
Role-Permission-Matrix.md
```

* Map expected permissions.

---

## Stage 3 — Object Ownership

* Create at least:

```text
User A Object

User B Object

Cross-Tenant Object
```

* Where the application supports them.

---

## Stage 4 — Horizontal Testing

* Test:

```text
Read

Update

Delete

Download

Export
```

* Against controlled objects.

---

## Stage 5 — Vertical Testing

* Test lower-privileged accounts against privileged functionality.

---

## Stage 6 — Property Authorization

* Identify at least five properties.

* Determine which should be user-modifiable.

* Test sensitive properties individually.

---

## Stage 7 — Cross-Tenant Testing

* Test:

```text
Object IDs

Tenant IDs

Organization IDs

Workspace IDs

API Routes
```

---

## Stage 8 — Alternate Interfaces

* Compare authorization across:

```text
UI

REST

GraphQL

WebSockets

Legacy APIs
```

* Where available.

---

## Stage 9 — Validation

* Validate every suspicious result using:

```text
Controlled Identity

+

Controlled Object

+

Known Ownership

+

Expected Permission

+

Final-State Verification
```

---

# Challenge Success Criteria

* You should be able to answer:

```text
Which Roles Exist?

Which Objects Exist?

Who Owns Each Object?

Which Tenant Owns Each Object?

Which Actions Should Each Role Perform?

Where Are Horizontal Boundaries?

Where Are Vertical Boundaries?

Where Are Tenant Boundaries?

Which Properties Are Protected?

Are Checks Consistent Across Methods?

Are Checks Consistent Across APIs?

Are Bulk Operations Protected?

Are Files Protected?

Are Workflow Permissions Enforced?

Do Permission Changes Take Effect Correctly?
```

---

# Authorization Testing Workflow

```text
Map Actors

↓

Map Roles

↓

Map Objects

↓

Map Ownership

↓

Map Tenants

↓

Define Expected Permissions

↓

Capture Positive Baselines

↓

Test Horizontal Boundaries

↓

Test Vertical Boundaries

↓

Test Object-Level Controls

↓

Test Property-Level Controls

↓

Test Cross-Tenant Isolation

↓

Test Alternate Methods and Endpoints

↓

Test Files and Bulk Operations

↓

Verify Final State

↓

Validate Findings

↓

Collect Evidence

↓

Report
```

---

# Common Mistakes

* Testing authorization without multiple controlled accounts.
* Failing to prove object ownership.
* Guessing random IDs belonging to real users.
* Confusing authentication with authorization.
* Testing only read access.
* Ignoring update authorization.
* Ignoring delete authorization.
* Ignoring export and download endpoints.
* Testing only visible UI functions.
* Assuming hidden buttons provide security.
* Testing only one HTTP method.
* Ignoring API versions.
* Ignoring mobile or legacy endpoints.
* Ignoring GraphQL field-level authorization.
* Ignoring property-level authorization.
* Ignoring mass assignment.
* Ignoring tenant identifiers.
* Assuming UUIDs provide authorization.
* Ignoring bulk endpoints.
* Ignoring nested resources.
* Ignoring file authorization.
* Ignoring relationship changes.
* Ignoring stale privileges.
* Treating every 200 response as successful exploitation.
* Failing to verify persistent state.
* Confusing object-existence disclosure with full data exposure.
* Performing destructive tests against uncontrolled data.
* Reporting authorization issues without proving expected permissions.

---

# Professional Tips

* Build the authorization model before testing.
* Use at least two controlled users whenever possible.
* Add controlled tenants when the application supports multi-tenancy.
* Create your own test objects.
* Record object IDs and ownership.
* Define expected permissions before sending unauthorized requests.
* Start with positive controls.
* Change one authorization variable at a time.
* Test read, update, delete, download, export, and sharing independently.
* Test both horizontal and vertical boundaries.
* Treat tenant isolation as a separate authorization dimension.
* Test property-level authorization separately from object-level authorization.
* Check whether the server trusts client-supplied role or tenant context.
* Test direct endpoints even when the UI hides functionality.
* Compare authorization across API versions.
* Test bulk operations per object.
* Check parent-child relationships in nested resources.
* Verify final state after mutations.
* Use disposable controlled objects for destructive tests.
* Recheck authorization after role and relationship changes.
* Distinguish information disclosure from unauthorized action.
* Capture evidence proving both actor identity and target ownership.
* Explain the exact failed authorization check in reports.

---

# Practice Tasks

1. Select an authorized application with multiple users.

2. Create User A.

3. Create User B.

4. Create separate tenants if supported.

5. Identify all roles.

6. Build a role-permission matrix.

7. Identify at least five object types.

8. Create controlled objects for User A.

9. Create controlled objects for User B.

10. Record object ownership.

11. Record tenant ownership.

12. Capture User A reading User A's object.

13. Change only the object ID to User B's object.

14. Verify the result.

15. Test unauthorized update.

16. Verify persistent state as User B.

17. Test unauthorized delete using disposable data.

18. Test download authorization.

19. Test export authorization.

20. Test sharing authorization.

21. Identify privileged functions.

22. Capture a privileged request.

23. Replay it with a lower-privileged session.

24. Identify sensitive object properties.

25. Test one protected property at a time.

26. Test possible mass-assignment fields.

27. Identify tenant-context parameters.

28. Test cross-tenant object access.

29. Test cross-tenant updates.

30. Test authorization across GET, POST, PATCH, and DELETE where applicable.

31. Identify alternate API versions.

32. Compare authorization across versions.

33. Review GraphQL authorization if available.

34. Review WebSocket authorization if available.

35. Test file access controls.

36. Test bulk operations.

37. Test nested resources.

38. Test relationship-based permissions.

39. Remove a user's permission.

40. Replay an old authorized request.

41. Build an authorization test matrix.

42. Validate every suspicious result.

43. Collect evidence proving identity and ownership.

44. Write an authorization summary.

---

# Interview Questions

1. What is authorization?
2. What is the difference between authentication and authorization?
3. What dimensions should be considered during authorization testing?
4. What is horizontal privilege escalation?
5. What is vertical privilege escalation?
6. What is object-level authorization?
7. What is function-level authorization?
8. What is property-level authorization?
9. What is IDOR?
10. Why is a UUID not an authorization control?
11. How do you test IDOR safely?
12. Why should object ownership be proven?
13. What is a positive authorization control?
14. How do you test unauthorized updates?
15. Why must persistent state be verified?
16. How do you test delete authorization safely?
17. Why is hiding an admin button insufficient?
18. How do you test administrative endpoints?
19. What is mass assignment?
20. Which properties are commonly security sensitive?
21. What is tenant isolation?
22. How do you test cross-tenant authorization?
23. Why are client-supplied tenant IDs interesting?
24. How do you test authorization across HTTP methods?
25. Why should alternate API versions be tested?
26. How do you test GraphQL authorization?
27. How can file delivery introduce authorization weaknesses?
28. Why should export endpoints be tested separately?
29. How do you test bulk operations?
30. What authorization issues can occur in nested resources?
31. What is relationship-based authorization?
32. What happens when permissions change during an active session?
33. How do you interpret 401, 403, and 404 during authorization testing?
34. Why is status code alone insufficient?
35. What is object-existence disclosure?
36. How is existence disclosure different from unauthorized access?
37. How do you validate an IDOR finding?
38. What evidence is needed for an authorization report?
39. What are common authorization root causes?
40. Walk me through your complete authorization testing methodology.

---

# Quick Revision

* Authorization:

```text
Who

Can Do What

To Which Object

Under Which Conditions?
```

* Core model:

```text
Actor

×

Object

×

Action

×

Role

×

Tenant
```

* Horizontal:

```text
User A

→

User B Object
```

* Vertical:

```text
Standard User

→

Admin Function
```

* Cross-tenant:

```text
Tenant A

→

Tenant B Object
```

* Object-level:

```text
Can This Actor Access This Specific Object?
```

* Function-level:

```text
Can This Actor Perform This Function?
```

* Property-level:

```text
Can This Actor Modify This Specific Field?
```

* Reliable test:

```text
Known Actor

+

Known Object

+

Known Ownership

+

Known Expected Permission

+

Controlled Mutation
```

* Validation:

```text
Baseline

↓

Unauthorized Test

↓

Verify Result

↓

Verify Final State

↓

Confirm Boundary Failure
```

---

# Authorization Quick Checklist

```text
ACTORS

[ ] Unauthenticated
[ ] User A
[ ] User B
[ ] Privileged user
[ ] Organization admin
[ ] Application admin


OBJECTS

[ ] IDs
[ ] Owners
[ ] Tenants
[ ] Relationships
[ ] Sensitive data


ACTIONS

[ ] Create
[ ] Read
[ ] Update
[ ] Delete
[ ] Download
[ ] Export
[ ] Share
[ ] Approve
[ ] Assign


BOUNDARIES

[ ] Horizontal
[ ] Vertical
[ ] Object-level
[ ] Function-level
[ ] Property-level
[ ] Cross-tenant


INTERFACES

[ ] UI
[ ] REST
[ ] GraphQL
[ ] WebSockets
[ ] Legacy APIs
[ ] Mobile APIs


EDGE CASES

[ ] Alternate methods
[ ] Alternate endpoints
[ ] Bulk operations
[ ] Nested resources
[ ] Files
[ ] Exports
[ ] Relationship changes
[ ] Stale permissions


VALIDATION

[ ] Actor proven
[ ] Role proven
[ ] Target ownership proven
[ ] Tenant proven
[ ] Expected permission defined
[ ] Positive control
[ ] Unauthorized test
[ ] Final state verified
[ ] Impact demonstrated
[ ] Root cause identified
```

---

# Key Takeaways

* Authorization determines which actions an authenticated or unauthenticated actor may perform on specific resources under specific conditions.
* Authorization testing should model actors, objects, actions, roles, tenants, relationships, and workflow state.
* Reliable authorization testing requires controlled identities, controlled objects, known ownership, and clearly defined expected permissions.
* Role-permission matrices establish the expected authorization model before active boundary testing.
* Horizontal authorization protects resources between users at similar privilege levels.
* Vertical authorization protects privileged functions from lower-privileged identities.
* Object-level authorization controls access to specific resource instances.
* Function-level authorization controls access to privileged operations.
* Property-level authorization controls which fields an actor may view or modify.
* Unpredictable identifiers such as UUIDs do not replace server-side authorization.
* Read, update, delete, download, export, sharing, and other actions should be tested independently because authorization may differ by operation.
* Successful-looking HTTP responses must be followed by final-state verification for state-changing tests.
* Destructive authorization testing should use disposable controlled objects.
* UI restrictions are not security controls unless the server independently enforces the same restriction.
* Mass assignment can expose protected properties when backend frameworks bind client-controlled fields without appropriate authorization.
* Multi-tenant systems require explicit testing of organization, workspace, tenant, account, and object boundaries.
* Client-supplied tenant context should not be blindly trusted as authorization evidence.
* Authorization should be tested across HTTP methods, API versions, alternate endpoints, GraphQL, WebSockets, mobile APIs, and legacy interfaces where relevant.
* File, export, bulk, batch, and nested-resource endpoints frequently introduce distinct authorization paths.
* Authorization must remain correct after membership, ownership, role, or relationship changes.
* Status codes alone do not prove whether authorization succeeded or failed; response data, side effects, ownership, and persistent state must also be evaluated.
* Object-existence disclosure should not be overstated as complete unauthorized access.
* Strong authorization evidence proves actor identity, role, tenant, target ownership, expected permission, unauthorized behavior, and resulting impact.
* Common root causes include missing ownership checks, missing role checks, missing tenant checks, trusted client authorization context, inconsistent endpoint enforcement, missing property authorization, stale permission state, and UI-only restrictions.
* The complete methodology is **map actors → map roles → map objects and ownership → map tenants → define expected permissions → establish positive controls → test horizontal boundaries → test vertical boundaries → test object, function, and property controls → test cross-tenant isolation → test alternate methods and interfaces → verify final state → validate boundary failure → collect evidence → report**.
