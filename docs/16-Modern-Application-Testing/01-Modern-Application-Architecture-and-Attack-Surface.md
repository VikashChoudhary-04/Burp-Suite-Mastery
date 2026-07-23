# Modern Application Architecture & Attack Surface

> **Module 16 — Modern Application Testing**

> **Progress**

```text
██░░░░░░░░░░░ 2 / 13 Files
```

---

## Overview

* Before testing a modern application for vulnerabilities, you need to understand how the application is built and how its components communicate.

* Modern applications may consist of:

  * Browser-based frontends.
  * Single-page applications.
  * REST APIs.
  * GraphQL APIs.
  * WebSocket connections.
  * Authentication services.
  * API gateways.
  * Microservices.
  * Cloud storage.
  * Third-party integrations.
  * Mobile application backends.
  * Internal administrative interfaces.

* The visible website may represent only one part of the complete system.

* A professional tester therefore begins with:

  ```text
  Architecture Discovery

  ↓

  Component Identification

  ↓

  Communication Mapping

  ↓

  Trust Boundary Identification

  ↓

  Attack-Surface Mapping

  ↓

  Security Testing
  ```

* The objective is to understand:

  ```text
  What Exists?

  ↓

  How Does It Communicate?

  ↓

  What Does It Trust?

  ↓

  What Can the User Control?

  ↓

  Where Should Security Controls Exist?
  ```

---

## Learning Objectives

* By the end of this lesson, you will be able to:

  * Understand the basic architecture of modern web applications.
  * Distinguish traditional and modern application architectures.
  * Identify frontend and backend components.
  * Recognize APIs as security-relevant interfaces.
  * Understand the role of API gateways.
  * Recognize microservice architectures.
  * Identify authentication and identity components.
  * Understand client-side and server-side trust boundaries.
  * Map application communication through Burp Suite.
  * Identify first-party and third-party components.
  * Recognize hidden attack-surface sources.
  * Build an architecture map.
  * Build an attack-surface inventory.
  * Prioritize high-value components for deeper testing.

---

## Traditional Web Application Architecture

* A traditional web application commonly follows:

  ```text
  Browser

  ↓

  Web Server

  ↓

  Application

  ↓

  Database
  ```

* The browser requests a page.

* The server generates HTML.

* The browser displays the response.

* Example:

  ```http
  GET /account HTTP/1.1
  Host: example.com
  ```

* The server may respond with:

  ```http
  HTTP/1.1 200 OK
  Content-Type: text/html
  ```

* Much of the application functionality is directly represented through:

  ```text
  URLs

  Forms

  Links

  Cookies

  Server-Rendered Pages
  ```

---

## Modern Web Application Architecture

* Modern applications often separate the frontend from backend functionality.

* Example:

  ```text
  Browser

  ↓

  JavaScript Application

  ↓

  REST / GraphQL API

  ↓

  API Gateway

  ↓

  Backend Services

  ↓

  Databases / Cloud / Third Parties
  ```

* The initial page may contain very little application data.

* JavaScript then retrieves data dynamically.

---

## Example Modern Request Flow

```text
User Opens Dashboard

↓

Browser Loads HTML

↓

Browser Loads JavaScript

↓

JavaScript Requests /api/v2/dashboard

↓

API Validates Authentication

↓

Backend Retrieves Data

↓

JSON Returned

↓

JavaScript Renders Dashboard
```

* Burp allows you to observe the underlying requests rather than only the rendered interface.

---

## Why Architecture Matters

* Architecture determines:

  ```text
  Where Data Enters

  Where Authentication Happens

  Where Authorization Happens

  Which Components Trust Each Other

  Where Validation Occurs

  Which Interfaces Are Exposed

  Which Security Boundaries Exist
  ```

* Without architecture awareness, testing becomes random.

---

## Architecture Before Vulnerabilities

* Avoid beginning with:

  ```text
  Find Parameter

  ↓

  Send Payloads
  ```

* Prefer:

  ```text
  Understand Application

  ↓

  Identify Components

  ↓

  Identify Data Flows

  ↓

  Identify Trust Boundaries

  ↓

  Form Security Hypotheses

  ↓

  Test
  ```

---

## Core Application Layers

* A useful model separates the application into layers.

```text
Presentation Layer

↓

Client Logic

↓

Communication Layer

↓

Application / API Layer

↓

Service Layer

↓

Data Layer

↓

External Dependencies
```

* Each layer exposes different security questions.

---

## Presentation Layer

* This is what the user sees.

* Examples:

  ```text
  Pages

  Forms

  Buttons

  Dashboards

  Menus

  Input Fields
  ```

* Important principle:

  ```text
  Visible UI

  ≠

  Complete Functionality
  ```

* A function may exist even if the interface does not expose it.

---

## Client Logic

* Modern applications frequently contain significant logic in JavaScript.

* Client logic may control:

  ```text
  Routing

  Validation

  API Requests

  Role-Based UI Elements

  Feature Flags

  State Management

  Token Storage

  Error Handling
  ```

* Client-side logic is useful for understanding application behavior.

* It should not be trusted as a security boundary.

---

## Example — Hidden Button

* Suppose JavaScript contains:

  ```text
  if (user.role === "admin") {
      showAdminButton();
  }
  ```

* A normal user may not see the button.

* But the important security question is:

  ```text
  Does the Server Independently Enforce Admin Authorization?
  ```

* Hiding the button is not sufficient.

---

## Communication Layer

* Communication may use:

  ```text
  HTTP

  HTTPS

  REST

  GraphQL

  WebSockets

  Server-Sent Events
  ```

* Burp provides visibility into much of this communication.

* This layer is often where the application's real attack surface becomes visible.

---

## Backend / API Layer

* Backend interfaces may expose:

  ```text
  User Management

  Account Functions

  Orders

  Payments

  Files

  Search

  Administration

  Reporting

  Integrations
  ```

* These interfaces should enforce:

  ```text
  Authentication

  Authorization

  Validation

  Business Rules

  State Integrity
  ```

---

## Service Layer

* Larger applications may split functionality across multiple services.

* Example:

  ```text
  API Gateway
      │
      ├── Authentication Service
      │
      ├── User Service
      │
      ├── Order Service
      │
      ├── Payment Service
      │
      └── Notification Service
  ```

* Each service relationship creates potential trust assumptions.

---

## Data Layer

* Backend services may interact with:

  ```text
  Relational Databases

  NoSQL Databases

  Search Engines

  Caches

  Object Storage

  Message Queues

  File Systems
  ```

* User-controlled data reaching these systems may create different security risks depending on context.

---

## External Dependencies

* Modern applications commonly depend on:

  ```text
  Payment Providers

  Identity Providers

  Analytics

  Cloud Storage

  Email Services

  SMS Providers

  CAPTCHA

  CDN Services

  Support Platforms

  Webhooks
  ```

* These should be distinguished from assets owned by the target.

---

## First-Party vs Third-Party

* During mapping, classify traffic as:

  ```text
  First-Party

  or

  Third-Party
  ```

* Example:

  ```text
  app.example.com

  api.example.com

  auth.example.com
  ```

* May be first-party.

* While:

  ```text
  Analytics Provider

  Payment Provider

  CDN Provider
  ```

* May belong to third parties.

---

## Why Classification Matters

* Seeing a request in Burp does not mean the destination is authorized for testing.

* Use:

  ```text
  Traffic Observed

  ≠

  Testing Authorized
  ```

* Scope must determine what you actively test.

---

## Single-Page Applications

* A single-page application commonly loads a frontend application once and then updates content dynamically.

* Architecture:

  ```text
  Browser

  ↓

  SPA JavaScript

  ↓

  API Requests

  ↓

  Backend
  ```

* Navigation may occur without full page reloads.

---

## SPA Indicators

* You may observe:

  ```text
  Large JavaScript Bundles

  JSON API Traffic

  Client-Side Routes

  Dynamic DOM Updates

  fetch()

  XMLHttpRequest

  Bearer Tokens
  ```

* Burp HTTP history often reveals more than the visible page source.

---

## REST APIs

* REST-style APIs commonly expose resources through URLs.

* Example:

  ```text
  GET /api/users/42

  POST /api/orders

  PATCH /api/profile

  DELETE /api/files/91
  ```

* Security controls may vary across:

  ```text
  Resource

  Method

  User

  Role

  Object

  Tenant
  ```

---

## GraphQL

* GraphQL commonly uses a smaller number of HTTP endpoints.

* Example:

  ```text
  POST /graphql
  ```

* But operations may expose a large logical attack surface.

* The real structure may include:

  ```text
  Queries

  Mutations

  Types

  Fields

  Relationships

  Arguments
  ```

---

## WebSockets

* WebSockets provide long-lived bidirectional communication.

* Architecture:

  ```text
  Browser

  ⇄

  WebSocket Connection

  ⇄

  Backend
  ```

* Common uses include:

  ```text
  Chat

  Notifications

  Collaboration

  Live Data

  Real-Time Actions
  ```

* Authorization may need to be enforced for every message or action.

---

## API Gateways

* An API gateway may sit between clients and backend services.

```text
Client

↓

API Gateway

↓

Backend Services
```

* The gateway may handle:

  ```text
  Routing

  Authentication

  Rate Limiting

  Logging

  Request Transformation

  API Versioning
  ```

---

## Gateway Trust Questions

* Ask:

  ```text
  Does the Gateway Authenticate the Request?

  Does the Backend Also Enforce Authorization?

  Which Headers Does the Backend Trust?

  Can External Clients Influence Internal Identity Data?

  Are Some Services Reachable Without the Gateway?
  ```

---

## Microservices

* A microservice architecture divides functionality into smaller services.

* Example:

  ```text
  Browser

  ↓

  API Gateway

  ├── User Service

  ├── Order Service

  ├── Payment Service

  └── File Service
  ```

* Each service may have:

  ```text
  Separate Endpoints

  Separate Databases

  Different Authentication Logic

  Different Authorization Logic

  Different Deployment Cycles
  ```

---

## Microservice Security Concern

* Security controls may become inconsistent.

* Example:

  ```text
  API v2 Through Gateway

  → Strong Authorization
  ```

* But:

  ```text
  Legacy Service Endpoint

  → Different Authorization
  ```

* Architecture mapping helps identify these differences.

---

## Authentication Components

* Authentication may occur through:

  ```text
  Application Login

  Identity Provider

  OAuth / OIDC

  SSO

  API Gateway

  Authentication Service

  External Identity Platform
  ```

* Record where identity is established and how it propagates.

---

## Identity Flow

```text
User

↓

Login / Identity Provider

↓

Credential or Token Issued

↓

Client Stores Authentication State

↓

Client Sends Authenticated Request

↓

Gateway / Backend Validates Identity

↓

Application Performs Authorization
```

* Authentication and authorization may occur in different components.

---

## Trust Boundaries

* A trust boundary exists where data or identity moves between components with different trust levels.

* Examples:

  ```text
  Browser → API

  Internet → API Gateway

  API Gateway → Internal Service

  Service → Database

  Application → Third-Party Provider

  User A → User B Object

  Standard User → Admin Function
  ```

---

## Why Trust Boundaries Matter

* Vulnerabilities frequently occur when one component assumes another component has already performed a security check.

* Example:

  ```text
  API Gateway

  → Authenticates User
  ```

* Backend service assumes:

  ```text
  Request Reaching Me

  =

  Authorized Request
  ```

* But:

  ```text
  Authentication

  ≠

  Authorization
  ```

---

## Client Trust Boundary

* Everything sent by the client should generally be considered potentially controllable.

* Examples:

  ```text
  Headers

  Cookies

  Query Parameters

  JSON Fields

  Object IDs

  Hidden Form Fields

  Role Values

  Prices

  Workflow States

  Filenames
  ```

* Burp allows these values to be modified independently of the frontend.

---

## Example — Client-Supplied Role

* Request:

  ```json
  {
    "name": "Alice",
    "role": "user"
  }
  ```

* Security question:

  ```text
  Is "role" Merely Client-Supplied Data?

  or

  Does the Server Enforce Who May Change It?
  ```

* The existence of the field is an observation.

* A vulnerability requires demonstrating improper server-side authorization.

---

## Attack Surface

* The attack surface consists of interfaces and functionality through which input, identity, state, or actions reach the application.

* Examples:

  ```text
  Pages

  Forms

  APIs

  Parameters

  Headers

  Cookies

  File Uploads

  WebSockets

  GraphQL Operations

  Authentication Flows

  Password Reset

  Administrative Functions

  Webhooks

  Import / Export

  Third-Party Integrations
  ```

---

## Visible Attack Surface

* Easily observable functionality may include:

  ```text
  Navigation Links

  Forms

  Buttons

  Search

  Profile

  Login

  Registration
  ```

---

## Hidden Attack Surface

* Less obvious functionality may exist in:

  ```text
  JavaScript

  API Calls

  Old API Versions

  Mobile Endpoints

  Source Maps

  Error Messages

  Documentation

  WebSocket Messages

  Feature Flags

  Deprecated Routes

  Administrative APIs
  ```

---

## Attack-Surface Sources in Burp

* Use:

  ```text
  Proxy → HTTP History

  Target → Site Map

  WebSocket History

  Logger

  Search

  Repeater History
  ```

* Combine these with normal application browsing.

---

## HTTP History Analysis

* Review traffic for:

  ```text
  Hosts

  Methods

  Paths

  Status Codes

  MIME Types

  Parameters

  Authentication Headers

  Cookies

  API Calls
  ```

* Look for patterns rather than isolated requests.

---

## Host Inventory

* Record observed hosts.

| Host                | Purpose   | First/Third Party | In Scope | Notes          |
| ------------------- | --------- | ----------------- | :------: | -------------- |
| `app.example.test`  | Frontend  | First-party       |    Yes   | Main app       |
| `api.example.test`  | API       | First-party       |    Yes   | JSON API       |
| `auth.example.test` | Identity  | First-party       |  Depends | Authentication |
| External service    | Analytics | Third-party       |    No    | Do not test    |

---

## Endpoint Inventory

* Record endpoints systematically.

| Method | Endpoint           | Function            | Auth | State Change | Priority |
| ------ | ------------------ | ------------------- | ---- | :----------: | :------: |
| POST   | `/login`           | Authentication      | No   |      Yes     |   High   |
| GET    | `/api/me`          | Current user        | Yes  |      No      |  Medium  |
| GET    | `/api/orders/{id}` | Read order          | Yes  |      No      |   High   |
| PATCH  | `/api/orders/{id}` | Update order        | Yes  |      Yes     |   High   |
| POST   | `/api/admin/users` | User administration | Yes  |      Yes     | Critical |

---

## Parameter Inventory

* Record security-relevant parameters.

| Parameter  | Location | Meaning         | Security Interest       |
| ---------- | -------- | --------------- | ----------------------- |
| `user_id`  | JSON     | User identity   | Authorization           |
| `order_id` | Path     | Order object    | BOLA                    |
| `role`     | JSON     | Privilege       | Property authorization  |
| `price`    | JSON     | Financial value | Business logic          |
| `url`      | JSON     | Remote location | Server-side interaction |
| `redirect` | Query    | Destination     | Redirect logic          |

---

## Object Inventory

* Modern applications frequently operate on objects.

* Examples:

  ```text
  User

  Order

  Invoice

  Ticket

  File

  Message

  Project

  Organization

  API Key
  ```

* For each object, identify:

  ```text
  Identifier

  Owner

  Tenant

  Read Operations

  Write Operations

  Delete Operations

  Privileged Operations
  ```

---

## Role Inventory

* Identify roles such as:

  ```text
  Anonymous

  User

  Moderator

  Manager

  Administrator

  Organization Owner

  Support Agent
  ```

* Do not rely only on role names shown by the frontend.

* Determine actual capabilities through observed requests and authorized testing.

---

## Tenant Boundaries

* Multi-tenant applications may separate:

  ```text
  Organization A

  Organization B
  ```

* This creates an important boundary:

  ```text
  Same Role

  +

  Different Tenant
  ```

* Example:

  ```text
  User A — Tenant A

  User B — Tenant B
  ```

* Cross-tenant authorization should be considered separately from same-tenant horizontal authorization.

---

## Data-Flow Mapping

* Map how important data moves.

* Example:

  ```text
  Browser

  ↓

  POST /api/orders

  ↓

  API Gateway

  ↓

  Order Service

  ↓

  Database

  ↓

  Payment / Notification Services
  ```

* Ask where each security decision should occur.

---

## Sensitive Data Flows

* Prioritize flows involving:

  ```text
  Credentials

  Session Tokens

  Personal Data

  Financial Data

  Files

  API Keys

  Administrative Data

  Password Reset Tokens

  Authentication Secrets
  ```

---

## State-Changing Functions

* Identify requests that change server state.

* Examples:

  ```text
  POST

  PUT

  PATCH

  DELETE
  ```

* But do not rely solely on method names.

* A `GET` endpoint can still trigger state changes in poorly designed applications.

---

## State-Change Inventory

* Record:

  ```text
  Create

  Update

  Delete

  Approve

  Transfer

  Pay

  Upload

  Invite

  Reset

  Redeem

  Export
  ```

* These functions often deserve higher testing priority.

---

## High-Value Attack Surface

* Prioritize:

  ```text
  Authentication

  Password Reset

  Account Settings

  Authorization Boundaries

  Administrative Functions

  Financial Operations

  File Uploads

  Imports

  Exports

  APIs

  Webhooks

  External URL Processing

  Multi-Tenant Functions

  Sensitive Data
  ```

---

## Attack-Surface Prioritization

* A simple model:

  ```text
  Authentication Sensitivity

  +

  Authorization Complexity

  +

  Data Sensitivity

  +

  State-Changing Capability

  +

  User-Controlled Input

  +

  External Interaction
  ```

* Higher combined importance should receive deeper testing.

---

## Architecture Discovery Workflow

* Use:

  ```text
  Define Scope

  ↓

  Browse Application

  ↓

  Observe Hosts

  ↓

  Observe HTTP Traffic

  ↓

  Identify APIs

  ↓

  Identify JavaScript

  ↓

  Identify Authentication

  ↓

  Identify WebSockets

  ↓

  Identify Third Parties

  ↓

  Build Architecture Map

  ↓

  Build Attack-Surface Inventory
  ```

---

## Burp Mapping Workflow

### Step 1 — Configure Scope

* Add only authorized targets.

* Filter unrelated traffic where possible.

---

### Step 2 — Browse Normally

* Visit:

  ```text
  Public Pages

  Login

  Dashboard

  Profile

  Search

  Account Settings

  Major Application Functions
  ```

* Allow the application to generate natural traffic.

---

### Step 3 — Review HTTP History

* Identify:

  ```text
  New Hosts

  API Requests

  JSON Responses

  Authentication Headers

  Object IDs

  State-Changing Requests
  ```

---

### Step 4 — Review Site Map

* Look for:

  ```text
  API Base Paths

  Versioned APIs

  Hidden Branches

  Static JavaScript

  Authentication Routes

  Administrative Paths
  ```

---

### Step 5 — Identify Communication Styles

* Determine whether the application uses:

  ```text
  Server-Rendered HTTP

  REST

  GraphQL

  WebSockets

  Multiple Interfaces
  ```

---

### Step 6 — Identify Identities

* Record:

  ```text
  Anonymous

  Authenticated User

  Higher-Privilege User

  Tenant Membership
  ```

---

### Step 7 — Identify Objects

* Look for identifiers in:

  ```text
  URL Paths

  Query Parameters

  JSON

  Headers

  Cookies

  GraphQL Variables

  WebSocket Messages
  ```

---

### Step 8 — Identify Trust Boundaries

* Mark:

  ```text
  Client → Server

  User → Object

  Role → Function

  Tenant → Tenant

  Gateway → Service

  Application → Third Party
  ```

---

### Step 9 — Prioritize

* Categorize:

  ```text
  Critical

  High

  Medium

  Low
  ```

* Based on security relevance rather than URL appearance.

---

## Architecture Diagram Template

```text
Users
  │
  ▼
Frontend
  │
  ├── Static Assets / JavaScript
  │
  ▼
API / Gateway
  │
  ├── Authentication Service
  ├── User Service
  ├── Business Service
  ├── File Service
  └── Other Services
          │
          ▼
Databases / Storage / Third Parties
```

* Adapt the diagram to observed evidence.

* Do not invent components that have not been identified.

---

## Architecture Investigation Card

```text
Application:

Primary Host:

Frontend Type:

Observed Hosts:

API Base Paths:

API Style:

Authentication System:

Session / Token Type:

Roles:

Tenants:

Main Objects:

WebSocket Endpoints:

GraphQL Endpoint:

Third-Party Services:

State-Changing Functions:

Sensitive Data Flows:

Trust Boundaries:

High-Value Components:

Unknown Areas:

Evidence:

Follow-Up:
```

---

## Attack-Surface Inventory Template

```text
Host:

Method:

Endpoint:

Function:

Authentication Required:

Role:

Tenant:

Object:

Object Identifier:

Parameters:

Content Type:

State Changing?:

Sensitive Data?:

External Interaction?:

Observed Through:

Priority:

Security Questions:

Notes:
```

---

## Security Hypothesis Generation

* Architecture mapping should produce security questions.

* Example observation:

  ```text
  GET /api/orders/481
  ```

* Hypothesis:

  ```text
  Does the Backend Verify That the Authenticated User Owns Order 481?
  ```

---

## Example — Admin API

* Observation:

  ```text
  POST /api/admin/users/42/disable
  ```

* Hypothesis:

  ```text
  Does the Backend Enforce Administrator Privileges for This Function?
  ```

---

## Example — Hidden Property

* Observation:

  ```json
  {
    "name": "Alice",
    "department": "Sales"
  }
  ```

* JavaScript references:

  ```text
  role
  ```

* Hypothesis:

  ```text
  Can a Normal User Submit a Restricted "role" Property?
  ```

---

## Example — Multiple API Versions

* Observation:

  ```text
  /api/v1/profile

  /api/v2/profile
  ```

* Hypothesis:

  ```text
  Do Both Versions Enforce Equivalent Security Controls?
  ```

---

## Example — Cross-Tenant Object

* Observation:

  ```text
  /api/organizations/{org_id}/projects/{project_id}
  ```

* Hypothesis:

  ```text
  Does the Backend Validate Both Organization Membership and Project Authorization?
  ```

---

## Unknowns Are Valuable

* During mapping, record what you do not understand.

* Examples:

  ```text
  Unknown Authentication Header

  Unknown Service Host

  Unexplained Object Identifier

  Undocumented API Version

  Hidden WebSocket Message Type

  Unclear Role Boundary
  ```

* Unknowns become investigation targets.

---

## Avoid Premature Conclusions

* Observation:

  ```text
  Endpoint Returns 403
  ```

* Do not immediately conclude:

  ```text
  Authorization Is Secure
  ```

* Other:

  ```text
  Methods

  Endpoints

  Object Types

  API Versions

  Roles
  ```

* May behave differently.

---

## Mapping vs Testing

* Mapping asks:

  ```text
  What Exists?
  ```

* Testing asks:

  ```text
  Does It Enforce the Expected Security Rule?
  ```

* Keep these stages conceptually separate.

---

## Common Architecture Patterns

### Monolithic Application

```text
Browser

↓

Single Application

↓

Database
```

* Security controls may be centralized.

---

### SPA + API

```text
Browser

↓

JavaScript SPA

↓

REST / GraphQL API

↓

Backend
```

* Client and backend are clearly separated.

---

### API Gateway + Microservices

```text
Client

↓

API Gateway

↓

Multiple Services

↓

Multiple Data Stores
```

* Trust and authorization may be distributed.

---

### Multi-Client Backend

```text
Web App ──────┐

Mobile App ───┼──► Shared API

Partner App ──┘
```

* Different clients may expose different functionality against the same backend.

---

### Third-Party Integrated Application

```text
Application

├── Payment Provider

├── Identity Provider

├── Storage Provider

└── Notification Provider
```

* Scope and trust relationships become especially important.

---

## Professional Tips

* Map before testing deeply.
* Treat every client-controlled value as potentially modifiable.
* Separate observed architecture from assumptions.
* Record hosts as soon as they appear.
* Classify first-party and third-party traffic.
* Keep Burp Target scope aligned with authorization.
* Build inventories for endpoints, objects, roles, and tenants.
* Pay special attention to state-changing functions.
* Map both visible and hidden attack surfaces.
* Use JavaScript and API traffic to understand functionality not shown by the UI.
* Record unknown components rather than ignoring them.
* Convert observations into explicit security hypotheses.
* Prioritize by security impact, not merely by endpoint count.
* Revisit the architecture map as new evidence appears.

---

## Common Mistakes

* Starting payload testing before understanding the application.
* Assuming the visible website represents the complete attack surface.
* Treating frontend restrictions as security controls.
* Ignoring JavaScript-generated traffic.
* Recording URLs without methods or business purposes.
* Failing to identify object ownership.
* Failing to distinguish same-tenant and cross-tenant boundaries.
* Ignoring WebSocket traffic.
* Ignoring GraphQL because only one endpoint is visible.
* Treating every observed third-party host as authorized scope.
* Assuming API gateways enforce every backend security requirement.
* Assuming internal services are automatically secure.
* Ignoring older API versions.
* Focusing only on parameters while ignoring workflow state.
* Testing without controlled identities.
* Failing to update the architecture map as the assessment progresses.

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

### Task 2 — Configure Scope

* Record:

  ```text
  Primary Host:

  Authorized Hosts:

  Excluded Hosts:
  ```

---

### Task 3 — Browse the Application

* Visit all major accessible functions.

* Do not perform aggressive testing yet.

---

### Task 4 — Build a Host Inventory

* Record:

  ```text
  Host

  Purpose

  First / Third Party

  Scope Status
  ```

---

### Task 5 — Identify Architecture

* Determine whether you observe:

  ```text
  Server Rendering

  SPA

  REST

  GraphQL

  WebSockets

  API Gateway

  Multiple Services
  ```

---

### Task 6 — Build an Endpoint Inventory

* Record at least 10 important requests if the application exposes enough functionality.

* Include:

  ```text
  Method

  Endpoint

  Function

  Authentication

  State Change

  Priority
  ```

---

### Task 7 — Identify Objects

* Find at least three object types.

* Example:

  ```text
  User

  Order

  File
  ```

* Record their identifiers.

---

### Task 8 — Identify Roles and Tenants

* Record known:

  ```text
  Roles

  Organizations

  Workspaces

  Tenants
  ```

* If none exist, record that observation.

---

### Task 9 — Identify Trust Boundaries

* Document at least five relevant boundaries.

* Example:

  ```text
  Browser → API

  User → User Object

  User → Admin Function

  Tenant A → Tenant B

  API → Third Party
  ```

---

### Task 10 — Identify High-Value Functions

* Prioritize at least five functions for later testing.

---

### Task 11 — Create Architecture Diagram

* Produce a simple diagram based only on observed evidence.

---

### Task 12 — Generate Security Hypotheses

* Write at least five questions such as:

  ```text
  Does the API verify object ownership?

  Does the backend enforce role authorization?

  Does the older API version enforce the same controls?

  Can restricted properties be submitted directly?

  Are cross-tenant identifiers validated?
  ```

---

## Investigation Journal

```text
Application:

Date:

Scope:

Primary Host:

Observed Hosts:

Frontend Architecture:

API Architecture:

Authentication:

Roles:

Tenants:

Objects:

API Base Paths:

GraphQL:

WebSockets:

JavaScript Assets:

Third-Party Integrations:

State-Changing Functions:

Sensitive Functions:

Sensitive Data:

Trust Boundaries:

High-Priority Endpoints:

Hidden Attack Surface:

Unknown Components:

Security Hypotheses:

Evidence:

Next Steps:
```

---

## Interview Questions

1. Why should application architecture be understood before vulnerability testing?
2. What is the difference between a traditional server-rendered application and a single-page application?
3. Why is the visible frontend not the complete attack surface?
4. What are the main layers of a modern application?
5. Why should client-side logic not be treated as a security boundary?
6. What information can client-side JavaScript reveal?
7. What is an API gateway?
8. Which security functions may an API gateway perform?
9. Why can relying entirely on an API gateway for security be risky?
10. What is a microservice architecture?
11. Why can microservices create inconsistent security controls?
12. What is a trust boundary?
13. Give examples of trust boundaries in a modern application.
14. Why should first-party and third-party traffic be distinguished?
15. Why does observing a host in Burp not mean it is authorized for active testing?
16. What is an attack surface?
17. What is the difference between visible and hidden attack surface?
18. Which Burp tools help with attack-surface mapping?
19. What information should an endpoint inventory contain?
20. Why should object ownership be recorded?
21. What is a tenant boundary?
22. How does cross-tenant authorization differ from normal horizontal authorization?
23. Why should state-changing functions receive higher testing priority?
24. How would you prioritize a large attack surface?
25. What is a data-flow map?
26. Why are sensitive data flows important?
27. How can API versions expand the attack surface?
28. Why can GraphQL have a large attack surface despite using one HTTP endpoint?
29. Why should WebSocket traffic be included in architecture mapping?
30. What is the difference between architecture mapping and vulnerability testing?
31. How do you convert an architectural observation into a security hypothesis?
32. Why should unknown components be recorded?
33. Why is `403 Forbidden` insufficient to conclude that authorization is secure everywhere?
34. What is the difference between authentication and authorization across service boundaries?
35. Describe your process for mapping an unfamiliar modern application using Burp Suite.

---

## Quick Revision

* Start with:

  ```text
  Architecture

  Before

  Vulnerabilities
  ```

* Modern application:

  ```text
  Frontend

  +

  APIs

  +

  Services

  +

  Data

  +

  Third Parties
  ```

* Frontend:

  ```text
  Client

  ≠

  Security Boundary
  ```

* Trust boundary:

  ```text
  Point Where Data or Identity

  Crosses Between Different Trust Levels
  ```

* Attack surface:

  ```text
  Every Relevant Interface

  Through Which

  Data, Identity, State, or Actions

  Reach the Application
  ```

* Endpoint inventory:

  ```text
  Method

  +

  Endpoint

  +

  Function

  +

  Authentication

  +

  Role

  +

  Object

  +

  State Change
  ```

* Important inventories:

  ```text
  Hosts

  Endpoints

  Parameters

  Objects

  Roles

  Tenants

  Data Flows
  ```

* Hidden attack surface:

  ```text
  JavaScript

  APIs

  Old Versions

  WebSockets

  GraphQL

  Mobile Interfaces

  Undocumented Routes
  ```

* Mapping:

  ```text
  What Exists?
  ```

* Testing:

  ```text
  Does It Enforce the Expected Security Rule?
  ```

* Professional workflow:

  ```text
  Scope

  ↓

  Observe

  ↓

  Map

  ↓

  Classify

  ↓

  Identify Trust Boundaries

  ↓

  Prioritize

  ↓

  Form Hypotheses

  ↓

  Test
  ```

---

## Key Takeaways

* Architecture discovery should occur before deep vulnerability testing.
* Modern applications often separate frontend interfaces from backend APIs and services.
* The visible UI represents only part of the complete attack surface.
* Client-side restrictions must not be treated as server-side security controls.
* Burp HTTP history and Site Map reveal communication that may not be obvious from the rendered interface.
* Modern architectures may include REST, GraphQL, WebSockets, API gateways, microservices, cloud services, and third-party integrations.
* Classify observed systems as first-party or third-party and keep active testing aligned with explicit scope.
* Trust boundaries identify where security assumptions exist between users, clients, gateways, services, tenants, data stores, and external systems.
* Build structured inventories for hosts, endpoints, parameters, objects, roles, tenants, and state-changing functions.
* Record object ownership and tenant membership before performing authorization testing.
* Prioritize authentication, authorization, sensitive data, state-changing operations, administrative functionality, financial workflows, files, and external interactions.
* Hidden attack surface may exist in JavaScript, APIs, old versions, mobile backends, GraphQL operations, WebSockets, source maps, and undocumented functionality.
* Architecture mapping should be evidence-based; do not invent components simply because they are common in modern systems.
* Unknown components and unexplained behavior should be recorded as investigation targets.
* Convert architectural observations into explicit security hypotheses before testing.
* The professional progression is **scope → architecture → components → data flows → trust boundaries → attack surface → prioritization → hypotheses → testing**.
