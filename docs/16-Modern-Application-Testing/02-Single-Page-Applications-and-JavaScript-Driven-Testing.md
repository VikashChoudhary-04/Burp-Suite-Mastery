# Single-Page Applications & JavaScript-Driven Testing

> **Module 16 — Modern Application Testing**

> **Progress**

```text
███░░░░░░░░░░ 3 / 13 Files
```

---

## Overview

* Modern web applications increasingly rely on **single-page application (SPA)** architectures and client-side JavaScript.

* Instead of requesting a completely new HTML page for every action, an SPA typically:

  * Loads an application shell.
  * Downloads JavaScript bundles.
  * Executes application logic in the browser.
  * Requests data from backend APIs.
  * Updates the interface dynamically.

* A simplified flow looks like:

  ```text
  Browser Loads Application

  ↓

  JavaScript Executes

  ↓

  API Requests Are Generated

  ↓

  Backend Returns Data

  ↓

  JavaScript Updates the Interface
  ```

* This architecture changes how penetration testers discover functionality.

* Looking only at:

  ```text
  HTML

  Links

  Forms
  ```

* May reveal only a fraction of the real attack surface.

* JavaScript and the network traffic it generates can reveal:

  ```text
  API Endpoints

  Hidden Routes

  Parameter Names

  Object Structures

  API Versions

  GraphQL Endpoints

  WebSocket URLs

  Feature Flags

  Role Logic

  Undocumented Functions
  ```

* Burp Suite allows you to investigate this client-server communication directly.

---

## Learning Objectives

* By the end of this lesson, you will be able to:

  * Understand how single-page applications operate.
  * Recognize common SPA behavior.
  * Distinguish client-side routes from server-side endpoints.
  * Understand why JavaScript expands the observable attack surface.
  * Identify JavaScript files through Burp Suite.
  * Analyze JavaScript for security-relevant information.
  * Discover API endpoints referenced by frontend code.
  * Identify hidden and conditional functionality.
  * Understand client-side validation and authorization assumptions.
  * Recognize feature flags and configuration data.
  * Identify source maps and understand their security relevance.
  * Correlate JavaScript discoveries with captured HTTP traffic.
  * Build a JavaScript-derived endpoint inventory.
  * Convert frontend observations into security hypotheses.

---

## What Is a Single-Page Application?

* A single-page application is a web application that dynamically updates the current page rather than loading a completely new HTML document for every interaction.

* Traditional model:

  ```text
  Browser

  ↓

  GET /products

  ↓

  Server Generates HTML

  ↓

  Full Page Load

  ↓

  GET /account

  ↓

  Server Generates New HTML

  ↓

  Full Page Load
  ```

* SPA model:

  ```text
  Browser

  ↓

  Load Application Shell

  ↓

  Load JavaScript

  ↓

  API Requests

  ↓

  Dynamic UI Updates
  ```

---

## Common SPA Technologies

* SPAs may be built using frameworks and libraries such as:

  ```text
  React

  Angular

  Vue

  Svelte

  Next.js

  Nuxt

  Other JavaScript Frameworks
  ```

* Framework identification can help explain application behavior.

* However:

  ```text
  Framework Identification

  ≠

  Vulnerability
  ```

* The objective is to understand architecture and functionality.

---

## SPA Request Flow

* Consider a user opening an account dashboard.

```text
GET /

↓

HTML Application Shell

↓

JavaScript Bundles Loaded

↓

JavaScript Initializes

↓

GET /api/v2/me

↓

GET /api/v2/accounts

↓

GET /api/v2/notifications

↓

Interface Rendered
```

* The initial HTML may contain almost no sensitive application data.

* The important functionality appears in subsequent API requests.

---

## Why Burp Is Important for SPA Testing

* Burp provides visibility into requests generated automatically by JavaScript.

* Use:

  ```text
  Proxy

  ↓

  HTTP History
  ```

* To observe:

  ```text
  XHR Requests

  fetch() Requests

  API Calls

  Authentication Headers

  JSON Bodies

  Background Requests

  Dynamic Data Loading
  ```

* These requests may never appear as traditional forms or links.

---

## Browser UI vs Actual Application Interface

* Suppose the UI shows:

  ```text
  Profile

  Orders

  Settings
  ```

* But captured traffic reveals:

  ```text
  /api/v2/profile

  /api/v2/orders

  /api/v2/settings

  /api/v2/internal/preferences

  /api/v1/export

  /graphql
  ```

* The actual attack surface is broader than the visible navigation.

---

## Client-Side Routing

* SPAs frequently use client-side routing.

* Example URLs:

  ```text
  /dashboard

  /profile

  /orders/481

  /settings
  ```

* These paths may not correspond directly to backend server resources.

---

## Client Route vs API Endpoint

* Example:

  ```text
  Browser Route:

  /orders/481
  ```

* JavaScript may request:

  ```text
  GET /api/v2/orders/481
  ```

* Security testing should focus on the backend interface enforcing the action.

---

## Important Distinction

```text
Client Route

≠

Backend Endpoint
```

* Record both when mapping the application.

---

## JavaScript as an Attack-Surface Map

* Frontend JavaScript must know how to communicate with the backend.

* Therefore, it may contain references to:

  ```text
  API Paths

  HTTP Methods

  Parameter Names

  Request Structures

  Object Fields

  Authentication Logic

  Feature Routes

  Error Conditions

  Role Checks

  WebSocket Connections
  ```

* This makes JavaScript valuable during reconnaissance and application mapping.

---

## Finding JavaScript Files in Burp

* JavaScript files commonly appear as:

  ```text
  .js
  ```

* Examples:

  ```text
  /static/app.js

  /assets/main.js

  /js/vendor.js

  /static/js/main.a91c2.js

  /assets/index-7f8a91.js
  ```

---

## Using HTTP History

* In:

  ```text
  Proxy

  →

  HTTP History
  ```

* Look for responses with:

  ```text
  Content-Type: application/javascript
  ```

* Or filenames ending in:

  ```text
  .js
  ```

---

## Using Target Site Map

* Navigate to:

  ```text
  Target

  →

  Site Map
  ```

* Expand:

  ```text
  Static

  Assets

  JavaScript

  Bundles
  ```

* Site Map can make large JavaScript inventories easier to understand.

---

## JavaScript Bundles

* Production SPAs commonly combine application code into bundles.

* Example:

  ```text
  main.js

  runtime.js

  vendor.js

  chunk-481.js
  ```

* Or hashed files:

  ```text
  app.34fa82c.js

  192.a7bc41.js
  ```

* Important functionality may be distributed across multiple chunks.

---

## Lazy-Loaded JavaScript

* Some JavaScript is downloaded only when specific functionality is accessed.

* Example:

  ```text
  User Opens Application

  ↓

  Core Bundle Loaded

  ↓

  User Opens Admin Page

  ↓

  Admin JavaScript Chunk Loaded
  ```

* Therefore:

  ```text
  Initial JavaScript Files

  ≠

  Complete JavaScript Attack Surface
  ```

---

## Practical Mapping Principle

* Browse as much authorized functionality as possible before concluding JavaScript discovery is complete.

* Different:

  ```text
  Pages

  Roles

  Features

  Workflows
  ```

* May load different bundles.

---

## Searching JavaScript

* Useful search terms include:

  ```text
  /api/

  api/v1

  api/v2

  graphql

  websocket

  wss://

  fetch(

  axios

  Authorization

  Bearer

  token

  admin

  role

  permissions

  feature

  internal

  debug

  export

  upload
  ```

* Search results are leads.

* They are not automatically vulnerabilities.

---

## Endpoint Discovery

* JavaScript may contain:

  ```text
  "/api/v2/users"

  "/api/v2/orders"

  "/api/admin/reports"

  "/api/v1/export"
  ```

* Record each discovered endpoint.

---

## Endpoint Classification

* For every endpoint, determine:

  ```text
  Was It Observed in Traffic?

  or

  Only Found in JavaScript?
  ```

* Example:

| Endpoint             | Source       | Observed Live | Priority |
| -------------------- | ------------ | :-----------: | :------: |
| `/api/v2/me`         | HTTP History |      Yes      |  Medium  |
| `/api/v2/orders`     | JS + History |      Yes      |   High   |
| `/api/admin/reports` | JavaScript   |       No      |   High   |
| `/api/v1/export`     | JavaScript   |       No      |   High   |

---

## Why Unobserved Endpoints Matter

* An endpoint referenced in JavaScript may be:

  ```text
  Active

  Deprecated

  Role Restricted

  Feature Flagged

  Environment Specific

  Unused

  Removed
  ```

* Do not assume it is reachable.

* Validate carefully within scope.

---

## Parameter Discovery

* JavaScript may reveal expected request fields.

* Example:

  ```text
  updateProfile({
      name,
      email,
      department
  })
  ```

* This suggests possible fields:

  ```text
  name

  email

  department
  ```

* Other code may reveal additional properties.

---

## Object Structure Discovery

* Example:

  ```text
  {
      userId,
      organizationId,
      role,
      permissions,
      status
  }
  ```

* Security questions may include:

  ```text
  Which Fields Are User-Controlled?

  Which Fields Are Server-Controlled?

  Which Fields Are Privileged?

  Which Fields Are Returned but Not Editable?
  ```

---

## Hidden Properties

* The frontend may display only:

  ```text
  name

  email
  ```

* But JavaScript may reference:

  ```text
  role

  status

  organizationId

  permissions
  ```

* This can help identify property-level authorization questions.

---

## Important Principle

```text
Property Exists

≠

Property Is User-Modifiable
```

* The server must be tested before drawing conclusions.

---

## Client-Side Validation

* JavaScript often validates input before sending requests.

* Example:

  ```text
  if (amount > 1000) {
      showError();
      return;
  }
  ```

* This prevents the browser UI from sending certain values.

* But Burp can modify the request after client-side validation.

---

## Security Question

```text
Does the Server Independently Enforce the Same Rule?
```

* Client-side validation improves user experience.

* Server-side validation provides security enforcement.

---

## Client-Side Authorization Logic

* JavaScript may contain logic such as:

  ```text
  if (user.role === "admin") {
      displayAdminPanel();
  }
  ```

* This tells you something about application functionality.

* It does not prove that authorization is client-side only.

---

## Correct Testing Model

```text
Discover Privileged Function

↓

Identify Backend Request

↓

Establish Privileged Baseline

↓

Replay Using Controlled Lower-Privilege Identity

↓

Observe Server Enforcement
```

---

## Hidden UI Functionality

* JavaScript may contain routes for functionality not visible to the current user.

* Example:

  ```text
  /admin

  /reports

  /billing/export

  /internal/tools
  ```

* Possible reasons include:

  ```text
  Different Role

  Disabled Feature

  Feature Flag

  Legacy Function

  Development Artifact
  ```

* Treat these as investigation leads.

---

## Feature Flags

* Modern applications may enable functionality conditionally.

* Example concepts:

  ```text
  enableNewBilling

  betaDashboard

  adminReports

  experimentalUpload
  ```

* Feature flags may appear in:

  ```text
  JavaScript

  API Responses

  Configuration Objects

  Local Storage

  Cookies
  ```

---

## Feature Flag Security Principle

```text
Feature Disabled in UI

≠

Backend Endpoint Disabled
```

* Verify whether backend controls independently enforce access.

---

## Configuration Data

* Frontend applications often require runtime configuration.

* This may include:

  ```text
  API Base URLs

  Environment Names

  Public Client IDs

  Feature Flags

  Analytics IDs

  Public Service Configuration

  WebSocket URLs
  ```

* Not all exposed configuration is sensitive.

---

## Public Configuration vs Secrets

* Browser-delivered JavaScript is accessible to users.

* Therefore, values intentionally required by the browser should generally be considered public.

* Examples may include:

  ```text
  Public API Base URL

  Public OAuth Client ID

  Analytics Identifier
  ```

* Do not report a value merely because it looks technical.

---

## Secret Discovery

* Occasionally, JavaScript may expose credentials or sensitive tokens that should not be public.

* Potential examples:

  ```text
  Private API Credentials

  Long-Lived Privileged Tokens

  Internal Authentication Secrets

  Private Signing Material
  ```

* A finding requires validating:

  ```text
  Is It Actually Secret?

  Is It Active?

  What Permissions Does It Have?

  Was It Intended for Public Clients?

  What Is the Security Impact?
  ```

---

## Never Assume Based on Name

* A string named:

  ```text
  apiKey
  ```

* Is not automatically a secret.

* Some APIs intentionally use public client identifiers.

* Context and privilege determine security significance.

---

## API Base URLs

* JavaScript may reveal:

  ```text
  https://api.example.test

  https://auth.example.test

  https://files.example.test
  ```

* These hosts can help expand the architecture map.

* Before active testing:

  ```text
  Confirm Scope
  ```

---

## Environment References

* JavaScript may contain references to:

  ```text
  production

  staging

  development

  test

  sandbox
  ```

* These may reveal architecture or deployment history.

* However:

  ```text
  Reference Found

  ≠

  Environment Is Reachable

  ≠

  Environment Is In Scope
  ```

---

## Source Maps

* JavaScript source maps help browsers map minified production code back to original source files.

* Common references:

  ```text
  //# sourceMappingURL=main.js.map
  ```

* Source map file:

  ```text
  main.js.map
  ```

---

## Why Source Maps Matter

* Source maps may expose more readable:

  ```text
  Source Code

  Function Names

  Directory Structures

  API Calls

  Route Definitions

  Comments

  Component Names
  ```

* This can make architecture analysis significantly easier.

---

## Source Maps Are Not Automatically Vulnerabilities

* The presence of a source map alone does not prove meaningful security impact.

* Evaluate whether it exposes:

  ```text
  Sensitive Internal Information

  Secrets

  Hidden Security-Relevant Functionality

  Dangerous Implementation Details
  ```

* Report based on demonstrated impact.

---

## Minified JavaScript

* Production JavaScript may appear like:

  ```text
  function a(b){return c.post("/api/v2/users",b)}
  ```

* Minification reduces readability.

* It does not prevent analysis.

---

## Useful Analysis Strategy

```text
Search First

↓

Identify Interesting Strings

↓

Understand Nearby Code

↓

Correlate With HTTP Traffic

↓

Validate Endpoint Behavior
```

* Avoid trying to understand every line of a large bundle.

---

## Beautifying JavaScript

* Formatting minified JavaScript can improve readability.

* Burp's message editor and browser developer tools may help with viewing content.

* The goal is to identify:

  ```text
  Routes

  Requests

  Parameters

  Conditions

  Security-Relevant Logic
  ```

* Not to perform a complete source-code audit.

---

## API Client Functions

* JavaScript may contain reusable API functions.

* Example:

  ```text
  getUser(id)

  updateOrder(id, data)

  deleteFile(id)

  exportReport(type)
  ```

* These functions reveal:

  ```text
  Operations

  Objects

  Parameters

  Methods
  ```

---

## Route Definitions

* Client-side route configuration may reveal functionality.

* Conceptual example:

  ```text
  /dashboard

  /account

  /orders/:id

  /admin/users

  /reports/export
  ```

* Compare route definitions against:

  ```text
  Visible UI

  Observed HTTP Traffic

  Current Role
  ```

---

## Role and Permission References

* Search for terms such as:

  ```text
  admin

  moderator

  owner

  manager

  role

  permission

  canEdit

  canDelete

  isAdmin
  ```

* These may help identify privilege boundaries.

---

## Important Caution

* JavaScript role checks often control only interface presentation.

* Example:

  ```text
  canDelete === true
  ```

* The real security question remains:

  ```text
  Does the Backend Enforce Delete Authorization?
  ```

---

## Authentication Logic

* JavaScript may reveal how authentication operates.

* Look for:

  ```text
  Authorization

  Bearer

  access_token

  refresh_token

  login

  logout

  refresh

  session
  ```

* This can help map the authentication lifecycle.

---

## Token Storage

* Applications may store authentication-related data in:

  ```text
  Cookies

  Memory

  Local Storage

  Session Storage
  ```

* Storage choice affects security analysis.

* But avoid simplistic conclusions.

---

## Example

* Finding a token in:

  ```text
  localStorage
  ```

* Does not alone prove a vulnerability.

* Security relevance depends on:

  ```text
  Token Sensitivity

  XSS Exposure

  Expiry

  Scope

  Application Threat Model
  ```

---

## API Version Discovery

* Search JavaScript for:

  ```text
  /api/v1/

  /api/v2/

  /api/v3/
  ```

* Multiple versions may indicate:

  ```text
  Migration

  Legacy Functionality

  Mobile Compatibility

  Deprecated Endpoints
  ```

---

## Version Inventory

| Version | Endpoint Example  | Observed | Notes       |
| ------- | ----------------- | :------: | ----------- |
| v1      | `/api/v1/profile` |    No    | Found in JS |
| v2      | `/api/v2/profile` |    Yes   | Current UI  |
| v3      | `/api/v3/search`  |    Yes   | New feature |

* Later testing can compare security controls across versions.

---

## GraphQL Discovery

* Search for:

  ```text
  graphql

  /graphql

  query

  mutation
  ```

* JavaScript may reveal:

  ```text
  Operation Names

  Query Structures

  Mutation Names

  Fields
  ```

* This can significantly improve GraphQL mapping.

---

## WebSocket Discovery

* Search for:

  ```text
  ws://

  wss://

  WebSocket

  socket
  ```

* Example:

  ```text
  wss://example.test/realtime
  ```

* Add discovered WebSocket interfaces to the architecture map.

---

## Third-Party Integrations

* JavaScript often reveals integrations with:

  ```text
  Analytics

  Payments

  Support Systems

  CAPTCHA

  Cloud Storage

  Identity Providers
  ```

* Distinguish:

  ```text
  Application-Owned Functionality

  vs

  Third-Party Services
  ```

* Do not actively test third-party systems without authorization.

---

## Error Handling

* JavaScript may reveal expected backend error conditions.

* Example concepts:

  ```text
  USER_NOT_FOUND

  ACCESS_DENIED

  TENANT_MISMATCH

  FEATURE_DISABLED

  TOKEN_EXPIRED
  ```

* These can provide clues about:

  ```text
  Security Boundaries

  Backend Logic

  Hidden States

  Authentication Flows
  ```

---

## Commented and Legacy Code

* Bundles or source maps may contain references to:

  ```text
  Deprecated Endpoints

  Old Features

  Disabled Routes

  Legacy API Versions
  ```

* Treat them as historical clues until validated.

---

## JavaScript Discovery Workflow

```text
Browse Application

↓

Capture All Relevant Traffic

↓

Identify JavaScript Files

↓

Identify Lazy-Loaded Chunks

↓

Search for Security-Relevant Strings

↓

Extract Endpoint Candidates

↓

Extract Parameters and Object Fields

↓

Identify Roles and Features

↓

Identify API Versions

↓

Identify GraphQL / WebSockets

↓

Correlate With HTTP History

↓

Validate Within Scope

↓

Update Attack-Surface Map
```

---

## Burp Workflow — Step 1: Browse Broadly

* Navigate through authorized functionality.

* Include:

  ```text
  Public Pages

  Authentication

  Dashboard

  Profile

  Settings

  Search

  Files

  Business Workflows
  ```

* Different functions may trigger different JavaScript chunks.

---

## Step 2 — Review JavaScript Requests

* Use:

  ```text
  Proxy

  →

  HTTP History
  ```

* Identify:

  ```text
  .js Files

  JavaScript MIME Types

  Bundles

  Chunks
  ```

---

## Step 3 — Review Site Map

* Use:

  ```text
  Target

  →

  Site Map
  ```

* Organize JavaScript by host and path.

---

## Step 4 — Search Interesting Terms

* Start with high-value patterns:

  ```text
  /api/

  admin

  role

  token

  graphql

  websocket

  export

  upload

  internal
  ```

---

## Step 5 — Record Findings

* Do not rely on memory.

* Record:

  ```text
  File

  Search Term

  Finding

  Endpoint

  Parameter

  Role

  Feature

  Evidence
  ```

---

## Step 6 — Correlate With Traffic

* For each endpoint found in JavaScript:

  ```text
  Search HTTP History

  ↓

  Search Site Map

  ↓

  Determine Whether It Is Actively Used
  ```

---

## Step 7 — Validate Carefully

* If an endpoint has not been observed:

  ```text
  Confirm Scope

  ↓

  Understand Expected Method

  ↓

  Use Safe Request

  ↓

  Observe Response
  ```

* Avoid sending destructive requests merely to determine whether an endpoint exists.

---

## Step 8 — Update Architecture

* Add confirmed:

  ```text
  Hosts

  APIs

  Versions

  WebSockets

  GraphQL

  Features

  Roles

  Objects
  ```

* To your architecture map.

---

## JavaScript Endpoint Inventory

```text
JavaScript File:

Endpoint:

Method:

Observed in Traffic?:

API Version:

Authentication:

Role:

Object:

Parameters:

State Changing?:

Feature:

Evidence:

Scope Confirmed?:

Priority:

Follow-Up:
```

---

## JavaScript Analysis Journal

```text
Application:

Date:

JavaScript Files Reviewed:

Lazy-Loaded Chunks:

Source Maps:

API Base URLs:

Endpoints Found:

Hidden Routes:

Parameters:

Object Fields:

Roles:

Permissions:

Feature Flags:

Authentication References:

Token Handling:

API Versions:

GraphQL:

WebSockets:

Third-Party Services:

Environment References:

Potential Secrets:

Validated Findings:

False Positives:

Security Hypotheses:

Next Steps:
```

---

## Example Investigation

* Suppose JavaScript contains:

  ```text
  /api/v2/account

  /api/v2/orders

  /api/admin/reports

  /api/v1/export
  ```

* HTTP history shows only:

  ```text
  /api/v2/account

  /api/v2/orders
  ```

* Record:

  ```text
  Observed Live:

  /api/v2/account

  /api/v2/orders
  ```

* And:

  ```text
  JavaScript-Only Candidates:

  /api/admin/reports

  /api/v1/export
  ```

---

## Generate Hypotheses

* `/api/admin/reports`

  ```text
  Is This Endpoint Restricted by Server-Side Role Authorization?
  ```

* `/api/v1/export`

  ```text
  Is This an Active Legacy API?

  Does It Enforce the Same Security Controls as the Current Version?
  ```

* These are hypotheses.

* They are not findings until tested and validated.

---

## Example — Client Validation

* JavaScript:

  ```text
  Maximum transfer amount = 1000
  ```

* UI prevents larger values.

* Hypothesis:

  ```text
  Does the Server Independently Enforce the Maximum Transfer Amount?
  ```

* Testing should use an authorized environment and controlled values.

---

## Example — Hidden Property

* JavaScript references:

  ```text
  account.role
  ```

* Profile update request sends:

  ```json
  {
    "name": "Alice"
  }
  ```

* Hypothesis:

  ```text
  Does the Update Endpoint Reject Unauthorized Changes to the "role" Property?
  ```

* This becomes a property-level authorization test.

---

## Example — Feature Flag

* JavaScript references:

  ```text
  enableInvoiceExport
  ```

* The current UI does not display export functionality.

* Hypothesis:

  ```text
  Does the Backend Restrict the Export Function Independently of the Feature Flag?
  ```

---

## Example — Role Route

* JavaScript contains:

  ```text
  /admin/users
  ```

* Normal users cannot navigate to it through the UI.

* Correct question:

  ```text
  Which Backend Requests Power This Function,
  and Does the Server Enforce Role Authorization?
  ```

* Do not confuse route visibility with backend authorization.

---

## Handling Large JavaScript Applications

* Large applications may contain hundreds of JavaScript files.

* Prioritize:

  ```text
  Main Application Bundles

  Route Chunks

  Authentication Code

  Account Functions

  Admin Functions

  API Clients

  File Handling

  Payment Logic

  Export / Import

  GraphQL

  WebSocket Code
  ```

---

## Avoid Noise

* Vendor bundles may contain large amounts of third-party library code.

* Example:

  ```text
  Framework Runtime

  UI Libraries

  Utility Libraries
  ```

* Prioritize application-specific bundles first.

---

## Naming Clues

* Useful filenames may include:

  ```text
  account

  admin

  dashboard

  auth

  billing

  orders

  settings

  reports
  ```

* Hashed filenames require content-based analysis instead.

---

## Differential Role Analysis

* If authorized test accounts with different roles exist:

  ```text
  Login as User

  ↓

  Record Loaded JavaScript and Requests

  ↓

  Login as Admin

  ↓

  Record Loaded JavaScript and Requests

  ↓

  Compare
  ```

* Differences may reveal:

  ```text
  Privileged Bundles

  Additional Routes

  Additional API Calls

  Different Features
  ```

---

## Important Limitation

* If privileged code is sent only to privileged users, a normal user may not discover it directly.

* This is not itself a vulnerability.

* It simply affects attack-surface visibility.

---

## Client-Side Storage

* Inspect application behavior involving:

  ```text
  Cookies

  Local Storage

  Session Storage
  ```

* Look for:

  ```text
  Authentication State

  Feature Configuration

  User Identifiers

  Application State
  ```

* Determine whether sensitive security decisions depend improperly on client-controlled state.

---

## Example — Client-Controlled Role State

* Suppose local storage contains:

  ```text
  role=user
  ```

* Changing it causes an admin menu to appear.

* This alone proves only:

  ```text
  UI Authorization Is Client-Controlled
  ```

* A security vulnerability requires testing whether privileged backend functions are also improperly accessible.

---

## Client-Side State Principle

```text
UI Change

≠

Security Impact
```

* Always validate server-side behavior.

---

## JavaScript and Reconnaissance

* JavaScript analysis complements:

  ```text
  HTTP History

  Site Map

  Manual Browsing

  API Documentation

  Application Behavior
  ```

* No single source should be treated as complete.

---

## Evidence Quality

* For important JavaScript discoveries, preserve:

  ```text
  JavaScript File

  Relevant Code Context

  Endpoint or Property

  Corresponding HTTP Request

  Authentication Context

  Validation Result
  ```

* This creates a traceable path from discovery to testing.

---

## Professional Tips

* Browse broadly before analyzing JavaScript so lazy-loaded chunks are captured.
* Prioritize application-specific code over large vendor bundles.
* Search strategically rather than reading every line.
* Record whether an endpoint was observed live or only referenced in code.
* Treat JavaScript discoveries as leads, not confirmed vulnerabilities.
* Correlate endpoint strings with HTTP history and Site Map.
* Distinguish client routes from backend endpoints.
* Search for API versions, role names, permissions, GraphQL, and WebSockets.
* Treat frontend configuration as public unless there is evidence it contains a genuine secret.
* Validate suspected secrets before reporting them.
* Use source maps to improve understanding when they are legitimately exposed.
* Do not assume source-map exposure automatically creates meaningful impact.
* Convert client-side restrictions into server-side security questions.
* Confirm scope before interacting with newly discovered hosts.
* Update the architecture map continuously as new interfaces are discovered.

---

## Common Mistakes

* Looking only at HTML forms and visible links.
* Assuming every SPA route is a backend endpoint.
* Ignoring background API traffic.
* Analyzing only the initial JavaScript bundle.
* Forgetting lazy-loaded chunks.
* Reading huge vendor bundles before application-specific code.
* Treating every string containing `admin` as a vulnerability.
* Treating every API key-like value as a secret.
* Reporting source maps without demonstrating meaningful exposure.
* Assuming hidden UI functionality lacks backend authorization.
* Assuming client-side validation is the only validation.
* Confusing a changed interface with a security compromise.
* Actively testing third-party hosts discovered in JavaScript without authorization.
* Assuming an endpoint referenced in old code is still active.
* Failing to correlate JavaScript discoveries with actual HTTP traffic.
* Failing to preserve evidence showing where a discovered endpoint originated.

---

## Practice Tasks

### Task 1 — Select an Authorized SPA

* Use a deliberately vulnerable application, system you own, or explicitly authorized target.

---

### Task 2 — Browse Through Burp

* Visit as much application functionality as possible.

* Record:

  ```text
  Pages Visited

  Features Used

  Roles Used
  ```

---

### Task 3 — Identify JavaScript Files

* Use:

  ```text
  Proxy → HTTP History

  Target → Site Map
  ```

* Record the main application bundles and chunks.

---

### Task 4 — Identify Lazy-Loaded Files

* Navigate to different application features.

* Observe whether new JavaScript files appear.

---

### Task 5 — Search JavaScript

* Search for:

  ```text
  /api/

  admin

  role

  token

  graphql

  websocket

  upload

  export
  ```

---

### Task 6 — Build an Endpoint Inventory

* Record at least five endpoints discovered through JavaScript if available.

* Mark each as:

  ```text
  Observed Live

  or

  JavaScript Only
  ```

---

### Task 7 — Identify Parameters

* Record security-relevant:

  ```text
  IDs

  Roles

  Status Fields

  Tenant IDs

  Prices

  Permissions
  ```

---

### Task 8 — Identify API Versions

* Look for:

  ```text
  v1

  v2

  v3
  ```

* Record differences.

---

### Task 9 — Check for Source Maps

* Determine whether relevant JavaScript references:

  ```text
  .map
  ```

* If available, assess what additional information they expose.

---

### Task 10 — Identify Client-Side Security Logic

* Find at least one example of:

  ```text
  Input Validation

  Role Check

  Feature Flag

  Conditional Route
  ```

* Convert it into a server-side security hypothesis.

---

### Task 11 — Correlate With Burp Traffic

* For every high-value discovery:

  ```text
  Search HTTP History

  ↓

  Identify Corresponding Request

  ↓

  Record Authentication Context
  ```

---

### Task 12 — Update Architecture Map

* Add newly discovered:

  ```text
  Hosts

  APIs

  Versions

  GraphQL

  WebSockets

  Features
  ```

---

## Interview Questions

1. What is a single-page application?
2. How does an SPA differ from a traditional server-rendered application?
3. Why can the initial HTML provide an incomplete view of an SPA's attack surface?
4. Why is Burp HTTP history particularly useful when testing SPAs?
5. What is the difference between a client-side route and a backend API endpoint?
6. Why is JavaScript useful for attack-surface discovery?
7. What security-relevant information can JavaScript reveal?
8. What are JavaScript bundles?
9. What is lazy loading?
10. Why should testers navigate broadly before completing JavaScript analysis?
11. How can Burp be used to identify JavaScript files?
12. Which strings would you search for when analyzing JavaScript?
13. Why should an endpoint found in JavaScript not automatically be considered active?
14. How should JavaScript-only endpoints be documented?
15. How can JavaScript reveal hidden object properties?
16. Why does the existence of a property not mean users are authorized to modify it?
17. Why is client-side validation insufficient as a security control?
18. How would you test whether a client-side restriction is enforced by the server?
19. Why are client-side role checks useful to a penetration tester?
20. Why does hiding an admin button not provide authorization?
21. What are feature flags?
22. Why can feature flags reveal hidden attack surface?
23. What is a source map?
24. Why can source maps help security testing?
25. Why is source-map exposure not automatically a vulnerability?
26. How should suspected secrets found in JavaScript be validated?
27. Why is an `apiKey` string not automatically sensitive?
28. How can JavaScript reveal API versions?
29. How can JavaScript help discover GraphQL?
30. How can JavaScript help discover WebSocket endpoints?
31. Why must newly discovered hosts be checked against scope?
32. How can JavaScript reveal authentication architecture?
33. What security considerations apply to tokens stored in local storage?
34. Why does changing a client-side role value and revealing an admin menu not prove authorization bypass?
35. How would you systematically analyze a large JavaScript-heavy application?

---

## Quick Revision

* SPA:

  ```text
  Application Shell

  +

  JavaScript

  +

  Dynamic API Communication
  ```

* Traditional:

  ```text
  Request Page

  ↓

  Server Returns New HTML
  ```

* SPA:

  ```text
  Load App

  ↓

  JavaScript Executes

  ↓

  APIs Called

  ↓

  UI Updated
  ```

* Important distinction:

  ```text
  Client Route

  ≠

  Backend Endpoint
  ```

* JavaScript can reveal:

  ```text
  APIs

  Routes

  Parameters

  Objects

  Roles

  Features

  Versions

  GraphQL

  WebSockets
  ```

* Client-side validation:

  ```text
  UX Control

  ≠

  Server-Side Security Enforcement
  ```

* Hidden UI:

  ```text
  Function Not Visible

  ≠

  Function Not Reachable
  ```

* JavaScript finding:

  ```text
  Discovery Lead

  ≠

  Confirmed Vulnerability
  ```

* Source map:

  ```text
  More Readable Source Context

  ≠

  Automatic Security Finding
  ```

* Secret validation:

  ```text
  Identify

  ↓

  Determine Purpose

  ↓

  Determine Privilege

  ↓

  Validate Exposure

  ↓

  Assess Impact
  ```

* Workflow:

  ```text
  Browse

  ↓

  Capture JavaScript

  ↓

  Search

  ↓

  Extract

  ↓

  Correlate

  ↓

  Validate

  ↓

  Update Attack Surface
  ```

---

## Key Takeaways

* Single-page applications rely heavily on JavaScript and backend APIs rather than full-page server rendering.
* The visible interface may expose only a fraction of the application's real functionality.
* Burp HTTP history reveals API requests and background communication generated dynamically by JavaScript.
* Client-side routes must be distinguished from backend endpoints.
* JavaScript can reveal APIs, parameters, object structures, roles, feature flags, API versions, GraphQL operations, WebSocket endpoints, and hidden functionality.
* Production applications may load functionality through multiple bundles and lazy-loaded chunks.
* Browse broadly before assuming JavaScript discovery is complete.
* Search strategically for security-relevant strings rather than attempting to understand every line of a large bundle.
* JavaScript-discovered endpoints should be correlated with HTTP history and validated before being treated as active.
* Client-side validation and role-based UI restrictions do not replace server-side enforcement.
* Hidden properties and role references should be converted into authorization hypotheses rather than immediately treated as vulnerabilities.
* Frontend configuration is often intentionally public; suspected secrets require contextual validation.
* Source maps can significantly improve application understanding but are not automatically vulnerabilities.
* JavaScript may reveal old API versions and hidden interfaces that expand the attack surface.
* Newly discovered hosts must be checked against authorized scope before active testing.
* Client-side state changes that alter the UI do not prove backend security impact.
* Evidence should connect the JavaScript discovery to the corresponding backend behavior.
* The professional workflow is **browse → capture → identify JavaScript → search → extract attack surface → correlate with traffic → validate → update architecture → generate security hypotheses**.
