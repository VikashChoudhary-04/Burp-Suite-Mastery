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

* These architectures change how penetration testers discover attack surfaces and investigate security boundaries.

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

* Effective testing therefore requires understanding the application's underlying communication model.

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

  Discover Hidden Interfaces

  ↓

  Identify Trust Boundaries

  ↓

  Analyze Client-Server Communication

  ↓

  Test Authentication

  ↓

  Test Authorization

  ↓

  Test Data and Workflow Boundaries

  ↓

  Validate Security Impact
  ```

---

## Learning Objectives

* By completing this module, you will be able to:

  * Recognize modern web application architectures.
  * Understand single-page application communication.
  * Discover API endpoints through Burp and JavaScript.
  * Map REST APIs systematically.
  * Investigate GraphQL applications.
  * Analyze JWT and token-based authentication in context.
  * Test API authentication boundaries.
  * Test object-level authorization.
  * Test function-level authorization.
  * Investigate property-level authorization.
  * Identify mass-assignment risks.
  * Analyze excessive data exposure.
  * Compare API versions.
  * Test API methods and content types.
  * Investigate WebSocket-based functionality.
  * Understand microservice trust boundaries.
  * Analyze frontend security assumptions.
  * Investigate hidden and undocumented endpoints.
  * Test multi-step API workflows.
  * Evaluate rate-limiting behavior safely.
  * Combine Burp tools into modern application testing workflows.
  * Produce reproducible evidence for modern application vulnerabilities.

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

* Burp allows you to communicate directly with the backend interface and test what the server actually enforces.

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
  Normal User Cannot Call Admin Endpoint
  ```

* The real test is:

  ```text
  Capture Privileged Request

  ↓

  Understand Endpoint

  ↓

  Replay With Lower-Privilege Identity

  ↓

  Verify Server-Side Authorization
  ```

---

## Module Structure

* This module progresses from architecture discovery to advanced modern application testing.

```text
01 — Modern Application Architecture & Attack Surface

02 — Single-Page Applications & JavaScript-Driven Testing

03 — API Discovery & Endpoint Mapping

04 — REST API Testing with Burp Suite

05 — API Authentication & Token Workflows

06 — API Authorization: BOLA, BFLA & Object Boundaries

07 — Mass Assignment & Property-Level Authorization

08 — GraphQL Testing with Burp Suite

09 — WebSocket Security Testing

10 — Hidden Endpoints, API Versions & Undocumented Functionality

11 — Content Types, HTTP Methods & Parser Behavior

12 — Rate Limits, Workflow Abuse & API Business Logic

13 — Microservices, Trust Boundaries & Backend Communication

14 — Modern Application Testing Workflow

```

* Including this `README.md`:

  ```text
  15 Total Files
  ```

---

## Module Progress

| File | Topic                                                       |   Status   |
| ---: | ----------------------------------------------------------- | :--------: |
|    1 | Module README                                               | ✅ Complete |
|    2 | Modern Application Architecture & Attack Surface            |  ⏳ Planned |
|    3 | Single-Page Applications & JavaScript-Driven Testing        |  ⏳ Planned |
|    4 | API Discovery & Endpoint Mapping                            |  ⏳ Planned |
|    5 | REST API Testing with Burp Suite                            |  ⏳ Planned |
|    6 | API Authentication & Token Workflows                        |  ⏳ Planned |
|    7 | API Authorization: BOLA, BFLA & Object Boundaries           |  ⏳ Planned |
|    8 | Mass Assignment & Property-Level Authorization              |  ⏳ Planned |
|    9 | GraphQL Testing with Burp Suite                             |  ⏳ Planned |
|   10 | WebSocket Security Testing                                  |  ⏳ Planned |
|   11 | Hidden Endpoints, API Versions & Undocumented Functionality |  ⏳ Planned |
|   12 | Content Types, HTTP Methods & Parser Behavior               |  ⏳ Planned |
|   13 | Rate Limits, Workflow Abuse & API Business Logic            |  ⏳ Planned |
|   14 | Microservices, Trust Boundaries & Backend Communication     |  ⏳ Planned |
|   15 | Modern Application Testing Workflow                         |  ⏳ Planned |

---

## Part 1 — Architecture Discovery

* Before testing vulnerabilities, understand what you are testing.

* Identify:

  ```text
  Frontend Type

  ↓

  Backend Interfaces

  ↓

  API Style

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

* Burp becomes especially valuable because it exposes the communication hidden behind the interface.

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

* JavaScript discovery should therefore complement HTTP history and Site Map analysis.

---

## Part 2 — API Discovery

* API testing begins with inventory.

* You cannot test what you have not discovered.

* Sources include:

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

  State Change

  +

  Business Purpose
  ```

* This turns a URL list into a security-testing map.

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

* And data formats such as:

  ```text
  JSON

  Form Data

  Multipart Data
  ```

* Testing should examine security controls independently across methods and resources.

---

## REST Security Questions

* Ask:

  ```text
  Is Authentication Required?

  Is the Correct User Authorized?

  Is the Correct Role Authorized?

  Does the User Own the Object?

  Can Restricted Properties Be Modified?

  Does the Response Expose Excessive Data?

  Are State Transitions Enforced?

  Are Alternate Methods Handled Safely?
  ```

---

## Part 4 — Authentication

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

* Identify exactly what proves identity.

---

## Authentication Mapping

```text
Login

↓

Access Token Issued

↓

API Requests

↓

Token Expires

↓

Refresh Token Used

↓

New Access Token

↓

Logout / Revocation
```

* Test the complete lifecycle rather than examining one token in isolation.

---

## Part 5 — Authorization

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

## BOLA

* Broken Object Level Authorization occurs when a user can access an object they should not be authorized to access.

* Mental model:

  ```text
  Valid User

  +

  Unauthorized Object

  =

  Authorization Test
  ```

* Example:

  ```text
  GET /api/orders/1001
  ```

* Change only the object identifier using controlled accounts and verify server behavior.

---

## BFLA

* Broken Function Level Authorization concerns access to functions outside the user's intended privilege level.

* Mental model:

  ```text
  Lower-Privilege User

  +

  Higher-Privilege Function

  =

  Function Authorization Test
  ```

---

## Property-Level Authorization

* Modern APIs frequently accept structured objects.

* Example:

  ```json
  {
    "name": "Example",
    "email": "user@example.test"
  }
  ```

* Security questions include:

  ```text
  Which Properties Can the User Modify?

  Which Properties Exist but Are Hidden?

  Does the Backend Enforce Property Permissions?

  Can Privileged Fields Be Supplied Directly?
  ```

---

## Part 6 — GraphQL

* GraphQL may expose a single endpoint such as:

  ```text
  /graphql
  ```

* But that endpoint may support many:

  ```text
  Queries

  Mutations

  Objects

  Fields

  Relationships
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

Understand Operations

↓

Map Queries and Mutations

↓

Identify Objects and Fields

↓

Test Authentication

↓

Test Object Authorization

↓

Test Function Authorization

↓

Test Field-Level Exposure

↓

Test Workflow Logic
```

---

## Part 7 — WebSockets

* Modern applications may use WebSockets for:

  ```text
  Chat

  Notifications

  Collaboration

  Trading

  Games

  Live Dashboards

  Real-Time Updates
  ```

* Security controls must often exist at the message level.

---

## WebSocket Security Principle

* A valid connection does not automatically authorize every message.

* Test:

  ```text
  Connection Authentication

  Message Authorization

  Object Ownership

  Privileged Message Types

  Session Expiry

  Logout Behavior

  Origin Handling
  ```

---

## Part 8 — Hidden Functionality

* Modern applications often contain endpoints that are:

  ```text
  Undocumented

  Deprecated

  Hidden From UI

  Used by Older Clients

  Used by Mobile Applications

  Used Internally

  Feature-Flagged
  ```

* Hidden does not mean inaccessible.

---

## Version Comparison

* Compare:

  ```text
  /api/v1/

  vs

  /api/v2/
  ```

* Ask:

  ```text
  Does the Older Version Enforce the Same Authentication?

  Does It Enforce the Same Authorization?

  Does It Expose More Data?

  Does It Accept Deprecated Properties?

  Does It Support Unsafe Legacy Behavior?
  ```

---

## Part 9 — HTTP Methods and Content Types

* Security behavior may vary across:

  ```text
  POST

  PUT

  PATCH

  DELETE
  ```

* Or:

  ```text
  application/json

  application/x-www-form-urlencoded

  multipart/form-data
  ```

* Alternate representations may reach different parsing or validation paths.

---

## Testing Principle

* Do not change methods or content types randomly.

* First understand:

  ```text
  What the Endpoint Supports

  ↓

  What Security Control Is Expected

  ↓

  Whether an Alternate Representation Changes Enforcement
  ```

---

## Part 10 — Rate Limits and Business Logic

* APIs frequently enforce rules involving:

  ```text
  Request Frequency

  Quotas

  OTP Attempts

  Coupon Usage

  Credits

  Inventory

  Workflow Order

  One-Time Operations
  ```

* These are stateful security controls.

---

## Business Logic Principle

* Express the rule as an invariant.

* Example:

  ```text
  OTP Attempts ≤ Allowed Limit
  ```

* Or:

  ```text
  Coupon Redemption Count ≤ 1
  ```

* Then test whether controlled request sequences can violate that invariant.

---

## Part 11 — Microservices

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

## Microservice Security Questions

* Ask:

  ```text
  Which Service Authenticates the User?

  Which Service Authorizes the Action?

  Are Identity Headers Trusted?

  Can Client-Controlled Data Reach Internal Trust Boundaries?

  Are Internal Endpoints Externally Reachable?

  Are Security Controls Consistent Across Services?
  ```

---

## Burp Suite Tools Used in This Module

* You will combine:

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

Map APIs

↓

Analyze JavaScript

↓

Identify Authentication Model

↓

Create Controlled Identities

↓

Build Endpoint Inventory

↓

Identify Objects and Roles

↓

Establish Baselines

↓

Test Authentication

↓

Test Authorization

↓

Test Properties

↓

Test Methods and Versions

↓

Test Business Logic

↓

Investigate Real-Time Interfaces

↓

Apply Controlled Automation

↓

Validate Findings

↓

Preserve Evidence
```

---

## Evidence Requirements

* For every candidate vulnerability, preserve:

  ```text
  Endpoint

  Method

  Authentication Context

  User

  Role

  Tenant

  Object

  Object Owner

  Baseline Request

  Modified Request

  Baseline Response

  Modified Response

  State Before

  State After

  Expected Security Rule

  Actual Behavior

  Security Impact
  ```

---

## Investigation Journal Template

```text
Application:

Architecture:

Frontend:

API Style:

API Base Paths:

Authentication:

Token Type:

Roles:

Tenants:

Important Objects:

REST Endpoints:

GraphQL Endpoint:

WebSocket Endpoints:

JavaScript Findings:

Hidden Endpoints:

API Versions:

High-Risk Functions:

Authorization Boundaries:

Business Invariants:

Rate Limits:

Interesting Requests:

Candidate Findings:

Confirmed Findings:

False Positives:

Evidence:

Follow-Up:
```

---

## Professional Tips

* Map the architecture before testing individual vulnerabilities.
* Treat the frontend as an untrusted client rather than a security boundary.
* Build an endpoint inventory instead of relying only on browsing.
* Analyze JavaScript because the visible UI may not expose the complete attack surface.
* Record the identity, role, tenant, and object owner for authorization tests.
* Test read, write, update, and delete permissions independently.
* Compare API versions when multiple versions exist.
* Do not assume one HTTP method's authorization behavior applies to another.
* Verify actual server-side state after state-changing tests.
* Keep controlled user accounts separate.
* Establish baselines before modifying API requests.
* Use automation for repetition, not security reasoning.
* Validate important automated findings manually.
* Preserve reproducible request and response evidence.

---

## Common Mistakes

* Treating an API as merely a collection of URLs.
* Testing without first understanding the application's architecture.
* Assuming frontend restrictions provide backend security.
* Ignoring JavaScript as an attack-surface source.
* Testing authorization with uncontrolled or unknown objects.
* Confusing authentication with authorization.
* Testing only GET requests.
* Ignoring update and delete authorization.
* Ignoring property-level authorization.
* Assuming hidden fields cannot be submitted.
* Ignoring older API versions.
* Treating one GraphQL endpoint as a small attack surface.
* Assuming an authenticated WebSocket connection authorizes every message.
* Testing rate limits without controlling request volume.
* Reporting unusual API behavior without demonstrating security impact.
* Running broad automation before understanding endpoint side effects.
* Failing to verify server-side state after a request.
* Ignoring tenant boundaries in multi-tenant applications.

---

## Practice Tasks

1. Select an authorized modern web application or deliberately vulnerable lab.

2. Identify whether the application uses:

   ```text
   Traditional Server Rendering

   SPA

   REST

   GraphQL

   WebSockets

   or

   Multiple Architectures
   ```

3. Browse the application through Burp.

4. Build an initial API inventory containing:

   ```text
   Method

   Endpoint

   Authentication

   Role

   Object

   Purpose
   ```

5. Identify at least three requests generated dynamically by JavaScript.

6. Determine the application's authentication mechanism.

7. Identify at least one object identifier.

8. Identify at least one state-changing API request.

9. Identify any:

   ```text
   API Versions

   GraphQL Endpoints

   WebSocket Connections

   Hidden Routes
   ```

10. Create an architecture sketch:

```text
Client

↓

Interfaces

↓

Backend

↓

Services / Data
```

11. Record three security questions for later testing.

---

## Interview Questions

1. Why do modern applications require a different security-testing approach from traditional server-rendered applications?
2. What is a single-page application?
3. Why is the frontend not a reliable security boundary?
4. Why should JavaScript files be analyzed during application mapping?
5. What information can JavaScript reveal about an application's attack surface?
6. How would you build an API inventory?
7. Why should an API inventory include more than endpoint URLs?
8. What security boundaries should be identified in a modern application?
9. What is the difference between authentication and authorization in API testing?
10. What is BOLA?
11. What is BFLA?
12. What is property-level authorization?
13. What is mass assignment?
14. Why should API methods be tested independently?
15. Why should multiple API versions be compared?
16. Why can a GraphQL endpoint represent a large attack surface despite using one URL?
17. Why must WebSocket authorization often be tested at the message level?
18. Why are hidden or undocumented endpoints security-relevant?
19. How can content-type differences affect backend behavior?
20. Why should business rules be expressed as invariants?
21. What security risks can arise at microservice trust boundaries?
22. Why should controlled identities be used for authorization testing?
23. Why is baseline-first testing important?
24. How should automation be used during modern application testing?
25. What evidence is required to demonstrate an API authorization vulnerability professionally?

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

* BOLA:

  ```text
  Valid Identity

  +

  Unauthorized Object
  ```

* BFLA:

  ```text
  Lower Privilege

  +

  Unauthorized Function
  ```

* Property authorization:

  ```text
  Can This User Read or Modify This Field?
  ```

* GraphQL:

  ```text
  One Endpoint

  ≠

  One Function
  ```

* WebSockets:

  ```text
  Authenticated Connection

  ≠

  Authorized Message
  ```

* Hidden endpoint:

  ```text
  Not Visible

  ≠

  Not Reachable
  ```

* API versions:

  ```text
  New Security Controls

  Must Not Leave

  Old Versions Exposed
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
* JavaScript, APIs, WebSockets, mobile backends, and hidden routes can expose functionality not visible through normal browsing.
* The frontend must be treated as an untrusted client rather than a security control.
* Build a structured API inventory containing methods, endpoints, authentication, roles, objects, and business purposes.
* REST API testing should evaluate authentication, object authorization, function authorization, property authorization, methods, versions, and workflow state.
* Authorization is one of the highest-value areas of modern application testing.
* Controlled users and known object ownership are essential for reliable BOLA testing.
* GraphQL requires mapping operations, objects, fields, and authorization boundaries rather than focusing only on the endpoint URL.
* WebSocket security must often be evaluated at both the connection and individual-message levels.
* Hidden endpoints and older API versions may preserve functionality or weaker security controls no longer exposed by the current UI.
* Alternate methods and content types should be tested when there is a reason to believe they may reach different backend processing paths.
* Business logic and rate-limit testing should be based on explicit security invariants and controlled request sequences.
* Microservice architectures introduce additional trust boundaries between gateways, services, identity systems, and backend components.
* Burp Suite should be used as an integrated investigation platform rather than as isolated individual tools.
* The professional methodology is **architecture → mapping → baselines → security hypotheses → controlled testing → validation → evidence**.
