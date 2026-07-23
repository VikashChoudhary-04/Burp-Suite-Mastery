# GraphQL Testing with Burp Suite

> **Module 16 — Modern Application Testing**

> **Progress**

```text
██████████████░ 14 / 15 Files
```

---

## Overview

* GraphQL is an API query language and runtime that allows clients to request precisely the data they need.

* Unlike traditional REST APIs, where applications commonly expose many endpoints:

  ```text
  GET /api/users/731

  GET /api/users/731/orders

  POST /api/orders

  PATCH /api/profile
  ```

* A GraphQL application may expose much of its functionality through a single endpoint:

  ```text
  POST /graphql
  ```

* The client sends structured queries describing:

  ```text
  What Operation to Perform

  Which Object to Access

  Which Fields to Return

  Which Variables to Use
  ```

* This flexibility makes GraphQL powerful, but it also creates important security-testing questions around:

  * Schema exposure.
  * Authentication.
  * Object-level authorization.
  * Field-level authorization.
  * Mutations.
  * Hidden functionality.
  * Introspection.
  * Aliases.
  * Batching.
  * Query depth.
  * Query complexity.
  * Error handling.
  * Information disclosure.
  * Business logic.
  * Denial-of-service protections.

* Burp Suite helps capture GraphQL traffic, modify queries and variables, replay operations, compare responses, and investigate authorization and application logic.

---

## Learning Objectives

* By the end of this lesson, you will be able to:

  * Understand the basic GraphQL architecture.
  * Identify GraphQL endpoints.
  * Understand queries, mutations, fields, arguments, and variables.
  * Capture GraphQL requests in Burp Suite.
  * Establish legitimate GraphQL baselines.
  * Map operations and object identifiers.
  * Understand GraphQL schemas and types.
  * Understand introspection.
  * Test introspection exposure appropriately.
  * Test authentication.
  * Test object-level authorization.
  * Test field-level authorization.
  * Test mutation authorization.
  * Test role and tenant boundaries.
  * Investigate hidden fields and operations.
  * Understand aliases and batching.
  * Test query depth and complexity safely.
  * Analyze error messages.
  * Investigate excessive data exposure.
  * Test business logic through GraphQL.
  * Understand GraphQL-specific rate-limiting considerations.
  * Validate findings without aggressive queries.
  * Build a repeatable GraphQL testing methodology.

---

## What Is GraphQL?

* GraphQL allows a client to describe the exact structure of the data it wants.

* Example query:

```graphql
query {
  user(id: 731) {
    id
    username
    email
  }
}
```

* A possible response:

```json
{
  "data": {
    "user": {
      "id": 731,
      "username": "testuser",
      "email": "test@example.test"
    }
  }
}
```

---

## REST vs GraphQL

### REST

```text
GET /users/731

↓

User Object

GET /users/731/orders

↓

Orders
```

### GraphQL

```text
POST /graphql

↓

Query Defines:

User

+

Orders

+

Requested Fields
```

---

## Important Principle

```text
REST Often Exposes Many Endpoints

GraphQL Often Exposes Many Operations

Through Fewer Endpoints
```

---

## Common GraphQL Endpoints

* Look for paths such as:

  ```text
  /graphql

  /api/graphql

  /graphql/api

  /gql
  ```

* Endpoint naming varies by application.

---

## Finding GraphQL Traffic

* Use:

  ```text
  Proxy

  ↓

  HTTP History

  ↓

  Use Application Normally

  ↓

  Search for GraphQL Requests
  ```

* Look for:

  ```text
  GraphQL Query Syntax

  operationName

  query

  variables
  ```

---

## Example HTTP Request

```http
POST /graphql HTTP/1.1
Host: example.test
Content-Type: application/json
Authorization: Bearer CONTROLLED_TOKEN

{
  "operationName": "GetProfile",
  "variables": {
    "id": 731
  },
  "query": "query GetProfile($id: ID!) { user(id: $id) { id username email } }"
}
```

---

## GraphQL Request Components

* Common components include:

  ```text
  operationName

  query

  variables
  ```

---

## Operation Name

* Example:

```json
{
  "operationName": "GetProfile"
}
```

* This identifies a named operation.

---

## Query

* Example:

```graphql
query GetProfile($id: ID!) {
  user(id: $id) {
    id
    username
    email
  }
}
```

---

## Variables

* Example:

```json
{
  "id": 731
}
```

* Variables separate dynamic values from the query structure.

---

## GraphQL Operations

* Common operation types include:

  ```text
  Query

  Mutation

  Subscription
  ```

---

## Query

* Queries generally retrieve data.

```graphql
query {
  product(id: 1001) {
    name
    price
  }
}
```

---

## Mutation

* Mutations generally change state.

```graphql
mutation {
  updateProfile(name: "Test") {
    id
    name
  }
}
```

---

## Subscription

* Subscriptions provide real-time updates.

* They may operate over WebSockets or another transport.

```graphql
subscription {
  notificationAdded {
    id
    message
  }
}
```

* Subscription security should also consider the WebSocket methodology covered in the previous lesson.

---

## GraphQL Schema

* A GraphQL schema defines:

  ```text
  Types

  Fields

  Queries

  Mutations

  Arguments

  Relationships

  Enums

  Input Types
  ```

---

## Example Schema Concept

```graphql
type User {
  id: ID!
  username: String!
  email: String
}

type Query {
  user(id: ID!): User
}
```

* This tells the client that:

  ```text
  Query:

  user

  Argument:

  id

  Returns:

  User
  ```

---

## GraphQL Type System

* Common types include:

  ```text
  String

  Int

  Float

  Boolean

  ID

  Custom Objects

  Lists

  Enums

  Input Objects
  ```

---

## Non-Null Fields

* The `!` symbol indicates a non-null requirement.

* Example:

```graphql
id: ID!
```

* Meaning:

  ```text
  id Must Not Be Null
  ```

---

## Lists

* Example:

```graphql
users: [User]
```

* This represents a list of `User` objects.

---

## Custom Scalars

* GraphQL applications may define custom scalar types beyond the built-in scalar types.

* Examples may include:

  ```text
  Date

  DateTime

  Email

  URL

  JSON

  Application-Specific Types
  ```

* A declared scalar type does not automatically guarantee that its semantic constraints are securely enforced.
* When testing custom scalars, evaluate whether backend validation matches the intended meaning of the type.
* For example:

  ```text
  Declared Type: Email

  Does the Server Validate:

  Syntax?

  Length?

  Allowed Characters?

  Normalization?

  Business Rules?
  ```

* Treat custom scalar validation separately from authorization.

```text
Valid Input

≠

Authorized Operation
```

---
## GraphQL Test Card

```text
Application:

Endpoint:

Method:

Authentication:

User:

Role:

Tenant:

Operation Name:

Operation Type:

Query / Mutation:

Variables:

Object IDs:

Requested Fields:

Sensitive Fields:

Nested Objects:

Errors:

Introspection Available?:

Batching Supported?:

Aliases Supported?:

Notes:
```

---

## Establish a Baseline

* Before changing a GraphQL request:

  ```text
  Perform Legitimate Action

  ↓

  Capture Request

  ↓

  Send to Repeater

  ↓

  Record Query

  ↓

  Record Variables

  ↓

  Record Response

  ↓

  Verify Final State if Mutation
  ```

---

## Example Baseline

```graphql
query GetOrder($id: ID!) {
  order(id: $id) {
    id
    status
    total
  }
}
```

Variables:

```json
{
  "id": 1001
}
```

* Record:

  ```text
  Current User

  Object Owner

  Expected Access

  Returned Fields
  ```

---

## Build an Operation Inventory

```text
Operation:

Type:

Purpose:

Arguments:

Variables:

Objects:

Sensitive Fields:

Required Role:

Expected Authorization:

State Change?:
```

---

## Example Inventory

```text
GetProfile

GetUser

GetOrder

ListOrders

UpdateProfile

CreateOrder

CancelOrder

DeleteDocument
```

* Actual operations depend on the application.

---

## Introspection

* GraphQL introspection allows clients to query information about the schema itself.

* It can reveal:

  ```text
  Types

  Fields

  Queries

  Mutations

  Arguments

  Enums

  Input Objects
  ```

---

## Simple Introspection Concept

```graphql
query {
  __schema {
    queryType {
      name
    }
  }
}
```

---

## Type Inspection

* A specific type may be queried through introspection.

```graphql
query {
  __type(name: "User") {
    name
    fields {
      name
    }
  }
}
```

---

## Why Introspection Matters

* Introspection can make API mapping significantly easier.

* It may reveal:

  ```text
  Hidden Operations

  Administrative Mutations

  Sensitive Fields

  Internal Object Relationships

  Deprecated Fields
  ```

---

## Important Principle

```text
Introspection Enabled

≠

Automatically a Vulnerability
```

* Many GraphQL systems intentionally expose introspection.

* Security impact depends on:

  ```text
  Environment

  Intended Exposure

  Information Revealed

  Whether Hidden Sensitive Functionality Becomes Meaningfully Exposed
  ```

---

## Introspection Testing

* Determine:

  ```text
  Is Introspection Available?

  Is Authentication Required?

  Does Access Differ by Role?

  Does It Reveal Sensitive Internal Operations?

  Is Exposure Intended?
  ```

---

## Schema Discovery Without Introspection

* Even when introspection is disabled, GraphQL behavior may reveal schema information through:

  ```text
  Existing Frontend Queries

  JavaScript Bundles

  Error Messages

  Network Traffic

  Documentation
  ```

---

## Frontend JavaScript

* Search application JavaScript for:

  ```text
  query

  mutation

  gql

  operationName

  fragment
  ```

* Frontend code may contain legitimate operations used by the application.

---

## GraphQL Errors

* Invalid queries may return useful error information.

* Example:

```json
{
  "errors": [
    {
      "message": "Cannot query field \"adminData\" on type \"Query\"."
    }
  ]
}
```

* Errors may reveal:

  ```text
  Field Names

  Type Names

  Expected Arguments

  Validation Rules

  Internal Paths
  ```

---

## Error Disclosure

* Determine whether errors expose:

  ```text
  Stack Traces

  Database Details

  Internal File Paths

  Resolver Names

  Framework Versions

  Internal Services
  ```

---

## Avoid Aggressive Guessing

* Do not perform high-volume schema brute forcing against production systems unless explicitly authorized.

* Prefer:

  ```text
  Observed Operations

  Introspection if Available

  Frontend Code

  Documentation

  Low-Volume Controlled Tests
  ```

---

## Authentication Testing

* GraphQL authentication may use:

  ```text
  Session Cookies

  Bearer Tokens

  API Keys

  Custom Headers
  ```

* Test the same authentication states used for REST APIs.

---

## Authentication Matrix

```text
Valid Session

No Session

Invalid Session

Expired Session

Logged-Out Session

Lower-Privilege Session
```

---

## Important Principle

```text
Single GraphQL Endpoint

Does Not Mean

Single Authorization Decision
```

* Every sensitive operation and object must enforce authorization.

---

## Object-Level Authorization

* Example:

```graphql
query GetDocument($id: ID!) {
  document(id: $id) {
    id
    title
    content
  }
}
```

Variables:

```json
{
  "id": 1001
}
```

* Security question:

  ```text
  Does the Server Verify That the Current User
  May Access Document 1001?
  ```

---

## IDOR / BOLA Testing

* Use two controlled accounts.

```text
User A

Owns Object A

User B

Owns Object B
```

* Test:

  ```text
  User A + Object A

  ↓

  Baseline Success

  User A + Object B

  ↓

  Compare
  ```

---

## Controlled Variable Change

### Baseline

```json
{
  "id": 1001
}
```

### Test

```json
{
  "id": 1002
}
```

* Keep the query unchanged.

---

## Important Principle

```text
Changing GraphQL Variables

Is Often Cleaner Than

Rewriting the Entire Query
```

* This helps isolate the tested variable.

---

## Nested Object Authorization

* GraphQL makes nested relationships easy to query.

* Example:

```graphql
query {
  user(id: 731) {
    id
    orders {
      id
      total
    }
  }
}
```

* Security question:

  ```text
  Is Authorization Applied to Nested Objects Too?
  ```

---

## Resolver-Level Authorization

* Different GraphQL fields may be handled by different resolver logic.

* Conceptually:

  ```text
  User Resolver

  ↓

  Orders Resolver

  ↓

  Payment Resolver
  ```

* Authorization must remain consistent across the resolver chain.

---

## Field-Level Authorization

* A user may be authorized to access an object but not every field.

* Example:

```graphql
query {
  user(id: 731) {
    id
    username
    email
    internalNotes
  }
}
```

* Security question:

  ```text
  Is the Current User Allowed to Access internalNotes?
  ```

---

## Object vs Field Authorization

```text
Object Authorization:

Can User Access This User Object?

Field Authorization:

Can User Access This Specific Field?
```

* Both matter.

---

## Sensitive Fields

* Examples may include:

  ```text
  Email

  Phone

  Address

  Internal Notes

  Roles

  Permissions

  Billing Data

  Tokens

  Administrative Metadata
  ```

* Whether a field is sensitive depends on application context.

---

## Excessive Data Exposure

* GraphQL clients choose requested fields.

* Test whether sensitive fields can be requested even when the UI does not display them.

---

## Example

### UI Query

```graphql
query {
  user(id: 731) {
    id
    username
  }
}
```

### Controlled Test

```graphql
query {
  user(id: 731) {
    id
    username
    email
  }
}
```

* Determine whether the current role is intended to access `email`.

---

## Important Principle

```text
Hidden From UI

≠

Protected
```

* Security must be enforced server-side.

---

## Mutations

* Mutations modify application state.

* Example:

```graphql
mutation UpdateProfile($name: String!) {
  updateProfile(name: $name) {
    id
    name
  }
}
```

---

## Mutation Security

* Test:

  ```text
  Authentication

  Role Authorization

  Object Ownership

  Field Authorization

  Business Rules

  Input Validation

  Replay

  Final State
  ```

---

## Mutation Authorization

* Example:

```graphql
mutation DeleteDocument($id: ID!) {
  deleteDocument(id: $id) {
    success
  }
}
```

* Security question:

  ```text
  Can the Current User Delete This Object?
  ```

---

## Two-Account Mutation Test

```text
User A Deletes Own Controlled Object

↓

Baseline

User A Attempts User B Controlled Object

↓

Compare

↓

Verify Final State
```

---

## Never Trust Mutation Names

* A mutation named:

  ```text
  adminDeleteUser
  ```

* Is not protected merely because the frontend hides it.

* Server-side authorization must enforce access.

---

## Role-Based Authorization

* Test operations across:

  ```text
  Anonymous

  Normal User

  Moderator

  Administrator
  ```

* Use only controlled accounts.

---

## Role Matrix

| Operation        | User     | Moderator               | Admin    |
| ---------------- | -------- | ----------------------- | -------- |
| View Own Profile | Expected | Expected                | Expected |
| Moderate Content | Denied   | Expected                | Expected |
| Admin Operation  | Denied   | Denied if not permitted | Expected |

---

## Tenant Isolation

* Multi-tenant GraphQL APIs may expose:

  ```text
  tenantId

  organizationId

  workspaceId

  projectId
  ```

* Never assume these identifiers are trusted.

---

## Tenant Test

```text
Tenant A User

↓

Query Tenant A Object

↓

Baseline

Tenant A User

↓

Query Controlled Tenant B Object

↓

Compare
```

---

## Input Objects

* Mutations often use input objects.

* Example:

```graphql
mutation UpdateUser($input: UpdateUserInput!) {
  updateUser(input: $input) {
    id
    username
  }
}
```

Variables:

```json
{
  "input": {
    "username": "test"
  }
}
```

---

## Mass Assignment

* Input objects may contain security-sensitive fields.

* Example controlled test:

```json
{
  "input": {
    "username": "test",
    "role": "admin"
  }
}
```

* Determine whether unauthorized fields are:

  ```text
  Rejected

  Ignored

  Applied
  ```

* Use only controlled accounts.

---

## Important Principle

```text
Field Present in GraphQL Input Type

Does Not Mean

Every User May Set It
```

---

## Nested Input Objects

* Example:

```json
{
  "input": {
    "profile": {
      "name": "Test"
    },
    "permissions": {
      "admin": true
    }
  }
}
```

* Authorization must apply to nested security-sensitive fields as well.

---

## Aliases

* GraphQL aliases allow multiple fields or operations to be named differently in one query.

* Example:

```graphql
query {
  first: user(id: 1001) {
    id
  }

  second: user(id: 1002) {
    id
  }
}
```

---

## Why Aliases Matter

* Aliases may allow multiple resolver calls inside one GraphQL request.

* Security considerations include:

  ```text
  Rate Limiting

  Authorization

  Query Complexity

  Repeated Sensitive Operations
  ```

---

## Important Principle

```text
One HTTP Request

May Trigger

Many GraphQL Resolver Operations
```

---

## Alias-Based Testing

* Use only a small number of aliases.

* Determine whether controls count:

  ```text
  HTTP Requests

  or

  Actual GraphQL Operations / Resolver Work
  ```

---

## Batching

* Some GraphQL implementations allow multiple operations in one HTTP request.

* Conceptually:

```json
[
  {
    "query": "query { ... }"
  },
  {
    "query": "query { ... }"
  }
]
```

* Support depends on implementation.

---

## Batching Security Questions

```text
Is Authentication Applied to Every Operation?

Is Authorization Applied Independently?

Are Rate Limits Counted Correctly?

Are Mixed Allowed and Denied Operations Handled Safely?
```

---

## Avoid High-Volume Batching

* Do not use large batches against production systems.

* Small controlled tests are sufficient to understand behavior.

---

## Query Depth

* GraphQL relationships may allow deeply nested queries.

* Example concept:

```text
User

↓

Friends

↓

Friends

↓

Friends

↓

...
```

* Excessive depth may consume significant server resources.

---

## Query Complexity

* Resource cost depends on more than depth.

* Consider:

  ```text
  Number of Fields

  Nested Lists

  Resolver Cost

  Database Queries

  Aliases

  Pagination Size
  ```

---

## Important Principle

```text
Deep Query

and

Expensive Query

Are Related

But Not Identical
```

---

## Safe Complexity Testing

* Increase complexity gradually.

* Example:

  ```text
  Baseline Query

  ↓

  Add One Nested Relationship

  ↓

  Observe Response

  ↓

  Stop Before Resource Impact
  ```

---

## Do Not Stress Production

* GraphQL complexity testing should not become denial-of-service testing unless explicitly authorized.

```text
Security Validation

≠

Load Testing
```

---

## Query Depth Controls

* Applications may implement:

  ```text
  Maximum Depth

  Complexity Scoring

  Resolver Timeouts

  Pagination Limits

  Rate Limits
  ```
---

## Filtering and Search Controls

* GraphQL operations may expose client-controlled filtering or search arguments.

* Common examples include:

  ```text
  userId

  organizationId

  tenantId

  owner

  status

  search
  ```

* Filters can change which objects are requested, but they must not define which objects the requester is authorized to access.

```text
Client-Supplied Filter

≠

Server-Side Authorization Boundary
```

* Test whether changing filter values can expose:

  * Objects belonging to another user.
  * Objects belonging to another tenant.
  * Hidden or restricted records.
  * Data outside the authenticated user's permitted scope.

* Always distinguish broader query functionality from an actual authorization failure.

---

## Pagination

* GraphQL list queries may accept:

  ```text
  first

  last

  limit

  offset

  after

  before
  ```

* Test safe boundary behavior.

---

## Example

```graphql
query {
  users(first: 20) {
    id
    username
  }
}
```

* Test whether excessive values are constrained without requesting harmful volumes.

---

## Pagination Authorization

* Pagination must not expose unauthorized objects.

* Example:

  ```text
  First Page Authorized

  ↓

  Later Pages Must Apply Same Authorization
  ```

---

## Fragments

* GraphQL fragments allow reusable field selections.

* Example:

```graphql
fragment UserFields on User {
  id
  username
}

query {
  user(id: 731) {
    ...UserFields
  }
}
```

* Fragments do not bypass authorization by themselves.

* Authorization must apply regardless of query syntax.

---

## Inline Fragments

* Interfaces and unions may use inline fragments.

```graphql
query {
  search {
    ... on User {
      id
      username
    }

    ... on Admin {
      id
      permissions
    }
  }
}
```

* Review whether sensitive type-specific fields are properly authorized.

---

## Deprecated Fields

* Introspection may reveal deprecated fields.

* Deprecated does not mean inaccessible.

* Security question:

  ```text
  Does an Older Field Bypass Newer Authorization or Validation?
  ```

---

## Legacy Operations

* Frontend bundles or schemas may reveal older:

  ```text
  Queries

  Mutations

  Fields

  Arguments
  ```

* Test only where in scope.

* Old functionality may have weaker controls than current workflows.

---

## GraphQL Business Logic

* GraphQL can expose the same business-logic weaknesses as REST APIs.

* Examples:

  ```text
  Skipping Workflow Steps

  Replaying Mutations

  Manipulating Prices

  Changing Ownership

  Bypassing Limits

  Invalid State Transitions

  Duplicate Operations
  ```

---

## Example State Transition

```text
Draft

↓

Submitted

↓

Approved
```

* If mutations exist for each state:

  ```text
  submitRequest

  approveRequest
  ```

* Determine whether unauthorized or out-of-order transitions are blocked.

---

## Replay Testing

* Example mutation:

```graphql
mutation {
  claimReward(id: 42) {
    success
  }
}
```

* Controlled test:

  ```text
  Execute Once

  ↓

  Verify State

  ↓

  Replay

  ↓

  Verify State Again
  ```

---

## Important Principle

```text
Successful Mutation Replay

≠

Automatically Vulnerable
```

* Determine whether the action is intended to be one-time.

---

## GraphQL CSRF

* GraphQL endpoints may use:

  ```text
  Cookies

  Bearer Tokens

  Custom Headers
  ```

* CSRF risk depends on authentication and request handling.

---

## GraphQL and CORS

* GraphQL endpoints follow normal browser cross-origin security rules.

* CORS testing should evaluate:

  ```text
  Allowed Origins

  Credential Handling

  Sensitive Responses

  Authentication Context
  ```

* The use of GraphQL does not fundamentally change CORS security principles.

* Evaluate whether cross-origin access allows an untrusted origin to read sensitive GraphQL responses in the victim's authenticated context.

* Do not classify a permissive CORS response as a vulnerability without confirming:

```text
Attacker-Controlled Origin

+

Browser-Readable Sensitive Response

+

Relevant Authentication Context

+

Meaningful Security Impact
```

---
## Content Types

* Some GraphQL servers accept:

  ```text
  application/json

  application/graphql

  form-style requests

  GET queries
  ```

* If cookie authentication is used, investigate whether state-changing operations can be triggered through browser-compatible cross-site requests.

---

## Important Principle

```text
GraphQL

Does Not Eliminate

Traditional Web Security Risks
```

* Authentication method and transport behavior remain important.

---

## GET-Based Queries

* Some GraphQL servers support:

  ```text
  GET /graphql?query=...
  ```

* Sensitive data in URLs may appear in:

  ```text
  Browser History

  Logs

  Analytics

  Referrer Contexts
  ```

* Mutations over GET should be examined carefully if supported.

---

## Rate Limiting

* GraphQL complicates rate limiting because:

  ```text
  One HTTP Request

  May Contain

  Multiple Fields

  Aliases

  Batched Operations

  Expensive Resolvers
  ```

---

## Rate-Limit Questions

```text
Is Limiting Per HTTP Request?

Per Operation?

Per Resolver?

Per Account?

Per IP?

Per Object?

Based on Complexity?
```

---

## Safe Rate-Limit Testing

* Use a small number of controlled requests.

* Avoid:

  ```text
  Large Alias Sets

  Huge Batches

  Deep Recursive Queries

  Excessive Pagination
  ```

---

## Denial-of-Service Considerations

* Potential resource amplification may involve:

  ```text
  Deep Nesting

  Large Lists

  Expensive Resolvers

  Aliases

  Batching

  Recursive Relationships
  ```

* Do not attempt disruptive testing without explicit authorization.

---

## Information Disclosure

* GraphQL may expose sensitive information through:

  ```text
  Introspection

  Errors

  Hidden Fields

  Nested Relationships

  Deprecated Fields

  Debug Extensions
  ```

---

## GraphQL Response Extensions

* Some responses contain:

```json
{
  "data": {},
  "extensions": {
    "debug": {}
  }
}
```

* Review `extensions` for unintended sensitive information.

---

## Partial Responses

* GraphQL can return both:

  ```text
  data

  and

  errors
  ```

* Example:

```json
{
  "data": {
    "user": null
  },
  "errors": [
    {
      "message": "Access denied"
    }
  ]
}
```

* Analyze both sections.

---

## Important Principle

```text
HTTP 200

Does Not Necessarily Mean

GraphQL Operation Succeeded
```

* GraphQL errors may be returned inside a `200 OK` response.

---

## Status Codes

* Do not rely only on:

  ```text
  200

  400

  401

  403
  ```

* Always inspect:

  ```text
  data

  errors

  extensions
  ```

---

## Error Comparison

* Compare responses for:

  ```text
  Existing Authorized Object

  Existing Unauthorized Object

  Nonexistent Object
  ```

* Different responses may reveal object existence.

---

## Object Enumeration

* If responses differ significantly:

  ```text
  Unauthorized Object

  vs

  Nonexistent Object
  ```

* The API may reveal whether an object exists.

* Determine whether this creates meaningful security impact.

---

## Persisted Queries

* Some GraphQL systems use persisted queries.

* Instead of sending the full query every time, the client may send an identifier or hash.

* Conceptually:

```json
{
  "operationName": "GetProfile",
  "extensions": {
    "persistedQuery": {
      "sha256Hash": "..."
    }
  }
}
```

---

## Persisted Query Testing

* Determine:

  ```text
  How Queries Are Registered

  Whether IDs Are Predictable

  Whether Authorization Is Still Enforced

  Whether Old Operations Remain Available
  ```

* Persisted queries are not a substitute for authorization.

---

## GraphQL File Uploads

* Some GraphQL APIs support file uploads through multipart requests or implementation-specific mechanisms.

* Apply the previous file-upload methodology to:

  ```text
  File Validation

  Authorization

  Storage

  Processing

  Retrieval
  ```

---

## GraphQL Subscriptions

* Subscriptions may use WebSockets.

* Test:

  ```text
  Connection Authentication

  Subscription Authorization

  Object Authorization

  Tenant Isolation

  Server-Pushed Data

  Session Invalidation
  ```

* Apply the WebSocket testing methodology where appropriate.

---

## GraphQL Testing Workflow

```text
Define Scope

↓

Identify GraphQL Endpoint

↓

Capture Legitimate Operations

↓

Establish Authentication Context

↓

Build Operation Inventory

↓

Map Queries and Mutations

↓

Map Variables and Object IDs

↓

Review Schema Information

↓

Check Introspection

↓

Review Frontend Operations

↓

Test Authentication

↓

Test Object-Level Authorization

↓

Test Field-Level Authorization

↓

Test Mutation Authorization

↓

Test Roles and Tenants

↓

Test Input Objects

↓

Test Hidden / Deprecated Fields

↓

Review Aliases and Batching

↓

Test Replay and Business Logic

↓

Review Query Depth / Complexity Safely

↓

Review Errors and Information Exposure

↓

Verify Final State

↓

Validate Impact

↓

Document Evidence
```

---

## Step 1 — Identify the Endpoint

* Record:

  ```text
  URL

  Method

  Content-Type

  Authentication
  ```

---

## Step 2 — Capture Legitimate Queries

* Use application features normally.

* Send requests to Repeater.

---

## Step 3 — Build an Operation Inventory

* Record:

  ```text
  Queries

  Mutations

  Subscriptions
  ```

---

## Step 4 — Map Variables

* Identify:

  ```text
  User IDs

  Object IDs

  Tenant IDs

  Status Values

  Input Objects
  ```

---

## Step 5 — Review Schema Exposure

* Use:

  ```text
  Introspection if Available

  Frontend JavaScript

  Existing Requests

  Errors

  Documentation
  ```

---

## Step 6 — Test Authentication

* Compare authentication states.

---

## Step 7 — Test Object Authorization

* Use controlled cross-user objects.

---

## Step 8 — Test Field Authorization

* Request additional fields one at a time.

---

## Step 9 — Test Mutations

* Verify:

  ```text
  Role

  Ownership

  Tenant

  Business Rules

  Final State
  ```

---

## Step 10 — Test Input Objects

* Review security-sensitive optional fields.

---

## Step 11 — Test Nested Objects

* Verify authorization at every relationship level.

---

## Step 12 — Review Aliases and Batching

* Use small controlled tests only.

---

## Step 13 — Test Business Logic

* Review:

  ```text
  Replay

  Sequence

  Limits

  State Transitions
  ```

---

## Step 14 — Review Complexity Controls

* Increase complexity minimally and safely.

---

## Step 15 — Analyze Errors

* Inspect:

  ```text
  data

  errors

  extensions
  ```

---

## Step 16 — Verify Final State

* Especially after mutations.

---

## Example — Object Authorization

### Baseline Query

```graphql
query GetInvoice($id: ID!) {
  invoice(id: $id) {
    id
    total
    status
  }
}
```

### Baseline Variables

```json
{
  "id": 1001
}
```

* `1001` belongs to User A.

### Controlled Test

```json
{
  "id": 1002
}
```

* `1002` belongs to controlled User B.

### Expected

```text
Denied

or

No Unauthorized Sensitive Data Returned
```

---

## Example — Field Authorization

### Baseline

```graphql
query {
  user(id: 731) {
    id
    username
  }
}
```

### Controlled Test

```graphql
query {
  user(id: 731) {
    id
    username
    internalNotes
  }
}
```

* Determine whether the current role should access `internalNotes`.

---

## Example — Mutation Authorization

```graphql
mutation DeleteDocument($id: ID!) {
  deleteDocument(id: $id) {
    success
  }
}
```

* Test only controlled objects.

```text
Own Object

↓

Baseline

Other Controlled User's Object

↓

Compare

↓

Verify Final State
```

---

## Example — Input Object

### Baseline Variables

```json
{
  "input": {
    "displayName": "Test"
  }
}
```

### Controlled Test

```json
{
  "input": {
    "displayName": "Test",
    "role": "admin"
  }
}
```

* Determine whether unauthorized fields are ignored, rejected, or applied.

---

## Example — Alias Behavior

```graphql
query {
  one: product(id: 1001) {
    id
  }

  two: product(id: 1002) {
    id
  }
}
```

* Determine whether each resolver independently enforces authorization.

---

## Example — Introspection

* Observation:

  ```text
  Introspection Enabled
  ```

* Do not immediately report.

* Investigate:

  ```text
  Is Exposure Intended?

  Does It Reveal Sensitive Hidden Operations?

  Does It Meaningfully Increase Attack Surface?

  Are Those Operations Still Properly Authorized?
  ```

---

## Evidence Requirements

* Strong GraphQL evidence should include:

  ```text
  Endpoint

  User / Role

  Tenant

  Operation

  Operation Type

  Baseline Query

  Baseline Variables

  Modified Query / Variables

  Changed Field

  Response Data

  Errors

  Final State

  Expected Authorization

  Actual Authorization

  Reproduction Steps

  Security Impact
  ```

---

## Strong Object Authorization Evidence

```text
User A

↓

Queries Own Object A

↓

Baseline Success

↓

Changes Only Object ID

↓

Queries Controlled Object B

↓

Unauthorized Data Returned

↓

Repeat and Verify
```

---

## Strong Mutation Evidence

```text
Low-Privilege User

↓

Captures Restricted Mutation

↓

Executes Against Controlled Object

↓

Server Accepts

↓

Unauthorized State Change Confirmed

↓

Final State Verified
```

---

## Anomaly vs Vulnerability

* Anomaly:

  ```text
  Introspection Enabled
  ```

* Vulnerability:

  ```text
  Sensitive Schema Information Is Exposed

  +

  Exposure Creates Demonstrable Security Impact
  ```

* Anomaly:

  ```text
  Hidden Field Can Be Requested
  ```

* Vulnerability:

  ```text
  Unauthorized Sensitive Field Data Is Returned
  ```

---

## False Positive Causes

* Common causes include:

  ```text
  Public GraphQL Schema

  Intentionally Public Introspection

  Public Objects

  Shared Tenant Resources

  Fields Already Authorized for Current Role

  Aliases Processed With Correct Authorization

  Replayable Idempotent Operations

  Expected Partial Responses

  Generic Error Handling

  Persisted Queries With Proper Authorization
  ```

---

## False Positive Control

```text
Understand Intended Access

↓

Establish Legitimate Baseline

↓

Change One Variable or Field

↓

Compare

↓

Use Controlled Accounts

↓

Verify Final State

↓

Repeat

↓

Demonstrate Meaningful Impact
```

---

## GraphQL Testing Checklist

### Discovery

* Identify:

  ```text
  GraphQL Endpoint

  Queries

  Mutations

  Subscriptions

  Authentication
  ```

---

### Schema

* Review:

  ```text
  Introspection

  Types

  Fields

  Arguments

  Input Objects

  Deprecated Fields
  ```

---

### Authentication

* Test:

  ```text
  Valid

  Missing

  Invalid

  Expired

  Lower Privilege
  ```

---

### Authorization

* Test:

  ```text
  Objects

  Nested Objects

  Fields

  Mutations

  Roles

  Tenants
  ```

---

### Input

* Review:

  ```text
  Variables

  Input Objects

  Extra Fields

  Types

  Null Values

  Boundary Values
  ```

---

### Query Behavior

* Review safely:

  ```text
  Aliases

  Batching

  Fragments

  Depth

  Complexity

  Pagination
  ```

---

### Business Logic

* Test:

  ```text
  Replay

  Sequence

  State Changes

  One-Time Actions

  Limits
  ```

---

### Information Exposure

* Review:

  ```text
  Errors

  Extensions

  Hidden Fields

  Schema Data

  Object Existence
  ```

---

### Final Verification

* Verify:

  ```text
  Backend State

  Object Ownership

  Role

  Tenant

  Reproducibility
  ```

---

## GraphQL Investigation Journal

```text
Application:

Date:

Scope:

GraphQL Endpoint:

Method:

Content-Type:

Authentication:

User:

Role:

Tenant:

Queries:

Mutations:

Subscriptions:

Operation Names:

Variables:

Object IDs:

Tenant IDs:

Input Objects:

Sensitive Fields:

Nested Objects:

Introspection:

Schema Types:

Hidden Fields:

Deprecated Fields:

Frontend Operations:

Authentication Tests:

Object Authorization Tests:

Nested Authorization Tests:

Field Authorization Tests:

Mutation Authorization Tests:

Role Tests:

Tenant Tests:

Mass Assignment Tests:

Alias Tests:

Batching Tests:

Replay Tests:

Sequence Tests:

Pagination Tests:

Depth Observations:

Complexity Observations:

Rate-Limit Observations:

Error Messages:

Response Extensions:

Information Disclosure:

Persisted Queries:

Validated Findings:

False Positives:

Evidence:

Next Steps:
```

---

## Professional Tips

* Treat GraphQL as an API architecture, not as a vulnerability by itself.
* Start with legitimate application-generated queries.
* Capture GraphQL requests in Burp and send them to Repeater.
* Build an operation inventory before testing aggressively.
* Record queries, mutations, variables, object IDs, and requested fields separately.
* Change variables instead of rewriting entire queries when testing object authorization.
* Use two controlled accounts for IDOR/BOLA testing.
* Test nested object authorization, not just top-level objects.
* Distinguish object-level authorization from field-level authorization.
* Request additional fields one at a time.
* Never assume a field hidden by the UI is protected.
* Treat mutations as state-changing API operations requiring independent authorization.
* Verify final application state after mutations.
* Test role and tenant boundaries explicitly.
* Review input objects for unauthorized security-sensitive fields.
* Remember that fields present in an input schema may still require role-specific authorization.
* Treat introspection as an information source, not automatically as a vulnerability.
* Review frontend JavaScript and observed operations when introspection is disabled.
* Inspect GraphQL errors carefully, but avoid aggressive schema brute forcing.
* Remember that `HTTP 200` does not necessarily mean the GraphQL operation succeeded.
* Always inspect `data`, `errors`, and `extensions`.
* Test aliases and batching with only small controlled examples.
* Remember that one HTTP request may trigger many resolver operations.
* Test pagination boundaries without requesting excessive datasets.
* Increase query depth or complexity gradually and stop before resource impact.
* Do not turn complexity testing into denial-of-service testing.
* Review deprecated and legacy operations for inconsistent authorization.
* Test replay and workflow sequencing for state-changing mutations.
* Apply traditional CSRF analysis when cookie-authenticated GraphQL endpoints support browser-compatible state-changing requests.
* Apply WebSocket security methodology to GraphQL subscriptions.
* Apply file-upload methodology when GraphQL supports file operations.
* Report demonstrable authorization or data-exposure impact rather than merely unusual GraphQL behavior.

---

## Common Mistakes

* Treating GraphQL itself as a vulnerability.
* Searching only for `/graphql` and missing custom endpoints.
* Ignoring application-generated queries.
* Modifying several query components simultaneously.
* Ignoring variables.
* Testing only top-level objects.
* Ignoring nested authorization.
* Confusing object authorization with field authorization.
* Assuming hidden UI fields are protected.
* Ignoring mutation authorization.
* Failing to verify final state after mutations.
* Ignoring role boundaries.
* Ignoring tenant isolation.
* Ignoring security-sensitive fields inside input objects.
* Reporting introspection as a vulnerability without impact.
* Performing aggressive schema brute forcing.
* Ignoring frontend JavaScript as a source of operations.
* Ignoring deprecated fields.
* Ignoring legacy mutations.
* Assuming HTTP `200` means success.
* Ignoring the `errors` array.
* Ignoring response `extensions`.
* Sending large alias-based queries.
* Sending huge batches.
* Creating deeply nested queries against production.
* Confusing complexity testing with load testing.
* Ignoring pagination limits.
* Ignoring replay behavior.
* Ignoring business-logic sequencing.
* Ignoring traditional CSRF considerations.
* Ignoring WebSocket security for subscriptions.
* Reporting anomalies without meaningful impact.

---

## Practice Tasks

### Task 1 — Select an Authorized GraphQL Application

* Use:

  ```text
  Deliberately Vulnerable Lab

  Local Application

  System You Own

  or

  Explicitly Authorized Target
  ```

---

### Task 2 — Identify the GraphQL Endpoint

* Record:

  ```text
  URL

  Method

  Authentication

  Content-Type
  ```

---

### Task 3 — Capture a Legitimate Query

* Send it to Repeater.

* Record:

  ```text
  operationName

  query

  variables
  ```

---

### Task 4 — Build an Operation Inventory

* Identify:

  ```text
  Queries

  Mutations

  Subscriptions
  ```

---

### Task 5 — Review Introspection

* Determine whether it is available.

* Do not treat availability alone as a vulnerability.

---

### Task 6 — Test Object Authorization

* Use two controlled accounts and objects.

* Change only the relevant object ID.

---

### Task 7 — Test Field Authorization

* Add one potentially sensitive field to an authorized object query.

* Compare behavior with intended permissions.

---

### Task 8 — Test a Controlled Mutation

* Perform a legitimate mutation.

* Record final state.

* Modify one authorization-relevant variable.

---

### Task 9 — Review Input Objects

* Identify optional security-sensitive fields.

* Test only on controlled accounts.

---

### Task 10 — Test Nested Authorization

* Query one legitimate nested relationship.

* Verify authorization applies to nested objects.

---

### Task 11 — Test Aliases Safely

* Use two small aliases.

* Observe authorization and rate-limit behavior.

---

### Task 12 — Review Error Handling

* Send one harmless invalid field or argument.

* Record information disclosed.

---

### Task 13 — Review Complexity Controls

* Add only one additional nested level.

* Observe behavior without stressing the application.

---

### Task 14 — Build a GraphQL Security Matrix

* Document:

  ```text
  Authentication

  Object Authorization

  Field Authorization

  Mutation Authorization

  Roles

  Tenants

  Input Validation

  Business Logic

  Complexity

  Information Exposure
  ```

---

## Interview Questions

1. What is GraphQL?
2. How does GraphQL differ from REST?
3. What is a GraphQL schema?
4. What are queries, mutations, and subscriptions?
5. What is `operationName`?
6. What are GraphQL variables?
7. Why are variables useful during security testing?
8. What is a GraphQL resolver?
9. What is GraphQL introspection?
10. Why is enabled introspection not automatically a vulnerability?
11. How can you discover GraphQL operations when introspection is disabled?
12. How can Burp Suite help test GraphQL APIs?
13. How do you establish a GraphQL testing baseline?
14. What is object-level authorization in GraphQL?
15. How can IDOR/BOLA occur in GraphQL?
16. How would you test object authorization using two controlled accounts?
17. Why must nested GraphQL objects be authorization-tested?
18. What is field-level authorization?
19. How does field-level authorization differ from object-level authorization?
20. What is excessive data exposure in GraphQL?
21. Why does hiding a field in the frontend not protect it?
22. What security controls should be applied to GraphQL mutations?
23. How would you test mutation authorization?
24. Why should final state be verified after a mutation?
25. How can role-based authorization fail in GraphQL?
26. How can tenant-isolation flaws occur?
27. What are GraphQL input objects?
28. How can mass assignment occur through GraphQL input objects?
29. Why does a field being present in an input type not mean every user can modify it?
30. What are GraphQL aliases?
31. Why can aliases affect rate limiting and resource consumption?
32. What is GraphQL batching?
33. What security issues can arise from batching?
34. What is query depth?
35. What is query complexity?
36. Why are query depth and query complexity not identical?
37. How should complexity testing be performed safely?
38. What role does pagination play in GraphQL security?
39. What are GraphQL fragments?
40. Why should deprecated fields and legacy operations be reviewed?
41. How can business-logic vulnerabilities occur through GraphQL mutations?
42. How does CSRF apply to GraphQL?
43. Why can GET-based GraphQL requests create additional considerations?
44. Why does HTTP `200 OK` not necessarily mean a GraphQL operation succeeded?
45. What information should be inspected in a GraphQL response?
46. How can GraphQL errors disclose sensitive information?
47. What are persisted queries?
48. How should GraphQL subscriptions be security-tested?
49. What evidence is required for a strong GraphQL authorization finding?
50. Describe your complete methodology for testing GraphQL APIs using Burp Suite.

---

## Quick Revision

* GraphQL:

  ```text
  Client Defines

  What Data / Operation It Wants

  ↓

  GraphQL Endpoint

  ↓

  Schema + Resolvers

  ↓

  Response
  ```

* Main operations:

  ```text
  Query

  Mutation

  Subscription
  ```

* Request components:

  ```text
  operationName

  query

  variables
  ```

* Security layers:

  ```text
  Authentication

  Object Authorization

  Field Authorization

  Mutation Authorization

  Role / Tenant Authorization
  ```

* Important:

  ```text
  Hidden From UI

  ≠

  Protected
  ```

* Also:

  ```text
  Introspection Enabled

  ≠

  Automatically Vulnerable
  ```

* Object testing:

  ```text
  User A + Object A

  ↓

  Baseline

  User A + Controlled Object B

  ↓

  Compare
  ```

* Field testing:

  ```text
  Authorized Object

  ↓

  Add Sensitive Field

  ↓

  Verify Field-Level Authorization
  ```

* Mutation testing:

  ```text
  Baseline Mutation

  ↓

  Change One Authorization-Relevant Variable

  ↓

  Send

  ↓

  Verify Final State
  ```

* GraphQL response:

  ```text
  HTTP Status

  +

  data

  +

  errors

  +

  extensions
  ```

* Important:

  ```text
  HTTP 200

  ≠

  Operation Necessarily Succeeded
  ```

* Complexity:

  ```text
  Depth

  +

  Fields

  +

  Lists

  +

  Aliases

  +

  Resolver Cost
  ```

* Safety:

  ```text
  Complexity Testing

  ≠

  Stress Testing
  ```

* Professional workflow:

  ```text
  Identify Endpoint

  ↓

  Capture Legitimate Operations

  ↓

  Map Schema / Operations

  ↓

  Establish Baselines

  ↓

  Test Authentication

  ↓

  Test Object Authorization

  ↓

  Test Field Authorization

  ↓

  Test Mutations

  ↓

  Test Roles / Tenants / Inputs

  ↓

  Review Aliases / Batching / Complexity

  ↓

  Test Business Logic

  ↓

  Analyze Errors

  ↓

  Verify Final State

  ↓

  Validate Impact
  ```

---

## Key Takeaways

* GraphQL allows clients to request specific data and invoke operations through a structured schema, often using a small number of HTTP endpoints.
* Security testing should focus on GraphQL operations, objects, fields, resolvers, variables, and business rules rather than treating the endpoint as a single security boundary.
* Burp Suite can capture GraphQL requests, modify queries and variables, replay operations, and compare responses.
* Begin with legitimate application-generated GraphQL operations and establish baselines before modifying traffic.
* GraphQL introspection can reveal schema information, but enabled introspection alone is not automatically a vulnerability.
* When introspection is unavailable, operations can often be mapped through frontend JavaScript, observed network traffic, documentation, and controlled error analysis.
* Authentication at the GraphQL endpoint does not replace authorization for individual objects, fields, mutations, roles, and tenants.
* Object-level authorization must be tested using controlled cross-user objects, preferably by changing only the relevant GraphQL variable.
* Nested objects require independent authorization because access to a parent object should not automatically expose every related object.
* Field-level authorization is distinct from object-level authorization; users may legitimately access an object while remaining unauthorized to view particular sensitive fields.
* Fields hidden by the frontend are not protected unless the server independently enforces access.
* Mutations require authorization, input validation, business-rule enforcement, and final-state verification.
* Input objects should be reviewed for security-sensitive fields that could enable mass assignment or privilege changes.
* Aliases and batching can cause one HTTP request to trigger multiple GraphQL operations, affecting rate limiting, authorization, and resource controls.
* Query depth alone does not determine resource cost; field count, nested lists, aliases, pagination, and resolver complexity also matter.
* Complexity testing should be gradual and low impact and must not become denial-of-service or load testing without explicit authorization.
* Deprecated fields and legacy operations may remain accessible and should enforce the same modern security controls as current functionality.
* GraphQL remains vulnerable to traditional API and web security issues, including IDOR/BOLA, broken access control, mass assignment, CSRF, business-logic flaws, race conditions, and information disclosure.
* GraphQL responses must be evaluated using `data`, `errors`, and `extensions`; an HTTP `200 OK` response does not necessarily mean the requested operation succeeded.
* GraphQL subscriptions often introduce WebSocket security considerations, while GraphQL file operations require the same file-handling analysis as other upload mechanisms.
* Strong GraphQL findings demonstrate a clear violation of intended authentication, authorization, privacy, or business rules rather than merely documenting unusual schema behavior.
* The professional GraphQL testing workflow is **identify the GraphQL endpoint → capture legitimate queries and mutations → map operations, variables, objects, and schema information → establish baselines → test authentication → test object and nested-object authorization → test field-level authorization → test mutations, roles, tenants, and input objects → review aliases, batching, pagination, and complexity safely → test business logic → analyze errors and information exposure → verify final state → validate impact → document evidence**.
