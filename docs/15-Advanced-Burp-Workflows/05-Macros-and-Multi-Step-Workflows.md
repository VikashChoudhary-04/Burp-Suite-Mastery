# Macros and Multi-Step Workflows

> **Module 15 — Advanced Burp Workflows**

> **Progress**

```text
██████░░░░░░░░░ 6 / 15 Files
```

---

## Overview

* Many web application actions cannot be reproduced with a single isolated HTTP request.

* A request may depend on earlier steps that establish:

  * Authentication state
  * Session cookies
  * CSRF tokens
  * Workflow identifiers
  * Temporary transaction values
  * Redirect state
  * Other dynamic data

* Burp macros allow you to define a **sequence of HTTP requests** that can be replayed as part of a session-handling workflow.

* A macro can conceptually reproduce actions such as:

  ```text
  Load Login Page

  ↓

  Obtain CSRF Token

  ↓

  Submit Credentials

  ↓

  Receive Session Cookie
  ```

* Macros become especially useful when authentication or state refresh requires multiple dependent requests.

* However, a macro should not be treated as a recording that is automatically correct forever.

* Reliable macro design requires understanding:

  ```text
  Request Order

  Dynamic Dependencies

  State Changes

  Extraction

  Parameter Updates

  Success Conditions

  Failure Conditions
  ```

---

## Learning Objectives

- By the end of this lesson, you will be able to:

  * Explain what a Burp macro is.
  * Recognize workflows that benefit from macros.
  * Distinguish macros from session-handling rules.
  * Map multi-step request dependencies.
  * Identify dynamic values between macro steps.
  * Understand token extraction and propagation.
  * Design authentication macros conceptually.
  * Handle redirects and intermediate state.
  * Recognize one-time and session-bound values.
  * Validate macro success reliably.
  * Detect stale recorded values.
  * Prevent recursion and unintended traffic.
  * Troubleshoot failed multi-step workflows.
  * Decide when macros are inappropriate.
  * Build minimal and predictable macro designs.

---

## What Is a Macro?

- A macro is an ordered sequence of one or more HTTP requests that Burp can replay.

- Conceptually:

  ```text
  Macro

  Step 1
    ↓
  Step 2
    ↓
  Step 3
    ↓
  Step 4
  ```

- Each step may contribute state required by later steps.

- Example:

  ```text
  GET /login

  ↓

  POST /login

  ↓

  GET /account
  ```  

- A macro is useful when:

  ```text
  Request B depends on Request A
  ```  

- or:

  ```text
  Request C depends on state created by A and B
  ```  

---

## Macro vs Session-Handling Rule

- These concepts work together but serve different purposes.

### Session-Handling Rule

- Answers:

  ```text
  WHEN should session logic run?
  ```

- Example:

  ```text
  For Intruder requests to /api/private/,
  run authentication logic when required.
  ```

### Macro

- Answers:

  ```text
  WHAT request sequence should run?
  ```

- Example:

  ```text
  GET /login

  ↓

  Extract CSRF

  ↓

  POST /login

  ↓

  Receive Session
  ```  

- Together:

  ```text
  Target Request

  ↓

  Session Rule Applies

  ↓

  Macro Runs

  ↓

  Session State Updated
  
  ↓

  Target Request Sent
  ```  

---

## Why Multi-Step Workflows Exist

- Applications often enforce state transitions.

- Example:

  ```text
  Step 1

  Request Password Reset

  ↓
  
  Step 2
  
  Verify Token
  
  ↓
  
  Step 3

  Choose New Password

  ↓

  Step 4

  Confirm Change
  ```  

- The server may track:

  ```text
  Current Workflow Stage

  Session

  Temporary Token

  Transaction ID

  Expiration
  ```  

- Jumping directly to Step 4 may fail.

- The workflow itself is part of the application's security model.

---

## Simple vs Dependent Macros

### Simple Macro

- Each request can run with little dynamic dependency.

  ```text
  GET /page1

  ↓

  GET /page2
  ```

### Dependent Macro

- Later requests require data from earlier responses.

  ```text
  GET /login

  ↓

  Response contains CSRF=A
  
  ↓

  POST /login

  csrf=A
  ```

- Most interesting macros involve dependencies.

---

## The Dependency Principle

- Before building a macro, map:

  ```text
  What does each request need?

  Where does that value come from?

  Does it change?

  How is it passed forward?
  ```    

- Example:

  ```text
  GET /login

  Produces:

  CSRF Token
  Session Cookie
  ```

  ```text
  POST /login

  Requires:

  CSRF Token
  Session Cookie
  Credentials
  ```  

- This is a dependency map.

---

## Dependency Map Example

```text
GET /login
│
├── Set-Cookie: preauth=ABC
│
└── csrf=TOKEN1
        │
        ▼
POST /login
│
├── Cookie: preauth=ABC
├── csrf=TOKEN1
├── username=user
└── password=...
        │
        ▼
302 /dashboard
│
└── Set-Cookie: session=AUTH123
        │
        ▼
Authenticated State
```

- Without understanding these dependencies, macro replay may fail.

---

## Start With Manual Reproduction

- Before creating a macro:

  ```text
  Perform Workflow Manually

  ↓

  Capture Requests

  ↓

  Replay Manually
  
  ↓

  Identify Dynamic Values

  ↓

  Confirm Dependencies

  ↓

  Then Automate
  ```

- Do not start by recording a workflow you do not understand.

---

## The Manual-First Rule

- A macro should encode:

  ```text
  A workflow you can already reproduce manually
  ```

- not:

  ```text
  A workflow you hope automation will somehow fix
  ```  

- If the manual sequence is unreliable, the macro will usually be unreliable too.

---

## Example — Basic Login Workflow

- Suppose authentication works as follows:

  ```http
  GET /login HTTP/1.1
  Host: example.test
  ```

- Response:

  ```http
  HTTP/1.1 200 OK
  Set-Cookie: preauth=ABC

  <input type="hidden" name="csrf" value="TOKEN123">
  ```  

- Then:

  ```http
  POST /login HTTP/1.1
  Host: example.test
  Cookie: preauth=ABC
  Content-Type: application/x-www-form-urlencoded

  username=user&password=Password123&csrf=TOKEN123
  ```

- Response:

  ```http
  HTTP/1.1 302 Found
  Set-Cookie: session=AUTH456
  Location: /dashboard
  ```

- Dependencies:

  ```text
  POST /login requires:

  preauth=ABC

  +

  csrf=TOKEN123
  ```  

- These values come from the first response.

---

## Static vs Dynamic Values

- Classify every important value.

### Static

```text
Username

Fixed Endpoint

Known Form Field Name
```

### Dynamic

```text
CSRF Token

Session Cookie

Nonce

Transaction ID

Timestamp

State Parameter
```

- A recorded macro containing dynamic values may fail when replayed unless they are refreshed correctly.

---

## The Stale-Value Problem

- Recorded request:

  ```text
  csrf=OLD123
  ```  

- Later:

  ```text
  Server expects:

  csrf=NEW456
  ```  

- Macro replays:

  ```text
  OLD123
  ```

- Result:

  ```text
  403 Invalid CSRF Token
  ```

- The macro sequence may look correct while still using stale state.

---

## Dynamic Parameter Propagation

- Conceptually:

  ```text
  Step 1 Response

  csrf=ABC

  ↓

  Extract ABC

  ↓

  Step 2 Request

  csrf=ABC
  ```  

- The important principle is:

  ```text
  Fresh Response Value

  ↓

  Correct Later Request Location
  ```  

- Possible locations include:

  ```text
  Body Parameter

  Header
  
  Cookie
  
  URL Parameter
  
  JSON Field
  ```

---

## Dynamic Value Sources

- Values may appear in:

  ```text
  HTML

  JSON

  Response Headers

  Set-Cookie

  Redirect Location

  JavaScript

  Hidden Form Fields
  ```

- Example HTML:

  ```html
  <input type="hidden" name="csrf" value="ABC123">
  ```

- Example JSON:

  ```json  
  {
    "token": "ABC123"
  }
  ```

- Example header:

  ```http
  X-CSRF-Token: ABC123
  ```

- Example redirect:

  ```http
  Location: /continue?state=ABC123
  ```

- Map the source accurately.

---

## Dynamic Value Destinations

- A value may need to be inserted into:

  ```text
  POST Body

  JSON Body

  Cookie

  Authorization Header

  Custom Header
  
  URL Query

  Path Segment
  ```  

- Example:

  ```text
  Response:

  token=ABC
  ```  

- Then:

  ```http
  Authorization: Bearer ABC
  ```  

- The source and destination may use completely different formats.

---

## Session Cookies Between Steps

- Suppose:

  ```text
  Step 1

  Set-Cookie: preauth=A
  ```  

- Then:

  ```text
  Step 2

  Requires:

  Cookie: preauth=A
  ```  

- After login:

  ```text
  Set-Cookie: session=B
  ```

- The macro must preserve the correct cookie transition.

- Conceptually:

  ```text
  Unauthenticated

  ↓

  preauth=A

  ↓

  Login

  ↓

  session=B
  ```

---

## Cookie Rotation During a Macro

- A workflow may rotate cookies several times.

- Example:

  ```text
  GET /login

  session=A

  ↓

  POST /login

  session=B

  ↓

  POST /mfa

  session=C
  ```  

- The final authenticated state is:

  ```text
  session=C
  ```  

- not:

  ```text
  session=A
  ```

- or:

  ```text
  session=B
  ```

- Track state transitions across the full sequence.

---

## Redirects

- Authentication workflows frequently use redirects.

- Example:

  ```text
  POST /login

  ↓

  302 /dashboard

  ↓

  GET /dashboard

  ↓

  200 Authenticated Page
  ```  

- The redirect may:

  ```text
  Set Cookies

  Change State

  Carry Parameters

  Trigger Additional Authentication Logic
  ```  

- Do not assume redirects are irrelevant.

---

## Redirect Dependency Example

```text
POST /login

↓

302

Set-Cookie: session=A

Location: /continue?state=B

↓

GET /continue?state=B

↓

Set-Cookie: session=C
```

- If you stop after the first response, the session may not be fully established.

---

## Authentication Success

- A macro needs a reliable concept of success.

- Possible indicators:

  ```text
  Authenticated Page Content

  Known JSON Field

  Expected Redirect

  Presence of Logout Link

  Authenticated API Response

  Expected Session Cookie
  ```

- Poor success condition:

  ```text
  Status = 200
  ```  

- because login failure pages may also return:

  ```text
  200 OK
  ```

---

## Reliable Success Detection

- Prefer a combination such as:

  ```text
  Expected Authenticated Endpoint

  +

  Expected Identity Indicator

  +

  Expected Session State
  ```  

- Example:

  ```text
  GET /api/me

  ↓

  200 OK

  ↓

  "user":"alice"
  ```  

- This provides stronger evidence than merely checking a status code.

---

## Authentication Failure Signals

- Possible signals:

  ```text
  401 Unauthorized

  403 Forbidden

  Redirect Back to Login

  "Invalid Credentials"

  "CSRF Token Invalid"

  "MFA Required"

  Account Locked

  Captcha Required
  ```  

- A macro should not treat every completed request sequence as successful authentication.

---

## Example — Login Appears Successful but Is Not

- Sequence:

  ```text
  POST /login
  
  ↓
    
  302 /dashboard
  ```  

- You assume:

  ```text
  Authenticated
  ```  

- But:

  ```text
  GET /dashboard

  ↓

  302 /login
  ```

- The first redirect alone was not sufficient proof.

- Always validate the final state.

---

## Macro Verification Request

- A useful pattern:

  ```text
  Authentication Sequence

  ↓

  Verification Request

  ↓

  Confirm Expected Identity
  ```  

- Example:

  ```text
  POST /login

  ↓

  GET /api/me

  ↓

  {
    "username": "test-user"
  }
  ```

- This helps confirm which identity the macro actually established.

---

## Identity Verification

- A successful macro must establish the **intended** identity.

- Not merely:

  ```text
  Some authenticated session
  ```  

- but:

  ```text
  The expected user and role
  ```

- This matters especially when multiple accounts are used.

---

## Multi-User Macros

- Suppose you have:

  ```text
  User A

  User B

  Admin
  ```

- Avoid a single ambiguous authentication macro that can unpredictably produce different identities.

- Prefer conceptually:

  ```text
  Macro — Login User A
  ```

  ```text
  Macro — Login User B
  ```

  ```text
  Macro — Login Admin
  ```

with clear rule scope.

- Predictability matters more than reducing macro count.

---

## Macro Naming

- Poor:

  ```text
  Macro 1
  ```

- Better:

  ```text
  Login Standard User A
  ```

- Poor:

  ```text
  Auth
  ```

- Better:

  ```text
  Refresh API Test User Session
  ```

- Names should communicate:

  ```text
  Purpose

  +

  Identity
  ```

---

## Multi-Step Login With MFA

- Example:

  ```text
  POST /login

  ↓

  MFA Challenge

  ↓

  POST /mfa

  ↓

  Authenticated Session
  ```  

- Automation depends on the MFA mechanism.

- Some factors may be:

  ```text
  Static in a Lab

  Time-Based

  Out-of-Band

  Human-Approved

  Hardware-Backed
  ```  

- Do not attempt to automate controls in ways that violate engagement authorization or invalidate the intended security boundary.

- Sometimes the correct conclusion is:

  ```text
  Full authentication automation is inappropriate.
  ```  

---

## Human-in-the-Loop Workflows

- Not every workflow should be fully automated.

- Example:

  ```text
  Login

  ↓

  Human Completes MFA

  ↓

  Capture Authenticated Session

  ↓

  Use Controlled Testing Workflow
  ```

- This may be more reliable and appropriate than trying to automate every step.

---

## Captcha and Anti-Automation

- Authentication may involve:

  ```text
  CAPTCHA

  Device Challenges

  Risk-Based Authentication
  
  Behavioral Checks
  ```

- A macro may fail because the application intentionally resists automation.

- Do not treat this simply as a technical obstacle to bypass.

- Instead determine:

  ```text
  Is automation authorized?

  Is a test account available?

  Can the engagement provide a supported authentication method?

  Is manual session refresh more appropriate?
  ```  

---

## One-Time Tokens

- A workflow may issue:

  ```text
  token=A
  ```  

which becomes invalid after use.

- Macro behavior:

  ```text
  Run 1

  A obtained

  A used

  Success
  ```  

- Run 2 cannot reuse:

  ```text  
  A
  ```

- The macro must obtain:

  ```text  
  B
  ```  

before the second use.

- Recorded one-time values must never be assumed reusable.

---

## Nonces

- A nonce may be:

  ```text
  Unique Per Request

  Unique Per Session

  Short-Lived
  
  Single-Use
  ```

- If a macro fails intermittently, check whether a nonce is being:

  ```text
  Reused

  Extracted Incorrectly

  Associated With Wrong Session

  Expired Before Use
  ```  

---

## Workflow IDs

- Example:

  ```text
  POST /checkout/start

  ↓

  workflow_id=123
  ```

- Then:

  ```text
  POST /checkout/address

  workflow_id=123
  ```

- Then:

  ```text
  POST /checkout/confirm

  workflow_id=123
  ```

- The macro must propagate the correct workflow identifier.

- A hard-coded old ID may refer to:

  ```text
  Completed Transaction

  Expired Workflow

  Another User

  Deleted State
  ```  

---

## Object Creation Dependencies

- Some workflows create an object first.

- Example:

  ```text
  POST /drafts

  ↓

  draft_id=500
  ```  

- Then:

  ```text
  PUT /drafts/500/content
  ```  

- Then:

  ```text
  POST /drafts/500/publish  
  ```  

- Every macro execution may create a new object.

- This has side effects.

---

## Side-Effect Awareness

- Before replaying a macro, ask:

  ```text
  Does this workflow:

  Create Data?
  
  Send Emails?

  Place Orders?

  Modify Accounts?

  Trigger Notifications?

  Consume Tokens?

  Change Passwords?

  Delete Resources?
  ```  

- A macro can multiply side effects quickly.

---

## Safe Macro Design

- Prefer workflows that are:

  ```text
  Idempotent

  Low Impact
  
  Reversible
  
  Test-Account Specific
  
  Within Scope
  ```

when possible.

- Avoid repeatedly automating destructive or externally visible actions unless explicitly required and authorized.

---

## Macro Request Volume

- Suppose:

  ```text
  Macro = 5 Requests
  ```  

- and it runs before:

  ```text
  1000 Intruder Requests
  ```  

- Worst-case traffic may become:

  ```text
  5000 Macro Requests

  +

  1000 Target Requests
  ```

- Automation can multiply traffic dramatically.

- Estimate total request volume before running it.

---

## Macro Frequency

- Ask:

  ```text
  Does the macro need to run:

  Before Every Request?

  Only When Session Expires?

  Once Per Tool Run?

  Only Manually?
  ```  

- Choose the least frequent execution that reliably maintains required state.

---

## Avoid Login Before Every Request

- Poor workflow:

  ```text
  Login

  ↓

  Send One Request

  ↓

  Login Again

  ↓

  Send One Request

  ↓

  Login Again
  ```  

- Potential problems:

  ```text
  Excessive Traffic

  Session Rotation

  Account Alerts

  Rate Limiting

  Old Session Invalidation

  Performance Problems
  ```  

- Prefer reauthentication only when required.

---

## Session Invalidation by New Login

- Some applications enforce:

  ```text
  New Login

  ↓

  Old Session Invalidated
  ```  

- If a macro logs in repeatedly:

  ```text
  Session A

  ↓

  Macro Login

  Session B

  A Invalid

  ↓

  Another Macro Login

  Session C

  B Invalid
  ```  

- Concurrent testing may become unstable.

- Understand session concurrency behavior first.

---

## Macro Recursion

- A dangerous configuration:

  ```text
  Session Rule

  Applies to all example.test requests
  ```

- Macro contains:

  ```text
  GET /login
  POST /login
  ```

- Those requests also trigger the same rule.

- Result:

  ```text
  Target Request

  ↓

  Rule

  ↓

  Macro

  ↓
  
  GET /login

  ↓

  Rule

  ↓

  Macro

  ↓

  ...
  ```  

- This is recursion.

---

## Preventing Recursion

- Use narrow applicability.

- Conceptually:

  ```text
  Rule Applies To:

  /api/private/*
  ```  

- Macro uses:

  ```text
  /login
  ```  

- Therefore:

  ```text
  /login

  Does Not Match Rule
  ```  

- Also verify actual generated traffic.

---

## Nested Automation

- Complex Burp setups may involve:

  ```text
  Session Rule

  ↓

  Macro

  ↓

  Extension

  ↓

  Additional Request
  
  ↓
  
  Another Rule
  ```  

- This can become difficult to reason about.

- Use the minimum automation necessary.

---

## Macro Complexity Budget

- Every additional step adds:

  ```text
  Another Request

  Another Failure Point

  Another State Transition

  Another Dynamic Dependency
  
  More Traffic

  More Debugging Complexity
  ```  

- Prefer:

  ```text
  Shortest Reliable Workflow
  ```  

- not:

  ```text
  Full Browser Journey
  ```  

- unless the full journey is actually required.

---

## Minimal Macro Principle

- Suppose login requires:

  ```text
  GET /

  GET /assets/app.js

  GET /login

  POST /login

  GET /dashboard
  ```  

- The browser generated all five.

- But the authentication dependency may require only:

  ```text
  GET /login

  ↓

  POST /login
  ```  

- Do not include irrelevant requests simply because they appeared in browser traffic.

---

## Distinguish Browser Noise

- Browser workflows may include:

  ```text
  Analytics

  Images

  JavaScript

  Fonts

  Telemetry

  Background API Calls
  ```  

- A macro should include only requests necessary for the state transition.

- Ask:

  ```text
  If I remove this step, does the workflow still succeed?
  ```  

- If yes, it may not belong in the macro.

---

## Macro Baseline

- Before automation:

  ```text
  Run Workflow Manually

  ↓

  Record Exact Necessary Requests

  ↓

  Record Dynamic Values

  ↓

  Record Final State
  ```  

- This becomes your baseline.

---

## Macro Testing Strategy

- Test incrementally.

  ```text
  Step 1 Alone

  ↓

  Does It Work?

  ↓

  Step 1 + Step 2
  
  ↓
  
  Does Dependency Propagate?

  ↓

  Add Step 3

  ↓

  Verify Final State
  ```  

- Do not debug ten steps simultaneously.

---

## Troubleshooting — Macro Fails Immediately

```text
Macro Fails

↓

Can Step 1 Run Manually?

↓

Correct Host?

↓

Correct Method?

↓

Required Cookie?

↓

Required Headers?

↓

Application State Changed?
```

- Start at the first failing step.

---

## Troubleshooting — CSRF Failure

```text
Invalid CSRF

↓

Is Token Fresh?

↓

Was Token Extracted?

↓

Correct Source?

↓

Correct Destination?

↓

Bound to Correct Session?

↓

Already Used?

↓

Correct Request Order?
```

---

## Troubleshooting — Login Fails

```text
Login Failure

↓

Credentials Correct?

↓

Pre-Login Cookie Required?

↓

CSRF Current?

↓

Hidden Parameter Missing?

↓

Redirect Required?

↓

MFA / CAPTCHA Present?

↓

Account Locked?
```

- Compare with a fresh successful browser login.

---

## Troubleshooting — Macro Works Manually but Not Automatically

```text
Manual Works

Macro Fails

↓

Compare Exact Requests

↓

Check Dynamic Extraction

↓

Check Cookie Transitions

↓

Check Request Timing

↓

Check Redirects

↓

Check Rule Interactions

↓

Check Extensions / Match-Replace
```

- The automated sequence is likely not identical to the working dependency chain.

---

## Troubleshooting — Wrong User Logged In

```text
Unexpected Identity

↓

Stop Testing

↓

Inspect Credentials Used

↓

Inspect Final Session Cookie / Token

↓

Check Shared Cookie State

↓

Check Session Rules

↓

Check Macro Selection

↓

Verify With /me Endpoint
```

- Do not continue authorization testing until identity is certain.

---

## Troubleshooting — Works Once, Then Fails

- Likely causes:

  ```text
  One-Time Token

  Nonce Reuse

  Workflow Already Completed

  Object Already Created

  Session Invalidated

  Account State Changed
  ```

- Determine what changed after the first execution.

---

## Troubleshooting — Repeated Login Requests

```text
Many Login Requests

↓

Check Macro Frequency

↓

Check Session Validity Detection

↓

Check Recursion

↓

Check Rule Scope

↓

Check Whether New Login Invalidates Previous Session
```

- Stop automation before causing account lockout or excessive traffic.

---

## Troubleshooting — Final Target Still Uses Old Session

```text
Macro Succeeds

↓

Target Request Still Old

↓

Was New Session Captured?

↓

Was Cookie State Updated?

↓

Does Session Rule Apply Correct Action?

↓

Does Another Rule Override It?

↓

Inspect Final Outgoing Request
```

- Macro success and target-request update are separate concerns.

---

## Troubleshooting — Macro Creates Too Much Traffic

```text
Unexpected Volume

↓

Count Macro Steps

↓

Check Execution Frequency

↓

Check Redirects

↓

Check Recursion

↓

Check Retries

↓

Check Background Automation
```

- Calculate:

  ```text
  Macro Requests × Number of Executions
  ```  

---

## Troubleshooting With Logger

- Use Logger to reconstruct:

  ```text
  Original Target Request

  ↓

  Macro Step 1

  ↓

  Macro Step 2

  ↓

  Macro Step 3

  ↓

  Final Target Request
  ```  

- Ask:

  ```text
  Which request failed?

  Which value changed?

  Which value should have changed but did not?

  Which identity was established?
  ```  

---

## Response Comparison

- Compare:

  ```text
  Successful Manual Step

  vs

  Failed Macro Step
  ```  

- Look at:

  ```text
  Status

  Headers

  Cookies

  Redirects

  Response Length

  Dynamic Values

  Error Messages
  ```  

- Small differences often reveal missing state.

---

## Macro Dependency Table

- For complex workflows, create:

| Step | Request       | Requires                   | Produces              |
| ---- | ------------- | -------------------------- | --------------------- |
| 1    | `GET /login`  | None                       | CSRF, preauth cookie  |
| 2    | `POST /login` | CSRF, preauth, credentials | Auth session          |
| 3    | `GET /api/me` | Auth session               | Identity confirmation |

- This makes the workflow explicit.

---

## Example — CSRF-Protected Login

```text
Step 1

GET /login
```

- Produces:

  ```text
  csrf=A

  preauth=B
  ```  

- Step 2:

  ```text
  POST /login

  Cookie: preauth=B

  csrf=A
  username=user
  password=...
  ```

- Produces:

  ```text
  session=C
  ```  

- Step 3:

  ```text
  GET /api/me

  Cookie: session=C
  ```

- Expected:

  ```text
  "user":"test-user"
  ```  

- This is a strong macro design because success and identity are verified.

---

## Example — Token Refresh Macro

- Architecture:

  ```text
  Access Token A

  ↓

  Expires

  ↓

  POST /token/refresh

  refresh_token=R

  ↓

  Access Token B
  ```

- Macro goal:

  ```text
  Refresh Only the Access Token
  ```  

- not:

  ```text
  Perform Full Login Every Time
  ```

- Prefer the smallest reliable state-refresh workflow.

---

## Example — Workflow Token

```text
GET /transfer/new

↓

transaction_token=A

↓

POST /transfer/preview

token=A

↓

confirmation_token=B

↓

POST /transfer/confirm

token=B
```

- This macro has multiple dynamic dependencies.

- Because it may cause a real transfer, automated replay could be dangerous.

- A safe testing environment and explicit authorization are essential.

---

## Example — Password Reset Workflow

```text
POST /forgot-password

↓

Email Sent

↓

Reset Token

↓

POST /reset-password
```

- This workflow depends on an out-of-band email channel.

- A fully automatic macro may be inappropriate or impractical.

- Use a test environment or approved workflow where required.

---

## Example — OAuth-Like Flow

- Conceptually:

  ```text
  Application

  ↓

  Authorization Endpoint
  
  ↓

  State Parameter
  
  ↓

  Callback
  
  ↓

  Authorization Code

  ↓

  Token Exchange

  ↓

  Authenticated Session
  ```  

- Such flows may involve:

  ```text
  Multiple Domains

  Redirects

  State Parameters

  One-Time Codes

  External Identity Providers
  ```  

- Do not automate broadly without understanding scope and authorization boundaries.

---

## Cross-Domain Workflows

- A macro may contact:

  ```text
  app.example.test

  auth.example.test

  identity-provider.example
  ```  

- Before automation, verify:

  ```text
  Which domains are in scope?

  Which are third-party?

  Which requests are safe to replay?
  ```  

- Authentication dependencies do not automatically grant testing authorization against every participating domain.

---

## Macro Security Boundaries

- Always distinguish:

  ```text
  Needed to authenticate to target
  ```  

- from:

  ```text
  Authorized to actively test
  ```  

- A third-party identity provider may be required for login while remaining out of scope for security testing.

---

## Macro Data Handling

- Macros may contain:

  ```text
  Usernames

  Passwords

  Tokens

  Cookies

  API Secrets

  MFA Data
  ```

- Protect Burp project files and screenshots accordingly.

- Do not commit real credentials or session values to public repositories.

- Use placeholders in documentation:

  ```text
  <TEST_USERNAME>

  <TEST_PASSWORD>

  <SESSION_TOKEN>
  ```  

---

## Credential Management

- Prefer:

  ```text
  Dedicated Test Accounts

  Least Required Privilege

  Engagement-Approved Credentials
  ```  

- Avoid unnecessary use of:

  ```text
  Personal Accounts

  Production Administrator Accounts

  Shared Sensitive Credentials
  ```

unless explicitly required and authorized.

---

## Macro Documentation

- Record:

  ```text
  Macro Name:

  Purpose:

  Identity:

  Trigger:

  Step 1:

  Step 2:

  Step 3:

  Dynamic Values:

  Cookie Transitions:

  Success Condition:
  
  Failure Condition:

  Side Effects:
  
  Expected Request Count:

  Scope:
  
  Known Limitations:
  ```  

- This makes automation reproducible.

---

## Macro Validation Checklist

- Before using a macro in a larger workflow:

  ```text
  [ ] Manual workflow works

  [ ] Required steps identified

  [ ] Unnecessary browser traffic removed

  [ ] Dynamic values identified

  [ ] Dynamic values propagated correctly

  [ ] Cookie transitions understood

  [ ] Redirects understood

  [ ] Final identity verified

  [ ] Success condition reliable

  [ ] Failure condition understood

  [ ] Side effects reviewed

  [ ] Request volume estimated

  [ ] Rule recursion prevented

  [ ] Scope verified

  [ ] Credentials protected
  ```

---

## Professional Macro Workflow

- Use:

  ```text
  Define Goal

  ↓
  
  Perform Workflow Manually

  ↓

  Capture Requests

  ↓

  Remove Irrelevant Requests

  ↓

  Map Dependencies

  ↓

  Classify Static / Dynamic Values

  ↓

  Build Minimal Sequence

  ↓

  Configure Dynamic Propagation

  ↓

  Test Each Step

  ↓

  Verify Final State

  ↓

  Integrate With Narrow Session Rule

  ↓

  Observe Full Traffic

  ↓

  Document
  ```

---

## When Not to Use a Macro

- Avoid unnecessary macros when:

  ```text
  Session Is Long-Lived

  Manual Refresh Is Easy

  Only a Few Requests Need Testing

  Workflow Has Dangerous Side Effects

  Human MFA Is Required

  Third-Party Boundaries Complicate Automation

  Application State Is Highly Unstable
  ```  

- Sometimes:

  ```text
  Manual Control

  >

  Complex Automation
  ```  

---

## Macro Decision Tree

```text
Does Request Require Multi-Step State?

├── No
│   ↓
│   Macro Probably Unnecessary
│
└── Yes
    ↓

Can Workflow Be Reproduced Manually?

├── No
│   ↓
│   Understand Workflow First
│
└── Yes
    ↓

Are Dynamic Dependencies Known?

├── No
│   ↓
│   Map Them
│
└── Yes
    ↓

Can Workflow Be Safely Replayed?

├── No
│   ↓
│   Use Manual / Alternative Method
│
└── Yes
    ↓

Build Minimal Macro

↓

Verify Final Identity and State

↓

Integrate Carefully
```

---

## Common Mistakes

- Avoid:

  ```text
  Recording every browser request into a macro.
  ```  

- Include only necessary requests.

---

```text
Hard-coding dynamic CSRF tokens.
```

- Fresh values may be required.

---

```text
Assuming 200 means login succeeded.
```

- Validate authenticated identity.

---

```text
Ignoring redirects.
```

- They may establish required state.

---

```text
Running login before every request.
```

- This may create unnecessary traffic and instability.

---

```text
Ignoring session rotation.
```

- The final cookie may differ from the first authenticated cookie.

---

```text
Using one ambiguous macro for multiple users.
```

- Identity should remain deterministic.

---

```text
Automating workflows with destructive side effects.
```

- Assess impact first.

---

```text
Ignoring third-party authentication boundaries.
```

- Authentication dependency does not equal testing authorization.

---

```text
Debugging the entire macro at once.
```

- Test step by step.

---

## Practical Exercise

- Use a deliberately vulnerable lab or another system you are authorized to test.

### Task 1 — Capture a Multi-Step Workflow

- Choose a safe workflow such as:

  ```text
  Load Login Page

  ↓

  Submit Login
  
  ↓

  Open Profile
  ```  

- Record the requests.

---

### Task 2 — Remove Noise

- Identify requests that are not required:

  ```text
  Images

  Analytics
  
  Fonts

  Static JavaScript

  Unrelated Background Calls
  ```

- Keep only the minimum sequence.

---

### Task 3 — Build Dependency Map

- For each step:

  ```text
  Request:

  Requires:

  Produces:

  Dynamic Values:

  Cookies:
  ```  

---

### Task 4 — Identify Static and Dynamic Values

- Create:

  ```text
  Static:

  Dynamic:
  
  Session-Bound:

  One-Time:

  Unknown:
  ```  

---

### Task 5 — Replay Manually

- Replay the minimal sequence manually.

- Confirm:

  ```text
  Workflow Still Works
  ```  

- If it does not, identify the missing dependency.

---

### Task 6 — Validate Final Identity

- Use a harmless endpoint such as:

  ```text
  /profile
  ```

- or:

  ```text
  /api/me
  ```

- Confirm the expected user.

---

### Task 7 — Estimate Automation Impact

- Record:

  ```text
  Requests Per Macro Run:

  Expected Macro Executions:
  
  Estimated Total Requests:

  Potential Side Effects:
  ```

---

## Investigation Journal

```text
Application:

Macro Name:

Purpose:

Identity:

Trigger:

Manual Workflow Confirmed:

Required Steps:

Removed Browser Noise:

Step 1 Requires:

Step 1 Produces:

Step 2 Requires:

Step 2 Produces:

Dynamic Tokens:

Cookie Transitions:

Redirects:

Final Verification Request:

Expected Identity:

Success Condition:

Failure Condition:

Side Effects:

Requests Per Run:

Scope Boundaries:

Recursion Risk:

Known Limitations:

Automation Appropriate?:
```

---

## Interview Questions

1. What is a Burp macro?
2. What is the difference between a macro and a session-handling rule?
3. Why should a workflow be reproduced manually before creating a macro?
4. What is a request dependency?
5. What is the difference between static and dynamic macro values?
6. Why can a recorded CSRF token cause a macro to fail later?
7. Where can dynamic values appear in HTTP responses?
8. Where might extracted values need to be inserted?
9. Why are cookie transitions important in multi-step authentication?
10. How can session rotation affect a macro?
11. Why should redirects be analyzed during authentication automation?
12. Why is `200 OK` insufficient proof that authentication succeeded?
13. How can a macro verify the final authenticated identity?
14. Why should separate users often have separate authentication macros?
15. Why may MFA prevent full authentication automation?
16. What are one-time tokens and how do they affect replay?
17. Why can macros create dangerous side effects?
18. How can macro request volume become unexpectedly large?
19. Why should a login macro not necessarily run before every request?
20. What is macro recursion?
21. How can narrow session-rule scope prevent recursion?
22. Why should browser-generated noise be removed from macros?
23. What is the minimal macro principle?
24. How would you troubleshoot a macro that works manually but fails automatically?
25. When should you choose manual session handling instead of a macro?

---

## Quick Revision

- Macro:

  ```text
  Ordered Request Sequence
  ```

- Rule:

  ```text
  Determines When Macro Runs
  ```

- Core dependency model:

  ```text
  Step 1

  Produces Value

  ↓

  Step 2

  Consumes Value
  ```  

- Before automation:

  ```text
  Manual Reproduction

  ↓

  Dependency Mapping

  ↓

  Minimal Sequence

  ↓

  Dynamic Value Handling

  ↓

  Final Verification
  ```

- Classify values:

  ```text
  Static

  Dynamic

  Session-Bound

  One-Time
  ```

- Authentication macro:

  ```text
  Login Sequence

  ↓

  Fresh State

  ↓

  Verify Identity

  ↓

  Use State
  ```

- Remember:

  ```text
  Macro Completed

  ≠

  Authentication Confirmed
  ```  

  ```text
  200 OK

  ≠

  Correct User
  ```

  ```text
  Recorded Value

  ≠

  Reusable Value
  ```

  ```text
  Browser Request
  
  ≠

  Necessary Macro Step
  ```  

- Professional principle:

  ```text
  Shortest Reliable Workflow

  +

  Correct Dynamic State

  +

  Verified Identity

  +

  Controlled Side Effects

  =

  Reliable Macro
  ```  

---

## Key Takeaways

* Burp macros replay ordered HTTP request sequences and are useful when a request depends on earlier application state.
* Session-handling rules determine when session logic applies; macros define the request sequence that performs that logic.
* Multi-step workflows should be understood and reproduced manually before automation.
* Every important value should be classified as static, dynamic, session-bound, one-time, or otherwise state-dependent.
* Dynamic values may need to be extracted from one response and propagated into later requests.
* Cookies may rotate several times during authentication, so the final authenticated session must be identified correctly.
* Redirects can carry parameters, set cookies, or complete state transitions and should not automatically be ignored.
* A completed macro or `200 OK` response does not prove successful authentication; verify the final identity explicitly.
* Separate macros can help preserve deterministic identity during multi-user testing.
* MFA, CAPTCHA, external identity providers, and human approval steps may make full automation inappropriate.
* Macros can create real side effects and multiply request volume, especially when executed repeatedly by automated tools.
* Avoid unnecessary login attempts; refresh state only as often as required.
* Prevent recursion by ensuring macro requests do not unintentionally trigger the same session-handling rule.
* Include only the shortest request sequence required to reproduce the state transition.
* Debug macros incrementally and compare failed automated steps with known-good manual requests.
* A reliable macro should make a workflow **more deterministic, observable, and reproducible—not merely more automated**.
