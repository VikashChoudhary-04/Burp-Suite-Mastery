# Session Handling Rules

> **Module 15 — Advanced Burp Workflows**

> **Progress**

```text
█████░░░░░░░░░░ 5 / 15 Files
```

---

## Overview

* In the previous lesson, we established that requests may depend on changing state such as:

  * Session cookies
  * Access tokens
  * CSRF tokens
  * Nonces
  * Authentication workflows
  * Other dynamic values

* Manually refreshing these values is practical for small investigations.

* It becomes inefficient when:

  * Sessions expire frequently.
  * Automated tools run for long periods.
  * Authentication requires multiple requests.
  * Dynamic tokens must be refreshed repeatedly.
  * The same state-management process must be applied to many requests.

* Burp's **session-handling rules** provide a structured way to perform session-related actions before requests are sent.

* A session-handling rule can be thought of as:

  ```text
  IF

  This Request Matches Certain Conditions

  THEN

  Perform These Session Actions
  ```

* The power of session handling comes from controlling:

  ```text
  WHERE

  WHEN

  HOW

  and

  TO WHICH REQUESTS
  ```

  the rule applies.

---

## Learning Objectives

- By the end of this lesson, you will be able to:

  * Explain what session-handling rules are.
  * Understand rule scope and applicability.
  * Understand session-handling actions conceptually.
  * Apply session handling only to intended Burp tools and destinations.
  * Understand cookie-jar update workflows.
  * Understand how macros can participate in session handling.
  * Recognize rule ordering and interaction problems.
  * Prevent accidental identity replacement.
  * Preserve controlled multi-user testing.
  * Troubleshoot unexpected session modifications.
  * Validate the final outgoing request.
  * Design minimal and predictable session-handling workflows.

---

## The Core Problem

- Imagine an authenticated request:

  ```http
  GET /api/account HTTP/1.1
  Host: example.test
  Cookie: session=OLD123
  ```

- Initially:

  ```text
  OLD123 = Valid
  ```

- Later:

  ```text
  OLD123 = Expired
  ```

- A fresh login creates:

  ```text
  NEW456 = Valid
  ```

- Without session handling:

  ```text
  Stored Request

  Cookie: OLD123

  ↓

  Server

  ↓

  401 Unauthorized
  ```  

- With correctly configured session handling:

  ```text
  Stored Request

  ↓

  Session Handling

  ↓

  Cookie Updated

  ↓

  Cookie: NEW456

  ↓

  Server
  
  ↓
  
  200 OK
  ```

- This is useful when maintaining authenticated workflows.

- But the same mechanism can become dangerous if applied incorrectly.

---

## What Is a Session-Handling Rule?

- A session-handling rule defines:

  ```text
  WHEN

  a rule should apply
  ```  

- and:

  ```text
  WHAT

  Burp should do before the request is sent
  ```

- Conceptually:

  ```text
  Request

  ↓

  Does Rule Apply?
  
  ├── No
  │   ↓
  │   Send Normally  
  │
  └── Yes
      ↓
      Execute Session Action(s)
          ↓
      Send Final Request
  ```

- The rule therefore acts as conditional request preparation.

---

## Anatomy of a Session-Handling Rule

- A useful mental model:

  ```text
  Session Rule
  │
  ├── Description
  ├── Scope
  │   ├── Tools
  │   ├── Hosts
  │   ├── URLs
  │   └── Other Applicability Conditions
  │
  └── Actions
      ├── Update Cookies
      ├── Run Macro
      └── Perform Other Session Operations
  ```

- The exact available configuration depends on Burp's version and capabilities.

- The important concepts remain:

  ```text
  Applicability

  +

  Action
  ```

---

## Rule Applicability

- A rule should answer:

  ```text
  Which requests should this affect?
  ```

- Possible dimensions include:

  ```text
  Burp Tool
  
  Target Scope

  Host

  URL

  Protocol

  Path

  Request Context
  ```  

- A rule should be as narrow as practical.

---

## Why Narrow Scope Matters

- Suppose you create a rule:

  ```text
  Use latest authenticated session cookie
  ```

and apply it globally.

- You later test:

  ```text
  Unauthenticated Endpoint
  ```

- Expected:

  ```text
  No Session
  ```

- But Burp automatically adds:

  ```http
  Cookie: session=AUTHENTICATED
  ```

- Now the test is invalid.

- The rule solved one problem while silently creating another.

---

## Scope Principle

- Prefer:

  ```text
  Only the requests that require this automation
  ```

- over:

  ```text
  Everything in Burp
  ```

- This reduces:

  ```text
  Unexpected Modifications

  Cross-User Contamination

  False Conclusions

  Troubleshooting Complexity
  ```

---

## Tool Scope

- Different Burp tools may have different session requirements.

- For example:

  ```text
  Proxy

  → Browser already maintains session
  ```  

- while:

  ```text
  Intruder

  → Long-running attack may need refreshed session
  ```  

- and:

  ```text
  Scanner

  → Automated requests may need authenticated state
  ```  

- Therefore, a rule intended for Intruder may not need to affect Proxy or Repeater.

- Conceptually:

  ```text
  Rule Scope

  Tools:

  [ ] Proxy

  [ ] Repeater

  [X] Intruder

  [ ] Scanner
  ```

- Use only what the workflow requires.

---

## Why Repeater Often Needs Manual Control

- Repeater is commonly used for:

  ```text
  Precise Request Manipulation

  Multi-User Testing

  Expired-Token Testing

  Authentication Bypass Testing

  Session Fixation Investigation
  ```  

- Automatic session replacement can interfere with these tests.

- Example:

- You intentionally send:

  ```http
  Cookie: session=EXPIRED
  ```  

- A session rule silently replaces it with:

  ```http
  Cookie: session=VALID
  ```  

- The server returns:

  ```text
  200 OK
  ```

- You might incorrectly conclude:

  ```text
  Expired session still works.
  ```

- But the expired session was never actually sent.

---

## The Final-Request Principle

- Never assume:

  ```text
  The request visible before processing

  =

  The exact request sent to the server
  ```

- Advanced Burp processing may include:

  ```text
  Session Handling

  Match and Replace

  Extensions

  Tool-Specific Processing
  ```  

- The security conclusion must be based on:

  ```text
  The Final Outgoing Request
  ```

---

## Session-Handling Actions

- A rule may perform one or more actions.

- Conceptually:

  ```text
  Request

  ↓

  Action 1

  ↓

  Action 2

  ↓

  Action 3

  ↓

  Final Request
  ```  

- Common session-management goals include:

  ```text
  Update Cookies

  Refresh Authentication

  Obtain Dynamic Tokens
  
  Run Required Request Sequence

  Maintain Valid Session State
  ```

---

## Cookie Update Workflow

- One common pattern is using current cookie state.

- Conceptually:

  ```text
  Stored Request

  Cookie: OLD

  ↓

  Burp Session Handling

  ↓
  
  Current Cookie State
  
  Cookie: NEW
  
  ↓

  Final Request

  Cookie: NEW
  ```  

- This can help keep long-running authenticated workflows alive.

---

## Cookie Jar Mental Model

- Conceptually:

  ```text
  Server Response

  Set-Cookie: session=NEW

  ↓

  Burp Observes Cookie

  ↓

  Cookie State Stored

  ↓

  Later Request

  ↓
  
  Applicable Session Rule

  ↓

  Cookie Updated
  ```

- Important:

  ```text
  Cookie Stored

  ≠
  
  Every Request Automatically Uses It
  ```  

- Behavior depends on configuration and workflow.

---

## Cookie Collision

- Suppose a request already contains:

  ```http
  Cookie: session=USER_A
  ```  

- while current stored cookie state contains:

  ```text
  session=USER_B
  ```  

- If a rule updates the request:

  ```text
  Before

  USER_A
  ```  

  ```text
  After

  USER_B
  ```  

- The request identity changes.

- This is one of the most important risks in advanced Burp session handling.

---

## Identity Contamination

- Consider authorization testing:

  ```text
  User A

  session=A
  ```  

  ```text
  User B

  session=B
  ```

- You intend:

  ```http
  GET /api/user-a/document
  Cookie: session=B
  ```

- This tests whether User B can access User A's document.

- But session handling changes:

  ```text
  session=B
  ```

- to:

  ```text
  session=A
  ```

- The request succeeds.

- You conclude:

  ```text
  Authorization bypass!
  ```

- But the server actually received User A's valid session.

- The result is invalid.

---

## Critical Identity Rule

- Before interpreting any authorization test:

  ```text
  Verify the authentication state
  of the exact outgoing request.
  ```

- Never trust only:

  ```text
  Repeater Tab Name

  Expected User

  Original Captured Request

  Your Memory
  ```  

- Trust the actual request.

---

## Session Rules and Two-User Testing

- For multi-user testing, consider:

  ```text
  Separate Browser Sessions

  Separate Cookie Sets

  Clearly Labeled Repeater Tabs

  Minimal Automatic Session Handling
  ```

- If automation is necessary, isolate it carefully.

- Example:

  ```text
  Rule A

  Only applies to User A workflow
  ```  

  ```text
  Rule B

  Only applies to User B workflow
  ```

- Avoid:

  ```text
  Global Rule

  Always use most recent authenticated session
  ```

- unless that is explicitly the desired behavior.

---

## Rule Scope by Host

- Suppose authentication belongs to:

  ```text
  app.example.test
  ```  

- A broad rule might also affect:

  ```text
  api.example.test

  admin.example.test

  thirdparty.example.net
  ```

- These may use different authentication systems.

- Scope rules carefully.

- Conceptually:

  ```text
  Host:

  app.example.test
  ```

- instead of:

  ```text
  *.example.*
  ```

unless broad behavior is intentional.

---

## Rule Scope by Path

- A session rule may only be needed for:

  ```text
  /api/private/
  ```

- not:

  ```text
  /public/
  ```

- Conceptually:

  ```text
  /app/private/*

  → Session automation
  ```  

  ```text
  /public/*

  → No session automation
  ```

- Narrow path scope can prevent unwanted authentication injection.

---

## Rule Scope by Tool

- Example:

  ```text
  Proxy

  No rule
  ```

  ```text
  Repeater

  Manual state
  ```  

  ```text
  Intruder

  Automatic session refresh
  ```

  ```text
  Scanner

  Authenticated session handling
  ```

- This can create a cleaner workflow than one universal rule.

---

## Macro-Based Session Handling

- Some sessions cannot be refreshed by simply using the latest cookie.

- Example:

  ```text
  Session Expires

  ↓

  Must Visit Login Page

  ↓

  Extract CSRF Token

  ↓

  Submit Credentials

  ↓

  Receive New Session Cookie
  ```  

- This requires a request sequence.

- Conceptually:

  ```text
  Request Needs Valid Session

  ↓

  Run Login Macro

  ↓

  Obtain Fresh State

  ↓

  Update Request

  ↓

  Send Request
  ```  

- Macros are covered in depth in the next lesson.

---

## Simple Login Macro Concept

```text
GET /login

↓

Extract CSRF Token

↓

POST /login

username=...

password=...

csrf=...

↓

Set-Cookie: session=NEW
```

- Then:

  ```text
  Original Request

  ↓

  Use NEW Session
  
  ↓

  Send
  ```

- The session rule decides **when** this workflow runs.

- The macro defines **what sequence** is executed.

---

## Rule vs Macro

- Do not confuse them.

```text
SESSION-HANDLING RULE

Determines:

When should session logic apply?
```

```text
MACRO

Defines:

What request sequence should run?
```

- Together:

  ```text
  Request
  
  ↓
  
  Rule Applies

  ↓

  Macro Runs

  ↓

  State Updated

  ↓

  Request Sent
  ```

---

## Conditional Session Refresh

- Running a login macro before every request may be unnecessary.

- Example:

  ```text
  1000 Requests

  ↓

  1000 Logins
  ```

- This may cause:

  ```text
  Excessive Traffic

  Account Alerts

  Session Invalidation

  Rate Limits

  Unnecessary Load
  ```  

- A better design may refresh only when needed.

- Conceptually:

  ```text
  Request

  ↓

  Is Session Valid?

  ├── Yes
  │   ↓
  │   Send  
  │
  └── No
      ↓
      Refresh Session
          ↓
        Send
  ```

- The exact implementation depends on the application's behavior and Burp configuration.

---

## Detecting Session Invalidity

- Applications may signal invalid sessions using:

  ```text
  401 Unauthorized

  302 Redirect to /login

  Login Page Content

  "Session Expired"

  Specific JSON Error
  
  Missing Authenticated Content
  ```  

- Example:

  ```json
  {
    "error": "token_expired"
  }
  ```

- A session workflow may use recognizable behavior to determine when reauthentication is required.

---

## Detection Must Be Reliable

- Suppose the rule assumes:

  ```text
  302 = Session Expired
  ```  

- But the application also uses 302 for:

  ```text
  Normal Navigation

  Post-Login Redirects

  Business Workflow Redirects
  ```  

- The condition may be too broad.

- Prefer indicators specific to session failure.

---

## False Session Expiration Detection

- Poor condition:

  ```text
  Response contains "login"
  ```

- But an authenticated page contains:

  ```text
  Last login: 10:30
  ```  

- The rule may incorrectly interpret the response as logged out.

- Session validity detection must be precise.

---

## Session Refresh Loops

- A badly configured workflow can create:

  ```text
  Request Fails

  ↓

  Login Macro Runs

  ↓

  Login Macro Request Also Matches Rule

  ↓

  Rule Runs Again

  ↓

  Login Macro Runs Again
  
  ↓

  Loop
  ```  

- This may produce:

  ```text
  Unexpected Traffic

  Repeated Login Attempts

  Account Lockout

  Burp Performance Problems
  ```

- Scope authentication endpoints carefully to avoid recursive behavior.

---

## Recursive Rule Problem

- Conceptually:

  ```text
  Rule:

  Apply to all requests on example.test
  ```

- Macro sends:

  ```text
  POST /login  
  ```  

- But:

  ```text
  /login
  ```  

also matches the same rule.

- Result:

  ```text
  Rule

  ↓

  Macro

  ↓

  /login

  ↓

  Rule
  
  ↓

  Macro

  ↓
  
  ...
  ```

- Avoid rule recursion through precise applicability.

---

## Rule Ordering

- Multiple rules may apply to the same request.

- Example:

  ```text
  Rule 1

  Update Cookies
  ```

  ```text
  Rule 2

  Run Authentication Macro
  ```  

  ```text
  Rule 3

  Update Cookies Again
  ```

- The order can affect the final request.

- Conceptually:

  ```text
  Original Request

  ↓

  Rule 1

  ↓

  Rule 2

  ↓

  Rule 3

  ↓

  Final Request
  ```

- Understand which action happens first and what state each later action receives.

---

## Conflicting Rules

- Example:

  ```text
  Rule A

  Set session from Cookie Jar
  ```  

  ```text
  Rule B

  Run macro that creates another session
  ```

- Final identity may depend on processing order.

- Symptoms:

  ```text
  Unexpected User

  Session Changes Between Requests

  Intermittent Authentication

  Tests Work Sometimes
  ```

- Simplify rules whenever possible.

---

## Minimal-Rule Principle

- Prefer:

  ```text
  One Clear Rule

  For One Clear Purpose
  ```  

- over:

  ```text
  Many Overlapping Rules

  With Hidden Interactions
  ```  

- A rule should be explainable in one sentence.

- Example:

  ```text
  For Intruder requests to /api/private/,
  refresh the authenticated session when it expires.
  ```

- If you cannot clearly explain the rule, it may be too complex.

---

## Session Rules and Intruder

- Intruder may run longer than a session lifetime.

- Pattern:

  ```text
  Request 1

  200
  ```  

  ```text
  Request 50

  200
  ```

  ```text
  Session Expires
  ```

  ```text
  Request 51+

  401
  ```

- Without session handling, later results may be meaningless.

- A suitable workflow may:

  ```text
  Detect Expired Session

  ↓

  Refresh Authentication

  ↓

  Continue Controlled Testing
  ```  

---

## Session Rules and Scanner

- Authenticated scanning may require:

  ```text
  Stable Authentication

  Correct CSRF State

  Consistent Session Identity
  ```

- If the session expires:

  ```text
  Scanner Requests

  ↓

  Login Page

  ↓
  
  Scanner Analyzes Wrong Content
  ```

- This can reduce scan quality.

- Session handling can help maintain the intended authenticated state.

- However, verify:

  ```text
  Which Account Is Being Used?

  What Privileges Does It Have?

  Which Requests Can Change State?
  ```  

---

## Session Rules and Repeater

- Repeater often benefits from manual state control.

- Why?

  - Because Repeater is used to test hypotheses such as:

    ```text
    What if I remove authentication?

    What if I use User B's cookie?

    What if I replay an expired token?

    What if I modify the CSRF token?

    What if I send no cookie?
    ```

- Automatic session handling may undo these deliberate changes.

- Use session rules in Repeater only when they support the specific investigation.

---

## Session Rules and Proxy

- The browser usually maintains its own session state.

- Applying additional automatic session changes to Proxy traffic can create confusing behavior.

- Possible result:

  ```text
  Browser Thinks:

  User B
  ```  

- while Burp changes outgoing traffic to:

  ```text
  User A
  ```  

- The browser UI and server-side identity may diverge.

- Use carefully.

---

## Negative Testing

- Security testing often requires intentionally invalid state.

- Examples:

  ```text
  No Session

  Expired Session

  Logged-Out Session

  Wrong User Session

  Old CSRF Token

  Missing Token

  Invalid Token
  ```  

- Session automation can silently repair these values.

- Therefore:

  ```text
  Automation Must Never Override
  the State You Intentionally Want to Test
  ```

---

## Example — Testing Logout

- Goal:

  ```text
  Determine whether session remains valid after logout.
  ```

- Workflow:

  ```text
  Login

  ↓

  Capture session=A

  ↓

  Logout

  ↓

  Replay request with session=A
  ```  

- If a session rule replaces:

  ```text
  session=A
  ```  

- with:

  ```text
  session=B
  ```  

from a fresh login, the test is invalid.

- Disable or isolate automation for this test.

---

## Example — Testing Session Fixation

- You may intentionally preserve:

  ```text
  Pre-Login Session Identifier
  ```

through authentication.

- If Burp automatically replaces it:

  ```text
  Fixation Test

  ↓

  Invalidated by Automation
  ```  

- Manual control is essential.

---

## Example — Stable Intruder Authentication

- Goal:

  ```text
  Test a set of harmless parameter values
  while maintaining one authenticated identity.
  ```

- Potential workflow:

  ```text
  Intruder Request

  ↓

  Session Valid?

  ├── Yes
  │   ↓
  │   Send  
  │
  └── No
      ↓
      Reauthenticate
          ↓
      Update Session
          ↓
      Send
  ```

- This is an appropriate use of session automation.

---

## Example — CSRF Refresh

- Suppose every update request requires a fresh CSRF token.

  ```text
  GET /settings

  ↓

  Extract CSRF

  ↓

  POST /settings
  ```

- An automated workflow may need:

  ```text
  Before Each Relevant Request

  ↓

  Obtain Fresh CSRF

  ↓

  Insert Token

  ↓

  Send
  ```  

- But first verify whether:

  ```text
  Token Is One-Time

  Token Is Session-Bound

  Token Is Reusable

  Token Rotates
  ```  

- Do not automate based on assumptions.

---

## Example — API Token Refresh

- Architecture:

  ```text
  Access Token

  Expires after 15 minutes  
  ```  

  ```text
  Refresh Token

  Used to obtain new access token
  ```

- Potential workflow:

  ```text
  API Request

  ↓

  Access Token Expired

  ↓

  Call Refresh Endpoint

  ↓

  Receive New Access Token

  ↓

  Update Request

  ↓

  Retry / Continue
  ```  

- The session-handling design should preserve:

  ```text
  Correct User

  Correct Scope
  
  Correct Token Audience
  ```

---

## Authentication State Must Be Deterministic

- A professional workflow should answer:

  ```text
  For this request,
  which identity will Burp use?
  ```

- The answer should never be:

  ```text
  Probably the latest one.
  ```

- It should be:

  ```text
  User B,
  because this rule is scoped specifically
  to User B's workflow.
  ```

- Predictability is essential.

---

## Session Rule Documentation

- For important rules, record:

  ```text
  Rule Name:

  Purpose:

  Applicable Tools:

  Hosts / Paths:

  Trigger:

  Actions:

  Identity Used:

  Macro Used:

  Cookie Behavior:
  
  Expected Final State:

  Known Risks:
  ```  

- This makes complex Burp projects easier to understand later.

---

## Naming Rules Clearly

- Poor:

  ```text
  Rule 1
  ```  

- Better:

  ```text
  Refresh API User Session for Intruder
  ```

- Poor:

  ```text
  Auth Fix
  ```

- Better:

  ```text
  Refresh User-B Token for Private API Tests
  ```

- Names should explain intent.

---

## Session Rule Testing Workflow

- Before trusting a rule:

  ```text
  1. Disable Rule

  2. Capture Baseline

  3. Send Known Request

  4. Record Exact Outgoing State

  5. Enable Rule

  6. Repeat Same Request

  7. Compare Final Request
  
  8. Confirm Identity

  9. Confirm Additional Traffic

  10. Confirm Expected Behavior
  ```  

- This reveals what the rule actually changes.

---

## Use Logger for Validation

- Logger can help identify:

  ```text
  Original Request

  Macro Requests

  Authentication Requests

  Final Target Request

  Unexpected Repeated Requests
  ```  

- When testing a session rule, ask:

  ```text
  What traffic did this one action generate?
  ```

- Do not inspect only the final response.

---

## Troubleshooting — Request Uses Wrong User

```text
Wrong Identity

↓

Inspect Final Cookie / Token

↓

Check Session-Handling Rules

↓

Check Cookie State

↓

Check Macro Output

↓

Check Other Automatic Modifications

↓

Disable Rules

↓

Re-establish Baseline
```

- Do not continue authorization testing until resolved.

---

## Troubleshooting — Rule Does Not Run

```text
Rule Not Running

↓

Is Rule Enabled?

↓

Does Tool Match Scope?

↓

Does Host / URL Match?

↓

Are Applicability Conditions Correct?

↓

Does Action Require Additional Configuration?

↓

Check Logs / Traffic
```

- Start with scope.

---

## Troubleshooting — Rule Runs Everywhere

```text
Unexpected Requests Modified

↓

Inspect Rule Scope

↓

Check Tool Selection

↓

Check Host / Path Conditions

↓

Narrow Applicability

↓

Retest
```

- Avoid fixing this by adding more overlapping rules.

- Simplify first.

---

## Troubleshooting — Authentication Loop

```text
Repeated Login Requests

↓

Stop Automation

↓

Check Whether Macro Requests Match Same Rule

↓

Exclude Login / Authentication Endpoints

↓

Check Session-Validity Detection

↓

Retest Slowly
```

- Repeated authentication traffic should be investigated immediately.

---

## Troubleshooting — Session Constantly Changes

- Possible causes:

  ```text
  Application Rotates Session

  Macro Logs In Repeatedly

  Multiple Rules Update Cookies
  
  Browser and Burp Compete Over State

  Multiple Users Share Cookie State
  ```

- Isolate each source.

---

## Troubleshooting — Request Works Manually but Fails With Rule

```text
Manual Works

Automated Fails

↓

Compare Exact Outgoing Requests

↓

Check Cookie Replacement

↓

Check Dynamic Token

↓

Check Macro Sequence

↓

Check Rule Order

↓

Check Timing
```

- Do not assume the automation reproduced the manual workflow correctly.

---

## Troubleshooting — Request Works With Rule but Not Without

- This means the rule is changing something important.

- Identify exactly what:

  ```text
  Cookie?

  Token?

  Header?

  Macro Side Effect?

  Workflow State?
  ```

- Understanding the dependency is more valuable than merely leaving the rule enabled.

---

## Troubleshooting — Unexpected Extra Traffic

```text
One Request Expected

Many Requests Observed

↓

Check Session Rule

↓

Check Macro

↓

Check Retry Behavior

↓

Check Scanner / Extension Activity

↓

Use Logger

↓

Map Request Sequence
```

- Extra traffic may be legitimate automation, recursion, or misconfiguration.

---

## Rule Interaction Map

- For complex setups, document:

  ```text
  Incoming Request

  ↓

  Rule A
  Update Cookie

  ↓

  Rule B
  Check Session

  ↓

  Macro
  Login

  ↓

  Rule C
  Update Token

  ↓

  Extension
  Modify Header

  ↓

  Final Request
  ```

- If this diagram becomes difficult to explain, simplify the workflow.

---

## Disable-to-Diagnose Method

- When behavior becomes confusing:

  ```text
  Disable Session Rules

  ↓

  Disable Related Macros
  
  ↓

  Disable Relevant Automatic Modifications

  ↓

  Capture Fresh Baseline

  ↓

  Re-enable One Component

  ↓

  Retest

  ↓

  Repeat
  ```

- This isolates the source of behavior.

---

## Rule Design Checklist

- Before enabling:

  ```text
  [ ] Exact purpose defined

  [ ] Correct tool selected

  [ ] Correct host / path selected

  [ ] Authentication identity known

  [ ] Session source understood

  [ ] Macro behavior understood

  [ ] Dynamic tokens identified

  [ ] State-changing requests considered

  [ ] Recursive behavior prevented

  [ ] Negative tests protected

  [ ] Multi-user tests protected

  [ ] Final request can be verified
  ```

---

## Professional Workflow

- Use:

  ```text
  Understand Session Manually

  ↓

  Identify State That Becomes Stale

  ↓

  Determine Which Tool Needs Automation

  ↓

  Create Narrow Rule

  ↓

  Add Minimum Required Action

  ↓
  
  Test Against Known Baseline

  ↓

  Observe All Generated Traffic

  ↓

  Verify Final Identity

  ↓

  Run Controlled Assessment Workflow

  ↓

  Disable When No Longer Needed
  ```  

---

## Session Rule Strategy by Testing Type

| Testing Type                     | Typical Strategy                               |
| -------------------------------- | ---------------------------------------------- |
| Manual Repeater investigation    | Prefer manual control                          |
| Multi-user authorization testing | Strict identity isolation                      |
| Expired-session testing          | Disable automatic refresh                      |
| Logout testing                   | Preserve old session intentionally             |
| Long Intruder run                | Consider controlled refresh                    |
| Authenticated Scanner workflow   | Maintain stable intended identity              |
| One-time CSRF workflow           | Refresh only required token                    |
| Public endpoint testing          | Avoid injecting authentication unintentionally |

- No strategy is universal.

---

## Rule Complexity Warning Signs

- Your session setup may be too complex if:

  ```text
  You cannot predict which user a request uses.

  You do not know why login requests are appearing.

  Disabling one rule breaks unrelated tools.

  Several rules modify the same cookie.

  Requests behave differently each time.

  You need multiple workarounds to keep automation running.
  ```

- When this happens:

  ```text
  Return to Baseline

  ↓
  
  Simplify
  ```

---

## Practical Exercise

- Use only a deliberately vulnerable lab or another system you are authorized to test.

### Task 1 — Establish Authenticated Baseline

- Capture a harmless authenticated request.

- Record:

  ```text
  User:

  Endpoint:

  Session Cookie / Token:

  Expected Status:

  Expected Response:
  ```

- Send it manually and confirm it works.

---

### Task 2 — Identify Session Change

- Safely cause the authentication state to change, for example by logging out and back in.

- Capture a fresh request.

- Compare:

  ```text
  Old Session

  vs
  
  New Session
  ```  

---

### Task 3 — Create a Narrow Session Rule

- Design a rule conceptually for:

  ```text
  One Tool

  +

  One Host / Path
  
  +

  One Session Goal
  ```  

- Example:

  ```text
  Maintain current authenticated session
  for Intruder requests to /api/private/.
  ```

- Avoid broad scope.

---

### Task 4 — Compare Before and After

- With rule disabled:

  ```text
  Send Stored Request

  ↓

  Record Final Request
  ```

- With rule enabled:

  ```text
  Send Same Stored Request

  ↓

  Record Final Request
  ```

- Identify exactly what changed.

---

### Task 5 — Verify Identity

- Confirm:

  ```text
  Which user did the final request represent?
  ```  

- Do not rely only on whether the request returned `200`.

---

### Task 6 — Test Rule Isolation

- Send a request that should **not** match the rule.

- Confirm:

  ```text
  No Unexpected Session Modification
  ```  

---

### Task 7 — Negative-State Test

- Create a harmless test where you intentionally use:

  ```text
  No Session
  ```  

- or:

  ```text
  Old Session
  ```  

- Verify the session rule does not silently replace the value when the test requires manual state.

---

## Investigation Journal

```text
Application:

Rule Name:

Purpose:

Applicable Tools:

Host / Path Scope:

Session Mechanism:

Identity:

Original Request State:

Action Performed:

Macro Used:

Final Request State:

Additional Traffic:

Expected Behavior:

Observed Behavior:

Identity Verified:

Unexpected Modifications:

Negative Tests Protected:

Troubleshooting Notes:

Keep Rule Enabled?:
```

---

## Interview Questions

1. What is a Burp session-handling rule?
2. What are the two main conceptual parts of a session-handling rule?
3. Why should session rules be narrowly scoped?
4. Why can global session rules interfere with security testing?
5. How can tool scope affect session-handling behavior?
6. Why is automatic session handling potentially dangerous in Repeater?
7. What is the relationship between the cookie jar and session handling?
8. Why does a stored cookie not necessarily mean every request automatically uses it?
9. What is identity contamination?
10. How can automatic cookie replacement create a false authorization finding?
11. Why must the final outgoing request be verified?
12. What is the difference between a session-handling rule and a macro?
13. Why might a login macro be used with a session rule?
14. Why should authentication not necessarily run before every request?
15. What can cause recursive session-handling loops?
16. Why does rule ordering matter?
17. How can overlapping rules cause unpredictable behavior?
18. Why can session handling help long-running Intruder workflows?
19. How can expired authentication reduce Scanner effectiveness?
20. Why should session automation often be disabled for logout testing?
21. Why can automation interfere with session fixation testing?
22. How would you troubleshoot a request that suddenly uses the wrong user?
23. How would you troubleshoot repeated login requests?
24. Why is Logger useful when validating session rules?
25. What is the minimal-rule principle?

---

## Quick Revision

- Session rule:

  ```text
  IF

  Request Matches Scope

  ↓

  THEN

  Perform Session Action
  ```

- Core structure:

  ```text
  Applicability

  +

  Actions
  ```

- Scope can involve:

  ```text
  Tool

  Host

  Path

  Target Context
  ```

- Rule and macro:

  ```text
  Rule

  → When session logic applies
  ```

  ```text
  Macro

  → What request sequence runs
  ```

- Critical principle:

  ```text
  Expected Identity

  Must Equal

  Actual Outgoing Identity
  ```  

- For multi-user testing:

  ```text
  User A State

  ≠

  User B State
  ```  

- Avoid:

  ```text
  Global "Latest Session" Automation
  ```  

when identity must remain controlled.

- Troubleshooting:

  ```text
  Unexpected Behavior

  ↓

  Disable Rules

  ↓

  Capture Baseline

  ↓

  Enable One Component

  ↓

  Compare

  ↓

  Verify Final Request
  ```  

- Professional design:

  ```text
  Narrow Scope

  +

  Minimal Actions

  +

  Known Identity

  +

  Verified Traffic

  =

  Reliable Session Handling
  ```  

---

## Key Takeaways

* Session-handling rules conditionally apply session-related actions to requests before they are sent.
* The two fundamental concepts are **applicability** and **actions**.
* Rules should be scoped as narrowly as practical by tool, destination, path, and testing purpose.
* Cookie updates can maintain authenticated workflows but may also silently change request identity.
* Automatic session handling can invalidate multi-user authorization, logout, expired-session, session-fixation, and other negative tests.
* Never assume the session shown in an old request is the session that actually reached the server.
* Verify the final outgoing request before drawing security conclusions.
* Session-handling rules determine when automation applies; macros can define the multi-step request sequences used by that automation.
* Poorly scoped rules may create recursion, repeated login requests, unexpected traffic, or identity contamination.
* Overlapping rules increase complexity and should be minimized.
* Long-running Intruder or Scanner workflows are strong use cases for carefully controlled session refresh.
* Repeater often benefits from manual state control because its purpose is precise hypothesis testing.
* When session behavior becomes confusing, disable automation, establish a clean baseline, and reintroduce components one at a time.
* A professional session-handling setup should make authentication state **more predictable**, not hide how the request became authenticated.
