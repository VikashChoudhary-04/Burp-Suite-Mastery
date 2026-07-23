# Modifying and Forwarding Requests

## Overview

- After intercepting a request in Burp Proxy, you can inspect it, modify selected values, and decide whether it should continue to the server.

- Request modification is useful for understanding whether the server correctly validates and enforces security-sensitive behavior.

- Common testing targets include:

  * Parameters.
  * Headers.
  * Cookies.
  * Object identifiers.
  * User-controlled fields.
  * Content types.
  * Workflow values.
  * Authentication-related data.

- Professional testing should begin with a legitimate baseline and make controlled changes.

- The core principle is:

  ```text
  Capture Baseline

  ↓

  Understand Expected Behavior

  ↓

  Modify One Relevant Variable

  ↓

  Forward the Request

  ↓

  Observe the Response

  ↓

  Verify Final Application State

  ↓

  Compare With Baseline
  ```

---

## Learning Objectives

- By the end of this lesson, you should be able to:

  * Understand what forwarding an intercepted request does.
  * Modify intercepted requests safely.
  * Distinguish request components that can be edited.
  * Preserve a clean baseline before modification.
  * Change one meaningful variable at a time.
  * Understand the effect of dropping a request.
  * Recognize state-changing requests.
  * Avoid accidental duplicate actions.
  * Interpret responses cautiously.
  * Verify final application state.
  * Decide when Repeater is more appropriate than Proxy.
  * Document controlled request modifications clearly.

---

## Forwarding Requests

- When a request is intercepted, it is paused before reaching the destination server.

- Selecting **Forward** allows that request to continue.

  ```text
  Browser

  ↓

  Burp Proxy

  ↓

  Intercepted Request

  ↓

  Forward

  ↓

  Target Server
  ```

- If you modify the request before forwarding it, the server receives the modified version.

---

## What Can Be Modified?

- Most client-controlled parts of an HTTP request can be edited.

- Examples include:

  * HTTP method.
  * Request path.
  * Query parameters.
  * Form parameters.
  * JSON properties.
  * XML values.
  * Headers.
  * Cookies.
  * Object identifiers.
  * Content types.
  * Request bodies.

- Example baseline:

  ```http
  POST /api/profile HTTP/1.1
  Host: example.com
  Cookie: session=abc123
  Content-Type: application/json

  {
    "displayName": "Alice"
  }
  ```

- A controlled modification might change only:

  ```json
  {
    "displayName": "Alice-Test"
  }
  ```

- Everything else remains unchanged.

---

## Preserve the Baseline

- Before modifying a request, preserve the legitimate version.

- Record:

  * Method.
  * Endpoint.
  * Parameters.
  * Headers.
  * Cookies.
  * Authentication context.
  * User role.
  * Relevant object identifiers.
  * Expected response.
  * Expected final state.

- A baseline provides the reference needed to determine whether a modification caused meaningful behavioral change.

---

## One Variable at a Time

- Avoid changing several request components simultaneously.

- Poor testing:

  ```text
  Change Object ID

  +

  Change HTTP Method

  +

  Remove CSRF Token

  +

  Change Role Header
  ```

- If behavior changes, you cannot reliably identify which modification caused it.

- Better testing:

  ```text
  Baseline Request

  ↓

  Change Object ID Only

  ↓

  Observe

  ↓

  Restore Baseline

  ↓

  Change Another Variable

  ↓

  Observe
  ```

- Controlled experimentation produces stronger evidence.

---

## Modifying Parameters

- Parameters commonly appear in:

  * URL query strings.
  * Form bodies.
  * JSON.
  * XML.
  * Multipart requests.

- Example:

  ```http
  GET /api/orders?id=1001 HTTP/1.1
  Host: example.com
  Cookie: session=abc123
  ```

- A controlled authorization test in an authorized environment might modify only:

  ```text
  id=1001
  ```

  to:

  ```text
  id=1002
  ```

- Before interpreting the result, establish that the test accounts or objects are controlled and that ownership is known.

---

## Modifying Headers

- Headers can influence application behavior.

- Examples include:

  ```text
  Authorization

  Cookie

  Content-Type

  Origin

  Referer

  X-Requested-With

  Custom application headers
  ```

- Header modification can help investigate:

  * Authentication behavior.
  * Authorization assumptions.
  * Content negotiation.
  * CSRF defenses.
  * Origin validation.
  * Application-specific trust decisions.

- Never assume a client-supplied header is trusted by the server.

- Test the actual behavior.

---

## Modifying Cookies

- Cookies may contain:

  * Session identifiers.
  * Preferences.
  * Tracking values.
  * Workflow state.
  * Application-specific identifiers.

- Example:

  ```http
  Cookie: session=abc123; language=en
  ```

- Before modifying cookies, determine what each value appears to control.

- Session-related changes can invalidate authentication or alter testing context.

- Record the active user and session before drawing conclusions.

---

## Modifying JSON Requests

- Modern applications frequently use JSON APIs.

- Example:

  ```http
  PATCH /api/account HTTP/1.1
  Host: example.com
  Authorization: Bearer <token>
  Content-Type: application/json

  {
    "displayName": "Alice",
    "timezone": "UTC"
  }
  ```

- Potential investigation questions include:

  ```text
  Which fields are expected?

  Which fields are client-controlled?

  Are unexpected fields accepted?

  Are sensitive fields protected server-side?

  Does the server enforce authorization independently of the client?
  ```

- Change one field at a time and verify actual state after the request.

---

## Modifying HTTP Methods

- Some endpoints may behave differently depending on the HTTP method.

- Common methods include:

  ```text
  GET

  POST

  PUT

  PATCH

  DELETE

  OPTIONS
  ```

- Method changes should be performed only when relevant to a clear hypothesis.

- Do not assume that a different status code alone proves a security issue.

- Verify whether an unauthorized action or security-boundary violation actually occurred.

---

## Dropping Requests

- Selecting **Drop** prevents the intercepted request from being forwarded by Burp.

  ```text
  Browser

  ↓

  Burp Proxy

  ↓

  Intercepted Request

  ↓

  Drop

  X

  Target Server
  ```

- Dropping can help investigate:

  * Whether a client-side request is required.
  * Whether a workflow continues without a particular request.
  * Whether background traffic affects application behavior.
  * How the client handles a missing request.

- Dropping a request does not reverse an action that has already reached the server.

---

## Be Careful With State-Changing Requests

- State-changing requests may:

  * Create records.
  * Delete records.
  * Change account settings.
  * Submit orders.
  * Send messages.
  * Trigger emails.
  * Modify permissions.
  * Perform financial or workflow actions.

- Before forwarding a modified state-changing request, confirm:

  * The target is authorized.
  * The action is permitted.
  * Test data is controlled.
  * The expected impact is understood.
  * Repetition will not create harmful side effects.

---

## Duplicate Request Risk

- Repeatedly forwarding a state-changing request can trigger the same action multiple times.

- Examples include:

  ```text
  Multiple account changes

  Duplicate orders

  Repeated invitations

  Duplicate messages

  Multiple password-reset emails
  ```

- Track whether a request has already been processed.

- For repeatable testing, prefer controlled lab data and use Repeater when appropriate.

---

## Response Does Not Always Equal State

- A response is evidence, but it is not always proof of final application state.

- For example:

  ```text
  HTTP 200

  ≠

  Requested state change definitely occurred
  ```

- Likewise:

  ```text
  HTTP 403

  ≠

  No backend side effect definitely occurred
  ```

- After a state-changing request, verify the result independently.

- Possible verification methods include:

  * Reloading the affected page.
  * Fetching the object again.
  * Checking through another controlled account.
  * Reviewing the relevant application state.
  * Repeating the observation under controlled conditions.

---

## Editing Authentication Data

- Authentication-related values may include:

  * Session cookies.
  * Bearer tokens.
  * API keys.
  * CSRF tokens.
  * Custom authentication headers.

- Modifying these values can change the identity or validity of the request.

- Always record:

  ```text
  Active User

  Role

  Tenant

  Session

  Target Object

  Expected Permission
  ```

- Without this context, authorization results may be unreliable.

---

## When to Use Proxy vs Repeater

- Proxy is useful when:

  * Capturing a request during a specific browser workflow.
  * Making a one-time modification before forwarding.
  * Observing requests generated dynamically.
  * Investigating workflow timing or browser behavior.

- Repeater is usually better when:

  * Sending the same request repeatedly.
  * Testing several controlled variations.
  * Comparing responses.
  * Investigating parameters systematically.
  * Preserving a stable request for manual testing.

- Typical workflow:

  ```text
  Browser

  ↓

  Proxy

  ↓

  Capture Interesting Request

  ↓

  Send to Repeater

  ↓

  Perform Controlled Testing
  ```

---

## Professional Modification Workflow

```text
Confirm Scope

↓

Trigger Legitimate Application Action

↓

Capture Request

↓

Preserve Baseline

↓

Understand Authentication Context

↓

Identify Security-Sensitive Input

↓

Form a Specific Hypothesis

↓

Modify One Variable

↓

Forward or Send to Repeater

↓

Observe Response

↓

Verify Final State

↓

Compare With Baseline

↓

Repeat if Necessary

↓

Document Evidence
```

---

## Example: Controlled Object Identifier Test

- Suppose two controlled test accounts exist:

  ```text
  User A owns object 1001

  User B owns object 1002
  ```

- Baseline request from User A:

  ```http
  GET /api/documents/1001 HTTP/1.1
  Host: example.com
  Cookie: session=user-a-session
  ```

- Establish that User A can legitimately access object `1001`.

- Then change only:

  ```text
  /1001
  ```

  to:

  ```text
  /1002
  ```

- Keep User A's authentication context unchanged.

- Evaluate:

  * Response status.
  * Response body.
  * Returned object identity.
  * Ownership.
  * Whether access actually occurred.
  * Whether the result is reproducible.

- A changed response alone is not enough.

- The key question is whether the server violated the expected authorization boundary.

---

## Example: Controlled Header Modification

- Baseline:

  ```http
  GET /account HTTP/1.1
  Host: example.com
  Cookie: session=abc123
  X-User-Role: user
  ```

- If the application sends a client-controlled role-related header, a tester may investigate whether the server trusts it.

- Controlled test:

  ```text
  Baseline
      ↓
  Change X-User-Role Only
      ↓
  Forward
      ↓
  Observe
      ↓
  Verify Actual Permissions
  ```

- Do not conclude vulnerability merely because the response differs.

- Confirm whether unauthorized functionality or data became accessible.

---

## Common Mistakes

- Common mistakes include:

  * Editing multiple values simultaneously.
  * Failing to preserve the baseline.
  * Forgetting which user or session generated the request.
  * Blindly changing identifiers without known ownership.
  * Repeatedly triggering destructive or state-changing actions.
  * Assuming a status-code difference proves vulnerability.
  * Trusting response text without verifying final state.
  * Testing outside authorized scope.
  * Using Proxy for repetitive testing better suited to Repeater.
  * Forgetting that queued requests may still be waiting.

---

## Practical Exercises

1. Open an authorized lab through Burp Proxy.

2. Capture a simple request.

3. Record the baseline:

   * Method.
   * Path.
   * Parameters.
   * Headers.
   * Cookies.
   * Authentication context.

4. Forward the unmodified request.

5. Observe the normal response.

6. Capture the request again.

7. Modify one harmless parameter.

8. Forward it.

9. Compare the result with the baseline.

10. Capture another request and send it to Repeater.

11. Compare the Proxy and Repeater workflows.

12. Identify one state-changing request and document what precautions should be taken before modifying or replaying it.

---

## Interview Questions

1. What happens when you forward an intercepted request in Burp Proxy?

2. Why should you preserve the original request before modification?

3. Why is changing one variable at a time important?

4. What types of request components can be modified?

5. What does dropping a request do?

6. Why can repeatedly forwarding a state-changing request be risky?

7. Why should final application state be verified after a request?

8. When is Repeater preferable to modifying requests directly in Proxy?

9. Why must authentication context be documented during authorization testing?

10. Why does an unexpected response not automatically prove a vulnerability?

---

## Quick Revision

```text
Intercept Request

↓

Preserve Baseline

↓

Understand Authentication Context

↓

Identify Relevant Client-Controlled Input

↓

Form One Hypothesis

↓

Change One Variable

↓

Forward or Send to Repeater

↓

Observe Response

↓

Verify Final State

↓

Compare With Baseline

↓

Document Evidence
```

- Remember:

  * Forward sends the request onward.
  * Drop prevents the paused request from being forwarded.
  * Modified requests reach the server with your changes.
  * Preserve the legitimate baseline first.
  * Change one meaningful variable at a time.
  * Treat state-changing requests carefully.
  * Verify actual state, not only response text.
  * Use Repeater for repeated controlled testing.

---

## Key Takeaways

- Modifying and forwarding requests allows you to test how the server handles controlled changes to client-supplied data.

- Strong testing requires a known baseline, clear authentication context, one-variable-at-a-time experimentation, and independent verification of results.

- Proxy is valuable for capturing and making immediate changes during browser workflows.

- Repeater is generally better for repeated manual experimentation.

- A modified response is only an observation until you validate the security boundary, reproduce the behavior, and establish meaningful impact.

- Always perform request modification only within explicitly authorized scope.
