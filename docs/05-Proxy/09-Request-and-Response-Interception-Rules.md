# Request and Response Interception Rules

## Overview

- Burp Proxy can pause HTTP requests and responses so that you can inspect or modify them before they continue to their destination.

- During real application testing, intercepting every message quickly becomes inefficient.

- Modern applications generate large amounts of background traffic, including:

  * Images.
  * Stylesheets.
  * JavaScript files.
  * Analytics requests.
  * API calls.
  * Polling requests.
  * Third-party resources.
  * Telemetry.
  * WebSocket-related traffic.

- **Interception rules** help control which traffic Burp pauses.

- Properly configured rules make interception:

  * More focused.
  * Less disruptive.
  * Easier to analyze.
  * Safer during authorized assessments.
  * More efficient during manual testing.

---

## Learning Objectives

- By the end of this lesson, you will be able to:

  * Explain the purpose of interception rules.
  * Distinguish request interception from response interception.
  * Understand how rule conditions affect intercepted traffic.
  * Use target scope to reduce interception noise.
  * Identify useful criteria for filtering traffic.
  * Design focused interception strategies.
  * Recognize risks created by overly broad or overly narrow rules.
  * Troubleshoot unexpected interception behavior.
  * Apply interception rules within professional Burp workflows.

---

## Core Mental Model

```text
Browser / Client
      │
      ▼
Burp Proxy
      │
      ▼
Interception Rules
      │
      ├── Match
      │      ↓
      │   Pause Message
      │
      └── No Match
             ↓
          Continue
```

- Interception rules act as decision logic.

- They help answer:

  ```text
  Should Burp stop this message for manual review?
  ```

- Interception should be intentional rather than indiscriminate.

---

## Intercept ON Does Not Mean Intercept Everything

- The **Intercept ON/OFF** control determines whether interception is active.

- Interception rules determine which eligible messages are actually paused.

```text
Intercept ON
    +
Rule Matches
    =
Message Paused
```

- Depending on the active configuration:

```text
Intercept ON
    +
Rule Does Not Match
    =
Message Continues
```

- This distinction is important when troubleshooting why a request was or was not intercepted.

---

## Why Interception Rules Matter

- Consider loading a modern application.

- A single page may generate:

  ```text
  GET /dashboard

  GET /app.js

  GET /styles.css

  GET /logo.svg

  GET /api/profile

  GET /api/notifications

  POST /analytics

  GET /tracking/pixel

  GET /api/messages

  GET /favicon.ico
  ```

- If every request stops manually, normal browsing becomes slow.

- The tester may spend more time forwarding irrelevant traffic than analyzing meaningful requests.

- Interception rules reduce this noise.

---

## Request Interception

- Request interception pauses selected requests before they are sent onward.

```text
Client
    ↓
Request
    ↓
Burp
    ↓
Request Interception Rules
    ↓
Pause / Continue
    ↓
Server
```

- Request interception is useful when you need to modify data before the server processes it.

- Examples include controlled testing of:

  * Parameters.
  * Headers.
  * Cookies.
  * HTTP methods.
  * Object identifiers.
  * Authentication values.
  * Workflow fields.
  * Content types.

---

## Response Interception

- Response interception pauses selected responses before they reach the client.

```text
Server
    ↓
Response
    ↓
Burp
    ↓
Response Interception Rules
    ↓
Pause / Continue
    ↓
Client
```

- Response interception can be useful when investigating:

  * Redirect behavior.
  * Security headers.
  * Cookies.
  * Client-side feature controls.
  * Response-driven application behavior.
  * Browser-visible configuration.

- Intercepting every response is usually unnecessary.

- Enable response interception selectively when the investigation requires it.

---

## Request vs Response Interception

| Interception Type | Message Direction | Typical Purpose |
| --- | --- | --- |
| Request | Client → Server | Inspect or modify data before server processing |
| Response | Server → Client | Inspect or modify data before client processing |

- Both can be useful, but they answer different testing questions.

---

## Scope-Based Interception

- One of the most useful filtering strategies is to focus interception on **in-scope traffic**.

```text
Request
    ↓
Is Target In Scope?
    │
    ├── Yes
    │    ↓
    │  Eligible for Interception
    │
    └── No
         ↓
       Continue
```

- This reduces interruptions from:

  * Search engines.
  * Analytics platforms.
  * Advertising services.
  * Browser background traffic.
  * Third-party CDNs.
  * Unrelated applications.

- Scope-based interception also supports safer testing by helping keep attention on authorized assets.

- Scope is a workflow control, not a substitute for understanding the written authorization and rules of engagement.

---

## Host-Based Rules

- Rules can focus on specific hosts or domains.

- Example conceptual target:

  ```text
  app.example.test
  ```

- You may want to intercept application traffic while allowing unrelated third-party requests to continue.

```text
app.example.test
    ↓
Intercept

analytics.example.net
    ↓
Do Not Intercept
```

- Host-based filtering is useful when applications depend on many external services.

---

## URL-Based Rules

- Interception can be focused around specific URL patterns.

- Example:

  ```text
  /api/
  ```

- This can help during API-focused testing.

```text
/api/users
/api/orders
/api/account
        ↓
     Intercept
```

- Meanwhile, static content may continue normally.

```text
/images/
/fonts/
/assets/
        ↓
     Continue
```

- Avoid making URL rules so narrow that relevant traffic is accidentally excluded.

---

## HTTP Method-Based Rules

- HTTP methods can provide useful filtering criteria.

- Example investigation:

  ```text
  Focus on state-changing requests
  ```

- Common state-changing methods may include:

  ```text
  POST

  PUT

  PATCH

  DELETE
  ```

- A focused rule may reduce interruptions from routine `GET` requests.

- However:

  ```text
  HTTP Method

  ≠

  Guaranteed Security Meaning
  ```

- Applications may use methods inconsistently.

- Do not assume that every `GET` is harmless or every `POST` changes state.

---

## File Extension and Resource-Type Filtering

- Static resources often create significant interception noise.

- Examples include:

  ```text
  .png
  .jpg
  .jpeg
  .gif
  .svg
  .ico
  .css
  .woff
  .woff2
  ```

- Excluding routine static resources can make manual interception more manageable.

- JavaScript requires more care.

- JavaScript files may reveal:

  * API endpoints.
  * Feature names.
  * Client-side routes.
  * Parameter names.
  * Application logic.
  * Configuration.

- Do not automatically treat all JavaScript as irrelevant.

---

## Content-Type Considerations

- Response interception may be filtered based on content type.

- Examples:

  ```text
  text/html

  application/json

  text/javascript

  application/javascript
  ```

- During API testing, JSON responses may be particularly relevant.

- During client-side analysis, HTML and JavaScript responses may matter more.

- Filtering should reflect the current investigation goal.

---

## Combining Conditions

- Effective rules often combine multiple conditions.

- Conceptually:

```text
Target Is In Scope
        AND
URL Contains /api/
        AND
Method Is POST
        ↓
Intercept
```

- Combined rules can dramatically reduce noise.

- But every added condition narrows visibility.

- Excessive filtering may hide important traffic.

---

## AND vs OR Logic

- Rule logic matters.

### AND Logic

```text
Condition A
AND
Condition B
```

- Both conditions must be satisfied.

- Example:

  ```text
  In Scope

  AND

  URL Contains /api/
  ```

### OR Logic

```text
Condition A
OR
Condition B
```

- Either condition may be sufficient.

- Misunderstanding logical relationships can cause:

  * Too much traffic to be intercepted.
  * Important traffic to be missed.

- Review rule behavior after making changes.

---

## Positive and Negative Conditions

- Rules may conceptually include positive or negative logic.

### Positive Condition

```text
Intercept if:

URL contains /api/
```

### Negative Condition

```text
Do not intercept if:

File extension is .png
```

- Negative filtering is useful for removing predictable noise.

- Positive filtering is useful for focusing on specific investigation targets.

- A balanced configuration often uses both concepts.

---

## Example: Basic In-Scope Workflow

```text
All Browser Traffic
        ↓
Burp Proxy
        ↓
Is Request In Scope?
        │
        ├── No
        │    ↓
        │  Continue
        │
        └── Yes
             ↓
          Intercept
```

- This is a strong starting point for many manual assessments.

- It keeps interception focused while HTTP history can still provide broader visibility depending on configuration.

---

## Example: Authentication Testing

- Suppose you are investigating login and session behavior.

- Relevant endpoints may include:

  ```text
  /login

  /logout

  /session

  /refresh

  /password-reset

  /mfa
  ```

- A focused interception strategy may prioritize requests involving these flows.

```text
Authentication Endpoint
        ↓
Intercept
        ↓
Inspect:
Credentials
Cookies
Tokens
Headers
Redirects
```

- Do not filter so aggressively that supporting authentication requests are missed.

---

## Example: Authorization Testing

- Suppose the application uses:

  ```text
  /api/users/123

  /api/orders/456

  /api/projects/789
  ```

- You may focus interception on API requests containing object identifiers.

```text
In-Scope API Request
        ↓
Object Identifier Present
        ↓
Intercept
        ↓
Establish Baseline
        ↓
Controlled Modification
```

- Repeater is usually better for repeated controlled authorization testing after the request has been captured.

---

## Example: State-Changing Requests

- During workflow analysis, you may prioritize:

  ```text
  POST
  PUT
  PATCH
  DELETE
  ```

- Example:

```text
Browse Normally
      ↓
GET Requests Continue
      ↓
State-Changing Request Appears
      ↓
Intercept
      ↓
Inspect Security-Sensitive Fields
```

- This can be useful when mapping:

  * Account changes.
  * Purchases.
  * Permission updates.
  * Profile modifications.
  * Administrative actions.

---

## Interception vs HTTP History

- Interception and HTTP history serve different purposes.

### Interception

```text
Pause Before Continuing
```

### HTTP History

```text
Record and Review Traffic
```

- You do not need to intercept every request to analyze it.

- A highly effective workflow is:

```text
Intercept OFF
      ↓
Browse Normally
      ↓
Review HTTP History
      ↓
Identify Interesting Request
      ↓
Send to Repeater
      ↓
Perform Controlled Testing
```

- Turn interception on when you specifically need to modify a request before the application processes it.

---

## Why Over-Interception Is Inefficient

- Intercepting too much traffic creates several problems.

```text
Too Many Messages
      ↓
Constant Forwarding
      ↓
Lost Context
      ↓
Tester Fatigue
      ↓
Important Request Missed
```

- Excessive interception can also:

  * Break page loading.
  * Delay asynchronous requests.
  * Affect timing-sensitive behavior.
  * Interrupt application workflows.

- More interception does not automatically mean better testing.

---

## Why Under-Interception Can Also Be a Problem

- Overly restrictive rules may hide messages you intended to inspect.

- Example:

  ```text
  Rule:
  Only intercept POST requests
  ```

- But the application may perform a sensitive operation using:

  ```text
  GET

  PUT

  PATCH

  DELETE
  ```

- Filtering assumptions must be validated against actual application behavior.

---

## Interception and Application State

- Pausing requests can affect application state and timing.

- Consider:

```text
Request A
    ↓
Request B
    ↓
Request C
```

- If Request B is intercepted and held:

```text
Request A
    ↓
Request B [PAUSED]
    ↓
Request C may wait, fail, or behave differently
```

- This matters for:

  * Authentication flows.
  * Multi-step workflows.
  * Asynchronous applications.
  * Race-sensitive behavior.
  * Token refresh mechanisms.
  * Single-page applications.

- Be aware that interception itself can alter observed behavior.

---

## Response Interception Use Case

- Suppose a server returns:

  ```http
  HTTP/1.1 302 Found
  Location: /dashboard
  Set-Cookie: session=...
  ```

- Response interception allows you to inspect the response before the browser processes it.

- This may help investigate:

  * Redirect chains.
  * Cookie issuance.
  * Client behavior.
  * Security headers.

- The goal is controlled observation, not arbitrary modification.

---

## Response Modification Caution

- Modifying a response changes what the client sees.

- It does not necessarily change server-side security state.

```text
Modified Client Response

≠

Server-Side Authorization Bypass
```

- For example, changing:

  ```text
  role=user
  ```

  to:

  ```text
  role=admin
  ```

  in a response does not prove that the server granted administrative privileges.

- Always validate actual server-side behavior and impact.

---

## Interception Rules and Testing Scope

- Interception rules can support scope discipline.

- Example:

```text
Authorized Host
    ↓
Intercept

Unrelated Host
    ↓
Do Not Intercept for Testing
```

- However:

  ```text
  Burp Scope Configuration

  ≠

  Legal Authorization
  ```

- Always follow the actual program scope, engagement documentation, and rules of engagement.

---

## Rule Design Strategy

- Start simple.

```text
Step 1
Define Scope

Step 2
Intercept In-Scope Requests

Step 3
Observe Traffic

Step 4
Identify Noise

Step 5
Add Focused Exclusions

Step 6
Add Specialized Rules Only When Needed
```

- Avoid building a complicated rule set before understanding the application's traffic.

---

## Temporary Rules

- Some rules are useful only during a specific investigation.

- Example:

  ```text
  Intercept only requests containing:

  /checkout
  ```

- After the test, remove or disable the temporary rule.

- Forgotten rules can cause confusing behavior later.

---

## Hidden State Problem

- Burp configuration can silently influence what you observe.

```text
Unexpected Behavior
      ↓
Possible Application Issue?
      ↓
Or Active Interception Rule?
```

- Before concluding that traffic is missing, check:

  * Interception state.
  * Request interception rules.
  * Response interception rules.
  * Scope.
  * Match and Replace rules.
  * Proxy listener configuration.

---

## Troubleshooting: Expected Request Is Not Intercepted

- Use a structured process.

```text
1. Is Intercept ON?
        ↓
2. Is Traffic Passing Through Burp?
        ↓
3. Does the Request Appear in HTTP History?
        ↓
4. Does the Request Match Active Rules?
        ↓
5. Is Scope Affecting the Rule?
        ↓
6. Is Another Condition Excluding It?
```

- If the request appears in HTTP history but is not paused, interception rules are a likely area to inspect.

---

## Troubleshooting: Too Much Traffic Is Intercepted

- Check whether:

  * Rules are too broad.
  * Out-of-scope traffic is included.
  * Static resources are not filtered.
  * Third-party hosts are being intercepted.
  * Multiple rules overlap.

- Simplify the configuration before adding more rules.

---

## Troubleshooting: Browser Appears Frozen

- A browser may wait because one or more requests are paused.

```text
Browser
    ↓
Request Sent
    ↓
Burp Holds Request
    ↓
Browser Waits
```

- Check the Intercept tab.

- Forward, drop, or intentionally handle the pending request.

- Background requests may also be waiting.

---

## Troubleshooting: Application Behaves Differently with Intercept ON

- Possible causes include:

  * Timing changes.
  * Expired tokens.
  * Delayed asynchronous requests.
  * Held authentication requests.
  * Dependency ordering.
  * Application timeouts.

- Compare behavior with interception disabled.

- Use HTTP history and Repeater when real-time interception is unnecessary.

---

## Professional Interception Workflow

```text
Define Authorized Scope
        ↓
Configure Burp Scope
        ↓
Browse with Intercept OFF
        ↓
Understand Normal Traffic
        ↓
Identify High-Value Requests
        ↓
Design Focused Interception Rules
        ↓
Enable Interception When Needed
        ↓
Modify One Variable
        ↓
Observe Result
        ↓
Return to Normal Traffic Flow
```

- This approach minimizes disruption and preserves context.

---

## Minimal-Interception Principle

- Intercept only what you need.

```text
Maximum Interception

≠

Maximum Visibility
```

- HTTP history already provides broad traffic visibility.

- Interception is most valuable when timing or pre-delivery modification matters.

---

## Common Mistakes

* Leaving Intercept ON for all browsing without a reason.

* Intercepting every static resource.

* Assuming a request disappeared when a rule simply allowed it through.

* Creating complex rules before understanding normal application traffic.

* Forgetting temporary interception rules.

* Using method-only filters and assuming application semantics from HTTP verbs.

* Ignoring third-party traffic noise.

* Holding requests long enough to alter session or workflow behavior.

* Treating response modification as proof of a server-side vulnerability.

* Assuming Burp scope is equivalent to authorization.

* Changing multiple interception settings simultaneously while troubleshooting.

---

## Professional Tips

* Browse with Intercept OFF when mapping the application.

* Use HTTP history to identify requests worth deeper investigation.

* Use scope-based rules as a clean starting point.

* Add exclusions only after observing actual noise.

* Keep temporary rules documented.

* Review active rules when Burp behavior seems inconsistent.

* Use Repeater for controlled repeated testing rather than repeatedly intercepting the same workflow.

* Be cautious when intercepting authentication, token-refresh, or timing-sensitive requests.

* Restore a predictable configuration after specialized testing.

---

## Field Notes

- Interception rules are workflow controls.

- They should help you answer a testing question rather than create additional complexity.

- Before adding a rule, ask:

  ```text
  What traffic am I trying to observe?

  Why must it be paused?

  What traffic can safely continue?

  Could this rule hide something important?

  Will pausing this request alter application behavior?
  ```

- A small, understandable rule set is generally more reliable than a large collection of forgotten conditions.

---

## Practice Tasks

1. Open Burp Proxy and locate the request interception rules.

2. Review the current default rules before changing anything.

3. Configure an authorized test target in Burp scope.

4. Browse the target with Intercept OFF.

5. Review HTTP history and identify:

   * HTML requests.
   * API requests.
   * Static resources.
   * Third-party traffic.

6. Enable interception.

7. Observe which requests are paused.

8. Configure a focused rule that reduces irrelevant traffic.

9. Verify that important application requests are still visible.

10. Test a state-changing request.

11. Observe how holding the request affects the application.

12. Review response interception settings.

13. Enable response interception temporarily in a lab.

14. Observe a relevant response.

15. Restore the original configuration after the exercise.

---

## Practical Investigation Exercise

- Scenario:

  * You are testing an authorized application.
  * Every page load generates dozens of requests.
  * Most intercepted messages are images, fonts, analytics, and third-party resources.
  * You need to focus on application API requests.

- Design a rule strategy using:

  ```text
  Scope

  Host

  URL Pattern

  Resource Type
  ```

- Your objective is to reduce noise without losing relevant API traffic.

- Then answer:

  1. Why is intercepting every request inefficient?
  2. Why might excluding all `GET` requests be unsafe?
  3. Why should JavaScript not always be treated as irrelevant?
  4. How would you verify that your rules are not hiding important traffic?

---

## Interview Questions

1. What are interception rules in Burp Proxy?

2. What is the difference between request and response interception?

3. Does Intercept ON necessarily mean every request will be paused?

4. Why is scope-based interception useful?

5. Why should testers avoid intercepting every request during normal browsing?

6. How can static-resource filtering improve workflow efficiency?

7. What risks come from overly restrictive interception rules?

8. How can holding a request alter application behavior?

9. Why is HTTP history often preferable to constant interception?

10. When is request interception especially useful?

11. When might response interception be useful?

12. Why does modifying a response not necessarily prove a server-side vulnerability?

13. How would you troubleshoot a request that appears in HTTP history but is not intercepted?

14. Why should temporary rules be removed after testing?

15. What is the minimal-interception principle?

---

## Quick Revision

```text
Intercept ON/OFF
=
Whether Interception Is Active
```

```text
Interception Rules
=
Which Messages Are Paused
```

```text
Request Interception

Client
→ Burp
→ Server
```

```text
Response Interception

Server
→ Burp
→ Client
```

```text
Good Starting Strategy

Define Scope
→ Browse Normally
→ Observe Traffic
→ Add Focused Rules
```

```text
HTTP History
=
Broad Review

Interception
=
Pause Before Delivery
```

```text
Too Much Interception
=
Noise + Workflow Disruption
```

```text
Too Little Interception
=
Potentially Missed Relevant Traffic
```

```text
Response Modification

≠

Proof of Server-Side Impact
```

```text
Best Practice

Intercept Only What You Need
```

---

## Summary

- Request and response interception rules determine which messages Burp pauses for manual inspection or modification.

- Effective rules reduce noise while preserving visibility into security-relevant traffic.

- Scope, hosts, URLs, methods, and resource characteristics can all help focus interception.

- Interception should complement HTTP history and Repeater rather than replace them.

- Overly broad rules create unnecessary disruption, while overly narrow rules can hide important traffic.

- Professional Burp usage relies on simple, intentional, and well-understood interception rules that support a specific testing objective.
