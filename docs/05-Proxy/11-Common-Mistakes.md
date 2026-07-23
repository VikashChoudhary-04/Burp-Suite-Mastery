# Common Mistakes

> **Module 05 — Proxy**

---

## Overview

- Burp Proxy is often the first Burp Suite tool used during an assessment.

- Because nearly all browser traffic passes through the Proxy, small configuration or workflow mistakes can create:

  * Missing traffic.
  * Unnecessary noise.
  * Broken application behavior.
  * Incorrect testing conclusions.
  * Accidental out-of-scope requests.
  * Inefficient investigations.

- This lesson covers common Proxy mistakes and the habits that prevent them.

---

## 1. Leaving Intercept On During Normal Browsing

- One of the most common beginner mistakes is leaving **Intercept on** while trying to browse normally.

- This causes requests to stop inside Burp until they are manually forwarded.

- Symptoms may include:

  * Pages appearing to hang.
  * Images or scripts failing to load.
  * Multiple requests accumulating in the Intercept tab.
  * The application appearing broken.

- A better workflow is:

  ```text
  Normal Browsing
      ↓
  Intercept Off
      ↓
  Review HTTP History
      ↓
  Turn Intercept On Only When Needed
  ```

---

## 2. Assuming Intercept Off Means Burp Is Not Capturing Traffic

- Turning Intercept off does not normally stop Proxy logging.

- Requests can still appear in HTTP history while passing through automatically.

- Do not confuse:

  ```text
  Interception

  with

  Traffic Recording
  ```

- Use HTTP history as the primary place to review normal browsing traffic.

---

## 3. Testing Before Confirming Scope

- Never begin modifying or replaying requests before confirming that the target is authorized and in scope.

- A browser session may contact:

  * Analytics providers.
  * CDNs.
  * Authentication providers.
  * Advertising services.
  * Third-party APIs.
  * External storage services.

- Not every host visible in Burp is automatically authorized for testing.

- Before active testing:

  ```text
  Verify Authorization
      ↓
  Define Scope
      ↓
  Filter Traffic
      ↓
  Test Only Authorized Assets
  ```

---

## 4. Treating HTTP History as an Unfiltered Request Dump

- Large applications can generate thousands of requests.

- Reviewing everything without filters wastes time and hides important traffic.

- Useful filtering dimensions include:

  * Scope.
  * Host.
  * HTTP method.
  * MIME type.
  * Status code.
  * Search terms.
  * File extension.

- Focus on security-relevant traffic instead of reading every static-resource request.

---

## 5. Ignoring the Original Baseline

- Modifying a request before understanding its normal behavior makes comparison difficult.

- Always preserve a legitimate baseline first.

- Record:

  * Original request.
  * Original response.
  * Authentication state.
  * User role.
  * Relevant object ownership.
  * Expected application behavior.

- Then change one meaningful variable at a time.

---

## 6. Changing Too Many Things at Once

- Modifying several parameters, headers, cookies, or methods simultaneously makes results difficult to interpret.

- If behavior changes, you may not know which modification caused it.

- Prefer:

  ```text
  Baseline
      ↓
  Change One Variable
      ↓
  Send Request
      ↓
  Compare Result
      ↓
  Record Observation
  ```

- Controlled testing produces stronger evidence.

---

## 7. Forwarding Sensitive Modifications Accidentally

- When editing intercepted traffic, remember that clicking **Forward** sends the modified request onward.

- Before forwarding, verify:

  * Target host.
  * Request method.
  * Modified parameters.
  * Authentication context.
  * Potential state changes.
  * Scope authorization.

- Be especially careful with requests that may:

  * Delete data.
  * Change passwords.
  * Modify permissions.
  * Trigger payments.
  * Send messages.
  * Perform administrative actions.

---

## 8. Dropping Requests Without Understanding Dependencies

- Modern applications often issue multiple dependent requests.

- Dropping one request may affect:

  * Authentication.
  * CSRF tokens.
  * Session state.
  * API initialization.
  * JavaScript application state.
  * Subsequent requests.

- If the application behaves unexpectedly after dropping traffic, reproduce the workflow from a clean state before drawing conclusions.

---

## 9. Ignoring Response Behavior

- Proxy analysis should not focus only on requests.

- Responses may reveal:

  * Status codes.
  * Redirects.
  * Authentication failures.
  * Authorization decisions.
  * Error messages.
  * Security headers.
  * Tokens.
  * API data.
  * State-transition results.

- Always analyze both sides:

  ```text
  Request
      ↓
  Server Processing
      ↓
  Response
      ↓
  Final Application State
  ```

---

## 10. Assuming a Successful Response Proves a Vulnerability

- A `200 OK` response does not automatically mean an attack succeeded.

- Likewise, a response containing `"success": true` may not prove that a security-sensitive state changed.

- Validate:

  * Whether the action actually occurred.
  * Whether unauthorized data was exposed.
  * Whether the change persisted.
  * Whether another user or role was affected.
  * Whether the result is reproducible.

- Evidence must prove the security impact, not merely an unusual response.

---

## 11. Ignoring Redirects

- Redirect chains can contain important security behavior.

- A request may return:

  ```http
  HTTP/1.1 302 Found
  Location: /login
  ```

- This may indicate authentication enforcement, but the complete behavior should still be examined.

- Review:

  * Initial response.
  * `Location` header.
  * Cookies set during redirects.
  * Final destination.
  * Whether protected content was exposed before the redirect.

---

## 12. Forgetting About WebSocket Traffic

- Applications using WebSockets may perform important actions outside ordinary HTTP history.

- If you inspect only HTTP requests, you may miss:

  * Real-time messages.
  * Subscription events.
  * Chat functionality.
  * Notifications.
  * State changes.
  * WebSocket authorization behavior.

- Check WebSocket history when the application uses persistent real-time communication.

---

## 13. Misusing Match and Replace

- Match and Replace can modify traffic automatically.

- Poorly scoped rules may unintentionally alter many requests or responses.

- Common problems include:

  * Replacing values too broadly.
  * Forgetting that a rule is enabled.
  * Modifying unrelated hosts.
  * Creating misleading application behavior.
  * Testing with hidden automation active.

- Keep rules:

  * Narrow.
  * Documented.
  * Reversible.
  * Limited to authorized traffic.

---

## 14. Forgetting Active Proxy Rules

- Hidden Proxy configuration can affect investigations.

- Examples include:

  * Interception rules.
  * Match and Replace rules.
  * Listener configuration.
  * TLS settings.
  * Upstream proxy settings.

- When traffic behaves unexpectedly, inspect active Proxy configuration before assuming the target application is responsible.

---

## 15. Using Intercept for Every Manual Test

- Intercept is useful for observing or changing live traffic, but it is not the best workspace for repeated experimentation.

- For controlled testing:

  ```text
  Capture in Proxy
      ↓
  Send to Repeater
      ↓
  Establish Baseline
      ↓
  Modify
      ↓
  Compare
      ↓
  Validate
  ```

- Use the right Burp tool for each stage.

---

## 16. Replaying Requests Without Checking Session State

- Captured requests may contain expired or stale:

  * Session cookies.
  * CSRF tokens.
  * Authorization tokens.
  * Nonces.
  * One-time values.

- A failed replay may indicate stale state rather than secure behavior.

- Before interpreting a result, confirm that the request still has valid authentication and workflow context.

---

## 17. Mixing User Sessions

- Authorization testing often involves multiple accounts.

- Mixing cookies or tokens between sessions can invalidate conclusions.

- Clearly distinguish:

  ```text
  User A Session

  User B Session

  Admin Session

  Unauthenticated Session
  ```

- Record which identity produced each request and response.

---

## 18. Ignoring Browser-Generated Background Traffic

- Browsers and applications may generate traffic unrelated to the feature currently being tested.

- Examples include:

  * Analytics.
  * Telemetry.
  * Prefetch requests.
  * Background API polling.
  * Service workers.
  * Extension traffic.

- Do not assume every request in HTTP history was triggered directly by your current action.

- Correlate requests with specific user actions.

---

## 19. Failing to Reproduce Findings

- A single unusual response is not enough.

- Reproduce important observations under controlled conditions.

- A strong validation process is:

  ```text
  Recreate Baseline
      ↓
  Repeat Modification
      ↓
  Observe Same Result
      ↓
  Verify Final State
      ↓
  Confirm Security Boundary Violation
      ↓
  Preserve Evidence
  ```

---

## 20. Keeping Poor Evidence

- HTTP history can become large and difficult to navigate.

- When you identify something important, preserve:

  * Exact request.
  * Exact response.
  * Relevant timestamps.
  * User/session context.
  * Baseline comparison.
  * Modified values.
  * Final-state verification.

- Do not rely on memory when documenting findings later.

---

## 21. Treating Every Anomaly as a Vulnerability

- Unexpected behavior is a starting point for investigation, not automatically a finding.

- Distinguish:

  ```text
  Observation
      ↓
  Hypothesis
      ↓
  Controlled Test
      ↓
  Reproduction
      ↓
  Security Impact Validation
      ↓
  Finding
  ```

- This prevents false positives and weak reports.

---

## 22. Ignoring Application State

- Many requests depend on earlier workflow steps.

- Testing a request outside its expected state may produce misleading behavior.

- Consider:

  * Logged-in versus logged-out state.
  * Cart or checkout state.
  * Multi-step forms.
  * Password-reset flows.
  * Approval workflows.
  * One-time operations.

- Understand the normal workflow before manipulating individual requests.

---

## 23. Overlooking HTTPS Configuration Problems

- If HTTPS traffic does not appear correctly, do not immediately assume the application is blocking Burp.

- Check:

  * Browser proxy configuration.
  * Burp proxy listener.
  * Burp CA certificate installation.
  * Certificate trust.
  * Listener address and port.
  * Other applications already using the port.

- Troubleshoot the interception path systematically.

---

## 24. Using Burp's Embedded Browser and External Browser Interchangeably Without Tracking State

- Different browsers may maintain different:

  * Cookies.
  * Sessions.
  * Certificates.
  * Extensions.
  * Proxy settings.
  * Local storage.

- Know which browser generated the traffic you are analyzing.

- Avoid mixing sessions unless the test specifically requires it.

---

## 25. Failing to Reset the Environment

- Repeated testing can alter application state.

- Previous actions may influence later results.

- When necessary, reset:

  * Session state.
  * Test accounts.
  * Created objects.
  * Modified permissions.
  * Cart or workflow state.
  * Burp configuration.

- Reproducibility requires controlled starting conditions.

---

## Professional Mistake-Prevention Checklist

- Before testing:

  * Confirm authorization.
  * Define scope.
  * Verify Proxy configuration.
  * Confirm the correct user/session.
  * Establish a clean baseline.

- During testing:

  * Keep normal browsing interception controlled.
  * Use HTTP history filters.
  * Change one variable at a time.
  * Watch both requests and responses.
  * Track application state.
  * Use Repeater for repeated experiments.
  * Avoid unintended state-changing actions.

- Before reporting:

  * Reproduce the result.
  * Verify final state.
  * Confirm the security boundary violation.
  * Eliminate alternative explanations.
  * Preserve exact evidence.
  * Describe meaningful impact.

---

## Practice Tasks

1. Browse an authorized lab with Intercept on and observe how normal page loading is affected.

2. Turn Intercept off and confirm that requests still appear in HTTP history.

3. Apply filters to isolate only in-scope dynamic requests.

4. Capture one request and preserve it as a baseline.

5. Send the request to Repeater and change only one parameter.

6. Compare the baseline and modified responses.

7. Create a safe Match and Replace rule in a lab, observe its effect, then disable it.

8. Identify whether the lab uses WebSockets and inspect WebSocket history if applicable.

9. Reproduce one observation twice before deciding whether it represents a security issue.

10. Document the request, response, session context, modification, and final-state verification.

---

## Interview Questions

1. Why should Intercept usually be off during normal browsing?

2. Does turning Intercept off stop Burp from recording HTTP traffic?

3. Why should you establish a baseline before modifying a request?

4. Why is changing multiple variables at once a poor testing methodology?

5. Why does a `200 OK` response not necessarily prove exploitation?

6. What risks can poorly configured Match and Replace rules introduce?

7. Why should authentication and session state be checked before replaying captured requests?

8. Why is it important to distinguish traffic from multiple user sessions?

9. When should a request be moved from Proxy to Repeater?

10. What evidence should be preserved before reporting a vulnerability?

---

## Quick Revision

```text
Intercept On
→ Pause selected live traffic

Intercept Off
→ Traffic normally passes while history can still be recorded

HTTP History
→ Review and select requests

Baseline
→ Understand legitimate behavior first

One Variable at a Time
→ Keep experiments interpretable

Scope
→ Test only explicitly authorized assets

Match and Replace
→ Powerful automation that must remain controlled

Session State
→ Confirm identity, tokens, and workflow context

Response
→ Evidence, not automatic proof of impact

Final State
→ Verify what actually happened

Reproduction
→ Repeat important results

Proxy → Repeater
→ Capture first, experiment in a controlled workspace

Professional Rule
→ Observe → Baseline → Modify → Compare → Validate → Document
```

---

## Key Takeaways

- Most Proxy mistakes come from poor control of scope, interception, session state, automation, or testing methodology.

- Use HTTP history to observe traffic systematically rather than intercepting everything.

- Preserve a legitimate baseline and change one variable at a time.

- Never treat an unusual response as proof of a vulnerability without validating final state and security impact.

- Keep Proxy rules and automated modifications visible, narrow, and documented.

- Strong Proxy usage is disciplined:

  ```text
  Authorized Scope
      ↓
  Controlled Observation
      ↓
  Reliable Baseline
      ↓
  Focused Modification
      ↓
  Response Comparison
      ↓
  State Verification
      ↓
  Reproduction
      ↓
  Evidence
  ```
