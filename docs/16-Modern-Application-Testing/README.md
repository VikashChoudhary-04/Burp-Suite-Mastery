# Modern Application Testing

> **Module 16 — Modern Application Testing**

> **Progress**

```text
█░░░░░░░░░░░░░░ 1 / 15 Files
```

---

## Overview

* Modern web applications are significantly different from traditional server-rendered websites.

* Applications increasingly rely on:

  * REST APIs.
  * GraphQL.
  * Single-page applications.
  * JavaScript-heavy frontends.
  * JSON-based communication.
  * JWT and token-based authentication.
  * WebSockets.
  * Microservices.
  * Cloud-hosted services.
  * Mobile application backends.
  * Third-party integrations.

* These architectures change how penetration testers discover attack surfaces, identify trust boundaries, and investigate security controls.

* Traditional testing often begins with:

  ```text
  Page

  ↓

  Form

  ↓

  Parameter

  ↓

  Server Response
  ```

* Modern applications may instead operate through:

  ```text
  Frontend

  ↓

  JavaScript

  ↓

  API Gateway

  ↓

  Multiple Backend Services

  ↓

  Databases / Cloud Services / Third Parties
  ```

* The visible interface may expose only a fraction of the real attack surface.

* Effective testing therefore requires understanding the application's architecture, communication model, identities, objects, workflows, and trust boundaries.

---

## Module Goal

* The goal of this module is to teach you how to use Burp Suite to investigate **modern application architectures systematically**.

* You will move beyond:

  ```text
  Browse Page

  ↓

  Intercept Form

  ↓

  Modify Parameter
  ```

* Toward:

  ```text
  Map Architecture

  ↓

  Discover Interfaces

  ↓

  Identify Trust Boundaries

  ↓

  Analyze Client-Server Communication

  ↓

  Map Authentication and Identity

  ↓

  Test Authorization

  ↓

  Test Data and Workflow Boundaries

  ↓

  Investigate Concurrency and File Handling

  ↓

  Analyze Real-Time and GraphQL Interfaces

  ↓

  Validate Security Impact

  ↓

  Preserve Evidence
  ```

---

## Learning Objectives

* By completing this module, you should be able to:

  * Recognize modern web application architectures.
  * Understand single-page application communication.
  * Discover API endpoints through Burp Suite and JavaScript analysis.
  * Build structured endpoint and attack-surface inventories.
  * Map REST APIs systematically.
  * Analyze API methods, parameters, content types, and versions.
  * Identify hidden and undocumented functionality.
  * Understand authentication and token lifecycles.
  * Investigate session cookies, bearer tokens, JWTs, refresh tokens, and related authentication mechanisms.
  * Test object-level authorization.
  * Test function-level authorization.
  * Test property-level authorization.
  * Investigate tenant and role boundaries.
  * Identify mass-assignment risks.
  * Analyze excessive data exposure.
  * Investigate business-logic weaknesses.
  * Model workflows and security invariants.
  * Investigate race conditions and concurrency-sensitive operations safely.
  * Test file-upload and file-handling workflows.
  * Investigate WebSocket-based functionality.
  * Analyze GraphQL queries, mutations, objects, fields, and authorization boundaries.
  * Understand microservice and backend trust boundaries.
  * Evaluate rate-limiting behavior safely.
  * Combine Burp Suite tools into repeatable modern application testing workflows.
  * Distinguish anomalies from validated vulnerabilities.
  * Produce reproducible evidence for modern application security findings.

---

## Why Modern Applications Require a Different Mindset

* A traditional application may expose functionality directly through HTML.

* Example:

  ```html
  <form action="/profile/update" method="POST">
  ```

* A modern frontend may instead execute:

  ```text
  JavaScript

  ↓

  fetch()

  ↓

  /api/v2/users/4831

  ↓

  JSON Request

  ↓

  JSON Response
  ```

* The security-relevant functionality may never appear as a traditional HTML form.

* Important functionality may instead be exposed through:

  ```text
  REST Endpoints

  GraphQL Operations

  WebSocket Messages

  JavaScript Routes

  Background Requests

  Mobile APIs

  Versioned APIs

  Internal Service Interfaces
  ```

---

## The Browser Is Only One Client

* Modern backend services may serve:

  ```text
  Web Application

  Mobile Application

  Desktop Application

  Partner Integration

  Internal Service

  Third-Party Client
  ```

* Therefore:

  ```text
  Frontend Restrictions

  ≠

  Backend Security Controls
  ```

* Burp Suite allows you to inspect and manipulate the underlying communication so you can test what the server actually enforces.

---

## Modern Application Mental Model

* Think in layers:

  ```text
  User Interface

  ↓

  Client-Side JavaScript

  ↓

  HTTP / WebSocket Communication

  ↓

  API Gateway / Backend

  ↓

  Services

  ↓

  Data Stores / External Systems
  ```

* At every boundary, ask:

  ```text
  What Data Crosses This Boundary?

  Who Controls It?

  What Identity Is Trusted?

  What Authorization Is Expected?

  What Validation Should Occur?

  What State Must Be Preserved?

  What Happens If the Client Changes It?
  ```

---

## Core Principle

* Never assume that something is secure because the frontend prevents it.

* Example:

  ```text
  UI Does Not Show "Admin" Button
  ```

* Does not prove:

  ```text
  Normal User Cannot Call Admin Function
  ```

* The real security question is:

  ```text
  What Does the Server Enforce?
  ```

* A controlled investigation may follow:

  ```text
  Capture Legitimate Request

  ↓

  Understand Identity and Authorization Context

  ↓

  Establish Baseline

  ↓

  Change One Security-Relevant Variable

  ↓

  Replay Under Controlled Conditions

  ↓

  Compare Behavior

  ↓

  Verify Final State

  ↓

  Validate Security Impact
  ```

---

## Module Structure

* Module 16 contains twelve lessons followed by a practical capstone.

```text
01 — Modern Application Architecture and Attack Surface

02 — Single-Page Applications and JavaScript-Driven Testing

03 — API Discovery and Endpoint Mapping

04 — REST API Testing with Burp Suite

05 — Authentication and Token Testing in Modern Applications

06 — Authorization and Access Control Testing in Modern Applications

07 — Business Logic Testing with Burp Suite

08 — Race Conditions and Concurrency Testing with Burp Suite

09 — File Upload and File Handling Testing with Burp Suite

10 — WebSocket Testing with Burp Suite

11 — GraphQL Testing with Burp Suite

12 — Modern Application Testing Capstone
```

* Including this `README.md`:

  ```text
  13 Total Files

  12 Lessons

  1 Module README
  ```

---

## Module Progress

| File | Topic                                                         |   Status   |
| ---: | ------------------------------------------------------------- | :--------: |
|    1 | Module README                                                 | ✅ Complete |
|    2 | Modern Application Architecture and Attack Surface            | ✅ Complete |
|    3 | Single-Page Applications and JavaScript-Driven Testing        | ✅ Complete |
|    4 | API Discovery and Endpoint Mapping                            | ✅ Complete |
|    5 | REST API Testing with Burp Suite                              | ✅ Complete |
|    6 | Authentication and Token Testing in Modern Applications       | ✅ Complete |
|    7 | Authorization and Access Control Testing in Modern Applications | ✅ Complete |
|    8 | Business Logic Testing with Burp Suite                        | ✅ Complete |
|    9 | Race Conditions and Concurrency Testing with Burp Suite       | ✅ Complete |
|   10 | File Upload and File Handling Testing with Burp Suite         | ✅ Complete |
|   11 | WebSocket Testing with Burp Suite                             | ✅ Complete |
|   12 | GraphQL Testing with Burp Suite                               | ✅ Complete |
|   13 | Modern Application Testing Capstone                           | ✅ Complete |

```text
█████████████ 13 / 13 Files

✅ MODULE COMPLETE
```

---

## Part 1 — Architecture and Attack-Surface Discovery

* Before testing individual vulnerabilities, understand what you are testing.

* Identify:

  ```text
  Frontend Type

  ↓

  Backend Interfaces

  ↓

  API Styles

  ↓

  Authentication Model

  ↓

  Services

  ↓

  Data Flows

  ↓

  Trust Boundaries
  ```

* Architecture determines which security questions matter.

* Important components may include:

  * Browser frontends.
  * Single-page applications.
  * REST APIs.
  * GraphQL.
  * WebSockets.
  * API gateways.
  * Microservices.
  * Authentication services.
  * Storage services.
  * Third-party integrations.
  * Cloud services.

---

## Single-Page Applications

* Single-page applications commonly use frameworks such as:

  ```text
  React

  Angular

  Vue

  Other JavaScript Frameworks
  ```

* The browser often loads an application shell and then communicates with backend APIs dynamically.

* Burp Suite becomes especially valuable because it exposes communication that may not be obvious from the rendered interface.

---

## SPA Testing Model

```text
Browser Loads Application

↓

JavaScript Executes

↓

API Requests Generated

↓

Burp Captures Requests

↓

Endpoints Identified

↓

Parameters and Objects Mapped

↓

Security Boundaries Tested
```

---

## JavaScript as an Attack-Surface Source

* JavaScript files may reveal:

  ```text
  API Endpoints

  Hidden Routes

  Parameter Names

  Feature Flags

  API Versions

  WebSocket URLs

  GraphQL Endpoints

  Internal Service References

  Client-Side Validation

  Role Logic
  ```

* JavaScript analysis should complement:

  ```text
  HTTP History

  +

  Target / Site Map

  +

  Application Browsing

  +

  API Documentation

  +

  Observed Runtime Traffic
  ```

---

## Part 2 — API Discovery and Endpoint Mapping

* API testing begins with inventory.

* You cannot systematically test functionality that you have not discovered and understood.

* Discovery sources may include:

  ```text
  HTTP History

  Site Map

  JavaScript

  HTML

  API Documentation

  Mobile Traffic

  Error Messages

  Network Requests

  Historical Endpoints

  Versioned Routes

  GraphQL Operations

  WebSocket Handshakes
  ```

---

## API Inventory

* Build a structured inventory.

| Method | Endpoint           | Authentication | Role  | Object | Purpose      |
| ------ | ------------------ | -------------- | ----- | ------ | ------------ |
| GET    | `/api/users/me`    | Bearer         | User  | User   | Profile      |
| GET    | `/api/orders/{id}` | Bearer         | User  | Order  | Read order   |
| PATCH  | `/api/orders/{id}` | Bearer         | User  | Order  | Update order |
| POST   | `/api/files`       | Bearer         | User  | File   | Upload       |
| POST   | `/api/admin/users` | Bearer         | Admin | User   | Admin action |

---

## API Mapping Principle

* Do not record only:

  ```text
  Endpoint
  ```

* Record:

  ```text
  Endpoint

  +

  Method

  +

  Authentication

  +

  Role

  +

  Object

  +

  Parameters

  +

  Content Type

  +

  State Change

  +

  Business Purpose
  ```

* This turns a URL list into a security-testing map.

---

## Hidden and Undocumented Functionality

* Modern applications may contain endpoints that are:

  ```text
  Undocumented

  Deprecated

  Hidden From UI

  Used by Older Clients

  Used by Mobile Applications

  Used Internally

  Feature-Flagged
  ```

* Hidden does not mean protected.

* Discovery alone is not a vulnerability.

* The security question is whether discovered functionality exposes a security boundary that is incorrectly enforced.

---

## API Version Comparison

* Multiple versions may coexist:

  ```text
  /api/v1/

  /api/v2/
  ```

* Compare security behavior where appropriate.

* Ask:

  ```text
  Does the Older Version Enforce the Same Authentication?

  Does It Enforce the Same Authorization?

  Does It Expose More Data?

  Does It Accept Deprecated Properties?

  Does It Preserve Unsafe Legacy Behavior?
  ```

---

## Part 3 — REST API Testing

* REST APIs commonly use:

  ```text
  GET

  POST

  PUT

  PATCH

  DELETE
  ```

* Common representations include:

  ```text
  application/json

  application/x-www-form-urlencoded

  multipart/form-data
  ```

* Testing should evaluate security controls independently across relevant:

  ```text
  Resources

  Methods

  Objects

  Roles

  Properties

  Representations

  Workflows
  ```

---

## REST Security Questions

* Ask:

  ```text
  Is Authentication Required?

  Is the Correct User Authorized?

  Is the Correct Role Authorized?

  Does the User Own the Object?

  Can Restricted Properties Be Read?

  Can Restricted Properties Be Modified?

  Does the Response Expose Excessive Data?

  Are State Transitions Enforced?

  Are Alternate Supported Methods Handled Safely?

  Are Alternate Supported Content Types Handled Consistently?
  ```

---

## HTTP Methods and Content Types

* Security behavior may vary across methods such as:

  ```text
  POST

  PUT

  PATCH

  DELETE
  ```

* Or representations such as:

  ```text
  application/json

  application/x-www-form-urlencoded

  multipart/form-data
  ```

* Alternate representations may reach different parsing, validation, or routing paths.

* Do not change methods or content types randomly.

* First understand:

  ```text
  What the Endpoint Supports

  ↓

  What Security Control Is Expected

  ↓

  Whether an Alternate Supported Representation Changes Enforcement
  ```

---

## Part 4 — Authentication and Token Testing

* Modern applications may use:

  ```text
  Session Cookies

  Bearer Tokens

  JWTs

  OAuth Tokens

  API Keys

  Refresh Tokens

  Multiple Authentication Layers
  ```

* Identify exactly what proves identity and how that identity changes throughout the session lifecycle.

---

## Authentication Lifecycle

```text
Login

↓

Credential Validation

↓

Session / Access Token Issued

↓

Authenticated Requests

↓

Token or Session Changes

↓

Refresh / Renewal

↓

Logout / Revocation

↓

Post-Logout Validation
```

* Test the complete lifecycle rather than examining one token in isolation.

---

## Authentication Questions

* Investigate:

  ```text
  How Is Identity Established?

  Where Is Authentication State Stored?

  How Long Is It Valid?

  How Is It Refreshed?

  What Happens After Logout?

  What Happens After Password Change?

  What Happens After Revocation?

  Are Old Credentials Still Accepted?

  Are Authentication Controls Consistent Across Interfaces?
  ```

---

## Part 5 — Authorization and Access Control

* Authorization is one of the highest-value areas in modern application testing.

* Test:

  ```text
  Object-Level Authorization

  Function-Level Authorization

  Property-Level Authorization

  Tenant Boundaries

  Role Boundaries

  Workflow Authorization
  ```

---

## Object-Level Authorization

* A valid authenticated user should not automatically gain access to every object.

* Mental model:

  ```text
  Valid User

  +

  Unauthorized Object

  =

  Object Authorization Test
  ```

* Reliable testing requires:

  ```text
  Controlled Users

  +

  Known Object Ownership

  +

  Preserved Baselines

  +

  One Controlled Change

  +

  Final-State Verification
  ```

---

## Function-Level Authorization

* Function-level authorization concerns access to actions outside the user's intended privilege level.

* Mental model:

  ```text
  Lower-Privilege User

  +

  Higher-Privilege Function

  =

  Function Authorization Test
  ```

* A hidden button or route is not an authorization control.

* Authorization must be enforced server-side.

---

## Property-Level Authorization

* Modern APIs frequently accept and return structured objects.

* Example:

  ```json
  {
    "name": "Example",
    "email": "user@example.test"
  }
  ```

* Security questions include:

  ```text
  Which Properties Can the User Read?

  Which Properties Can the User Modify?

  Which Properties Exist but Are Hidden?

  Does the Backend Enforce Property Permissions?

  Can Privileged Fields Be Supplied Directly?

  Does the Response Expose Unnecessary Sensitive Fields?
  ```

---

## Mass Assignment

* Structured request bodies may contain properties that map to backend objects.

* The security risk appears when the server accepts security-sensitive properties that the current user should not control.

* Test based on evidence rather than blindly adding arbitrary fields.

* Relevant questions include:

  ```text
  Which Fields Are Client-Controlled?

  Which Fields Are Server-Controlled?

  Are Hidden Fields Accepted?

  Are Privileged Properties Writable?

  Are Nested Properties Authorized Independently?
  ```

---

## Part 6 — Business Logic Testing

* Business-logic vulnerabilities occur when technically valid requests violate intended business rules.

* Examples of security-sensitive rules may involve:

  ```text
  Workflow Order

  One-Time Actions

  Credits

  Discounts

  Coupons

  Inventory

  Account State

  Approval Requirements

  Role Transitions

  Transaction Limits
  ```

---

## Security Invariants

* Express important rules as invariants.

* Examples:

  ```text
  Coupon Redemption Count ≤ 1
  ```

  ```text
  Account Balance Must Not Become Invalid
  ```

  ```text
  User Must Complete Required Step Before Final Action
  ```

  ```text
  Only Authorized Role May Approve Operation
  ```

* Then design controlled tests around whether the server consistently preserves those rules.

---

## Rate Limits

* Rate limiting may protect:

  ```text
  Authentication Attempts

  OTP Verification

  Password Recovery

  API Quotas

  Resource Creation

  Expensive Operations

  Abuse-Sensitive Workflows
  ```

* Rate-limit testing should be controlled.

* Do not generate unnecessary production load.

* Test only within authorization and program rules.

---

## Part 7 — Race Conditions and Concurrency

* Some security rules fail only when multiple operations overlap.

* Race-condition testing focuses on whether concurrent requests can violate an invariant that sequential requests preserve.

* Potential candidates include:

  ```text
  One-Time Actions

  Coupon Redemption

  Balance Updates

  Inventory Changes

  Limit Enforcement

  State Transitions

  Duplicate Processing
  ```

---

## Race-Condition Principle

```text
Define Invariant

↓

Establish Sequential Baseline

↓

Identify Concurrency Candidate

↓

Synchronize Controlled Requests

↓

Observe Responses

↓

Verify Final State

↓

Repeat Carefully

↓

Validate Impact
```

* Response anomalies alone are insufficient.

* Always verify the resulting server-side state.

---

## Part 8 — File Upload and File Handling

* File security extends beyond the initial upload request.

* Think in terms of a lifecycle:

  ```text
  Upload

  ↓

  Validation

  ↓

  Storage

  ↓

  Processing

  ↓

  Retrieval

  ↓

  Rendering / Execution Context

  ↓

  Deletion / Retention
  ```

* Security controls may fail at any stage.

---

## File-Handling Questions

* Investigate:

  ```text
  Who Can Upload?

  What File Types Are Accepted?

  How Is Type Determined?

  How Are Filenames Handled?

  Where Is Content Stored?

  Is Content Processed?

  Who Can Retrieve It?

  Is Retrieval Authorized?

  How Is Content Rendered?

  Are Files Renamed?

  Are Metadata and Derived Files Protected?

  What Happens During Deletion?
  ```

---

## Part 9 — WebSocket Testing

* Modern applications may use WebSockets for:

  ```text
  Chat

  Notifications

  Collaboration

  Trading

  Games

  Live Dashboards

  Real-Time Updates

  GraphQL Subscriptions
  ```

* Security controls may exist at both connection and message levels.

---

## WebSocket Security Model

```text
HTTP Upgrade Handshake

↓

Connection Authentication

↓

Persistent WebSocket Connection

↓

Client-to-Server Messages

↓

Server-to-Client Messages

↓

Authorization and Validation

↓

State Changes / Subscriptions / Events
```

---

## WebSocket Security Principle

* A valid connection does not automatically authorize every message.

* Test:

  ```text
  Connection Authentication

  Message Authorization

  Object Ownership

  Function Authorization

  Subscription Authorization

  Session Expiry

  Logout Behavior

  Origin Handling

  Message Validation

  Replay Behavior

  State and Sequencing

  Server-Pushed Data
  ```

---

## Cross-Origin WebSocket Considerations

* WebSocket handshakes may involve browser credentials and the `Origin` header.

* Evaluate:

  ```text
  Origin Validation

  Cookie Behavior

  SameSite Context

  Authentication Requirements

  Sensitive Actions

  Sensitive Server-Pushed Data
  ```

* Weak Origin validation alone does not automatically prove exploitability.

* Validate the complete browser, credential, and application context.

---

## WebSocket Data Flow

* User-controlled WebSocket data may eventually reach:

  ```text
  HTML Rendering

  Databases

  Templates

  Logs

  Notifications

  Other Users

  Other Protocols
  ```

* Trace the complete data flow.

* Security depends on:

  ```text
  Input Validation

  Storage

  Transformation

  Output Context

  Encoding

  Sanitization
  ```

---

## Part 10 — GraphQL Testing

* GraphQL may expose a single endpoint such as:

  ```text
  /graphql
  ```

* But that endpoint may support many:

  ```text
  Queries

  Mutations

  Subscriptions

  Objects

  Fields

  Relationships

  Resolvers
  ```

* Therefore:

  ```text
  One URL

  ≠

  Small Attack Surface
  ```

---

## GraphQL Testing Model

```text
Identify Endpoint

↓

Capture Legitimate Operations

↓

Build Operation Inventory

↓

Map Variables

↓

Map Objects and Fields

↓

Understand Schema Exposure

↓

Test Authentication

↓

Test Object Authorization

↓

Test Field Authorization

↓

Test Mutation Authorization

↓

Test Input Objects

↓

Test Business Logic

↓

Analyze GraphQL-Specific Behavior

↓

Verify Final State
```

---

## GraphQL Security Questions

* Investigate:

  ```text
  Which Queries Exist?

  Which Mutations Exist?

  Which Objects Can Be Accessed?

  Which Fields Are Sensitive?

  Which Variables Control Object Selection?

  Is Authorization Enforced Per Object?

  Is Authorization Enforced Per Field?

  Are Mutations Authorized?

  Are Nested Relationships Authorized?

  Are Input Objects Safely Handled?

  Are Custom Scalars Validated Semantically?

  Are Filters Authorization-Safe?

  Does CORS Behavior Match the Intended Trust Model?

  Are Errors Excessively Informative?

  Are Complexity Controls Appropriate?
  ```

---

## Part 11 — Trust Boundaries and Backend Communication

* A modern application may involve:

  ```text
  Browser

  ↓

  API Gateway

  ↓

  Authentication Service

  ↓

  User Service

  ↓

  Order Service

  ↓

  Payment Service
  ```

* Each service boundary introduces trust assumptions.

---

## Trust-Boundary Questions

* Ask:

  ```text
  Which Component Authenticates the User?

  Which Component Authorizes the Action?

  Where Is Identity Established?

  How Is Identity Propagated?

  Are Security-Sensitive Headers Trusted?

  Can Client-Controlled Data Reach Internal Trust Boundaries?

  Are Internal Interfaces Externally Reachable?

  Are Security Controls Consistent Across Services?

  Does One Service Incorrectly Trust Another?
  ```

---

## Part 12 — Modern Application Testing Capstone

* The capstone combines the module into one structured assessment.

* The objective is not random vulnerability hunting.

* The objective is to demonstrate a repeatable investigation process:

  ```text
  Scope

  ↓

  Architecture

  ↓

  Attack Surface

  ↓

  Identity

  ↓

  Objects

  ↓

  Functions

  ↓

  Properties

  ↓

  Workflows

  ↓

  Real-Time Interfaces

  ↓

  Security Hypotheses

  ↓

  Controlled Tests

  ↓

  Validation

  ↓

  Evidence
  ```

---

## Burp Suite Tools Used in This Module

* Depending on the investigation, you may combine:

  ```text
  Proxy

  +

  HTTP History

  +

  Target / Site Map

  +

  Repeater

  +

  Intruder

  +

  Decoder

  +

  Comparer

  +

  Logger

  +

  WebSocket History

  +

  Scanner Where Appropriate

  +

  Extensions Where Useful
  ```

* The goal is not to use every tool on every endpoint.

* Choose the tool that best answers the current security question.

---

## Modern Application Investigation Workflow

```text
Define Scope

↓

Browse Application

↓

Capture Traffic

↓

Identify Architecture

↓

Map APIs and Interfaces

↓

Analyze JavaScript

↓

Identify Authentication Model

↓

Create Controlled Identities

↓

Build Endpoint Inventory

↓

Identify Objects, Roles, Tenants, and Properties

↓

Establish Baselines

↓

Test Authentication

↓

Test Authorization

↓

Test Business Rules

↓

Investigate Concurrency Candidates

↓

Review File Handling

↓

Investigate WebSockets

↓

Investigate GraphQL

↓

Compare Versions and Interfaces Where Relevant

↓

Apply Controlled Automation

↓

Verify Final State

↓

Validate Findings

↓

Preserve Evidence
```

---

## Evidence Requirements

* For every candidate vulnerability, preserve enough context to answer:

  ```text
  What Endpoint or Interface Was Tested?

  What Method or Operation Was Used?

  What Authentication Context Was Active?

  Which User Performed the Action?

  What Role Did the User Have?

  Which Tenant Did the User Belong To?

  Which Object Was Accessed?

  Who Owned the Object?

  What Was the Baseline?

  What Exactly Changed?

  What Response Was Observed?

  What Was the State Before?

  What Was the State After?

  What Security Rule Was Expected?

  What Actually Happened?

  Can the Result Be Reproduced?

  What Is the Security Impact?
  ```

---

## Investigation Journal Template

```text
Application:

Scope:

Architecture:

Frontend:

API Style:

API Base Paths:

Authentication:

Token / Session Type:

Roles:

Tenants:

Important Objects:

REST Endpoints:

GraphQL Endpoint:

GraphQL Operations:

WebSocket Endpoints:

JavaScript Findings:

Hidden Endpoints:

API Versions:

High-Risk Functions:

Authorization Boundaries:

Property Boundaries:

Business Invariants:

Race Candidates:

File Handling:

Rate Limits:

Trust Boundaries:

Interesting Requests:

Candidate Findings:

Confirmed Findings:

Rejected Hypotheses:

False Positives:

Evidence:

Follow-Up:
```

---

## Professional Tips

* Map the architecture before testing individual vulnerabilities.
* Treat the frontend as an untrusted client rather than a security boundary.
* Build an endpoint inventory instead of relying only on normal browsing.
* Analyze JavaScript because the visible UI may not expose the complete attack surface.
* Record identity, role, tenant, and object ownership for authorization tests.
* Test read, create, update, and delete permissions independently where applicable.
* Distinguish authentication failures from authorization failures.
* Test object, function, and property authorization separately.
* Compare API versions when multiple versions are legitimately exposed.
* Do not assume one HTTP method's security behavior applies to another.
* Understand supported content types before testing parser differences.
* Express important business rules as explicit invariants.
* Establish sequential baselines before investigating concurrency.
* Trace uploaded files through their complete lifecycle.
* Treat WebSocket connection authorization and message authorization as separate questions.
* Map GraphQL operations, objects, fields, variables, and mutations systematically.
* Verify actual server-side state after state-changing tests.
* Keep controlled user accounts and test objects clearly separated.
* Establish baselines before modifying requests.
* Change one security-relevant variable at a time whenever practical.
* Use automation for repetition, not security reasoning.
* Validate important automated findings manually.
* Preserve reproducible request, response, and state evidence.

---

## Common Mistakes

* Treating an API as merely a collection of URLs.
* Testing without first understanding the application's architecture.
* Assuming frontend restrictions provide backend security.
* Ignoring JavaScript as an attack-surface source.
* Testing authorization with uncontrolled or unknown objects.
* Confusing authentication with authorization.
* Testing only read operations.
* Ignoring update, delete, and state-changing authorization.
* Ignoring property-level authorization.
* Assuming hidden fields cannot be submitted.
* Ignoring older API versions where they remain legitimately exposed.
* Changing methods or content types without understanding endpoint behavior.
* Treating one GraphQL endpoint as a small attack surface.
* Assuming an authenticated WebSocket connection authorizes every message.
* Ignoring server-pushed WebSocket data.
* Testing business logic without defining the expected invariant.
* Testing race conditions without first establishing a sequential baseline.
* Treating an upload response as the end of file-security testing.
* Testing rate limits without controlling request volume.
* Reporting unusual behavior without demonstrating a security boundary violation.
* Running broad automation before understanding endpoint side effects.
* Failing to verify server-side state after a request.
* Ignoring tenant boundaries in multi-tenant applications.
* Treating every anomaly as a vulnerability.

---

## Practice Tasks

### Task 1 — Select an Authorized Application

* Select:

  * A deliberately vulnerable application.
  * A local lab.
  * A training environment.
  * Or another system where testing is explicitly authorized.

---

### Task 2 — Identify the Architecture

* Determine whether the application uses:

  ```text
  Traditional Server Rendering

  SPA

  REST

  GraphQL

  WebSockets

  Microservices

  or

  Multiple Architectures
  ```

---

### Task 3 — Capture Traffic

* Browse the application through Burp Suite.

* Record:

  ```text
  Hosts

  Endpoints

  Methods

  Content Types

  Authentication

  Interesting Objects
  ```

---

### Task 4 — Build an API Inventory

* Record:

  ```text
  Method

  Endpoint

  Authentication

  Role

  Object

  Parameters

  State Change

  Purpose
  ```

---

### Task 5 — Analyze JavaScript

* Identify examples of:

  ```text
  API Routes

  Hidden Routes

  Parameter Names

  API Versions

  GraphQL Endpoints

  WebSocket URLs

  Feature References
  ```

---

### Task 6 — Map Authentication

* Determine:

  ```text
  Login Mechanism

  Session / Token Type

  Refresh Behavior

  Logout Behavior

  Revocation Behavior
  ```

---

### Task 7 — Map Authorization

* Identify:

  ```text
  Users

  Roles

  Tenants

  Objects

  Object Owners

  Privileged Functions

  Sensitive Properties
  ```

---

### Task 8 — Identify Business Invariants

* Record at least three rules that should always hold.

* Example:

  ```text
  User May Access Only Authorized Objects

  One-Time Operation May Execute Only Once

  Restricted State Transition Requires Required Preconditions
  ```

---

### Task 9 — Identify Specialized Interfaces

* Look for:

  ```text
  GraphQL

  WebSockets

  File Uploads

  Versioned APIs

  Hidden or Undocumented Routes
  ```

---

### Task 10 — Build an Architecture Sketch

```text
Client

↓

Frontend

↓

Interfaces

↓

Gateway / Backend

↓

Services

↓

Data / External Systems
```

---

### Task 11 — Generate Security Hypotheses

* Create hypotheses based on observed architecture and functionality.

* Avoid random payload testing.

---

### Task 12 — Complete the Capstone

* Apply the complete methodology:

  ```text
  Map

  ↓

  Baseline

  ↓

  Hypothesize

  ↓

  Test

  ↓

  Compare

  ↓

  Verify

  ↓

  Validate

  ↓

  Document
  ```

---

## Interview Questions

1. Why do modern applications require a different security-testing approach from traditional server-rendered applications?
2. What is a single-page application?
3. Why is the frontend not a reliable security boundary?
4. Why should JavaScript files be analyzed during application mapping?
5. What information can JavaScript reveal about an application's attack surface?
6. How would you build an API inventory?
7. Why should an API inventory contain more than endpoint URLs?
8. What trust boundaries should be identified in a modern application?
9. What is the difference between authentication and authorization?
10. How would you test an authentication lifecycle?
11. What is object-level authorization?
12. What is function-level authorization?
13. What is property-level authorization?
14. What is mass assignment?
15. Why should controlled identities and known object ownership be used during authorization testing?
16. Why should API methods sometimes be evaluated independently?
17. How can content-type differences affect backend processing?
18. Why can older API versions be security-relevant?
19. Why are hidden or undocumented endpoints relevant during attack-surface mapping?
20. What is a business-logic invariant?
21. How would you identify a race-condition candidate?
22. Why must final server-side state be verified during concurrency testing?
23. Why should file uploads be treated as a lifecycle rather than a single request?
24. Why must WebSocket authorization often be tested at the message level?
25. What is Cross-Site WebSocket Hijacking conceptually?
26. Why can GraphQL expose a large attack surface through one endpoint?
27. What is the difference between GraphQL object-level and field-level authorization?
28. Why are GraphQL mutations security-sensitive?
29. What security risks can arise at microservice trust boundaries?
30. Why should rate-limit testing be controlled?
31. Why is baseline-first testing important?
32. Why should one variable be changed at a time during controlled testing?
33. How should automation be used during modern application testing?
34. What distinguishes an anomaly from a validated vulnerability?
35. What evidence is required to demonstrate a modern application security vulnerability professionally?

---

## Quick Revision

* Modern application:

  ```text
  UI

  +

  JavaScript

  +

  APIs

  +

  Tokens

  +

  Services

  +

  Real-Time Communication
  ```

* Core mindset:

  ```text
  Map Architecture

  Before

  Testing Vulnerabilities
  ```

* Frontend:

  ```text
  Client

  ≠

  Security Boundary
  ```

* API inventory:

  ```text
  Method

  +

  Endpoint

  +

  Authentication

  +

  Role

  +

  Object

  +

  Purpose
  ```

* Authentication:

  ```text
  Login

  →

  Session / Token

  →

  Refresh

  →

  Revocation

  →

  Logout
  ```

* Object authorization:

  ```text
  Valid Identity

  +

  Unauthorized Object
  ```

* Function authorization:

  ```text
  Lower Privilege

  +

  Unauthorized Function
  ```

* Property authorization:

  ```text
  Can This User Read or Modify This Field?
  ```

* Business logic:

  ```text
  Define Invariant

  ↓

  Test Whether Server Preserves It
  ```

* Race condition:

  ```text
  Sequentially Safe

  ≠

  Concurrently Safe
  ```

* File handling:

  ```text
  Upload

  →

  Validate

  →

  Store

  →

  Process

  →

  Retrieve
  ```

* WebSockets:

  ```text
  Authenticated Connection

  ≠

  Authorized Message
  ```

* GraphQL:

  ```text
  One Endpoint

  ≠

  One Function
  ```

* Hidden endpoint:

  ```text
  Not Visible

  ≠

  Not Reachable

  ≠

  Automatically Vulnerable
  ```

* Automation:

  ```text
  Automate Repetition

  Not Reasoning
  ```

* Professional workflow:

  ```text
  Architecture

  ↓

  Attack Surface

  ↓

  Identity

  ↓

  Objects

  ↓

  Functions

  ↓

  Properties

  ↓

  Workflows

  ↓

  Validation
  ```

---

## Key Takeaways

* Modern application testing requires understanding architecture before testing individual vulnerabilities.
* The visible frontend often represents only part of the true attack surface.
* JavaScript, APIs, WebSockets, GraphQL, mobile backends, versioned routes, and hidden functionality may expose important application behavior.
* The frontend must be treated as an untrusted client rather than a security control.
* Build structured inventories containing methods, endpoints, authentication, roles, objects, properties, and business purposes.
* REST API testing should evaluate authentication, authorization, methods, representations, state, and workflow behavior.
* Authentication testing should consider the complete session and token lifecycle.
* Authorization should be evaluated at object, function, property, role, and tenant boundaries.
* Controlled users and known object ownership are essential for reliable authorization testing.
* Business-logic testing should begin by defining explicit security invariants.
* Race-condition testing should compare sequential behavior with carefully controlled concurrent behavior and verify final state.
* File security must be evaluated across upload, validation, storage, processing, retrieval, rendering, and deletion.
* WebSocket security must often be evaluated at both connection and individual-message levels.
* GraphQL requires mapping operations, objects, fields, variables, inputs, mutations, and authorization boundaries rather than focusing only on the endpoint URL.
* Hidden endpoints and older API versions may preserve functionality or security behavior no longer visible through the current UI.
* Alternate methods and content types should be investigated when supported behavior suggests different backend processing paths.
* Microservice architectures introduce trust boundaries between gateways, identity systems, services, and backend components.
* Rate-limit testing should be controlled and aligned with authorization and rules of engagement.
* Burp Suite should be used as an integrated investigation platform rather than as isolated individual tools.
* Unexpected behavior is not automatically a vulnerability.
* The professional methodology is:

  ```text
  Architecture

  ↓

  Mapping

  ↓

  Baselines

  ↓

  Security Hypotheses

  ↓

  Controlled Testing

  ↓

  Final-State Verification

  ↓

  Impact Validation

  ↓

  Evidence
  ```
