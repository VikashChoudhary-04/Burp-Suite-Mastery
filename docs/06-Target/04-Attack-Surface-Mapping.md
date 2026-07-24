# Attack Surface Mapping

## Overview

- **Attack surface mapping** is the process of identifying and organizing the parts of an application that can affect security.

- Burp Suite's Target tool helps turn observed application traffic into a structured map of:

  * Hosts.
  * Application paths.
  * Endpoints.
  * Parameters.
  * APIs.
  * User roles.
  * Objects and identifiers.
  * State-changing actions.
  * Trust boundaries.
  * Sensitive workflows.

- The goal is not simply to collect as many URLs as possible.

- The goal is to understand **where security assumptions exist and where meaningful testing should be focused**.

---

## Learning Objectives

- By the end of this lesson, you should be able to:

  * Explain what an application attack surface is.
  * Convert Site Map observations into a structured attack-surface inventory.
  * Identify high-value application functionality.
  * Recognize authentication and authorization boundaries.
  * Identify security-relevant inputs and object identifiers.
  * Map browser functionality to underlying APIs.
  * Prioritize application areas for deeper testing.
  * Distinguish observations, hypotheses, and validated findings.

---

## What Is an Attack Surface?

- An application's attack surface consists of the interfaces, functionality, inputs, objects, and trust boundaries through which users or systems interact with it.

- Examples include:

  ```text
  Application Attack Surface
  │
  ├── Authentication
  ├── Authorization
  ├── Account Management
  ├── APIs
  ├── Administrative Functions
  ├── File Handling
  ├── Search and Input
  ├── Business Workflows
  ├── Integrations
  └── Real-Time Communication
  ```

- Every application has a different attack surface.

---

## Attack Surface Mapping vs URL Collection

- URL collection answers:

  ```text
  What paths have been observed?
  ```

- Attack-surface mapping asks:

  ```text
  What does this functionality do?

  Who can access it?

  What data does it handle?

  What objects does it reference?

  What trust boundary does it cross?

  What security assumption does it depend on?
  ```

- A long URL list without context is less useful than a smaller, well-classified inventory.

---

## Example

- Suppose Burp reveals:

  ```text
  /login
  /register
  /profile
  /profile/password
  /orders/1001
  /orders/1002
  /documents/upload
  /admin/users
  /api/orders/1001
  /api/users/me
  ```

- A structured attack-surface map might be:

  ```text
  Authentication
  ├── /login
  └── /register

  Account Management
  ├── /profile
  └── /profile/password

  Object Authorization
  ├── /orders/{order_id}
  └── /api/orders/{order_id}

  File Handling
  └── /documents/upload

  Privileged Functionality
  └── /admin/users

  User API
  └── /api/users/me
  ```

- This structure immediately provides better testing context.

---

## Start With Major Application Functions

- First identify the major functional areas.

- Common categories include:

  * Authentication.
  * Registration.
  * Account recovery.
  * Account management.
  * User-generated content.
  * Search.
  * File upload and download.
  * Orders and payments.
  * Messaging.
  * Administrative functionality.
  * APIs.
  * Integrations.
  * Reporting.
  * Import and export.
  * Role management.

- Functional classification prevents random testing.

---

## Authentication Surface

- Identify every authentication-related function.

  ```text
  Authentication
  │
  ├── Login
  ├── Logout
  ├── Registration
  ├── Password Reset
  ├── Account Recovery
  ├── MFA
  ├── Remember-Me
  └── Session Management
  ```

- Questions to record:

  * What credentials are accepted?
  * Are multiple authentication mechanisms present?
  * Are external identity providers used?
  * Where are sessions created?
  * Where are sessions terminated?
  * Are sensitive actions protected by re-authentication?
  * Are different roles authenticated through different interfaces?

---

## Authorization Surface

- Authorization determines what an authenticated identity is allowed to access or perform.

- Map:

  * User roles.
  * Object ownership.
  * Tenant boundaries.
  * Administrative privileges.
  * Feature permissions.
  * State-dependent permissions.

  ```text
  User A
      │
      └── Own Objects

  User B
      │
      └── Own Objects

  Administrator
      │
      └── Privileged Functions
  ```

- Authorization boundaries are often among the highest-value areas for manual testing.

---

## Object Identifiers

- Look for identifiers such as:

  ```text
  /users/42
  /orders/1042
  /documents/8f23
  /projects/7
  /accounts/10001
  ```

- Also inspect:

  * Query parameters.
  * JSON fields.
  * Form fields.
  * Headers.
  * Cookies.
  * GraphQL variables.

- Record what each identifier appears to represent.

---

## Object Relationship Mapping

- Build relationships between objects and identities.

  ```text
  User
   │
   ├── Account
   │    ├── Profile
   │    └── Billing
   │
   ├── Orders
   │    ├── Order 1001
   │    └── Order 1002
   │
   └── Documents
        ├── Document A
        └── Document B
  ```

- This helps identify where ownership checks should exist.

---

## Role Mapping

- If multiple authorized roles are available, map their functionality.

  ```text
  Guest
  │
  ├── View Products
  └── Register

  User
  │
  ├── Manage Profile
  ├── Place Orders
  └── View Own Orders

  Manager
  │
  ├── View Reports
  └── Manage Team

  Administrator
  │
  ├── Manage Users
  ├── Change Roles
  └── Configure System
  ```

- Differences between roles create testable authorization hypotheses.

---

## State-Changing Functionality

- Identify requests that modify application state.

- Examples:

  ```text
  POST /account/email

  PATCH /api/profile

  DELETE /documents/42

  POST /orders/1001/cancel

  PUT /admin/users/42/role
  ```

- State-changing actions deserve careful review because they may affect:

  * Accounts.
  * Permissions.
  * Financial state.
  * Data integrity.
  * Ownership.
  * Workflow progression.

---

## Read vs Write Operations

- Classify operations.

  ```text
  Read
  ├── View profile
  ├── View order
  └── Download report

  Write
  ├── Update profile
  ├── Create order
  ├── Delete document
  └── Change role
  ```

- Write operations often carry greater security impact, but sensitive read operations can also expose confidential data.

---

## Sensitive Data Surface

- Identify functionality handling:

  * Personal information.
  * Authentication secrets.
  * Financial information.
  * Private documents.
  * API keys.
  * Tokens.
  * Internal reports.
  * Administrative data.

- Ask:

  ```text
  Who should be able to access this?

  Under what conditions?

  Through which interfaces?
  ```

---

## API Attack Surface

- Modern applications often rely heavily on APIs.

- Map:

  * API hosts.
  * Versioned paths.
  * Resources.
  * Methods.
  * Authentication mechanisms.
  * Object identifiers.
  * State-changing operations.

  ```text
  /api/v1/users
  /api/v1/orders
  /api/v2/profile
  /graphql
  ```

- Do not assume the browser interface and API enforce identical security controls.

---

## Browser-to-API Mapping

- Connect visible actions to network requests.

  ```text
  User clicks:
  "Change Email"
        │
        ▼
  Browser sends:
  PATCH /api/account/email
        │
        ▼
  Server validates:
  Session + authorization + input
        │
        ▼
  Account updated
  ```

- Mapping this relationship helps reveal the true security-relevant interface.

---

## File Handling Surface

- Identify:

  * Upload endpoints.
  * Download endpoints.
  * Import functionality.
  * Export functionality.
  * Attachment handling.
  * Image processing.
  * Document previews.

  ```text
  /upload
  /attachments
  /documents/{id}/download
  /import
  /export
  ```

- Record:

  * Accepted file types.
  * Ownership relationships.
  * Access requirements.
  * Processing behavior.
  * Storage and retrieval paths.

---

## Administrative Surface

- Administrative functionality deserves high priority.

- Examples:

  ```text
  /admin
  /admin/users
  /admin/settings
  /api/admin/reports
  ```

- Map:

  * Required roles.
  * Privileged actions.
  * User-management functions.
  * Configuration changes.
  * Sensitive reporting.
  * Audit functionality.

---

## Business Logic Surface

- Business logic describes how the application expects workflows to operate.

- Examples:

  ```text
  Add Item
      ↓
  Cart
      ↓
  Checkout
      ↓
  Payment
      ↓
  Order Confirmation
  ```

- Security-relevant questions include:

  * Can steps occur out of order?
  * Can a completed action be repeated?
  * Are values trusted from the client?
  * Does workflow state control authorization?
  * Are limits enforced server-side?

- At this stage, record the workflow before attempting to test it.

---

## Workflow Mapping

- Represent workflows explicitly.

  ```text
  Registration
      │
      ▼
  Email Verification
      │
      ▼
  Profile Setup
      │
      ▼
  Account Activation
  ```

- Or:

  ```text
  Select Product
      │
      ▼
  Add to Cart
      │
      ▼
  Apply Discount
      │
      ▼
  Checkout
      │
      ▼
  Payment
      │
      ▼
  Fulfillment
  ```

- Each transition may contain assumptions worth investigating.

---

## Trust Boundaries

- A trust boundary exists where security decisions change.

- Examples:

  ```text
  Unauthenticated
        │
        ▼
  Authenticated
  ```

  ```text
  Standard User
        │
        ▼
  Administrator
  ```

  ```text
  Tenant A
        │
        ╳
  Tenant B
  ```

  ```text
  Browser
        │
        ▼
  Backend API
  ```

- Mark these boundaries in your attack-surface map.

---

## Input Surface

- Identify controllable inputs.

- Common locations:

  * URL paths.
  * Query strings.
  * Form fields.
  * JSON bodies.
  * XML bodies.
  * Headers.
  * Cookies.
  * File uploads.
  * WebSocket messages.
  * GraphQL variables.

  ```text
  Request
  │
  ├── Path
  ├── Query
  ├── Headers
  ├── Cookies
  └── Body
  ```

- Inputs are potential control points for security testing.

---

## Integration Surface

- Applications may integrate with:

  * Payment providers.
  * OAuth providers.
  * Cloud storage.
  * Email systems.
  * Webhooks.
  * External APIs.
  * Analytics.
  * Support systems.

- Map integrations, but verify authorization before testing third-party systems.

---

## Webhook Surface

- If an application allows users to configure callback destinations or integrations, record:

  * Creation endpoint.
  * Update endpoint.
  * Trigger conditions.
  * Authentication.
  * Ownership.
  * Delivery behavior.

- Treat the functionality as part of the application's attack surface while respecting all scope restrictions.

---

## WebSocket Surface

- Real-time applications may use WebSockets for:

  * Chat.
  * Notifications.
  * Trading.
  * Collaboration.
  * Live dashboards.

- Map:

  * Connection endpoints.
  * Authentication state.
  * Message types.
  * Object identifiers.
  * Role-sensitive actions.

---

## Hidden and Conditional Functionality

- Some functionality appears only when:

  * A specific role is used.
  * A feature flag is enabled.
  * An account reaches a certain state.
  * A workflow step is completed.
  * A particular object exists.
  * JavaScript triggers a request.

- Attack-surface mapping is iterative.

---

## Attack Surface by User State

- Compare application states.

  ```text
  Guest
  │
  ├── Login
  ├── Register
  └── Public Content

  Authenticated User
  │
  ├── Dashboard
  ├── Profile
  ├── Orders
  └── API

  Administrator
  │
  ├── User Management
  ├── Reports
  └── Configuration
  ```

- Each state may expose a different surface.

---

## Prioritization Model

- A simple prioritization framework:

  ```text
  Priority =

  Security Impact
      +
  Privilege Boundary
      +
  Sensitive Data
      +
  State Change
      +
  User-Controlled Input
      +
  Business Criticality
  ```

- This is a reasoning model, not a mathematical score.

---

## High-Priority Areas

- Common high-priority areas include:

  * Authentication.
  * Account recovery.
  * Authorization.
  * Administrative functionality.
  * Object ownership.
  * APIs.
  * File handling.
  * Payment workflows.
  * Role management.
  * Sensitive exports.
  * Multi-tenant boundaries.

---

## Lower-Priority Areas

- Lower-priority areas may include:

  * Static images.
  * Fonts.
  * Public informational pages.
  * Non-sensitive static assets.

- Lower priority does not mean irrelevant.

- Static resources may still reveal useful application information.

---

## Observations vs Hypotheses vs Findings

- Keep these concepts separate.

### Observation

  ```text
  GET /orders/1001
  ```

### Hypothesis

  ```text
  Order access may depend on the numeric identifier.
  ```

### Test

  ```text
  Perform an authorized controlled comparison using permitted accounts.
  ```

### Finding

  ```text
  Only after reproducible evidence demonstrates a security failure.
  ```

- Never report a vulnerability based only on an interesting endpoint.

---

## Attack-Surface Inventory Template

```text
Target:

Scope:

Authentication:
-

Roles:
-

Account Management:
-

Objects and Identifiers:
-

APIs:
-

State-Changing Operations:
-

Administrative Functions:
-

File Handling:
-

Sensitive Data:
-

Business Workflows:
-

Integrations:
-

WebSockets:
-

Trust Boundaries:
-

High-Priority Hypotheses:
-
```

---

## Professional Mapping Workflow

```text
Confirm Scope
      │
      ▼
Browse Application
      │
      ▼
Review Site Map
      │
      ▼
Identify Functional Areas
      │
      ▼
Map Roles and States
      │
      ▼
Map Objects and Identifiers
      │
      ▼
Map Inputs and Methods
      │
      ▼
Map APIs and Workflows
      │
      ▼
Identify Trust Boundaries
      │
      ▼
Prioritize High-Value Areas
      │
      ▼
Write Testable Hypotheses
      │
      ▼
Perform Controlled Testing
```

---

## Example Attack-Surface Map

- Assume an authorized application exposes:

  ```text
  portal.example
  │
  ├── /login
  ├── /forgot-password
  ├── /dashboard
  ├── /account
  │   ├── /profile
  │   └── /security
  ├── /projects
  │   ├── /101
  │   └── /102
  ├── /files
  │   └── /upload
  ├── /admin
  │   └── /users
  └── /api
      ├── /projects/101
      ├── /projects/102
      └── /users/me
  ```

- Map it:

  ```text
  Authentication
  ├── /login
  └── /forgot-password

  Account Security
  ├── /account/profile
  └── /account/security

  Object Authorization
  ├── /projects/{id}
  └── /api/projects/{id}

  File Handling
  └── /files/upload

  Privileged Functionality
  └── /admin/users

  API Identity
  └── /api/users/me
  ```

- Possible hypotheses:

  * Project objects should enforce ownership or membership.
  * Administrative functionality should enforce role authorization.
  * File access should enforce ownership and access rules.
  * Browser and API authorization behavior should be consistent.

---

## Common Mistakes

- Collecting URLs without classifying functionality.

- Treating every endpoint as equally important.

- Ignoring roles and user states.

- Ignoring object identifiers.

- Ignoring underlying API calls.

- Failing to map state-changing operations.

- Testing before establishing normal behavior.

- Confusing a hypothesis with a vulnerability.

- Ignoring business workflows.

- Testing third-party integrations without authorization.

---

## Professional Tips

- Build the attack-surface map while browsing rather than waiting until the end.

- Group endpoints by functionality.

- Record object types separately from individual object IDs.

- Mark state-changing requests.

- Compare functionality across authorized roles.

- Map browser actions to backend requests.

- Record trust boundaries explicitly.

- Prioritize security-critical workflows.

- Update the map whenever new functionality is discovered.

- Use the map to decide what should be sent to Repeater or other testing tools.

---

## Practice Exercise

- Use an explicitly authorized training application.

1. Browse every major application feature.
2. Review the Site Map.
3. Create categories for:

   * Authentication.
   * Account management.
   * Authorization-relevant objects.
   * APIs.
   * Administrative functions.
   * File handling.
   * Sensitive data.
   * Business workflows.

4. Record at least five object identifiers or input points.
5. Identify all visible user roles.
6. Identify at least three trust boundaries.
7. Mark all state-changing operations.
8. Select five high-priority testing areas.
9. Write one hypothesis for each.
10. Verify scope before performing deeper tests.

---

## Interview Questions

1. What is an application attack surface?
2. How is attack-surface mapping different from URL collection?
3. Why should endpoints be grouped by functionality?
4. What makes an application area high priority?
5. Why are object identifiers important?
6. How do user roles affect attack-surface mapping?
7. Why should state-changing operations be identified?
8. What is a trust boundary?
9. Why should browser functionality be mapped to APIs?
10. How do business workflows contribute to the attack surface?
11. What is the difference between an observation, hypothesis, and finding?
12. Why is attack-surface mapping iterative?
13. How would you prioritize a large application?
14. What information belongs in an attack-surface inventory?
15. How does Target help prepare requests for deeper manual testing?

---

## Quick Revision

```text
Attack Surface
│
├── Functions
├── Inputs
├── Objects
├── Roles
├── APIs
├── Workflows
├── State Changes
├── Sensitive Data
└── Trust Boundaries
```

- Remember:

  ```text
  URLs ≠ Complete Attack Surface

  Observation ≠ Vulnerability

  Map → Classify → Prioritize → Hypothesize → Test → Validate
  ```

---

## Key Takeaways

- Attack-surface mapping turns discovered application content into a structured security model.

- The goal is to understand functionality, inputs, objects, roles, workflows, and trust boundaries rather than merely collect URLs.

- Authentication, authorization, administrative functions, APIs, sensitive data, file handling, and state-changing operations commonly deserve high priority.

- Browser actions should be connected to their underlying requests and APIs.

- Object relationships and user roles are essential for understanding authorization boundaries.

- Observations should produce testable hypotheses, not premature vulnerability conclusions.

- A strong attack-surface map provides the foundation for systematic, efficient, and evidence-driven manual testing.
