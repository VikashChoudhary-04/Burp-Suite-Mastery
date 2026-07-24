# Common Mistakes and Troubleshooting

## Overview

- The Target tool is conceptually simple, but incorrect scope configuration, incomplete browsing, poor organization, and mistaken assumptions can significantly reduce the quality of an assessment.

- Many Target-related problems are not software failures.

- They are workflow problems such as:

  * Incomplete Site Map coverage.
  * Incorrect scope rules.
  * Missing authenticated traffic.
  * Excessive third-party noise.
  * Confusing discovered content with authorized content.
  * Treating the Site Map as a complete representation of the application.
  * Losing track of roles, objects, and application state.

- This lesson covers common mistakes, troubleshooting methods, and practical recovery workflows.

---

## Learning Objectives

- By the end of this lesson, you should be able to:

  * Recognize common Target workflow mistakes.
  * Troubleshoot an incomplete or noisy Site Map.
  * Diagnose scope configuration problems.
  * Understand why expected endpoints may be missing.
  * Handle third-party and out-of-scope traffic safely.
  * Avoid incorrect conclusions from status codes or Site Map entries.
  * Recover from poor mapping and coverage practices.
  * Apply a systematic troubleshooting process.

---

## Mistake 1 — Treating the Site Map as Complete

- A common assumption is:

  ```text
  "If it is not in the Site Map, it does not exist."
  ```

- This is incorrect.

- The Site Map reflects what Burp has learned through observed or discovered traffic.

- Functionality may be absent because:

  * It has not been visited.
  * Authentication is required.
  * A different role is required.
  * A workflow has not been completed.
  * A feature flag controls it.
  * JavaScript triggers it conditionally.
  * A separate API host is involved.

### Fix

```text
Review Coverage
    │
    ▼
Exercise Missing Workflows
    │
    ▼
Test Authorized Roles
    │
    ▼
Review Proxy History
    │
    ▼
Revisit Site Map
```

---

## Mistake 2 — Assuming Every Site Map Entry Is In Scope

- Burp may observe:

  * Analytics.
  * CDNs.
  * Identity providers.
  * Payment services.
  * Support widgets.
  * External APIs.

- Presence in Target does not grant authorization.

### Fix

- Classify hosts as:

  ```text
  Confirmed In Scope

  Confirmed Out of Scope

  Unknown — Requires Verification
  ```

- Do not actively test unknown or out-of-scope systems.

---

## Mistake 3 — Adding an Overly Broad Scope

- Example:

  ```text
  Authorized:
  app.example.com

  Incorrect assumption:
  *.example.com
  ```

- Broad scope rules can create:

  * Noise.
  * Accidental testing of unauthorized assets.
  * Misleading Site Map organization.
  * Unsafe automation targets.

### Fix

- Configure scope to match the authoritative rules precisely.

- Verify:

  * Host.
  * Protocol.
  * Port.
  * Path.
  * Explicit exclusions.

---

## Mistake 4 — Scope Is Too Narrow

- The opposite problem also occurs.

- Example:

  ```text
  Authorized:
  app.example.com
  api.example.com

  Burp configured only for:
  app.example.com
  ```

- Important API traffic may then be filtered from the tester's normal view.

### Fix

- Compare Burp scope with the actual authorization document.

- Add only explicitly authorized missing assets.

---

## Mistake 5 — Confusing Burp Scope With Permission

- Adding a host to Burp scope is a local configuration action.

- It does not create legal or contractual permission.

  ```text
  Burp Scope ≠ Authorization
  ```

### Fix

- Always derive technical scope from:

  * Rules of engagement.
  * Program policy.
  * Written authorization.
  * Approved lab boundaries.

---

## Mistake 6 — Browsing With Intercept Left On

- Symptoms may include:

  * Pages appear stuck.
  * Resources fail to load.
  * Site Map populates slowly.
  * Application workflows break.

- Cause:

  ```text
  Browser Request
       │
       ▼
  Burp Intercept
       │
       X
  Waiting for Forward
  ```

### Fix

- During normal mapping, keep interception off unless you intentionally need to pause a request.

- Proxy history will still record traffic.

---

## Mistake 7 — Missing Authenticated Content

- Symptoms:

  * Site Map contains only login and public pages.
  * Dashboard endpoints are absent.
  * APIs appear incomplete.

### Possible Causes

- Login was not completed through the proxied browser.

- Session expired.

- Browser traffic is not passing through Burp.

- A different browser profile is being used.

### Fix

```text
Verify Proxy
    │
    ▼
Authenticate Normally
    │
    ▼
Confirm Session Works
    │
    ▼
Exercise Authenticated Features
    │
    ▼
Review Proxy History
    │
    ▼
Review Site Map
```

---

## Mistake 8 — Missing Role-Specific Content

- A standard user may not expose:

  * Administrative pages.
  * Management APIs.
  * Reporting.
  * Approval workflows.

### Fix

- If authorized accounts for multiple roles are provided:

  * Browse each role separately.
  * Record role context.
  * Compare available functionality.
  * Update coverage.

- Never create or obtain unauthorized privileges merely to expand coverage.

---

## Mistake 9 — Excessive Third-Party Noise

- Symptoms:

  ```text
  Site Map
  ├── target.example
  ├── analytics.vendor
  ├── fonts.vendor
  ├── cdn.vendor
  ├── support.vendor
  └── identity.vendor
  ```

- This can obscure relevant target traffic.

### Fix

- Use:

  * Correct scope.
  * Scope-based filters.
  * Host classification.
  * Focused investigation notes.

- Do not delete useful evidence merely because the interface looks busy.

---

## Mistake 10 — Ignoring Proxy History

- Target provides structure.

- Proxy history provides chronological request detail.

- Using only one can limit understanding.

### Better Workflow

```text
Target
→ Understand structure

Proxy History
→ Find exact requests and sequence

Repeater
→ Perform controlled testing
```

---

## Mistake 11 — Ignoring HTTP Methods

- These are not equivalent:

  ```text
  GET /api/users/42

  PATCH /api/users/42

  DELETE /api/users/42
  ```

- The same path may represent several distinct security operations.

### Fix

- Track endpoint families by both path and method.

---

## Mistake 12 — Ignoring Application State

- Requests may behave differently depending on whether:

  * Email is verified.
  * Payment is completed.
  * Account is active.
  * Invitation is accepted.
  * Workflow step is complete.

### Fix

- Record application state alongside requests.

  ```text
  Request:
  POST /order/confirm

  State:
  Payment completed

  Role:
  User
  ```

---

## Mistake 13 — Losing Track of Object Ownership

- Authorization testing becomes unreliable if you do not know who owns each object.

- Example:

  ```text
  Project 101 → User A

  Project 202 → User B
  ```

### Fix

- Maintain an object ownership map before testing access boundaries.

---

## Mistake 14 — Treating a 200 Response as Proof of Access

- A `200 OK` may return:

  * An error message.
  * An empty object.
  * A generic application shell.
  * A login page.
  * Redacted data.

### Fix

- Compare:

  * Status.
  * Body.
  * Length.
  * Headers.
  * Actual application effect.

---

## Mistake 15 — Treating a 404 as Proof of Nonexistence

- Applications may return `404` to hide:

  * Unauthorized resources.
  * Privileged routes.
  * Invalid object references.

### Fix

- Interpret status codes in context.

- Do not infer existence or authorization from status alone.

---

## Mistake 16 — Reporting Interesting Endpoints as Vulnerabilities

- Example:

  ```text
  /admin
  ```

- Discovering this path is not itself evidence of a vulnerability.

### Correct Process

```text
Observation
    │
    ▼
Understand Expected Access
    │
    ▼
Create Hypothesis
    │
    ▼
Controlled Test
    │
    ▼
Validate Impact
    │
    ▼
Finding
```

---

## Mistake 17 — Testing Randomly

- Randomly selecting endpoints creates:

  * Coverage gaps.
  * Duplicate testing.
  * Weak prioritization.
  * Poor evidence organization.

### Fix

- Organize by:

  * Function.
  * Role.
  * Object.
  * Workflow.
  * Priority.

- Maintain a coverage matrix and hypothesis queue.

---

## Mistake 18 — Not Establishing Baselines

- Without a baseline, modified responses are difficult to interpret.

### Fix

- Preserve:

  ```text
  Original Request

  Original Response

  Role

  Session Context

  Object Ownership

  Application State
  ```

- Then change one relevant variable at a time.

---

## Mistake 19 — Modifying Too Many Variables

- Example:

  ```text
  Changed:
  - Session
  - Object ID
  - Method
  - Headers
  - Body
  ```

- If behavior changes, the cause is unclear.

### Fix

```text
Baseline
    │
    ▼
Change One Variable
    │
    ▼
Compare
    │
    ▼
Record
```

---

## Mistake 20 — Failing to Revisit Target

- The Site Map evolves during testing.

- New content may appear after:

  * New roles.
  * New workflows.
  * API calls.
  * Error conditions.
  * Feature activation.
  * Object creation.

### Fix

- Revisit Target periodically and update coverage.

---

## Troubleshooting — Site Map Is Empty

### Check 1 — Is Browser Traffic Reaching Burp?

- Open Proxy history.

- If no requests appear, verify:

  * Proxy listener.
  * Browser proxy configuration.
  * Burp browser usage.
  * Listener address and port.

### Check 2 — Is the Application Actually Being Browsed?

- The Site Map needs observed or discovered content.

### Check 3 — Are Filters Hiding Content?

- Review display filters.

---

## Troubleshooting — Only HTTP Traffic Appears

- If HTTPS traffic is missing or failing, possible causes include:

  * Proxy misconfiguration.
  * Certificate trust issues.
  * Browser not using the intended proxy.

### Fix

- Verify the browser-Burp HTTPS setup in your authorized testing environment.

- Confirm HTTPS requests appear in Proxy history before troubleshooting Target itself.

---

## Troubleshooting — Expected Endpoint Is Missing

### Possible Causes

- Feature not exercised.

- Wrong role.

- Wrong account state.

- Endpoint loaded dynamically.

- API call failed.

- Request bypassed Burp.

- Filters hide it.

### Workflow

```text
Perform Feature
    │
    ▼
Check Proxy History
    │
    ├── Request Present → Review Target / Filters
    │
    └── Request Missing → Troubleshoot Proxy / Application Flow
```

---

## Troubleshooting — API Host Is Missing

- Check whether:

  * The browser actually contacted it.
  * The API call was blocked.
  * The request appears in Proxy history.
  * The host is hidden by filters.
  * The API is loaded only after authentication.

- Verify authorization before adding or testing a separate API host.

---

## Troubleshooting — Too Many Hosts

- Causes:

  * Third-party dependencies.
  * Browser background traffic.
  * Broad scope.
  * Extensions.
  * External authentication.

### Fix

- Review:

  * Scope.
  * Filters.
  * Browser environment.
  * Host ownership.

- Focus on confirmed authorized assets.

---

## Troubleshooting — Site Map Contains Old or Irrelevant Data

- Long-running Burp projects may contain previous traffic.

### Fix

- Use a clean project or carefully distinguish current assessment data.

- Preserve evidence before removing anything important.

- Maintain clear project naming and assessment boundaries.

---

## Troubleshooting — Scope Filters Hide Needed Traffic

- Symptoms:

  * Request exists in Proxy history but disappears when filtering to in-scope traffic.

### Fix

1. Verify the request host.
2. Compare it with authorization.
3. Review Burp scope rules.
4. Correct the scope only if the asset is explicitly authorized.
5. Reapply filters.

---

## Troubleshooting — Duplicate-Looking Endpoints

- Examples:

  ```text
  /profile
  /profile/
  /api/profile
  /api/v1/profile
  ```

- They may represent:

  * Redirect aliases.
  * Frontend and backend routes.
  * Different API versions.
  * Distinct operations.

### Fix

- Compare:

  * Methods.
  * Responses.
  * Parameters.
  * Authentication.
  * Application purpose.

- Do not merge them prematurely.

---

## Troubleshooting — Application Uses a Single-Page Interface

- Single-page applications may show relatively few browser paths while generating many API requests.

### Fix

- Focus on:

  * Proxy history.
  * XHR/fetch traffic.
  * API hosts.
  * JSON requests.
  * JavaScript references.

- The visible browser URL may not represent the true backend attack surface.

---

## Troubleshooting — Content Appears Only After Refresh or Action

- Modern applications may load resources conditionally.

### Fix

- Exercise:

  * Navigation.
  * Search.
  * Filters.
  * Modal dialogs.
  * Account settings.
  * Object creation.
  * Role-specific actions.

- Observe the resulting network traffic.

---

## Troubleshooting — Authentication Keeps Expiring

- Possible causes:

  * Short session lifetime.
  * Logout from another browser.
  * Account security policy.
  * Session invalidation.
  * Incorrect cookie handling.

### Fix

- Re-authenticate normally.

- Confirm the current session in the browser.

- Avoid assuming stale requests still represent valid application behavior.

---

## Troubleshooting Decision Tree

```text
Problem in Target
      │
      ▼
Is Request in Proxy History?
   /              \
 No                Yes
 │                  │
 ▼                  ▼
Check Proxy      Check Filters
Browser Setup    Scope
Application      Host Classification
Workflow         Site Map View
 │                  │
 ▼                  ▼
Retest Flow      Correct View / Scope
      \              /
       ▼            ▼
       Verify Expected Content
              │
              ▼
         Update Coverage
```

---

## Recovery Workflow for Poor Mapping

- If mapping has become disorganized:

  ```text
  1. Reconfirm scope.

  2. List authorized hosts.

  3. Identify available roles.

  4. Rebuild major functional categories.

  5. Browse core workflows systematically.

  6. Review Proxy history.

  7. Review Site Map.

  8. Map object types and APIs.

  9. Build a coverage matrix.

  10. Resume hypothesis-driven testing.
  ```

---

## Common Safety Mistakes

- Testing third-party hosts because they appeared in Target.

- Expanding scope without authorization.

- Running automated discovery against unknown assets.

- Ignoring rate restrictions.

- Testing production-impacting actions without permission.

- Using unauthorized accounts or tenants.

### Prevention

```text
Discover
    │
    ▼
Verify Authorization
    │
    ▼
Verify Technique Restrictions
    │
    ▼
Test Carefully
```

---

## Professional Troubleshooting Checklist

- When Target does not look correct, verify:

  * Is Burp receiving traffic?
  * Is the correct browser being used?
  * Is the target actually being exercised?
  * Is authentication valid?
  * Is the correct role active?
  * Are filters hiding traffic?
  * Is scope configured correctly?
  * Is the host explicitly authorized?
  * Is application state preventing the feature?
  * Does the request appear in Proxy history?
  * Is the application API-driven?
  * Has Target been revisited after new workflows?

---

## Practice Exercise

- Use an explicitly authorized training application.

1. Browse the public application.
2. Review Site Map.
3. Authenticate.
4. Review Site Map again.
5. Exercise a state-changing workflow.
6. Compare new entries.
7. Apply an in-scope filter.
8. Identify third-party hosts.
9. Record object ownership for two test objects.
10. Build a small coverage matrix.
11. Intentionally leave one workflow unexplored.
12. Diagnose why its endpoints are absent.
13. Exercise the workflow.
14. Verify that new traffic appears.
15. Update coverage.

---

## Interview Questions

1. Why might the Site Map be incomplete?
2. Does every Site Map entry belong to the authorized target?
3. What happens if Burp scope is configured too broadly?
4. What happens if scope is too narrow?
5. Why might authenticated endpoints be missing?
6. How can role-specific functionality affect Site Map coverage?
7. Why should Proxy history be used alongside Target?
8. Why should HTTP methods be tracked separately?
9. Why is object ownership important?
10. Why does a `200` response not automatically prove successful access?
11. Why does a `404` not always prove nonexistence?
12. How would you troubleshoot an empty Site Map?
13. How would you troubleshoot a missing API host?
14. Why should baseline requests be preserved?
15. What is the safest response when a newly discovered host has unclear authorization?

---

## Quick Revision

```text
Target Problem
    │
    ▼
Check Scope
    │
    ▼
Check Proxy Traffic
    │
    ▼
Check Authentication
    │
    ▼
Check Role and State
    │
    ▼
Check Filters
    │
    ▼
Exercise Workflow
    │
    ▼
Revisit Site Map
```

- Remember:

  ```text
  Site Map ≠ Complete Application

  Site Map Entry ≠ Authorization

  200 ≠ Valid Access

  404 ≠ Definitive Absence

  Discovery ≠ Vulnerability

  Troubleshoot Systematically
  ```

---

## Key Takeaways

- Most Target problems can be traced to scope, traffic flow, authentication, roles, application state, filters, or incomplete workflow coverage.

- The Site Map is a continuously evolving representation of observed application content, not a guaranteed complete inventory.

- Proxy history is essential for troubleshooting missing or unexpected Target entries.

- Scope must accurately reflect authorization without being broadened merely for convenience.

- Status codes and discovered paths require contextual analysis.

- Good mapping depends on tracking roles, objects, workflows, and coverage.

- A systematic troubleshooting workflow is more reliable than repeatedly changing unrelated Burp settings.
