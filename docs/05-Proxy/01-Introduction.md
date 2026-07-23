# Introduction

> **Module 05 — Proxy**

---

## Overview

- Burp Proxy is the central traffic-observation and interception component of Burp Suite.

- It operates between a client, such as a web browser, and the target application.

- This position allows authorized testers to observe how applications communicate and, when necessary, control individual requests and responses before they continue through the normal communication flow.

- Proxy is not only an interception feature.

- It is the foundation for:

  * Understanding application behavior.
  * Capturing requests for deeper testing.
  * Identifying security-relevant inputs.
  * Observing authentication and session state.
  * Mapping application functionality.
  * Investigating HTTP and WebSocket traffic.
  * Sending requests to other Burp Suite tools.
  * Preserving evidence during security assessments.

---

## Learning Objectives

- By the end of this lesson, you will be able to:

  * Explain what Burp Proxy is.
  * Describe where Proxy sits in the client-server communication path.
  * Understand why an intercepting proxy is useful during security testing.
  * Distinguish traffic observation from active interception.
  * Explain the role of HTTP history.
  * Understand how Proxy connects with other Burp Suite tools.
  * Recognize the importance of scope, baselines, and controlled testing.
  * Describe a basic professional Proxy workflow.

---

## What Is Burp Proxy?

- Burp Proxy is an intercepting web proxy.

- Conceptually, it sits between the client and the target server:

  ```text
  Client / Browser
        │
        ▼
    Burp Proxy
        │
        ▼
  Target Server
  ```

- Instead of the browser communicating directly with the application, traffic is routed through Burp.

- This allows Burp to observe the communication.

- Depending on configuration, selected traffic can also be paused before continuing.

---

## Normal Browser Communication

- Without an intercepting proxy, communication usually follows this simplified model:

  ```text
  Browser
      │
      │ Request
      ▼
  Web Server
      │
      │ Response
      ▼
  Browser
  ```

- The browser automatically constructs and sends requests.

- The server processes those requests and returns responses.

- Much of this communication happens without the user seeing the underlying HTTP messages.

---

## Communication Through Burp Proxy

- When the browser is configured to use Burp Proxy, the flow becomes:

  ```text
  Browser
      │
      │ Request
      ▼
  Burp Proxy
      │
      │ Request
      ▼
  Target Server
      │
      │ Response
      ▼
  Burp Proxy
      │
      │ Response
      ▼
  Browser
  ```

- Burp now occupies a strategic observation point.

- This makes it possible to inspect the exact messages exchanged between the client and server.

---

## Why Does an Intercepting Proxy Matter?

- Modern applications make many security-sensitive decisions based on HTTP messages.

- Requests may contain:

  * Authentication cookies.
  * Session tokens.
  * Object identifiers.
  * User IDs.
  * API parameters.
  * Form fields.
  * JSON data.
  * Authorization headers.
  * File uploads.
  * Workflow state.
  * Security-related headers.

- Responses may reveal:

  * Application behavior.
  * Authorization decisions.
  * Error conditions.
  * Object data.
  * Session changes.
  * Redirects.
  * Security headers.
  * API responses.
  * Hidden functionality.

- Observing these messages helps a tester understand what the application actually sends and receives rather than relying only on what the user interface displays.

---

## The Browser Interface Is Only One View

- A web interface does not necessarily reveal every security-relevant value.

- For example, a page may display:

  ```text
  View Order
  ```

- The underlying request might contain:

  ```http
  GET /api/orders/4821 HTTP/1.1
  Host: example.com
  Cookie: session=...
  ```

- The visible interface shows an action.

- Proxy reveals the underlying protocol interaction.

- This distinction is fundamental to web application security testing.

---

## Proxy as an Observation Layer

- One of the most important uses of Proxy is passive observation.

- You do not need to intercept every request.

- During normal browsing, Burp can record proxied traffic in HTTP history while requests continue automatically.

- This supports a workflow such as:

  ```text
  Browse Application

  ↓

  Observe Traffic

  ↓

  Understand Features

  ↓

  Identify Interesting Requests

  ↓

  Investigate Selected Requests
  ```

- This is often more efficient than stopping every request manually.

---

## Proxy as a Control Layer

- When interception is enabled for relevant traffic, Proxy can pause a request before it reaches the server.

- A tester may then:

  * Inspect the request.
  * Modify selected values.
  * Add or remove headers.
  * Change cookies.
  * Forward the request.
  * Drop the request.
  * Send the request to another Burp tool.

- Conceptually:

  ```text
  Browser
      │
      ▼
  Intercepted Request
      │
      ├── Inspect
      ├── Modify
      ├── Forward
      ├── Drop
      └── Send to Another Tool
  ```

- Interception provides control, but it should be used deliberately.

---

## Intercept ON vs Intercept OFF

- A common beginner misconception is that Proxy only works when Intercept is enabled.

- That is incorrect.

### Intercept ON

- Relevant requests may pause inside Burp.

- This is useful when you intentionally need to inspect or modify traffic before it continues.

### Intercept OFF

- Requests normally continue without being manually paused.

- Burp can still record traffic in HTTP history.

- This is often the preferred mode during general application exploration.

- A practical workflow is:

  ```text
  General Browsing
        │
        ▼
  Intercept OFF
        │
        ▼
  Review HTTP History
        │
        ▼
  Select Interesting Request
        │
        ▼
  Investigate Further
  ```

---

## HTTP History as an Investigation Record

- HTTP history records proxied HTTP requests and responses.

- It can help answer questions such as:

  * Which endpoints were requested?
  * Which HTTP methods were used?
  * Which parameters were submitted?
  * Which cookies were present?
  * Which API calls occurred?
  * Which responses were returned?
  * Which requests appear security-sensitive?

- Consider a simple user action:

  ```text
  User clicks "View Profile"
  ```

- The browser might generate:

  ```http
  GET /api/users/1042 HTTP/1.1
  Host: example.com
  Cookie: session=...
  ```

- HTTP history exposes this underlying request for analysis.

---

## Proxy and Application Mapping

- Proxy also contributes to understanding the application's attack surface.

- As traffic passes through Burp, testers may discover:

  * Hosts.
  * Paths.
  * API endpoints.
  * Parameters.
  * JavaScript files.
  * Authentication flows.
  * Administrative functionality.
  * File-handling endpoints.
  * WebSocket connections.

- This traffic can contribute to the Target tool's Site Map and broader application mapping.

- Proxy therefore supports both traffic analysis and attack-surface discovery.

---

## Proxy and Other Burp Tools

- Proxy is frequently the entry point into deeper Burp workflows.

- A common pattern is:

  ```text
  Browser
      │
      ▼
  Proxy
      │
      ▼
  HTTP History
      │
      ├── Repeater
      ├── Intruder
      ├── Comparer
      ├── Scanner
      └── Other Burp Tools
  ```

- Different tools serve different purposes.

- For example:

  * **Repeater** supports controlled manual request modification and replay.
  * **Intruder** supports systematic request variation.
  * **Comparer** helps analyze differences.
  * **Scanner** supports automated vulnerability analysis where available and authorized.
  * **Target** helps organize discovered application content.

- Proxy captures and exposes the traffic that often begins these workflows.

---

## Example: Login Request

- Imagine a user submits a login form.

- The browser may send:

  ```http
  POST /login HTTP/1.1
  Host: example.com
  Content-Type: application/x-www-form-urlencoded

  username=alice&password=Password123
  ```

- Proxy allows the tester to inspect details such as:

  * HTTP method.
  * Request path.
  * Headers.
  * Cookies.
  * Content type.
  * Submitted parameters.

- The tester can then investigate how authentication behaves using an appropriate controlled workflow.

---

## Example: API Request

- A modern application may send:

  ```http
  GET /api/account/profile HTTP/1.1
  Host: example.com
  Authorization: Bearer <token>
  ```

- Proxy makes the API interaction visible even if the browser interface hides these implementation details.

- This may reveal:

  * API paths.
  * Authentication mechanisms.
  * Object identifiers.
  * Request formats.
  * Response structures.
  * Security-relevant headers.

---

## Example: State-Changing Request

- Suppose a user changes an account setting.

- The browser might send:

  ```http
  POST /api/account/settings HTTP/1.1
  Host: example.com
  Content-Type: application/json
  Cookie: session=...

  {
    "notifications": false
  }
  ```

- A professional investigation should not focus only on whether the response says:

  ```text
  Success
  ```

- The tester should also verify the final application state.

- This leads to an important principle:

  ```text
  Response

  ≠

  Guaranteed Final State
  ```

---

## Establish a Baseline First

- Before modifying a request, understand legitimate behavior.

- A useful sequence is:

  ```text
  Perform Legitimate Action

  ↓

  Capture Request

  ↓

  Record Expected Response

  ↓

  Verify Expected State

  ↓

  Establish Baseline

  ↓

  Begin Controlled Testing
  ```

- Without a baseline, unexpected behavior can easily be misinterpreted.

---

## Change One Variable at a Time

- Controlled testing is easier to interpret when only one meaningful variable changes between requests.

- For example:

  ```text
  Baseline
  user_id=1001
  ```

  ```text
  Test
  user_id=1002
  ```

- If several headers, cookies, parameters, and methods are changed simultaneously, it becomes difficult to determine what caused the observed behavior.

- A disciplined workflow is:

  ```text
  Baseline

  ↓

  Change One Variable

  ↓

  Send Request

  ↓

  Compare Result

  ↓

  Verify State

  ↓

  Record Evidence
  ```

---

## Scope Comes Before Testing

- Proxy may observe traffic from many applications and background browser services.

- Seeing traffic in Burp does not mean that traffic is authorized for testing.

- Before performing active testing:

  * Confirm the authorized target.
  * Confirm allowed hosts and applications.
  * Understand exclusions.
  * Understand prohibited techniques.
  * Configure Burp scope appropriately.

- A useful rule is:

  ```text
  Visible in Proxy

  ≠

  Authorized to Test
  ```

---

## Avoid Random Modification

- Randomly changing values may produce unusual responses without providing meaningful security evidence.

- Instead, ask:

  ```text
  What Is This Feature Supposed to Do?

  ↓

  What Does the Client Control?

  ↓

  What Must the Server Enforce?

  ↓

  What Security Boundary Exists?

  ↓

  What Single Change Can Test That Boundary?

  ↓

  What Evidence Would Prove a Failure?
  ```

- Proxy is most effective when used as part of this reasoning process.

---

## Anomaly vs Vulnerability

- An unexpected response is not automatically a vulnerability.

- For example:

  ```text
  Different Status Code
  ```

- Or:

  ```text
  Different Response Length
  ```

- These are observations.

- A validated vulnerability generally requires stronger evidence:

  ```text
  Security Boundary Violation

  +

  Reproducibility

  +

  Meaningful Impact
  ```

- Proxy helps collect evidence, but interpretation still requires careful analysis.

---

## Basic Professional Proxy Workflow

```text
Confirm Authorization

↓

Define Scope

↓

Configure Proxy Correctly

↓

Browse the Application Normally

↓

Observe HTTP History

↓

Understand Application Behavior

↓

Identify Security-Relevant Requests

↓

Establish Legitimate Baselines

↓

Send Selected Requests to Appropriate Tools

↓

Perform Controlled Tests

↓

Compare Results

↓

Verify Final State

↓

Repeat Important Results

↓

Preserve Evidence
```

---

## Professional Tips

- Keep Intercept off during general browsing unless you specifically need to pause traffic.

- Use HTTP history as a primary source for reviewing application requests.

- Read the complete request before modifying anything.

- Understand the feature before testing its security controls.

- Establish a baseline before introducing changes.

- Modify one meaningful variable at a time.

- Verify final application state after state-changing requests.

- Preserve important requests and responses for reproducibility.

- Keep testing within explicit authorization and scope.

---

## Common Beginner Mistakes

- Treating Proxy only as an interception button.

- Leaving Intercept enabled and wondering why the browser appears stuck.

- Modifying requests before understanding legitimate behavior.

- Testing traffic simply because it appears in HTTP history.

- Changing several variables simultaneously.

- Ignoring cookies and authentication context.

- Assuming every different response indicates a vulnerability.

- Failing to verify whether a state-changing operation actually succeeded.

- Ignoring background requests, APIs, or WebSocket communication.

- Performing active testing before confirming scope.

---

## Practice Tasks

1. Start Burp Suite in an authorized lab environment.

2. Configure a browser to send traffic through Burp Proxy.

3. Browse the application with Intercept off.

4. Open HTTP history and identify:

   * One `GET` request.
   * One `POST` request, if available.
   * One request containing parameters.
   * One request containing cookies.

5. Select one application action and identify:

   * What triggered the request.
   * Which endpoint received it.
   * Which HTTP method was used.
   * Which values were client-controlled.
   * Which authentication or session data was present.

6. Turn Intercept on for a controlled request.

7. Observe the request before forwarding it.

8. Turn Intercept off again and confirm normal browsing resumes.

9. Record the difference between:

   ```text
   Observing Traffic
   ```

   and:

   ```text
   Intercepting Traffic
   ```

10. Identify one request that would be appropriate to send to Repeater for further controlled analysis.

---

## Interview Questions

1. What is Burp Proxy?

2. Where does Burp Proxy sit in the client-server communication flow?

3. Why is an intercepting proxy useful during web application security testing?

4. What is the difference between Intercept ON and Intercept OFF?

5. Does Burp stop recording HTTP history when Intercept is off?

6. Why is HTTP history important during an assessment?

7. Why should a tester establish a baseline before modifying a request?

8. Why is changing one variable at a time useful?

9. How does Proxy integrate with tools such as Repeater?

10. Why does seeing a request in Proxy not mean you are authorized to test it?

11. Why is an unexpected response not automatically a vulnerability?

12. Why should final application state be verified after state-changing requests?

---

## Quick Revision

```text
Burp Proxy
    =
Traffic Observation
    +
Controlled Interception
    +
Request / Response Inspection
    +
Traffic Routing
    +
Investigation Entry Point
```

```text
General Browsing
    ↓
Intercept OFF
    ↓
HTTP History
    ↓
Identify Interesting Requests
```

```text
Controlled Investigation
    ↓
Establish Baseline
    ↓
Change One Variable
    ↓
Compare Behavior
    ↓
Verify State
    ↓
Preserve Evidence
```

```text
Visible in Proxy
    ≠
Authorized to Test
```

```text
Unexpected Behavior
    ≠
Validated Vulnerability
```

---

## Key Takeaways

- Burp Proxy sits between the client and target application and provides visibility into web traffic.

- Proxy remains useful even when interception is disabled because traffic can still be reviewed through HTTP history.

- Interception should be used deliberately rather than as the default mode for all browsing.

- Proxy is often the starting point for workflows involving Target, Repeater, Intruder, Comparer, Scanner, and other Burp capabilities.

- Professional testing begins with understanding legitimate behavior, establishing baselines, and making controlled changes.

- Security conclusions require reproducible evidence and meaningful impact, not merely unusual responses.

- All testing must remain within explicit authorization and scope.
