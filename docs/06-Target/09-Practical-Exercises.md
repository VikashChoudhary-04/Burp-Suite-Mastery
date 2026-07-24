# Practical Exercises

## Overview

- These exercises are designed to turn the Target tool from a navigation feature into a practical application-mapping and investigation workspace.

- Use only:

  * Applications you own.
  * Deliberately vulnerable training applications.
  * Lab environments.
  * Targets for which you have explicit authorization.

- The exercises progress from basic Site Map observation to structured attack-surface mapping, endpoint analysis, role comparison, coverage tracking, and hypothesis-driven investigation.

---

## Learning Objectives

- By completing these exercises, you should be able to:

  * Configure and verify target scope.
  * Populate and navigate the Site Map.
  * Distinguish in-scope, out-of-scope, and unknown hosts.
  * Map application functionality and attack surface.
  * Build an endpoint inventory.
  * Identify endpoint families and object relationships.
  * Compare authenticated and unauthenticated functionality.
  * Compare authorized user roles.
  * Track business workflows and application state.
  * Build a coverage matrix.
  * Prioritize security-relevant areas.
  * Create testable security hypotheses.
  * Move selected requests from Target to Repeater.
  * Troubleshoot incomplete Target coverage.

---

## Lab Preparation

- Before beginning, confirm:

  ```text
  [ ] Target is explicitly authorized

  [ ] Burp Suite is running

  [ ] Browser traffic passes through Burp

  [ ] HTTPS interception works where required

  [ ] Proxy history records requests

  [ ] Test accounts are available if needed

  [ ] Scope restrictions are understood

  [ ] Notes are ready
  ```

- Record the lab target before testing.

  ```text
  Target:

  Authorization:

  In-Scope Hosts:

  Exclusions:

  Available Roles:

  Restrictions:
  ```

---

## Exercise 1 — Observe Target Population

### Objective

- Understand how normal browsing populates the Target Site Map.

### Steps

1. Start with a clean authorized lab session.

2. Open the Target tool.

3. Record what appears before browsing.

4. Visit the application's home page.

5. Browse several public pages.

6. Return to Target.

7. Identify newly observed:

   * Hosts.
   * Directories.
   * Pages.
   * JavaScript files.
   * API requests.
   * Static resources.

### Expected Result

- You should be able to explain how observed traffic contributes to the Site Map.

### Record

```text
Initial Site Map:

Pages Visited:

New Hosts:

New Paths:

New APIs:

New JavaScript:

Observations:
```

---

## Exercise 2 — Configure Scope

### Objective

- Configure scope to match explicit authorization.

### Steps

1. Review the authorized target definition.

2. Identify:

   * Scheme.
   * Host.
   * Port.
   * Path restrictions.

3. Add only authorized assets to Burp scope.

4. Review the resulting scope configuration.

5. Enable a scope-based view or filter where appropriate.

6. Confirm that relevant target traffic remains visible.

7. Confirm that unrelated traffic is distinguishable.

### Validation Questions

- Does Burp scope exactly match authorization?

- Did you accidentally include a parent domain or wildcard?

- Are explicitly excluded paths still excluded?

### Record

```text
Authorized Scope:

Burp Scope:

Differences:

Corrections Made:
```

---

## Exercise 3 — Classify Observed Hosts

### Objective

- Learn that observed traffic and authorized scope are different concepts.

### Steps

1. Browse the application normally.

2. List every host observed in Target or Proxy history.

3. Classify each host:

   ```text
   Confirmed In Scope

   Confirmed Out of Scope

   Unknown — Requires Verification
   ```

4. Note the apparent purpose of each host.

### Example

```text
Host                         Classification       Purpose
----------------------------------------------------------------
app.example.test             In Scope             Main application
api.example.test             In Scope             API
cdn.vendor.test              Out of Scope         Static content
identity.vendor.test         Unknown              Authentication
```

### Rule

- Do not actively test unknown hosts.

---

## Exercise 4 — Build a Functional Map

### Objective

- Organize the application by business function rather than raw URLs.

### Steps

1. Browse all major navigation areas.

2. Review the Site Map.

3. Group resources into categories such as:

   * Authentication.
   * Account.
   * Search.
   * Products.
   * Orders.
   * Files.
   * Administration.
   * APIs.

4. Create a functional tree.

### Example

```text
Application
│
├── Authentication
│   ├── Login
│   └── Password Reset
│
├── Account
│   ├── Profile
│   └── Security
│
└── Orders
    ├── List
    ├── View
    └── Cancel
```

### Deliverable

- A functional map of the application.

---

## Exercise 5 — Build an Endpoint Inventory

### Objective

- Record endpoint context rather than collecting URLs alone.

### Steps

1. Select at least ten meaningful requests.

2. Record:

   * Method.
   * Path.
   * Function.
   * Authentication requirement.
   * Role.
   * Inputs.
   * Response type.
   * Whether the operation changes state.

### Template

```text
Method | Path | Function | Auth | Role | Inputs | State Change
```

### Example

```text
GET | /api/profile | View profile | Yes | User | None | No
PATCH | /api/profile | Update profile | Yes | User | JSON | Yes
```

### Deliverable

- An inventory containing at least ten endpoints.

---

## Exercise 6 — Identify Endpoint Families

### Objective

- Group related operations around application objects.

### Steps

1. Choose one important object type.

2. Search Target and Proxy history for related requests.

3. Map available operations.

### Example

```text
Resource: Project

GET    /api/projects
POST   /api/projects
GET    /api/projects/{id}
PATCH  /api/projects/{id}
DELETE /api/projects/{id}
```

4. Identify missing or conditional operations.

5. Record which roles can perform each observed operation.

### Deliverable

- One complete endpoint-family map.

---

## Exercise 7 — Map Object Relationships

### Objective

- Understand ownership and parent-child relationships.

### Steps

1. Identify object identifiers in paths, parameters, or bodies.

2. Map relationships.

### Example

```text
Organization
    │
    ├── Project
    │      │
    │      └── Document
    │
    └── Members
```

3. Record:

   * Identifier format.
   * Owner.
   * Parent object.
   * Expected access boundary.

### Deliverable

```text
Object:

Identifier:

Owner:

Parent:

Allowed Roles:

Related Endpoints:
```

---

## Exercise 8 — Compare Unauthenticated and Authenticated Surfaces

### Objective

- Understand how authentication changes the attack surface.

### Steps

1. Browse the application while logged out.

2. Record visible functionality and requests.

3. Authenticate using an authorized account.

4. Browse all newly available functionality.

5. Compare both states.

### Comparison Table

```text
Function              Guest     Authenticated
----------------------------------------------
Home                  Yes       Yes
Login                 Yes       N/A
Profile               No        Yes
Orders                No        Yes
Account Settings      No        Yes
```

### Deliverable

- A documented authentication-state comparison.

---

## Exercise 9 — Compare Authorized Roles

### Objective

- Map privilege boundaries between roles.

### Requirements

- Use only roles explicitly provided or permitted for the lab.

### Steps

1. Browse the application as Role A.

2. Record:

   * Pages.
   * APIs.
   * Actions.
   * Objects.

3. Repeat as Role B.

4. Compare the two surfaces.

### Example

```text
Function              User      Admin
--------------------------------------
Profile               Yes       Yes
Orders                Yes       Yes
User Management       No        Yes
Role Management       No        Yes
System Settings       No        Yes
```

### Deliverable

- A role capability matrix.

---

## Exercise 10 — Identify State-Changing Operations

### Objective

- Locate requests that modify application state.

### Steps

1. Review Target and Proxy history.

2. Identify operations such as:

   * Create.
   * Update.
   * Delete.
   * Upload.
   * Approve.
   * Cancel.
   * Change password.
   * Change role.

3. Record:

   * Method.
   * Endpoint.
   * Required role.
   * Inputs.
   * Expected effect.

### Deliverable

```text
Operation:

Method:

Endpoint:

Role:

Object:

Expected State Change:
```

---

## Exercise 11 — Map a Business Workflow

### Objective

- Understand how multiple requests form one application process.

### Steps

1. Choose a workflow such as:

   * Registration.
   * Password reset.
   * Checkout.
   * File sharing.
   * Project creation.

2. Perform the workflow normally.

3. Record each important request in order.

### Example

```text
Create Order
    │
    ▼
Apply Discount
    │
    ▼
Select Payment
    │
    ▼
Confirm Order
    │
    ▼
View Receipt
```

4. Record state transitions.

### Deliverable

- A request-level workflow map.

---

## Exercise 12 — Analyze API Traffic

### Objective

- Identify the backend API surface used by the application.

### Steps

1. Exercise dynamic application functionality.

2. Review Proxy history for:

   * XHR.
   * Fetch.
   * JSON.
   * GraphQL.
   * API hosts.

3. Identify at least five API operations.

4. Record:

   * Method.
   * Route.
   * Authentication.
   * Inputs.
   * Object.
   * Response data.

### Deliverable

- A five-endpoint API inventory.

---

## Exercise 13 — Review JavaScript-Derived Leads

### Objective

- Use JavaScript references as discovery leads without treating them as confirmed findings.

### Steps

1. Identify JavaScript files associated with the authorized application.

2. Review relevant application-owned scripts.

3. Record interesting route references.

4. For each reference:

   * Verify scope.
   * Determine whether the route is observed elsewhere.
   * Determine likely method and purpose.
   * Mark it as confirmed or unconfirmed.

### Template

```text
Reference:

Source:

Scope Verified:

Observed in Traffic:
Yes / No

Likely Function:

Status:
Confirmed / Unconfirmed
```

---

## Exercise 14 — Build a Coverage Matrix

### Objective

- Track what has and has not been investigated.

### Steps

1. List major functional areas.

2. List available user states or roles.

3. Mark coverage.

### Example

```text
Area                 Guest   User   Admin   Status
---------------------------------------------------
Authentication       Yes     N/A    N/A     Reviewed
Profile              No      Yes    Yes     Reviewed
Orders               No      Yes    Yes     Partial
Reports              No      No     Yes     Not Tested
Administration       No      No     Yes     Not Tested
```

4. Identify at least three coverage gaps.

### Deliverable

- A coverage matrix plus a follow-up list.

---

## Exercise 15 — Prioritize the Attack Surface

### Objective

- Rank application areas using security relevance.

### Steps

1. Review your functional map.

2. Assign priority based on:

   * Sensitive data.
   * Privilege boundaries.
   * State changes.
   * Object ownership.
   * Business criticality.
   * Authentication exposure.

3. Create three tiers.

### Example

```text
Priority 1
- Account recovery
- Authorization
- Administration

Priority 2
- File handling
- APIs
- Business workflows

Priority 3
- Static informational pages
```

### Deliverable

- A prioritized testing queue.

---

## Exercise 16 — Create Security Hypotheses

### Objective

- Convert mapping observations into testable expectations.

### Steps

1. Choose five high-priority observations.

2. For each, write:

   * Observation.
   * Expected security control.
   * Testable hypothesis.

### Example

```text
Observation:
GET /api/projects/{id}

Expected Control:
Only project members should access project data.

Hypothesis:
The server should verify project membership for every project request.
```

### Deliverable

- Five clearly written hypotheses.

---

## Exercise 17 — Capture a Baseline and Send to Repeater

### Objective

- Connect Target mapping with controlled manual testing.

### Steps

1. Select one high-priority request in Target.

2. Record:

   * User role.
   * Authentication state.
   * Object ownership.
   * Application state.

3. Send the request to Repeater.

4. Send the unmodified request.

5. Record the baseline:

   * Status.
   * Response length.
   * Relevant body content.
   * Application behavior.

6. Change one permitted variable.

7. Compare the result.

### Rule

- Do not change several variables simultaneously.

---

## Exercise 18 — Troubleshoot a Missing Endpoint

### Objective

- Diagnose incomplete Site Map coverage systematically.

### Steps

1. Choose a known application feature.

2. Check whether its request appears in Target.

3. If missing:

   ```text
   Check Proxy History
       │
       ├── Present
       │     └── Check filters / Site Map view
       │
       └── Missing
             └── Check browser, proxy, role, state, and workflow
   ```

4. Exercise the feature again.

5. Confirm where the request appears.

### Deliverable

```text
Missing Feature:

Root Cause:

Troubleshooting Steps:

Resolution:
```

---

## Exercise 19 — Identify Third-Party Dependencies Safely

### Objective

- Separate application dependencies from authorized testing targets.

### Steps

1. Review all hosts observed while using the application.

2. Identify likely:

   * CDNs.
   * Analytics.
   * Authentication providers.
   * Payment services.
   * Support widgets.

3. Mark authorization status.

4. Do not actively test third-party systems unless explicitly authorized.

### Deliverable

- A host-classification table.

---

## Exercise 20 — Perform a Full Target Investigation

### Objective

- Combine all Module 06 skills into one structured workflow.

### Steps

1. Confirm authorization.

2. Configure scope.

3. Browse public functionality.

4. Authenticate.

5. Browse authenticated functionality.

6. Exercise major workflows.

7. Review Target.

8. Classify hosts.

9. Build a functional map.

10. Build an endpoint inventory.

11. Map API families.

12. Map objects.

13. Map roles.

14. Map state-changing operations.

15. Map sensitive data flows.

16. Build a coverage matrix.

17. Identify coverage gaps.

18. Prioritize high-value areas.

19. Create at least five hypotheses.

20. Select requests for Repeater.

21. Capture baselines.

22. Perform controlled tests.

23. Record observations and evidence.

24. Revisit Target.

25. Update coverage.

---

## Final Deliverables

- At the end of the practical exercises, you should have:

  ```text
  1. Scope Record

  2. Host Classification

  3. Functional Application Map

  4. Endpoint Inventory

  5. Endpoint Family Map

  6. Object Relationship Map

  7. Authentication-State Comparison

  8. Role Capability Matrix

  9. State-Changing Operation List

  10. Business Workflow Map

  11. API Inventory

  12. Coverage Matrix

  13. Prioritized Testing Queue

  14. Security Hypothesis List

  15. Baseline Testing Notes

  16. Troubleshooting Record
  ```

---

## Self-Assessment Checklist

- Confirm that you can perform each task without relying on step-by-step instructions.

  ```text
  [ ] Define and verify Burp scope

  [ ] Explain the difference between scope and observed traffic

  [ ] Navigate the Site Map

  [ ] Classify hosts safely

  [ ] Build a functional application map

  [ ] Create an endpoint inventory

  [ ] Group endpoint families

  [ ] Map object ownership

  [ ] Compare authentication states

  [ ] Compare authorized roles

  [ ] Map business workflows

  [ ] Identify API traffic

  [ ] Track coverage

  [ ] Prioritize attack surface

  [ ] Create testable hypotheses

  [ ] Send requests from Target to Repeater

  [ ] Capture baselines

  [ ] Troubleshoot missing Target content
  ```

---

## Practice Questions

1. Why is normal browsing useful before active investigation?
2. Why must every observed host be classified before testing?
3. What is the difference between a URL list and an endpoint inventory?
4. Why should endpoints be grouped into families?
5. Why is object ownership important?
6. How does authentication state change the attack surface?
7. Why should roles be mapped separately?
8. What makes a business workflow security relevant?
9. Why should state-changing operations receive special attention?
10. What is the purpose of a coverage matrix?
11. How should testing priorities be determined?
12. What is a security hypothesis?
13. Why should a baseline be captured before modification?
14. Why should one variable be changed at a time?
15. How would you troubleshoot a known endpoint that does not appear in Target?

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
Classify
    │
    ▼
Inventory
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
Update Coverage
```

- Remember:

  ```text
  Observed Host ≠ Authorized Host

  Endpoint Found ≠ Vulnerability

  Site Map ≠ Complete Application

  Hypothesis ≠ Finding

  Mapping + Context + Coverage = Better Testing
  ```

---

## Key Takeaways

- Practical mastery of Target requires more than browsing the Site Map.

- Scope, host classification, functional mapping, endpoint analysis, object relationships, roles, workflows, and coverage should be documented systematically.

- The Site Map should evolve as new application states, roles, and workflows are exercised.

- High-value observations should be converted into explicit security hypotheses.

- Target provides the organizational foundation for deeper manual testing with tools such as Repeater.

- A professional Target workflow remains authorized, structured, repeatable, coverage-aware, and evidence-driven.
