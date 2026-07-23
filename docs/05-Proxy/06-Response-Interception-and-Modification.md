# Response Interception and Modification

> **Module 05 — Proxy**

---

## Overview

- Burp Proxy can intercept not only requests sent by the client, but also selected HTTP responses returned by the server.

- Response interception allows a tester to inspect or modify a response before the browser processes it.

- This is especially useful for understanding:

  * Client-side behavior.
  * Hidden interface states.
  * Front-end validation.
  * Feature flags.
  * JavaScript-controlled workflows.
  * Browser security behavior.
  * Differences between client-side presentation and server-side enforcement.

- The core mental model is:

  ```text
  Browser Sends Request

  ↓

  Server Processes Request

  ↓

  Server Returns Response

  ↓

  Burp Intercepts Response

  ↓

  Tester Inspects or Modifies It

  ↓

  Browser Receives Modified Response
  ```

- A critical distinction must always be maintained:

  ```text
  Modifying What the Browser Receives
      ≠
  Modifying Server-Side State
  ```

- Response modification is primarily an investigative technique.

- Any apparent security impact must still be validated against the server.

---

## Learning Objectives

- By the end of this lesson, you will be able to:

  * Understand how response interception fits into the HTTP lifecycle.
  * Explain the difference between request and response interception.
  * Configure response interception deliberately.
  * Identify situations where response interception is useful.
  * Inspect response status lines, headers, cookies, and bodies.
  * Modify client-visible response content in controlled labs.
  * Analyze hidden or disabled interface functionality.
  * Distinguish front-end behavior from server-side authorization.
  * Understand the risks of drawing conclusions from locally modified responses.
  * Verify whether discovered functionality is actually accessible server-side.
  * Use response modification as part of a reproducible investigation workflow.
  * Preserve accurate evidence without misrepresenting locally altered behavior.

---

## Request Interception vs Response Interception

### Request Interception

```text
Browser
    │
    ▼
Burp
    │
    │ Request Paused Here
    ▼
Server
```

- The tester can modify what the server receives.

### Response Interception

```text
Server
    │
    ▼
Burp
    │
    │ Response Paused Here
    ▼
Browser
```

- The tester can modify what the browser receives.

- This difference determines what conclusions can be drawn from the test.

---

## Why Intercept Responses?

- Applications often place important logic in the front end.

- Responses may contain:

  * JSON configuration.
  * User-role information.
  * Feature flags.
  * UI permissions.
  * JavaScript.
  * HTML elements.
  * Redirect instructions.
  * Error messages.
  * Security headers.
  * Cookies.
  * Application state.

- Inspecting and modifying these values can reveal how the client behaves.

---

## The Fundamental Security Principle

```text
Client-Side Behavior
    ≠
Server-Side Security Enforcement
```

- A secure application should not rely solely on the browser to enforce sensitive security decisions.

- Examples of insufficient controls include:

  ```text
  Hide Admin Button

  Disable Sensitive Form

  Remove Menu Item

  Block Action Only in JavaScript

  Trust Client-Side Role Flag
  ```

- Server-side authorization must still protect the underlying functionality.

---

## Response Interception Workflow

```text
Identify Relevant Feature

↓

Capture Baseline Request and Response

↓

Understand Expected Client Behavior

↓

Enable Response Interception Deliberately

↓

Trigger the Feature Again

↓

Intercept Relevant Response

↓

Modify One Meaningful Value

↓

Forward Response to Browser

↓

Observe Client Behavior

↓

Identify Any Newly Exposed Functionality

↓

Test Underlying Server-Side Controls Separately

↓

Document Results Accurately
```

---

## Configure Response Interception Deliberately

- Response interception should generally be enabled only when needed.

- Intercepting every response can create excessive noise and interrupt normal browsing.

- A practical workflow is:

  ```text
  Browse Normally

  ↓

  Identify Interesting Request

  ↓

  Determine Which Response Matters

  ↓

  Configure Interception Rules

  ↓

  Reproduce the Action

  ↓

  Inspect Selected Response
  ```

- Burp interface labels and settings may vary by version.

- Focus on the underlying concept rather than memorizing one interface layout.

---

## What Can Be Modified in a Response?

- Common response components include:

  ```text
  Status Line

  Headers

  Cookies

  Body

  HTML

  JSON

  JavaScript

  Redirect Location

  Security Policy Headers
  ```

- Each component can influence browser behavior differently.

---

## Baseline Response First

- Always preserve the original response before modification.

- Record:

  ```text
  Request:

  Original Status:

  Original Headers:

  Original Body:

  Browser Behavior:

  Expected State:
  ```

- Then make one controlled modification.

- Without the baseline, it is difficult to determine what your change actually affected.

---

## Example Baseline

```http
HTTP/1.1 200 OK
Content-Type: application/json

{
  "username": "alice",
  "role": "user",
  "adminPanelVisible": false
}
```

- Suppose the browser hides an administrative navigation item.

- This establishes the baseline behavior.

---

## Controlled Response Modification

- In an authorized lab, you might change:

  ```json
  {
    "username": "alice",
    "role": "user",
    "adminPanelVisible": true
  }
  ```

- The browser may now display an administrative navigation item.

- This proves:

  ```text
  The Client Uses adminPanelVisible
  to Control UI Presentation
  ```

- It does **not** prove:

  ```text
  Alice Is Now an Administrator
  ```

- Server-side authorization must be tested independently.

---

## UI Exposure Is Discovery, Not Validation

```text
Modified Response

↓

Hidden Admin Link Appears

↓

Interesting Endpoint Discovered
```

- This is useful.

- But:

```text
Admin Link Visible

≠

Admin Action Authorized
```

- The correct next step is to inspect the underlying request and determine whether the server enforces authorization.

---

## Testing the Underlying Functionality

- Suppose response modification reveals:

  ```text
  /admin/users
  ```

- The security test should then ask:

  ```text
  Can the Standard User Request This Endpoint Normally?

  Does the Server Check the User's Role?

  Are Sensitive Actions Protected?

  Does the Server Return Unauthorized Data?
  ```

- Use a clean, unmodified server request for validation.

---

## Modifying JSON Responses

- Modern applications frequently return JSON that controls front-end behavior.

- Example:

  ```json
  {
    "features": {
      "reports": true,
      "admin": false
    }
  }
  ```

- A controlled response modification may reveal whether the front end contains dormant functionality.

- This can help identify:

  * Hidden routes.
  * Feature-gated UI.
  * Client-side assumptions.
  * Additional API calls.

- Any discovered server functionality still requires independent authorization testing.

---

## Modifying HTML Responses

- HTML may contain:

  * Hidden elements.
  * Disabled controls.
  * Form fields.
  * Links.
  * Comments.
  * Embedded configuration.

- Example:

  ```html
  <button disabled>Delete Account</button>
  ```

- Removing `disabled` locally may allow the browser to submit an action.

- The important security question is not whether the button becomes clickable.

- The important question is:

  ```text
  Does the Server Authorize the Resulting Request?
  ```

---

## Hidden HTML Elements

- Example:

  ```html
  <div style="display:none">
      Administrative Tools
  </div>
  ```

- Revealing the element may expose useful information.

- But hidden content is not automatically a vulnerability.

- Determine whether:

  * Sensitive data was already delivered to an unauthorized user.
  * Hidden controls invoke protected functionality.
  * The server properly enforces access.

---

## Modifying JavaScript

- JavaScript responses may contain logic such as:

  ```text
  Role Checks

  Feature Flags

  Route Definitions

  API Endpoints

  Validation Logic

  Workflow Conditions
  ```

- Modifying JavaScript locally can help understand how the client behaves.

- However:

  ```text
  Bypassing JavaScript Check Locally
      ≠
  Bypassing Server-Side Security
  ```

- Always validate the corresponding server request.

---

## Client-Side Validation

- A browser may prevent certain input through JavaScript.

- Example:

  ```text
  Maximum Quantity = 10
  ```

- Modifying the response or JavaScript may remove that restriction.

- The meaningful test is then:

  ```text
  Does the Server Independently Enforce the Maximum?
  ```

- If the server enforces it, the client-side restriction is only a usability control.

---

## Response Status Modification

- HTTP status codes influence browser and application behavior.

- Examples:

  ```text
  200 OK

  302 Found

  401 Unauthorized

  403 Forbidden

  404 Not Found
  ```

- Changing a status code locally can alter how the browser handles the response.

- Example:

  ```text
  302 → 200
  ```

- This may help reveal content or client behavior in a controlled lab.

- It does not prove the server originally granted access.

---

## 403-to-200 Misinterpretation

- A common mistake is:

  ```text
  Original Response:
  403 Forbidden

  ↓

  Tester Changes:
  403 → 200

  ↓

  Browser Renders Something

  ↓

  Tester Claims Authorization Bypass
  ```

- This is not sufficient.

- The tester changed the response locally.

- A real authorization bypass requires the server to provide unauthorized functionality or data without relying on local response tampering.

---

## Redirect Modification

- Responses may redirect the browser:

  ```http
  HTTP/1.1 302 Found
  Location: /login
  ```

- Modifying the redirect locally can help investigate what the client would do without following it.

- However, determine:

  * Whether sensitive content exists in the response body.
  * Whether the destination itself is protected.
  * Whether subsequent server requests are authorized.

- Preventing a redirect locally does not automatically bypass authentication.

---

## Response Headers

- Headers can affect:

  * Browser rendering.
  * Caching.
  * Framing.
  * Content interpretation.
  * Redirect behavior.
  * Cookie handling.
  * Browser security policies.

- Common examples include:

  ```text
  Content-Type

  Location

  Cache-Control

  Set-Cookie

  Content-Security-Policy

  X-Frame-Options
  ```

---

## Modifying Security Headers Locally

- Removing a security header from an intercepted response can demonstrate browser behavior without that protection.

- Example:

  ```text
  Remove Content-Security-Policy Locally
  ```

- This does not prove the production server lacks CSP.

- Evidence must clearly distinguish:

  ```text
  Original Server Response

  vs

  Locally Modified Response
  ```

---

## Set-Cookie Modification

- Responses may contain:

  ```http
  Set-Cookie: session=CONTROLLED_VALUE; Secure; HttpOnly
  ```

- Modifying cookie attributes locally can help study browser behavior in a controlled environment.

- Do not claim a server-side cookie misconfiguration based on a response you modified yourself.

- Security findings must be based on the original server behavior.

---

## Content-Type Modification

- Browsers may interpret content differently depending on:

  ```http
  Content-Type: application/json
  ```

  or:

  ```http
  Content-Type: text/html
  ```

- Local changes can help investigate browser parsing behavior.

- Again, document the original server-provided header separately.

---

## Feature Flags

- Applications may return feature configuration:

  ```json
  {
    "newDashboard": true,
    "betaExport": false
  }
  ```

- Modifying:

  ```text
  betaExport: false
  ```

  to:

  ```text
  betaExport: true
  ```

- may expose a hidden interface.

- This can reveal:

  * Undocumented functionality.
  * Additional endpoints.
  * New workflows.
  * Beta features.

- The security question remains whether the server properly authorizes those features.

---

## Role Flags

- Example:

  ```json
  {
    "role": "viewer"
  }
  ```

- Changing it locally to:

  ```json
  {
    "role": "admin"
  }
  ```

- may alter the UI.

- This indicates the client trusts or uses that field for presentation.

- It does not necessarily mean the server trusts the modified response.

---

## Permission Arrays

- Some applications return permission lists:

  ```json
  {
    "permissions": [
      "profile.read",
      "profile.update"
    ]
  }
  ```

- Adding a permission locally may expose controls.

- Test the resulting server requests independently.

- Proper server-side authorization should reject actions the user is not actually permitted to perform.

---

## Sensitive Data Already Present in Responses

- Response analysis can reveal a different class of issue.

- Suppose the server sends sensitive data to the client but hides it using CSS or JavaScript.

- Example:

  ```json
  {
    "publicName": "Alice",
    "internalSecret": "SENSITIVE_VALUE"
  }
  ```

- If an unauthorized user legitimately receives sensitive data in the original server response, the issue may already exist without modifying anything.

- The key evidence is the **original response**, not the modified browser display.

---

## Distinguish Two Cases

### Case 1 — Data Already Sent

```text
Server Sends Unauthorized Sensitive Data

↓

Client Hides It
```

- Potential server-side data exposure.

### Case 2 — Tester Inserts Data Locally

```text
Server Does Not Send Sensitive Data

↓

Tester Modifies Response

↓

Browser Displays Inserted Data
```

- No server-side data exposure demonstrated.

- This distinction is essential.

---

## Front-End Route Discovery

- Modified responses may expose routes or navigation elements.

- Example:

  ```text
  /admin

  /reports

  /internal-tools
  ```

- Treat these as leads.

- Then validate:

  ```text
  Is the Route Reachable?

  Is Authentication Required?

  Is Authorization Enforced?

  Does It Return Sensitive Data?

  Can Protected Actions Be Performed?
  ```

---

## Browser Behavior Investigation

- Response modification is useful for understanding how the browser reacts to:

  * Different status codes.
  * Different content types.
  * Feature flags.
  * Missing headers.
  * Changed JSON values.
  * Alternate redirects.
  * Modified HTML.

- This can help map application architecture.

---

## Single-Page Applications

- SPAs often rely heavily on API responses.

- A response may determine:

  ```text
  Routes

  Components

  Permissions

  Account State

  Feature Availability

  UI Rendering
  ```

- Modifying API responses can reveal how front-end decisions are made.

- Server-side endpoints must still enforce their own controls.

---

## Response Modification and Authentication

- Suppose an unauthenticated request receives:

  ```http
  HTTP/1.1 302 Found
  Location: /login
  ```

- Changing this locally does not create an authenticated session.

- Authentication depends on server-side state and credentials.

- Always verify whether subsequent requests are actually accepted by the server.

---

## Response Modification and Authorization

- Suppose the server returns:

  ```http
  HTTP/1.1 403 Forbidden
  ```

- Replacing the body with:

  ```text
  Access Granted
  ```

- changes only what the browser sees.

- The server still denied the request.

- Authorization findings must be demonstrated through actual server behavior.

---

## Response Modification and Business Logic

- Client-side responses may control workflow presentation.

- Example:

  ```json
  {
    "checkoutStep": 2,
    "paymentComplete": false
  }
  ```

- Modifying the response may move the UI to another screen.

- The important question is:

  ```text
  Does the Server Accept Later Workflow Actions
  Without Required Earlier Conditions?
  ```

- Validate server-side state transitions separately.

---

## Response Modification and File Uploads

- A response may tell the browser whether an upload succeeded.

- Changing:

  ```json
  {
    "success": false
  }
  ```

  to:

  ```json
  {
    "success": true
  }
  ```

- may change the UI.

- It does not prove the file was stored.

- Verify actual server state.

---

## Response Modification and API Testing

- API clients may react to specific fields.

- Example:

  ```json
  {
    "canDelete": false
  }
  ```

- Changing this locally can reveal a delete control.

- The real test is whether the delete request is authorized by the API.

---

## Response Modification and WebSockets

- Traditional HTTP response interception and WebSocket message manipulation are related but distinct workflows.

- WebSocket communication has its own message history and interception behavior.

- Do not assume HTTP response rules automatically apply to ongoing WebSocket messages.

---

## Match and Replace vs Manual Response Modification

- Manual interception is useful when:

  * Testing one response.
  * Exploring a hypothesis.
  * Understanding a single workflow.

- Match and Replace may be useful when:

  * The same controlled transformation must be repeated.
  * A value appears consistently.
  * Repetitive manual editing would be inefficient.

- Automated transformations must be documented because hidden modification can confuse later analysis.

---

## Avoid Hidden Automation

- If Burp automatically modifies responses, you may forget that the browser is no longer seeing the original server output.

- This can create false conclusions.

- Maintain awareness of:

  ```text
  Match and Replace Rules

  Extensions

  Response Interception Rules

  Browser Modifications

  Other Proxy Tooling
  ```

---

## Clean Testing State

- Before validating a finding:

  ```text
  Disable Temporary Response Modifications

  ↓

  Reproduce Original Server Behavior

  ↓

  Confirm Baseline

  ↓

  Test Server-Side Control Directly
  ```

- This prevents local modifications from contaminating evidence.

---

## Evidence Requirements

- Preserve both:

  ```text
  Original Server Response

  and

  Locally Modified Response
  ```

- Clearly label them.

- Example:

  ```text
  Original Response:
  role=user

  Local Test Modification:
  role=admin

  Client Observation:
  Admin navigation became visible

  Server Validation:
  Admin endpoint returned 403
  ```

- This accurately communicates the result.

---

## Do Not Misrepresent Evidence

- Screenshots of a locally modified page can be misleading if presented without context.

- Always state:

  * What the server originally returned.
  * What you changed locally.
  * Why you changed it.
  * What the browser did.
  * Whether server-side impact was validated.

---

## Reproducibility

- A useful response-modification experiment should record:

  ```text
  Feature:

  User:

  Request:

  Original Response:

  Modified Field:

  Original Value:

  Modified Value:

  Client Behavior:

  Follow-Up Server Request:

  Server Result:

  Security Impact:
  ```

- This allows another tester to reproduce the investigation.

---

## Common Mistakes

- Intercepting every response without a clear reason.

- Forgetting to preserve the original response.

- Changing several response values simultaneously.

- Treating a locally modified `403` as an authorization bypass.

- Assuming a visible admin button means admin access exists.

- Confusing client-side role flags with server-side role changes.

- Claiming a missing security header after manually removing it.

- Treating modified UI state as proof of modified server state.

- Failing to test the underlying endpoint.

- Forgetting active Match and Replace rules.

- Capturing screenshots without labeling local modifications.

- Leaving temporary response modifications enabled during later testing.

- Failing to verify behavior with clean, unmodified traffic.

---

## Professional Response Investigation Workflow

```text
Confirm Scope

↓

Identify Feature

↓

Capture Original Request and Response

↓

Record Baseline Client Behavior

↓

Form a Specific Hypothesis

↓

Intercept Relevant Response

↓

Modify One Value

↓

Forward to Browser

↓

Observe Client-Side Difference

↓

Identify Newly Exposed Requests or Features

↓

Disable Local Modification

↓

Test Underlying Server Control Directly

↓

Verify Actual Server State

↓

Document Original and Modified Evidence Separately
```

---

## Professional Tips

- Use response interception selectively.

- Preserve the original server response before editing.

- Modify one value at a time.

- Treat modified UI behavior as investigative evidence, not automatic proof of vulnerability.

- Distinguish browser presentation from server state.

- Validate every sensitive action against the server.

- Disable temporary rules before final validation.

- Clearly label locally modified evidence.

- Use HTTP History to preserve clean original traffic.

- Use Repeater to validate underlying endpoints.

- Verify authorization with controlled identities.

- Never claim a server-side weakness based solely on a response you altered yourself.

---

## Practice Tasks

1. Open an authorized lab through Burp Proxy.

2. Browse normally with response interception disabled.

3. Identify a harmless response containing HTML or JSON.

4. Save the original request and response.

5. Record:

   ```text
   Status:

   Content-Type:

   Important Headers:

   Relevant Body Field:

   Browser Behavior:
   ```

6. Configure Burp to intercept the relevant response.

7. Trigger the same action again.

8. Modify one harmless display value.

9. Forward the response.

10. Observe how the browser changes.

11. Confirm that the server-side state remains unchanged.

12. Find a response containing a boolean UI flag in a deliberately vulnerable lab.

13. Modify only that flag.

14. Observe whether hidden functionality becomes visible.

15. Capture the request generated by the newly visible control.

16. Disable response modification.

17. Test the underlying request directly.

18. Determine whether the server enforces authorization.

19. Document:

   ```text
   Original Response

   Local Modification

   Client-Side Effect

   Server-Side Validation

   Final Conclusion
   ```

20. Review active Proxy rules and ensure temporary modifications are disabled after testing.

---

## Interview Questions

1. What is response interception in Burp Suite?

2. How does response interception differ from request interception?

3. Why would a penetration tester modify a server response?

4. Why does changing a response locally not automatically change server-side state?

5. What can a modified feature flag reveal?

6. Why is making an admin button visible not sufficient evidence of an authorization bypass?

7. How should you validate functionality exposed through response modification?

8. Why is changing `403 Forbidden` to `200 OK` locally not proof of access-control bypass?

9. What is the difference between sensitive data already present in an original response and data inserted through local modification?

10. How can response modification help analyze single-page applications?

11. Why should the original response always be preserved?

12. What risks arise from leaving Match and Replace rules enabled?

13. How can response modification help investigate client-side validation?

14. Why must security-header testing distinguish original and locally modified responses?

15. What evidence should be collected during a response-modification test?

16. How would you investigate a hidden administrative feature revealed by modifying a JSON response?

17. Why should server-side authorization be tested using clean requests?

18. What is the role of final-state verification?

19. When should manual response interception be preferred over automated Match and Replace?

20. How can a tester avoid misrepresenting locally modified browser behavior in a report?

---

## Quick Revision

```text
Request Interception
    =
Modify What Server Receives
```

```text
Response Interception
    =
Modify What Browser Receives
```

```text
Modified Browser View
    ≠
Modified Server State
```

```text
Visible Admin UI
    ≠
Authorized Admin Access
```

```text
403 Changed Locally to 200
    ≠
Authorization Bypass
```

```text
Strong Workflow

Original Response

↓

One Local Modification

↓

Observe Client Behavior

↓

Disable Modification

↓

Test Server Control

↓

Verify Impact
```

```text
Original Sensitive Data Sent
    =
Potential Exposure
```

```text
Sensitive Data Inserted Locally
    =
Not Server Exposure
```

---

## Key Takeaways

- Response interception allows Burp to pause server responses before the browser processes them.

- It is valuable for understanding client-side logic, feature flags, hidden interface states, redirects, JavaScript behavior, and browser-side security assumptions.

- Response modification changes what the client receives; it does not automatically change authentication, authorization, business logic, or server-side state.

- A hidden feature revealed through local modification is a useful investigation lead, but the underlying server functionality must be tested independently.

- Changing a `403` response to `200`, exposing an admin button, or altering a role flag locally is not sufficient proof of a vulnerability.

- Preserve original server responses and clearly distinguish them from locally modified evidence.

- Disable temporary response modifications before validating findings.

- The strongest workflow uses response modification for discovery and understanding, then validates actual security impact through clean server-side requests and independent state verification.
