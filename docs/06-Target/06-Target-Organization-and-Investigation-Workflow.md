# Target Organization and Investigation Workflow

## Overview

- Burp Suite's Target tool becomes most useful when discovered application content is turned into an organized investigation process.

- A professional tester does not simply collect requests and endpoints.

- The tester continuously organizes the application by:

  * Scope.
  * Hosts.
  * Functional areas.
  * User roles.
  * Authentication states.
  * Objects and identifiers.
  * APIs.
  * Sensitive operations.
  * Business workflows.
  * Testing status.

- Good organization reduces duplicate work, prevents coverage gaps, and helps convert reconnaissance into systematic manual testing.

---

## Learning Objectives

- By the end of this lesson, you should be able to:

  * Organize Target information into a practical assessment model.
  * Group application resources by function instead of reviewing isolated URLs.
  * Track testing coverage across hosts, roles, and workflows.
  * Prioritize high-value investigation areas.
  * Build hypotheses from Site Map and endpoint observations.
  * Move selected requests into appropriate Burp tools.
  * Maintain a repeatable investigation workflow.
  * Distinguish discovery, analysis, testing, validation, and reporting stages.

---

## Why Target Organization Matters

- Large applications quickly generate significant amounts of traffic.

- Without organization, the tester may face:

  ```text
  Hundreds of Requests
          │
          ├── Repeated Static Assets
          ├── API Calls
          ├── Authentication Traffic
          ├── Third-Party Requests
          ├── Background Requests
          └── Business Workflows
  ```

- Reviewing this as one undifferentiated stream is inefficient.

- Target organization converts traffic into a usable application model.

---

## From Traffic to Investigation

- A useful progression is:

  ```text
  Raw Traffic
      │
      ▼
  Scope Filtering
      │
      ▼
  Site Map
      │
      ▼
  Functional Classification
      │
      ▼
  Attack-Surface Inventory
      │
      ▼
  Prioritized Hypotheses
      │
      ▼
  Manual Testing
      │
      ▼
  Validated Findings
  ```

---

## Organize by Host

- Begin with authorized hosts.

  ```text
  app.example.com

  api.example.com

  files.example.com
  ```

- For each host, record:

  * Purpose.
  * Scope status.
  * Authentication requirements.
  * Technologies or interfaces observed.
  * Relationship to other hosts.

- Do not assume that every observed host is authorized.

---

## Organize by Functional Area

- Within each host, group functionality.

  ```text
  Authentication
  ├── Login
  ├── Registration
  └── Password Reset

  Account
  ├── Profile
  ├── Security
  └── Sessions

  Orders
  ├── Create
  ├── View
  ├── Cancel
  └── Export

  Administration
  ├── Users
  ├── Roles
  └── Settings
  ```

- Functional grouping is more useful than memorizing paths individually.

---

## Organize by User State

- Applications may expose different surfaces depending on authentication state.

  ```text
  Unauthenticated
  │
  ├── Public Pages
  ├── Login
  └── Registration

  Authenticated
  │
  ├── Dashboard
  ├── Account
  ├── Orders
  └── API

  Privileged
  │
  ├── Administration
  ├── Reporting
  └── User Management
  ```

- Track which functionality becomes available at each state.

---

## Organize by Role

- If multiple authorized roles exist, maintain separate coverage.

  ```text
  Standard User

  Manager

  Administrator
  ```

- Record:

  * Visible pages.
  * API calls.
  * Available actions.
  * Accessible object types.
  * Privileged operations.

- Role comparison is essential for authorization testing.

---

## Organize by Object Type

- Identify major application objects.

  ```text
  Users
  Orders
  Projects
  Documents
  Accounts
  Teams
  Reports
  ```

- For each object type, record:

  * Identifier format.
  * Ownership model.
  * Create operation.
  * Read operation.
  * Update operation.
  * Delete operation.
  * Role restrictions.

---

## Object Investigation Matrix

```text
Object: Project

Create:
POST /api/projects

Read:
GET /api/projects/{id}

Update:
PATCH /api/projects/{id}

Delete:
DELETE /api/projects/{id}

Membership:
GET /api/projects/{id}/members

Authorization:
Owner / Member / Admin
```

- This provides a clearer investigation model than reviewing each request independently.

---

## Organize by Workflow

- Business workflows should be tracked as sequences.

  ```text
  Registration
      │
      ▼
  Verification
      │
      ▼
  Profile Setup
      │
      ▼
  Account Activation
  ```

- Or:

  ```text
  Product
      │
      ▼
  Cart
      │
      ▼
  Discount
      │
      ▼
  Checkout
      │
      ▼
  Payment
      │
      ▼
  Order
  ```

- Record each request involved in the workflow.

---

## Build a Coverage Matrix

- A simple coverage matrix helps prevent missed areas.

  ```text
  Area                  Guest   User   Admin   Reviewed
  -----------------------------------------------------
  Login                 Yes     N/A    N/A     Yes
  Profile               No      Yes    Yes     Yes
  Orders                No      Yes    Yes     Partial
  User Management       No      No     Yes     No
  Reports               No      No     Yes     No
  ```

- Adapt the matrix to the application.

---

## Testing Status Labels

- Use simple status labels.

  ```text
  Not Reviewed

  Mapped

  Baseline Captured

  Testing

  Needs Follow-Up

  Validated

  No Issue Observed
  ```

- Avoid marking an area as "secure."

- Testing can establish what was observed under specific conditions, not prove absolute security.

---

## Investigation Notes

- For each important area, record:

  ```text
  Function:

  Relevant Requests:

  Authentication:

  Role:

  Objects:

  Inputs:

  State Changes:

  Sensitive Data:

  Hypotheses:

  Testing Status:

  Evidence:
  ```

- This keeps investigation context attached to the functionality.

---

## Prioritization

- Not every area deserves equal immediate attention.

- Prioritize based on:

  * Security impact.
  * Privilege boundaries.
  * Sensitive data.
  * State-changing behavior.
  * Object ownership.
  * Business criticality.
  * User-controlled input.
  * Exposure to unauthenticated users.

---

## Example Priority Tiers

```text
Priority 1
├── Authentication
├── Authorization
├── Account Recovery
├── Administrative Functions
└── Sensitive State Changes

Priority 2
├── APIs
├── File Handling
├── Business Workflows
├── Exports
└── Integrations

Priority 3
├── Low-Risk Informational Functions
└── Static Resources
```

- Priorities should be adapted to the actual application.

---

## Build Hypotheses Before Testing

- Good investigation is hypothesis-driven.

- Example observation:

  ```text
  GET /api/projects/104
  ```

- Context:

  ```text
  Project IDs are numeric.

  Users belong to separate projects.
  ```

- Hypothesis:

  ```text
  The server should verify project membership before returning
  data for /api/projects/{id}.
  ```

- Then design a controlled test using authorized accounts and objects.

---

## Hypothesis Queue

- Maintain a queue:

  ```text
  H-01
  Area: Projects
  Hypothesis: Object access should enforce membership
  Priority: High
  Status: Not Tested

  H-02
  Area: Admin
  Hypothesis: User-management actions require admin role
  Priority: High
  Status: Baseline Captured

  H-03
  Area: Export
  Hypothesis: Exported reports should respect tenant boundaries
  Priority: High
  Status: Needs Follow-Up
  ```

- This creates a repeatable testing backlog.

---

## Selecting Requests for Repeater

- Target helps identify interesting requests.

- Repeater is commonly used for controlled manual testing.

  ```text
  Site Map
      │
      ▼
  Identify Interesting Request
      │
      ▼
  Send to Repeater
      │
      ▼
  Capture Baseline
      │
      ▼
  Modify One Variable
      │
      ▼
  Compare Response
  ```

- Preserve the original request whenever possible.

---

## When to Use Other Burp Tools

- Different tasks may require different tools.

  ```text
  Target
  → Map and organize

  Proxy
  → Observe traffic

  Repeater
  → Controlled manual testing

  Intruder
  → Permitted automated request variation

  Sequencer
  → Token randomness analysis

  Decoder
  → Transform and inspect encoded data

  Comparer
  → Compare responses or values
  ```

- Tool selection should follow the testing objective.

---

## Baseline Management

- Capture normal behavior before modifications.

- Record:

  * Request.
  * User role.
  * Authentication state.
  * Object ownership.
  * Response status.
  * Response body.
  * Relevant headers.
  * Application effect.

- Without a baseline, response differences are harder to interpret.

---

## One Variable at a Time

- A controlled investigation usually changes one meaningful factor at a time.

  ```text
  Baseline

  ↓

  Change Object ID

  ↓

  Compare

  ↓

  Restore Baseline

  ↓

  Change Role / Session

  ↓

  Compare
  ```

- This improves causal reasoning.

---

## Investigation Across Roles

- Example:

  ```text
  Request:
  GET /api/reports/42

  User Session
      │
      ▼
  Baseline Response

  Manager Session
      │
      ▼
  Compare

  Admin Session
      │
      ▼
  Compare
  ```

- Only use accounts and roles authorized by the engagement.

---

## Investigation Across Objects

- Example:

  ```text
  User A owns Project 101

  User B owns Project 202
  ```

- A controlled authorization test may compare expected access boundaries using these known objects.

- Keep object ownership documented to avoid ambiguous results.

---

## Investigation Across Application States

- Some behavior depends on state.

  ```text
  Before Verification

  After Verification

  Before Payment

  After Payment

  Active Account

  Disabled Account
  ```

- Record the state associated with each request.

---

## Avoid Duplicate Testing

- Duplicate work often occurs when:

  * The same API backs multiple UI pages.
  * Multiple routes perform the same operation.
  * Different testers investigate the same object family.
  * Requests are renamed or reorganized without notes.

- Use endpoint families and coverage records to reduce duplication.

---

## Track Coverage Gaps

- Periodically ask:

  ```text
  Which roles have not been reviewed?

  Which workflows are incomplete?

  Which API families lack coverage?

  Which object types have not been tested?

  Which state-changing operations remain?

  Which discovered hosts need scope verification?
  ```

- Coverage review should happen throughout the assessment.

---

## New Discovery Workflow

```text
New Resource Observed
        │
        ▼
Verify Scope
        │
        ▼
Classify Function
        │
        ▼
Identify Auth / Role
        │
        ▼
Identify Object / Inputs
        │
        ▼
Add to Coverage Map
        │
        ▼
Prioritize
        │
        ▼
Create Hypothesis if Relevant
```

---

## Investigation Evidence

- Evidence should support reproducibility.

- Useful evidence may include:

  * Original request.
  * Modified request.
  * Relevant responses.
  * Account or role context.
  * Object ownership context.
  * Exact reproduction steps.
  * Observed impact.

- Avoid collecting unnecessary sensitive data.

---

## Observation vs Finding

- Example:

  ```text
  Observation:
  /admin/users appears in Site Map.

  Hypothesis:
  The endpoint should require an administrator role.

  Test:
  Controlled request using an authorized lower-privileged account.

  Finding:
  Only if evidence demonstrates unauthorized access or action.
  ```

- This separation improves reporting quality.

---

## Investigation Workflow Example

- Suppose the application contains:

  ```text
  /login
  /dashboard
  /projects
  /projects/{id}
  /api/projects/{id}
  /reports/export
  /admin/users
  ```

- Organize:

  ```text
  Authentication
  └── /login

  Projects
  ├── /projects
  ├── /projects/{id}
  └── /api/projects/{id}

  Reporting
  └── /reports/export

  Administration
  └── /admin/users
  ```

- Prioritize:

  ```text
  1. Project object authorization
  2. Administrative role enforcement
  3. Report data boundaries
  4. Authentication behavior
  ```

- Then create individual hypotheses and test systematically.

---

## Professional Investigation Workflow

```text
Confirm Scope
      │
      ▼
Map Application
      │
      ▼
Organize by Function
      │
      ▼
Map Roles and States
      │
      ▼
Map Objects and APIs
      │
      ▼
Build Coverage Matrix
      │
      ▼
Prioritize
      │
      ▼
Create Hypothesis Queue
      │
      ▼
Capture Baselines
      │
      ▼
Perform Controlled Tests
      │
      ▼
Validate Results
      │
      ▼
Record Evidence
      │
      ▼
Update Coverage
```

---

## Common Mistakes

- Testing directly from a large unorganized HTTP history.

- Treating every endpoint as an independent feature.

- Failing to track which roles have been tested.

- Losing track of object ownership.

- Modifying multiple variables simultaneously.

- Repeating the same tests unnecessarily.

- Failing to capture baseline behavior.

- Confusing discovered resources with vulnerabilities.

- Ignoring unfinished workflows.

- Failing to update the investigation map after new discoveries.

---

## Professional Tips

- Organize by business function first and URL second.

- Keep separate notes for roles, objects, and workflows.

- Maintain a coverage matrix.

- Use a hypothesis queue for high-value tests.

- Preserve baseline requests.

- Change one meaningful variable at a time.

- Record object ownership before authorization testing.

- Revisit Target after every major workflow.

- Update coverage continuously.

- Keep findings evidence-driven and reproducible.

---

## Investigation Template

```text
Area:

Host:

Scope Verified:
Yes / No

Function:

Relevant Endpoints:

User State:

Roles:

Objects:

Inputs:

Sensitive Data:

State Changes:

Baseline Captured:
Yes / No

Hypotheses:

Testing Status:

Evidence:

Follow-Up:
```

---

## Practice Exercise

- Use an explicitly authorized training application.

1. Browse all major functionality.
2. Review the Target Site Map.
3. Group resources into functional areas.
4. Identify available roles and user states.
5. Identify major object types.
6. Build a coverage matrix.
7. Select five high-priority areas.
8. Create one hypothesis for each area.
9. Capture baseline requests.
10. Send selected requests to Repeater.
11. Change only one relevant variable per test.
12. Record results and update coverage status.

---

## Interview Questions

1. Why is Target organization important during a large assessment?
2. Why should resources be grouped by function?
3. What is a coverage matrix?
4. Why should user roles be tracked separately?
5. How should application objects be organized?
6. What is a hypothesis queue?
7. Why should baseline requests be preserved?
8. Why is changing one variable at a time useful?
9. How does Target interact with Repeater?
10. How can duplicate testing be reduced?
11. What are coverage gaps?
12. How should new discoveries be integrated into an investigation?
13. What is the difference between an observation and a validated finding?
14. What evidence should be preserved for reproducibility?
15. How would you organize testing for a large multi-role application?

---

## Quick Revision

```text
Target Data
    │
    ▼
Organize
    │
    ├── Hosts
    ├── Functions
    ├── Roles
    ├── States
    ├── Objects
    ├── APIs
    └── Workflows
    │
    ▼
Track Coverage
    │
    ▼
Prioritize
    │
    ▼
Create Hypotheses
    │
    ▼
Capture Baselines
    │
    ▼
Test Systematically
    │
    ▼
Validate and Document
```

- Remember:

  ```text
  Traffic ≠ Investigation

  Discovery ≠ Testing

  Hypothesis ≠ Finding

  Organized Coverage → Better Testing
  ```

---

## Key Takeaways

- Target organization converts raw application traffic into a structured investigation model.

- Resources should be grouped by hosts, functionality, roles, objects, states, APIs, and workflows.

- Coverage tracking reduces gaps and duplicate work.

- High-value observations should become explicit, prioritized hypotheses.

- Baseline requests and controlled one-variable changes improve manual testing quality.

- Target acts as the organizational bridge between application mapping and deeper testing with tools such as Repeater.

- A professional investigation remains scope-aware, systematic, evidence-driven, and continuously updated.
