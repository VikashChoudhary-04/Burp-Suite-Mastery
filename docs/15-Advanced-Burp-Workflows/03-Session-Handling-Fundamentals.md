# Session Handling Fundamentals

> **Module 15 — Advanced Burp Workflows**

> **Progress**

```text
████░░░░░░░░░░░ 4 / 15 Files
```

---

## Overview

* Modern web applications rarely treat every HTTP request as an isolated event.

* Applications maintain state using mechanisms such as:

  * Session cookies
  * Bearer tokens
  * CSRF tokens
  * Refresh tokens
  * Nonces
  * Workflow identifiers
  * Request signatures
  * Temporary transaction values

* These values may change during an authenticated session.

* A request copied into Repeater, Intruder, Scanner, or another Burp workflow may therefore stop working when its authentication or application state becomes stale.

* **Session handling** is the process of ensuring that requests contain the correct state before they are sent.

* Understanding session handling is essential before using Burp's session-handling rules, macros, authentication automation, or complex multi-step workflows.

---

## Learning Objectives

- By the end of this lesson, you will be able to:

  * Explain what session state means in web applications.
  * Distinguish browser, Burp, request-copy, and server-side state.
  * Understand cookies and token-based session mechanisms.
  * Recognize static and dynamic session values.
  * Understand session creation, rotation, expiration, and invalidation.
  * Identify stale authentication and workflow state.
  * Understand Burp's cookie handling conceptually.
  * Recognize CSRF and anti-automation dependencies.
  * Understand why replayed requests may stop working.
  * Diagnose common session-related failures.
  * Establish reliable authenticated baselines.
  * Determine when session automation is appropriate.

---

## What Is Application State?

- HTTP is fundamentally request-response based.

- Conceptually:

  ```text
  Client

  ↓
  
  Request
  
  ↓
  
  Server

  ↓

  Response
  ```  

- But applications need to remember information such as:

  ```text
  Who is logged in?

  What role do they have?

  What workflow are they performing?

  Which transaction is active?

  Has a security token already been used?
  ```  

- Applications therefore maintain **state** across requests.

---

## Stateless HTTP vs Stateful Applications

- HTTP itself can be used in a stateless manner:

  ```text
  Request A
  
  Request B

  Request C
  ```  

- Each request can theoretically be processed independently.

- Modern applications often build state on top of HTTP:

  ```text
  Login

  ↓
  
  Session Created

  ↓

  Request A uses Session

  ↓

  Request B uses Session

  ↓

  Request C uses Session
  ```  

- This allows the server to associate multiple requests with the same user or workflow.

---

## What Is a Session?

- A session is a mechanism that allows an application to associate multiple interactions with a particular user, client, or application state.

- A common model:

  ```text
  User Logs In

  ↓

  Server Validates Credentials

  ↓

  Server Creates Session

  ↓

  Client Receives Session Identifier

  ↓

  Future Requests Include Identifier
  
  ↓

  Server Maps Identifier to Session State
  ```

- Example:

  ```http
  Set-Cookie: session=abc123
  ```  

- Later:

  ```http
  Cookie: session=abc123
  ```  

- The server uses the identifier to determine the associated session.

---

## Session Identifier vs Session State

- These concepts are related but different.

### Session Identifier

- A value presented by the client.

- Example:

  ```text
  session=abc123
  ```  

### Session State

- Information maintained or recognized by the application.

- Example:

  ```text
  Session abc123

  User: Alice
  Role: Standard User
  Authenticated: Yes
  Created: 10:00
  Expires: 11:00
  ```

- Conceptually:

  ```text
  Client

  session=abc123

  ↓

  Server

  ↓

  Session Store

  abc123 → Alice
  ```  

- The identifier points to or represents state.

---

## Common State Mechanisms

- Applications may use:

  ```text
  Cookies

  Bearer Tokens
  
  JWTs
  
  CSRF Tokens
  
  Refresh Tokens
  
  API Keys

  Nonces

  Request Signatures
  
  Workflow IDs

  Temporary Transaction Tokens
  ```

- Not all of these are technically "sessions" in the same implementation sense.

- From a Burp workflow perspective, however, they may all affect whether a request remains valid.

---

## Cookie-Based Sessions

- A common login flow:

  ```text
  POST /login

  ↓

  Credentials Valid

  ↓

  Set-Cookie: session=ABC123

  ↓

  Browser Stores Cookie
  
  ↓

  Future Requests Include Cookie
  ```

- Example:

  ```http
  GET /account HTTP/1.1
  Host: example.test
  Cookie: session=ABC123
  ```

- The server may use the cookie to identify the authenticated user.

---

## Token-Based Authentication

- Some applications use authorization tokens.

- Example:

  ```http
  Authorization: Bearer eyJ...
  ```  

- Conceptually:

  ```text
  Login / Token Endpoint
  
  ↓

  Access Token Issued

  ↓
  
  Client Stores Token

  ↓

  Future API Requests Include Token
  ```

- Tokens may:

  ```text
  Expire

  Rotate

  Be Revoked

  Depend on Scope

  Depend on Audience
  
  Require Refresh
  ```  

- A copied request may eventually contain an invalid token.

---

## CSRF Tokens

- A state-changing request may require both:

  ```text
  Authenticated Session

  +

  CSRF Token
  ```  

- Example:

  ```http
  POST /account/email HTTP/1.1
  Cookie: session=ABC123
  Content-Type: application/x-www-form-urlencoded

  csrf=TOKEN123&email=user@example.test
  ```

- The server may validate:

  ```text
  Is session valid?

  Is CSRF token valid?

  Does token belong to this session?

  Has token expired?

  Has token already been used?
  ```  

- Therefore:

  ```text
  Valid Session Cookie

  ≠
  
  Request Must Succeed
  ```  

- Other state may also be required.

---

## Dynamic State

- Some values remain stable for a long period.

- Others change frequently.

## Relatively Stable

```text
Long-Lived Session Cookie

Persistent API Key

Long-Lived Refresh Token
```

### Dynamic

```text
CSRF Token

Nonce

Timestamp

One-Time Token

Short-Lived Access Token

Transaction Identifier

Request Signature
```

- Dynamic values create challenges for replay and automation.

---

## The Four-State Mental Model

- During Burp testing, distinguish four forms of state:

  ```text
  1. Browser State

  2. Burp State
  
  3. Request-Copy State

  4. Server State
  ```

- Confusing them is a common source of troubleshooting errors.

---

### 1 — Browser State

- The browser may automatically maintain:

  ```text
  Cookies

  Storage

  Authentication Tokens

  Redirect Chains
  
  Application JavaScript State
  ```

- Example:

  ```text
  Browser

  Cookie:
  
  session=NEW123
  ```  

- The user continues browsing successfully.

---

### 2 — Burp State

- Burp may maintain information such as:

  ```text
  Cookies

  Cookie Jar Entries

  Session-Handling Configuration

  Macro Results

  Tool Configuration
  ```  

- Depending on the workflow, Burp may use this state when preparing requests.

- This state is separate from what the browser currently holds.

---

### 3 — Request-Copy State

- When a request is copied to a tool such as Repeater:

  ```text
  Browser Request

  ↓

  Send to Repeater

  ↓

  Independent Request Copy
  ```  

- The copied request may contain:

  ```text
  Cookie: session=OLD123
  ```

- Later the browser may use:

  ```text
  Cookie: session=NEW456
  ```
- The Repeater request does not automatically become identical to the browser's latest request merely because the browser session changed.

---

### 4 — Server State

- The server maintains its own truth.

- Example:

  ```text
  OLD123 → Invalid

  NEW456 → Valid
  ```  

- Even if Burp still contains:

  ```text
  OLD123
  ```  

the server may no longer accept it.

- The server decides whether the presented state is valid.

---

## State Divergence

- Consider:

  ```text
  10:00

  Browser:
  session=A

  Repeater:
  session=A

  Server:
  A = Valid
  ```

- Everything works.

- Then:

  ```text
  10:30
  
  Browser logs in again

  Browser:
  session=B

  Repeater:
  session=A

  Server:
  A = Invalid
  B = Valid
  ```

- Now:

  ```text
  Browser Works

  Repeater Returns 401
  ```  

- Nothing is necessarily wrong with Repeater.

- The states diverged.

---

## State Divergence Diagram

```text
Initial State

Browser ───── session=A ─────► Server
Repeater ──── session=A ─────► Server

A = Valid
```

- Later:

  ```text
  Browser ───── session=B ─────► Server
  Repeater ──── session=A ─────► Server

  A = Invalid
  B = Valid
  ```

- The correct troubleshooting question is:

  ```text
  What state changed?
  ```

---

## Session Creation

- A session may be created after:

  ```text
  Login

  Registration

  Anonymous Visit

  OAuth Callback

  MFA Completion

  Password Reset

  Privilege Change
  ```  

- Observe:

  ```text
  Set-Cookie

  Authorization Tokens

  Redirects

  Response Bodies
  ```

to determine when session state is established.

---

## Session Rotation

- Applications may rotate session identifiers after security-sensitive events.

- Example:

  ```text
  Anonymous Session

  session=A

  ↓

  Login
  
  ↓

  Authenticated Session
  
  session=B
  ```

- This can help protect against session fixation.

- Rotation may also occur after:

  ```text
  Privilege Change

  MFA

  Password Change

  Token Refresh

  Reauthentication
  ```  

- Do not assume the first observed session identifier remains valid forever.

---

## Session Expiration

- Sessions may expire because of:

  ```text
  Absolute Lifetime

  Idle Timeout

  Token Expiration

  Server Restart
  
  Administrative Revocation

  Password Change

  Logout

  Security Event
  ```  

- A stale request may therefore fail even if nothing in the request was manually changed.

---

## Session Invalidation

- Example:

  ```text
  User Logged In

  session=A
  
  ↓

  User Logs Out
  
  ↓

  Server Invalidates A
  ```  

- Repeater may still contain:

  ```http
  Cookie: session=A
  ```  

- But the server may return:

  ```text
  401 Unauthorized
  ```

- or:

  ```text
  302 → /login
  ```

- This is expected state behavior.

---

## Multiple Concurrent Sessions

- Applications may allow:

  ```text
  User A
  
  Browser Session 1

  Browser Session 2

  Mobile Session

  API Session
  ```  

or they may invalidate older sessions.

- When testing, determine whether:

  ```text
  New Login Invalidates Old Login

  Password Change Invalidates Sessions
  
  Logout Invalidates One or All Sessions
  ```  

- These behaviors affect Burp workflows.

---

## Authentication vs Session State

- Authentication answers:

  ```text
  Who are you?
  ```

- Session state may also answer:

  ```text
  What are you currently allowed to do?

  What workflow are you in?

  What security checks have been completed?
  ```  

- Example:

  ```text
  Authenticated User

  ↓

  MFA Pending
  ```

- The user is partly identified but may not yet have full application access.

- Do not reduce every state problem to a cookie.

---

## Authorization State

- A valid session may represent:

  ```text
  User A

  Standard Role
  ```  

- while another represents:

  ```text
  User B

  Administrator
  ```

- When testing authorization, you must know which identity each request actually represents.

- A valid session with the wrong identity can invalidate the entire test.

---

## The Identity Rule

- Before interpreting an authorization result, answer:

  ```text
  Which user does this request represent?
  ```  

- Do not assume based on:

  ```text
  Browser Tab

  Repeater Tab Name

  Old Notes

  Expected Cookie
  ```

- Verify the actual request state.

---

## Two-User Testing

- Authorization testing often uses:

  ```text
  User A

  User B
  ```  

- Conceptually:

  ```text
  User A owns Object 100

  User B owns Object 200
  ```

- You may need to maintain:

  ```text
  Session A

  Session B
  ```

simultaneously.

- A mistake such as:

  ```text
  Expected Session B

  Actually Sent Session A
  ```

can create a false conclusion.

---

## Session Labeling

- For manual testing, clearly label state.

- Example:

  ```text
  USER_A_SESSION

  USER_B_SESSION
  
  ADMIN_SESSION  
  ```

- In notes:

  ```text
  User A:
  session=...

  User B:
  session=...

  Admin:
  session=...
  ```

- Avoid exposing real sensitive values in committed documentation or public repositories.

- The goal is identity clarity.

---

## Fresh Baselines

- Before testing:

  ```text
  Capture Fresh Request

  ↓

  Confirm Identity

  ↓

  Confirm Request Works

  ↓

  Establish Baseline
  ```

- Then modify one relevant variable.

- Example:

  ```text
  Fresh User B Request

  ↓

  Confirm User B Object Works

  ↓

  Change Only Object ID

  ↓

  Observe Authorization Behavior
  ```  

- This is stronger than replaying an unknown request from hours earlier.

---

## The Fresh-State Principle

- Prefer:

  ```text
  Fresh Authentication

  +

  Fresh Request

  +

  Known Identity

  +

  Known Object Ownership
  ```  

before drawing conclusions.

- Stale state introduces uncertainty.

---

## Why Replayed Requests Fail

- Common causes:

  ```text
  Expired Session

  Rotated Cookie
  
  Expired Access Token

  Stale CSRF Token

  One-Time Nonce

  Expired Timestamp

  Invalid Signature

  Changed Workflow State
  
  Object Already Modified

  Transaction Already Completed
  ```  

- Do not immediately assume:

  ```text
  Burp is broken.
  ```  

- First identify state dependencies.

---

## Replayability

- Some requests are naturally replayable.

- Example:

  ```http
  GET /api/profile
  ```

- Others may not be.

- Example:

  ```http
  POST /api/payment/confirm
  ```  

- The server may enforce:

  ```text
  One-Time Transaction Token

  Nonce

  State Transition

  Idempotency Rule
  ```  

- A second request may intentionally fail.

---

## Request State Dependencies

- For an interesting request, identify:

  ```text
  Authentication Dependency

  CSRF Dependency

  Workflow Dependency

  Timing Dependency

  Signature Dependency

  Object-State Dependency
  ```  

- Example:

  ```text
  POST /checkout/confirm

  Requires:

  Session Cookie
  +
  CSRF Token
  +
  Cart ID
  +
  Checkout State
  +
  Payment Token
  ```

- Changing only one component may not be sufficient to replay the workflow.

---

## Cookie Jar Concept

- Burp can maintain cookie information for use in certain workflows.

- Conceptually:

  ```text
  Responses

  ↓
  
  Set-Cookie Observed

  ↓
  
  Cookie State Stored

  ↓

  Relevant Requests May Use Cookie State
  ```  

- The exact behavior depends on Burp configuration and the tool or workflow involved.

- Important:

  ```text
  Cookie Jar Exists

  ≠

  Every Request Automatically Uses the Latest Cookie
  ```

- Understand the configuration being used.

---

## Cookie Scope

- Cookies may be restricted by:

  ```text
  Domain

  Path

  Secure Attribute

  SameSite

  Expiration
  ```  

- Example:

  ```http
  Set-Cookie: session=ABC; Path=/app; Secure
  ```  

- The cookie may not apply identically to every request.

- When troubleshooting, inspect cookie attributes and request destination.

---

## Multiple Cookies

- Applications may use:

  ```text
  session

  csrf

  tracking

  preferences

  load-balancer affinity

  authentication state
  ```  

- Do not assume the cookie named:

  ```text
  session
  ```  

is the only security-relevant value.

- Test dependencies carefully.

---

## Cookie Rotation Example

- Initial response:

  ```http
  Set-Cookie: session=A
  ```  

- Later:

  ```http
  Set-Cookie: session=B
  ```  

- If a stored request still sends:

  ```http
  Cookie: session=A
  ```  

the request may fail.

- Always compare with a fresh browser request.

---

## Bearer Token Expiration

- Example token flow:

  ```text
  Access Token

  Lifetime: 15 Minutes

  ↓

  Expires

  ↓

  Refresh Token Used

  ↓

  New Access Token
  ```  

- Browser or application JavaScript may refresh automatically.

- Repeater may still contain the old access token.

- Result:

  ```text
  Browser Works

  Repeater Fails
  ```  

- Again, the issue is state divergence.

---

## Refresh Tokens

- A common architecture:

  ```text
  Access Token

  Short-Lived

  +

  Refresh Token

  Longer-Lived
  ```  

- When the access token expires:

  ```text
  Refresh Request

  ↓

  New Access Token

  ↓

  Application Continues
  ```  

- Advanced Burp workflows may need to reproduce this sequence when automated tools require long-running authenticated access.

---

## CSRF Token Binding

- A CSRF token may be:

  ```text
  Session-Bound

  User-Bound

  Request-Bound

  One-Time

  Time-Limited
  ```

- Example:

  ```text
  Session A

  CSRF A
  ```  

- If you combine:

  ```text
  Session B

  CSRF A
  ```  

the server may reject the request.

- Therefore:

  ```text
  Fresh Token

  ≠

  Valid With Every Session
  ```  

- Understand binding.

---

## Token Extraction

- Dynamic tokens may appear in:

  ```text
  HTML

  JSON

  Headers

  Cookies

  Redirect URLs
  ```  

- Example:

  ```html
  <input type="hidden" name="csrf" value="TOKEN123">
  ```  

- A multi-step workflow may require:

  ```text
  GET Form

  ↓

  Extract TOKEN123

  ↓

  POST Form With TOKEN123
  ```  

- This concept becomes important when learning macros.

---

## Nonces

- A nonce is typically intended to be used in a limited context.

- Possible properties:

  ```text
  Unique

  One-Time

  Short-Lived
  
  Request-Specific
  ```

- Replay may fail because:

  ```text
  Nonce Already Used
  ```  

- This can be expected secure behavior.

---

## Timestamps

- Requests may contain:

  ```text
  timestamp=...
  ```  

or signed time-based values.

- A request copied earlier may fail because:

  ```text
  Current Time

  -

  Request Timestamp

  >

  Allowed Window  
  ```

- Refreshing only the session cookie may not solve the problem.

---

## Request Signatures

- Some APIs sign requests using values such as:

  ```text
  Method

  Path

  Body

  Timestamp

  Secret
  ```

- Conceptually:

  ```text
  Request Data

  ↓

  Signature Function

  ↓

  Signature

  ↓

  Server Verification
  ```

- If you modify:

  ```text
  Body
  ```  

- without recalculating:

  ```text
  Signature
  ```  

the request may fail.

- This is a state and integrity dependency, not necessarily an authentication failure.

---

## Workflow State

- Applications may enforce sequences.

- Example:

  ```text
  Step 1 — Create Order

  ↓

  Step 2 — Select Payment

  ↓

  Step 3 — Confirm

  ↓

  Step 4 — Complete
  ```  

- Attempting:

  ```text
  Step 4
  ```

without the required server-side state from previous steps may fail.

- A request cannot always be understood independently from its workflow.

---

## Server-Side Object State

- Suppose:

  ```text
  POST /coupon/apply
  ```  

works once.

- Repeating it may fail because:

  ```text
  Coupon Already Applied
  ```

- The session may still be valid.

- The changed state is:

  ```text
  Order / Cart State
  ```

not authentication.

---

## Session Failure Signals

- Common signals include:

  ```text
  401 Unauthorized

  403 Forbidden

  302 Redirect to Login

  Login HTML Returned

  "Session Expired"

  "Invalid CSRF Token"

  "Token Expired"

  "Invalid Signature"

  "Invalid State"

  "Nonce Already Used"
  ```  

- Do not interpret each signal literally without examining application behavior.

- For example:

  ```text
  403
  ```  

- may represent:

  ```text
  Authorization Failure

  CSRF Failure

  WAF Block

  Invalid Token

  Business Rule
  ```  

- Investigate context.

---

## Baseline Comparison

- When a request unexpectedly fails:

  ```text
  Failing Stored Request

  vs

  Fresh Working Browser Request
  ```

- Compare:

  ```text
  Method

  Path

  Cookies

  Authorization

  CSRF

  Body
  
  Headers

  Timestamp

  Nonce
  
  Signature

  Redirects
  ```  

- This often reveals the changed state.

---

## Session Troubleshooting Workflow

- Use:

  ```text
  Request Fails

  ↓

  Capture Fresh Browser Request

  ↓

  Does Fresh Request Work?

  ├── No
  │   ↓
  │   Problem May Be Application / Account / Network / Workflow  
  │
  └── Yes
      ↓

  Compare Fresh vs Failing Request

  ↓

  Identify State Difference

  ↓

  Update One Dependency

  ↓

  Retest
  ```

- Avoid changing everything simultaneously.

---

## Troubleshooting — 401 Unauthorized

```text
401

↓

Check Session Cookie

↓

Check Authorization Header

↓

Check Token Expiration

↓

Check Session Invalidation

↓

Capture Fresh Request

↓

Compare
```

---

## Troubleshooting — 403 Forbidden

```text
403

↓

Is Identity Correct?

↓

Is Authorization Expected?

↓

Is CSRF Valid?

↓

Is Request Signature Valid?

↓

Is WAF / Security Control Involved?

↓

Compare With Fresh Baseline
```

- Do not automatically classify every 403 as authorization.

---

## Troubleshooting — Redirect to Login

- Possible causes:

  ```text
  Expired Session

  Missing Cookie

  Wrong Cookie Domain

  Session Invalidated

  Authentication Workflow Changed
  ```  

- Compare with a working request.

---

## Troubleshooting — Invalid CSRF Token

```text
CSRF Error

↓

Fresh Session?

↓

Fresh Token?

↓

Correct Token Source?

↓

Token Bound to Session?

↓

One-Time Token?

↓

Required Workflow Step Completed?
```

---

## Troubleshooting — Works Once Only

```text
Works Once

↓

Check Nonce

↓

Check One-Time Token

↓

Check Transaction State

↓

Check Server-Side Object State

↓

Check Idempotency Controls
```

---

## Troubleshooting — Wrong User

```text
Unexpected User Data

↓

Stop Interpretation

↓

Verify Cookie

↓

Verify Authorization Header

↓

Check Session Handling

↓

Check Automatic Modifications

↓

Capture Fresh Identity Baseline
```

- Do not continue authorization testing until identity is certain.

---

## Troubleshooting — Browser Works, Repeater Fails

- This is one of the most common patterns.

  ```text
  Browser Works

  Repeater Fails
  
  ↓

  Compare Fresh Browser Request

  With Repeater Request
  ```  

- Look for:

  ```text
  New Cookie

  New Bearer Token

  New CSRF Token
  
  New Nonce

  New Timestamp

  New Signature

  Changed Workflow ID
  ```  

- The browser may be updating state automatically.

---

## Troubleshooting — Intruder Loses Authentication

- Possible pattern:

  ```text
  Intruder Starts

  ↓

  Initial Requests Work
  
  ↓

  Session Expires
  
  ↓

  Later Requests Fail
  ```  

- Possible solution concept:

  ```text
  Session Handling

  +

  Fresh Authentication

  +

  Controlled Automation
  ```

- Do not simply increase request volume or retry failed requests without understanding the state problem.

---

## When Manual Session Handling Is Enough

- You may not need automation if:

  ```text
  Session Is Long-Lived

  Only a Few Requests Are Tested

  Tokens Rarely Rotate

  Manual Refresh Is Easy
  ```  

- Example:

  ```text
  Capture Fresh Request

  ↓
  
  Send to Repeater

  ↓

  Perform Five Controlled Tests

  ↓

  Done
  ```  

- Simple workflows are preferable when sufficient.

---

## When Automation Becomes Useful

- Automation may help when:
  
  ```text
  Sessions Expire Frequently

  Tokens Rotate
  
  CSRF Values Change

  Many Requests Must Be Tested

  Scanner Requires Authentication

  Intruder Runs Longer Than Session Lifetime

  Multi-Step Login Is Required
  ```  

- This is where session-handling rules and macros become useful.

---

## Do Not Automate Too Early

- Poor approach:

  ```text
  Request Fails

  ↓

  Immediately Build Complex Macro
  ```

- Better:

  ```text
  Request Fails

  ↓

  Understand Why

  ↓

  Identify Dynamic Dependency

  ↓

  Reproduce Manually

  ↓

  Automate Only the Necessary Part
  ```

- Automation should encode understanding.

---

## Session Dependency Map

- For complex applications, create:

  ```text
  Authentication

  ├── session cookie
  ├── access token
  └── refresh token

  Request Protection

  ├── CSRF token
  └── nonce

  Workflow

  ├── transaction ID
  └── workflow state

  Integrity

  ├── timestamp
  └── signature
  ```

- This helps identify what must remain current.

---

## Example — Simple Session

```text
Login

↓

session=ABC

↓

All Authenticated Requests Use ABC

↓

Session Valid for 8 Hours
```

- Manual testing may be sufficient.

---

## Example — Rotating CSRF

```text
GET /profile/edit

↓

CSRF=A

↓

POST /profile/edit with A

↓

Server Accepts

↓

New Page Returns CSRF=B
```

- Replaying:

  ```text
  CSRF=A
  ```

may fail.

- Automation may need to obtain a fresh token before each state-changing request.

---

## Example — Short-Lived API Token

```text
Login

↓

Access Token A

↓

15 Minutes

↓

Token Expires

↓

Refresh Endpoint

↓

Access Token B
```

- A long Intruder or Scanner workflow may require token refresh handling.

---

## Example — Multi-Step Authentication

```text
POST /login

↓

MFA Challenge

↓

POST /mfa

↓

Session Created
```

- Automating authentication may require reproducing multiple steps.

- If MFA requires human interaction or an external factor, full automation may not be appropriate or possible.

---

## Example — Authorization Test With Two Sessions

```text
User A

session=A

Owns Object 100
```

```text
User B

session=B

Owns Object 200
```

- Baseline:

  ```http
  GET /objects/200
  Cookie: session=B
  ```

- Then controlled test:

  ```http
  GET /objects/100
  Cookie: session=B
  ```

- The key requirement is:

  ```text
  Session B must remain Session B
  ```

- If automatic session handling replaces it with:

  ```text
  Session A
  ```

the authorization test becomes invalid.

- Automation can sometimes interfere with testing methodology.

---

## Critical Warning — Session Automation and Multi-User Testing

- Suppose Burp automatically updates every request with:

  ```text
  Latest Session Cookie
  ```  

- You intend:

  ```text
  Request 1 → User A

  Request 2 → User B
  ```  

- But automation changes both to:

  ```text
  User A
  ```

- You may incorrectly conclude:

  ```text
  User B can access User A data.
  ```

when the request actually used User A's session.

- Therefore:

  ```text
  Automation

  Must Preserve

  Testing Identity
  ```

- This is critical for authorization testing.

---

## Session Automation Is Context-Sensitive

- There is no universal rule:

  ```text
  Always use latest session.
  ```

- Sometimes you need:

  ```text
  Current User A Session
  ```

- Sometimes:

  ```text
  Current User B Session
  ```

- Sometimes:

  ```text
  Unauthenticated Request
  ```

- Sometimes:

  ```text
  Expired Session
  ```

- The correct state depends on the hypothesis.

---

## Negative Session Testing

- Sometimes stale or missing state is intentionally part of the test.

- Examples:

  ```text
  Remove Session Cookie

  Use Expired Token

  Use Session After Logout

  Reuse Old CSRF Token
  
  Replay One-Time Request
  ```

- If automation silently "fixes" these values, it may invalidate the test.

- Know when session handling should be disabled.

---

## Manual Control vs Automation

- Use manual control when you need:

  ```text
  Precise Identity

  Expired-State Testing

  Token Manipulation
  
  Logout Testing

  Session Fixation Testing

  Cross-User Comparison
  ```

- Use automation when you need:

  ```text
  Stable Authenticated Access

  Long-Running Scans
  
  Repeated Fresh Tokens

  Predictable Login Refresh
  ```

- Choose based on the security question.

---

## Professional Session Workflow

- Use this model:

  ```text
  Identify Request

  ↓
  
  Establish Fresh Baseline

  ↓

  Map State Dependencies

  ↓

  Determine Which Values Change

  ↓

  Determine Which Values Must Remain Fixed

  ↓

  Test Manually
  
  ↓

  Identify Repetition
  
  ↓

  Automate Only Required State

  ↓

  Verify Final Outgoing Requests

  ↓

  Continue Investigation
  ```

---

## State Verification Checklist

- Before interpreting a result:

  ```text
  [ ] Correct user?

  [ ] Correct role?

  [ ] Session valid?
  
  [ ] Correct cookies?

  [ ] Correct authorization token?
  
  [ ] CSRF token valid?

  [ ] Token bound to correct session?
  
  [ ] Nonce current?

  [ ] Timestamp current?

  [ ] Signature valid?

  [ ] Workflow state valid?

  [ ] Object state known?

  [ ] Automatic session handling understood?
  ```  

---

## Common Mistakes

- Avoid:

```text
If the browser works, Repeater must work.
```

- Browser and Repeater state may differ.

---

```text
A fresh cookie fixes every session problem.
```

- Other dynamic state may exist.

---

```text
401 always means bad credentials.
```

- It may indicate expired or invalid session state.

---

```text
403 always means authorization failure.
```

- CSRF or other controls may be involved.

---

```text
Use the latest cookie for every request.
```

- This can break multi-user and negative testing.

---

```text
Automate session handling immediately.
```

- Understand the manual dependency first.

---

```text
A request that works once should replay forever.
```

- Nonces, workflows, and object state may prevent this.

---

```text
Repeater tab name tells me which user is active.
```

- Verify actual authentication state.

---

## Practical Exercise

- Use a deliberately vulnerable lab or another system you are authorized to test.

### Task 1 — Identify Session State

- Log in and identify:

  ```text
  Session Cookie:

  Authorization Header:

  CSRF Token:

  Other Dynamic Values:
  ```  

---

### Task 2 — Capture Baseline

- Choose a harmless authenticated request.

- Record:

  ```text
  User:

  Endpoint:

  Session Value:

  Status:

  Expected Response:
  ```  

---

### Task 3 — Copy to Repeater

- Send the request to Repeater.

- Confirm:

  ```text
  Original Copy Works
  ```

- This establishes baseline.

---

### Task 4 — Change Browser State

- Perform one safe state change such as:

  ```text
  Logout and Login Again
  ```

- Then capture a fresh request.

- Compare:

  ```text
  Old Repeater Request

  vs

  Fresh Browser Request
  ```  

- Identify changed authentication or state values.

---

### Task 5 — Replay Old Request

- Send the old Repeater request.

- Record:

  ```text
  Status:

  Response:

  Still Valid?:

  Why?:
  ```

---

### Task 6 — Update One State Value

- If necessary, replace only the relevant stale value.

- Retest.

- Determine whether that value caused the failure.

---

### Task 7 — Identify Dynamic Dependencies

- Create:

  ```text
  Stable Values:

  Dynamic Values:
  
  Session-Bound Values:

  One-Time Values:

  Unknown Values:
  ```  

---

## Investigation Journal

```text
Application:

User / Role:

Baseline Request:

Authentication Mechanism:

Session Cookie:

Authorization Token:

CSRF Mechanism:

Other Dynamic State:

Session Rotation Observed:

Expiration Observed:

Browser State:

Burp State:

Repeater State:

Server Behavior:

Failure Observed:

State Difference Identified:

Fix:

Automation Needed?:

Reason:
```

---

## Interview Questions

1. What is session state?
2. Why do web applications need state when HTTP is request-response based?
3. What is the difference between a session identifier and session state?
4. What are common mechanisms used to maintain authentication state?
5. What is the difference between browser state and Repeater state?
6. Why can a request work in the browser but fail in Repeater?
7. What is session rotation?
8. Why might an application rotate a session after login?
9. What causes session expiration?
10. What is session invalidation?
11. Why may a CSRF token fail even when the session cookie is valid?
12. What does it mean for a CSRF token to be session-bound?
13. Why can replaying a request fail even with valid authentication?
14. What are nonces?
15. How can timestamps affect request replay?
16. Why can request signatures complicate manual testing?
17. What is workflow state?
18. How would you troubleshoot a 401 in Repeater?
19. Why should you compare a failing request with a fresh browser request?
20. Why can automatic session handling be dangerous during multi-user authorization testing?
21. When should session automation be used?
22. When is manual session control preferable?
23. Why should automation not silently replace intentionally expired or invalid tokens?
24. What should you verify before interpreting an authorization result?
25. Why should session handling be understood manually before it is automated?

---

## Quick Revision

- Application state:

  ```text
  Request

  +

  Identity

  +

  Session

  +

  Dynamic State

  +

  Workflow State
  ```  

- Four-state model:

  ```text
  Browser State

  Burp State

  Request-Copy State

  Server State
  ```  

- Remember:

  ```text
  Browser Works

  ≠
  
  Repeater Must Work
  ```

- When a request fails:

  ```text
  Capture Fresh Working Request

  ↓
  
  Compare With Failing Request

  ↓

  Identify Changed State

  ↓

  Update One Dependency

  ↓

  Retest
  ```  

- Common dynamic values:

  ```text
  Session Cookie

  Access Token
  
  CSRF Token

  Nonce
  
  Timestamp

  Signature

  Workflow ID
  ```

- Automation principle:

  ```text
  Understand Manually

  ↓

  Identify Repetition

  ↓
  
  Automate Only What Is Necessary
  ```

- Critical authorization principle:

  ```text
  Correct Identity

  Before

  Security Conclusion
  ```  

---

## Key Takeaways

* Modern applications maintain authentication, authorization, workflow, and transaction state across HTTP requests.
* Session state may be represented through cookies, tokens, CSRF values, nonces, signatures, and other mechanisms.
* Browser state, Burp state, copied-request state, and server-side state can diverge.
* A request that worked earlier may fail because its session or dynamic values became stale.
* Session identifiers may rotate after login, privilege changes, reauthentication, or other security-sensitive events.
* CSRF tokens may be session-bound, user-bound, one-time, or time-limited.
* Bearer tokens may expire while the browser automatically refreshes them, leaving Repeater with stale state.
* Request replay may also depend on workflow state, object state, timestamps, nonces, or signatures.
* Always establish a fresh authenticated baseline before interpreting security behavior.
* Verify the actual identity represented by a request before drawing authorization conclusions.
* Automatic session handling can improve long-running workflows but can also invalidate tests by replacing intentionally controlled state.
* Manual control is particularly important for multi-user authorization, expired-session, logout, and negative testing.
* Session automation should encode a workflow you already understand rather than hide an unexplained state problem.
* The foundation of reliable session handling is knowing **which values must stay current, which must remain fixed, and which you intentionally want to manipulate**.
