# HTTP History and Traffic Analysis

## Overview

- Burp Proxy's **HTTP history** records HTTP requests and responses that pass through Burp.

- During web application testing, HTTP history becomes one of the most important places for reviewing application behavior after normal browsing.

- Instead of intercepting every request manually, you can allow traffic to flow normally and use HTTP history to:

  * Review requests and responses.
  * Identify interesting endpoints.
  * Analyze parameters, cookies, and headers.
  * Compare authenticated and unauthenticated behavior.
  * Locate API traffic.
  * Find requests worth sending to other Burp tools.
  * Build an evidence trail during an investigation.

---

## Learning Objectives

- By the end of this lesson, you will be able to:

  * Understand the purpose of HTTP history.
  * Distinguish interception from passive traffic recording.
  * Navigate and analyze recorded traffic efficiently.
  * Identify security-relevant requests.
  * Use filtering to reduce noise.
  * Review requests and responses systematically.
  * Send interesting traffic to other Burp tools.
  * Apply HTTP history in professional testing workflows.

---

## HTTP History Mental Model

```text
Browser Activity
      ↓
Burp Proxy
      ↓
HTTP History
      ↓
Recorded Requests and Responses
      ↓
Filter and Prioritize
      ↓
Inspect Interesting Traffic
      ↓
Send to Repeater / Intruder / Comparer
      ↓
Validate and Document
```

- HTTP history is not merely a log.

- It is a working dataset representing the application's observed HTTP behavior.

---

## Intercept vs HTTP History

- **Intercept** and **HTTP history** serve different purposes.

### Intercept

- Intercept allows selected traffic to pause before continuing.

- Use it when you need to:

  * Inspect a request before it reaches the server.
  * Modify a request in transit.
  * Forward or drop traffic manually.

### HTTP History

- HTTP history records traffic for later analysis.

- You can normally keep interception off while browsing and still review captured traffic afterward.

```text
Intercept
→ Control traffic in transit

HTTP History
→ Review recorded traffic
```

- During normal application exploration, keeping interception off often provides a smoother workflow.

---

## What HTTP History Records

- Depending on the traffic and Burp configuration, HTTP history can expose information such as:

  * Host.
  * HTTP method.
  * URL.
  * Path.
  * Query string.
  * Request parameters.
  * Request headers.
  * Cookies.
  * Request body.
  * Response status.
  * Response headers.
  * Response body.
  * MIME or content type.
  * Response length.
  * Timing-related information.

- These details help reconstruct how the application communicates with its backend.

---

## Reading the History Table

- The history table provides a high-level view of captured traffic.

- Useful fields commonly include:

  * Host.
  * Method.
  * URL.
  * Status.
  * MIME type.
  * Extension.
  * Length.

- The exact interface may vary between Burp Suite versions.

- Do not analyze requests only from the table.

- Select important entries and inspect the complete request and response.

---

## Start With Normal Application Behavior

- Before testing security assumptions, browse the application normally.

- Exercise important features such as:

  * Login.
  * Logout.
  * Registration.
  * Profile updates.
  * Search.
  * Password changes.
  * File uploads.
  * Administrative functions.
  * API-backed actions.
  * Object creation, modification, and deletion.

- This creates legitimate baseline traffic.

```text
Normal User Action
      ↓
Recorded Request
      ↓
Recorded Response
      ↓
Known-Good Baseline
```

- Baselines make later modifications easier to interpret.

---

## Identify High-Value Requests

- Not every recorded request deserves equal attention.

- Prioritize requests involving:

  * Authentication.
  * Authorization.
  * Session management.
  * Sensitive objects.
  * Account settings.
  * Administrative actions.
  * File handling.
  * Financial or transactional operations.
  * API endpoints.
  * State-changing actions.
  * Security-sensitive workflow transitions.

- Common methods worth reviewing include:

```text
POST
PUT
PATCH
DELETE
```

- `GET` requests can also be security-sensitive, especially when they retrieve private objects or trigger poorly designed state changes.

---

## Analyze Request Components

- For each interesting request, inspect the complete structure.

### Method

```http
POST /api/profile HTTP/1.1
```

- Ask:

  * Is the method appropriate?
  * Do alternate methods behave differently?
  * Is this request state-changing?

### Path

```text
/api/users/1842
```

- Ask:

  * Does the path contain an object identifier?
  * Is there a versioned API?
  * Does the path expose privileged functionality?

### Query Parameters

```text
?user_id=1842&view=full
```

- Ask:

  * Which values are client-controlled?
  * Do parameters influence authorization or application state?

### Headers

- Review security-relevant headers such as:

```text
Authorization
Cookie
Origin
Referer
Content-Type
X-CSRF-Token
```

### Body

```json
{
  "user_id": 1842,
  "role": "user"
}
```

- Identify:

  * Object identifiers.
  * Roles.
  * Prices.
  * Status values.
  * Tenant identifiers.
  * Hidden fields.
  * Workflow controls.

---

## Analyze the Response

- A request cannot be understood fully without its response.

- Review:

  * Status code.
  * Response headers.
  * Response body.
  * Error messages.
  * Returned objects.
  * Redirects.
  * Cookies.
  * Content length.
  * Observable application state.

- Example:

```http
HTTP/1.1 200 OK
Content-Type: application/json
```

```json
{
  "id": 1842,
  "username": "alice",
  "email": "alice@example.test"
}
```

- Ask:

  * Did the server accept the action?
  * Was sensitive data returned?
  * Was authorization enforced?
  * Did the application state actually change?

---

## Status Codes Are Signals, Not Proof

- Status codes help classify behavior, but they do not prove security impact.

```text
200
201
204
302
400
401
403
404
500
```

- Examples:

  * `200` does not automatically mean a security control failed.
  * `403` does not always mean the requested action had no effect.
  * `404` may intentionally conceal an authorization decision.
  * `500` indicates an error, not automatically a vulnerability.

- Always inspect the complete response and verify final state when relevant.

---

## Filtering HTTP History

- Real applications generate large amounts of traffic.

- Noise may include:

  * Images.
  * Fonts.
  * Stylesheets.
  * Analytics.
  * Advertising.
  * Third-party resources.
  * Static JavaScript bundles.
  * Out-of-scope hosts.

- Use filtering to focus on relevant traffic.

- Useful filtering strategies include:

  * Show only in-scope items.
  * Filter by HTTP method.
  * Filter by MIME type.
  * Hide common static extensions.
  * Search for specific hosts, paths, or values.

- Filtering improves visibility.

- It does not replace careful analysis.

---

## Search for Security-Relevant Terms

- Search recorded traffic for terms related to important functionality.

- Examples:

```text
login
logout
password
reset
token
admin
role
user
account
profile
upload
download
api
graphql
oauth
callback
```

- Also search for application-specific object names and workflow terminology.

---

## Authentication Analysis

- HTTP history can reveal how authentication works.

- Look for:

  * Login requests.
  * Session cookies.
  * Bearer tokens.
  * Token refresh requests.
  * Logout behavior.
  * Authentication redirects.
  * MFA-related requests.

- Build a sequence:

```text
Unauthenticated
      ↓
Login Request
      ↓
Authentication Response
      ↓
Session Established
      ↓
Authenticated Requests
      ↓
Logout / Expiration
```

- Understanding this sequence is essential before testing authentication or session controls.

---

## Authorization Analysis

- HTTP history helps identify requests where access-control decisions should occur.

- Look for identifiers such as:

```text
user_id
account_id
tenant_id
order_id
invoice_id
document_id
project_id
```

- Establish ownership before changing identifiers.

```text
User A
      ↓
Accesses Object A
      ↓
Capture Baseline
      ↓
Identify Authorization Boundary
      ↓
Controlled Test
```

- Do not infer ownership from identifier patterns alone.

---

## API Traffic Analysis

- Modern applications often communicate heavily through APIs.

- HTTP history can expose:

```text
/api/users
/api/orders
/api/v1/profile
/graphql
```

- Review:

  * HTTP methods.
  * Endpoint patterns.
  * Authentication mechanisms.
  * Object identifiers.
  * JSON bodies.
  * Response schemas.
  * Error behavior.

- Repeated endpoint patterns can reveal the application's API structure.

---

## State-Changing Requests

- Identify requests that change server-side state.

- Examples:

```text
Change Email

Change Password

Create User

Delete Object

Update Role

Submit Payment

Upload File

Approve Request
```

- These requests often deserve deeper analysis because security failures can have direct impact.

- Record the baseline state before testing and verify the final state afterward.

---

## Redirect Chains

- Redirects can reveal important workflow behavior.

```text
POST /login
      ↓
302 /dashboard
      ↓
GET /dashboard
      ↓
200 OK
```

- Review the full chain.

- A redirect may reveal:

  * Authentication transitions.
  * Authorization behavior.
  * Session establishment.
  * Workflow enforcement.

---

## Cookies and Session Changes

- Compare cookies across important events.

- Examples:

```text
Before Login
After Login
After Privilege Change
After Password Change
After Logout
```

- Look for:

  * New session identifiers.
  * Session rotation.
  * Cookie deletion.
  * Changes in attributes.
  * Unexpected persistence.

- HTTP history helps reconstruct these transitions.

---

## Send Requests to Other Burp Tools

- Interesting history entries can become starting points for deeper analysis.

### Repeater

- Use Repeater for controlled manual modifications.

```text
HTTP History
      ↓
Interesting Request
      ↓
Send to Repeater
      ↓
Change One Variable
      ↓
Compare Behavior
```

### Intruder

- Use Intruder when systematic request variation is justified and authorized.

### Comparer

- Use Comparer when subtle request or response differences require structured comparison.

- Preserve the original baseline before extensive modification.

---

## Notes and Organization

- During larger assessments, mark or annotate important traffic where supported by your workflow.

- Useful categories include:

```text
AUTH

AUTHZ

SESSION

API

UPLOAD

ADMIN

BUSINESS LOGIC

CANDIDATE

CONFIRMED
```

- Consistent organization reduces repeated work and improves evidence quality.

---

## Professional HTTP History Workflow

```text
Define Scope
      ↓
Browse Normally
      ↓
Generate Representative Traffic
      ↓
Filter Noise
      ↓
Identify High-Value Requests
      ↓
Inspect Complete Request and Response
      ↓
Establish Baselines
      ↓
Send Selected Requests to Testing Tools
      ↓
Modify One Variable at a Time
      ↓
Compare Results
      ↓
Verify Final State
      ↓
Document Evidence
```

---

## Example Investigation

- Assume a user views an order.

```http
GET /api/orders/4812 HTTP/1.1
Host: shop.example.test
Cookie: session=USER_A_SESSION
```

- The response contains the user's order.

- HTTP history gives you the baseline request and response.

- Before testing authorization, determine:

  * Which user owns order `4812`.
  * Which session generated the request.
  * What legitimate behavior looks like.
  * Whether another controlled account owns a separate order.

- Then perform only authorized, controlled comparisons.

- The history entry preserves the baseline for evidence and reproduction.

---

## Common Mistakes

### Treating HTTP History as Background Noise

- HTTP history is one of Burp's primary investigation surfaces.

- Review it deliberately.

### Leaving Intercept On for All Browsing

- This can make normal exploration unnecessarily slow.

- Use HTTP history for passive recording and enable interception when you need in-transit control.

### Testing Every Request

- Prioritize security-sensitive functionality instead of modifying traffic randomly.

### Ignoring Responses

- A request alone rarely proves behavior.

- Analyze both sides of the exchange.

### Changing Multiple Variables

- Multiple simultaneous changes make results difficult to interpret.

```text
Baseline

→ Change One Variable

→ Compare

→ Record Result
```

### Trusting Status Codes Alone

- Verify response content and application state.

### Ignoring Scope

- HTTP history may contain third-party or out-of-scope traffic.

- Authorization and scope remain mandatory.

---

## Professional Tips

- Keep interception off during broad exploration unless you specifically need it.

- Use HTTP history as the primary record of observed application traffic.

- Filter aggressively when applications generate large amounts of noise.

- Preserve known-good baseline requests.

- Prioritize state-changing and authorization-sensitive requests.

- Track authentication and session transitions.

- Send only meaningful candidates to deeper testing tools.

- Verify server-side state after important actions.

- Keep evidence sufficient for another tester to reproduce your observations.

---

## Practice Tasks

1. Configure Burp with an authorized lab application.

2. Browse the application normally with interception off.

3. Open HTTP history and identify:

   * One `GET` request.
   * One state-changing request.
   * One request containing a cookie.
   * One request containing parameters.
   * One API-style request, if available.

4. Select one request and document:

```text
Method:

Host:

Path:

Parameters:

Authentication Material:

Status Code:

Response Type:

Security-Relevant Observation:
```

5. Apply filters to reduce static-resource noise.

6. Identify one high-value request and send it to Repeater.

7. Preserve the original request as a baseline.

8. Change one safe variable in an authorized environment.

9. Compare the result with the baseline.

10. Record whether the observed behavior matched your hypothesis.

---

## Interview Questions

1. What is HTTP history in Burp Proxy?

2. What is the difference between Intercept and HTTP history?

3. Why is HTTP history useful during penetration testing?

4. How do you reduce noise in HTTP history?

5. Which requests should you prioritize during analysis?

6. Why should you establish a baseline before modifying requests?

7. Why are HTTP status codes insufficient for proving a vulnerability?

8. How can HTTP history help with authentication analysis?

9. How can HTTP history support authorization testing?

10. When would you send a request from HTTP history to Repeater?

11. Why should request and response data be analyzed together?

12. How does HTTP history contribute to reproducible evidence?

---

## Quick Revision

```text
HTTP History
→ Records observed HTTP traffic

Intercept
→ Controls selected traffic in transit

Filter
→ Reduce noise

Prioritize
→ Security-sensitive requests

Inspect
→ Complete request and response

Baseline
→ Preserve legitimate behavior

Test
→ Change one variable

Compare
→ Evaluate differences

Verify
→ Confirm final state

Document
→ Preserve reproducible evidence
```

---

## Key Takeaways

- HTTP history is a central source of evidence during Burp-based application testing.

- It allows you to review application traffic without manually intercepting every request.

- Effective analysis requires filtering noise and prioritizing security-sensitive functionality.

- Requests and responses should always be interpreted together.

- Baseline behavior should be established before controlled modifications.

- HTTP history becomes most powerful when used as the bridge between normal application exploration and focused testing with tools such as Repeater, Intruder, and Comparer.

- Professional testing depends on scope, reproducibility, controlled experimentation, and evidence—not random request modification.
