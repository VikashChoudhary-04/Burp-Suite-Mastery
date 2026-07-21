# Authentication Automation

> **Module 15 — Advanced Burp Workflows**

> **Progress**

```text
███████░░░░░░░░ 7 / 15 Files
```

---

## Overview

* Authenticated testing becomes difficult when authentication state changes during an assessment.

* A testing workflow may begin with a valid session but later encounter:

  * Session expiration
  * Access-token expiration
  * Cookie rotation
  * CSRF-token changes
  * Reauthentication requirements
  * Session invalidation
  * Multiple simultaneous identities

* Manually logging in again is often sufficient for short investigations.

* Longer or automated workflows may require controlled authentication automation.

* Authentication automation is not simply:

  ```text
  Automatically log in.
  ```

* A professional workflow must answer:

  ```text
  Which identity?

  When should authentication occur?

  How is login success detected?

  How is fresh state transferred?

  Which requests should receive that state?

  How do we prevent automation from changing the test itself?
  ```

* Reliable authentication automation combines:

  ```text
  Session Understanding

  +

  Session-Handling Rules

  +

  Macros or Token Refresh

  +

  Identity Verification

  +

  Controlled Scope
  ```

---

## Learning Objectives

* By the end of this lesson, you will be able to:

  * Explain the purpose of authentication automation.
  * Determine when authentication automation is appropriate.
  * Map an application's authentication lifecycle.
  * Distinguish login, session maintenance, and reauthentication.
  * Design reliable login-state detection.
  * Understand cookie and token refresh strategies.
  * Preserve identity across automated testing.
  * Manage multiple authenticated users safely.
  * Integrate authentication with Repeater, Intruder, and Scanner workflows.
  * Recognize authentication loops and session churn.
  * Protect negative authentication and authorization tests from automation.
  * Troubleshoot failed automated authentication.
  * Validate final outgoing identity.
  * Design minimal and deterministic authentication workflows.

---

## The Authentication Lifecycle

* Do not think of authentication as only:

  ```text
  Username + Password

  ↓

  Logged In
  ```

* A more realistic lifecycle is:

  ```text
  Unauthenticated

  ↓

  Authentication Initiated

  ↓

  Credentials / Identity Proof

  ↓

  Additional Security Checks

  ↓

  Authenticated Session Created

  ↓

  Session Used

  ↓

  Session Refreshed or Rotated

  ↓

  Session Expires / Is Revoked

  ↓

  Reauthentication
  ```

* Automation may need to interact with several stages.

---

## Authentication vs Session Maintenance

* Authentication and session maintenance solve different problems.

### Authentication

* Authentication establishes identity.

  ```text
  Unauthenticated

  ↓

  Login

  ↓

  Authenticated
  ```

### Session Maintenance

* Session maintenance keeps an already established identity usable.

  ```text
  Authenticated

  ↓

  Token Expires

  ↓

  Refresh

  ↓

  Still Authenticated
  ```

* Do not perform a full login when a smaller refresh operation is sufficient.

---

## Reauthentication

* Sometimes an application requires fresh authentication before allowing a sensitive action.

* Example:

  ```text
  Authenticated User

  ↓

  Change Password

  ↓

  Enter Password Again

  ↓

  Sensitive Action Allowed
  ```

* This does not necessarily mean the original session expired.

* Reauthentication is a separate security control from normal session refresh.

* Automation must distinguish:

  ```text
  Session Refresh

  from

  Reauthentication
  ```

---

## When Authentication Automation Is Useful

* Authentication automation can be useful for:

  ```text
  Long-Running Authenticated Scans

  Long Intruder Runs

  Short-Lived API Tokens

  Frequently Expiring Sessions

  Repeated Test-Account Login

  Automated CSRF Refresh

  Predictable Lab Workflows
  ```

* The goal is to maintain reliable testing state rather than automate authentication simply because automation is available.

---

## When Manual Authentication Is Better

* Prefer manual control when:

  ```text
  Only a Few Requests Need Testing

  Session Lifetime Is Long

  MFA Requires Human Interaction

  CAPTCHA Is Present

  Authentication Has High Side Effects

  Multiple Identities Need Precise Manual Control

  Expired or Invalid Sessions Are Being Tested
  ```

* Automation should solve a real workflow problem.

---

## Authentication Automation Architecture

* A useful mental model is:

  ```text
  Target Request

  ↓

  Is Required Authentication Valid?

  ├── Yes
  │   ↓
  │   Send Request
  │
  └── No
      ↓

  Authentication / Refresh Workflow

  ↓

  Obtain Fresh State

  ↓

  Verify Identity

  ↓

  Apply State to Intended Request

  ↓

  Send Request
  ```

* Each stage should be understood independently.

---

## Step 1 — Define the Identity

* Before automating anything, define:

  ```text
  Who should this workflow authenticate as?
  ```

* Example:

  ```text
  Identity:

  Standard Test User A

  Role:

  User

  Purpose:

  Authenticated API Testing
  ```

* Avoid ambiguous definitions such as:

  ```text
  Logged-in user
  ```

* Authentication should be deterministic.

---

## Identity Card

* Maintain a simple record:

  ```text
  Identity Name:

  Username:

  Role:

  Authentication Method:

  Session Mechanism:

  Token Lifetime:

  MFA:

  Expected Accessible Area:
  ```

* Do not place real passwords, tokens, or other live secrets in public documentation.

---

## Step 2 — Map the Authentication Flow

* Capture a fresh login and map the complete sequence.

  ```text
  Initial Request

  ↓

  Login Page / Authentication Endpoint

  ↓

  Dynamic Values

  ↓

  Credential Submission

  ↓

  Redirect / Additional Step

  ↓

  Session Creation

  ↓

  Identity Verification
  ```

* Example:

  ```text
  GET /login

  ↓

  csrf=A

  ↓

  POST /login

  username=user
  password=...
  csrf=A

  ↓

  Set-Cookie: session=B

  ↓

  GET /api/me

  ↓

  "user":"test-user"
  ```

* This becomes the authentication baseline.

---

## Step 3 — Identify Authentication State

* Determine what actually proves authentication.

* Possible authentication state includes:

  ```text
  Session Cookie

  Bearer Token

  JWT

  Multiple Cookies

  Access + Refresh Token

  Client Certificate

  Signed Request State
  ```

* Example:

  ```http
  Cookie: session=ABC123
  ```

* Or:

  ```http
  Authorization: Bearer eyJ...
  ```

* Do not assume the most obvious token is the only state required by the application.

---

## Step 4 — Determine Lifetime and Rotation

* Observe whether authentication state:

  ```text
  Remains Stable

  Expires After Time

  Rotates Per Request

  Rotates After Login

  Rotates After Privilege Change

  Refreshes Automatically
  ```

* Example:

  ```text
  Access Token:

  15 minutes
  ```

  ```text
  Refresh Token:

  8 hours
  ```

* This suggests:

  ```text
  Refresh Access Token

  rather than

  Full Login Every 15 Minutes
  ```

---

## Step 5 — Define Valid-Session Detection

* Automation needs a reliable way to determine whether authentication is still valid.

* Possible indicators include:

  ```text
  401 Unauthorized

  Redirect to Login

  Specific JSON Error

  Known Login Page

  Missing Authenticated Content

  Token Expiration Response
  ```

* Choose the most reliable application-specific indicator available.

---

## Weak Login-State Detection

* A poor condition would be:

  ```text
  Status != 200

  → Logged Out
  ```

* Authenticated requests may legitimately return:

  ```text
  201

  204

  302

  400

  403

  404
  ```

* Status alone may not identify session validity.

---

## Weak Content Detection

* Another poor condition is:

  ```text
  Response contains "login"
  ```

* An authenticated page may contain:

  ```text
  Last login

  Login history

  Login settings
  ```

* This can create false logout detection.

---

## Stronger Session Detection

* Prefer specific application behavior.

* Example:

  ```text
  Unauthenticated API Response:

  401

  {
    "error": "AUTHENTICATION_REQUIRED"
  }
  ```

* Or:

  ```text
  Unauthenticated Response:

  302

  Location: /login?expired=true
  ```

* Specific indicators are easier to automate reliably.

---

## Positive Identity Verification

* Instead of only asking:

  ```text
  Am I logged out?
  ```

* Also ask:

  ```text
  Who am I logged in as?
  ```

* Example:

  ```http
  GET /api/me
  ```

* Response:

  ```json
  {
    "username": "test-user-a",
    "role": "user"
  }
  ```

* This provides stronger confirmation of:

  ```text
  Authentication

  +

  Identity
  ```

---

## Why Identity Verification Matters

* Suppose automation successfully creates:

  ```text
  A valid session
  ```

* But the session belongs to:

  ```text
  User A
  ```

* While the test expects:

  ```text
  User B
  ```

* The automation technically succeeded, but the security test failed.

* Therefore:

  ```text
  Authentication Success

  ≠

  Correct Authentication Context
  ```

---

## Cookie-Based Authentication Automation

* A basic cookie-based workflow looks like:

  ```text
  Session Expired

  ↓

  Run Login Workflow

  ↓

  Receive New Cookie

  ↓

  Update Intended Requests

  ↓

  Continue Testing
  ```

* Example:

  ```http
  Set-Cookie: session=NEW123
  ```

* The important questions are:

  ```text
  Where is NEW123 stored?

  Which requests receive it?

  Which identity does it represent?
  ```

---

## Token-Based Authentication Automation

* Example:

  ```text
  Access Token A

  ↓

  Expires

  ↓

  Refresh Token R

  ↓

  New Access Token B

  ↓

  Update Authorization Header
  ```

* This is often more efficient than performing a full login.

* Conceptually:

  ```http
  POST /token/refresh

  refresh_token=R
  ```

* Response:

  ```json
  {
    "access_token": "B"
  }
  ```

* Then:

  ```http
  Authorization: Bearer B
  ```

---

## Full Login vs Token Refresh

* Prefer the smallest valid operation.

  ```text
  Access Token Expired

  ↓

  Can Refresh Token Restore Access?

  ├── Yes
  │   ↓
  │   Refresh Token
  │
  └── No
      ↓
      Full Authentication
  ```

* This reduces:

  ```text
  Traffic

  Session Churn

  Login Alerts

  Account Lockout Risk

  Complexity
  ```

---

## Authentication State Transfer

* After authentication succeeds, fresh state must reach the intended request.

* Conceptually:

  ```text
  Login Workflow

  ↓

  Fresh Session = B

  ↓

  Target Request

  Old Session = A

  ↓

  Replace / Update

  ↓

  Target Request

  Fresh Session = B
  ```

* The transfer must be:

  ```text
  Correct

  Scoped

  Predictable
  ```

---

## The Identity Preservation Rule

* Authentication automation should preserve:

  ```text
  The identity intentionally selected for the test.
  ```

* It should never silently replace:

  ```text
  User B
  ```

* With:

  ```text
  User A
  ```

* Simply because User A happens to have the newest session.

---

## Multi-User Authentication

* Authorization testing commonly requires multiple identities:

  ```text
  User A

  User B

  Admin
  ```

* Treat each identity as a separate state domain.

  ```text
  User A
  │
  ├── Credentials A
  ├── Session A
  └── Refresh Workflow A
  ```

  ```text
  User B
  │
  ├── Credentials B
  ├── Session B
  └── Refresh Workflow B
  ```

* Do not mix their authentication state.

---

## Separate Identity Workflows

* A safer design may use:

  ```text
  Login User A

  Refresh User A

  Verify User A
  ```

* Separately:

  ```text
  Login User B

  Refresh User B

  Verify User B
  ```

* This is more predictable than:

  ```text
  Use latest authenticated session
  ```

---

## Authentication Context Matrix

* Maintain a clear context matrix:

| Context | Identity           | Role            | Session Source | Automation |
| ------- | ------------------ | --------------- | -------------- | ---------- |
| A       | User A             | User            | Login A        | Controlled |
| B       | User B             | User            | Login B        | Controlled |
| Admin   | Admin Test Account | Admin           | Login Admin    | Restricted |
| Public  | None               | Unauthenticated | None           | Disabled   |

* This helps prevent accidental state mixing.

---

## Public Requests Must Stay Public

* Suppose you test:

  ```text
  GET /public/profile
  ```

* The goal is to determine whether authentication is required.

* A global session rule adds:

  ```http
  Cookie: session=VALID
  ```

* The endpoint returns:

  ```text
  200
  ```

* This tells you nothing about unauthenticated access.

* For public or negative testing:

  ```text
  Authentication Automation

  Must Be Disabled
  ```

* Unless authentication is intentionally part of the test.

---

## Authentication Bypass Testing

* Authentication tests may intentionally include:

  ```text
  No Cookie

  Invalid Cookie

  Expired Cookie

  Malformed Token

  Missing Authorization Header

  Old Session After Logout
  ```

* Automation must not repair these values.

* Otherwise:

  ```text
  Negative Test

  ↓

  Automation Adds Valid Authentication

  ↓

  False Result
  ```

---

## Logout Testing

* Goal:

  ```text
  Does logout invalidate the old session?
  ```

* Workflow:

  ```text
  Login

  ↓

  Capture session=A

  ↓

  Logout

  ↓

  Replay session=A
  ```

* If automation replaces `session=A` with:

  ```text
  session=B
  ```

* The test becomes meaningless.

* Disable authentication automation for this workflow.

---

## Session Expiration Testing

* Goal:

  ```text
  Does the session expire correctly?
  ```

* You may intentionally wait for:

  ```text
  Timeout
  ```

* Then replay the old session.

* Automatic refresh would destroy the condition being tested.

---

## Session Fixation Testing

* Goal:

  ```text
  Observe whether session identifier changes across authentication.
  ```

* Automation that automatically replaces cookies may hide the behavior.

* Manual control is preferable.

---

## Authorization Testing

* Goal:

  ```text
  Can User B access User A's object?
  ```

* Required:

  ```text
  Object belongs to User A

  +

  Request authenticates as User B
  ```

* Automation must preserve User B.

* Always verify the final outgoing identity.

---

## Authentication Automation With Repeater

* Repeater is usually hypothesis-driven.

* Examples:

  ```text
  Remove Authentication

  Swap User Session

  Replay Expired Token

  Modify Role Claims

  Test Logout State
  ```

* For this reason:

  ```text
  Manual Authentication Control

  is often preferable.
  ```

* Automation can still be useful for specific workflows, but scope it carefully.

---

## Authentication Automation With Intruder

* Intruder may benefit from authentication automation when:

  ```text
  Session Lifetime < Attack Duration
  ```

* Example:

  ```text
  Request 1–200

  Authenticated
  ```

* Then:

  ```text
  Session Expires
  ```

* Requests 201 onward return:

  ```text
  401
  ```

* This corrupts the result set.

* A controlled refresh workflow can maintain the intended session.

---

## Intruder Authentication Pattern

* A controlled pattern is:

  ```text
  Intruder Request

  ↓

  Check Authentication State

  ↓

  Valid?

  ├── Yes
  │   ↓
  │   Send
  │
  └── No
      ↓
      Refresh / Login
          ↓
      Verify Identity
          ↓
      Update Request
          ↓
      Continue
  ```

* The refresh process must not change the intended identity or payload semantics.

---

## Intruder Result Integrity

* Suppose the first 100 requests use:

  ```text
  User A
  ```

* After reauthentication, the next 100 use:

  ```text
  Admin
  ```

* The result set is no longer comparable.

* Authentication state is part of experimental consistency.

---

## Authentication Automation With Scanner

* Authenticated scanning requires:

  ```text
  Stable Identity

  Valid Session

  Correct Scope

  Predictable Application State
  ```

* If authentication expires:

  ```text
  Scanner

  ↓

  Receives Login Page

  ↓

  Continues Crawling / Auditing Wrong State
  ```

* This can create:

  ```text
  Coverage Gaps

  False Positives

  False Negatives

  Wasted Requests
  ```

---

## Scanner Identity Choice

* Before authenticated scanning, define:

  ```text
  Which role should be scanned?
  ```

* Examples:

  ```text
  Standard User

  Privileged User

  Administrator
  ```

* Different roles expose different attack surfaces.

* Do not accidentally scan with higher privileges than intended.

---

## Multiple Role Coverage

* A professional assessment may require separate authenticated contexts:

  ```text
  Unauthenticated Surface

  Standard User Surface

  Privileged User Surface

  Administrator Surface
  ```

* Do not merge all roles into one session workflow.

* Role separation improves both coverage and interpretation.

---

## Authentication Automation With Proxy

* The browser usually manages authentication itself.

* Additional automation can create divergence:

  ```text
  Browser UI:

  User B
  ```

* While Burp changes the outgoing request to:

  ```text
  User A
  ```

* The browser UI and server-side identity may no longer match.

* Use Proxy-level authentication modification only when the workflow clearly requires it.

---

## Reauthentication Triggers

* Applications may require fresh authentication after:

  ```text
  Session Timeout

  Password Change

  Privilege Change

  Sensitive Action

  Risk Event

  Token Expiration
  ```

* Map each trigger separately.

* Do not assume every authentication failure requires the same recovery workflow.

---

## Step-Up Authentication

* Example:

  ```text
  Normal Session

  ↓

  Access /settings

  ↓

  Allowed
  ```

* But:

  ```text
  Change Payment Details

  ↓

  Re-enter Password

  ↓

  Allowed
  ```

* Automation should not automatically neutralize this security boundary unless doing so is explicitly required for an authorized test.

* The step-up mechanism itself may be part of what you are assessing.

---

## MFA Considerations

* Authentication may require:

  ```text
  TOTP

  Push Approval

  SMS

  Hardware Key

  Passkey

  Biometric Interaction
  ```

* Full automation may be:

  ```text
  Impossible

  Inappropriate

  Against Engagement Constraints
  ```

* A better workflow may be:

  ```text
  Human Login

  ↓

  Capture Session

  ↓

  Automate Only Session Maintenance
  ```

---

## CAPTCHA and Bot Protection

* If authentication encounters:

  ```text
  CAPTCHA

  Bot Challenge

  Risk-Based Verification
  ```

* Do not immediately design automation to defeat it.

* First determine:

  ```text
  Is automation permitted?

  Is there a testing bypass provided?

  Is a dedicated test environment available?

  Can authentication be maintained manually?
  ```

* Respect engagement boundaries.

---

## Authentication Loops

* A common failure looks like:

  ```text
  Target Request

  ↓

  Detected as Logged Out

  ↓

  Login Macro

  ↓

  Login Request Triggers Same Rule

  ↓

  Login Macro

  ↓

  ...
  ```

* The result is:

  ```text
  Repeated Login Requests
  ```

* Prevent this through narrow rule scope and careful trigger design.

---

## False Logout Detection

* Suppose a rule assumes:

  ```text
  403 = Logged Out
  ```

* But the application returns `403` for:

  ```text
  Valid User Without Permission
  ```

* Automation logs in again.

* The request returns `403` again.

* Automation logs in again.

* The result is:

  ```text
  Authentication Loop
  ```

* The session was valid all along.

* The actual problem was authorization.

---

## Authentication vs Authorization Failure

* Always distinguish:

  ```text
  401 / Login Redirect

  Potential Authentication Failure
  ```

* From:

  ```text
  403 / Access Denied

  Potential Authorization Failure
  ```

* Status codes alone are not definitive.

* Use application-specific evidence.

---

## Session Churn

* Session churn occurs when automation repeatedly creates new sessions.

* Example:

  ```text
  Session A

  ↓

  Login

  Session B

  ↓

  Login

  Session C

  ↓

  Login

  Session D
  ```

* Potential effects include:

  ```text
  Old Sessions Invalidated

  Testing Becomes Inconsistent

  Account Alerts Triggered

  Cookie State Becomes Confusing
  ```

* Minimize unnecessary authentication.

---

## Concurrent Session Restrictions

* Some applications allow only:

  ```text
  One Active Session Per User
  ```

* If automation creates:

  ```text
  Automated Session B
  ```

* It may invalidate:

  ```text
  Manual Browser Session A
  ```

* The browser stops working.

* You log in manually again and create:

  ```text
  Session C
  ```

* Which may invalidate:

  ```text
  Automated Session B
  ```

* This can create a continuous session conflict.

* Understand concurrency rules before automation.

---

## Separate Test Accounts

* When possible, use separate accounts for different testing contexts.

* Example:

  ```text
  Browser Test User

  Automation Test User
  ```

* This can reduce session collision.

* Only do so when engagement scope and account availability permit it.

---

## Token Refresh Race Conditions

* Parallel automated requests may detect expiration simultaneously.

* Conceptually:

  ```text
  Request A → Token Expired

  Request B → Token Expired

  Request C → Token Expired
  ```

* All may trigger refresh:

  ```text
  Refresh 1

  Refresh 2

  Refresh 3
  ```

* If refresh tokens rotate, this may create failures.

* Consider concurrency when designing automated refresh workflows.

---

## Refresh Token Rotation

* Example:

  ```text
  Refresh Token R1

  ↓

  Refresh

  ↓

  Access Token A2

  Refresh Token R2
  ```

* Now:

  ```text
  R1 = Invalid
  ```

* If another process attempts to use `R1`, the refresh fails.

* Automation must understand token rotation behavior.

---

## JWT Considerations

* A JWT may contain claims such as:

  ```text
  sub

  exp

  iat

  aud

  iss

  scope

  role
  ```

* Automation should distinguish between:

  ```text
  Token Expired

  Token Invalid

  Token Wrong Audience

  Token Wrong User

  Token Lacks Scope
  ```

* Do not assume every JWT failure requires a new login.

---

## Authentication State Verification Endpoint

* A useful authenticated endpoint may be:

  ```text
  /api/me

  /profile

  /account
  ```

* Use it to verify:

  ```text
  Current Identity

  Current Role

  Authentication Validity
  ```

* Prefer a harmless endpoint with minimal side effects.

---

## Verification Should Be Cheap

* Do not use:

  ```text
  POST /create-order
  ```

* To verify whether authentication works.

* Prefer:

  ```text
  GET /api/me
  ```

* A verification request should be:

  ```text
  Safe

  Low Cost

  Predictable

  Identity-Revealing
  ```

---

## Authentication Automation Baseline

* Before enabling automation:

  ```text
  Login Manually

  ↓

  Capture Working Request

  ↓

  Verify Identity

  ↓

  Record Session State

  ↓

  Observe Expiration / Rotation
  ```

* Then build automation.

* This provides a known-good comparison.

---

## Automation Validation Workflow

* Use a controlled validation process:

  1. Disable automation.
  2. Establish a fresh manual session.
  3. Send a known authenticated request.
  4. Record the exact identity and state.
  5. Enable automation.
  6. Trigger authentication refresh.
  7. Inspect generated requests.
  8. Verify the new identity.
  9. Inspect the final target request.
  10. Compare the result with the baseline.

* Do not test authentication automation for the first time during a large scan.

---

## Logger as Authentication Evidence

* Use Logger to observe:

  ```text
  Target Request

  Authentication Requests

  Token Refresh

  Redirects

  Cookie Changes

  Final Target Request
  ```

* Ask:

  ```text
  What exact sequence produced this authenticated request?
  ```

---

## Troubleshooting — Browser Works, Automation Fails

* Use:

  ```text
  Browser Works

  Automation Fails

  ↓

  Capture Fresh Browser Login

  ↓

  Compare Authentication Sequence

  ↓

  Check Dynamic Tokens

  ↓

  Check Cookies

  ↓

  Check Redirects

  ↓

  Check MFA / CAPTCHA

  ↓

  Check Missing Headers

  ↓

  Check Final Identity
  ```

---

## Troubleshooting — Authentication Works Once

* If authentication works once and then fails:

  ```text
  Works Once

  Then Fails

  ↓

  Check One-Time CSRF

  ↓

  Check Session Rotation

  ↓

  Check Refresh Token Rotation

  ↓

  Check Workflow State

  ↓

  Check New Login Invalidating Old Session
  ```

---

## Troubleshooting — Constant Reauthentication

* Investigate:

  ```text
  Repeated Login

  ↓

  Is Session Actually Invalid?

  ↓

  Is Detection Condition Too Broad?

  ↓

  Does Verification Endpoint Return Expected Result?

  ↓

  Does New Session Reach Target Request?

  ↓

  Is Another Rule Replacing It?

  ↓

  Is Login Invalidating Previous Session?
  ```

---

## Troubleshooting — Correct Login, Wrong Final Request

* Suppose:

  ```text
  Login Macro

  User B

  Success
  ```

* But the final request contains:

  ```text
  session=User A
  ```

* Check:

  ```text
  Cookie Jar

  Session Rule Order

  Other Authentication Rules

  Extensions

  Manual Headers

  Final Outgoing Traffic
  ```

* Authentication success alone is not enough.

---

## Troubleshooting — Wrong Role

* Expected:

  ```text
  Standard User
  ```

* Observed:

  ```text
  Admin
  ```

* Stop automated testing.

* Check:

  ```text
  Credentials

  Macro Selection

  Token Source

  Shared Cookie State

  Role Mapping

  Identity Verification
  ```

* A higher-privileged session can invalidate both coverage and conclusions.

---

## Troubleshooting — Scanner Falls Back to Login Page

* If Scanner responses become mostly login-page content:

  ```text
  Scanner Responses

  ↓

  Mostly Login HTML
  ```

* Check:

  ```text
  Session Expiration

  Authentication Detection

  Refresh Workflow

  Cookie Transfer

  Rule Scope

  Identity Verification
  ```

* Do not interpret scan coverage until authenticated state is restored.

---

## Troubleshooting — Intruder Results Change Mid-Run

* A possible cause is:

  ```text
  Authentication Context Changed
  ```

* Compare:

  ```text
  Early Requests

  vs

  Late Requests
  ```

* Inspect:

  ```text
  Cookies

  Authorization Header

  Identity

  Response Type

  Session Refresh Events
  ```

---

## Troubleshooting — Manual Negative Test Keeps Authenticating

* Example:

  ```text
  Remove Cookie

  ↓

  Send

  ↓

  Request Still Authenticated
  ```

* Check whether automation:

  ```text
  Adds Cookie

  Adds Authorization Header

  Runs Login Macro

  Updates Session
  ```

* Disable relevant rules before negative testing.

---

## Authentication Automation Safety Checklist

* Before running authentication automation, verify:

  ```text
  [ ] Identity explicitly defined

  [ ] Role explicitly defined

  [ ] Manual login reproduced

  [ ] Authentication state identified

  [ ] Token/session lifetime understood

  [ ] Refresh mechanism understood

  [ ] Login-state detection is specific

  [ ] Identity verification endpoint chosen

  [ ] Rule scope is narrow

  [ ] Macro scope is narrow

  [ ] Public requests protected

  [ ] Negative tests protected

  [ ] Multi-user state isolated

  [ ] Session churn considered

  [ ] Concurrent-session behavior understood

  [ ] MFA/CAPTCHA boundaries respected

  [ ] Request volume estimated

  [ ] Final outgoing request can be inspected
  ```

---

## Authentication Automation Design Card

* Document:

  ```text
  Workflow Name:

  Purpose:

  Identity:

  Role:

  Authentication Endpoint:

  Authentication Mechanism:

  Session / Token:

  Lifetime:

  Rotation Behavior:

  Refresh Mechanism:

  Login-State Detection:

  Identity Verification Endpoint:

  Session Rule:

  Macro:

  Applicable Tools:

  Applicable Hosts / Paths:

  Negative-Test Exclusions:

  Multi-User Isolation:

  Expected Request Volume:

  Known Side Effects:

  Failure Recovery:
  ```

---

## Minimal Authentication Principle

* Prefer:

  ```text
  Refresh Access Token
  ```

* Over:

  ```text
  Full Login
  ```

* When refresh is sufficient.

* Prefer:

  ```text
  Manual Session
  ```

* Over:

  ```text
  Complex Macro
  ```

* When only a few requests need testing.

* Prefer:

  ```text
  One Narrow Rule
  ```

* Over:

  ```text
  Several Overlapping Authentication Rules
  ```

* Use the least automation required for reliability.

---

## Deterministic Authentication

* A good workflow should allow you to answer:

  ```text
  Which user?

  Which role?

  Which session?

  How was it obtained?

  When was it refreshed?

  Which requests receive it?
  ```

* If any answer is unclear, simplify the workflow.

---

## Professional Workflow

* Use:

  ```text
  Define Testing Identity

  ↓

  Map Authentication Manually

  ↓

  Identify Session / Token State

  ↓

  Observe Lifetime and Rotation

  ↓

  Choose Refresh Strategy

  ↓

  Define Reliable Failure Detection

  ↓

  Define Identity Verification

  ↓

  Build Minimal Automation

  ↓

  Scope to Required Tools / Paths

  ↓

  Test in Controlled Workflow

  ↓

  Inspect All Generated Traffic

  ↓

  Verify Final Outgoing Identity

  ↓

  Run Assessment

  ↓

  Disable Automation for Negative Tests
  ```

---

## Authentication Strategy Matrix

| Scenario                   | Preferred Approach                      |
| -------------------------- | --------------------------------------- |
| Short manual Repeater test | Manual authentication                   |
| Long-lived session         | Manual capture may be sufficient        |
| Short-lived access token   | Controlled token refresh                |
| Long Intruder run          | Automated refresh may help              |
| Authenticated Scanner      | Stable scoped authentication            |
| Two-user IDOR testing      | Strict identity separation              |
| Logout validation          | Disable auto-refresh                    |
| Expired-session testing    | Preserve stale session                  |
| Session fixation testing   | Manual cookie control                   |
| Human MFA                  | Human-in-the-loop session establishment |
| CAPTCHA                    | Use authorized testing workflow         |
| Public endpoint testing    | Keep authentication automation off      |

---

## Practical Exercise

* Use only a deliberately vulnerable lab or another system you are authorized to test.

### Task 1 — Map Authentication

* Record:

  ```text
  Login Endpoint:

  Authentication Requests:

  Dynamic Tokens:

  Cookies:

  Redirects:

  Final Session / Token:
  ```

---

### Task 2 — Verify Identity

* Choose a harmless authenticated endpoint.

* Record:

  ```text
  Verification Endpoint:

  Expected Username:

  Expected Role:

  Expected Status:
  ```

* Confirm the identity manually.

---

### Task 3 — Observe State Change

* Safely determine whether:

  ```text
  Session Rotates?

  Token Expires?

  New Login Invalidates Old Session?

  Logout Invalidates Session?
  ```

* Record your observations.

---

### Task 4 — Choose Automation Strategy

* Select one:

  ```text
  Manual Session

  Cookie Refresh

  Token Refresh

  Login Macro
  ```

* Explain why it is the minimum required approach.

---

### Task 5 — Define Scope

* Document:

  ```text
  Burp Tool:

  Host:

  Path:

  Identity:

  Requests That Must Be Excluded:
  ```

---

### Task 6 — Test Authentication Refresh

* Trigger the refresh in a controlled way.

* Verify:

  ```text
  Fresh State Obtained

  Correct Identity

  Correct Role

  Final Target Request Updated

  No Unexpected Requests
  ```

---

### Task 7 — Test a Negative Request

* Send a harmless request intentionally without authentication.

* Confirm that automation does not silently authenticate it when the test requires an unauthenticated state.

---

## Investigation Journal

* Record:

  ```text
  Application:

  Authentication Workflow:

  Identity:

  Role:

  Login Endpoint:

  Session Mechanism:

  Access Token:

  Refresh Token:

  CSRF:

  Session Lifetime:

  Rotation:

  Concurrent Session Behavior:

  Refresh Strategy:

  Login-State Detection:

  Identity Verification Endpoint:

  Automation Scope:

  Excluded Requests:

  Macro:

  Session Rule:

  Final Identity Verified:

  Unexpected Traffic:

  Negative Tests Protected:

  Known Limitations:

  Automation Reliable?:
  ```

---

## Interview Questions

1. What is authentication automation in Burp Suite?
2. When is authentication automation useful?
3. When is manual authentication preferable?
4. What is the difference between authentication and session maintenance?
5. What is reauthentication?
6. Why should the intended identity be defined before automation?
7. How would you map an application's authentication lifecycle?
8. Why should token lifetime and rotation behavior be understood?
9. What makes a good session-expiration indicator?
10. Why is checking only for `200 OK` insufficient?
11. Why should authentication automation verify identity positively?
12. What is the difference between full login and token refresh?
13. Why should the smallest valid refresh operation be preferred?
14. How can automatic authentication corrupt multi-user authorization testing?
15. Why should public endpoint tests exclude authentication automation?
16. Why should logout testing disable automatic session refresh?
17. How can authentication automation help Intruder?
18. How can session expiration affect Scanner results?
19. Why should different roles often be tested in separate authentication contexts?
20. What problems can repeated login create?
21. What is session churn?
22. How can single-session restrictions cause automation conflicts?
23. What is refresh-token rotation?
24. How can incorrect logout detection create authentication loops?
25. What should you verify in the final outgoing request before trusting an authenticated result?

---

## Quick Revision

* Authentication lifecycle:

  ```text
  Unauthenticated

  ↓

  Authenticate

  ↓

  Authenticated Session

  ↓

  Maintain / Refresh

  ↓

  Expire / Revoke

  ↓

  Reauthenticate
  ```

* Automation model:

  ```text
  Target Request

  ↓

  Authentication Valid?

  ├── Yes → Send
  │
  └── No
      ↓
      Refresh / Login
      ↓
      Verify Identity
      ↓
      Apply Fresh State
      ↓
      Send
  ```

* Critical questions:

  ```text
  Which User?

  Which Role?

  Which Session?

  Which Requests?

  Which Refresh Method?
  ```

* Prefer:

  ```text
  Token Refresh

  over

  Full Login
  ```

* When sufficient.

* For authorization testing:

  ```text
  Correct Object

  +

  Correct User Session

  =

  Valid Test
  ```

* Remember:

  ```text
  Authenticated

  ≠

  Correct Identity
  ```

  ```text
  Latest Session

  ≠

  Correct Session
  ```

  ```text
  Automation Success

  ≠

  Valid Security Test
  ```

* Professional principle:

  ```text
  Minimal Automation

  +

  Narrow Scope

  +

  Verified Identity

  +

  Controlled State

  =

  Reliable Authenticated Testing
  ```

---

## Key Takeaways

* Authentication automation should maintain a known testing identity, not merely produce any valid session.
* Authentication, session maintenance, token refresh, and reauthentication are different processes and should be handled accordingly.
* Map the complete authentication lifecycle manually before automating it.
* Identify session lifetime, token expiration, rotation, revocation, and concurrent-session behavior.
* Use specific application behavior to detect authentication failure rather than broad status-code assumptions.
* Positive identity verification is essential after automated authentication or refresh.
* Prefer token or session refresh over full login when a smaller operation is sufficient.
* Keep User A, User B, administrator, and unauthenticated contexts isolated.
* Authentication automation can invalidate authorization, logout, session-expiration, session-fixation, and other negative tests if it silently repairs authentication.
* Long-running Intruder and Scanner workflows are strong use cases for carefully scoped authentication maintenance.
* Repeated login can cause session churn, account alerts, lockouts, and invalidation of concurrent sessions.
* MFA, CAPTCHA, and third-party authentication boundaries may require human-in-the-loop workflows rather than full automation.
* Always inspect the final outgoing authentication state before trusting a result.
* Reliable authentication automation should make the answer to **"Who exactly sent this request?"** deterministic.
