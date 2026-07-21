# GraphQL Testing with Burp Suite

> **Module 16 — Modern Application Testing**

> **Progress**

```text
██████░░░░░░░░░ 6 / 15 Files
```

---

## Overview

* GraphQL is an API query language and runtime commonly used by modern web and mobile applications.

* Unlike many REST APIs, where different resources are exposed through multiple endpoints, GraphQL commonly exposes a smaller number of endpoints through which clients request specific data and operations.

* A common GraphQL endpoint may look like:

  ```text
  /graphql
  ```

* Requests may contain:

  ```text
  Queries

  Mutations

  Variables

  Operation Names

  Fragments
  ```

* Example:

  ```graphql
  query GetUser($id: ID!) {
    user(id: $id) {
      id
      name
      email
    }
  }
  ```

* GraphQL changes the shape of API testing.

* Instead of mapping only:

  ```text
  Method

  +

  Endpoint
  ```

* You often need to map:

  ```text
  Endpoint

  +

  Operation

  +

  Field

  +

  Argument

  +

  Object

  +

  Resolver

  +

  Authorization Context
  ```

* Burp Suite allows you to capture, replay, modify, and compare GraphQL requests just like other HTTP traffic.

---

## Learning Objectives

* By the end of this lesson, you will be able to:

  * Understand the basic GraphQL request model.
  * Distinguish queries, mutations, subscriptions, fields, arguments, and variables.
  * Identify GraphQL traffic using Burp Suite.
  * Recognize common GraphQL request formats.
  * Map GraphQL operations and objects.
  * Understand GraphQL schemas and introspection.
  * Analyze authorization at object and field level.
  * Test GraphQL variables systematically.
  * Identify hidden or privileged operations.
  * Understand aliases and batching from a security perspective.
  * Recognize query-depth and complexity concerns.
  * Analyze error messages without overinterpreting them.
  * Test mutations and state changes carefully.
  * Build a repeatable GraphQL testing workflow.

---

## What Is GraphQL?

* GraphQL allows clients to request specific fields from an API.

* Example:

  ```graphql
  query {
    currentUser {
      id
      name
    }
  }
  ```

* The client specifies exactly which fields it wants returned.

---

## REST vs GraphQL

* REST-style example:

  ```text
  GET /api/users/42
  ```

* GraphQL example:

  ```graphql
  query {
    user(id: 42) {
      id
      name
    }
  }
  ```

* REST often distributes functionality across multiple paths.

* GraphQL often centralizes many operations behind one endpoint.

---

## Important Difference

```text
REST Mapping

Method + Path
```

* Compared with:

  ```text
  GraphQL Mapping

  Endpoint + Operation + Field + Arguments
  ```

* Therefore, seeing only:

  ```text
  POST /graphql
  ```

* Tells you very little about the complete attack surface.

---

## Common GraphQL Endpoint Paths

* GraphQL may be exposed through paths such as:

  ```text
  /graphql

  /api/graphql

  /gql

  /query
  ```

* These are common patterns, not guarantees.

---

## GraphQL Operations

* The primary GraphQL operation types are:

  ```text
  Query

  Mutation

  Subscription
  ```

---

## Queries

* Queries generally retrieve data.

* Example:

  ```graphql
  query GetProfile {
    currentUser {
      id
      name
      email
    }
  }
  ```

* Conceptually:

  ```text
  Query

  →

  Read Data
  ```

---

## Mutations

* Mutations generally change server-side state.

* Example:

  ```graphql
  mutation UpdateProfile($name: String!) {
    updateProfile(name: $name) {
      id
      name
    }
  }
  ```

* Conceptually:

  ```text
  Mutation

  →

  State Change
  ```

* Mutations deserve careful testing because they may:

  ```text
  Create

  Update

  Delete

  Approve

  Transfer

  Invite

  Reset

  Trigger Business Actions
  ```

---

## Subscriptions

* Subscriptions support real-time updates.

* They may use:

  ```text
  WebSockets

  Persistent Connections
  ```

* Example use cases:

  ```text
  Chat

  Notifications

  Live Dashboards

  Status Updates
  ```

* Subscription testing may therefore overlap with WebSocket security testing.

---

## Fields

* GraphQL objects contain fields.

* Example:

  ```graphql
  user {
    id
    name
    email
    role
  }
  ```

* Fields may have different sensitivity levels.

* Example:

  ```text
  name

  vs

  email

  vs

  role

  vs

  internalNotes
  ```

---

## Arguments

* Fields may accept arguments.

* Example:

  ```graphql
  user(id: 42) {
    id
    name
  }
  ```

* Here:

  ```text
  Argument:

  id

  Value:

  42
  ```

* Arguments often become important authorization and validation test points.

---

## Variables

* GraphQL commonly separates queries from input values.

* Example query:

  ```graphql
  query GetUser($id: ID!) {
    user(id: $id) {
      id
      name
    }
  }
  ```

* Variables:

  ```json
  {
    "id": "42"
  }
  ```

* Burp Repeater makes controlled variable modification straightforward.

---

## Operation Names

* GraphQL operations may be named.

* Example:

  ```graphql
  query GetUser {
    ...
  }
  ```

* Operation name:

  ```text
  GetUser
  ```

* Useful operation names may reveal business functionality.

---

## Fragments

* Fragments allow reusable groups of fields.

* Example:

  ```graphql
  fragment UserFields on User {
    id
    name
    email
  }
  ```

* They can make requests more complex but do not fundamentally change the authorization questions.

---

## Typical GraphQL HTTP Request

```http
POST /graphql HTTP/1.1
Host: api.example.test
Authorization: Bearer <token>
Content-Type: application/json

{
  "operationName": "GetUser",
  "query": "query GetUser($id: ID!) { user(id: $id) { id name email } }",
  "variables": {
    "id": "42"
  }
}
```

* Important components:

  ```text
  Endpoint

  Authentication

  Operation Name

  Query

  Variables
  ```

---

## GraphQL Over GET

* Some GraphQL queries may be sent through GET requests.

* Example conceptually:

  ```text
  GET /graphql?query=...
  ```

* Do not assume GraphQL always uses POST.

---

## Finding GraphQL Traffic in Burp

* Use:

  ```text
  Proxy

  →

  HTTP History
  ```

* Look for:

  ```text
  /graphql

  /gql

  GraphQL Query Syntax

  operationName

  variables

  application/json
  ```

---

## JavaScript Discovery

* Search JavaScript for:

  ```text
  graphql

  mutation

  query

  operationName

  Apollo

  Relay
  ```

* JavaScript may reveal:

  ```text
  GraphQL Endpoint

  Operation Names

  Query Documents

  Mutations

  Fields

  Variables
  ```

---

## GraphQL Mapping Principle

* Do not record only:

  ```text
  POST /graphql
  ```

* Record:

  ```text
  Operation

  Object

  Fields

  Arguments

  Variables

  Authentication

  Role

  State Change
  ```

---

## Operation Inventory

| Operation       | Type     | Object | State Change | Priority |
| --------------- | -------- | ------ | :----------: | :------: |
| `GetProfile`    | Query    | User   |      No      |  Medium  |
| `GetOrder`      | Query    | Order  |      No      |   High   |
| `UpdateProfile` | Mutation | User   |      Yes     |   High   |
| `RefundOrder`   | Mutation | Order  |      Yes     | Critical |

---

## GraphQL Schema

* A GraphQL schema defines available:

  ```text
  Types

  Fields

  Queries

  Mutations

  Arguments

  Relationships
  ```

* Conceptual example:

  ```graphql
  type User {
    id: ID!
    name: String!
    email: String!
  }

  type Query {
    user(id: ID!): User
  }
  ```

* Understanding the schema can greatly improve attack-surface mapping.

---

## Introspection

* GraphQL supports a feature called **introspection**.

* Introspection allows clients to query information about the schema.

* It may reveal:

  ```text
  Types

  Fields

  Queries

  Mutations

  Arguments

  Input Types
  ```

---

## Security Meaning of Introspection

* Introspection being enabled is not automatically a vulnerability.

* It may:

  ```text
  Increase Attack-Surface Visibility
  ```

* But a reportable security issue requires meaningful impact in the specific context.

---

## Important Principle

```text
Introspection Enabled

≠

Automatic Vulnerability
```

* Treat schema visibility as reconnaissance unless additional impact exists.

---

## When Introspection Is Disabled

* You may still discover GraphQL functionality through:

  ```text
  Normal Traffic

  JavaScript

  Error Messages

  Documentation

  Observed Operation Names
  ```

* Disabled introspection does not mean the API is undiscoverable.

---

## Error-Based Schema Clues

* GraphQL errors may reveal:

  ```text
  Unknown Fields

  Expected Types

  Valid Arguments

  Suggested Field Names
  ```

* Example conceptually:

  ```text
  Cannot query field "username" on type "User".
  ```

* Such errors can help understand schema behavior.

---

## Error Messages Are Reconnaissance

* Do not treat verbose GraphQL errors as automatically vulnerable.

* Ask:

  ```text
  What Sensitive Information Is Actually Exposed?

  Does It Enable Meaningful Attack-Surface Discovery?

  Is There Demonstrable Impact?
  ```

---

## Baseline GraphQL Request

* Preserve an original request before modifying it.

* Record:

  ```text
  Operation

  Variables

  Authentication

  Role

  Tenant

  Returned Fields

  Status

  Response Body

  Errors
  ```

---

## Send to Repeater

* Use:

  ```text
  Right-Click Request

  →

  Send to Repeater
  ```

* Repeater allows you to modify:

  ```text
  Variables

  Arguments

  Fields

  Operation

  Authentication

  Headers
  ```

---

## Change One Variable at a Time

* Avoid simultaneously changing:

  ```text
  User ID

  +

  Authentication Token

  +

  Requested Fields

  +

  Operation
  ```

* Prefer controlled experiments.

---

## Object-Level Authorization

* Example:

  ```graphql
  query GetOrder($id: ID!) {
    order(id: $id) {
      id
      total
      status
    }
  }
  ```

* Variables:

  ```json
  {
    "id": "481"
  }
  ```

* Security question:

  ```text
  Does the Server Verify That the Authenticated User
  Is Authorized to Access Order 481?
  ```

---

## Controlled Authorization Testing

* With two authorized test accounts:

  ```text
  User A

  User B
  ```

* Establish:

  ```text
  User A Object

  User B Object
  ```

* Then compare expected and actual access.

---

## Authorization Matrix

| Identity | Object        | Expected | Actual |
| -------- | ------------- | -------- | ------ |
| User A   | User A object | Allow    | ?      |
| User A   | User B object | Deny     | ?      |
| User B   | User B object | Allow    | ?      |
| User B   | User A object | Deny     | ?      |

---

## Field-Level Authorization

* GraphQL clients can request specific fields.

* Example:

  ```graphql
  query {
    currentUser {
      id
      name
      email
      role
    }
  }
  ```

* Security question:

  ```text
  Are Sensitive Fields Protected Independently?
  ```

---

## Example

* Normal client requests:

  ```graphql
  currentUser {
    id
    name
  }
  ```

* Schema or JavaScript indicates additional fields:

  ```text
  email

  role

  internalNotes
  ```

* A controlled test may determine whether the authenticated identity is authorized to retrieve those fields.

---

## Important Distinction

```text
Field Exists

≠

Field Is Authorized
```

* But also:

  ```text
  Field Returned

  ≠

  Automatically Vulnerable
  ```

* Determine whether the information is actually sensitive and unauthorized.

---

## Nested Authorization

* GraphQL queries can retrieve related objects.

* Example:

  ```graphql
  query {
    organization(id: 91) {
      id
      projects {
        id
        files {
          id
          name
        }
      }
    }
  }
  ```

* Authorization may need to apply at:

  ```text
  Organization

  Project

  File
  ```

---

## Nested Authorization Principle

```text
Authorized Parent

≠

Automatic Authorization to Every Child Object
```

* Each sensitive relationship should be evaluated.

---

## Mutation Authorization

* Example:

  ```graphql
  mutation UpdateUser($id: ID!, $name: String!) {
    updateUser(id: $id, name: $name) {
      id
      name
    }
  }
  ```

* Security questions:

  ```text
  Can the User Modify Only Their Own Object?

  Is Role Authorization Enforced?

  Are Tenant Boundaries Enforced?

  Are Restricted Properties Protected?
  ```

---

## Mutation Safety

* Mutations can create real side effects.

* Before replaying:

  ```text
  Understand the Mutation

  ↓

  Use Controlled Object

  ↓

  Record Initial State

  ↓

  Send Once

  ↓

  Verify Final State
  ```

---

## Input Objects

* Mutations often use structured input objects.

* Example:

  ```graphql
  mutation UpdateProfile($input: UpdateProfileInput!) {
    updateProfile(input: $input) {
      id
      name
    }
  }
  ```

* Variables:

  ```json
  {
    "input": {
      "name": "Alice"
    }
  }
  ```

---

## Property-Level Authorization

* Input types may contain multiple properties.

* Example conceptual fields:

  ```text
  name

  email

  role

  organizationId

  status
  ```

* Security question:

  ```text
  Which Properties Is This Identity Authorized to Modify?
  ```

---

## Input Object Testing

```text
Capture Valid Mutation

↓

Identify Expected Properties

↓

Identify Known Additional Properties

↓

Modify One Property

↓

Send Request

↓

Retrieve Object Again

↓

Verify Persisted State
```

---

## Mass Assignment Concepts

* Similar to REST APIs, GraphQL mutations may pass structured objects into backend update logic.

* Risk exists if sensitive properties are accepted without proper authorization.

* Potential fields:

  ```text
  role

  permissions

  ownerId

  organizationId

  accountStatus
  ```

* Validate actual persistence and impact before concluding.

---

## Variable Validation

* Variables can be tested for:

  ```text
  Missing Values

  Null

  Empty Strings

  Boundary Values

  Unexpected Types

  Invalid Enumerations

  Unexpected IDs
  ```

* GraphQL's type system may reject some invalid values before resolver execution.

---

## GraphQL Type System

* Example:

  ```graphql
  $id: ID!
  ```

* The `!` indicates a non-null value.

* Types may include:

  ```text
  String

  Int

  Float

  Boolean

  ID

  Enum

  Input Object

  Custom Scalar
  ```

---

## Type Validation Is Not Business Validation

* A value can be valid according to the GraphQL schema while still violating a business rule.

* Example:

  ```text
  quantity: Int!
  ```

* Allows an integer structurally.

* But the business rule may require:

  ```text
  1 ≤ quantity ≤ 10
  ```

* Both layers matter.

---

## Custom Scalars

* Applications may define custom scalar types.

* Examples:

  ```text
  Date

  DateTime

  Email

  URL

  JSON
  ```

* Test whether backend validation matches the intended semantic meaning.

---

## Enumerations

* Example:

  ```graphql
  enum Role {
    USER
    MANAGER
    ADMIN
  }
  ```

* The existence of privileged enum values may reveal application roles.

* It does not mean users can assign those values.

---

## Enumeration Principle

```text
Privileged Value Exists

≠

User Can Set Privileged Value
```

* Test authorization separately.

---

## Aliases

* GraphQL aliases allow multiple fields to be requested under different names.

* Example:

  ```graphql
  query {
    first: user(id: 1) {
      id
    }

    second: user(id: 2) {
      id
    }
  }
  ```

* Aliases can cause multiple resolver operations within one request.

---

## Security Relevance of Aliases

* Aliases may affect:

  ```text
  Rate Limiting

  Request Counting

  Resource Consumption

  Bulk Object Access
  ```

* Security testing should determine whether controls apply:

  ```text
  Per HTTP Request

  or

  Per Underlying Operation
  ```

---

## Do Not Abuse Production Systems

* Large alias-based requests can consume significant resources.

* Use:

  ```text
  Small Controlled Tests

  Authorized Labs

  Explicit Testing Limits
  ```

* Avoid denial-of-service behavior.

---

## Batching

* Some GraphQL implementations support multiple operations in a single HTTP request.

* Conceptually:

  ```text
  One HTTP Request

  ↓

  Multiple GraphQL Operations
  ```

* Security relevance may include:

  ```text
  Rate-Limit Enforcement

  Authentication Consistency

  Authorization Per Operation

  Resource Consumption
  ```

---

## Batch Authorization Principle

```text
One Authorized Operation

≠

Every Batched Operation Is Authorized
```

* Each operation must be independently protected.

---

## Query Depth

* GraphQL supports nested queries.

* Example:

  ```text
  User

  ↓

  Organization

  ↓

  Projects

  ↓

  Files

  ↓

  Owners
  ```

* Excessive nesting can increase server processing cost.

---

## Query Complexity

* Complexity depends on more than depth.

* A shallow query returning large collections may also be expensive.

* Controls may include:

  ```text
  Depth Limits

  Complexity Limits

  Timeouts

  Pagination

  Rate Limits
  ```

---

## Safe Complexity Testing

* Do not attempt to exhaust resources.

* Instead:

  ```text
  Observe Existing Limits

  Use Small Incremental Changes

  Measure Responses

  Stop Before Operational Impact
  ```

* Availability testing requires explicit authorization and care.

---

## Pagination

* GraphQL collections may use:

  ```text
  first

  last

  after

  before

  cursor
  ```

* Example:

  ```graphql
  users(first: 20) {
    edges {
      node {
        id
      }
    }
  }
  ```

* Test whether authorization remains consistent across pagination.

---

## Filtering

* GraphQL may support filters such as:

  ```text
  userId

  organizationId

  status

  owner

  search
  ```

* Client-supplied filters must not substitute for server-side authorization.

---

## Search Operations

* Search queries may expose objects that direct navigation does not.

* Compare:

  ```text
  Search Results

  vs

  Direct Object Access
  ```

* Look for inconsistent authorization.

---

## Hidden Operations

* Operations may be discoverable through:

  ```text
  Schema

  JavaScript

  Documentation

  Error Messages

  Other Authorized Roles
  ```

* Examples:

  ```text
  adminUsers

  exportReports

  resetUserPassword

  disableAccount
  ```

* Discovery does not imply authorization bypass.

---

## Privileged Operation Testing

* With controlled roles:

  ```text
  Standard User

  Privileged User
  ```

* Compare whether backend authorization is enforced independently.

---

## Authentication Testing

* GraphQL authentication is usually handled at the HTTP or transport layer.

* Examples:

  ```text
  Bearer Token

  Session Cookie

  API Key
  ```

* Controlled tests may examine:

  ```text
  Missing Authentication

  Invalid Authentication

  Expired Authentication

  Credential Replacement
  ```

---

## GraphQL Errors

* GraphQL responses may return:

  ```http
  HTTP/1.1 200 OK
  ```

* Even when the operation contains errors.

* Example:

  ```json
  {
    "errors": [
      {
        "message": "Access denied"
      }
    ],
    "data": {
      "user": null
    }
  }
  ```

---

## Important Principle

```text
HTTP 200

≠

GraphQL Operation Succeeded
```

* Always inspect:

  ```text
  data

  errors

  extensions

  Returned Objects
  ```

---

## Partial Responses

* GraphQL may return both:

  ```text
  Data

  +

  Errors
  ```

* Example:

  ```json
  {
    "data": {
      "user": {
        "name": "Alice",
        "email": null
      }
    },
    "errors": [
      {
        "message": "Not authorized to access email"
      }
    ]
  }
  ```

* Evaluate field-level behavior carefully.

---

## Error Leakage

* Errors may expose:

  ```text
  Stack Traces

  Internal Paths

  Database Details

  Resolver Names

  Framework Details
  ```

* Determine whether the exposed information creates meaningful security impact.

---

## GraphQL GET and Caching

* Queries sent through GET may interact with:

  ```text
  Browser History

  Proxy Logs

  Caches

  URLs
  ```

* Sensitive query variables should be evaluated in context.

* Do not assume GET usage alone is a vulnerability.

---

## Persisted Queries

* Some applications use persisted queries.

* Instead of sending the complete query each time, the client may send:

  ```text
  Query Identifier

  Hash

  Variables
  ```

* This can make raw traffic look different from traditional GraphQL requests.

---

## Persisted Query Mapping

* Record:

  ```text
  Identifier

  Operation Name

  Variables

  Observed Function
  ```

* JavaScript may help correlate identifiers with operations.

---

## GraphQL and CSRF

* Whether GraphQL operations are exposed to CSRF depends on:

  ```text
  Authentication Mechanism

  Cookie Behavior

  Content Types Accepted

  CSRF Protections

  Browser Request Constraints
  ```

* Do not assume all GraphQL APIs are or are not vulnerable.

---

## GraphQL and CORS

* GraphQL follows normal browser cross-origin rules.

* CORS testing should evaluate:

  ```text
  Allowed Origins

  Credentials

  Sensitive Responses

  Authentication Context
  ```

* The fact that the endpoint is GraphQL does not fundamentally change CORS principles.

---

## GraphQL and Rate Limiting

* Rate limits may need to consider:

  ```text
  HTTP Requests

  Operations

  Aliases

  Batched Queries

  Resolver Cost
  ```

* A control based only on request count may behave differently when one request contains multiple operations.

---

## GraphQL Attack-Surface Map

```text
GraphQL Endpoint

↓

Queries

├── Users
├── Orders
├── Files
└── Search

↓

Mutations

├── Update Profile
├── Create Order
├── Delete File
└── Admin Actions

↓

Subscriptions

├── Notifications
└── Messages
```

* Build this map from observed evidence.

---

## GraphQL Operation Card

```text
Endpoint:

Operation Name:

Operation Type:

Object:

Fields:

Arguments:

Variables:

Authentication:

Role:

Tenant:

Object Owner:

State Changing?:

Sensitive Fields:

Related Objects:

Observed Source:

Expected Security Rule:

Priority:

Security Hypotheses:

Evidence:

Notes:
```

---

## GraphQL Testing Workflow

```text
Define Scope

↓

Identify GraphQL Endpoint

↓

Capture Legitimate Operations

↓

Map Queries / Mutations / Subscriptions

↓

Identify Objects and Fields

↓

Identify Arguments and Variables

↓

Map Authentication

↓

Map Roles and Tenants

↓

Establish Baseline

↓

Send to Repeater

↓

Define Security Hypothesis

↓

Modify One Variable

↓

Compare data + errors

↓

Verify Server State

↓

Validate Authorization / Business Rules

↓

Document Evidence
```

---

## Step 1 — Identify Endpoint

* Use:

  ```text
  HTTP History

  Site Map

  JavaScript

  Documentation
  ```

---

## Step 2 — Capture Natural Operations

* Exercise normal application functionality.

* Record operation names and business purposes.

---

## Step 3 — Separate Operation Types

* Classify:

  ```text
  Query

  Mutation

  Subscription
  ```

---

## Step 4 — Map Objects

* Identify:

  ```text
  User

  Order

  Project

  File

  Organization
  ```

---

## Step 5 — Map Fields

* Mark:

  ```text
  Public

  Personal

  Sensitive

  Privileged

  Unknown
  ```

* Based on application context.

---

## Step 6 — Map Variables

* Record:

  ```text
  IDs

  Tenant IDs

  Status

  Roles

  Search Inputs

  Business Values
  ```

---

## Step 7 — Map Identity Context

* Record:

  ```text
  User

  Role

  Tenant

  Authentication
  ```

---

## Step 8 — Preserve Baselines

* Save representative requests in Repeater.

---

## Step 9 — Test Authorization

* Test separately:

  ```text
  Object-Level

  Field-Level

  Function-Level

  Property-Level

  Tenant-Level
  ```

---

## Step 10 — Test Validation

* Evaluate:

  ```text
  Missing

  Null

  Boundary

  Type

  Enumeration

  Business Constraints
  ```

---

## Step 11 — Analyze GraphQL-Specific Behavior

* Where relevant, evaluate:

  ```text
  Introspection

  Aliases

  Batching

  Query Depth

  Complexity

  Persisted Queries
  ```

---

## Step 12 — Validate Findings

* Confirm:

  ```text
  Reproducibility

  Authorization Context

  Returned Data

  Persisted State

  Security Impact
  ```

---

## Example — Object Authorization

### Baseline

```graphql
query GetProject($id: ID!) {
  project(id: $id) {
    id
    name
  }
}
```

* Variables:

  ```json
  {
    "id": "781"
  }
  ```

* Project belongs to controlled User A.

### Hypothesis

```text
Does the Resolver Verify That the Authenticated User
Is Authorized to Access the Requested Project?
```

### Controlled Test

* Use a project belonging to controlled User B.

* Keep authentication unchanged.

* Modify only:

  ```text
  id
  ```

* Compare results.

---

## Example — Sensitive Field

* Normal query:

  ```graphql
  query {
    currentUser {
      id
      name
    }
  }
  ```

* Known schema contains:

  ```text
  email

  phone

  role
  ```

* Security question:

  ```text
  Which Additional Fields Is This Identity Authorized to Read?
  ```

* Request only fields appropriate for the authorized test.

---

## Example — Mutation Property

### Baseline

```graphql
mutation UpdateProfile($input: UpdateProfileInput!) {
  updateProfile(input: $input) {
    id
    name
  }
}
```

* Variables:

  ```json
  {
    "input": {
      "name": "Alice"
    }
  }
  ```

### Hypothesis

```text
Does the Backend Restrict Sensitive Properties
That Should Not Be User-Modifiable?
```

* Validate using controlled test data and verify persistence afterward.

---

## Example — Privileged Mutation

* Discovered operation:

  ```text
  DisableUser
  ```

* Expected:

  ```text
  Administrative Role Required
  ```

* Test matrix:

| Identity | Role     | Expected |
| -------- | -------- | -------- |
| User A   | Standard | Deny     |
| Manager  | Manager  | Depends  |
| Admin    | Admin    | Allow    |

* Compare actual backend enforcement.

---

## Example — Nested Authorization

* Query:

  ```graphql
  query {
    organization(id: 91) {
      projects {
        files {
          id
          name
        }
      }
    }
  }
  ```

* Questions:

  ```text
  Is Organization Access Authorized?

  Are Projects Independently Authorized?

  Are Files Independently Authorized?

  Are Sensitive Fields Restricted?
  ```

---

## Example — Alias Behavior

* Baseline:

  ```graphql
  query {
    first: user(id: 1) {
      id
    }
  }
  ```

* A small controlled alias test may determine whether multiple underlying operations are counted separately by relevant controls.

* Do not create large resource-intensive requests.

---

## Evidence Requirements

* Strong GraphQL evidence should include:

  ```text
  Endpoint

  Operation

  Variables

  Authentication Context

  Role

  Tenant

  Object Ownership

  Original Query

  Modified Query

  Original Response

  Modified Response

  data

  errors

  Verified State

  Security Impact
  ```

---

## Anomaly vs Finding

* Example anomaly:

  ```text
  Unexpected GraphQL Error
  ```

* Confirmed finding:

  ```text
  Reproducible Security Rule Violation

  +

  Unauthorized Data / Action

  +

  Demonstrable Impact
  ```

---

## False Positive Control

* Before concluding:

  ```text
  Restore Baseline

  ↓

  Repeat Test

  ↓

  Confirm Identity

  ↓

  Confirm Object Ownership

  ↓

  Confirm Fields Returned

  ↓

  Verify Persisted State

  ↓

  Eliminate Client / Cache Artifacts
  ```

---

## GraphQL Testing Checklist

### Discovery

* Identify:

  ```text
  Endpoint

  Queries

  Mutations

  Subscriptions

  Operation Names

  Schema Sources
  ```

---

### Authentication

* Review:

  ```text
  Missing Credentials

  Invalid Credentials

  Expired Credentials

  Controlled Credential Replacement
  ```

---

### Object Authorization

* Review:

  ```text
  Own Object

  Other Controlled User Object

  Same-Tenant Object

  Cross-Tenant Object
  ```

---

### Field Authorization

* Review:

  ```text
  Public Fields

  Sensitive Fields

  Privileged Fields

  Nested Fields
  ```

---

### Mutation Authorization

* Review:

  ```text
  Own Object Modification

  Other Object Modification

  Privileged Mutation

  Tenant Boundary

  Restricted Properties
  ```

---

### Validation

* Review:

  ```text
  Null

  Missing

  Boundary

  Type

  Enum

  Custom Scalar

  Business Rule
  ```

---

### GraphQL-Specific Features

* Review where relevant:

  ```text
  Introspection

  Aliases

  Batching

  Depth

  Complexity

  Pagination

  Persisted Queries
  ```

---

## Investigation Journal

```text
Application:

Date:

Scope:

GraphQL Endpoint:

Authentication:

Users:

Roles:

Tenants:

Queries:

Mutations:

Subscriptions:

Objects:

Sensitive Fields:

Arguments:

Variables:

Input Types:

Enums:

Custom Scalars:

Introspection:

JavaScript Operations:

Documented Operations:

Hidden Operations:

Object Authorization:

Field Authorization:

Function Authorization:

Property Authorization:

Tenant Authorization:

Pagination:

Aliases:

Batching:

Depth Controls:

Complexity Controls:

Persisted Queries:

Error Behavior:

High-Priority Operations:

Baseline Requests Saved:

Security Hypotheses:

Validated Findings:

False Positives:

Evidence:

Next Steps:
```

---

## Professional Tips

* Do not treat `/graphql` as a single API operation; map the operations behind it.
* Preserve legitimate GraphQL requests before modification.
* Use operation names to organize Repeater tabs.
* Separate queries, mutations, and subscriptions.
* Map objects, fields, arguments, variables, roles, and tenants.
* Test object-level and field-level authorization independently.
* Treat nested relationships as potential independent authorization boundaries.
* Verify mutation results by checking server state.
* Remember that GraphQL may return `200 OK` even when an operation fails.
* Inspect both `data` and `errors`.
* Treat introspection as reconnaissance unless meaningful impact is demonstrated.
* Use JavaScript and normal traffic when introspection is unavailable.
* Treat schema-discovered privileged fields as hypotheses, not vulnerabilities.
* Distinguish GraphQL type validation from business-rule validation.
* Test aliases and batching only with small controlled requests.
* Avoid resource-exhaustion testing unless explicitly authorized.
* Record persisted-query identifiers and correlate them with observed functions.
* Compare role behavior using controlled identities.
* Preserve exact query and variable evidence for validated findings.

---

## Common Mistakes

* Treating GraphQL like a REST API with only one endpoint.
* Recording only `/graphql` without operation names.
* Ignoring variables.
* Ignoring fields because the normal UI does not request them.
* Assuming introspection is automatically a vulnerability.
* Assuming disabled introspection prevents attack-surface discovery.
* Treating every schema field as sensitive.
* Treating every privileged enum value as assignable.
* Testing only object-level authorization and ignoring field-level authorization.
* Ignoring nested object authorization.
* Replaying mutations without understanding side effects.
* Trusting `200 OK` as proof of successful execution.
* Ignoring GraphQL `errors`.
* Ignoring partial responses.
* Confusing GraphQL type validation with business validation.
* Sending extremely deep queries without authorization.
* Using large alias or batch requests that risk operational impact.
* Assuming one authorized operation means all batched operations are authorized.
* Ignoring pagination and filters.
* Reporting verbose errors without demonstrating meaningful exposure.
* Failing to verify mutation persistence.
* Changing several variables simultaneously.
* Failing to document role, tenant, and object ownership.

---

## Practice Tasks

### Task 1 — Select an Authorized GraphQL Application

* Use:

  ```text
  Deliberately Vulnerable Lab

  System You Own

  or

  Explicitly Authorized Target
  ```

---

### Task 2 — Identify GraphQL Traffic

* Use:

  ```text
  Proxy → HTTP History

  Target → Site Map
  ```

* Record the GraphQL endpoint.

---

### Task 3 — Capture Operations

* Record at least:

  ```text
  Queries

  Mutations

  Operation Names
  ```

* If available.

---

### Task 4 — Build an Operation Inventory

* For each operation, record:

  ```text
  Type

  Object

  Fields

  Variables

  Authentication

  State Change
  ```

---

### Task 5 — Send Baselines to Repeater

* Preserve representative GraphQL requests.

---

### Task 6 — Map Objects and Fields

* Identify at least three objects if available.

* Mark potentially sensitive fields.

---

### Task 7 — Test Controlled Object Authorization

* Using controlled identities and objects:

  ```text
  Own Object

  vs

  Other Controlled User Object
  ```

* Modify only the relevant identifier.

---

### Task 8 — Review Field Authorization

* Compare fields requested by the normal client with other legitimately discovered schema fields.

* Test only within authorized scope.

---

### Task 9 — Analyze a Mutation

* Record:

  ```text
  Initial State

  Mutation

  Variables

  Response

  Final State
  ```

---

### Task 10 — Review Input Validation

* Test one safe variable using appropriate:

  ```text
  Missing

  Null

  Boundary

  Invalid Enum
  ```

* Cases.

---

### Task 11 — Review Introspection

* Determine whether schema introspection is available.

* Record its security relevance without assuming it is a vulnerability.

---

### Task 12 — Review Nested Relationships

* Identify one nested query.

* Document each authorization boundary.

---

### Task 13 — Review Pagination

* Determine how collections are paginated.

* Verify authorization consistency.

---

### Task 14 — Generate Security Hypotheses

* Write at least five questions such as:

  ```text
  Does this resolver verify object ownership?

  Is this sensitive field independently authorized?

  Can this mutation modify another user's object?

  Are restricted input properties protected?

  Does nested access enforce authorization at every level?
  ```

---

## Interview Questions

1. What is GraphQL?
2. How does GraphQL differ from a typical REST API?
3. What are queries, mutations, and subscriptions?
4. What is a GraphQL field?
5. What is a GraphQL argument?
6. What are GraphQL variables?
7. What is an operation name?
8. What are fragments?
9. How is a typical GraphQL request transported over HTTP?
10. Can GraphQL use GET as well as POST?
11. How do you identify GraphQL traffic in Burp Suite?
12. Why is recording only `/graphql` insufficient for attack-surface mapping?
13. What information should be recorded for each GraphQL operation?
14. What is a GraphQL schema?
15. What is introspection?
16. Is enabled introspection automatically a vulnerability?
17. How can GraphQL functionality be discovered when introspection is disabled?
18. How can JavaScript assist GraphQL discovery?
19. What is object-level authorization in GraphQL?
20. How would you test object authorization safely?
21. What is field-level authorization?
22. Why can nested GraphQL queries create multiple authorization boundaries?
23. How should mutation authorization be tested?
24. Why should mutation results be verified through server state?
25. What is property-level authorization in GraphQL input objects?
26. How can mass-assignment-style issues occur in GraphQL?
27. What does the GraphQL type system validate?
28. Why is type validation different from business-rule validation?
29. What are custom scalars?
30. What are enums, and why can they be useful during security mapping?
31. What are GraphQL aliases?
32. How can aliases affect rate limiting or resource usage?
33. What is GraphQL batching?
34. Why must authorization apply independently to batched operations?
35. What is query depth?
36. What is query complexity?
37. How should complexity testing be performed safely?
38. How does pagination commonly work in GraphQL?
39. Why should client-supplied filters not replace authorization?
40. Why can GraphQL return HTTP `200` when an operation fails?
41. What are partial GraphQL responses?
42. What information can GraphQL errors reveal?
43. What are persisted queries?
44. How can persisted queries affect attack-surface mapping?
45. How would you systematically test an unfamiliar GraphQL API using Burp Suite?

---

## Quick Revision

* GraphQL:

  ```text
  Endpoint

  +

  Operations

  +

  Schema

  +

  Objects

  +

  Fields
  ```

* Operations:

  ```text
  Query

  →

  Read
  ```

  ```text
  Mutation

  →

  State Change
  ```

  ```text
  Subscription

  →

  Real-Time Updates
  ```

* Mapping:

  ```text
  Endpoint

  +

  Operation

  +

  Object

  +

  Fields

  +

  Arguments

  +

  Variables

  +

  Authorization
  ```

* Authorization levels:

  ```text
  Object

  Field

  Function

  Property

  Tenant
  ```

* Important rule:

  ```text
  Authorized Parent

  ≠

  Authorized Child
  ```

* Introspection:

  ```text
  Schema Visibility

  ≠

  Automatic Vulnerability
  ```

* GraphQL response:

  ```text
  HTTP 200

  ≠

  Successful Operation
  ```

* Always inspect:

  ```text
  data

  +

  errors
  ```

* Type validation:

  ```text
  Valid GraphQL Type

  ≠

  Valid Business Value
  ```

* Mutations:

  ```text
  Initial State

  ↓

  Controlled Mutation

  ↓

  Final State
  ```

* GraphQL-specific review:

  ```text
  Introspection

  Aliases

  Batching

  Depth

  Complexity

  Pagination

  Persisted Queries
  ```

* Workflow:

  ```text
  Discover

  ↓

  Map Operations

  ↓

  Map Objects / Fields

  ↓

  Baseline

  ↓

  Hypothesis

  ↓

  Modify One Variable

  ↓

  Compare Data + Errors

  ↓

  Verify State

  ↓

  Validate Impact
  ```

---

## Key Takeaways

* GraphQL often exposes many operations through a small number of HTTP endpoints, so endpoint-only mapping is insufficient.
* Map operation types, operation names, objects, fields, arguments, variables, roles, tenants, and business functions.
* Queries usually retrieve data, mutations usually change state, and subscriptions provide real-time functionality.
* GraphQL schemas can reveal substantial attack-surface information.
* Enabled introspection is useful for reconnaissance but is not automatically a vulnerability.
* JavaScript, normal traffic, documentation, and error behavior can reveal operations even when introspection is unavailable.
* Object-level, field-level, function-level, property-level, and tenant-level authorization should be evaluated independently.
* Authorization to a parent object does not automatically imply authorization to every nested child object.
* GraphQL's type system provides structural validation but does not replace business-rule validation.
* Mutation testing requires controlled objects, careful replay, and verification of final server state.
* A GraphQL response may return HTTP `200 OK` while still containing operation errors.
* Always inspect both `data` and `errors`, including partial responses.
* Aliases and batching can change how many backend operations occur within a single HTTP request.
* Query-depth and complexity testing must avoid creating denial-of-service conditions.
* Pagination, filtering, search, and nested relationships can expose authorization inconsistencies.
* Persisted queries may hide full query documents from normal traffic but can still be mapped through identifiers, variables, JavaScript, and observed behavior.
* GraphQL-specific features do not replace the core methodology of establishing baselines and changing one variable at a time.
* A valid finding requires reproducible unauthorized access, action, information exposure, or another meaningful security impact.
* The professional GraphQL workflow is **discover → map operations → understand schema and objects → establish baselines → define security rules → modify one variable → compare data and errors → verify state → validate impact → document evidence**.
