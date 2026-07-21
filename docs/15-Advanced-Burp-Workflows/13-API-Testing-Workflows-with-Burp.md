# API Testing Workflows with Burp

> **Module 15 — Advanced Burp Workflows**

> **Progress**

```text
██████████████░ 14 / 15 Files
```

---

## Overview

* Modern web applications increasingly rely on APIs for communication between:

  * Web frontends.
  * Mobile applications.
  * Backend services.
  * Microservices.
  * Third-party integrations.
  * Partner platforms.

* A typical application may work like:

  ```text
  Browser / Mobile App

  ↓

  API Gateway

  ↓

  Authentication Layer

  ↓

  Application API

  ↓

  Database / Internal Services
  ```

* Instead of receiving complete HTML pages, clients frequently exchange structured data such as:

  ```text
  JSON

  XML

  GraphQL

  Form Data

  Multipart Data
  ```

* APIs expose application functionality directly through endpoints.

* This makes them especially important during security assessments.

* Common API vulnerabilities include:

  * Broken Object Level Authorization.
  * Broken Function Level Authorization.
  * Broken authentication.
  * Excessive data exposure.
  * Mass assignment.
  * Improper property-level authorization.
  * Unrestricted resource consumption.
  * Business logic flaws.
  * Rate-limit weaknesses.
  * API version exposure.
  * Hidden endpoint exposure.
  * Server-side request forgery.
  * Injection vulnerabilities.
  * GraphQL authorization weaknesses.

* Burp Suite provides an effective environment for API testing because it allows you to:

  ```text
  Discover

  Capture

  Modify

  Replay

  Compare

  Automate Selected Tests

  Analyze Responses
  ```

* A professional API workflow is:

  ```text
  Discover API

  +

  Map Endpoints

  +

  Understand Authentication

  +

  Identify Objects and Parameters

  +

  Build Role Matrix

  +

  Test Authorization

  +

  Test Input Handling

  +

  Test Business Logic

  +

  Analyze Responses

  +

  Validate Impact
  ```

---

## Learning Objectives

* By the end of this lesson, you will be able to:

  * Identify API traffic using Burp Suite.
  * Map REST-style API endpoints.
  * Understand API methods and resources.
  * Identify authentication mechanisms.
  * Build an API endpoint inventory.
  * Identify object identifiers.
  * Test object-level authorization.
  * Test function-level authorization.
  * Investigate property-level authorization.
  * Detect mass-assignment behavior.
  * Analyze excessive data exposure.
  * Discover hidden API parameters.
  * Investigate undocumented endpoints.
  * Test alternate API versions.
  * Analyze HTTP method handling.
  * Test rate limiting safely.
  * Investigate API business logic.
  * Understand GraphQL testing fundamentals.
  * Analyze API error handling.
  * Use Burp tools efficiently for API testing.
  * Build a structured API security testing methodology.

---

## What Is an API?

* API stands for:

  ```text
  Application Programming Interface
  ```

* An API allows software components to communicate using defined requests and responses.

* Example:

  ```http
  GET /api/users/42 HTTP/1.1
  Host: example.test
  Authorization: Bearer TOKEN
  ```

* Response:

  ```json
  {
    "id": 42,
    "username": "alice"
  }
  ```

* The client does not need to know how the server internally retrieves the user.

* It only needs to understand the API contract.

---

## Why APIs Matter in Security Testing

* APIs often expose direct access to:

  ```text
  User Accounts

  Financial Operations

  Orders

  Files

  Messages

  Administrative Functions

  Internal Objects

  Business Workflows
  ```

* A frontend may hide certain functionality.

* But the underlying API may still expose it.

* Therefore:

  ```text
  UI Restrictions

  ≠

  API Security Controls
  ```

---

## REST APIs

* Many APIs follow REST-style conventions.

* Example:

  ```text
  GET    /api/users

  GET    /api/users/42

  POST   /api/users

  PATCH  /api/users/42

  DELETE /api/users/42
  ```

* Common interpretation:

  ```text
  GET

  → Read
  ```

  ```text
  POST

  → Create / Action
  ```

  ```text
  PUT / PATCH

  → Update
  ```

  ```text
  DELETE

  → Delete
  ```

* Actual behavior depends on the application.

---

## Resources and Objects

* APIs commonly expose resources such as:

  ```text
  /users

  /orders

  /payments

  /messages

  /documents

  /projects
  ```

* Individual objects may be referenced through identifiers:

  ```text
  /api/orders/1042
  ```

* Or parameters:

  ```text
  /api/order?id=1042
  ```

* Or JSON:

  ```json
  {
    "order_id": 1042
  }
  ```

---

## API Attack Surface

* Map:

  ```text
  Hosts

  Endpoints

  Methods

  Parameters

  Headers

  Authentication

  Objects

  Roles

  Versions

  Content Types

  Error Responses
  ```

* Do not test endpoints individually without understanding how they relate.

---

## Identifying API Traffic in Burp

* Browse the application normally.

* In:

  ```text
  Proxy → HTTP history
  ```

* Look for paths such as:

  ```text
  /api/

  /v1/

  /v2/

  /graphql

  /rest/

  /services/
  ```

* Also inspect:

  ```text
  Content-Type: application/json
  ```

* And:

  ```text
  Accept: application/json
  ```

---

## API Discovery Sources

* API endpoints may be discovered through:

  ```text
  Proxy History

  Target Site Map

  JavaScript Files

  Mobile Application Traffic

  API Documentation

  OpenAPI Specifications

  Swagger UI

  GraphQL Endpoints

  Error Messages

  Historical API Versions
  ```

---

## JavaScript as an API Map

* Modern frontend JavaScript often contains:

  ```text
  Endpoint Paths

  API Versions

  Parameter Names

  Feature Flags

  GraphQL Operations

  Internal Route Names
  ```

* Example:

  ```javascript
  fetch("/api/v2/account/profile")
  ```

* This may reveal endpoints not reached during normal browsing.

---

## API Documentation

* Look for authorized documentation such as:

  ```text
  Swagger

  OpenAPI

  API Reference

  Developer Portal

  GraphQL Schema Documentation
  ```

* Documentation may reveal:

  ```text
  Hidden Endpoints

  Optional Parameters

  Administrative Functions

  Object Schemas

  Authentication Requirements
  ```

---

## Build an Endpoint Inventory

* Record:

  ```text
  Method:

  Endpoint:

  Authentication:

  Role:

  Parameters:

  Object IDs:

  Content Type:

  Response:

  Purpose:
  ```

* Example:

  ```text
  GET /api/v1/users/me

  Auth: Bearer Token

  Role: User

  Purpose: Current profile
  ```

---

## API Endpoint Table

| Method | Endpoint           | Auth   | Object       | Purpose      |
| ------ | ------------------ | ------ | ------------ | ------------ |
| GET    | `/api/users/me`    | Bearer | Current user | Profile      |
| GET    | `/api/orders/{id}` | Bearer | Order        | View order   |
| PATCH  | `/api/users/{id}`  | Bearer | User         | Update user  |
| POST   | `/api/orders`      | Bearer | Order        | Create order |
| DELETE | `/api/orders/{id}` | Bearer | Order        | Delete order |

---

## Establish an API Baseline

* For each important endpoint:

  ```text
  Capture Valid Request

  ↓

  Send to Repeater

  ↓

  Confirm Expected Response

  ↓

  Record Identity and Role

  ↓

  Modify One Variable
  ```

* Preserve the original request.

---

## API Authentication

* Common mechanisms include:

  ```text
  Session Cookies

  Bearer Tokens

  JWTs

  API Keys

  OAuth Access Tokens

  Client Certificates

  Custom Authentication Headers
  ```

* Determine exactly which credential controls access.

---

## Authentication Removal Test

* Start with:

  ```text
  Valid Authenticated Request
  ```

* Remove:

  ```text
  Cookie

  or

  Authorization Header
  ```

* Send again.

* Expected:

  ```text
  401

  403

  or

  Equivalent Rejection
  ```

* If sensitive data remains accessible, investigate further.

---

## Authentication Replacement Test

* With two controlled users:

  ```text
  User A Token

  User B Token
  ```

* Send the same request with each credential.

* Observe:

  ```text
  Object Access

  Data Returned

  Permissions

  Server-Side Identity
  ```

---

## Build a Role Matrix

* Example:

| Endpoint           | Anonymous | User A   | User B   | Admin   |
| ------------------ | --------- | -------- | -------- | ------- |
| `/api/profile`     | Deny      | Own      | Own      | Allowed |
| `/api/orders/{id}` | Deny      | Own only | Own only | All     |
| `/api/admin/users` | Deny      | Deny     | Deny     | Allowed |

* Test whether actual behavior matches expected behavior.

---

## Object-Level Authorization

* APIs frequently expose object identifiers.

* Example:

  ```http
  GET /api/orders/1001
  ```

* User A owns:

  ```text
  Order 1001
  ```

* User B owns:

  ```text
  Order 2002
  ```

* Test:

  ```text
  User A Credential

  +

  /api/orders/2002
  ```

* Expected:

  ```text
  Access Denied
  ```

---

## Broken Object Level Authorization

* Often abbreviated:

  ```text
  BOLA
  ```

* It occurs when the server fails to verify whether the authenticated user is authorized to access a specific object.

* Conceptually:

  ```text
  Authenticated User

  ↓

  Changes Object ID

  ↓

  Accesses Another User's Object
  ```

---

## Object Identifier Locations

* Look for identifiers in:

  ```text
  URL Path

  Query Parameters

  JSON Body

  Form Data

  Headers

  Cookies

  GraphQL Variables
  ```

* Examples:

  ```text
  /users/42
  ```

  ```text
  ?account_id=42
  ```

  ```json
  {
    "userId": 42
  }
  ```

---

## BOLA Testing Workflow

* Use two controlled accounts.

* Record:

  ```text
  User A Object ID

  User B Object ID
  ```

* Then:

  ```text
  Authenticate as User A

  ↓

  Request User A Object

  ↓

  Confirm Baseline

  ↓

  Replace ID With User B Object

  ↓

  Send

  ↓

  Verify Data / Action
  ```

---

## Read vs Write Authorization

* Test both:

  ```text
  Read
  ```

* And:

  ```text
  Modify / Delete
  ```

* Example:

  ```text
  GET /api/orders/2002
  ```

* May be protected.

* But:

  ```text
  PATCH /api/orders/2002
  ```

* Could still be vulnerable.

---

## Function-Level Authorization

* Object-level authorization asks:

  ```text
  Can this user access this object?
  ```

* Function-level authorization asks:

  ```text
  Can this user perform this function?
  ```

* Example:

  ```text
  Normal User

  ↓

  POST /api/admin/create-user
  ```

---

## Broken Function Level Authorization

* Often abbreviated:

  ```text
  BFLA
  ```

* It occurs when privileged functions are accessible to unauthorized roles.

* Examples:

  ```text
  /api/admin/users

  /api/internal/config

  /api/moderator/delete

  /api/v1/users/promote
  ```

---

## BFLA Testing Workflow

* Capture a privileged request using an authorized privileged test account.

* Then:

  ```text
  Preserve Request

  ↓

  Replace Admin Credential

  ↓

  Use Normal User Credential

  ↓

  Send

  ↓

  Verify Server-Side Action
  ```

* Do not rely only on status codes.

---

## Hidden Administrative Endpoints

* Frontends may hide administrative functionality from normal users.

* But endpoints may still exist.

* Discover through:

  ```text
  JavaScript

  Site Map

  API Documentation

  Privileged Test Account

  Historical Versions
  ```

* UI absence is not authorization.

---

## Property-Level Authorization

* APIs often accept objects with multiple properties.

* Example:

  ```json
  {
    "name": "Alice",
    "email": "alice@example.test",
    "role": "user"
  }
  ```

* A user may be allowed to modify:

  ```text
  name

  email
  ```

* But not:

  ```text
  role
  ```

---

## Mass Assignment

* Mass assignment occurs when user-supplied properties are automatically bound to internal object fields without sufficient restriction.

* Example normal request:

  ```json
  {
    "name": "Alice"
  }
  ```

* Tester adds:

  ```json
  {
    "name": "Alice",
    "role": "admin"
  }
  ```

* If the server accepts the unauthorized property, a serious vulnerability may exist.

---

## Mass Assignment Workflow

* Use:

  ```text
  Capture Valid Update Request

  ↓

  Identify Returned Object Properties

  ↓

  Identify Hidden / Read-Only Fields

  ↓

  Add One Candidate Property

  ↓

  Send

  ↓

  Retrieve Object Again

  ↓

  Verify Server-Side State
  ```

---

## Candidate Sensitive Properties

* Examples include:

  ```text
  role

  isAdmin

  permissions

  accountType

  verified

  balance

  ownerId

  userId

  tenantId

  status
  ```

* Only test properties supported by evidence from the application's schema, responses, JavaScript, or documentation.

* Avoid blind arbitrary property guessing when unnecessary.

---

## Property-Level Read Authorization

* APIs may return fields the frontend does not display.

* Example:

  ```json
  {
    "username": "alice",
    "email": "alice@example.test",
    "internal_notes": "...",
    "password_reset_token": "...",
    "admin_flag": false
  }
  ```

* The UI hiding a field does not prevent the API from exposing it.

---

## Excessive Data Exposure

* Review complete API responses.

* Look for unnecessary sensitive data such as:

  ```text
  Internal IDs

  Personal Data

  Tokens

  Secrets

  Hidden Account Attributes

  Administrative Metadata

  Debug Information
  ```

* Determine whether the client actually needs each field.

---

## Hidden Parameters

* APIs may support parameters not exposed by the normal UI.

* Discover through:

  ```text
  JavaScript

  API Documentation

  Response Objects

  Other API Versions

  Mobile Clients

  Error Messages
  ```

* Example response:

  ```json
  {
    "id": 42,
    "username": "alice",
    "role": "user"
  }
  ```

* This may justify investigating whether:

  ```text
  role
  ```

* Is accepted during updates.

---

## Parameter Mining

* Parameter discovery should be evidence-driven.

* Look for:

  ```text
  Similar Endpoint Parameters

  Object Properties

  JavaScript References

  API Schema Fields

  Historical Parameters
  ```

* Test one parameter at a time.

---

## HTTP Method Testing

* An endpoint may enforce authorization differently depending on the method.

* Example:

  ```text
  GET /api/users/42

  → Denied
  ```

* But:

  ```text
  PATCH /api/users/42

  → ?
  ```

* Test methods that are logically supported.

---

## Method Matrix

| Method  | Security Question                      |
| ------- | -------------------------------------- |
| GET     | Can data be read?                      |
| POST    | Can action/resource be created?        |
| PUT     | Can entire object be replaced?         |
| PATCH   | Can fields be modified?                |
| DELETE  | Can object be deleted?                 |
| OPTIONS | What methods/capabilities are exposed? |

---

## Method Override

* Some APIs support headers such as:

  ```text
  X-HTTP-Method-Override

  X-Method-Override
  ```

* Example:

  ```http
  POST /api/users/42
  X-HTTP-Method-Override: DELETE
  ```

* Determine whether security controls evaluate the original or effective method consistently.

---

## Content-Type Variation

* APIs may accept:

  ```text
  application/json

  application/x-www-form-urlencoded

  multipart/form-data

  application/xml
  ```

* Different parsers may trigger different validation paths.

* Where justified, compare equivalent requests using supported content types.

---

## Content-Type Differential

* Example:

  ```text
  JSON Request

  → Field Rejected
  ```

* But:

  ```text
  Form Request

  → Field Accepted
  ```

* This may indicate inconsistent validation.

* Confirm actual state changes.

---

## API Versioning

* APIs commonly use:

  ```text
  /api/v1/

  /api/v2/

  /api/v3/
  ```

* Older versions may remain accessible after newer versions are deployed.

* Security controls may differ between versions.

---

## Version Discovery

* Sources include:

  ```text
  JavaScript

  Documentation

  Site Map

  Mobile Clients

  Error Messages

  Historical URLs

  Existing Endpoint Patterns
  ```

* Example:

  ```text
  /api/v2/users
  ```

* May justify checking whether an authorized:

  ```text
  /api/v1/users
  ```

* Still exists.

---

## Version Differential Testing

* Compare:

  ```text
  Same Function

  +

  Same User

  +

  Same Object

  ```

* Across:

  ```text
  v1

  vs

  v2
  ```

* Check:

  ```text
  Authentication

  Authorization

  Data Exposure

  Validation

  Rate Limiting
  ```

---

## Deprecated API Endpoints

* Deprecated endpoints may have:

  ```text
  Older Authorization Logic

  Missing Validation

  Legacy Authentication

  Excessive Data Exposure

  Weak Rate Limits
  ```

* Do not assume deprecated means inaccessible.

---

## API Rate Limiting

* APIs may need limits for:

  ```text
  Login

  OTP Verification

  Password Reset

  Search

  Resource Creation

  Expensive Queries

  Financial Actions

  Messaging
  ```

* Test safely and with minimal traffic.

---

## Rate-Limit Baseline

* Record:

  ```text
  Request Count:

  Time Window:

  Status Codes:

  Headers:

  Response Messages:

  Delay:
  ```

* Look for:

  ```text
  429 Too Many Requests
  ```

* Or equivalent behavior.

---

## Rate-Limit Headers

* Possible headers include:

  ```text
  Retry-After

  RateLimit-Limit

  RateLimit-Remaining

  RateLimit-Reset
  ```

* Actual implementation varies.

---

## Rate-Limit Scope

* Determine whether limits are based on:

  ```text
  Account

  Token

  IP

  Session

  Endpoint

  Device

  Tenant
  ```

* Do not attempt broad bypass techniques without explicit authorization.

---

## Resource Consumption

* APIs may expose expensive operations.

* Examples:

  ```text
  Large Search Queries

  Complex Reports

  File Processing

  Bulk Exports

  GraphQL Queries

  Large Pagination Sizes
  ```

* Security testing should avoid causing denial of service.

* Use minimal proof.

---

## Pagination

* Common parameters:

  ```text
  page

  limit

  offset

  size

  cursor
  ```

* Investigate:

  ```text
  Maximum Page Size

  Authorization Across Pages

  Data Leakage

  Unexpected Negative Values

  Excessive Resource Requests
  ```

* Avoid requesting unnecessarily large datasets.

---

## Filtering and Sorting

* APIs may accept:

  ```text
  filter

  sort

  order

  fields

  include

  expand
  ```

* These parameters may expose:

  ```text
  Hidden Fields

  Related Objects

  Internal Data

  Unauthorized Records
  ```

---

## Field Selection

* Example:

  ```text
  ?fields=id,name,email
  ```

* Investigate whether sensitive fields can be requested.

* Example:

  ```text
  ?fields=id,name,internal_notes
  ```

* Only where the API supports such syntax.

---

## Object Expansion

* APIs may support:

  ```text
  ?include=user

  ?expand=account

  ?include=permissions
  ```

* Expansion may expose nested objects that have weaker authorization filtering.

---

## Nested Object Authorization

* Example response:

  ```json
  {
    "order": {
      "id": 1001,
      "customer": {
        "id": 42,
        "email": "..."
      }
    }
  }
  ```

* Verify authorization for:

  ```text
  Parent Object

  and

  Nested Objects
  ```

---

## Business Logic Testing

* APIs directly expose workflows, making them ideal targets for business logic testing.

* Ask:

  ```text
  Can Steps Be Skipped?

  Can Requests Be Reordered?

  Can Values Be Manipulated?

  Can One-Time Actions Be Replayed?

  Can Limits Be Bypassed?

  Can State Transitions Be Forced?
  ```

---

## Workflow Sequence Testing

* Normal:

  ```text
  Create Order

  ↓

  Pay

  ↓

  Ship
  ```

* Test whether:

  ```text
  Ship
  ```

* Can be triggered before:

  ```text
  Pay
  ```

* Backend APIs must enforce workflow state independently of the UI.

---

## Price and Quantity Parameters

* Identify client-controlled values such as:

  ```text
  price

  quantity

  discount

  total

  currency

  shipping_cost
  ```

* Determine which values the server recalculates.

* Example:

  ```json
  {
    "product_id": 100,
    "quantity": 1,
    "price": 1
  }
  ```

* Secure systems should not blindly trust client-controlled authoritative pricing.

---

## Negative and Boundary Values

* Where appropriate, test safe values such as:

  ```text
  0

  -1

  Maximum Expected Value

  Just Above Maximum

  Empty Value

  Null
  ```

* Observe validation and resulting state.

---

## API Replay

* Replay sensitive actions such as:

  ```text
  Redeem

  Transfer

  Claim

  Confirm

  Invite

  Verify
  ```

* Determine whether one-time operations are actually single-use.

* Sequential replay flaws should be distinguished from race conditions.

---

## API Race Conditions

* APIs frequently expose state-changing actions suitable for controlled concurrency testing.

* Example:

  ```text
  POST /api/redeem
  ```

* Use the race-condition workflow:

  ```text
  Establish Baseline

  ↓

  Test Sequential Duplicate

  ↓

  Reset State

  ↓

  Send Concurrent Requests

  ↓

  Verify Final State
  ```

---

## Injection Testing

* API inputs may reach:

  ```text
  Databases

  Operating System Commands

  Template Engines

  XML Parsers

  Search Engines

  Internal Services
  ```

* Potential vulnerability classes include:

  ```text
  SQL Injection

  NoSQL Injection

  Command Injection

  SSTI

  XPath Injection

  LDAP Injection
  ```

* Test based on observed technology and behavior rather than blindly inserting payloads everywhere.

---

## JSON Input Testing

* Example:

  ```json
  {
    "username": "alice",
    "search": "example"
  }
  ```

* Test one property at a time.

* Observe:

  ```text
  Errors

  Response Differences

  Timing

  Data Changes
  ```

---

## Type Confusion

* JSON supports types such as:

  ```text
  String

  Number

  Boolean

  Null

  Array

  Object
  ```

* An API expecting:

  ```json
  {
    "user_id": 42
  }
  ```

* May behave differently with:

  ```json
  {
    "user_id": "42"
  }
  ```

* Or:

  ```json
  {
    "user_id": [42]
  }
  ```

* Type changes may reveal validation inconsistencies.

---

## Null Handling

* Test whether:

  ```json
  {
    "field": null
  }
  ```

* Produces different behavior from:

  ```json
  {}
  ```

* Or:

  ```json
  {
    "field": ""
  }
  ```

* Null handling can affect:

  ```text
  Validation

  Defaults

  Authorization

  State Transitions
  ```

---

## Duplicate JSON Keys

* Different parsers may interpret duplicate keys differently.

* Conceptual example:

  ```json
  {
    "role": "user",
    "role": "admin"
  }
  ```

* Possible interpretations:

  ```text
  First Value

  Last Value

  Reject

  Parser-Specific Behavior
  ```

* Test only where parser inconsistency is relevant and authorized.

---

## API SSRF

* APIs may accept URLs for:

  ```text
  Webhooks

  Image Imports

  URL Previews

  File Imports

  Callback URLs

  Integrations
  ```

* Example:

  ```json
  {
    "url": "https://example.test/resource"
  }
  ```

* Such functionality may create server-side request forgery risk.

---

## SSRF Investigation

* Determine:

  ```text
  Does Server Fetch URL?

  Which Protocols Are Allowed?

  Are Redirects Followed?

  Is Destination Validated?

  Is DNS Re-Resolved?

  Are Internal Addresses Blocked?
  ```

* Use controlled callback infrastructure or authorized labs.

---

## Burp Collaborator for APIs

* Collaborator can help detect blind server-side interactions.

* Useful for:

  ```text
  SSRF

  Blind XXE

  Blind Command Injection

  Out-of-Band Interactions
  ```

* Example workflow:

  ```text
  Generate Collaborator Payload

  ↓

  Place in Suspected URL Parameter

  ↓

  Send API Request

  ↓

  Poll Collaborator

  ↓

  Correlate Interaction
  ```

---

## File Upload APIs

* APIs may accept:

  ```text
  multipart/form-data

  Base64-Encoded Files

  Presigned Uploads

  Direct Object Storage Uploads
  ```

* Test:

  ```text
  File Type Validation

  Content Validation

  Filename Handling

  Access Control

  Storage Permissions

  Retrieval Authorization
  ```

* Use harmless test files.

---

## API Error Handling

* Errors may expose:

  ```text
  Stack Traces

  Database Errors

  Internal Paths

  Framework Versions

  Internal Hostnames

  Object Schemas

  Validation Rules
  ```

* Compare malformed and valid requests carefully.

---

## Verbose Error Example

* Example:

  ```json
  {
    "error": "SQLSTATE...",
    "query": "...",
    "file": "/app/src/UserController.php",
    "line": 142
  }
  ```

* This may disclose useful internal information.

* Evaluate actual sensitivity and exploitability.

---

## Status Codes

* Common API status codes include:

  ```text
  200 OK

  201 Created

  204 No Content

  400 Bad Request

  401 Unauthorized

  403 Forbidden

  404 Not Found

  409 Conflict

  422 Unprocessable Content

  429 Too Many Requests

  500 Internal Server Error
  ```

* Status codes are useful signals but do not replace response-body analysis.

---

## `401` vs `403`

* Common interpretation:

  ```text
  401

  → Authentication required or invalid
  ```

* And:

  ```text
  403

  → Authenticated but not authorized
  ```

* Real implementations may vary.

* Judge actual behavior, not only code semantics.

---

## GraphQL APIs

* GraphQL commonly uses an endpoint such as:

  ```text
  /graphql
  ```

* Requests may look like:

  ```json
  {
    "query": "query { user { id name } }"
  }
  ```

* Unlike REST, many operations may share one endpoint.

---

## GraphQL Components

* Important concepts:

  ```text
  Queries

  Mutations

  Fields

  Arguments

  Variables

  Types

  Schema
  ```

* Example:

  ```graphql
  query {
    user(id: 42) {
      id
      name
      email
    }
  }
  ```

---

## GraphQL Authorization

* Authorization must be enforced at the appropriate:

  ```text
  Object

  Field

  Resolver

  Mutation
  ```

* A user being allowed to query:

  ```text
  user
  ```

* Does not mean every field should be accessible.

---

## GraphQL Object-Level Testing

* Example:

  ```graphql
  query {
    order(id: 2002) {
      id
      total
    }
  }
  ```

* Authenticate as User A.

* Substitute User B's controlled test object ID.

* Verify authorization.

---

## GraphQL Field-Level Authorization

* Example:

  ```graphql
  query {
    user(id: 42) {
      id
      username
      internalNotes
    }
  }
  ```

* The application may correctly protect the object but expose a sensitive field.

* Test fields according to schema evidence.

---

## GraphQL Mutations

* Mutations perform state changes.

* Example:

  ```graphql
  mutation {
    updateUser(id: 42, role: "admin") {
      id
      role
    }
  }
  ```

* Test:

  ```text
  Object Authorization

  Function Authorization

  Property Authorization

  Business Logic
  ```

---

## GraphQL Introspection

* Introspection can expose schema information when enabled.

* It may reveal:

  ```text
  Types

  Queries

  Mutations

  Fields

  Arguments
  ```

* Introspection being enabled is not automatically a vulnerability.

* Assess whether it exposes meaningful attack surface or sensitive implementation details.

---

## GraphQL Resource Consumption

* Complex nested queries can consume significant server resources.

* Avoid intentionally creating denial-of-service conditions.

* Test minimally.

* Investigate whether the application uses:

  ```text
  Depth Limits

  Complexity Limits

  Query Cost Controls

  Rate Limiting
  ```

---

## API CORS

* APIs may allow browser access from other origins.

* Inspect:

  ```text
  Access-Control-Allow-Origin

  Access-Control-Allow-Credentials
  ```

* CORS issues become security-relevant when an untrusted origin can read sensitive authenticated responses.

* Test complete exploit conditions, not headers in isolation.

---

## API Caching

* Sensitive API responses should be handled appropriately by:

  ```text
  Browser Cache

  CDN

  Reverse Proxy

  Shared Cache
  ```

* Review:

  ```text
  Cache-Control

  Vary

  Age
  ```

* Especially for:

  ```text
  User-Specific Data

  Authentication Responses

  Tokens

  Private Objects
  ```

---

## Multi-Tenant APIs

* SaaS applications may use:

  ```text
  tenant_id

  organization_id

  workspace_id

  team_id
  ```

* Authorization must enforce both:

  ```text
  User Identity

  +

  Tenant Boundary
  ```

* Test with controlled accounts in separate authorized tenants where possible.

---

## Tenant Boundary Testing

* Example:

  ```text
  User A → Tenant 100

  User B → Tenant 200
  ```

* Test:

  ```text
  User A Credential

  +

  Tenant 200 Object ID
  ```

* Verify both read and write operations.

---

## API Gateway Behavior

* APIs may sit behind:

  ```text
  API Gateway

  WAF

  Reverse Proxy

  Load Balancer
  ```

* Different layers may enforce:

  ```text
  Authentication

  Rate Limiting

  Routing

  Method Restrictions

  Schema Validation
  ```

* Look for inconsistencies between layers.

---

## Burp Proxy for API Testing

* Use Proxy to:

  ```text
  Capture API Calls

  Observe Authentication

  Discover Endpoints

  Identify Parameters

  Track Workflow Sequences
  ```

* Filter noisy static content where useful.

---

## Burp Target for API Testing

* Use Target to organize:

  ```text
  API Hosts

  Endpoint Hierarchy

  Versions

  Discovered Paths

  Scope
  ```

* Site Map can become a practical API inventory.

---

## Burp Repeater for API Testing

* Repeater is the primary manual API testing tool.

* Use it for:

  ```text
  Object ID Changes

  Token Swapping

  Method Changes

  Property Injection

  Content-Type Changes

  Version Comparison

  Business Logic Testing
  ```

---

## Burp Intruder for API Testing

* Intruder can assist with controlled testing of:

  ```text
  Object IDs

  Parameter Values

  Enumeration

  Small Wordlists

  Boundary Values

  Rate-Limit Observation
  ```

* Avoid blind high-volume fuzzing.

---

## Burp Decoder for API Testing

* Use Decoder for:

  ```text
  JWT Components

  Base64 Data

  URL-Encoding

  Encoded Parameters

  Custom Token Inspection
  ```

---

## Burp Comparer for API Testing

* Compare:

  ```text
  User A vs User B

  Authorized vs Unauthorized

  v1 vs v2

  GET vs PATCH

  Original vs Modified Property

  Valid vs Invalid Token
  ```

* This helps identify subtle authorization and data differences.

---

## Burp Logger for API Testing

* Logger can reveal:

  ```text
  Background API Calls

  Token Refreshes

  Hidden Requests

  Extension Traffic

  Multi-Step Workflows
  ```

* Use it when the frontend generates many asynchronous requests.

---

## API Testing Workflow

* Use:

  ```text
  Define Scope

  ↓

  Identify API Hosts

  ↓

  Browse Application

  ↓

  Capture API Traffic

  ↓

  Build Endpoint Inventory

  ↓

  Identify Authentication

  ↓

  Create Controlled Identities

  ↓

  Build Role Matrix

  ↓

  Identify Object IDs

  ↓

  Test Object-Level Authorization

  ↓

  Test Function-Level Authorization

  ↓

  Test Property-Level Authorization

  ↓

  Analyze Data Exposure

  ↓

  Test Methods and Content Types

  ↓

  Compare API Versions

  ↓

  Test Business Logic

  ↓

  Review Rate Limits

  ↓

  Investigate Input Handling

  ↓

  Test GraphQL if Present

  ↓

  Verify Final Server-Side State

  ↓

  Reproduce Findings

  ↓

  Preserve Evidence
  ```

---

## API Attack Surface Map

* Document:

  ```text
  API Host:

  Base Path:

  Versions:

  Authentication:

  Session Cookie:

  Bearer Token:

  API Key:

  GraphQL:

  Documentation:

  Endpoints:

  Methods:

  Object IDs:

  User Roles:

  Tenant IDs:

  Sensitive Properties:

  Hidden Parameters:

  Rate Limits:

  File Uploads:

  URL Inputs:

  Business Workflows:

  Deprecated Endpoints:
  ```

---

## API Investigation Card

* Record:

  ```text
  Investigation:

  API Host:

  Endpoint:

  Method:

  Version:

  User:

  Role:

  Tenant:

  Authentication:

  Object ID:

  Baseline Request:

  Baseline Response:

  Modified Parameter:

  Original Value:

  Modified Value:

  Expected Result:

  Actual Result:

  Server-Side State:

  Authorization Impact:

  Data Exposure:

  Reproduced?:

  Evidence Preserved?:

  Follow-Up:
  ```

---

## API Testing Checklist

* Verify:

  ```text
  [ ] API hosts identified

  [ ] API endpoints mapped

  [ ] Methods recorded

  [ ] API versions identified

  [ ] Authentication mechanism understood

  [ ] Controlled identities established

  [ ] Role matrix created

  [ ] Unauthenticated behavior tested

  [ ] Object identifiers identified

  [ ] BOLA tested

  [ ] Read authorization tested

  [ ] Write authorization tested

  [ ] Delete authorization tested

  [ ] Function-level authorization tested

  [ ] Administrative endpoints reviewed

  [ ] Property-level authorization tested

  [ ] Mass assignment considered

  [ ] Excessive data exposure reviewed

  [ ] Hidden parameters investigated

  [ ] HTTP methods compared

  [ ] Method override considered

  [ ] Content types reviewed

  [ ] API versions compared

  [ ] Deprecated endpoints reviewed

  [ ] Rate limiting reviewed safely

  [ ] Pagination reviewed

  [ ] Filtering / sorting reviewed

  [ ] Nested objects reviewed

  [ ] Business logic tested

  [ ] Replay behavior tested

  [ ] Race candidates identified

  [ ] Input handling reviewed

  [ ] URL-fetch functionality reviewed for SSRF risk

  [ ] File upload APIs reviewed

  [ ] Error handling reviewed

  [ ] GraphQL reviewed if present

  [ ] Tenant boundaries tested where applicable

  [ ] Final server-side state verified

  [ ] Findings reproduced cleanly
  ```

---

## Testing Strategy Matrix

| Scenario               | Primary Investigation                    |
| ---------------------- | ---------------------------------------- |
| `/users/{id}`          | Object-level authorization               |
| `/orders/{id}`         | Read/write ownership                     |
| `/admin/*`             | Function-level authorization             |
| JSON update body       | Property authorization / mass assignment |
| Large API response     | Excessive data exposure                  |
| Hidden response fields | Candidate writable properties            |
| `/v1/` and `/v2/`      | Version differential                     |
| Multiple methods       | Method authorization                     |
| Multiple content types | Parser/validation differential           |
| One-time action        | Replay / race condition                  |
| URL parameter          | SSRF                                     |
| File endpoint          | Upload/access controls                   |
| Pagination             | Data exposure/resource limits            |
| GraphQL query          | Object/field authorization               |
| GraphQL mutation       | Function/property authorization          |
| Multi-tenant object    | Tenant isolation                         |
| Repeated requests      | Rate limiting                            |
| Error responses        | Information disclosure                   |

---

## Troubleshooting — API Request Fails in Repeater

* Check:

  ```text
  Authentication Token Expired?

  ↓

  CSRF Token Required?

  ↓

  Missing Header?

  ↓

  Content-Type Correct?

  ↓

  Dynamic Signature?

  ↓

  Required Cookie Missing?

  ↓

  Request Body Valid?
  ```

---

## Troubleshooting — Changed Object ID Returns 404

* A `404` may mean:

  ```text
  Object Does Not Exist

  or

  Authorization Hides Object Existence
  ```

* Use a known object belonging to another controlled test account.

* Do not guess random production IDs unnecessarily.

---

## Troubleshooting — Modified Property Returns 200 but Nothing Changes

* Verify:

  ```text
  Was Property Ignored?

  ↓

  Is Field Read-Only?

  ↓

  Did Response Echo Input Only?

  ↓

  Retrieve Object Again

  ↓

  Check Authoritative Server State
  ```

---

## Troubleshooting — Admin Endpoint Returns 200 to User

* Determine:

  ```text
  Is Response Sensitive?

  Is Function Actually Executed?

  Is Endpoint Public by Design?

  Is Response Generic?

  Did Server-Side State Change?
  ```

* Status code alone is insufficient.

---

## Troubleshooting — Different API Version Behaves Differently

* Keep constant:

  ```text
  User

  Role

  Authentication

  Object

  Method

  Parameters
  ```

* Then isolate:

  ```text
  Version
  ```

---

## Troubleshooting — Rate Limit Not Triggering

* Possible explanations:

  ```text
  Limit Is Higher

  Endpoint Not Rate Limited

  Requests Distributed Across Nodes

  Limit Based on Account

  Limit Based on IP

  Limit Based on Token
  ```

* Do not escalate request volume indefinitely.

---

## Troubleshooting — GraphQL Returns Generic Errors

* Check:

  ```text
  Query Syntax

  Operation Name

  Variables

  Authentication

  Field Names

  Expected Types
  ```

* Preserve a valid baseline query before modifications.

---

## Troubleshooting — API Response Contains Another User's Data

* Confirm:

  ```text
  Which User Is Authenticated?

  Which Object Was Requested?

  Is Data Public?

  Is Object Shared?

  Is Tenant Context Correct?

  Is Response Cached?
  ```

* Reproduce with controlled accounts.

---

## Avoiding False Positives

* API findings can be misinterpreted because of:

  ```text
  Public Data

  Shared Objects

  Cached Responses

  Stale Tokens

  Background Requests

  Multiple Authentication Credentials

  Asynchronous Updates

  Client-Side Echoes

  Eventual Consistency
  ```

* Verify actual:

  ```text
  Server-Side Authorization

  and

  Server-Side State
  ```

---

## Strong BOLA Evidence

* Example:

  ```text
  User A owns object 1001.

  User B owns object 2002.
  ```

* Control:

  ```text
  User A + /objects/1001

  → Success
  ```

* Test:

  ```text
  User A + /objects/2002

  → User B's private object returned
  ```

* This demonstrates:

  ```text
  Authenticated Identity

  +

  Cross-User Object

  +

  Unauthorized Access
  ```

---

## Strong BFLA Evidence

* Example:

  ```text
  Admin request:

  POST /api/admin/users/42/disable
  ```

* Replace admin authentication with normal user authentication.

* If:

  ```text
  Normal User

  ↓

  Successfully Disables User
  ```

* Confirm the actual server-side state change.

---

## Strong Mass Assignment Evidence

* Baseline:

  ```json
  {
    "displayName": "Alice"
  }
  ```

* Modified:

  ```json
  {
    "displayName": "Alice",
    "role": "admin"
  }
  ```

* Strong proof requires:

  ```text
  Request Accepted

  +

  Role Actually Changed

  +

  Privileged Function Becomes Accessible
  ```

* Do not rely only on the response echo.

---

## Evidence Collection

* Preserve:

  ```text
  User

  Role

  Tenant

  Authentication Credential Type

  Endpoint

  Method

  API Version

  Object Ownership

  Original Request

  Modified Request

  Original Response

  Modified Response

  Final Server-Side State

  Security Impact

  Reproduction Steps
  ```

---

## Practical Exercise

* Use a deliberately vulnerable API lab or another API you are explicitly authorized to test.

### Task 1 — Identify API Traffic

* Browse the application through Burp.

* Record:

  ```text
  API Host:

  Base Path:

  Content Type:

  Authentication:
  ```

---

### Task 2 — Build Endpoint Inventory

* Identify at least five endpoints.

* Record:

  ```text
  Method

  Endpoint

  Purpose

  Authentication

  Object ID
  ```

---

### Task 3 — Establish Two Identities

* Use:

  ```text
  User A

  User B
  ```

* Record one object belonging to each.

---

### Task 4 — Test Authentication Removal

* Capture one authenticated API request.

* Remove the authentication credential.

* Record:

  ```text
  Authenticated Result:

  Unauthenticated Result:
  ```

---

### Task 5 — Test Object Authorization

* Authenticate as User A.

* Replace User A's object ID with User B's controlled object ID.

* Record:

  ```text
  Expected:

  Actual:

  Data Exposed?:

  State Changed?:
  ```

---

### Task 6 — Test Property Handling

* Capture a safe profile update.

* Identify one property from API responses or documentation not normally editable.

* Add it to the request.

* Retrieve the object again.

* Record whether it changed.

---

### Task 7 — Compare Methods

* For one endpoint, identify supported methods.

* Record:

  ```text
  GET:

  POST:

  PATCH:

  DELETE:
  ```

* Test only methods safe for the lab.

---

### Task 8 — Compare API Versions

* If multiple versions exist:

  ```text
  v1

  v2
  ```

* Compare equivalent functionality.

* Record security differences.

---

### Task 9 — Analyze Responses

* Review for:

  ```text
  Hidden Fields

  Internal IDs

  Sensitive Metadata

  Debug Information
  ```

---

### Task 10 — Build the API Map

* Document:

  ```text
  Client

  ↓

  API Host

  ↓

  Authentication

  ↓

  Endpoints

  ↓

  Objects

  ↓

  Roles

  ↓

  Business Workflows
  ```

---

## Investigation Journal

* Record:

  ```text
  Application:

  API Host:

  Base Path:

  API Versions:

  Documentation:

  Authentication:

  User A:

  User B:

  Roles:

  Tenants:

  Endpoints:

  Methods:

  Object IDs:

  BOLA Tests:

  BFLA Tests:

  Property Tests:

  Mass Assignment:

  Data Exposure:

  Hidden Parameters:

  Method Differences:

  Content-Type Differences:

  Version Differences:

  Rate Limiting:

  Pagination:

  Business Logic:

  Replay:

  Race Candidates:

  Injection Candidates:

  SSRF Candidates:

  File Uploads:

  GraphQL:

  Error Handling:

  Security Impact:

  Evidence:

  Follow-Up:
  ```

---

## Common Mistakes

* Common mistakes include:

  * Treating API testing as random endpoint fuzzing.
  * Testing endpoints without first mapping authentication and roles.
  * Using random object IDs instead of controlled cross-account objects.
  * Testing only `GET` requests for BOLA.
  * Ignoring update and delete authorization.
  * Confusing object-level and function-level authorization.
  * Assuming hidden UI functionality is inaccessible through the API.
  * Ignoring properties returned by API responses.
  * Claiming mass assignment based only on an echoed response.
  * Failing to verify server-side state after modifications.
  * Ignoring older API versions.
  * Blindly guessing thousands of hidden parameters.
  * Ignoring content-type and parser differences.
  * Performing uncontrolled rate-limit testing.
  * Requesting excessively large datasets.
  * Ignoring nested object authorization.
  * Testing only REST while overlooking GraphQL.
  * Treating GraphQL introspection as automatically vulnerable.
  * Ignoring multi-tenant boundaries.
  * Treating every `200 OK` as successful authorization.
  * Ignoring multiple authentication mechanisms.
  * Failing to distinguish public, shared, and private objects.
  * Ignoring business workflows behind individual endpoints.
  * Reporting API vulnerabilities without clearly documenting object ownership, role, and server-side impact.

---

## Interview Questions

1. What is an API?
2. Why are APIs important during web application security testing?
3. What is a REST API?
4. What are common HTTP methods used by APIs?
5. How do you identify API traffic using Burp Suite?
6. What sources can reveal hidden API endpoints?
7. Why are JavaScript files useful during API reconnaissance?
8. What should an API endpoint inventory contain?
9. What authentication mechanisms are commonly used by APIs?
10. What is BOLA?
11. How would you test BOLA safely?
12. Why should you use two controlled accounts for object authorization testing?
13. Where can object identifiers appear in API requests?
14. Why should both read and write authorization be tested?
15. What is BFLA?
16. How does BFLA differ from BOLA?
17. How would you test an administrative API function?
18. What is property-level authorization?
19. What is mass assignment?
20. How would you confirm a mass-assignment vulnerability?
21. What is excessive data exposure?
22. How can hidden API parameters be discovered?
23. Why should parameter discovery be evidence-driven?
24. Why should different HTTP methods be tested?
25. What are method-override headers?
26. Why can content-type differences be security-relevant?
27. Why should older API versions be tested?
28. What risks can deprecated API endpoints introduce?
29. How would you evaluate API rate limiting?
30. Why should resource-consumption testing be conservative?
31. What security issues can pagination and filtering introduce?
32. Why should nested object authorization be tested?
33. How do APIs expose business-logic vulnerabilities?
34. What is the difference between replay testing and race-condition testing?
35. Why can JSON type changes reveal validation problems?
36. Why can duplicate JSON keys create parser inconsistencies?
37. Which API features commonly create SSRF risk?
38. How can Burp Collaborator assist API testing?
39. What should be tested in file-upload APIs?
40. What information can verbose API errors expose?
41. What is GraphQL?
42. How does GraphQL differ from REST from a testing perspective?
43. What is GraphQL field-level authorization?
44. Why is introspection not automatically a vulnerability?
45. What are GraphQL resource-consumption risks?
46. How do multi-tenant APIs change authorization testing?
47. Why must server-side state be verified after API modifications?
48. How can Burp Repeater assist API testing?
49. How can Burp Comparer help identify authorization differences?
50. What is the correct end-to-end methodology for API security testing with Burp?

---

## Quick Revision

* API model:

  ```text
  Client

  ↓

  API Endpoint

  ↓

  Authentication

  ↓

  Authorization

  ↓

  Business Logic

  ↓

  Data
  ```

* API mapping:

  ```text
  Hosts

  +

  Endpoints

  +

  Methods

  +

  Parameters

  +

  Objects

  +

  Roles

  +

  Versions
  ```

* BOLA:

  ```text
  User A Credential

  +

  User B Object ID

  ↓

  Unauthorized Object Access?
  ```

* BFLA:

  ```text
  Normal User

  +

  Privileged Function

  ↓

  Unauthorized Action?
  ```

* Mass assignment:

  ```text
  Valid Update

  +

  Hidden Sensitive Property

  ↓

  Server Accepts Unauthorized Change?
  ```

* Version testing:

  ```text
  Same Function

  ↓

  v1 vs v2

  ↓

  Compare Security Controls
  ```

* API business logic:

  ```text
  Map Workflow

  ↓

  Change Order / Values / State

  ↓

  Verify Server Enforcement
  ```

* GraphQL:

  ```text
  Query / Mutation

  ↓

  Object

  ↓

  Fields

  ↓

  Resolver Authorization
  ```

* Strong authorization proof:

  ```text
  Known User

  +

  Known Role

  +

  Known Object Ownership

  +

  Controlled Modification

  +

  Verified Server-Side Result
  ```

* Remember:

  ```text
  Hidden in UI

  ≠

  Protected by API
  ```

  ```text
  Authenticated

  ≠

  Authorized for Every Object
  ```

  ```text
  200 OK

  ≠

  Successful Security Bypass
  ```

  ```text
  Response Echo

  ≠

  Server-Side State Change
  ```

  ```text
  Deprecated

  ≠

  Disabled
  ```

  ```text
  GraphQL Introspection Enabled

  ≠

  Automatically Vulnerable
  ```

* Professional principle:

  ```text
  Map Endpoints

  +

  Understand Identity

  +

  Model Objects and Roles

  +

  Test Authorization Boundaries

  +

  Test Business Logic

  +

  Verify Server-Side Impact

  =

  Reliable API Security Testing
  ```

---

## Key Takeaways

* APIs expose application functionality directly and must be treated as a complete security attack surface rather than merely backend support for the UI.
* Build an endpoint inventory containing methods, authentication, roles, parameters, object identifiers, versions, and purpose before deep testing.
* Use Proxy, Target, JavaScript, documentation, OpenAPI specifications, mobile traffic, and application workflows to discover endpoints.
* Establish controlled identities and a role matrix so authorization expectations are explicit.
* BOLA occurs when the server fails to enforce authorization for specific objects; test with known objects belonging to controlled accounts.
* Test object authorization across read, update, delete, and other state-changing operations rather than only `GET`.
* BFLA concerns access to privileged functions and should be tested by replaying legitimate privileged requests with lower-privileged controlled identities.
* Property-level authorization must prevent users from reading or modifying fields they are not permitted to access.
* Mass-assignment findings require verification that an unauthorized property actually changes authoritative server-side state.
* Review complete API responses for unnecessary sensitive fields and excessive data exposure.
* Hidden parameters should be discovered through evidence such as JavaScript, schemas, responses, documentation, and related endpoints.
* Compare supported HTTP methods, content types, and API versions because different processing paths may enforce different security controls.
* Deprecated API versions can retain weaker authentication, authorization, validation, or data-exposure behavior.
* Rate-limit and resource-consumption testing must be controlled to avoid operational disruption.
* Pagination, filtering, field selection, object expansion, and nested resources can introduce additional authorization and data-exposure risks.
* APIs frequently expose business logic directly, so test workflow ordering, replay, values, limits, and state transitions.
* JSON type handling, null values, and parser behavior can expose validation inconsistencies when tested methodically.
* URL-fetching API features such as webhooks and imports are common SSRF candidates, and out-of-band interactions can be investigated using controlled infrastructure.
* GraphQL requires testing authorization at object, field, resolver, and mutation levels rather than treating the single endpoint as one permission boundary.
* Multi-tenant APIs require explicit testing of both user authorization and tenant isolation.
* HTTP status codes alone do not prove authorization success or failure; verify returned data and final server-side state.
* Strong API findings clearly document the authenticated identity, role, tenant, object ownership, modified request, expected behavior, actual behavior, and measurable security impact.
* The core API testing mindset is to understand how identity, objects, functions, properties, and business state interact—and then systematically test every trust boundary between them.
