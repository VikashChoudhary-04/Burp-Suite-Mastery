# Practical Exercises

> **Module 05 — Proxy**

---

## Overview

- This lesson provides hands-on exercises for practicing Burp Proxy in an authorized lab environment.

- The exercises reinforce:

  * Proxy interception.
  * HTTP history analysis.
  * Request and response inspection.
  * Scope awareness.
  * Request modification.
  * WebSocket observation.
  * Match and Replace.
  * Proxy-to-Repeater workflows.
  * Troubleshooting.
  * Evidence-driven testing.

- Use only:

  * Local labs.
  * Deliberately vulnerable applications.
  * Systems you own.
  * Targets where you have explicit authorization.

---

## Exercise 1 — Verify Proxy Configuration

### Objective

- Confirm that browser traffic is correctly routed through Burp Proxy.

### Tasks

1. Start Burp Suite.

2. Confirm that a Proxy listener is running.

3. Verify the listener address and port.

4. Configure your testing browser to use the listener.

5. Browse to an authorized test page.

6. Open:

   ```text
   Proxy
       ↓
   HTTP history
   ```

7. Confirm that the browser request appears.

### Evidence

- Record:

  * Listener address.
  * Listener port.
  * Requested host.
  * HTTP method.
  * Response status.

### Success Criteria

- Browser traffic passes through Burp and appears in HTTP history.

---

## Exercise 2 — Practice Intercept On and Off

### Objective

- Understand the operational difference between intercepted and pass-through traffic.

### Tasks

1. Turn Intercept on.

2. Request a page in your authorized lab.

3. Observe the paused request.

4. Identify:

   * Method.
   * Host.
   * Path.
   * Headers.
   * Cookies, if present.

5. Forward the request.

6. Turn Intercept off.

7. Browse several additional pages.

8. Confirm that requests continue to appear in HTTP history.

### Questions

- What changed when Intercept was turned off?

- Did HTTP history stop recording traffic?

- Which mode was easier for normal browsing?

### Success Criteria

- You can clearly explain:

  ```text
  Intercept Off

  ≠

  Proxy Disabled
  ```

---

## Exercise 3 — Forward and Drop

### Objective

- Practice controlling intercepted traffic.

### Tasks

1. Turn Intercept on.

2. Trigger a harmless request in your lab.

3. Forward the request.

4. Observe the result.

5. Trigger another harmless request.

6. Drop the second request.

7. Observe the difference in browser behavior.

### Evidence

- Record:

  * Request that was forwarded.
  * Request that was dropped.
  * Result observed for each action.

### Success Criteria

- You understand how Forward and Drop affect request flow.

---

## Exercise 4 — Analyze HTTP History

### Objective

- Build a structured traffic-review workflow.

### Tasks

1. Turn Intercept off.

2. Browse several features of an authorized application.

3. Open HTTP history.

4. Identify examples of:

   * `GET`.
   * `POST`, if available.
   * Query parameters.
   * Cookies.
   * Redirects.
   * JSON responses.
   * Static resources.

5. Select five meaningful requests.

6. For each request, record:

   ```text
   Host:
   Method:
   Path:
   Parameters:
   Authentication Context:
   Status:
   Content Type:
   Why Interesting:
   ```

### Success Criteria

- You can distinguish meaningful application requests from routine traffic.

---

## Exercise 5 — Filter Proxy Traffic

### Objective

- Reduce noise and focus on relevant traffic.

### Tasks

1. Generate normal application traffic.

2. Review HTTP history without filters.

3. Apply filters appropriate to your lab.

4. Compare the traffic before and after filtering.

5. Focus on:

   * In-scope hosts.
   * Dynamic requests.
   * Relevant MIME types.
   * Interesting methods.

### Questions

- Which traffic created the most noise?

- Which filters improved your workflow?

- Did any useful requests disappear because of an overly restrictive filter?

### Success Criteria

- You can use filtering without accidentally hiding important traffic.

---

## Exercise 6 — Establish a Baseline Request

### Objective

- Practice evidence-driven testing before modification.

### Tasks

1. Choose a harmless request from HTTP history.

2. Document the unchanged request.

3. Record:

   * Method.
   * Path.
   * Parameters.
   * Cookies or authorization context.
   * Response status.
   * Response length.
   * Relevant response content.

4. Repeat the same legitimate action.

5. Compare the results.

### Questions

- Is the response deterministic?

- Which values change between requests?

- Are there timestamps, nonces, tokens, or dynamic identifiers?

### Success Criteria

- You have a reliable baseline before performing controlled modifications.

---

## Exercise 7 — Modify One Request Value

### Objective

- Practice controlled request modification.

### Tasks

1. Use a harmless lab request with a user-controlled parameter.

2. Preserve the original request as your baseline.

3. Change exactly one non-destructive value.

4. Forward or replay the modified request.

5. Compare:

   * Status code.
   * Response length.
   * Response body.
   * Application behavior.

6. Restore the original value.

### Evidence Table

| Observation | Baseline | Modified |
| --- | --- | --- |
| Method | | |
| Changed value | None | |
| Status | | |
| Length | | |
| Behavior | | |

### Success Criteria

- Only one meaningful variable changed between baseline and test.

---

## Exercise 8 — Inspect Request Headers

### Objective

- Understand the role of HTTP request headers.

### Tasks

1. Select a request from HTTP history.

2. Identify headers such as:

   * `Host`.
   * `User-Agent`.
   * `Accept`.
   * `Content-Type`.
   * `Origin`.
   * `Referer`.
   * `Cookie`.
   * `Authorization`, if present.

3. Categorize each header:

   ```text
   Routing
   Client Metadata
   Content Negotiation
   Authentication / Session
   Browser Context
   Application-Specific
   ```

4. Identify which values are security-sensitive.

### Success Criteria

- You can explain the purpose of important request headers instead of treating them as arbitrary text.

---

## Exercise 9 — Inspect Response Headers

### Objective

- Practice structured response analysis.

### Tasks

1. Select several responses.

2. Identify:

   * Status code.
   * `Content-Type`.
   * `Set-Cookie`.
   * `Location`.
   * Cache-related headers.
   * Security headers.

3. Note any differences between:

   * Public pages.
   * Authenticated pages.
   * Redirect responses.
   * Error responses.

### Questions

- Which responses set or modify session state?

- Which responses redirect the browser?

- Which security headers are present?

### Success Criteria

- You can extract security-relevant information from response headers.

---

## Exercise 10 — Trace a Redirect Chain

### Objective

- Understand multi-request navigation and authentication behavior.

### Tasks

1. Identify a harmless workflow that produces a redirect.

2. Use HTTP history to reconstruct the sequence.

3. Record:

   ```text
   Request 1
       ↓
   Response Status
       ↓
   Location
       ↓
   Request 2
       ↓
   Final Response
   ```

4. Note any cookies or state changes between requests.

### Success Criteria

- You can explain the complete redirect chain rather than only the final page.

---

## Exercise 11 — Identify Authentication State

### Objective

- Learn how session state appears in proxied traffic.

### Tasks

1. Capture traffic while unauthenticated.

2. Authenticate to an authorized lab account.

3. Capture traffic while authenticated.

4. Compare:

   * Cookies.
   * Authorization headers.
   * Redirect behavior.
   * Accessible endpoints.
   * Response content.

5. Log out.

6. Observe what changes.

### Success Criteria

- You can identify how the application represents authentication state.

---

## Exercise 12 — Compare Two Controlled Sessions

### Objective

- Practice maintaining clear identity context.

### Requirements

- Use two accounts you are authorized to control.

### Tasks

1. Log in as User A.

2. Capture a harmless authenticated request.

3. Log in separately as User B.

4. Capture the equivalent request.

5. Compare:

   * Session cookies.
   * Tokens.
   * User identifiers.
   * Object identifiers.
   * Response behavior.

6. Label all evidence clearly.

### Success Criteria

- You can distinguish the two sessions without mixing authentication context.

---

## Exercise 13 — Send a Request from Proxy to Repeater

### Objective

- Practice the standard Proxy-to-Repeater workflow.

### Tasks

1. Browse normally with Intercept off.

2. Find an interesting but safe request in HTTP history.

3. Send it to Repeater.

4. Send the unchanged request.

5. Confirm the baseline response.

6. Modify one harmless input.

7. Send again.

8. Compare the results.

### Workflow

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

### Success Criteria

- You can move efficiently from passive observation to controlled manual testing.

---

## Exercise 14 — Verify Final Application State

### Objective

- Learn why response text alone is insufficient.

### Tasks

1. Choose a harmless state-changing action in your lab.

2. Capture the legitimate request.

3. Record the response.

4. Independently verify the resulting application state.

5. Compare:

   ```text
   Response Claimed Result

   vs.

   Actual Application State
   ```

### Questions

- Did the response accurately represent the final state?

- How would you verify the result if the response were ambiguous?

### Success Criteria

- You do not treat status codes or response messages as definitive proof of state.

---

## Exercise 15 — Review WebSocket Traffic

### Objective

- Identify and inspect WebSocket communication.

### Requirements

- Use an authorized application or lab that supports WebSockets.

### Tasks

1. Trigger a real-time application feature.

2. Open WebSocket history.

3. Identify:

   * Handshake-related traffic.
   * Client-to-server messages.
   * Server-to-client messages.
   * Message structure.
   * Identifiers.
   * Authentication context, if visible.

4. Correlate a browser action with the corresponding WebSocket message.

### Success Criteria

- You can distinguish WebSocket traffic from ordinary HTTP request-response traffic.

---

## Exercise 16 — Create a Safe Match and Replace Rule

### Objective

- Practice controlled Proxy automation.

### Tasks

1. Choose a harmless header in a local or authorized lab.

2. Create a narrowly scoped Match and Replace rule.

3. Trigger matching traffic.

4. Verify that the intended transformation occurs.

5. Confirm that unrelated traffic is not modified.

6. Disable the rule.

7. Repeat the baseline request.

### Evidence

- Record:

  * Rule purpose.
  * Match condition.
  * Replacement.
  * Scope.
  * Before state.
  * After state.

### Success Criteria

- The rule performs one predictable transformation without unintended side effects.

---

## Exercise 17 — Troubleshoot a Match and Replace Rule

### Objective

- Practice diagnosing Proxy automation problems.

### Tasks

1. Use a safe test rule.

2. Intentionally create a harmless mismatch, such as an incorrect match condition.

3. Observe that the rule does not apply.

4. Troubleshoot:

   ```text
   Enabled?
       ↓
   Correct Rule Type?
       ↓
   Correct Match?
       ↓
   Correct Scope?
       ↓
   Correct Traffic Path?
       ↓
   Conflicting Rule?
   ```

5. Correct the configuration.

6. Verify expected behavior.

### Success Criteria

- You can troubleshoot systematically instead of changing multiple settings randomly.

---

## Exercise 18 — Troubleshoot Missing Proxy Traffic

### Objective

- Build a repeatable diagnostic workflow.

### Scenario

- Assume the browser loads pages but expected traffic does not appear in Burp.

### Tasks

1. Check browser proxy settings.

2. Check the Burp listener.

3. Verify address and port.

4. Review HTTP history filters.

5. Review scope-related display settings.

6. Check whether another proxy or VPN affects traffic.

7. For HTTPS problems, verify certificate trust.

### Troubleshooting Record

```text
Symptom:
Expected:
Observed:
Browser Proxy:
Burp Listener:
Filters:
TLS / Certificate:
VPN / Upstream Proxy:
Root Cause:
Fix:
Verification:
```

### Success Criteria

- You can isolate the problem through evidence rather than guesswork.

---

## Exercise 19 — Build a Proxy Investigation Journal

### Objective

- Practice professional documentation.

### Template

```text
Feature:
Date / Time:
User / Role:
Scope:
Request:
Baseline Behavior:
Hypothesis:
Controlled Change:
Response Difference:
Final-State Verification:
Reproduction Result:
Security Boundary:
Impact:
Conclusion:
Evidence Saved:
```

### Tasks

1. Select one harmless application feature.

2. Complete the journal from beginning to end.

3. Do not label anything a vulnerability unless the evidence proves a security boundary violation.

### Success Criteria

- Another tester could understand and reproduce your reasoning from the notes.

---

## Exercise 20 — Full Proxy Workflow Challenge

### Objective

- Combine the complete Module 05 workflow.

### Scenario

- Use an authorized lab application with multiple pages or features.

### Phase 1 — Prepare

1. Confirm authorization.

2. Define scope.

3. Verify Proxy listener configuration.

4. Confirm HTTPS interception if required.

### Phase 2 — Observe

1. Browse normally.

2. Keep Intercept off unless needed.

3. Build HTTP history.

4. Identify meaningful requests.

5. Filter irrelevant noise.

### Phase 3 — Map

- Identify:

  * Authentication requests.
  * Session-related traffic.
  * State-changing actions.
  * API requests.
  * Object identifiers.
  * Redirects.
  * WebSocket traffic, if present.

### Phase 4 — Test

1. Choose one harmless request.

2. Establish a baseline.

3. Send it to Repeater.

4. Form a testable hypothesis.

5. Change one variable.

6. Compare behavior.

7. Verify final state.

### Phase 5 — Validate

1. Repeat the result.

2. Restore the baseline.

3. Eliminate alternative explanations.

4. Confirm whether a security boundary was actually violated.

### Phase 6 — Document

- Preserve:

  * Baseline request.
  * Baseline response.
  * Modified request.
  * Modified response.
  * Session context.
  * Final-state evidence.
  * Reproduction steps.
  * Conclusion.

### Success Criteria

- You complete the workflow without random testing.

---

## Capstone Exercise — Proxy-Driven Feature Investigation

### Objective

- Perform a structured investigation using Proxy as the observation layer.

### Rules

- Use only an authorized lab.

- Do not begin with payloads.

- Do not modify multiple variables simultaneously.

- Do not claim a vulnerability from a response difference alone.

### Step 1 — Select a Feature

- Choose one feature such as:

  * Profile update.
  * Search.
  * Cart operation.
  * Preference change.
  * Account setting.
  * Harmless API-backed feature.

### Step 2 — Understand Normal Behavior

- Document:

  * User action.
  * Request sequence.
  * Relevant parameters.
  * Authentication context.
  * Response behavior.
  * Final state.

### Step 3 — Identify Security Boundaries

- Ask:

  ```text
  What Does the Client Control?

  What Must the Server Enforce?

  What Should Never Be Possible?
  ```

### Step 4 — Form a Hypothesis

- Write one clear statement:

  ```text
  If I change ______ while keeping ______ constant,
  the server should ______ because ______.
  ```

### Step 5 — Test Safely

1. Preserve the baseline.

2. Change one variable.

3. Send the request.

4. Compare the response.

5. Verify final state.

### Step 6 — Reproduce

- Repeat the exact test.

- Confirm whether the behavior is stable.

### Step 7 — Conclude

- Classify the result as:

  ```text
  Expected Behavior

  Interesting Observation

  Requires More Testing

  Validated Security Issue
  ```

### Step 8 — Produce Evidence

- Your final notes should contain:

  * Feature description.
  * Scope.
  * Identity and role.
  * Baseline request and response.
  * Hypothesis.
  * Modified request and response.
  * Final-state verification.
  * Reproduction result.
  * Security impact, if any.
  * Final conclusion.

---

## Self-Assessment Checklist

- You should now be able to:

  * Configure Burp Proxy.
  * Verify browser traffic.
  * Use Intercept deliberately.
  * Forward and drop requests.
  * Analyze HTTP history.
  * Filter traffic.
  * Inspect request headers.
  * Inspect response headers.
  * Trace redirects.
  * Identify authentication state.
  * Separate controlled user sessions.
  * Establish baselines.
  * Change one variable at a time.
  * Send requests to Repeater.
  * Verify final application state.
  * Review WebSocket traffic.
  * Use Match and Replace safely.
  * Troubleshoot Proxy issues.
  * Maintain investigation notes.
  * Preserve evidence.
  * Distinguish observations from validated vulnerabilities.

---

## Final Practice Workflow

```text
Confirm Authorization
    ↓
Configure Proxy
    ↓
Browse Normally
    ↓
Review HTTP History
    ↓
Filter Noise
    ↓
Identify Important Request
    ↓
Establish Baseline
    ↓
Form Hypothesis
    ↓
Send to Repeater
    ↓
Change One Variable
    ↓
Compare
    ↓
Verify Final State
    ↓
Reproduce
    ↓
Validate Impact
    ↓
Document Evidence
```

---

## Key Takeaways

- Proxy practice should develop investigation discipline, not just familiarity with interception controls.

- The strongest workflow is:

  ```text
  Observe

  ↓

  Understand

  ↓

  Baseline

  ↓

  Hypothesize

  ↓

  Test

  ↓

  Compare

  ↓

  Verify

  ↓

  Reproduce

  ↓

  Document
  ```

- Use Burp Proxy as the foundation for understanding application behavior before deeper security testing.
