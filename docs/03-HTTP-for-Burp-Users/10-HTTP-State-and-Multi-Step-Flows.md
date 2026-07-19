# HTTP State and Multi-Step Flows

> **Module Progress**

```text
██████████░░░ 10 / 13 Lessons
```

- Real web applications rarely consist of isolated HTTP requests.

- Important actions often occur through sequences such as:

  ```text
  Login

  ↓

  MFA Verification

  ↓

  Authenticated Dashboard
  ```  

- or:

  ```text
  Add Product

  ↓

  Enter Address

  ↓

  Apply Discount

  ↓
  
  Choose Payment

  ↓

  Confirm Order
  ```  

- To test these applications effectively, you must understand not only individual requests but also the **state transitions connecting them**.

- A useful mental model is:

  ```text
  Current State

  +

  Request / Action

  ↓

  Server Validation

  ↓

  New State
  ```  

- This is the beginning of thinking about a web application as a **state machine**.

---

## Why Individual Requests Are Not Enough

- Suppose you capture:

  ```http
  POST /checkout/confirm HTTP/1.1
  Host: shop.example.com
  Cookie: session=abc123

  orderId=4821
  ```

- Looking only at this request does not tell you:

  * How the order was created.
  * Whether shipping was selected.
  * Whether payment was authorized.
  * Whether a discount was applied.
  * Which user owns the order.
  * Which previous steps are required.

- The request belongs to a larger workflow.

---

## Workflow Mental Model

- Think in transitions:

  ```text
  State A

  ↓

  Action

  ↓

  State B

  ↓

  Action

  ↓

  State C
  ```  

- For example:

  ```text
  Anonymous

  ↓

  Submit Valid Credentials

  ↓

  Password Verified

  ↓

  Complete MFA

  ↓

  Fully Authenticated
  ```  

- Each transition may have its own security requirements.

---

## State

- State is information that affects how future actions are processed.

- Examples include:

  ```text
  Authentication State

  Session State

  Cart Contents

  Order Status

  MFA Completion

  Password Reset Progress

  CSRF State

  Account Verification

  Workflow Step
  ```  

- Some state exists on the server.

- Some information is carried by the client.

- Do not assume they are equally trustworthy.

---

## Server-Side State

- Example:

  ```text
  Session ID: abc123
  ```  

- The server may internally store:

  ```text
  Session abc123

  User: Alice

  Authenticated: Yes

  MFA Complete: Yes

  Role: User
  ```  

- The browser sends only the session identifier.

- The authoritative state remains server-side.

---

## Client-Side State

- Applications may also send state-related values to the client.

- Examples:

  ```text
  step=3

  role=user

  price=1000

  verified=true

  orderStatus=pending
  ```

- A security question is:

  > Does the server independently validate this state, or trust what the client sends?

- Client-supplied state should not automatically become authoritative.

---

## Hidden Fields Are Still Client-Controlled

- HTML may contain:

  ```html
  <input type="hidden" name="price" value="1000">
  ```

- The field is hidden from the normal user interface.

- But it still travels in the HTTP request.

- Therefore:

  ```text
  Hidden

  ≠

  Trusted
  ```

- A user controlling the request can potentially modify it.

---

## Disabled UI Elements Are Not Security Controls

- Suppose a button is disabled:

  ```text
  "Confirm Order"
  ```  

- until payment succeeds.

- That is a client-side interface restriction.

- The server must still verify:

  ```text
  Has Payment Actually Succeeded?
  ```  

when the confirmation request arrives.

- Security rules must be enforced server-side.

---

## Example — Login Flow

- A simplified login workflow:

  ```text
  GET /login

  ↓

  POST /login

  ↓

  Credentials Valid

  ↓
  
  Session Created

  ↓

  302 /dashboard

  ↓

  GET /dashboard
  ```  

- When analyzing this flow, identify:

  ```text
  Where are credentials validated?

  When is the session authenticated?

  When is the session identifier rotated?
  
  What happens on failure?

  What response creates the next transition?
  ```  

---

## Example — MFA Flow

- A stronger authentication workflow:

  ```text
  POST /login

  ↓

  Password Valid

  ↓

  Session State:
  Primary Authentication Complete

  ↓

  GET /mfa

  ↓

  POST /mfa

  ↓
  
  MFA Valid
  
  ↓

  Session State:
  Fully Authenticated

  ↓

  GET /dashboard
  ```

- The key security boundary is:

  ```text
  Primary Authentication Complete

  ≠

  Fully Authenticated
  ```

---

## MFA Workflow Questions

- Ask:

  ```text
  Can protected endpoints be accessed before MFA completion?

  Can the MFA endpoint be skipped?

  Can workflow state be changed client-side?

  Does a new session appear after MFA?

  Is MFA completion stored server-side?

  Can an old intermediate session be reused?
  ```  

- The vulnerability may exist between steps rather than inside the OTP request itself.

---

## Intermediate Authentication State

- Applications sometimes create partially authenticated sessions.

- Conceptually:

  ```text
  Anonymous

  ↓

  Password Valid

  ↓
  
  Partially Authenticated

  ↓

  MFA Valid

  ↓

  Fully Authenticated
  ```

- Each state should have clearly defined permissions.

- A partially authenticated session should not accidentally receive full account access.

---

## Example — Password Reset Flow

- A common workflow:

  ```text
  Request Password Reset

  ↓

  Server Generates Reset Token

  ↓
  
  User Receives Link

  ↓

  Reset Token Validated

  ↓
  
  New Password Submitted

  ↓
  
  Password Updated

  ↓

  Token Invalidated
  ```

- Testing only:

  ```text
  POST /reset-password
  ```  

misses most of the workflow.

---

## Password Reset Questions

- Analyze:

  ```text
  How is the account selected?

  How is the reset token generated?

  How long is it valid?

  Is it single-use?

  Is it tied to the intended account?
  
  Can it be reused?

  What happens after password change?
  
  Are existing sessions invalidated?
  ```

- The security of the workflow depends on the complete sequence.

---

## One-Time Tokens

- Applications may use tokens intended for one-time use.

- Examples:

  ```text
  Password Reset Token

  Email Verification Token

  Invite Token

  Transaction Confirmation Token
  ```

- Expected lifecycle:

  ```text
  Generated

  ↓

  Delivered

  ↓
  
  Validated
  
  ↓

  Consumed

  ↓

  Invalidated
  ```  
  
- A token described as one-time should not normally remain reusable after successful consumption.

---

## Token Expiration

- A token may have:

  ```text
  Creation Time

  Expiration Time

  Consumption State
  ```  

- Questions include:

  ```text
  Does it expire?

  Does expiration occur server-side?
  
  Can it be reused before expiration?

  Does generating a new token invalidate the old one?
  ```  

- Do not infer lifecycle from the token's appearance.

---

## Example — Checkout Workflow

- A simplified checkout:

  ```text
  Cart

  ↓

  Address

  ↓
  
  Shipping

  ↓

  Discount

  ↓
  
  Payment

  ↓

  Confirmation

  ↓  
  
  Order Created
  ```  

- Each stage may modify server-side state.

---

## Checkout State Machine

- Conceptually:

  ```text
  CART_CREATED

  ↓

  ADDRESS_SET

  ↓

  SHIPPING_SELECTED
  
  ↓
  
  PAYMENT_AUTHORIZED
  
  ↓
  
  ORDER_CONFIRMED
  ```  

- Expected transitions might be:

  ```text
  CART_CREATED

  cannot directly become

  ORDER_CONFIRMED
  ```  

unless all required conditions are independently validated.

---

## Step Skipping

- A common business-logic question is:

  > What happens if a later request is sent without completing an earlier required step?

- Conceptually:

  ```text
  Step 1

  ↓

  Step 2

  ↓

  Step 3
  ```

- Test hypothesis:

  ```text
  Step 1

  ↓
  
  Directly Request Step 3
  ```  
  
- The server should validate prerequisites rather than trusting that the browser followed the intended sequence.

---

## Replaying Earlier Requests

- Another workflow question:

  ```text
  Can a request from an earlier state be replayed after the workflow has progressed?
  ```

- Example:

  ```text
  Apply Coupon

  ↓

  Complete Order

  ↓

  Replay Apply Coupon Request
  ```  

- Expected behavior depends on application rules.

- Replaying stale workflow requests can reveal missing state validation.

---

## Reordering Requests

- Suppose normal flow is:

  ```text
  A → B → C
  ```

- Questions include:

  ```text
  What happens with A → C?

  What happens with B before A?

  What happens with C → B?

  Can B be repeated?
  ```
  
- This helps identify whether the server enforces workflow ordering.

---

## Duplicate Submission

- Consider:

  ```text
  POST /payment
  ```  

- What happens if the same request is sent twice?

- Potential outcomes:

  ```text
  Second Request Rejected

  Same Transaction Returned

  Duplicate Transaction Created
  ```  

- Sensitive operations should handle duplicate submissions appropriately.

---

## Idempotency

- An operation is idempotent when repeating the same intended operation has the same effect as performing it once.

- Conceptually:

  ```text
  Request Once

  ↓

  State X
  ```  

  ```text
  Same Request Repeated

  ↓

  Still State X
  ```

- Not all HTTP operations are naturally idempotent.

---

## HTTP Method Semantics and Idempotency

- At the HTTP semantic level:

  ```text
  GET
  HEAD
  PUT
  DELETE

  are defined as idempotent methods
  ```

- while:

  ```text
  POST

  is not inherently idempotent
  ```

- But application implementation still matters.

- For example, poorly designed endpoints can violate expected behavior.

---

## Payment Idempotency

- Payment APIs may use an idempotency mechanism.

- Conceptually:

  ```text
  POST /payment
  Idempotency-Key: ABC

  ↓

  Payment Created
  ```  

- Retry:

  ```text
  POST /payment
  Idempotency-Key: ABC

  ↓

  Same Logical Operation Recognized
  ```

- This can help prevent accidental duplicate processing.

- Implementation details vary.

---

## Race Conditions

- Some workflow flaws occur when multiple requests are processed concurrently.

- Conceptually:

  ```text
  Request A ──────┐
                ├── Shared State
  Request B ──────┘
  ```

- If both requests validate the same old state before either update is committed, unexpected outcomes may occur.

---

## Example Race Concept

- Suppose:

  ```text
  Coupon Remaining Uses = 1
  ```  

- Two requests arrive almost simultaneously:

  ```text
  Request A Checks:
  Uses Remaining? Yes

  Request B Checks:
  Uses Remaining? Yes  
  ```

- If synchronization is weak:

  ```text
  Both May Proceed
  ```

- This is a state-management problem involving timing.

- Only test concurrency where explicitly authorized and safe.

---

## CSRF Tokens as Workflow State

- Applications may include anti-CSRF tokens:

  ```html
  <input type="hidden" name="csrf" value="TOKEN123">
  ```

- or:

  ```http
  X-CSRF-Token: TOKEN123
  ```  

- These values may be tied to:

  * Session.
  * User.
  * Request.
  * Form.
  * Time window.

---

## CSRF Token Mental Model

- A simplified model:

  ```text
  Server Generates Token

  ↓

  Client Receives Token

  ↓

  Client Submits Token With State-Changing Request
  
  ↓

  Server Validates Token
  ```

- The exact implementation varies.

---

## Token Presence Is Not Proof of Protection

- Do not conclude:

  ```text
  CSRF Token Exists

  =

  CSRF Protection Works
  ```  

- Questions include:

  ```text
  Is the token actually validated?

  Is it tied to the correct session?

  What happens if it is missing?

  What happens if it is modified?

  Is it required for every relevant state-changing action?
  ```

- Evaluate behavior.

---

## Redirects and Workflow State

- Redirects often connect workflow steps.

- Example:

  ```text
  POST /login

  ↓

  302 /mfa

  ↓

  GET /mfa
  ```  
  
- The redirect itself does not enforce the security boundary.

- The server must still prevent unauthorized direct access to later endpoints.

---

## Redirect Does Not Equal Access Control

- Suppose:

  ```text
  POST /login

  ↓

  302 /mfa
  ```

- A tester should not assume:

  ```text
  Because Browser Was Redirected to MFA,
  Dashboard Must Be Protected
  ```

- The real question is:

  ```text
  What happens if the partially authenticated session requests /dashboard directly?
  ```

- Authorization must exist at the destination.

---

## Multi-Step Form State

- Applications sometimes spread data across multiple pages.

- Example:

  ```text
  Step 1:
  Personal Details

  ↓

  Step 2:
  Address

  ↓

  Step 3:
  Confirmation
  ```

- State may be stored:

  ```text
  Server-Side Session

  Database Draft Record

  Hidden Fields

  Signed Client Token

  Query Parameters  
  ```

- Determine which mechanism is authoritative.

---

## Client-Carried State

- Some applications carry state between steps:

  ```text
  ?step=2&accountType=premium
  ```

- or:

  ```html
  <input type="hidden" name="price" value="1000">
  ```

- Questions:

  ```text
  Can this value be modified?

  Is it integrity-protected?  

  Does the server recompute it?

  Does changing it alter security-sensitive behavior?
  ```  

---

## Signed State

- Applications may send client-visible state with an integrity mechanism.

- Conceptually:

  ```text
  Data

  +

  Signature / MAC
  ```

- The server can detect unauthorized modification if implemented correctly.

- Do not assume a long or encoded value is signed.

- Determine its actual structure and behavior.

---

## Server-Side State Is Usually Stronger for Authority

- For critical values such as:

  ```text
  Price

  Account Ownership

  Role

  Payment Status

  MFA Completion
  ```  

- a common secure principle is:

  ```text
  Server Maintains / Derives Authoritative State

  rather than

  Trusting Client Claims
  ```  

- Client input may request an action, but the server should validate authoritative facts.

---

## State Transitions

- For every important action, identify:

  ```text
  Starting State

  Required Preconditions

  Request

  Validation

  Resulting State
  ```  

- Example:

  ```text
  Starting State:
  Unpaid Order

  Precondition:
  Valid Payment Authorization

  Request:
  POST /confirm

  Result:
  Confirmed Order
  ```

- Then ask whether the server actually verifies the precondition.

---

## State Transition Table

- A simple analysis table:

| Current State | Action        | Expected Next State          |
| ------------- | ------------- | ---------------------------- |
| Anonymous     | Valid login   | Authenticated or MFA pending |
| MFA pending   | Valid MFA     | Fully authenticated          |
| Cart          | Checkout      | Checkout initiated           |
| Unpaid order  | Valid payment | Paid                         |
| Paid order    | Confirm       | Confirmed                    |

- This makes workflow assumptions explicit.

---

## Invalid Transition Testing

- Once expected transitions are understood, consider invalid ones:

  ```text
  Anonymous

  ↓
  
  Directly Request Authenticated Resource
  ```

  ```text  
  MFA Pending

  ↓

  Directly Request Dashboard
  ```  

  ```text
  Unpaid Order

  ↓

  Attempt Confirmation
  ```

  ```text
  Completed Reset Token

  ↓

  Attempt Reuse
  ```

- The goal is to verify server-side enforcement of state.

---

## State vs UI

- Never infer state solely from what the browser displays.

- The UI may say:

  ```text
  Payment Failed
  ```

- while server state could differ.

- Likewise, the UI may hide an action even though the endpoint remains callable.

- Verify using:

  ```text
  HTTP Responses

  Subsequent Requests

  Fresh Page Loads

  API State

  Controlled Account Observations
  ```

---

## Observable State Verification

- Suppose:

  ```text
  POST /change-email

  ↓

  500 Internal Server Error
  ```

- Do not immediately conclude failure.

- Verify:

  ```text
  GET /profile
  ```

- If the email changed, the operation succeeded despite the error response.

- State is stronger evidence than status text alone.

---

## Baseline Workflow First

- Before manipulating a workflow:

  ```text
  Complete It Normally
  ```

- Record:

  ```text
  Requests

  Responses

  Cookies

  Tokens

  Redirects

  Object IDs

  State Changes

  Step Order
  ```

- Without a valid baseline, abnormal behavior is difficult to interpret.

---

## Workflow Mapping Template

- For each step:

  ```text
  Step:

  Endpoint:

  Method:

  Authentication State:
  
  Important Parameters:

  Cookies / Tokens:

  Required Preconditions:

  Response:

  Redirect:

  State Change:

  Next Expected Step:
  ```  

- This turns HTTP history into a structured workflow map.

---

## Burp Workflow

- A practical process:

  ```text  
  1. Start With Clean State

  ↓

  2. Perform Workflow Normally Through Proxy

  ↓

  3. Review HTTP History

  ↓
  
  4. Identify Requests That Change State
  
  ↓
  
  5. Send Important Requests to Repeater

  ↓

  6. Map Tokens and Dependencies
  
  ↓

  7. Identify Expected State Transitions

  ↓

  8. Form One Hypothesis

  ↓

  9. Modify / Replay Carefully

  ↓

  10. Verify Actual State
  ```  

---

## Use Repeater Carefully

- A request copied to Repeater may contain stale:

  ```text
  CSRF Tokens

  Session Cookies

  One-Time Tokens

  Object IDs

  Workflow State
  ```  

- If a replay fails, ask:

  ```text
  Is the security hypothesis wrong?

  or

  Did the request simply become stale?
  ```  

- Understanding dependencies prevents false conclusions.

---

## Fresh State

- Some tests require resetting the workflow.

- Examples:

  ```text
  New Cart

  New Password Reset Token

  Fresh Login

  New Account

  Fresh CSRF Token
  ```  

- Repeated testing against already-consumed state may produce misleading results.

---

## Two-User Workflow Testing

- For account-specific workflows:

  ```text
  User A

  and

  User B
  ```  

- can help determine whether:

  * Tokens are bound to users.
  * Objects are ownership-checked.
  * Workflow state crosses account boundaries.
  * One user's identifiers work in another user's session.

- Use only controlled accounts and authorized environments.

---

## Workflow Security Questions

- For every multi-step process, ask:

  ```text
  Can steps be skipped?

  Can steps be repeated?

  Can steps be reordered?

  Can stale requests be replayed?

  Can one-time tokens be reused?

  Can client-controlled state be modified?

  Are prerequisites checked server-side?

  Are transitions tied to the correct user/session?

  Can concurrent requests violate assumptions?

  Does the final state match the response?
  ```

- These questions apply across many vulnerability classes.

---

## Common Mistakes

- Avoid:

  ```text
  Every request can be tested independently.
  ```

  ```text
  A redirect enforces access control.
  ```

  ```text
  A hidden field is trusted.
  ```

  ```text
  The browser's disabled button prevents the action.
  ```

  ```text
  A token exists, therefore it is validated correctly.
  ```

  ```text
  The response says failure, therefore no state changed.
  ```

  ```text
  The intended UI sequence is automatically enforced server-side.
  ```

  ```text
  A one-time token is one-time because its name says so.
  ```

- Observe actual server behavior.

---

## Workflow Analysis Checklist

```text
[ ] Complete the workflow normally first.

[ ] Capture the full HTTP sequence.

[ ] Identify state-changing requests.

[ ] Identify authentication state at each step.

[ ] Identify session changes.

[ ] Identify CSRF and workflow tokens.

[ ] Identify object IDs and ownership.

[ ] Identify client-carried state.

[ ] Identify server-side prerequisites.

[ ] Map expected state transitions.

[ ] Test whether required steps can be skipped.

[ ] Test whether relevant steps can be repeated.

[ ] Test whether stale requests are accepted.

[ ] Check one-time token invalidation.

[ ] Verify redirects are not being mistaken for access control.

[ ] Consider concurrency only where safe and authorized.

[ ] Verify actual resulting state independently.
```

---

## Key Takeaways

* Real applications must be analyzed as workflows, not only isolated requests.
* State determines how future requests are processed.
* Security-sensitive state may exist server-side or be carried by the client.
* Hidden fields and disabled UI controls are not trust boundaries.
* Authentication, MFA, password reset, checkout, and similar processes are state machines.
* Required workflow steps must be enforced server-side.
* Redirects guide clients but do not themselves provide access control.
* One-time tokens need proper expiration, binding, consumption, and invalidation.
* Requests may behave differently when skipped, reordered, repeated, or replayed.
* Race conditions involve concurrent interactions with shared state.
* CSRF token presence alone does not prove correct validation.
* Status codes and UI messages do not necessarily reflect actual state.
* Establish a normal baseline before manipulating workflows.
* Always verify the final server-side outcome.
