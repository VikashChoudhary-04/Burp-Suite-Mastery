# Quick Revision

> **Module 05 — Proxy**

---

## Purpose

- Use this file for rapid revision of the Burp Proxy module.

- Focus on:

  * Core concepts.
  * Essential workflows.
  * Troubleshooting logic.
  * Professional testing principles.
  * Interview-ready recall.

---

## Proxy in One Sentence

- Burp Proxy is an intercepting proxy that sits between the client and target server, allowing authorized testers to observe, intercept, modify, forward, drop, and analyze application traffic.

---

## Core Traffic Model

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

---

## Core Proxy Areas

```text
Intercept

HTTP History

WebSocket History

Proxy Settings
```

---

## Intercept

### Intercept On

```text
Request
    ↓
Paused in Burp
    ↓
Inspect / Modify
    ↓
Forward or Drop
```

- Use when you need direct control over live traffic.

### Intercept Off

```text
Request
    ↓
Passes Through Burp
    ↓
Usually Recorded in HTTP History
    ↓
Server
```

- Important:

  ```text
  Intercept Off

  ≠

  Proxy Disabled
  ```

---

## Forward vs Drop

### Forward

- Allows the intercepted message to continue.

### Drop

- Prevents the intercepted message from continuing normally.

---

## HTTP History

- HTTP history provides a record of proxied HTTP requests and responses.

- Review:

  * Host.
  * Method.
  * URL.
  * Status code.
  * MIME type.
  * Response length.
  * Parameters.
  * Headers.
  * Cookies.
  * Authentication data.

---

## High-Value Requests

- Prioritize traffic involving:

  * Login.
  * Logout.
  * Password reset.
  * Account settings.
  * Authorization-sensitive objects.
  * Administrative actions.
  * APIs.
  * File operations.
  * Transactions.
  * Security settings.
  * State-changing requests.

---

## Scope

- Scope helps define the targets relevant to the assessment.

- Benefits:

  * Cleaner traffic.
  * Better organization.
  * Reduced accidental interaction.
  * More focused testing.

- Remember:

  ```text
  Captured by Burp

  ≠

  Authorized to Test
  ```

---

## Filters

- Filters reduce noise in HTTP history.

- Use them to focus on relevant traffic.

- Be careful:

  ```text
  Overly Restrictive Filter

  ↓

  Useful Traffic Hidden
  ```

---

## Proxy Listener

- A Proxy listener accepts proxied client traffic.

- Common local configuration:

  ```text
  Address: 127.0.0.1
  Port: 8080
  ```

- Browser proxy settings must point to the correct listener.

---

## HTTPS Interception

- HTTPS traffic is encrypted.

- Burp performs authorized TLS interception so traffic can be inspected.

- The dedicated testing browser must trust Burp's CA certificate.

- Trust the certificate only in an appropriate testing environment.

---

## HTTPS Troubleshooting

```text
HTTPS Fails
    ↓
Check Browser Proxy
    ↓
Check Proxy Listener
    ↓
Check Burp CA Certificate
    ↓
Check Certificate Trust
    ↓
Check VPN / Upstream Proxy
    ↓
Check HTTP History
```

---

## No Traffic in Burp

- Check:

  1. Browser proxy configuration.
  2. Correct IP and port.
  3. Burp Proxy listener.
  4. HTTP history filters.
  5. Scope-related display settings.
  6. HTTPS certificate trust.
  7. VPN or upstream proxy interference.

---

## Request Modification

- Common editable request components:

  ```text
  Method

  Path

  Query Parameters

  Headers

  Cookies

  Body Parameters

  JSON

  Authentication Data
  ```

- Professional rule:

  ```text
  Baseline

  ↓

  Change One Variable

  ↓

  Compare

  ↓

  Verify
  ```

---

## Baseline Testing

- Before modification, record legitimate behavior.

- Baseline context should include:

  * User.
  * Role.
  * Tenant.
  * Session.
  * Request.
  * Response.
  * Object ownership.
  * Expected state.

---

## Response Modification

- Responses can be intercepted or transformed for controlled testing.

- Useful for investigating:

  * Client-side assumptions.
  * Hidden interface elements.
  * Feature flags.
  * Browser-side validation.

- Remember:

  ```text
  Client-Side Change

  ≠

  Server-Side Security Failure
  ```

---

## Response vs State

```text
"Success"

≠

State Definitely Changed
```

```text
"Error"

≠

State Definitely Did Not Change
```

- Verify final application state independently.

---

## Status Codes

- Do not rely on status codes alone.

```text
200 OK

≠

Authorized
```

```text
403 Forbidden

≠

Guaranteed No Side Effect
```

- Analyze:

  * Response body.
  * Authentication context.
  * Application state.
  * Reproducibility.
  * Security impact.

---

## Response Length

- Useful as a comparison signal.

- Not proof of a vulnerability.

- Differences may result from:

  * Tokens.
  * Timestamps.
  * Dynamic content.
  * Personalization.
  * Error identifiers.

---

## Proxy → Repeater Workflow

```text
Browse Application
    ↓
HTTP History
    ↓
Identify Interesting Request
    ↓
Send to Repeater
    ↓
Establish Baseline
    ↓
Modify One Variable
    ↓
Compare
    ↓
Verify State
    ↓
Reproduce
    ↓
Document
```

---

## Why Use Repeater After Proxy?

- Proxy captures real traffic.

- Repeater provides a controlled environment for:

  * Replay.
  * Modification.
  * Iteration.
  * Comparison.
  * Reproduction.

---

## Match and Replace

- Automatically transforms matching proxied traffic.

- Possible uses:

  * Add headers.
  * Replace headers.
  * Replace values.
  * Support repeatable testing conditions.

---

## Match and Replace Safety

```text
Narrow Rule

+

Known Scope

+

Known Baseline

+

Verify Transformation
```

- Avoid broad rules that may modify unrelated traffic.

- Disable rules when no longer needed.

---

## Match and Replace Troubleshooting

```text
Rule Enabled?
    ↓
Correct Rule Type?
    ↓
Correct Match?
    ↓
Correct Replacement?
    ↓
Correct Scope?
    ↓
Traffic Passing Through Proxy?
    ↓
Conflicting Rules?
```

---

## WebSocket History

- WebSockets provide persistent bidirectional communication.

- Review:

  * Upgrade handshake.
  * Authentication context.
  * Messages.
  * Object identifiers.
  * User-controlled fields.
  * Subscriptions.
  * Authorization behavior.

---

## HTTP vs WebSocket

### HTTP

```text
Request
    ↓
Response
```

### WebSocket

```text
HTTP Upgrade
    ↓
Persistent Connection
    ↕
Bidirectional Messages
```

---

## Authentication Testing with Proxy

- Observe:

  * Login requests.
  * Session cookies.
  * Tokens.
  * Redirects.
  * Logout.
  * Session invalidation.
  * Authentication transitions.

- Always preserve identity context.

---

## Authorization Testing with Proxy

```text
Controlled User A
    ↓
Legitimate Baseline
    ↓
Identify Object / Action
    ↓
Form Authorization Hypothesis
    ↓
Controlled Modification
    ↓
Compare
    ↓
Verify Final State
    ↓
Reproduce
```

- Clearly track:

  * User.
  * Role.
  * Tenant.
  * Session.
  * Ownership.

---

## API Discovery

- HTTP history can reveal:

  * REST endpoints.
  * API versions.
  * Methods.
  * JSON bodies.
  * Query parameters.
  * Authentication headers.
  * Object identifiers.

---

## Security Invariant

- A security invariant is a rule that should always remain true.

- Example:

  ```text
  Normal User

  Must Not

  Perform Administrator-Only Action
  ```

- Test invariants systematically instead of changing random values.

---

## Investigation Workflow

```text
Understand Feature

↓

Observe Traffic

↓

Map Inputs

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

Verify Final State

↓

Reproduce

↓

Validate Impact

↓

Document Evidence
```

---

## Strong Evidence

- Preserve:

  * Baseline request.
  * Baseline response.
  * Modified request.
  * Modified response.
  * Identity and role.
  * Session context.
  * Object ownership.
  * Final-state verification.
  * Reproduction steps.
  * Impact evidence.

---

## Common Mistakes

```text
Leaving Intercept On Accidentally

Ignoring HTTP History

Testing Outside Scope

Using Overly Broad Filters

Changing Multiple Variables

Mixing User Sessions

Using Broad Match and Replace Rules

Trusting Status Codes Alone

Trusting Response Messages Alone

Treating Every Anomaly as a Vulnerability
```

---

## Troubleshooting Principle

```text
Observe Symptom

↓

Identify Simplest Possible Cause

↓

Change One Setting

↓

Retest

↓

Record Result

↓

Continue Systematically
```

- Do not change multiple configuration settings simultaneously.

---

## Interview Essentials

### What is Burp Proxy?

- An intercepting HTTP/S proxy between the client and server.

### Does Intercept off disable Proxy?

- No.

### What does HTTP history do?

- Records proxied HTTP requests and responses for analysis.

### What does Forward do?

- Allows intercepted traffic to continue.

### What does Drop do?

- Stops the intercepted message from continuing normally.

### What is the common default listener?

```text
127.0.0.1:8080
```

### Why install Burp's CA certificate?

- To allow trusted HTTPS interception in the dedicated testing environment.

### Why establish a baseline?

- To understand legitimate behavior before testing modifications.

### Why change one variable at a time?

- To determine which change caused the observed behavior.

### Does `200 OK` prove authorization?

- No.

### Does revealing hidden UI prove a vulnerability?

- No.

### What should follow an interesting Proxy request?

- Usually controlled testing in Repeater.

### What does Match and Replace do?

- Automatically transforms matching proxied traffic.

### Can Burp inspect WebSocket traffic?

- Yes.

### What must be verified after state-changing tests?

- Final application state.

---

## Professional Proxy Workflow

```text
Confirm Authorization

↓

Define Scope

↓

Configure Proxy

↓

Browse Normally

↓

Build HTTP History

↓

Map Important Traffic

↓

Establish Baselines

↓

Select High-Value Requests

↓

Send to Repeater

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

## Five Rules to Remember

1. **Intercept off does not mean Proxy off.**
2. **Establish a baseline before modification.**
3. **Change one meaningful variable at a time.**
4. **A response alone does not prove final state or impact.**
5. **Captured traffic is not automatically authorized testing scope.**

---

## Final Mental Model

```text
Proxy

=

Visibility

+

Traffic Control

+

Application Mapping

+

Workflow Understanding

+

Evidence Collection
```

- Burp Proxy is most valuable when used as part of a disciplined investigation process rather than simply as a request-interception tool.

---

## Module 05 Completion Check

- You should now be able to:

  * Explain Proxy architecture.
  * Configure a Proxy listener.
  * Intercept and control requests.
  * Use HTTP history effectively.
  * Apply scope and filters.
  * Troubleshoot HTTPS interception.
  * Modify requests safely.
  * Understand response modification limitations.
  * Use Match and Replace carefully.
  * Analyze WebSocket history.
  * Integrate Proxy with Repeater.
  * Establish baselines.
  * Verify final application state.
  * Preserve strong evidence.
  * Explain professional Proxy workflows in interviews.

```text
Module 05 — Proxy

████████████████  COMPLETE
```
