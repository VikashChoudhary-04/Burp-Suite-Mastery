# Interview Questions

> **Module 05 — Proxy**

---

## Overview

- This lesson provides interview questions and concise answer points for Burp Proxy.

- Use these questions to practice explaining:

  * Proxy architecture.
  * Interception.
  * HTTP history.
  * Request and response modification.
  * Scope and filtering.
  * WebSocket history.
  * Match and Replace.
  * Troubleshooting.
  * Professional testing workflows.

- Do not memorize answers mechanically.

- Practice explaining:

  ```text
  What It Is

  ↓

  Why It Matters

  ↓

  How You Use It

  ↓

  What Can Go Wrong

  ↓

  How You Validate the Result
  ```

---

## 1. What is Burp Proxy?

- Burp Proxy is an intercepting HTTP/S proxy positioned between a client, such as a browser, and a target server.

- It allows testers to:

  * Observe traffic.
  * Intercept requests and responses.
  * Modify messages.
  * Forward or drop traffic.
  * Build HTTP history.
  * Send interesting requests to other Burp tools.

---

## 2. Why is Burp Proxy important during web application testing?

- It provides visibility into the actual communication between the client and server.

- This allows a tester to understand:

  * Endpoints.
  * Parameters.
  * Headers.
  * Cookies.
  * Authentication state.
  * API calls.
  * Application workflows.
  * Server responses.

- It is often the observation layer from which deeper manual testing begins.

---

## 3. Where does Burp Proxy sit in the request flow?

```text
Client
    ↓
Burp Proxy
    ↓
Target Server
    ↓
Burp Proxy
    ↓
Client
```

- Burp can inspect or manipulate traffic as it passes through this path.

---

## 4. What happens when Intercept is on?

- Matching traffic is paused inside Burp before continuing.

- The tester can:

  * Inspect it.
  * Modify it.
  * Forward it.
  * Drop it.

---

## 5. What happens when Intercept is off?

- Traffic normally passes through without being manually paused.

- Burp can still record that traffic in HTTP history.

- Therefore:

  ```text
  Intercept Off

  ≠

  Proxy Disabled
  ```

---

## 6. Why do testers often keep Intercept off during normal browsing?

- Constant interception can interrupt application navigation and create unnecessary friction.

- A common workflow is:

  ```text
  Browse with Intercept Off
      ↓
  Review HTTP History
      ↓
  Identify Interesting Request
      ↓
  Send to Repeater
      ↓
  Perform Controlled Testing
  ```

---

## 7. What is the difference between Forward and Drop?

- **Forward** allows an intercepted message to continue.

- **Drop** prevents that intercepted message from continuing through the normal request flow.

- These controls are useful for understanding how application behavior changes when specific traffic is allowed or suppressed.

---

## 8. What is HTTP history?

- HTTP history is Burp Proxy's record of HTTP requests and responses that pass through the proxy.

- It helps testers:

  * Reconstruct workflows.
  * Identify endpoints.
  * Review parameters.
  * Analyze authentication state.
  * Find API traffic.
  * Locate interesting requests for deeper testing.

---

## 9. Does Intercept need to be on for HTTP history to record traffic?

- No.

- Traffic can still appear in HTTP history while Intercept is off, provided it passes through Burp and is not hidden by relevant configuration or filters.

---

## 10. What information do you review in HTTP history?

- Common fields include:

  * Host.
  * Method.
  * URL.
  * Status code.
  * MIME type.
  * Response length.
  * Parameters.
  * Cookies.
  * Authentication headers.
  * Notes or highlights.

- The raw request and response should also be reviewed when necessary.

---

## 11. How do you identify interesting requests in HTTP history?

- Prioritize requests involving:

  * Authentication.
  * Authorization.
  * State changes.
  * Sensitive objects.
  * User-controlled parameters.
  * Administrative functionality.
  * APIs.
  * File operations.
  * Account settings.
  * Business-critical workflows.

---

## 12. Why are Proxy filters useful?

- Real applications generate large amounts of traffic.

- Filters help reduce noise and focus analysis on relevant requests.

- However, overly restrictive filters can hide useful traffic, so testers should understand what is being excluded.

---

## 13. What is the difference between scope and filtering?

- **Scope** defines which targets are considered part of the authorized assessment context.

- **Filtering** controls which traffic is displayed or emphasized.

- A filter changes visibility.

- It does not itself grant authorization.

---

## 14. Why should scope be configured carefully?

- Scope helps:

  * Reduce accidental interaction with unrelated systems.
  * Organize traffic.
  * Focus testing.
  * Support safer workflows.

- Authorization must still come from the engagement rules or program scope.

---

## 15. What is a Proxy listener?

- A Proxy listener is the local interface and port where Burp accepts proxied client traffic.

- A common local configuration is:

  ```text
  127.0.0.1:8080
  ```

- The browser or other client must be configured to route traffic through the listener.

---

## 16. What would you check if no browser traffic appears in Burp?

- Check systematically:

  ```text
  Browser Proxy Configuration
      ↓
  Burp Proxy Listener
      ↓
  Correct Address and Port
      ↓
  HTTP History Filters
      ↓
  Scope-Related Display Settings
      ↓
  HTTPS Certificate Trust
      ↓
  VPN / Upstream Proxy / Network Interference
  ```

- Avoid changing multiple settings at once.

---

## 17. Why is Burp's CA certificate needed for HTTPS interception?

- HTTPS normally creates an encrypted connection between the client and server.

- For Burp to inspect HTTPS traffic, the testing client must trust Burp's locally generated interception certificates.

- Installing Burp's CA certificate in the dedicated testing browser allows Burp to inspect authorized HTTPS traffic without constant certificate warnings.

---

## 18. What is a common symptom of incorrect HTTPS proxy configuration?

- Possible symptoms include:

  * Certificate warnings.
  * TLS errors.
  * Pages failing to load.
  * HTTPS traffic missing from history.
  * HTTP working while HTTPS fails.

- The exact symptom depends on the configuration problem.

---

## 19. Why should Burp's CA certificate be trusted only in a dedicated testing environment?

- Trusting an interception CA gives it significant ability to participate in TLS interception.

- Limiting that trust to a controlled testing environment reduces unnecessary security exposure.

---

## 20. Can Burp Proxy modify requests?

- Yes.

- Intercepted requests can be edited before forwarding.

- Testers may modify:

  * Parameters.
  * Headers.
  * Cookies.
  * Body data.
  * Methods.
  * Paths.

- Changes should be controlled and authorized.

---

## 21. Why should you change one variable at a time?

- It improves causal reasoning.

- If several values change simultaneously, it becomes difficult to determine which change caused the observed behavior.

- A disciplined workflow is:

  ```text
  Baseline
      ↓
  One Controlled Change
      ↓
  Compare
      ↓
  Verify
  ```

---

## 22. Why is establishing a baseline important?

- A baseline shows legitimate expected behavior before testing modifications.

- Without a baseline, normal application variation can be mistaken for security-relevant behavior.

- Baselines should capture:

  * Request.
  * Response.
  * Identity.
  * Role.
  * State.
  * Expected result.

---

## 23. Can Burp Proxy modify responses?

- Yes, responses can be intercepted or transformed when appropriately configured.

- Response modification can help investigate:

  * Client-side assumptions.
  * Hidden UI behavior.
  * Feature flags.
  * Browser-side validation.

- A client-side change does not prove a server-side vulnerability.

---

## 24. Why does changing a response not necessarily prove a vulnerability?

- Response modification changes what the client receives.

- It does not automatically change server-side authorization or application state.

- Security impact must be validated against the server's actual enforcement.

---

## 25. What is Match and Replace?

- Match and Replace is a Proxy feature that can automatically transform matching requests or responses as they pass through Burp.

- It can be used for repeatable modifications such as:

  * Adding or changing headers.
  * Replacing specific values.
  * Supporting controlled testing workflows.

---

## 26. What are the risks of broad Match and Replace rules?

- Broad rules can:

  * Modify unintended traffic.
  * Corrupt requests.
  * Break workflows.
  * Produce misleading observations.
  * Affect unrelated hosts.

- Rules should be narrow, documented, tested, and disabled when no longer needed.

---

## 27. How would you troubleshoot a Match and Replace rule that is not working?

- Verify:

  ```text
  Rule Enabled?
      ↓
  Correct Rule Type?
      ↓
  Correct Match Condition?
      ↓
  Correct Replacement?
      ↓
  Correct Scope?
      ↓
  Traffic Actually Passing Through Proxy?
      ↓
  Conflicting Rules?
  ```

- Compare baseline and transformed traffic directly.

---

## 28. What is WebSocket history?

- WebSocket history records WebSocket messages observed by Burp.

- It can help analyze:

  * Real-time messaging.
  * Notifications.
  * Live updates.
  * Subscriptions.
  * Authentication behavior.
  * Message-level authorization.

---

## 29. How are WebSockets different from normal HTTP request-response traffic?

- Traditional HTTP usually follows a request-response model.

- WebSockets establish a persistent bidirectional communication channel after an initial HTTP upgrade handshake.

- After the connection is established, either side can send messages without creating a new HTTP request for each message.

---

## 30. What should you inspect in WebSocket traffic?

- Review:

  * Handshake context.
  * Authentication state.
  * Message structure.
  * Object identifiers.
  * User-controlled fields.
  * Subscription identifiers.
  * Server responses.
  * Authorization boundaries.

---

## 31. How do Proxy and Repeater work together?

- Proxy is commonly used to observe and capture real application traffic.

- Repeater is used to replay and modify selected requests manually.

- Typical workflow:

  ```text
  Proxy
      ↓
  HTTP History
      ↓
  Interesting Request
      ↓
  Repeater
      ↓
  Baseline
      ↓
  Controlled Modification
      ↓
  Comparison
  ```

---

## 32. Why not perform every test directly in Intercept?

- Intercept is useful for live traffic manipulation, but repeated manual testing is often easier in Repeater.

- Repeater provides:

  * Controlled replay.
  * Easier iteration.
  * Better comparison.
  * Less disruption to normal browsing.

---

## 33. How would you investigate an unexpected response difference?

- Do not immediately classify it as a vulnerability.

- Use:

  ```text
  Reproduce Baseline
      ↓
  Repeat Modified Request
      ↓
  Change One Variable
      ↓
  Compare Responses
      ↓
  Verify Authentication Context
      ↓
  Verify Final Application State
      ↓
  Eliminate Alternative Explanations
      ↓
  Assess Security Boundary and Impact
  ```

---

## 34. Why is response length useful but insufficient?

- Response length can reveal that two responses differ.

- However, differences may result from:

  * Timestamps.
  * Tokens.
  * Dynamic content.
  * Personalization.
  * Error identifiers.

- The actual response content and resulting application state must be analyzed.

---

## 35. Why can status codes be misleading?

- Applications do not always use HTTP status codes consistently.

- A `200 OK` does not prove an operation succeeded securely.

- A `403 Forbidden` does not always prove that no side effect occurred.

- Final application state should be verified for important tests.

---

## 36. What does “response does not equal state” mean?

```text
Response Message

≠

Guaranteed Final Application State
```

- A tester should independently verify whether the intended or unintended state change actually occurred.

---

## 37. How would you use Proxy during authentication testing?

- Observe:

  * Login requests.
  * Session creation.
  * Cookies.
  * Tokens.
  * Redirects.
  * Logout behavior.
  * Session invalidation.
  * Authentication transitions.

- Preserve clear identity and session context throughout testing.

---

## 38. How would you use Proxy during authorization testing?

- Proxy helps capture legitimate requests made by controlled users.

- A disciplined workflow is:

  ```text
  Establish User A Baseline
      ↓
  Identify Object or Action
      ↓
  Preserve Authentication Context
      ↓
  Perform Authorized Controlled Test
      ↓
  Compare Behavior
      ↓
  Verify Final State
      ↓
  Reproduce
  ```

- The goal is to test a specific authorization invariant, not randomly modify identifiers.

---

## 39. Why should multiple test accounts be clearly separated?

- Mixing sessions can invalidate conclusions.

- Evidence should clearly identify:

  * User.
  * Role.
  * Tenant.
  * Session.
  * Object ownership.

- Otherwise, apparent authorization issues may simply result from session confusion.

---

## 40. How can Proxy help identify API endpoints?

- Modern browser applications frequently communicate with backend APIs.

- HTTP history can reveal:

  * API paths.
  * Methods.
  * JSON bodies.
  * Query parameters.
  * Authentication headers.
  * Object identifiers.
  * Versioned routes.

---

## 41. What types of requests deserve priority during manual review?

- High-value requests often include:

  * Login.
  * Password reset.
  * Account changes.
  * Role changes.
  * Object access.
  * Financial or transactional actions.
  * File operations.
  * Administrative actions.
  * API mutations.
  * Security settings.

---

## 42. What is the difference between observing traffic and validating a vulnerability?

- Observation identifies potentially interesting behavior.

- Validation requires evidence that a security property is actually violated.

- Strong validation requires:

  ```text
  Reproducibility

  +

  Security Boundary Violation

  +

  Meaningful Impact
  ```

---

## 43. What is a security invariant?

- A security invariant is a rule that should always remain true.

- Example:

  ```text
  A normal user must not be able to perform an administrator-only action.
  ```

- Proxy helps capture the real requests needed to test such assumptions systematically.

---

## 44. How do you avoid random testing in Proxy?

- Follow a structured process:

  ```text
  Understand Feature
      ↓
  Observe Traffic
      ↓
  Identify Security Boundary
      ↓
  Establish Baseline
      ↓
  Form Hypothesis
      ↓
  Change One Variable
      ↓
  Compare
      ↓
  Verify
      ↓
  Document
  ```

---

## 45. What evidence should you preserve from Proxy?

- Depending on the finding:

  * Baseline request.
  * Baseline response.
  * Modified request.
  * Modified response.
  * Authentication context.
  * Role.
  * Object ownership.
  * Relevant timestamps.
  * Final-state verification.
  * Reproduction steps.

---

## 46. What are common mistakes when using Burp Proxy?

- Common mistakes include:

  * Leaving Intercept on unintentionally.
  * Ignoring HTTP history.
  * Testing outside authorized scope.
  * Using overly broad filters.
  * Modifying several variables simultaneously.
  * Mixing user sessions.
  * Using broad Match and Replace rules.
  * Trusting response messages without verifying state.
  * Treating every anomaly as a vulnerability.

---

## 47. How would you explain a professional Proxy workflow in an interview?

- A strong answer:

  ```text
  Confirm Authorization and Scope
      ↓
  Configure Proxy
      ↓
  Browse Normally
      ↓
  Build HTTP History
      ↓
  Map Important Requests
      ↓
  Establish Baselines
      ↓
  Send Selected Requests to Repeater
      ↓
  Test One Hypothesis at a Time
      ↓
  Compare Behavior
      ↓
  Verify Final State
      ↓
  Reproduce
      ↓
  Document Evidence
  ```

---

## 48. Scenario: A page stops loading when Burp is enabled. What do you check?

- Check:

  * Whether Intercept is holding a request.
  * Proxy listener state.
  * Browser proxy configuration.
  * HTTPS certificate trust.
  * HTTP history for failed requests.
  * Redirect loops.
  * Match and Replace rules.
  * Upstream proxies or VPNs.

- Start with the simplest observable cause and change one configuration item at a time.

---

## 49. Scenario: The browser works, but HTTP history appears empty. What could cause this?

- Possible causes include:

  * Browser traffic bypassing Burp.
  * Wrong proxy address or port.
  * Listener misconfiguration.
  * Filters hiding traffic.
  * Scope-related display settings.
  * Another browser profile using different network settings.

- Verify the complete traffic path rather than assuming Burp is broken.

---

## 50. Scenario: You receive `200 OK` after modifying an object identifier. Is that an IDOR?

- Not necessarily.

- You must determine:

  * Which user made the request.
  * Who owns the object.
  * Whether unauthorized data was exposed or modified.
  * Whether the result is reproducible.
  * Whether final application state confirms the behavior.

- A status code alone is insufficient.

---

## 51. Scenario: A modified response reveals an admin button. Is that a vulnerability?

- Not by itself.

- A hidden or revealed client-side control does not prove that the server permits the privileged action.

- The server-side authorization boundary must be tested safely and explicitly.

---

## 52. Scenario: Two responses have different lengths. What do you do next?

- Inspect the actual differences.

- Determine whether the variation is caused by:

  * Dynamic tokens.
  * Timestamps.
  * User-specific data.
  * Error messages.
  * Authorization behavior.
  * Application state.

- Do not infer impact from length alone.

---

## 53. Scenario: A Match and Replace rule breaks the application. What do you do?

- Disable the rule.

- Re-establish the baseline.

- Review:

  * Rule type.
  * Match condition.
  * Replacement.
  * Scope.
  * Unintended matches.

- Narrow the rule and test again in a controlled environment.

---

## 54. Scenario: You need to test the same request repeatedly. Should you keep intercepting it?

- Usually, capture a legitimate request through Proxy and send it to Repeater.

- This creates a more controlled environment for repeated manual testing while allowing normal browser traffic to continue.

---

## 55. Scenario: You suspect a state-changing action succeeded despite an error response. What should you do?

- Verify the final application state independently.

- Check whether:

  * Data changed.
  * An object was created or deleted.
  * Permissions changed.
  * A transaction occurred.

- Response text alone should not determine the conclusion.

---

## 56. Scenario: You are testing with User A and User B. How do you avoid session confusion?

- Use clearly separated browser sessions or controlled session handling.

- Label evidence with:

  ```text
  User
  Role
  Tenant
  Session
  Object Ownership
  ```

- Reconfirm identity before critical tests.

---

## 57. Scenario: You discover an interesting request outside scope. What should you do?

- Do not test it merely because Burp captured it.

- Confirm the authorized scope and rules of engagement.

- Discovery does not equal authorization.

---

## 58. What is the most important mindset when using Proxy?

- Proxy is not merely a tool for stopping requests.

- It is a visibility and investigation layer.

- The goal is to understand:

  ```text
  What the Application Does

  What the Client Controls

  What the Server Must Enforce

  What Security Boundary Exists

  How That Boundary Can Be Tested Safely

  What Evidence Proves the Result
  ```

---

## Rapid-Fire Questions

1. What default port is commonly used by Burp Proxy?

   - `8080`.

2. Does Intercept off disable HTTP history?

   - No.

3. What does Forward do?

   - Allows an intercepted message to continue.

4. What does Drop do?

   - Prevents the intercepted message from continuing normally.

5. What tool is commonly used after finding an interesting request in Proxy?

   - Repeater.

6. What should you establish before modifying a request?

   - A baseline.

7. How many meaningful variables should you ideally change per controlled test?

   - One.

8. Does a `200 OK` prove authorization?

   - No.

9. Does revealing a hidden UI element prove a server-side vulnerability?

   - No.

10. Should you verify final state after important state-changing tests?

    - Yes.

11. Can Proxy observe WebSocket traffic?

    - Yes.

12. What feature automates repeatable traffic transformations?

    - Match and Replace.

13. Should Match and Replace rules be broad by default?

    - No.

14. Does captured traffic automatically mean the target is authorized for testing?

    - No.

15. What should come before exploitation attempts?

    - Understanding, mapping, baselining, and forming a testable hypothesis.

---

## Interview Answer Framework

- For scenario-based questions, structure answers as:

  ```text
  1. Confirm Scope and Authorization

  2. Establish the Legitimate Baseline

  3. Identify the Security Boundary

  4. Form One Testable Hypothesis

  5. Change One Variable

  6. Compare the Result

  7. Verify Final State

  8. Reproduce

  9. Validate Impact

  10. Document Evidence
  ```

---

## Self-Test

- You should be able to explain without notes:

  * What Burp Proxy does.
  * How interception works.
  * Why Intercept off does not disable Proxy.
  * How HTTP history supports testing.
  * How to configure and troubleshoot a listener.
  * Why HTTPS certificate trust matters.
  * How to modify requests safely.
  * Why baselines matter.
  * How Proxy integrates with Repeater.
  * What Match and Replace does.
  * How WebSocket history differs from HTTP history.
  * Why response differences are not automatically vulnerabilities.
  * Why final-state verification matters.
  * How to preserve authentication context.
  * How to explain a professional Proxy workflow.

---

## Key Takeaways

- Strong interview answers should demonstrate methodology, not only tool knowledge.

- A professional Proxy explanation connects:

  ```text
  Traffic Visibility

  +

  Application Understanding

  +

  Controlled Testing

  +

  Security Boundary Analysis

  +

  Evidence Validation
  ```

- The strongest answers explain both **how** Burp Proxy is used and **why** each testing step matters.
