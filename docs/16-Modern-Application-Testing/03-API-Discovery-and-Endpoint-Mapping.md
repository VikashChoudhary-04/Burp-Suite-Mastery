# API Discovery & Endpoint Mapping

> **Module 16 — Modern Application Testing**

> **Progress**

```text
████░░░░░░░░░░░ 4 / 15 Files
```

---

## Overview

* APIs form the backend communication layer for many modern applications.

* A single application may expose:

  * Public APIs.
  * Authenticated APIs.
  * Administrative APIs.
  * Internal APIs.
  * Versioned APIs.
  * Mobile APIs.
  * Partner APIs.
  * GraphQL interfaces.
  * WebSocket endpoints.

* Before testing API vulnerabilities, you need to understand the available attack surface.

* Effective API testing begins with:

  ```text
  Discover

  ↓

  Inventory

  ↓

  Classify

  ↓

  Understand

  ↓

  Prioritize

  ↓

  Test
  ```

* Randomly sending payloads to individual endpoints is not a methodology.

* A professional tester builds an **API map** that connects:

  ```text
  Endpoint

  +

  HTTP Method

  +

  Authentication

  +

  Role

  +

  Object

  +

  Parameters

  +

  Business Function

  +

  State Change
  ```

* This transforms raw traffic into a structured security-testing plan.

---

## Learning Objectives

* By the end of this lesson, you will be able to:

  * Understand why API discovery must precede deep API testing.
  * Identify API traffic using Burp Suite.
  * Discover endpoints through normal application usage.
  * Use HTTP History and Site Map for API mapping.
  * Discover endpoints from JavaScript.
  * Recognize common API base paths.
  * Identify versioned and legacy APIs.
  * Identify endpoints from application errors and responses.
  * Understand the value of API documentation.
  * Identify objects and object identifiers.
  * Map HTTP methods to business functions.
  * Identify authentication requirements.
  * Map roles and tenant boundaries.
  * Identify state-changing endpoints.
  * Build structured endpoint and parameter inventories.
  * Prioritize endpoints for deeper security testing.
  * Convert API observations into security hypotheses.

---

## Why API Discovery Comes First

* You cannot test functionality you have not discovered.

* Suppose normal browsing reveals:

  ```text
  GET /api/v2/profile

  GET /api/v2/orders
  ```

* But the application actually exposes:

  ```text
  GET    /api/v2/profile

  PATCH  /api/v2/profile

  GET    /api/v2/orders

  GET    /api/v2/orders/{id}

  POST   /api/v2/orders

  DELETE /api/v2/orders/{id}

  POST   /api/v2/orders/{id}/refund

  GET    /api/v1/orders/export
  ```

* Testing only the first two requests would leave significant functionality unexplored.

---

## API Discovery Principle

```text
Observed Traffic

≠

Complete API Attack Surface
```

* API discovery should combine multiple sources.

---

## API Discovery Sources

* Useful sources include:

  ```text
  Normal Application Traffic

  HTTP History

  Target Site Map

  JavaScript

  API Documentation

  Error Messages

  API Responses

  Client-Side Routes

  Mobile Application Traffic

  GraphQL Operations

  WebSocket Communication

  Historical / Versioned Endpoints
  ```

* No single source should automatically be treated as complete.

---

## What Is an Endpoint?

* An API endpoint represents an interface through which a client interacts with backend functionality.

* Example:

  ```text
  GET /api/users/42
  ```

* The endpoint contains more security context than the path alone.

* A useful representation is:

  ```text
  Method:

  GET

  Endpoint:

  /api/users/{id}

  Object:

  User

  Identifier:

  id

  Function:

  Read User

  Authentication:

  Required
  ```

---

## Endpoint vs Route

* Do not treat only the URL as the endpoint identity.

* These may represent different security operations:

  ```text
  GET /api/users/42

  PATCH /api/users/42

  DELETE /api/users/42
  ```

* Same path.

* Different:

  ```text
  Methods

  Actions

  Authorization Requirements

  Security Impact
  ```

---

## Important Principle

```text
Path

+

Method

=

Distinct Security Surface
```

---

## Common API Base Paths

* APIs may appear under paths such as:

  ```text
  /api/

  /api/v1/

  /api/v2/

  /rest/

  /service/

  /services/

  /graphql

  /gql/

  /internal/

  /mobile/

  /admin/api/
  ```

* These are patterns, not guarantees.

---

## API Hosts

* APIs may also use dedicated hosts.

* Example:

  ```text
  app.example.test

  api.example.test

  auth.example.test

  files.example.test
  ```

* Record each host separately.

---

## Scope Comes First

* A newly discovered API host must be checked against authorization before active testing.

* Remember:

  ```text
  Host Discovered

  ≠

  Host Authorized
  ```

---

## Discovery Through Normal Application Usage

* Begin by using the application normally.

* Exercise:

  ```text
  Registration

  Login

  Profile

  Search

  Account Settings

  File Functions

  Business Workflows

  Logout
  ```

* Observe the resulting API traffic.

---

## Why Normal Usage Matters

* Normal interaction establishes:

  ```text
  Legitimate Request Shapes

  Expected Parameters

  Authentication Context

  Normal Status Codes

  Business Workflows

  Object Relationships
  ```

* This creates baselines for later testing.

---

## Using Burp HTTP History

* Navigate to:

  ```text
  Proxy

  →

  HTTP History
  ```

* Look for:

  ```text
  JSON Requests

  JSON Responses

  API Paths

  Bearer Tokens

  Object IDs

  Versioned Routes

  State-Changing Methods
  ```

---

## Useful HTTP History Columns

* Pay attention to:

  ```text
  Host

  Method

  URL

  Status

  MIME Type

  Extension

  Length
  ```

* Patterns often reveal API structure.

---

## Example Traffic

```text
GET   /api/v2/me
GET   /api/v2/projects
GET   /api/v2/projects/781
PATCH /api/v2/projects/781
GET   /api/v2/projects/781/members
POST  /api/v2/projects/781/invite
```

* From this alone, you can infer:

  ```text
  Object:

  Project

  Identifier:

  781

  Related Object:

  Member

  State-Changing Function:

  Invite
  ```

---

## Using Target Site Map

* Navigate to:

  ```text
  Target

  →

  Site Map
  ```

* Site Map helps organize discovered paths hierarchically.

* Example:

  ```text
  api.example.test
      │
      └── api
          │
          ├── v1
          │
          └── v2
              │
              ├── users
              ├── projects
              ├── files
              └── admin
  ```

* This can reveal patterns that are harder to notice in chronological HTTP history.

---

## HTTP History vs Site Map

* Use HTTP History to understand:

  ```text
  Sequence

  Workflow

  Timing

  Request Context
  ```

* Use Site Map to understand:

  ```text
  Structure

  Hosts

  Paths

  Endpoint Families

  Application Coverage
  ```

* Use both together.

---

## Discovery Through JavaScript

* As covered in the previous lesson, JavaScript may reveal:

  ```text
  API Paths

  API Base URLs

  Methods

  Parameters

  Hidden Functions

  Versions

  GraphQL

  WebSockets
  ```

---

## Example

* JavaScript contains:

  ```text
  /api/v2/reports/export

  /api/admin/users

  /api/v1/files
  ```

* None appear during current browsing.

* Record them as:

  ```text
  Candidate Endpoints
  ```

* Then determine whether they are:

  ```text
  Active

  Role Restricted

  Deprecated

  Feature Flagged

  Removed
  ```

---

## Discovery Through API Responses

* API responses themselves may reveal additional resources.

* Example:

  ```json
  {
    "id": 781,
    "name": "Project Alpha",
    "members_url": "/api/v2/projects/781/members",
    "files_url": "/api/v2/projects/781/files"
  }
  ```

* This exposes related endpoints.

---

## Hypermedia and Links

* Some APIs explicitly return links or actions.

* Look for fields such as:

  ```text
  url

  href

  link

  next

  previous

  actions

  resources
  ```

* These may expand the endpoint map.

---

## Discovery Through Error Messages

* Errors may expose:

  ```text
  Expected Paths

  Required Parameters

  Supported Methods

  Object Types

  Internal Route Names

  API Versions
  ```

* Example:

  ```text
  Method PATCH not allowed.
  Supported methods: GET, PUT.
  ```

* This may indicate alternate supported methods.

---

## Error Messages Are Leads

* Do not intentionally generate destructive errors.

* Treat naturally observed error information as reconnaissance data.

---

## OPTIONS Requests

* Some APIs respond to:

  ```http
  OPTIONS /api/resource
  ```

* Responses may indicate supported methods.

* Example:

  ```http
  Allow: GET, POST, OPTIONS
  ```

* However:

  ```text
  Advertised Method

  ≠

  Guaranteed Security Behavior
  ```

* Each relevant method still requires independent analysis.

---

## API Documentation

* Applications may expose authorized documentation through:

  ```text
  OpenAPI

  Swagger UI

  API Reference Pages

  Developer Portals

  GraphQL Documentation
  ```

* Documentation can significantly improve endpoint coverage.

---

## Documentation May Reveal

* API documentation may describe:

  ```text
  Paths

  Methods

  Parameters

  Request Schemas

  Response Schemas

  Authentication

  Object Models

  Error Codes
  ```

---

## Documentation vs Reality

* Documentation may be:

  ```text
  Current

  Incomplete

  Outdated

  Public-Only

  Missing Internal Functions
  ```

* Therefore:

  ```text
  Documentation

  +

  Observed Traffic

  +

  JavaScript

  =

  Better Coverage
  ```

---

## OpenAPI Specifications

* OpenAPI specifications may exist as files such as:

  ```text
  openapi.json

  openapi.yaml

  swagger.json
  ```

* If legitimately accessible, they may provide a structured API inventory.

* Do not assume every possible documentation path exists or brute-force unrelated paths without authorization.

---

## Endpoint Families

* Group related endpoints.

* Example:

  ```text
  /api/v2/users

  /api/v2/users/{id}

  /api/v2/users/{id}/settings

  /api/v2/users/{id}/sessions
  ```

* Group:

  ```text
  User Management
  ```

---

## Why Grouping Helps

* Endpoint families make it easier to identify:

  ```text
  Missing Operations

  Inconsistent Authorization

  Object Relationships

  Privileged Functions

  CRUD Coverage
  ```

---

## CRUD Mapping

* For important objects, map:

  ```text
  Create

  Read

  Update

  Delete
  ```

* Example:

| Operation | Method | Endpoint             |
| --------- | ------ | -------------------- |
| Create    | POST   | `/api/projects`      |
| Read      | GET    | `/api/projects/{id}` |
| Update    | PATCH  | `/api/projects/{id}` |
| Delete    | DELETE | `/api/projects/{id}` |

* Authorization may differ for every operation.

---

## Object-Centric Mapping

* APIs frequently expose business objects.

* Examples:

  ```text
  Users

  Orders

  Files

  Projects

  Tickets

  Messages

  Invoices

  Organizations

  API Keys
  ```

* Map the API around these objects.

---

## Object Inventory

```text
Object:

Identifier:

Owner:

Tenant:

Create Endpoint:

Read Endpoint:

Update Endpoint:

Delete Endpoint:

Privileged Actions:

Related Objects:

Sensitive Fields:
```

---

## Object Identifiers

* Identifiers may appear in:

  ```text
  URL Paths

  Query Parameters

  JSON Bodies

  GraphQL Variables

  Headers

  WebSocket Messages
  ```

* Examples:

  ```text
  42

  781

  UUID

  Username

  Email

  Slug

  Composite Identifier
  ```

---

## Identifier Discovery

* Example:

  ```http
  GET /api/orders/7f24c781
  ```

* Or:

  ```json
  {
    "order_id": "7f24c781"
  }
  ```

* Record:

  ```text
  Object:

  Order

  Identifier:

  7f24c781
  ```

---

## Identifiers Are Not Authorization

* An unpredictable identifier does not replace authorization.

* Security rule:

  ```text
  User Knows Identifier

  ≠

  User Is Authorized
  ```

---

## Parameter Mapping

* Parameters may exist in:

  ```text
  Path

  Query

  Headers

  Cookies

  Form Body

  JSON

  XML

  Multipart Data
  ```

---

## Parameter Inventory

| Parameter | Location | Type   | Meaning         | Security Interest      |
| --------- | -------- | ------ | --------------- | ---------------------- |
| `user_id` | Path     | ID     | User object     | Authorization          |
| `org_id`  | JSON     | ID     | Tenant          | Cross-tenant           |
| `role`    | JSON     | String | Privilege       | Property authorization |
| `price`   | JSON     | Number | Financial value | Business logic         |
| `status`  | JSON     | String | Workflow state  | State manipulation     |

---

## Parameters vs Properties

* A parameter may represent:

  ```text
  Search Input

  Object Identifier

  Filter

  Pagination

  Business Value

  Object Property
  ```

* Understanding meaning is more important than merely recording the name.

---

## Authentication Mapping

* For each endpoint, record whether it requires:

  ```text
  No Authentication

  Session Cookie

  Bearer Token

  API Key

  Multiple Credentials
  ```

---

## Authentication Inventory

| Endpoint             | Anonymous | Authenticated | Mechanism    |
| -------------------- | :-------: | :-----------: | ------------ |
| `/api/login`         |    Yes    |       No      | Credentials  |
| `/api/me`            |     No    |      Yes      | Bearer       |
| `/api/public/search` |    Yes    |    Optional   | None / Token |
| `/api/admin/users`   |     No    |      Yes      | Bearer       |

---

## Authorization Context

* Authentication alone is not enough.

* Record:

  ```text
  Role

  Object Owner

  Tenant

  Required Permission
  ```

* Example:

  ```text
  GET /api/projects/781
  ```

* Context:

  ```text
  User:

  Alice

  Role:

  Member

  Tenant:

  Organization A

  Object Owner:

  Organization A
  ```

---

## Role Mapping

* Identify which endpoints are used by:

  ```text
  Anonymous Users

  Standard Users

  Managers

  Administrators

  Organization Owners

  Support Roles
  ```

* Different roles may expose different endpoint families.

---

## Differential Role Mapping

* With authorized controlled accounts:

  ```text
  Browse as User

  ↓

  Record API Traffic

  ↓

  Browse as Higher-Privilege Role

  ↓

  Record API Traffic

  ↓

  Compare
  ```

* Differences may reveal:

  ```text
  Additional Endpoints

  Additional Methods

  Privileged Parameters

  Administrative Objects
  ```

---

## Tenant Mapping

* Multi-tenant APIs may include:

  ```text
  organization_id

  tenant_id

  workspace_id

  team_id
  ```

* Or tenant information may be embedded in paths:

  ```text
  /organizations/91/projects/781
  ```

---

## Tenant Context

* Record:

  ```text
  User

  Role

  Tenant

  Object

  Object Tenant
  ```

* This becomes essential for later cross-tenant authorization testing.

---

## State-Changing Endpoints

* Identify endpoints that:

  ```text
  Create

  Update

  Delete

  Approve

  Transfer

  Refund

  Upload

  Invite

  Reset

  Export

  Generate
  ```

* These often deserve higher priority.

---

## Method Is Only a Clue

* Commonly:

  ```text
  POST

  PUT

  PATCH

  DELETE
  ```

* Change state.

* But always verify behavior.

* A poorly designed `GET` may also trigger an action.

---

## Business Function Mapping

* Translate technical endpoints into business meaning.

* Instead of recording only:

  ```text
  POST /api/v2/orders/481/refund
  ```

* Record:

  ```text
  Business Function:

  Refund Order

  Object:

  Order 481

  State Change:

  Yes

  Financial Impact:

  Yes

  Expected Role:

  Authorized Staff / Owner
  ```

---

## Why Business Meaning Matters

* Vulnerabilities often exist in:

  ```text
  Rules

  Relationships

  State

  Permissions
  ```

* Not merely in syntax.

---

## API Version Discovery

* Look for:

  ```text
  /v1/

  /v2/

  /v3/
  ```

* Or version headers such as:

  ```text
  Accept-Version

  API-Version
  ```

* Versioning may also occur through media types or other routing mechanisms.

---

## Version Inventory

| Version | Endpoint Family  | Observed | Status  |
| ------- | ---------------- | :------: | ------- |
| v1      | `/api/v1/orders` |    Yes   | Legacy  |
| v2      | `/api/v2/orders` |    Yes   | Current |
| v3      | `/api/v3/search` |    Yes   | New     |

---

## Why Old Versions Matter

* Older APIs may have:

  ```text
  Different Validation

  Different Authorization

  Deprecated Fields

  Excessive Data

  Legacy Functions
  ```

* But do not assume older automatically means vulnerable.

---

## Hidden Endpoints

* Hidden endpoints may be discovered through:

  ```text
  JavaScript

  Documentation

  Responses

  Old Versions

  Different Roles

  Mobile Clients

  Error Messages
  ```

* Classify each candidate.

---

## Candidate Endpoint States

```text
Observed Active

Documented

JavaScript Referenced

Role Restricted

Deprecated

Unknown

Confirmed Removed
```

* This avoids treating every discovered string as active functionality.

---

## API Content Types

* Record content types such as:

  ```text
  application/json

  application/x-www-form-urlencoded

  multipart/form-data

  application/xml
  ```

* Content type can affect:

  ```text
  Parsing

  Validation

  Parameter Handling

  Backend Processing
  ```

---

## Request Schema Mapping

* For important state-changing requests, record expected structure.

* Example:

  ```json
  {
    "name": "Project Alpha",
    "organization_id": 91,
    "visibility": "private"
  }
  ```

* Identify:

  ```text
  Required Fields

  Optional Fields

  IDs

  Privileged Properties

  Business Values
  ```

---

## Response Schema Mapping

* Responses may expose:

  ```text
  Object Fields

  Internal IDs

  Related Objects

  Permissions

  Status

  Metadata
  ```

* Example:

  ```json
  {
    "id": 781,
    "owner_id": 42,
    "organization_id": 91,
    "role": "member",
    "can_delete": false
  }
  ```

* This provides clues for later authorization testing.

---

## Pagination

* APIs may paginate using:

  ```text
  page

  limit

  offset

  cursor

  next_token
  ```

* Record pagination behavior when mapping collections.

* It may affect whether all accessible objects have been observed.

---

## Filters and Search

* Collection endpoints may accept:

  ```text
  status

  user_id

  organization_id

  sort

  filter

  query
  ```

* These parameters may expose additional object relationships and authorization questions.

---

## Nested Resources

* APIs often model relationships through nested paths.

* Example:

  ```text
  /organizations/91/projects/781/files/52
  ```

* Security boundaries may exist at multiple levels:

  ```text
  Organization

  Project

  File
  ```

* Do not assume validating one identifier is sufficient.

---

## Action Endpoints

* Not every API follows pure CRUD.

* Examples:

  ```text
  POST /orders/481/refund

  POST /users/42/disable

  POST /projects/781/archive

  POST /invoices/52/send
  ```

* These endpoints often represent high-value business functions.

---

## Prioritizing API Endpoints

* Prioritize endpoints involving:

  ```text
  Authentication

  Authorization

  User Objects

  Tenant Objects

  Administrative Functions

  Financial Actions

  Sensitive Data

  File Operations

  Password Reset

  API Keys

  Exports

  Imports

  External URLs

  State Transitions
  ```

---

## Simple Priority Model

* Consider:

  ```text
  Privilege

  +

  Data Sensitivity

  +

  State Change

  +

  Object Access

  +

  Business Impact

  +

  User-Controlled Input
  ```

* Higher combined importance receives deeper testing.

---

## API Endpoint Inventory

```text
Host:

Method:

Endpoint:

API Version:

Business Function:

Authentication:

Role:

Tenant:

Object:

Object Identifier:

Object Owner:

Parameters:

Content Type:

State Changing?:

Sensitive Data?:

Financial Impact?:

External Interaction?:

Observed Source:

Priority:

Security Questions:

Notes:
```

---

## API Mapping Table

| Method | Endpoint                  | Object | Auth   | Role       | State Change | Priority |
| ------ | ------------------------- | ------ | ------ | ---------- | :----------: | :------: |
| GET    | `/api/me`                 | User   | Bearer | User       |      No      |  Medium  |
| GET    | `/api/orders/{id}`        | Order  | Bearer | User       |      No      |   High   |
| PATCH  | `/api/orders/{id}`        | Order  | Bearer | User       |      Yes     |   High   |
| POST   | `/api/orders/{id}/refund` | Order  | Bearer | Privileged |      Yes     | Critical |
| DELETE | `/api/files/{id}`         | File   | Bearer | User       |      Yes     |   High   |

---

## Mapping Relationships

* Do not treat endpoints independently.

* Example:

  ```text
  Organization

  ↓

  Project

  ↓

  File
  ```

* Endpoints:

  ```text
  /organizations/{org}

  /organizations/{org}/projects/{project}

  /projects/{project}/files/{file}
  ```

* This creates authorization relationships across multiple objects.

---

## Build an Object Graph

```text
User

↓

Organization

↓

Project

↓

File
```

* Ask:

  ```text
  Which User Owns Which Object?

  Which Tenant Contains Which Object?

  Which Roles Can Perform Which Actions?
  ```

---

## API Mapping Workflow

```text
Define Scope

↓

Browse Application

↓

Capture API Traffic

↓

Identify API Hosts

↓

Identify Base Paths

↓

Group Endpoint Families

↓

Identify Methods

↓

Identify Objects

↓

Identify Parameters

↓

Map Authentication

↓

Map Roles

↓

Map Tenants

↓

Identify State Changes

↓

Identify Versions

↓

Correlate JavaScript and Documentation

↓

Prioritize

↓

Generate Security Hypotheses
```

---

## Step 1 — Establish Scope

* Record:

  ```text
  Authorized Hosts

  Authorized API Paths

  Exclusions

  Testing Restrictions
  ```

---

## Step 2 — Generate Natural Traffic

* Use normal application workflows.

* Avoid deep modification during initial mapping.

---

## Step 3 — Identify API Hosts

* Use:

  ```text
  HTTP History

  Site Map
  ```

* Record every relevant host.

---

## Step 4 — Identify API Base Paths

* Examples:

  ```text
  /api/v2/

  /graphql

  /services/
  ```

---

## Step 5 — Group Endpoint Families

* Group by:

  ```text
  Users

  Organizations

  Orders

  Files

  Admin

  Authentication
  ```

---

## Step 6 — Map Methods

* Record every observed method for each path.

---

## Step 7 — Map Objects

* Identify:

  ```text
  Object Type

  Identifier

  Owner

  Tenant
  ```

---

## Step 8 — Map Parameters

* Record:

  ```text
  Path Parameters

  Query Parameters

  JSON Properties

  Headers

  Cookies
  ```

---

## Step 9 — Map Identity Context

* Record:

  ```text
  Authentication Mechanism

  User

  Role

  Tenant
  ```

---

## Step 10 — Map Business Functions

* Translate technical requests into real actions.

---

## Step 11 — Identify Versions

* Compare observed and referenced API versions.

---

## Step 12 — Correlate Other Sources

* Add discoveries from:

  ```text
  JavaScript

  Documentation

  Responses

  Errors

  Other Authorized Clients
  ```

---

## Step 13 — Prioritize

* Mark:

  ```text
  Critical

  High

  Medium

  Low
  ```

---

## Step 14 — Generate Security Questions

* Each high-value endpoint should produce explicit hypotheses.

---

## From Observation to Hypothesis

### Observation

```text
GET /api/orders/481
```

* Object:

  ```text
  Order
  ```

* Identifier:

  ```text
  481
  ```

### Hypothesis

```text
Does the Server Verify That the Authenticated User
Is Authorized to Access Order 481?
```

---

## Example — Update Endpoint

### Observation

```text
PATCH /api/users/42
```

* Body:

  ```json
  {
    "name": "Alice",
    "department": "Sales"
  }
  ```

### Questions

```text
Can User 42 Update Only Their Own Record?

Can Other Users Update It?

Are Restricted Properties Accepted?

Does Tenant Membership Matter?
```

---

## Example — Admin Function

### Observation

```text
POST /api/admin/users/42/disable
```

### Hypothesis

```text
Does the Backend Independently Enforce
Administrative Authorization?
```

---

## Example — Version Difference

### Observation

```text
GET /api/v1/export

GET /api/v2/export
```

### Hypothesis

```text
Do Both Versions Enforce Equivalent
Authentication, Authorization, and Data-Exposure Controls?
```

---

## Example — Nested Object

### Observation

```text
GET /organizations/91/projects/781
```

### Hypothesis

```text
Does the Backend Validate:

User Membership in Organization 91

AND

Authorization to Project 781?
```

---

## Unknown Endpoints

* If an endpoint's purpose is unclear, record:

  ```text
  Unknown Function
  ```

* Do not guess.

* Investigate through:

  ```text
  Baseline Request

  Response

  Related Traffic

  JavaScript

  Documentation

  Application Behavior
  ```

---

## Coverage Tracking

* Maintain coverage by endpoint family.

| Endpoint Family |  Mapped | Auth Reviewed | Authorization Reviewed | Notes           |
| --------------- | :-----: | :-----------: | :--------------------: | --------------- |
| Authentication  |   Yes   |      Yes      |           N/A          | Login/reset     |
| Users           |   Yes   |      Yes      |         Pending        | Multiple roles  |
| Orders          |   Yes   |      Yes      |         Pending        | High priority   |
| Files           | Partial |    Partial    |         Pending        | Upload/download |
| Admin           | Partial |    Pending    |         Pending        | Privileged      |

* This prevents important areas from being forgotten.

---

## Evidence Preservation

* Preserve representative baseline requests for:

  ```text
  Authentication

  Important Objects

  State Changes

  Privileged Functions

  Tenant Operations

  Financial Actions
  ```

* These become starting points for later Repeater testing.

---

## Repeater Preparation

* Send high-value requests to Repeater.

* Organize tabs or groups by:

  ```text
  Authentication

  Users

  Authorization

  Orders

  Files

  Admin

  Business Logic
  ```

* Good organization reduces mistakes during deeper testing.

---

## Avoid Premature Automation

* Do not immediately automate every discovered parameter.

* First determine:

  ```text
  What Does This Endpoint Do?

  What Security Rule Should Apply?

  Which Variable Matters?

  Could Repetition Cause Side Effects?
  ```

* Automation should follow understanding.

---

## Professional Tips

* Generate natural traffic before modifying requests.
* Treat method + path as the meaningful endpoint unit.
* Use HTTP History for workflow context and Site Map for structural coverage.
* Build endpoint families around business objects.
* Record authentication, role, tenant, and object ownership.
* Translate technical requests into business functions.
* Map read and write operations separately.
* Prioritize state-changing and privileged functions.
* Compare JavaScript discoveries against observed traffic.
* Treat documentation as a valuable source, not absolute truth.
* Record candidate endpoints separately from confirmed active endpoints.
* Map multiple API versions.
* Preserve baseline requests for high-value functionality.
* Use controlled identities when mapping role differences.
* Confirm scope before testing newly discovered hosts.
* Convert every high-value endpoint into a security question.
* Update the API map continuously throughout the assessment.

---

## Common Mistakes

* Starting vulnerability testing before building an API inventory.
* Recording paths without HTTP methods.
* Treating every request to the same path as the same security operation.
* Ignoring API hosts separate from the frontend.
* Assuming observed traffic represents the complete API.
* Ignoring JavaScript-only endpoints.
* Assuming every JavaScript endpoint is active.
* Ignoring API documentation.
* Trusting documentation as perfectly complete.
* Failing to record object ownership.
* Failing to map tenant relationships.
* Confusing authentication with authorization.
* Ignoring state-changing functions.
* Testing only GET endpoints.
* Ignoring old API versions.
* Failing to identify nested object relationships.
* Treating identifiers as authorization controls.
* Running automation before understanding side effects.
* Failing to preserve baseline requests.
* Testing newly discovered third-party or out-of-scope hosts.

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

### Task 2 — Generate API Traffic

* Use all major accessible application functions.

---

### Task 3 — Identify API Hosts

* Record:

  ```text
  Host

  Purpose

  Scope Status
  ```

---

### Task 4 — Identify API Base Paths

* Record examples such as:

  ```text
  /api/

  /api/v1/

  /api/v2/

  /graphql
  ```

---

### Task 5 — Build Endpoint Inventory

* Record at least 10 endpoints if the application exposes enough functionality.

* Include:

  ```text
  Method

  Path

  Business Function

  Authentication

  Object

  State Change
  ```

---

### Task 6 — Group Endpoint Families

* Group endpoints into categories such as:

  ```text
  Authentication

  Users

  Files

  Orders

  Administration
  ```

---

### Task 7 — Map Objects

* Identify at least three business objects.

* Record:

  ```text
  Identifier

  Owner

  Tenant

  CRUD Operations
  ```

---

### Task 8 — Map Parameters

* Identify:

  ```text
  IDs

  Role Fields

  Tenant IDs

  Status Fields

  Business Values
  ```

---

### Task 9 — Identify State-Changing Functions

* Record every important:

  ```text
  Create

  Update

  Delete

  Approve

  Transfer

  Upload

  Export
  ```

* Function observed.

---

### Task 10 — Compare Sources

* Compare:

  ```text
  HTTP History

  Site Map

  JavaScript

  Documentation
  ```

* Record endpoints unique to each source.

---

### Task 11 — Identify API Versions

* Record all observed or legitimately referenced versions.

---

### Task 12 — Prioritize

* Select five high-value endpoints.

* Explain why each deserves deeper testing.

---

### Task 13 — Generate Security Hypotheses

* Write at least five explicit questions.

* Example:

  ```text
  Does this endpoint verify object ownership?

  Does this action require a privileged role?

  Can restricted properties be modified?

  Is tenant membership validated?

  Does the legacy version enforce equivalent controls?
  ```

---

### Task 14 — Send Baselines to Repeater

* Preserve representative high-value requests for later lessons.

---

## Investigation Journal

```text
Application:

Date:

Scope:

API Hosts:

API Base Paths:

API Versions:

Authentication Mechanisms:

Roles:

Tenants:

Endpoint Families:

Objects:

Object Identifiers:

Object Ownership:

Parameters:

State-Changing Functions:

Privileged Functions:

Sensitive Data Endpoints:

Financial Functions:

File Functions:

JavaScript-Only Endpoints:

Documented Endpoints:

Undocumented Endpoints:

Legacy Endpoints:

GraphQL:

WebSockets:

High-Priority Endpoints:

Unknown Functions:

Security Hypotheses:

Baseline Requests Saved:

Evidence:

Next Steps:
```

---

## Interview Questions

1. Why should API discovery occur before deep vulnerability testing?
2. What sources can be used to discover API endpoints?
3. Why is observed application traffic not necessarily the complete API attack surface?
4. What information should be recorded for each API endpoint?
5. Why should method and path be considered together?
6. How does Burp HTTP History help with API mapping?
7. How does Target Site Map complement HTTP History?
8. How can JavaScript reveal API endpoints?
9. Why should JavaScript-only endpoints be treated as candidates rather than confirmed active endpoints?
10. How can API responses reveal additional endpoints?
11. How can error messages assist API discovery?
12. What information can API documentation provide?
13. Why should API documentation not be assumed to be complete?
14. What is an OpenAPI specification?
15. What is an endpoint family?
16. Why is CRUD mapping useful?
17. What is object-centric API mapping?
18. Why should object ownership be recorded?
19. Why are unpredictable object identifiers not a replacement for authorization?
20. Where can API parameters appear?
21. What is the difference between a parameter name and its business meaning?
22. Why should authentication requirements be mapped per endpoint?
23. Why should role context be recorded separately from authentication?
24. What is differential role mapping?
25. Why are tenant boundaries important?
26. What are state-changing endpoints?
27. Why is the HTTP method only a clue to whether an endpoint changes state?
28. Why should technical endpoints be translated into business functions?
29. Why are older API versions security-relevant?
30. What are nested resources?
31. Why can nested resources introduce multiple authorization boundaries?
32. What are action endpoints?
33. How would you prioritize a large API attack surface?
34. Why should baseline requests be preserved?
35. Why should automation follow understanding rather than precede it?
36. How would you map an unfamiliar API using Burp Suite?
37. How do you convert an endpoint observation into a security hypothesis?
38. Why should candidate, deprecated, and confirmed endpoints be tracked separately?
39. How can you measure API testing coverage?
40. What information belongs in a professional API inventory?

---

## Quick Revision

* API testing begins with:

  ```text
  Discover

  ↓

  Map

  ↓

  Understand

  ↓

  Prioritize

  ↓

  Test
  ```

* Endpoint identity:

  ```text
  Method

  +

  Path
  ```

* API map:

  ```text
  Endpoint

  +

  Method

  +

  Authentication

  +

  Role

  +

  Tenant

  +

  Object

  +

  Parameters

  +

  Business Function
  ```

* Discovery sources:

  ```text
  HTTP History

  Site Map

  JavaScript

  Documentation

  Responses

  Errors

  Other Authorized Clients
  ```

* Object mapping:

  ```text
  Object

  +

  Identifier

  +

  Owner

  +

  Tenant

  +

  Operations
  ```

* Important endpoint categories:

  ```text
  Authentication

  Authorization

  Admin

  Financial

  Files

  Sensitive Data

  State Changes
  ```

* Important principle:

  ```text
  Identifier

  ≠

  Authorization
  ```

* API version:

  ```text
  New Version

  Does Not Guarantee

  Old Version Is Gone
  ```

* Documentation:

  ```text
  Valuable Source

  ≠

  Complete Truth
  ```

* Candidate endpoint:

  ```text
  Reference Found

  ↓

  Validate Activity

  ↓

  Understand Function

  ↓

  Test If Authorized
  ```

* Professional workflow:

  ```text
  Scope

  ↓

  Natural Traffic

  ↓

  Hosts

  ↓

  Base Paths

  ↓

  Endpoint Families

  ↓

  Methods

  ↓

  Objects

  ↓

  Parameters

  ↓

  Identity Context

  ↓

  Business Functions

  ↓

  Versions

  ↓

  Priority

  ↓

  Security Hypotheses
  ```

---

## Key Takeaways

* API discovery should precede deep vulnerability testing.
* Observed browser traffic rarely guarantees complete API coverage.
* Combine HTTP History, Site Map, JavaScript, documentation, responses, errors, and other authorized clients to improve discovery.
* Treat HTTP method and path together as the meaningful security surface.
* Build endpoint families around business objects and functions.
* Map objects, identifiers, ownership, tenants, and relationships.
* An identifier—predictable or unpredictable—is not a substitute for authorization.
* Record parameters together with their location and business meaning.
* Authentication, role, tenant, and object ownership should be tracked separately.
* State-changing and privileged functions deserve higher testing priority.
* Translate technical API requests into business actions to understand real security impact.
* Track multiple API versions and compare their security behavior during later testing.
* JavaScript-only and documented endpoints should initially be treated as candidates until their current behavior is validated.
* Nested resources may introduce authorization checks at several object levels.
* Preserve representative baseline requests for deeper Repeater testing.
* Avoid broad automation until endpoint behavior and side effects are understood.
* API mapping should continuously evolve as new functionality is discovered.
* Convert important observations into explicit, testable security hypotheses.
* The professional workflow is **discover → inventory → classify → understand → prioritize → test → validate → document**.
