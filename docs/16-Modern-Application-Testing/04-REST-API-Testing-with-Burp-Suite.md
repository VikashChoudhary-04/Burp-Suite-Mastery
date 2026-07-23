# REST API Testing with Burp Suite

> **Module 16 — Modern Application Testing**

> **Progress**

```text
█████░░░░░░░░ 5 / 13 Files
```

---

## Overview

* REST APIs are a major part of modern web and mobile applications.

* They commonly expose application functionality through:

  * Resources.
  * HTTP methods.
  * Structured URLs.
  * JSON request bodies.
  * Authentication tokens.
  * Object identifiers.
  * Query parameters.

* Example:

  ```http
  GET /api/v2/orders/481 HTTP/1.1
  Host: api.example.test
  Authorization: Bearer <token>
  ```

* REST API security testing is not simply about sending payloads.

* Effective testing requires understanding:

  ```text
  Resource

  +

  Method

  +

  Identity

  +

  Authorization

  +

  Input

  +

  State

  +

  Business Rule
  ```

* Burp Suite allows you to:

  ```text
  Capture

  ↓

  Understand

  ↓

  Replay

  ↓

  Modify

  ↓

  Compare

  ↓

  Validate
  ```

* The objective is to determine whether the API correctly enforces its expected security rules when requests are changed outside the intended client interface.

---

## Learning Objectives

* By the end of this lesson, you will be able to:

  * Understand REST-style API architecture.
  * Recognize resources and resource collections.
  * Understand how HTTP methods map to API actions.
  * Capture REST API traffic with Burp Suite.
  * Establish clean baseline requests.
  * Test API requests systematically with Repeater.
  * Identify path, query, header, and body inputs.
  * Test object identifiers safely.
  * Analyze authentication and authorization behavior.
  * Test JSON properties and server-side validation.
  * Investigate method-based access controls.
  * Analyze content-type handling.
  * Test state-changing operations carefully.
  * Compare responses using multiple signals.
  * Distinguish anomalies from validated vulnerabilities.
  * Build a repeatable REST API testing workflow.

---

## What Is REST?

* REST stands for:

  ```text
  Representational State Transfer
  ```

* REST is an architectural style commonly used when designing APIs.

* REST-style APIs often organize functionality around **resources**.

* Examples:

  ```text
  Users

  Orders

  Projects

  Files

  Tickets

  Messages
  ```

---

## Resource Collections

* A collection endpoint may represent multiple objects.

* Example:

  ```http
  GET /api/users
  ```

* This may return a collection of users.

---

## Individual Resources

* A specific object may be represented by an identifier.

* Example:

  ```http
  GET /api/users/42
  ```

* Here:

  ```text
  Resource:

  User

  Identifier:

  42
  ```

---

## Nested Resources

* Resources may be related.

* Example:

  ```text
  /organizations/91/projects

  /organizations/91/projects/781

  /organizations/91/projects/781/files
  ```

* This represents relationships between:

  ```text
  Organization

  ↓

  Project

  ↓

  File
  ```

* Each relationship may introduce an authorization boundary.

---

## REST and HTTP Methods

* Common methods include:

| Method  | Typical Purpose                                 |
| ------- | ----------------------------------------------- |
| GET     | Retrieve data                                   |
| POST    | Create or trigger an action                     |
| PUT     | Replace or update a resource                    |
| PATCH   | Partially update a resource                     |
| DELETE  | Delete a resource                               |
| OPTIONS | Discover communication options                  |
| HEAD    | Retrieve headers without a normal response body |

* These are conventions.

* Actual application behavior must always be observed.

---

## Method Does Not Guarantee Meaning

* Example:

  ```text
  POST /orders/481/refund
  ```

* This is not a simple resource creation operation.

* It represents:

  ```text
  Business Action:

  Refund Order
  ```

* Always map the technical request to its actual business function.

---

## REST Security Mental Model

* For every important request, ask:

  ```text
  Who Is Making the Request?

  ↓

  What Resource Is Being Accessed?

  ↓

  Which Action Is Requested?

  ↓

  Which Input Is User-Controlled?

  ↓

  Which Security Rule Should Apply?

  ↓

  Does the Server Enforce It?
  ```

---

## Burp Workflow for REST APIs

```text
Proxy

↓

Capture Legitimate Request

↓

HTTP History

↓

Understand Request

↓

Send to Repeater

↓

Establish Baseline

↓

Modify One Variable

↓

Send Request

↓

Compare Response

↓

Validate Server State

↓

Record Evidence
```

---

## Start With a Baseline

* Before modifying anything, preserve the original request.

* Example:

  ```http
  GET /api/v2/orders/481 HTTP/1.1
  Host: api.example.test
  Authorization: Bearer <token>
  Accept: application/json
  ```

* Record:

  ```text
  Status Code

  Response Length

  Response Body

  Important Headers

  Response Time

  User Identity

  Role

  Tenant

  Object Owner
  ```

---

## Why Baselines Matter

* Without a baseline, you cannot reliably determine what changed.

* Professional testing follows:

  ```text
  Original Request

  ↓

  Controlled Modification

  ↓

  Modified Response

  ↓

  Comparison
  ```

---

## Send Requests to Repeater

* Right-click an interesting request.

* Select:

  ```text
  Send to Repeater
  ```

* Repeater is ideal for:

  ```text
  Controlled Modifications

  Repeated Testing

  Response Comparison

  Hypothesis Validation
  ```

---

## Organize Repeater Requests

* Group requests by function.

* Example:

  ```text
  Authentication

  Users

  Orders

  Files

  Admin

  Business Logic
  ```

* Use descriptive tab names.

* Example:

  ```text
  GET Order — Baseline

  PATCH Profile — Own Account

  POST Invite — Member

  DELETE File — Owner
  ```

---

## Identify All Input Locations

* REST API input may appear in:

  ```text
  URL Path

  Query String

  Headers

  Cookies

  JSON Body

  Form Body

  Multipart Body
  ```

* Do not focus only on JSON.

---

## Path Parameters

* Example:

  ```http
  GET /api/orders/481
  ```

* User-controlled candidate:

  ```text
  481
  ```

* Security questions:

  ```text
  What Object Does It Identify?

  Who Owns It?

  Is Access Authorized Server-Side?

  Does Tenant Membership Matter?
  ```

---

## Query Parameters

* Example:

  ```http
  GET /api/orders?user_id=42&status=pending
  ```

* Parameters:

  ```text
  user_id

  status
  ```

* Potential security relevance:

  ```text
  Object Filtering

  Data Scope

  Tenant Filtering

  Search Behavior

  Pagination
  ```

---

## Header Inputs

* APIs may depend on headers such as:

  ```text
  Authorization

  X-Organization-ID

  X-Tenant-ID

  Content-Type

  Accept

  Origin
  ```

* Some custom headers may influence identity or routing.

* Never assume a security-sensitive header is trusted safely merely because the official client sends it.

---

## Cookies

* APIs may use:

  ```text
  Session Cookies

  CSRF Tokens

  Preference Cookies

  Routing Cookies
  ```

* Identify which cookies affect:

  ```text
  Authentication

  Authorization

  State

  Routing
  ```

---

## JSON Bodies

* Example:

  ```json
  {
    "name": "Alice",
    "department": "Sales",
    "organization_id": 91
  }
  ```

* Identify:

  ```text
  User-Controlled Fields

  Object IDs

  Tenant IDs

  Privileged Properties

  Business Values
  ```

---

## Test One Variable at a Time

* Avoid changing:

  ```text
  Object ID

  +

  Role

  +

  Token

  +

  Method
  ```

* Simultaneously.

* Prefer:

  ```text
  Baseline

  ↓

  Change One Variable

  ↓

  Observe

  ↓

  Restore

  ↓

  Change Next Variable
  ```

* This makes results attributable.

---

## Object-Level Authorization Testing

* Example baseline:

  ```http
  GET /api/orders/481
  Authorization: Bearer <User-A-Token>
  ```

* Suppose Order `481` belongs to User A.

* With two authorized controlled accounts:

  ```text
  User A

  User B
  ```

* A controlled test may compare access to objects owned by each account.

---

## Expected Security Rule

```text
User A

→

May Access Authorized User A Objects
```

* But:

  ```text
  User A

  →

  Should Not Automatically Access User B Objects
  ```

---

## Authorization Testing Matrix

| Identity | Object        | Expected | Actual |
| -------- | ------------- | -------- | ------ |
| User A   | User A object | Allow    | ?      |
| User A   | User B object | Deny     | ?      |
| User B   | User B object | Allow    | ?      |
| User B   | User A object | Deny     | ?      |

* Controlled matrices reduce ambiguity.

---

## Do Not Rely Only on Status Codes

* Suppose modified request returns:

  ```http
  HTTP/1.1 200 OK
  ```

* This may indicate success.

* But inspect:

  ```text
  Response Body

  Object Identity

  Returned Data

  Server State

  Side Effects
  ```

---

## False Success Example

* Response:

  ```http
  HTTP/1.1 200 OK
  ```

  ```json
  {
    "error": "Access denied"
  }
  ```

* Status code alone would be misleading.

---

## False Denial Example

* Response:

  ```http
  HTTP/1.1 403 Forbidden
  ```

* But a state-changing action may still have occurred due to flawed backend processing.

* Always verify state when safe.

---

## Function-Level Authorization

* Some endpoints expose privileged actions.

* Example:

  ```http
  POST /api/admin/users/42/disable
  ```

* Expected security rule:

  ```text
  Only Authorized Privileged Roles

  May Perform This Action
  ```

* Testing should compare controlled identities with different roles.

---

## Role Matrix

| Identity | Role    | Function     | Expected |
| -------- | ------- | ------------ | -------- |
| User A   | User    | Disable user | Deny     |
| Manager  | Manager | Disable user | Depends  |
| Admin    | Admin   | Disable user | Allow    |

* Document expected behavior before interpreting results.

---

## Authentication Testing

* Identify what proves identity.

* Examples:

  ```text
  Session Cookie

  Bearer Token

  API Key

  Multiple Headers
  ```

* Controlled tests may include:

  ```text
  Remove Credential

  Replace With Another Controlled Credential

  Use Expired Credential

  Test Logout Invalidity
  ```

* Exact testing depends on authorization and application design.

---

## Missing Authentication

* Baseline:

  ```http
  GET /api/me
  Authorization: Bearer <token>
  ```

* Controlled modification:

  ```text
  Remove Authorization Header
  ```

* Observe whether the server:

  ```text
  Rejects

  Treats as Anonymous

  Returns Sensitive Data

  Behaves Unexpectedly
  ```

---

## Authentication vs Authorization

* Keep them separate.

* Authentication asks:

  ```text
  Who Are You?
  ```

* Authorization asks:

  ```text
  Are You Allowed to Perform This Action?
  ```

* An endpoint can authenticate correctly and still authorize incorrectly.

---

## Property-Level Authorization

* APIs often accept JSON objects containing multiple fields.

* Example:

  ```json
  {
    "name": "Alice",
    "email": "alice@example.test"
  }
  ```

* But the underlying object may also contain:

  ```text
  role

  status

  organization_id

  permissions
  ```

---

## Security Question

```text
Can the User Modify Only Intended Properties?
```

* This is different from object-level authorization.

---

## Object vs Property Authorization

* Object-level:

  ```text
  Can User A Modify User B?
  ```

* Property-level:

  ```text
  Can User A Modify Their Own Restricted "role" Property?
  ```

* Both require separate testing.

---

## Property Testing Workflow

```text
Capture Valid Update

↓

Identify Allowed Fields

↓

Identify Other Known Properties

↓

Add or Modify One Property

↓

Send Request

↓

Inspect Response

↓

Retrieve Object Again

↓

Verify Persisted State
```

---

## Verify Persistence

* Suppose:

  ```http
  PATCH /api/profile
  ```

* Returns:

  ```http
  200 OK
  ```

* Do not conclude that a property changed.

* Retrieve the object again.

* Verify actual server state.

---

## Mass Assignment Concepts

* Some APIs automatically bind client-supplied JSON fields to backend object properties.

* Security risk can arise if sensitive properties are accepted without proper authorization.

* Potentially sensitive fields include:

  ```text
  role

  permissions

  account_status

  organization_id

  owner_id

  credit

  approval_state
  ```

* Presence alone does not prove exploitability.

---

## Server-Side Validation

* APIs should validate input independently of frontend restrictions.

* Example frontend rule:

  ```text
  Quantity Must Be Between 1 and 10
  ```

* Security question:

  ```text
  Does the API Enforce the Same Constraint?
  ```

---

## Validation Test Categories

* Depending on the parameter, controlled tests may examine:

  ```text
  Missing Value

  Empty Value

  Null

  Negative Number

  Zero

  Unexpected Type

  Boundary Value

  Excessive Length

  Unexpected Enumeration
  ```

* Use only safe values appropriate to the endpoint.

---

## Type Validation

* Expected:

  ```json
  {
    "quantity": 5
  }
  ```

* Controlled variations might include:

  ```json
  {
    "quantity": "5"
  }
  ```

* Or:

  ```json
  {
    "quantity": null
  }
  ```

* Observe:

  ```text
  Validation

  Coercion

  Error Handling

  Business Behavior
  ```

---

## Missing Fields

* Remove one field at a time.

* Determine whether it is:

  ```text
  Required

  Optional

  Defaulted

  Server-Controlled
  ```

* Unexpected default behavior may expose business-logic issues.

---

## Extra Fields

* Add a harmless unexpected field.

* Observe whether the API:

  ```text
  Rejects It

  Ignores It

  Persists It

  Processes It
  ```

* This helps understand deserialization and object binding behavior.

---

## Null Handling

* APIs may treat:

  ```text
  Missing

  Null

  Empty String

  Zero
  ```

* Differently.

* These differences can affect:

  ```text
  Validation

  State

  Authorization

  Business Rules
  ```

---

## HTTP Method Testing

* A resource may support different methods.

* Example:

  ```text
  GET    /api/users/42

  PATCH  /api/users/42

  DELETE /api/users/42
  ```

* Each method should enforce appropriate authorization.

---

## Method-Based Security Questions

```text
Can the User Read the Object?

Can the User Modify the Object?

Can the User Delete the Object?
```

* Permission to perform one action does not imply permission for all actions.

---

## Alternate Method Testing

* If documentation or legitimate traffic indicates alternate methods, compare:

  ```text
  POST

  PUT

  PATCH

  DELETE
  ```

* Do not blindly send destructive methods to production objects.

* Use controlled resources and explicit authorization.

---

## Method Override Mechanisms

* Some applications support method override patterns such as:

  ```text
  X-HTTP-Method-Override
  ```

* Or form parameters that indicate an alternate method.

* If legitimately used by the application, test whether authorization remains consistent.

---

## Content-Type Handling

* APIs may expect:

  ```http
  Content-Type: application/json
  ```

* Some backends may also parse:

  ```text
  application/x-www-form-urlencoded

  multipart/form-data

  XML
  ```

* Different parsers may apply different validation behavior.

---

## Content-Type Testing Principle

* Only test alternate formats when:

  ```text
  The Application Supports Them

  Documentation Indicates Them

  or

  There Is Evidence the Backend Parses Them
  ```

* Avoid arbitrary parser probing without a reason.

---

## Accept Header

* APIs may respond differently based on:

  ```http
  Accept: application/json
  ```

* Alternate representations can sometimes expose different data or error behavior.

* Record meaningful differences.

---

## Collection Endpoints

* Example:

  ```http
  GET /api/orders
  ```

* Security questions include:

  ```text
  Which Objects Are Returned?

  Is Filtering Server-Side?

  Are Other Users' Objects Included?

  Are Sensitive Fields Exposed?
  ```

---

## Collection vs Individual Object

* Test both:

  ```text
  GET /api/orders

  GET /api/orders/{id}
  ```

* Authorization may be implemented differently.

---

## Filtering Parameters

* Example:

  ```http
  GET /api/orders?user_id=42
  ```

* Ask:

  ```text
  Is user_id Trusted?

  Is It Used Only as a Filter?

  Does Authorization Independently Restrict Results?
  ```

* Client-supplied filtering must not replace authorization.

---

## Pagination

* Example:

  ```text
  ?page=2

  ?limit=50

  ?offset=100

  ?cursor=...
  ```

* Test whether authorization remains consistent across pages.

* Do not assume the first page represents the complete accessible dataset.

---

## Search Endpoints

* Example:

  ```http
  GET /api/search?q=alice
  ```

* Search results may expose:

  ```text
  Objects

  User Data

  Hidden Records

  Cross-Tenant Results
  ```

* Compare search authorization with direct object access.

---

## Bulk Operations

* APIs may support:

  ```text
  Bulk Update

  Bulk Delete

  Batch Export

  Multi-Object Actions
  ```

* Example:

  ```json
  {
    "ids": [41, 42, 43]
  }
  ```

* Security question:

  ```text
  Is Authorization Checked for Every Object?
  ```

---

## Batch Authorization

* A secure implementation should not assume:

  ```text
  First Object Authorized

  =

  Every Object Authorized
  ```

* Mixed-ownership batches deserve careful controlled testing.

---

## State-Changing Requests

* State-changing functions include:

  ```text
  Create

  Update

  Delete

  Transfer

  Refund

  Approve

  Invite

  Reset

  Upload
  ```

* These require additional care.

---

## Safe State Testing

* Before replaying:

  ```text
  Understand Side Effect

  ↓

  Use Controlled Object

  ↓

  Record Initial State

  ↓

  Send One Request

  ↓

  Verify Final State
  ```

* Avoid accidental duplicate or destructive actions.

---

## Replay Behavior

* Some requests may be safely replayable.

* Others may create:

  ```text
  Duplicate Orders

  Duplicate Payments

  Multiple Invitations

  Repeated Emails

  Repeated State Changes
  ```

* Understand side effects before replaying.

---

## Idempotency

* An idempotent operation should generally produce the same intended state when repeated.

* Commonly:

  ```text
  GET

  PUT

  DELETE
  ```

* Are designed to be idempotent.

* But implementation behavior may differ.

---

## Idempotency Keys

* Some APIs use headers such as:

  ```text
  Idempotency-Key
  ```

* These may help prevent duplicate operations.

* If present, understand:

  ```text
  Scope

  Lifetime

  Binding

  Replay Behavior
  ```

---

## Business Logic Testing

* REST API testing should examine more than technical input validation.

* Example workflow:

  ```text
  Create Order

  ↓

  Pay

  ↓

  Ship

  ↓

  Refund
  ```

* Expected state transitions matter.

---

## State Transition Questions

```text
Can Refund Occur Before Payment?

Can Cancel Occur After Completion?

Can Approval Be Skipped?

Can a Completed Action Be Replayed?

Can a User Change Server-Controlled State Directly?
```

* These are business-logic questions.

---

## Server-Controlled Fields

* Example object:

  ```json
  {
    "id": 481,
    "status": "paid",
    "owner_id": 42,
    "total": 1500
  }
  ```

* Not every returned property should necessarily be client-modifiable.

* Identify:

  ```text
  Client-Controlled Fields

  vs

  Server-Controlled Fields
  ```

---

## Error Analysis

* API errors may reveal:

  ```text
  Validation Rules

  Object Existence

  Permission Boundaries

  Internal Field Names

  Expected Types

  Workflow State
  ```

* Compare errors carefully.

---

## Example Differential Responses

* Request A:

  ```text
  404 Not Found
  ```

* Request B:

  ```text
  403 Forbidden
  ```

* This difference may suggest different backend decisions.

* But it is only a clue until security impact is demonstrated.

---

## Response Comparison

* Compare:

  ```text
  Status Code

  Response Length

  Response Body

  Headers

  Timing

  Object IDs

  Error Messages

  Server State
  ```

* Use multiple signals.

---

## Burp Comparer

* For subtle differences:

  ```text
  Send Response to Comparer
  ```

* Useful for comparing:

  ```text
  Authorized vs Unauthorized

  Baseline vs Modified

  User A vs User B

  API v1 vs API v2
  ```

---

## Response Length

* Length differences can indicate changed behavior.

* But:

  ```text
  Different Length

  ≠

  Vulnerability
  ```

* Dynamic values may change response size naturally.

---

## Timing

* Timing differences can provide clues.

* But network variability creates noise.

* Repeat controlled tests before drawing conclusions.

---

## REST API Authorization Matrix

* Build matrices for high-value resources.

| Role    | Own Object | Same-Tenant Other Object | Cross-Tenant Object | Admin Action |
| ------- | :--------: | :----------------------: | :-----------------: | :----------: |
| User    |    Allow   |          Depends         |         Deny        |     Deny     |
| Manager |    Allow   |          Depends         |         Deny        |    Depends   |
| Admin   |    Allow   |           Allow          |       Depends       |     Allow    |

* Replace assumptions with documented application requirements whenever available.

---

## REST API Test Card

```text
Endpoint:

Method:

Business Function:

Authentication:

User:

Role:

Tenant:

Object:

Object ID:

Object Owner:

Baseline Request:

Expected Security Rule:

Variable Changed:

Modified Request:

Expected Result:

Actual Status:

Actual Response:

Server State:

Side Effects:

Evidence:

Conclusion:

Follow-Up:
```

---

## REST API Testing Workflow

```text
Map Endpoint

↓

Understand Business Function

↓

Capture Legitimate Baseline

↓

Identify Identity Context

↓

Identify Object and Ownership

↓

Identify Inputs

↓

Define Expected Security Rule

↓

Send to Repeater

↓

Modify One Variable

↓

Compare Response

↓

Verify Server State

↓

Repeat With Controlled Identities

↓

Validate Impact

↓

Document Evidence
```

---

## Testing Checklist

### Authentication

* Check:

  ```text
  Missing Credentials

  Invalid Credentials

  Expired Credentials

  Logout Behavior

  Credential Replacement
  ```

---

### Object Authorization

* Check:

  ```text
  Own Object

  Other Controlled User Object

  Same-Tenant Object

  Cross-Tenant Object
  ```

---

### Function Authorization

* Check:

  ```text
  User Function

  Manager Function

  Admin Function

  Hidden Function
  ```

---

### Property Authorization

* Check:

  ```text
  Allowed Fields

  Restricted Fields

  Server-Controlled Fields

  Unexpected Fields
  ```

---

### Input Validation

* Check:

  ```text
  Missing

  Empty

  Null

  Boundary

  Type

  Enumeration

  Length
  ```

---

### Methods

* Check:

  ```text
  Observed Supported Methods

  Authorization Per Method

  Alternate Legitimate Methods

  Method Override If Used
  ```

---

### Collections

* Check:

  ```text
  Listing

  Filtering

  Search

  Pagination

  Bulk Operations
  ```

---

### State

* Check:

  ```text
  Valid Transition

  Invalid Transition

  Replay

  Duplicate Action

  Server-Controlled State
  ```

---

## Example — Object Authorization

### Baseline

```http
GET /api/v2/projects/781 HTTP/1.1
Authorization: Bearer <User-A-Token>
```

* Project `781` belongs to User A.

### Controlled Test

```text
Use User A Authentication

↓

Request a Project Belonging to Controlled User B

↓

Compare Response
```

### Validate

* Determine whether the response exposes:

  ```text
  User B Project Data

  Sensitive Fields

  Related Objects
  ```

* If access is unauthorized and reproducible, investigate impact.

---

## Example — Restricted Property

### Baseline

```http
PATCH /api/v2/profile HTTP/1.1
Content-Type: application/json

{
  "name": "Alice"
}
```

### Hypothesis

```text
Can a Standard User Modify a Restricted Property?
```

### Controlled Test

* Add one known property in an authorized test environment.

* Then:

  ```text
  Send Request

  ↓

  Retrieve Profile Again

  ↓

  Verify Persisted State
  ```

* A `200 OK` alone is insufficient evidence.

---

## Example — Collection Authorization

### Request

```http
GET /api/v2/orders?user_id=42 HTTP/1.1
```

### Security Question

```text
Does the Server Restrict Results Based on
the Authenticated User's Actual Authorization,
or Does It Trust user_id as the Security Boundary?
```

* Compare controlled users and objects.

---

## Example — Method Authorization

* Observed:

  ```text
  GET /api/files/52
  ```

* Also legitimately supported:

  ```text
  DELETE /api/files/52
  ```

* Security question:

  ```text
  Does Read Permission Incorrectly Imply Delete Permission?
  ```

* Test only with controlled disposable resources.

---

## Example — Workflow State

* API exposes:

  ```text
  POST /api/orders/481/cancel
  ```

* Expected:

  ```text
  Pending Order

  →

  Cancelled
  ```

* Security question:

  ```text
  Can an Order Be Cancelled From a State
  Where Cancellation Should No Longer Be Allowed?
  ```

---

## Evidence Requirements

* A strong REST API finding should include:

  ```text
  Endpoint

  Method

  Authentication Context

  Role

  Object Ownership

  Original Request

  Modified Request

  Original Response

  Modified Response

  Verified State or Data Access

  Reproduction Steps

  Security Impact
  ```

---

## Anomaly vs Vulnerability

* An anomaly:

  ```text
  Unexpected Response
  ```

* A validated vulnerability:

  ```text
  Reproducible Security Rule Violation

  +

  Demonstrable Impact
  ```

* Do not confuse the two.

---

## False Positive Control

* Before concluding:

  ```text
  Repeat the Test

  ↓

  Restore Baseline

  ↓

  Confirm Identity

  ↓

  Confirm Object Ownership

  ↓

  Confirm Server State

  ↓

  Eliminate Caching or UI Artifacts
  ```

---

## Professional Tips

* Build an API map before deep testing.
* Preserve a clean baseline before modifying requests.
* Understand the business function behind each endpoint.
* Change one variable at a time.
* Track user, role, tenant, object, and ownership explicitly.
* Test object-level and property-level authorization separately.
* Do not rely only on HTTP status codes.
* Verify state-changing results through a separate retrieval when possible.
* Compare collection and individual-object authorization.
* Review authorization independently for each HTTP method.
* Treat client-supplied filters as data, not authorization controls.
* Test bulk operations object by object conceptually.
* Understand replay side effects before repeating requests.
* Use disposable controlled objects for destructive tests.
* Compare multiple response signals.
* Use Comparer when differences are subtle.
* Treat unexpected behavior as a hypothesis until validated.
* Preserve evidence from both baseline and modified requests.

---

## Common Mistakes

* Testing REST endpoints without first understanding their business purpose.
* Modifying multiple variables simultaneously.
* Using only one test account for authorization testing.
* Assuming `200 OK` always means an action succeeded.
* Assuming `403 Forbidden` guarantees no state change occurred.
* Testing only path identifiers while ignoring body and header identifiers.
* Confusing authentication with authorization.
* Testing object-level authorization but ignoring property-level authorization.
* Assuming frontend validation is server-side validation.
* Failing to verify whether changes persisted.
* Ignoring collection endpoints.
* Ignoring filters, search, and pagination.
* Assuming authorization is identical across HTTP methods.
* Blindly testing destructive methods.
* Replaying state-changing requests without understanding side effects.
* Treating every extra JSON field as a mass-assignment vulnerability.
* Treating response-length differences as proof.
* Ignoring tenant context.
* Failing to establish expected behavior before testing.
* Reporting anomalies before confirming reproducibility and impact.

---

## Practice Tasks

### Task 1 — Select an Authorized REST API

* Use:

  ```text
  Deliberately Vulnerable Lab

  System You Own

  or

  Explicitly Authorized Target
  ```

---

### Task 2 — Capture API Traffic

* Perform normal application workflows.

* Identify at least five REST requests if available.

---

### Task 3 — Choose a Resource

* Select an object such as:

  ```text
  User

  Order

  Project

  File
  ```

* Record its identifier and owner.

---

### Task 4 — Map CRUD Operations

* Identify available:

  ```text
  Create

  Read

  Update

  Delete
  ```

* Operations.

---

### Task 5 — Create Baselines

* Send representative requests to Repeater.

* Record:

  ```text
  Status

  Length

  Body

  Identity

  Role

  Tenant

  Object
  ```

---

### Task 6 — Map Input Locations

* Identify:

  ```text
  Path Inputs

  Query Inputs

  Headers

  Cookies

  JSON Fields
  ```

---

### Task 7 — Test Controlled Object Authorization

* With authorized controlled identities:

  ```text
  Own Object

  vs

  Other Controlled User Object
  ```

* Record expected and actual behavior.

---

### Task 8 — Test Property Handling

* Identify:

  ```text
  Allowed Fields

  Server-Controlled Fields

  Restricted Fields
  ```

* Perform only safe controlled modifications.

---

### Task 9 — Test Validation

* Choose one harmless field.

* Compare:

  ```text
  Valid

  Missing

  Empty

  Null

  Boundary
  ```

* Inputs where appropriate.

---

### Task 10 — Compare Methods

* For a controlled resource, identify legitimately supported methods.

* Compare authorization behavior.

---

### Task 11 — Analyze Collections

* Test:

  ```text
  Listing

  Filtering

  Pagination

  Search
  ```

* Where available.

---

### Task 12 — Analyze a State Change

* Choose a safe state-changing operation.

* Record:

  ```text
  Initial State

  Request

  Response

  Final State
  ```

---

### Task 13 — Build Authorization Matrix

* Include:

  ```text
  Identity

  Role

  Tenant

  Object Ownership

  Function

  Expected Result

  Actual Result
  ```

---

### Task 14 — Document One Hypothesis

* Example:

  ```text
  Does the API verify object ownership independently
  of the object identifier supplied by the client?
  ```

* Record evidence whether confirmed or rejected.

---

## Investigation Journal

```text
Application:

Date:

Scope:

API Host:

API Version:

Endpoint:

Method:

Business Function:

Authentication:

User:

Role:

Tenant:

Object:

Object ID:

Object Owner:

Input Locations:

Baseline Request:

Baseline Response:

Expected Security Rule:

Variable Modified:

Modified Request:

Modified Response:

Status Difference:

Length Difference:

Body Difference:

Server State:

Side Effects:

Authorization Result:

Validation Result:

Reproducible?:

Security Impact:

Evidence:

Conclusion:

Next Steps:
```

---

## Interview Questions

1. What is REST?
2. What is a resource in a REST API?
3. What is the difference between a collection and an individual resource?
4. Why can nested resources create multiple authorization boundaries?
5. How do HTTP methods commonly map to REST operations?
6. Why should you not assume an HTTP method always has its conventional meaning?
7. What is your workflow for testing a REST API with Burp Suite?
8. Why should you establish a baseline before modifying a request?
9. Why is Repeater useful for REST API testing?
10. Where can user-controlled API input appear?
11. How do you test path parameters?
12. Why can query parameters be security-relevant?
13. Why should custom identity or tenant headers be investigated?
14. What is object-level authorization?
15. How would you test object-level authorization safely?
16. Why are two controlled accounts useful for authorization testing?
17. Why should you not rely only on HTTP status codes?
18. What is function-level authorization?
19. What is the difference between authentication and authorization?
20. What is property-level authorization?
21. How does property-level authorization differ from object-level authorization?
22. What is mass assignment?
23. Why does accepting an extra JSON field not automatically prove a vulnerability?
24. Why should persisted state be verified after an update request?
25. How do you test server-side validation?
26. Why should missing, null, empty, and zero values be tested separately?
27. Why should authorization be reviewed independently for each HTTP method?
28. What are method override mechanisms?
29. Why can content-type handling be security-relevant?
30. Why should collection endpoints be tested separately from individual-resource endpoints?
31. Why are filtering parameters not a substitute for authorization?
32. How can pagination affect security testing?
33. What security risks can exist in bulk operations?
34. Why should authorization conceptually be checked for every object in a batch?
35. What precautions should be taken before replaying state-changing requests?
36. What is idempotency?
37. What is an idempotency key?
38. How can REST APIs contain business-logic vulnerabilities?
39. Why are state transitions security-relevant?
40. What signals should you compare between baseline and modified responses?
41. When is Burp Comparer useful?
42. What is the difference between an anomaly and a validated vulnerability?
43. How do you reduce false positives during REST API testing?
44. What evidence should be included in a REST API vulnerability report?
45. Describe your complete methodology for testing an unfamiliar REST API.

---

## Quick Revision

* REST API:

  ```text
  Resources

  +

  Methods

  +

  Representations
  ```

* Security context:

  ```text
  Resource

  +

  Method

  +

  Identity

  +

  Authorization

  +

  Input

  +

  State

  +

  Business Rule
  ```

* Testing workflow:

  ```text
  Capture

  ↓

  Baseline

  ↓

  Understand

  ↓

  Modify One Variable

  ↓

  Compare

  ↓

  Verify State

  ↓

  Validate
  ```

* Input locations:

  ```text
  Path

  Query

  Headers

  Cookies

  Body
  ```

* Object authorization:

  ```text
  User

  +

  Object

  +

  Ownership

  +

  Tenant

  +

  Action
  ```

* Object-level:

  ```text
  Can I Access This Object?
  ```

* Property-level:

  ```text
  Can I Modify This Field?
  ```

* Function-level:

  ```text
  Can I Perform This Action?
  ```

* Important rule:

  ```text
  Authentication

  ≠

  Authorization
  ```

* Another important rule:

  ```text
  200 OK

  ≠

  Confirmed Success
  ```

* And:

  ```text
  403 Forbidden

  ≠

  Guaranteed No Side Effect
  ```

* Validation:

  ```text
  Valid

  Missing

  Empty

  Null

  Boundary

  Type
  ```

* State-changing test:

  ```text
  Initial State

  ↓

  One Controlled Request

  ↓

  Final State
  ```

* Finding:

  ```text
  Unexpected Behavior

  +

  Reproducibility

  +

  Security Rule Violation

  +

  Demonstrable Impact
  ```

---

## Key Takeaways

* REST API testing should begin with a mapped and understood endpoint rather than random payload injection.
* Resources, methods, identities, authorization rules, inputs, state, and business logic must be analyzed together.
* Preserve a clean baseline before modifying any request.
* Burp Repeater is the primary workspace for controlled manual REST API testing.
* User-controlled input can appear in paths, queries, headers, cookies, JSON, forms, and multipart requests.
* Change one variable at a time so behavioral differences can be attributed correctly.
* Object-level, function-level, and property-level authorization are distinct security concerns.
* Authentication does not imply correct authorization.
* Use controlled identities and known object ownership when testing access controls.
* HTTP status codes alone are insufficient; inspect response content and verify actual server state.
* Server-side validation must independently enforce security rules regardless of frontend restrictions.
* Extra or hidden JSON properties require validation before they can be considered security findings.
* Authorization should be reviewed independently across methods, collections, individual resources, bulk operations, and nested resources.
* Client-supplied filters must not act as substitutes for server-side authorization.
* State-changing requests require careful handling because replay may produce real side effects.
* Business workflows and state transitions are as important as technical request syntax.
* Response comparison should consider status, body, length, headers, timing, identity, object data, and final state.
* Unexpected behavior is only an anomaly until it is reproducible and demonstrates a meaningful security-rule violation.
* The professional REST API workflow is **map → baseline → understand → hypothesize → modify one variable → compare → verify state → validate impact → document evidence**.
