# Interview Questions

## Overview

- This lesson provides interview-focused questions for Burp Suite's Target tool.

- The questions progress from foundational concepts to practical assessment scenarios.

- Strong answers should demonstrate that you understand:

  * What the Target tool does.
  * How the Site Map is populated.
  * How scope should be configured and interpreted.
  * How to map an application's attack surface.
  * How to analyze endpoints, roles, objects, and workflows.
  * How Target integrates with Proxy, Repeater, and other Burp tools.
  * How to troubleshoot incomplete or misleading Target data.
  * How to maintain authorization and testing discipline.

---

## 1. What is the purpose of the Target tool in Burp Suite?

- The Target tool provides a structured view of the application and its discovered resources.

- It helps testers:

  * Understand application structure.
  * Review the Site Map.
  * Define and manage scope.
  * Organize endpoints.
  * Map attack surface.
  * Prioritize areas for deeper testing.

---

## 2. What is the Site Map?

- The Site Map is Burp's hierarchical representation of application content learned from observed or discovered traffic.

- It may contain:

  * Hosts.
  * Directories.
  * Pages.
  * API routes.
  * Parameters.
  * JavaScript.
  * Static resources.

- It evolves as more of the application is exercised.

---

## 3. How does Burp populate the Site Map?

- Burp learns application structure from traffic and discovery activity.

- Common sources include:

  * Requests passing through Proxy.
  * Normal browsing.
  * API traffic.
  * Authorized discovery mechanisms.

- The Site Map should not automatically be treated as a complete inventory.

---

## 4. What is Burp scope?

- Scope is Burp's local definition of which targets are relevant to the assessment.

- It can help:

  * Filter traffic.
  * Reduce noise.
  * Focus testing.
  * Control scope-aware tool behavior.

- Burp scope must be derived from actual authorization.

---

## 5. Does adding a host to Burp scope authorize you to test it?

- No.

- Burp scope is a technical configuration.

- Authorization comes from:

  * Written permission.
  * Rules of engagement.
  * Bug bounty program rules.
  * Approved lab boundaries.

  ```text
  Burp Scope ≠ Authorization
  ```

---

## 6. Why should scope be configured early?

- Correct scope helps:

  * Reduce irrelevant traffic.
  * Focus investigation.
  * Avoid accidental interaction with unrelated systems.
  * Improve Site Map usability.
  * Support safer use of other Burp tools.

---

## 7. What is the difference between observed traffic and in-scope traffic?

- Observed traffic includes anything Burp sees.

- In-scope traffic is traffic associated with targets explicitly included in the configured assessment scope.

- An observed third-party host may not be authorized for testing.

---

## 8. Why might third-party hosts appear in Target?

- Modern applications commonly use:

  * CDNs.
  * Analytics.
  * Identity providers.
  * Payment processors.
  * Support widgets.
  * External APIs.

- Their presence in Target does not imply permission to test them.

---

## 9. How would you classify hosts during an assessment?

- A useful classification is:

  ```text
  Confirmed In Scope

  Confirmed Out of Scope

  Unknown — Requires Verification
  ```

- Unknown hosts should not be actively tested until authorization is confirmed.

---

## 10. What is attack-surface mapping?

- Attack-surface mapping is the structured process of identifying and organizing security-relevant application components.

- Examples include:

  * Authentication.
  * APIs.
  * User roles.
  * Objects.
  * File handling.
  * Administrative functions.
  * Business workflows.
  * Trust boundaries.

---

## 11. Why is a simple URL list not enough?

- URLs alone often lack security context.

- A useful endpoint inventory should also record:

  * Method.
  * Function.
  * Authentication.
  * Role.
  * Inputs.
  * Object identifiers.
  * State changes.
  * Data sensitivity.

---

## 12. What is an endpoint family?

- An endpoint family is a group of related operations around the same resource.

- Example:

  ```text
  GET    /api/projects
  POST   /api/projects
  GET    /api/projects/{id}
  PATCH  /api/projects/{id}
  DELETE /api/projects/{id}
  ```

- Mapping families helps reveal the complete lifecycle of an object.

---

## 13. Why are HTTP methods important during endpoint analysis?

- The same path can support different operations.

- Example:

  ```text
  GET /api/users/42
  PATCH /api/users/42
  DELETE /api/users/42
  ```

- Read, update, and delete operations may require different authorization controls.

---

## 14. What information should you record for an important endpoint?

- Record:

  * Host.
  * Method.
  * Path.
  * Function.
  * Authentication requirement.
  * Required role.
  * Inputs.
  * Object identifier.
  * State-changing behavior.
  * Sensitive data.
  * Normal response behavior.

---

## 15. What is object mapping?

- Object mapping identifies important application resources and their relationships.

- Examples:

  * Users.
  * Accounts.
  * Orders.
  * Projects.
  * Documents.
  * Teams.

- For each object, record ownership, identifiers, relationships, and allowed operations.

---

## 16. Why is object ownership important?

- Ownership provides context for authorization boundaries.

- Example:

  ```text
  Project 101 → User A
  Project 202 → User B
  ```

- Without known ownership, authorization testing can produce ambiguous conclusions.

---

## 17. What is CRUD mapping?

- CRUD represents:

  ```text
  Create
  Read
  Update
  Delete
  ```

- Mapping CRUD operations helps determine which roles should be able to perform each action on an object.

---

## 18. Why should authenticated and unauthenticated surfaces be mapped separately?

- Authentication changes available functionality.

- Comparing states helps identify:

  * Protected pages.
  * APIs.
  * Account functions.
  * Sensitive operations.
  * Authentication boundaries.

---

## 19. Why should different user roles be mapped separately?

- Different roles may have different:

  * Pages.
  * APIs.
  * Objects.
  * Actions.
  * Privileges.

- Role mapping supports systematic authorization testing.

---

## 20. What is a trust boundary?

- A trust boundary is a point where security assumptions or privilege levels change.

- Examples:

  ```text
  Guest → Authenticated User

  User → Administrator

  Tenant A → Tenant B

  Browser → Backend API
  ```

- Trust boundaries are high-value areas for security testing.

---

## 21. What is business-workflow mapping?

- Business-workflow mapping documents a sequence of related actions.

- Example:

  ```text
  Add Item
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
  Order Confirmation
  ```

- Understanding normal order and state transitions helps identify meaningful test cases.

---

## 22. Why should state-changing operations be prioritized?

- State-changing requests can modify important application data or behavior.

- Examples:

  * Create.
  * Update.
  * Delete.
  * Transfer.
  * Approve.
  * Upload.
  * Change role.

- Their potential impact often makes them higher priority.

---

## 23. How can JavaScript assist with endpoint discovery?

- Application JavaScript may reference:

  * API routes.
  * Feature endpoints.
  * Versioned paths.
  * Configuration resources.

- These references are discovery leads and require verification.

---

## 24. Does a route found in JavaScript prove that the endpoint exists?

- No.

- It may be:

  * Old.
  * Conditional.
  * Unused.
  * Environment-specific.
  * Role-specific.

- Verify scope, reachability, method, and application context.

---

## 25. What is the difference between content discovery and endpoint analysis?

- Content discovery answers:

  ```text
  What resources can be identified?
  ```

- Endpoint analysis answers:

  ```text
  What does this resource do?
  Who can access it?
  What inputs does it accept?
  What object does it affect?
  Why is it security relevant?
  ```

---

## 26. What is a coverage matrix?

- A coverage matrix tracks which application areas, roles, or states have been reviewed.

- Example:

  ```text
  Area              Guest   User   Admin   Status
  ------------------------------------------------
  Login             Yes     N/A    N/A     Reviewed
  Profile           No      Yes    Yes     Reviewed
  Reports           No      No     Yes     Not Tested
  ```

- It helps reduce testing gaps and duplicate effort.

---

## 27. Why should you avoid marking an area as "secure"?

- Testing is bounded by:

  * Time.
  * Scope.
  * Techniques.
  * Accounts.
  * Application state.

- A better status is:

  ```text
  No Issue Observed
  ```

- This describes the result without making an absolute claim.

---

## 28. What is a security hypothesis?

- A security hypothesis is a testable expectation about a control.

- Example:

  ```text
  The server should verify project membership before returning
  data for /api/projects/{id}.
  ```

- It converts an observation into a structured test objective.

---

## 29. What is the difference between an observation, hypothesis, and finding?

```text
Observation
→ Something interesting was discovered.

Hypothesis
→ A security control is expected to behave in a specific way.

Finding
→ Evidence demonstrates a reproducible security weakness and impact.
```

---

## 30. Why should you capture a baseline request?

- A baseline establishes normal behavior.

- Record:

  * Request.
  * Response.
  * Role.
  * Session.
  * Object ownership.
  * Application state.

- Modified requests can then be compared meaningfully.

---

## 31. Why should you change one variable at a time?

- If several variables change simultaneously, it becomes difficult to determine what caused the response difference.

- Controlled testing improves:

  * Causal reasoning.
  * Reproducibility.
  * Evidence quality.

---

## 32. How does Target work with Proxy?

- Proxy captures and records traffic.

- Target organizes discovered application structure.

  ```text
  Proxy
  → Observe requests

  Target
  → Organize and map application
  ```

---

## 33. How does Target work with Repeater?

- Target helps identify interesting requests.

- Repeater is used for controlled manual modification and response comparison.

  ```text
  Target
      │
      ▼
  Select Request
      │
      ▼
  Send to Repeater
      │
      ▼
  Test Hypothesis
  ```

---

## 34. When might you use Intruder after Target analysis?

- Intruder may be appropriate when controlled automated request variation is authorized and useful.

- Examples include testing multiple permitted input values.

- Automation must respect:

  * Scope.
  * Rate limits.
  * Program rules.
  * Production safety.

---

## 35. Why might the Site Map be incomplete?

- Possible reasons:

  * Features have not been visited.
  * Authentication is required.
  * Different roles expose different functionality.
  * Workflows are conditional.
  * API requests have not been triggered.
  * Traffic bypassed Burp.
  * Filters hide entries.

---

## 36. How would you troubleshoot an empty Site Map?

- Check:

  1. Is browser traffic reaching Burp?
  2. Does Proxy history contain requests?
  3. Is the application being browsed?
  4. Are filters hiding content?
  5. Is the correct Burp project being used?

---

## 37. How would you troubleshoot a missing endpoint?

```text
Perform Feature
    │
    ▼
Check Proxy History
    │
    ├── Present
    │     └── Check Target filters and view
    │
    └── Missing
          └── Check proxy, role, state, and workflow
```

---

## 38. Why might an API host be absent from Target?

- Possible causes:

  * The API has not been called.
  * Authentication is required.
  * The browser is not proxying the request.
  * A feature has not been exercised.
  * Filters hide the host.

- Authorization should be verified before separately probing it.

---

## 39. Why might too many hosts appear in Target?

- Causes may include:

  * CDNs.
  * Analytics.
  * Browser background traffic.
  * External authentication.
  * Broad scope rules.

- Use correct scope and filtering rather than assuming all hosts are targets.

---

## 40. Why does a `200 OK` not prove authorization succeeded?

- The response may contain:

  * An error.
  * Empty data.
  * A generic page.
  * Redacted information.
  * A login interface.

- Analyze the response body and actual application effect.

---

## 41. Why does a `404 Not Found` not always prove that a resource does not exist?

- Some applications intentionally return `404` for unauthorized or hidden resources.

- Status codes must be interpreted with application context.

---

## 42. What would you do if Target contains an unfamiliar subdomain?

- Do not immediately test it.

- First:

  1. Identify how it was discovered.
  2. Check authorization.
  3. Determine whether it is first-party or third-party.
  4. Classify it.
  5. Test only if explicitly authorized.

---

## 43. How would you approach a large application with thousands of Target entries?

- I would:

  1. Confirm scope.
  2. Filter irrelevant traffic.
  3. Group by host and function.
  4. Map roles and objects.
  5. Identify endpoint families.
  6. Build a coverage matrix.
  7. Prioritize high-impact areas.
  8. Create a hypothesis queue.
  9. Test systematically.

---

## 44. How would you prioritize endpoints?

- Consider:

  * Authentication.
  * Authorization.
  * Sensitive data.
  * State changes.
  * Administrative functions.
  * Object ownership.
  * Business criticality.
  * File handling.
  * Multi-tenant boundaries.

---

## 45. How would you investigate a numeric object identifier?

- First establish context:

  * What object does it identify?
  * Who owns the baseline object?
  * What role is being used?
  * What access is expected?

- Then create a controlled authorization hypothesis using only authorized test accounts and objects.

---

## 46. How would you investigate an administrative endpoint discovered in Target?

- I would:

  1. Verify scope.
  2. Determine its intended function.
  3. Establish expected role requirements.
  4. Capture a legitimate baseline if authorized.
  5. Create a role-enforcement hypothesis.
  6. Perform controlled testing with permitted accounts.
  7. Validate behavior before reporting.

---

## 47. How would you map a multi-tenant application?

- I would map:

  * Tenants.
  * Users.
  * Roles.
  * Objects.
  * Tenant identifiers.
  * Cross-tenant trust boundaries.

- I would clearly record which test objects belong to each authorized tenant.

---

## 48. How would you map a single-page application?

- I would focus on:

  * Proxy history.
  * XHR/fetch traffic.
  * JSON APIs.
  * JavaScript.
  * API hosts.
  * Background requests.

- Browser-visible paths alone may not represent the real backend attack surface.

---

## 49. What is a professional Target workflow?

```text
Confirm Authorization
      │
      ▼
Configure Scope
      │
      ▼
Browse Normally
      │
      ▼
Build Site Map
      │
      ▼
Map Functions, Roles, Objects, APIs
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
Send Selected Requests to Repeater
      │
      ▼
Validate and Document
```

---

## 50. What is the biggest mistake beginners make with the Target tool?

- A common mistake is treating Target as only a list of URLs.

- Its real value is using discovered application structure to build an organized model of:

  * Scope.
  * Functionality.
  * Roles.
  * Objects.
  * Workflows.
  * Security boundaries.
  * Testing coverage.

---

## Scenario 1 — Unknown Host Appears

### Question

- While testing `app.example.com`, Target shows `payments.vendor.example`. What do you do?

### Strong Answer

- I would not test it merely because Burp observed it.

- I would verify whether it is explicitly authorized, classify it as in scope, out of scope, or unknown, and avoid active testing unless permission is confirmed.

---

## Scenario 2 — Site Map Shows Only Public Pages

### Question

- You expected account and API endpoints, but only public pages appear. What do you check?

### Strong Answer

- I would verify that authentication was completed through the proxied browser, confirm the session is valid, exercise authenticated functionality, check Proxy history, review filters, and then revisit Target.

---

## Scenario 3 — Same Path, Multiple Methods

### Question

- You find:

  ```text
  GET /api/account
  PATCH /api/account
  DELETE /api/account
  ```

- How do you treat them?

### Strong Answer

- As distinct operations with potentially different authorization and impact. I would map read, update, and delete behavior separately.

---

## Scenario 4 — Numeric IDs

### Question

- You observe:

  ```text
  GET /api/orders/1001
  ```

- What is your next step?

### Strong Answer

- I would first identify the object, ownership model, expected access control, role context, and baseline behavior. Then I would form a controlled authorization hypothesis using authorized test objects.

---

## Scenario 5 — Admin Path Found

### Question

- Target contains `/admin/users`. Is this a vulnerability?

### Strong Answer

- No. It is an observation. I need to understand intended access, verify role enforcement through authorized testing, and demonstrate reproducible impact before it becomes a finding.

---

## Scenario 6 — Response Returns 200

### Question

- A modified request returns `200 OK`. Does that prove a vulnerability?

### Strong Answer

- No. I would compare the response body, returned data, application state, and baseline behavior. Status code alone is insufficient.

---

## Scenario 7 — Endpoint Missing From Target

### Question

- You know a feature exists, but its endpoint is missing from Target. How do you troubleshoot it?

### Strong Answer

- I would perform the feature and check Proxy history. If the request appears there, I would inspect Target filters and views. If it does not, I would troubleshoot proxy configuration, role, authentication state, and workflow conditions.

---

## Scenario 8 — Large Application

### Question

- How do you avoid getting lost in a Site Map containing thousands of entries?

### Strong Answer

- I organize by scope, hosts, business functions, roles, objects, endpoint families, and workflows. I maintain a coverage matrix and prioritized hypothesis queue instead of testing requests randomly.

---

## Scenario 9 — Different User Roles

### Question

- You have authorized User and Admin accounts. How would Target help?

### Strong Answer

- I would browse the application under each role, compare available functionality and API calls, map role-specific endpoints and objects, and use those differences to define authorization hypotheses.

---

## Scenario 10 — New Functionality Appears Late

### Question

- A new API family appears after completing a workflow. What do you do?

### Strong Answer

- I would verify scope, classify the endpoints, update the functional and endpoint maps, identify associated objects and roles, update coverage, prioritize the new surface, and create hypotheses where appropriate.

---

## Rapid-Fire Questions

1. **Target or Proxy for chronological traffic?**

   - Proxy history.

2. **Target or Repeater for manual request modification?**

   - Repeater.

3. **Does Site Map equal complete application inventory?**

   - No.

4. **Does observed equal authorized?**

   - No.

5. **Should unknown hosts be actively tested?**

   - Not until authorization is confirmed.

6. **What should be mapped besides URLs?**

   - Methods, roles, objects, inputs, state changes, and workflows.

7. **What helps prevent coverage gaps?**

   - A coverage matrix.

8. **What comes before a controlled modification?**

   - A baseline.

9. **What comes before a finding?**

   - Validation and evidence.

10. **What is Target's core professional value?**

    - Organizing application structure and attack surface into a systematic investigation model.

---

## Interview Answer Framework

- For scenario questions, structure answers as:

  ```text
  1. Verify authorization.

  2. Establish context.

  3. Capture baseline.

  4. Form hypothesis.

  5. Test one variable carefully.

  6. Compare behavior.

  7. Validate impact.

  8. Document evidence.
  ```

- This demonstrates disciplined methodology rather than tool memorization.

---

## Self-Assessment

- You should be able to answer without notes:

  ```text
  [ ] What Target does

  [ ] How Site Map is populated

  [ ] Scope vs authorization

  [ ] Host classification

  [ ] Attack-surface mapping

  [ ] Endpoint inventory

  [ ] Endpoint families

  [ ] Object mapping

  [ ] Role mapping

  [ ] Workflow mapping

  [ ] Coverage tracking

  [ ] Hypothesis-driven testing

  [ ] Target-to-Repeater workflow

  [ ] Troubleshooting missing content

  [ ] Safe handling of unknown hosts
  ```

---

## Quick Revision

```text
Target
   │
   ├── Site Map
   └── Scope
        │
        ▼
Map
   │
   ├── Hosts
   ├── Functions
   ├── Endpoints
   ├── Roles
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
Hypothesize
        │
        ▼
Test
        │
        ▼
Validate
```

- Remember:

  ```text
  Burp Scope ≠ Authorization

  Site Map ≠ Complete Inventory

  Observed Host ≠ Authorized Host

  Endpoint ≠ Vulnerability

  Hypothesis ≠ Finding

  Evidence Before Reporting
  ```

---

## Key Takeaways

- Interviewers may ask about both Target features and how you use them in a real assessment.

- Strong answers connect Site Map and scope with application mapping, authorization discipline, endpoint analysis, roles, objects, workflows, and testing coverage.

- The Site Map should be treated as an evolving representation of discovered application content.

- Burp scope is a technical configuration and must never be confused with authorization.

- Professional Target usage is systematic: map, organize, prioritize, hypothesize, test, validate, and document.
