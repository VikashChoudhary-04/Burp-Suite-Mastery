# Site Map and Application Structure

## Overview

- The **Site Map** is the primary structural view of discovered application content inside Burp Suite's Target tool.

- It helps transform individual HTTP requests into an organized representation of the application.

- Instead of reviewing traffic only as a chronological stream, the Site Map allows you to examine resources according to their relationship within the target.

- A well-developed Site Map can help identify:

  * Hosts and subdomains.
  * Directories and paths.
  * Application pages.
  * API endpoints.
  * Static resources.
  * Parameters.
  * HTTP methods.
  * Response characteristics.
  * Authentication boundaries.
  * Administrative functionality.
  * High-value application areas.

---

## Learning Objectives

- By the end of this lesson, you should be able to:

  * Explain what the Site Map represents.
  * Understand how Burp populates the Site Map.
  * Navigate hierarchical application structures.
  * Distinguish observed content from confirmed application coverage.
  * Identify high-value resources from application structure.
  * Use the Site Map to support attack-surface analysis.
  * Recognize limitations of passive application mapping.
  * Build a repeatable Site Map review workflow.

---

## What Is the Site Map?

- The Site Map is a hierarchical representation of resources Burp has learned about while interacting with an application.

- A simplified structure might look like:

  ```text
  https://shop.example.com
  │
  ├── /
  ├── login
  ├── register
  ├── account
  │   ├── profile
  │   ├── security
  │   └── billing
  ├── products
  │   ├── 101
  │   └── 102
  ├── checkout
  ├── admin
  └── api
      ├── users
      ├── products
      └── orders
  ```

- This structure provides context that is difficult to obtain from a flat request list.

---

## Why Hierarchical Mapping Matters

- Consider these isolated paths:

  ```text
  /users/101
  /users/102
  /users/103
  /admin/users
  /api/users/101
  ```

- Viewed independently, they are simply endpoints.

- Viewed structurally, they suggest:

  * User objects.
  * Predictable identifiers.
  * Administrative user management.
  * API equivalents of browser functionality.
  * Potential ownership or role boundaries.

- Structure creates security context.

---

## How Burp Builds the Site Map

- Burp can learn about application content from observed and discovered traffic.

- Common sources include:

  * Browser requests passing through Proxy.
  * Responses returned by the application.
  * Requests manually sent through Burp tools.
  * Resources referenced by application content.
  * Authorized crawling or discovery activity where applicable.

- A simplified model is:

  ```text
  Request Observed
        │
        ▼
  Host and Path Identified
        │
        ▼
  Response Analyzed
        │
        ▼
  Resource Added or Updated
        │
        ▼
  Site Map Becomes More Complete
  ```

---

## Observed Does Not Mean Complete

- The Site Map represents what Burp currently knows.

- It should not automatically be treated as a complete inventory.

  ```text
  Site Map

  =

  Observed and Discovered Knowledge

  ≠

  Guaranteed Complete Application
  ```

- Missing functionality may include:

  * Pages that have not been visited.
  * Features available only to another role.
  * Hidden or undocumented APIs.
  * Endpoints referenced dynamically.
  * Conditional application states.
  * Legacy functionality.
  * Administrative interfaces.
  * Features exposed only after specific workflows.
  * JavaScript-generated requests not yet triggered.

---

## Understanding the Site Map Hierarchy

- A typical hierarchy begins with the host.

  ```text
  Host
  │
  ├── Directory
  │   ├── Resource
  │   └── Resource
  │
  └── Directory
      └── Resource
  ```

- Example:

  ```text
  app.example.com
  │
  ├── account
  │   ├── profile
  │   ├── password
  │   └── sessions
  │
  ├── api
  │   ├── profile
  │   ├── orders
  │   └── payments
  │
  └── admin
      ├── users
      └── settings
  ```

- The hierarchy helps reveal functional groupings.

---

## Functional Grouping

- While reviewing the Site Map, classify application areas by purpose.

  ```text
  Authentication
  ├── /login
  ├── /logout
  ├── /register
  └── /forgot-password

  Account Management
  ├── /profile
  ├── /security
  └── /sessions

  Commerce
  ├── /cart
  ├── /checkout
  └── /orders

  API
  ├── /api/users
  ├── /api/orders
  └── /api/payments

  Administration
  └── /admin
  ```

- Functional grouping makes prioritization easier.

---

## What to Look for in a Site Map

### Authentication Functionality

- Look for:

  * Login.
  * Registration.
  * Logout.
  * Password reset.
  * Account recovery.
  * Multi-factor authentication.
  * Session management.

### Authorization-Relevant Functionality

- Look for:

  * Object identifiers.
  * User IDs.
  * Account IDs.
  * Order IDs.
  * Document IDs.
  * Project IDs.
  * Tenant identifiers.
  * Administrative paths.

### Sensitive Operations

- Look for:

  * Password changes.
  * Email changes.
  * Role changes.
  * Payment actions.
  * Account deletion.
  * API-key management.
  * File uploads.
  * Data exports.

### APIs

- Look for:

  * `/api/`
  * `/v1/`
  * `/v2/`
  * `/graphql`
  * JSON endpoints.
  * Mobile-backend endpoints.
  * Internal-looking service routes.

---

## Static and Dynamic Resources

- Not every resource deserves equal testing priority.

### Static Resources

- Examples:

  ```text
  /images/logo.png
  /css/main.css
  /fonts/app.woff2
  ```

- These may provide useful contextual information but are usually lower priority for direct manual testing.

### Dynamic Resources

- Examples:

  ```text
  /account/profile
  /orders/1234
  /api/users/42
  /admin/settings
  ```

- Dynamic resources are often more security-relevant because they process user identity, input, state, or authorization decisions.

---

## Parameters and Inputs

- Resources become more interesting when they accept controllable input.

- Examples:

  ```text
  GET /search?q=laptop

  GET /orders?id=1042

  POST /profile/update

  POST /api/users/42/role
  ```

- Ask:

  * What parameters are accepted?
  * Where do values originate?
  * Are identifiers predictable?
  * Does the request change state?
  * Does authorization depend on a parameter?
  * Does the same function exist in multiple interfaces?

---

## HTTP Methods

- The same path may support different HTTP methods.

  ```text
  GET /api/profile
  PUT /api/profile
  DELETE /api/profile
  ```

- Different methods can represent different security-sensitive operations.

- Do not assume that understanding one request to a path means you understand every operation available at that path.

---

## Response Characteristics

- Response information can help prioritize resources.

- Useful observations include:

  * Status codes.
  * Content types.
  * Response sizes.
  * Redirect behavior.
  * Authentication requirements.
  * Error responses.
  * JSON versus HTML behavior.

- Differences can reveal distinct application states or access controls.

---

## Mapping Authentication States

- Compare what appears before and after authentication.

  ```text
  Unauthenticated
  │
  ├── /
  ├── /login
  └── /register

  Authenticated
  │
  ├── /dashboard
  ├── /profile
  ├── /orders
  └── /api/account
  ```

- This helps identify authentication boundaries.

---

## Mapping Multiple Roles

- If an authorized test provides multiple roles, map them separately.

  ```text
  Standard User
  │
  ├── /dashboard
  ├── /profile
  └── /orders

  Administrator
  │
  ├── /dashboard
  ├── /users
  ├── /reports
  └── /admin/settings
  ```

- Compare:

  * Visible functionality.
  * Requests.
  * API calls.
  * Object access.
  * Privileged actions.

- Role differences create authorization hypotheses.

---

## Mapping Object Relationships

- Suppose the Site Map reveals:

  ```text
  /projects/101
  /projects/102
  /projects/103

  /api/projects/101
  /api/projects/102
  /api/projects/103
  ```

- Record the relationship:

  ```text
  Browser Resource

  /projects/{project_id}

          │
          ▼

  API Resource

  /api/projects/{project_id}
  ```

- This may help identify where ownership checks should be tested in an authorized environment.

---

## Mapping Browser and API Functionality

- Modern applications often expose browser actions through APIs.

  ```text
  Browser Action
        │
        ▼
  Click "Update Profile"
        │
        ▼
  PATCH /api/profile
        │
        ▼
  JSON Response
        │
        ▼
  Interface Updates
  ```

- Site Map analysis should therefore include both visible pages and underlying API requests.

---

## JavaScript and Hidden Functionality

- JavaScript files may reveal application routes or API paths that have not yet been triggered.

- Examples might include strings such as:

  ```text
  /api/v2/account
  /internal/preferences
  /feature-flags
  /admin/reports
  ```

- Discovery does not prove that a route exists or is authorized for testing.

- Treat these references as hypotheses requiring scope verification and controlled validation.

---

## Site Map Prioritization

- A useful prioritization model is:

  ```text
  Highest Priority
  │
  ├── Authentication
  ├── Authorization
  ├── Administrative Functions
  ├── Sensitive Data
  ├── State-Changing Operations
  ├── APIs
  ├── File Handling
  └── Business-Critical Workflows
  │
  ▼
  Lower Priority
      ├── Static Assets
      └── Low-Impact Informational Pages
  ```

- Priority depends on the application and engagement objectives.

---

## Building an Attack-Surface Inventory

- Convert the Site Map into an investigation inventory.

  ```text
  Host:
  app.example.com

  Authentication:
  /login
  /register
  /forgot-password

  Account:
  /profile
  /security
  /sessions

  Objects:
  /orders/{id}
  /documents/{id}

  APIs:
  /api/profile
  /api/orders
  /api/documents

  Privileged:
  /admin
  /api/admin/users

  File Handling:
  /documents/upload
  ```

- This is more useful than an unstructured list of hundreds of URLs.

---

## Baseline Before Modification

- Before changing requests, understand normal behavior.

  ```text
  Normal Request
       │
       ▼
  Normal Response
       │
       ▼
  Record Baseline
       │
       ▼
  Modify One Variable
       │
       ▼
  Compare Result
  ```

- Site Map review helps select the correct baseline requests for deeper testing.

---

## Site Map Review Workflow

```text
Confirm Authorized Scope
        │
        ▼
Browse Major Features
        │
        ▼
Review Hosts
        │
        ▼
Review Directories
        │
        ▼
Identify Dynamic Resources
        │
        ▼
Identify Inputs and Objects
        │
        ▼
Map Authentication and Roles
        │
        ▼
Identify APIs
        │
        ▼
Prioritize High-Value Areas
        │
        ▼
Send Selected Requests for Testing
```

---

## Example Investigation

- Assume an authorized application reveals:

  ```text
  portal.example
  │
  ├── /login
  ├── /dashboard
  ├── /customers
  │   ├── /1001
  │   └── /1002
  ├── /reports
  │   └── /export
  └── /api
      ├── /customers/1001
      ├── /customers/1002
      └── /reports
  ```

- Observations:

  * Customer objects use numeric identifiers.
  * Browser and API representations both exist.
  * Report export may expose sensitive data.
  * Authentication and authorization boundaries require mapping.

- Potential hypotheses:

  * Customer objects should enforce ownership or role restrictions.
  * Report exports should require appropriate authorization.
  * API access controls should match browser access controls.

- These are hypotheses, not findings.

---

## Common Mistakes

- Treating the Site Map as automatically complete.

- Reviewing only page names and ignoring underlying API traffic.

- Failing to distinguish static resources from dynamic functionality.

- Ignoring HTTP methods.

- Ignoring identifiers and object relationships.

- Testing discovered third-party hosts without confirming authorization.

- Focusing only on unusual-looking paths instead of business-critical functionality.

- Failing to revisit the Site Map after authenticating or changing roles.

---

## Professional Tips

- Keep scope filters enabled where appropriate.

- Review the Site Map after completing each major workflow.

- Compare unauthenticated and authenticated application structures.

- Compare different authorized roles.

- Record important identifiers and object types.

- Group endpoints by functionality rather than memorizing individual URLs.

- Prioritize state-changing and authorization-sensitive operations.

- Treat JavaScript-discovered paths as leads, not confirmed vulnerabilities.

- Revisit mapping whenever new functionality appears.

---

## Practice Exercise

- Using an explicitly authorized training application:

1. Browse the application while Burp records traffic.
2. Open the Target Site Map.
3. Identify:

   * All observed hosts.
   * Major directories.
   * Authentication endpoints.
   * Account-management endpoints.
   * APIs.
   * Object identifiers.
   * State-changing requests.
   * Administrative functionality.

4. Build a functional inventory:

   ```text
   Authentication:

   Account:

   Objects:

   APIs:

   Privileged Functions:

   File Handling:

   Business-Critical Workflows:
   ```

5. Select five high-value resources.
6. For each resource, write one security hypothesis.
7. Verify that every selected target is authorized before testing.

---

## Interview Questions

1. What is the Site Map in Burp Suite?
2. How does Burp populate the Site Map?
3. Why should the Site Map not be considered automatically complete?
4. How does hierarchical mapping improve security testing?
5. What types of resources should receive higher testing priority?
6. Why should browser pages and underlying APIs both be mapped?
7. How can multiple user roles affect the Site Map?
8. Why are object identifiers important during attack-surface analysis?
9. What is the difference between an observed endpoint and a confirmed vulnerability?
10. How would you convert a large Site Map into a practical testing plan?

---

## Quick Revision

```text
Site Map
│
├── Hosts
├── Directories
├── Pages
├── APIs
├── Parameters
├── Methods
└── Resources
```

- Remember:

  ```text
  Observed ≠ Complete

  Discovered ≠ Authorized

  Structure → Context

  Context → Hypotheses

  Hypotheses → Controlled Testing
  ```

---

## Key Takeaways

- The Site Map organizes discovered application content hierarchically.

- It provides structural context that a chronological traffic list cannot provide by itself.

- Site Map coverage depends on what has been observed or discovered and may be incomplete.

- Effective review focuses on functionality, security boundaries, inputs, objects, APIs, and privileged operations.

- Browser and API functionality should be mapped together.

- Application mapping should produce prioritized security hypotheses rather than random testing.

- Always confirm authorization before interacting with discovered assets.
