# Professional Target Workflow

## Overview

- The Target tool should be used as part of a repeatable assessment workflow rather than as an isolated Burp Suite feature.

- A professional Target workflow connects:

  * Authorization and scope.
  * Application browsing.
  * Site Map development.
  * Attack-surface mapping.
  * Endpoint analysis.
  * Role and object mapping.
  * Prioritization.
  * Manual testing.
  * Evidence collection.
  * Coverage tracking.

- The objective is to move systematically from **understanding the application** to **testing well-defined security hypotheses**.

---

## Learning Objectives

- By the end of this lesson, you should be able to:

  * Apply a structured Target workflow from assessment setup through testing.
  * Establish scope before application interaction.
  * Build useful Site Map coverage.
  * Map application functions, roles, objects, APIs, and workflows.
  * Prioritize security-relevant areas.
  * Create and manage testable hypotheses.
  * Move requests from Target into other Burp tools appropriately.
  * Track coverage and evidence throughout an assessment.

---

## Workflow Overview

```text
Authorization
      │
      ▼
Scope Definition
      │
      ▼
Burp Configuration
      │
      ▼
Baseline Browsing
      │
      ▼
Site Map Review
      │
      ▼
Attack-Surface Mapping
      │
      ▼
Endpoint and Object Analysis
      │
      ▼
Role and Workflow Mapping
      │
      ▼
Prioritization
      │
      ▼
Hypothesis-Driven Testing
      │
      ▼
Validation
      │
      ▼
Evidence and Coverage Update
```

---

## Phase 1 — Confirm Authorization

- Before testing, identify the authoritative rules governing the assessment.

- Record:

  * Authorized hosts.
  * Allowed protocols and ports.
  * Path restrictions.
  * Explicit exclusions.
  * Test accounts.
  * Permitted techniques.
  * Automation restrictions.
  * Rate limits.
  * Testing windows.
  * Production-impact restrictions.

- Remember:

  ```text
  Technical Discovery ≠ Authorization
  ```

---

## Phase 2 — Configure Scope

- Configure Burp scope to reflect the authorized target as accurately as possible.

- Verify:

  * Exact hosts.
  * Wildcard rules where explicitly permitted.
  * Ports.
  * Paths.
  * Exclusions.

- Use scope-aware filters to reduce irrelevant traffic.

---

## Phase 3 — Establish a Clean Baseline

- Before aggressive investigation, understand normal application behavior.

- Browse the application as intended.

- Observe:

  * Public functionality.
  * Authentication.
  * Navigation.
  * API traffic.
  * Redirects.
  * Static resources.
  * Third-party dependencies.

- Avoid immediately modifying requests before understanding their normal purpose.

---

## Phase 4 — Build the Site Map

- Exercise major application functionality.

  ```text
  Public Pages
      │
      ▼
  Authentication
      │
      ▼
  Dashboard
      │
      ▼
  Account Functions
      │
      ▼
  Business Workflows
      │
      ▼
  APIs and Background Requests
  ```

- The Site Map becomes more useful as meaningful workflows are exercised.

---

## Phase 5 — Review Hosts

- Review every observed host.

- Classify each as:

  ```text
  Confirmed In Scope

  Confirmed Out of Scope

  Unknown — Requires Verification
  ```

- Do not actively test unknown hosts until authorization is confirmed.

---

## Phase 6 — Organize Functional Areas

- Group application resources by purpose.

  ```text
  Authentication

  Account Management

  User Content

  Orders

  Payments

  Files

  APIs

  Administration

  Reporting
  ```

- This converts a technical resource tree into a business-oriented security map.

---

## Phase 7 — Map Authentication States

- Compare:

  ```text
  Unauthenticated
  Authenticated
  Privileged
  ```

- Record which functionality appears at each state.

- Identify transitions such as:

  ```text
  Login

  Logout

  Session Creation

  Session Termination

  Password Reset

  Re-Authentication
  ```

---

## Phase 8 — Map Roles

- For each authorized role, record:

  * Visible functionality.
  * API requests.
  * Accessible objects.
  * State-changing actions.
  * Privileged operations.

- Example:

  ```text
  User
  ├── Profile
  ├── Orders
  └── Documents

  Manager
  ├── Team
  ├── Reports
  └── Approvals

  Administrator
  ├── Users
  ├── Roles
  └── Configuration
  ```

---

## Phase 9 — Map Objects

- Identify important object types.

  ```text
  User
  Account
  Project
  Order
  Document
  Team
  Report
  ```

- Record:

  * Identifier format.
  * Ownership.
  * Parent-child relationships.
  * Allowed roles.
  * CRUD operations.

---

## Phase 10 — Map Endpoint Families

- Group related endpoints.

  ```text
  Projects

  GET    /api/projects
  POST   /api/projects
  GET    /api/projects/{id}
  PATCH  /api/projects/{id}
  DELETE /api/projects/{id}
  ```

- This helps reveal the complete lifecycle of an object.

---

## Phase 11 — Map Inputs

- Record security-relevant input locations.

  ```text
  Path

  Query String

  Form Data

  JSON

  XML

  Headers

  Cookies

  Files

  WebSocket Messages
  ```

- Focus especially on inputs controlling:

  * Identity.
  * Object selection.
  * Roles.
  * Prices.
  * Workflow state.
  * Redirects.
  * File handling.

---

## Phase 12 — Map State-Changing Operations

- Mark operations that alter application state.

  ```text
  Create

  Update

  Delete

  Approve

  Cancel

  Transfer

  Upload

  Change Role

  Reset Credential
  ```

- State-changing operations often deserve higher testing priority.

---

## Phase 13 — Map Sensitive Data

- Identify endpoints returning or modifying:

  * Personal data.
  * Financial information.
  * Private files.
  * Authentication secrets.
  * API keys.
  * Administrative information.
  * Internal reports.

- Record expected access boundaries.

---

## Phase 14 — Map Business Workflows

- Document important sequences.

  ```text
  Select Item
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

- Understand normal workflow order before testing deviations.

---

## Phase 15 — Identify Trust Boundaries

- Mark boundaries such as:

  ```text
  Guest → User

  User → Administrator

  Tenant A → Tenant B

  Browser → API

  Application → Third Party
  ```

- Security controls should exist at these boundaries.

---

## Phase 16 — Build a Coverage Matrix

```text
Area                 Guest   User   Manager   Admin   Status
-------------------------------------------------------------
Authentication       Yes     N/A    N/A       N/A     Reviewed
Profile              No      Yes    Yes       Yes     Reviewed
Projects             No      Yes    Yes       Yes     Partial
Reports              No      No     Yes       Yes     Not Tested
User Management      No      No     No        Yes     Not Tested
```

- Update this continuously.

---

## Phase 17 — Prioritize

- Prioritize based on:

  ```text
  Potential Impact
       +
  Privilege Boundary
       +
  Sensitive Data
       +
  State Change
       +
  Object Ownership
       +
  Business Criticality
  ```

- High-value areas commonly include:

  * Authentication.
  * Authorization.
  * Account recovery.
  * Administration.
  * APIs.
  * File handling.
  * Payment logic.
  * Multi-tenant boundaries.

---

## Phase 18 — Create Hypotheses

- Convert observations into precise expectations.

- Example:

  ```text
  Observation:
  GET /api/projects/{id}

  Context:
  Projects belong to different users.

  Hypothesis:
  The server should enforce project membership before returning data.
  ```

- A good hypothesis states what security control should exist.

---

## Phase 19 — Capture Baselines

- Before modifying a request, preserve normal behavior.

- Record:

  * Original request.
  * Session.
  * Role.
  * Object ownership.
  * Response.
  * Application state.

- This baseline becomes the comparison point.

---

## Phase 20 — Send Selected Requests to Repeater

```text
Target
   │
   ▼
Interesting Request
   │
   ▼
Send to Repeater
   │
   ▼
Confirm Baseline
   │
   ▼
Change One Variable
   │
   ▼
Compare
```

- Target organizes the investigation.

- Repeater supports controlled manual testing.

---

## Phase 21 — Test One Security Assumption at a Time

- Avoid changing many variables simultaneously.

- Example:

  ```text
  Baseline Request

  Test 1:
  Change Object Identifier

  Test 2:
  Change Session

  Test 3:
  Change Role Context
  ```

- Separate tests make results easier to interpret.

---

## Phase 22 — Validate Results

- Interesting behavior is not automatically a vulnerability.

- Validation should establish:

  * Reproducibility.
  * Expected versus actual behavior.
  * Security impact.
  * Required privileges.
  * Affected scope.
  * Relevant evidence.

---

## Phase 23 — Update Coverage

- After testing an area, update its status.

  ```text
  Mapped

  Baseline Captured

  Tested

  Needs Follow-Up

  Validated Finding

  No Issue Observed
  ```

- Avoid using absolute labels such as "secure."

---

## Phase 24 — Revisit Target

- Return to Target regularly.

- New requests may appear after:

  * Authentication.
  * Role changes.
  * New objects.
  * Feature activation.
  * Workflow completion.
  * Error conditions.
  * Administrative access.

- Mapping is iterative.

---

## Phase 25 — Maintain Evidence

- Preserve enough evidence to reproduce validated issues.

- Useful evidence includes:

  * Baseline request.
  * Test request.
  * Relevant responses.
  * Role context.
  * Object ownership.
  * Reproduction steps.
  * Observed impact.

- Avoid retaining unnecessary sensitive data.

---

## Example End-to-End Workflow

- Assume an authorized SaaS application contains:

  ```text
  /login
  /dashboard
  /projects
  /projects/{id}
  /api/projects/{id}
  /reports/export
  /admin/users
  ```

### Step 1 — Map

  ```text
  Authentication:
  /login

  Projects:
  /projects
  /projects/{id}
  /api/projects/{id}

  Reports:
  /reports/export

  Administration:
  /admin/users
  ```

### Step 2 — Identify Boundaries

  ```text
  Guest → User

  User → Admin

  User A Project → User B Project
  ```

### Step 3 — Prioritize

  ```text
  1. Project authorization
  2. Admin role enforcement
  3. Report data access
  ```

### Step 4 — Hypothesize

  ```text
  Project data should be accessible only to authorized project members.
  ```

### Step 5 — Baseline

- Capture a legitimate project request using an authorized test account.

### Step 6 — Controlled Test

- Modify one relevant authorization variable using only permitted accounts and objects.

### Step 7 — Validate

- Compare expected and actual behavior.

### Step 8 — Record

- Update coverage and preserve evidence if a finding is confirmed.

---

## Daily Assessment Loop

```text
Start Session
    │
    ▼
Review Scope
    │
    ▼
Review Previous Notes
    │
    ▼
Continue Mapping
    │
    ▼
Review New Target Entries
    │
    ▼
Update Coverage
    │
    ▼
Prioritize Hypotheses
    │
    ▼
Perform Controlled Testing
    │
    ▼
Record Results
    │
    ▼
Plan Follow-Up
```

---

## Target-to-Repeater Workflow

- A common professional pattern is:

  ```text
  Target
  → Understand structure

  Proxy
  → Locate exact traffic

  Repeater
  → Test manually

  Comparer
  → Compare selected responses if useful

  Intruder
  → Use controlled automation only when authorized and appropriate
  ```

- The testing objective determines the tool.

---

## Investigation Checklist

- Before deep testing, confirm:

  * Scope is understood.
  * Relevant hosts are classified.
  * Major workflows have been browsed.
  * Site Map has been reviewed.
  * Roles are mapped.
  * Objects are mapped.
  * API families are identified.
  * State-changing requests are marked.
  * Sensitive data flows are understood.
  * High-value hypotheses are documented.

---

## Common Workflow Mistakes

- Beginning exploitation before understanding the application.

- Failing to configure scope.

- Treating Site Map as complete.

- Testing endpoints randomly.

- Ignoring role differences.

- Ignoring object ownership.

- Failing to preserve baselines.

- Changing multiple variables at once.

- Failing to update coverage.

- Reporting hypotheses as findings.

- Forgetting to revisit Target after discovering new functionality.

---

## Professional Tips

- Start every assessment with scope verification.

- Browse naturally before aggressively testing.

- Organize by business function.

- Maintain separate role and object maps.

- Keep a coverage matrix.

- Prioritize high-impact boundaries.

- Convert observations into explicit hypotheses.

- Preserve baseline requests.

- Test one assumption at a time.

- Return to Target throughout the engagement.

- Keep evidence reproducible.

---

## Professional Workflow Template

```text
Assessment:

Authorization Source:

In-Scope Hosts:

Excluded Assets:

Restrictions:

Roles:

Major Functional Areas:

Objects:

API Families:

Sensitive Operations:

Trust Boundaries:

Business Workflows:

Coverage Gaps:

High-Priority Hypotheses:

Requests Selected for Repeater:

Validated Findings:

Follow-Up:
```

---

## Practice Exercise

- Use an explicitly authorized training application.

1. Confirm scope.
2. Configure Burp scope.
3. Browse all major public functionality.
4. Authenticate with an authorized account.
5. Exercise major workflows.
6. Review the Site Map.
7. Classify hosts.
8. Group resources by function.
9. Map roles and objects.
10. Identify API families.
11. Mark state-changing operations.
12. Build a coverage matrix.
13. Select five high-priority hypotheses.
14. Capture baseline requests.
15. Send selected requests to Repeater.
16. Perform controlled tests.
17. Record results.
18. Revisit Target and update coverage.

---

## Interview Questions

1. What is the purpose of a professional Target workflow?
2. Why should authorization be reviewed before configuring Burp scope?
3. Why should baseline browsing happen before aggressive testing?
4. How should observed hosts be classified?
5. Why should application resources be grouped by function?
6. Why are user roles and object ownership important?
7. What is a coverage matrix?
8. How should testing priorities be determined?
9. What makes a good security hypothesis?
10. Why should baseline requests be preserved?
11. Why should one meaningful variable be changed at a time?
12. How does Target work with Repeater?
13. Why should Target be revisited throughout an assessment?
14. What is required before an observation becomes a validated finding?
15. How would you structure an end-to-end Target workflow during a professional penetration test?

---

## Quick Revision

```text
Authorize
   │
   ▼
Scope
   │
   ▼
Browse
   │
   ▼
Map
   │
   ▼
Organize
   │
   ▼
Prioritize
   │
   ▼
Hypothesize
   │
   ▼
Baseline
   │
   ▼
Test
   │
   ▼
Validate
   │
   ▼
Document
   │
   ▼
Update Coverage
```

- Remember:

  ```text
  Scope Before Testing

  Understand Before Modifying

  Map Before Prioritizing

  Hypothesis Before Test

  Baseline Before Comparison

  Evidence Before Finding
  ```

---

## Key Takeaways

- A professional Target workflow connects scope, mapping, organization, prioritization, testing, validation, and evidence.

- Site Map development should be driven by meaningful application workflows.

- Hosts, roles, objects, APIs, state changes, sensitive data, and trust boundaries should all be mapped.

- Coverage should be tracked continuously rather than assumed.

- High-value observations should become explicit security hypotheses.

- Target serves as the organizational foundation from which selected requests move into tools such as Repeater for deeper controlled testing.

- The strongest workflow is systematic, scope-aware, iterative, hypothesis-driven, and evidence-based.
