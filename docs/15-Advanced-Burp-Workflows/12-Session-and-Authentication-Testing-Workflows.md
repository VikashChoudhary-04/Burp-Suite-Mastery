# Session and Authentication Testing Workflows

> **Module 15 — Advanced Burp Workflows**

> **Progress**

```text
█████████████░░ 13 / 15 Files
```

---

## Overview

* Authentication and session management determine:

  ```text
  Who the User Is

  +

  Whether the User Is Authenticated

  +

  Which Session Belongs to the User

  +

  How Long Authentication Remains Valid

  +

  How Authentication State Changes
  ```

* These mechanisms protect some of the most security-sensitive boundaries in a web application.

* A typical authentication lifecycle may look like:

  ```text
  Unauthenticated User

  ↓

  Login

  ↓

  Authentication Verification

  ↓

  Session Created

  ↓

  Authenticated Requests

  ↓

  Session Refresh / Rotation

  ↓

  Logout

  ↓

  Session Invalidated
  ```

* Vulnerabilities can occur at any stage.

* Examples include:

  * Username enumeration.
  * Weak login controls.
  * Authentication bypass.
  * Session fixation.
  * Predictable session identifiers.
  * Missing session rotation.
  * Improper logout invalidation.
  * Concurrent-session weaknesses.
  * Weak remember-me functionality.
  * Password-reset flaws.
  * MFA bypasses.
  * Token reuse.
  * Session confusion.
  * JWT validation weaknesses.
  * Cross-account session handling errors.

* Burp Suite is particularly effective for authentication testing because it allows you to:

  ```text
  Capture

  Modify

  Replay

  Compare

  Automate Selected Tests

  Track Session State

  Analyze Tokens
  ```

* A professional workflow is:

  ```text
  Map Authentication

  +

  Establish Identities

  +

  Capture Session Lifecycle

  +

  Test State Transitions

  +

  Manipulate One Variable

  +

  Verify Server-Side Identity

  +

  Test Invalidation

  +

  Reproduce Security Impact
  ```

---

## Learning Objectives

* By the end of this lesson, you will be able to:

  * Map an application's authentication surface.
  * Understand the complete session lifecycle.
  * Establish clean authenticated and unauthenticated baselines.
  * Use multiple controlled accounts during testing.
  * Test login behavior systematically.
  * Investigate username enumeration.
  * Analyze session identifiers.
  * Test session rotation after authentication.
  * Understand session fixation.
  * Verify logout invalidation.
  * Test password-change session behavior.
  * Investigate concurrent sessions.
  * Analyze remember-me functionality.
  * Test password-reset workflows.
  * Investigate MFA workflows.
  * Analyze authentication tokens and JWTs.
  * Detect session confusion and cross-account issues.
  * Use Burp tools effectively during authentication testing.
  * Avoid false positives caused by stale client state.
  * Build a repeatable authentication and session testing methodology.

---

## Authentication vs Authorization

* Authentication answers:

  ```text
  Who are you?
  ```

* Authorization answers:

  ```text
  What are you allowed to do?
  ```

* Example:

  ```text
  Login Successfully

  =

  Authentication
  ```

* Accessing:

  ```text
  /admin
  ```

* Only as an administrator is:

  ```text
  Authorization
  ```

* These controls are related but should be tested separately.

---

## What Is a Session?

* HTTP is fundamentally stateless.

* A server needs a mechanism to associate multiple requests with the same authenticated user.

* Commonly:

  ```text
  Login

  ↓

  Server Creates Session

  ↓

  Session Identifier Returned

  ↓

  Browser Sends Identifier With Requests

  ↓

  Server Maps Identifier to User
  ```

* Example:

  ```http
  Cookie: session=abc123xyz
  ```

---

## Session Lifecycle

* A useful mental model is:

  ```text
  No Session

  ↓

  Pre-Authentication Session

  ↓

  Login

  ↓

  Authenticated Session

  ↓

  Privilege / Security Changes

  ↓

  Session Rotation or Continuation

  ↓

  Logout / Expiry

  ↓

  Invalid Session
  ```

* Test each transition independently.

---

## Authentication Attack Surface

* Map all authentication-related functionality.

* Look for:

  ```text
  Login

  Registration

  Logout

  Password Change

  Forgot Password

  Password Reset

  Email Verification

  MFA Enrollment

  MFA Verification

  MFA Recovery

  Remember Me

  Device Trust

  SSO

  OAuth / OIDC

  API Authentication

  Session Refresh

  Account Recovery
  ```

* Do not treat `/login` as the entire authentication surface.

---

## Build an Authentication Map

* Record:

  ```text
  Login Endpoint:

  Logout Endpoint:

  Registration Endpoint:

  Password Change Endpoint:

  Password Reset Request:

  Password Reset Completion:

  MFA Endpoint:

  Session Cookie:

  Authentication Header:

  Refresh Endpoint:

  Remember-Me Cookie:

  SSO Provider:

  Token Type:

  Session Expiry:
  ```

---

## Establish Test Identities

* Ideally use at least:

  ```text
  User A

  User B
  ```

* If roles exist:

  ```text
  Normal User

  Privileged User

  Administrator
  ```

* Keep identities clearly separated.

* Example:

  ```text
  Browser Profile A → User A

  Browser Profile B → User B
  ```

* Or maintain separate Burp Repeater tabs with clearly labeled sessions.

---

## Why Multiple Accounts Matter

* Two accounts allow you to distinguish:

  ```text
  Session Behavior

  from

  Account-Specific Behavior
  ```

* They also help detect:

  ```text
  Session Swapping

  Cross-Account Access

  Token Binding Issues

  Authentication Confusion
  ```

---

## Establish an Unauthenticated Baseline

* Before logging in, capture:

  ```text
  Protected Endpoint

  ↓

  Request Without Session

  ↓

  Response
  ```

* Record whether the application returns:

  ```text
  401 Unauthorized

  403 Forbidden

  Redirect to Login

  Public Content

  Generic Error
  ```

---

## Establish an Authenticated Baseline

* Log in normally.

* Capture:

  ```text
  Login Request

  Login Response

  Session Cookie

  Redirect

  First Authenticated Request
  ```

* Record:

  ```text
  Session Before Login:

  Session After Login:

  Cookies Added:

  Cookies Changed:

  Tokens Issued:

  Authentication State:
  ```

---

## Login Workflow Mapping

* A login may involve more than one request.

* Example:

  ```text
  GET /login

  ↓

  CSRF Token Issued

  ↓

  POST /login

  ↓

  Credentials Verified

  ↓

  MFA Challenge

  ↓

  Session Upgraded

  ↓

  Redirect /dashboard
  ```

* Capture the entire sequence.

---

## Login Testing Workflow

* Use:

  ```text
  Valid Username + Valid Password

  ↓

  Valid Username + Invalid Password

  ↓

  Invalid Username + Invalid Password

  ↓

  Empty Fields

  ↓

  Modified Request Structure

  ↓

  Rate-Limit Observation

  ↓

  Response Comparison
  ```

* Avoid unnecessary brute force.

---

## Username Enumeration

* Username enumeration occurs when an application reveals whether an account exists.

* Example:

  ```text
  Existing User:

  Incorrect password
  ```

* Compared with:

  ```text
  Unknown User:

  Account does not exist
  ```

* This reveals:

  ```text
  Account Existence
  ```

---

## Enumeration Signals

* Differences may appear in:

  ```text
  Response Message

  Status Code

  Response Length

  Redirect

  Timing

  Headers

  Lockout Behavior

  Password Reset Behavior
  ```

* Do not inspect only visible error text.

---

## Enumeration Testing Workflow

* Compare:

  ```text
  Known Valid Username

  vs

  Known Invalid Username
  ```

* Keep constant:

  ```text
  Password

  Request Structure

  Headers

  Session State
  ```

* Compare:

  ```text
  Status

  Length

  Body

  Timing

  Redirect
  ```

---

## Timing-Based Enumeration

* Some applications return identical messages but perform different backend work.

* Example:

  ```text
  Existing Account

  ↓

  Expensive Password Hash Verification
  ```

* Versus:

  ```text
  Invalid Account

  ↓

  Early Rejection
  ```

* This may produce measurable timing differences.

* Timing tests require:

  ```text
  Multiple Samples

  +

  Stable Conditions

  +

  Statistical Caution
  ```

* A single slow response proves nothing.

---

## Login Rate Limiting

* Determine whether repeated failed authentication attempts trigger:

  ```text
  Delay

  CAPTCHA

  Temporary Lockout

  Account Lockout

  IP-Based Limiting

  Device-Based Limiting

  Generic Throttling
  ```

* Use minimal request counts.

* Do not intentionally lock real user accounts.

---

## Rate-Limit Scope

* Ask:

  ```text
  Per Account?

  Per IP?

  Per Session?

  Per Device?

  Global?

  Combination?
  ```

* Weak scope can sometimes allow protections to be bypassed.

* Test only within authorization and safe operational limits.

---

## Session Identifier Analysis

* Identify all values that may represent session state.

* Examples:

  ```text
  session

  sessionid

  JSESSIONID

  PHPSESSID

  ASP.NET_SessionId

  connect.sid

  auth_token

  access_token
  ```

* Do not assume every cookie is authentication-related.

---

## Session Token Properties

* A secure session identifier should generally be:

  ```text
  Unpredictable

  Sufficiently Random

  Unique

  Server-Validated

  Properly Scoped

  Properly Expired
  ```

* Burp Sequencer can assist with randomness analysis when appropriate.

---

## Cookie Security Attributes

* Inspect:

  ```text
  Secure

  HttpOnly

  SameSite

  Domain

  Path

  Expires

  Max-Age
  ```

* Example:

  ```http
  Set-Cookie: session=abc123;
  Secure;
  HttpOnly;
  SameSite=Lax
  ```

---

## `Secure`

* The `Secure` attribute instructs browsers to send the cookie only over secure HTTPS connections.

* Missing `Secure` can increase exposure where HTTP access or downgrade conditions exist.

---

## `HttpOnly`

* `HttpOnly` prevents normal client-side JavaScript access to the cookie.

* This can reduce session-cookie theft through certain XSS scenarios.

* It does not prevent XSS itself.

---

## `SameSite`

* Common values:

  ```text
  Strict

  Lax

  None
  ```

* `SameSite` influences whether cookies are included in cross-site requests.

* It is relevant to:

  ```text
  CSRF

  Authentication Flows

  SSO

  Cross-Site Navigation
  ```

---

## Cookie Scope

* Review:

  ```text
  Domain

  Path
  ```

* Overly broad scope may expose cookies to more hosts or paths than necessary.

* Example:

  ```text
  Domain=.example.test
  ```

* May allow multiple subdomains to receive the cookie.

---

## Session Rotation

* A secure application should often issue a new session identifier after important authentication-state changes.

* Especially:

  ```text
  Login

  Privilege Elevation

  Password Change

  MFA Completion

  Sensitive Account Recovery
  ```

* Compare session values before and after the transition.

---

## Session Rotation Test

* Record:

  ```text
  Session Before Login:

  ABC
  ```

* Login.

* Record:

  ```text
  Session After Login:

  XYZ
  ```

* Expected:

  ```text
  ABC ≠ XYZ
  ```

* If the same identifier remains, investigate session fixation risk.

---

## Session Fixation

* Session fixation occurs when an attacker can cause a victim to authenticate using a session identifier known or controlled by the attacker.

* Conceptually:

  ```text
  Attacker Obtains Session ID

  ↓

  Victim Uses Same Session ID

  ↓

  Victim Logs In

  ↓

  Session ID Remains Unchanged

  ↓

  Attacker Reuses Known ID

  ↓

  Victim Session Access
  ```

---

## Session Fixation Testing Workflow

* In an authorized test environment:

  ```text
  Obtain Pre-Login Session

  ↓

  Record Identifier

  ↓

  Authenticate

  ↓

  Compare Post-Login Identifier

  ↓

  Test Whether Old Identifier Remains Authenticated
  ```

* A session remaining numerically unchanged does not alone prove exploitability.

* Validate whether the pre-authentication identifier can actually be controlled and reused.

---

## Session Invalidation

* Sessions should become invalid when appropriate.

* Important events include:

  ```text
  Logout

  Password Reset

  Password Change

  Account Disable

  Security-Sensitive Recovery

  Administrator Session Revocation
  ```

* Test the old token directly.

---

## Logout Testing

* Workflow:

  ```text
  Login

  ↓

  Capture Authenticated Request

  ↓

  Save Session Token

  ↓

  Logout

  ↓

  Replay Saved Authenticated Request
  ```

* Expected:

  ```text
  Old Session Rejected
  ```

* Vulnerable behavior:

  ```text
  Old Session Still Authenticated
  ```

---

## Client-Side Logout vs Server-Side Logout

* Some applications only delete the browser cookie.

* Example:

  ```text
  Browser Cookie Deleted

  but

  Server Session Still Valid
  ```

* Replaying the old cookie manually may restore authentication.

* Therefore:

  ```text
  Cookie Disappeared

  ≠

  Session Invalidated
  ```

---

## Password Change Session Testing

* After changing the password, test:

  ```text
  Current Session

  Other Existing Sessions

  Remember-Me Tokens

  Refresh Tokens

  API Tokens
  ```

* Expected behavior depends on application policy.

* Security-sensitive applications may invalidate other sessions.

---

## Password Reset Session Testing

* After a successful password reset, determine:

  ```text
  Are Old Sessions Still Valid?

  Are Remember-Me Tokens Still Valid?

  Are Refresh Tokens Revoked?

  Is Reset Token Invalidated?
  ```

* A password reset intended for account recovery may be weakened if an attacker-controlled existing session remains active indefinitely.

---

## Concurrent Sessions

* Determine whether multiple sessions are allowed.

* Example:

  ```text
  Login Browser A

  ↓

  Login Browser B

  ↓

  Test Browser A
  ```

* Possible policies:

  ```text
  Both Sessions Allowed

  Old Session Invalidated

  User Notified

  Device Management Available
  ```

* Evaluate behavior against the application's intended security model.

---

## Session Revocation

* Applications may provide:

  ```text
  Log Out All Devices

  Revoke Session

  Remove Trusted Device
  ```

* Test whether the revoked session actually loses access.

* Workflow:

  ```text
  Session A

  +

  Session B

  ↓

  Revoke Session B

  ↓

  Replay Session B Request

  ↓

  Verify Rejection
  ```

---

## Remember-Me Functionality

* Remember-me features often use long-lived authentication tokens.

* Identify:

  ```text
  Cookie Name

  Token Format

  Expiration

  Rotation Behavior

  Device Binding

  Revocation Behavior
  ```

* Long-lived tokens deserve careful testing because they may remain valid after normal sessions expire.

---

## Remember-Me Testing Workflow

* Use:

  ```text
  Login With Remember Me

  ↓

  Capture Long-Lived Token

  ↓

  Close / Expire Normal Session

  ↓

  Reuse Remember-Me Token

  ↓

  Observe Authentication

  ↓

  Logout

  ↓

  Test Token Again
  ```

* Also test behavior after:

  ```text
  Password Change

  Password Reset

  Log Out All Devices
  ```

---

## Password Reset Workflow Mapping

* Typical flow:

  ```text
  Request Reset

  ↓

  Account Identified

  ↓

  Reset Token Generated

  ↓

  Token Delivered

  ↓

  Token Submitted

  ↓

  New Password Set

  ↓

  Token Invalidated
  ```

* Every transition is security-sensitive.

---

## Password Reset Enumeration

* Compare reset requests for:

  ```text
  Existing Account

  vs

  Nonexistent Account
  ```

* Look for differences in:

  ```text
  Message

  Status

  Length

  Timing

  Email-Delivery Behavior
  ```

* Generic visible messages do not guarantee identical backend behavior.

---

## Reset Token Properties

* Investigate whether reset tokens are:

  ```text
  Random

  Single Use

  Time Limited

  Bound to Correct Account

  Invalidated After Use

  Invalidated After New Token Issuance Where Appropriate
  ```

* Avoid guessing live users' reset tokens.

---

## Reset Token Reuse

* Workflow:

  ```text
  Obtain Valid Test Reset Token

  ↓

  Reset Password

  ↓

  Reuse Same Token
  ```

* Expected:

  ```text
  Token Rejected
  ```

* If reused successfully, determine the actual security impact.

---

## Reset Token Binding

* A token should apply only to the intended account and action.

* Test whether modifying:

  ```text
  User ID

  Email

  Account Parameter

  Token Context
  ```

* Can redirect the reset to another controlled test account.

---

## Reset Host Handling

* Password-reset links may be generated using:

  ```text
  Host

  :authority

  X-Forwarded-Host

  Application Configuration
  ```

* If user-controlled host information influences generated links, host-header injection risks may exist.

* Test only with domains or infrastructure you are authorized to use.

---

## MFA Workflow

* Typical MFA:

  ```text
  Username + Password

  ↓

  Primary Authentication Valid

  ↓

  MFA Challenge

  ↓

  OTP / Approval

  ↓

  Fully Authenticated Session
  ```

* Critical question:

  ```text
  When does the server consider the session fully authenticated?
  ```

---

## MFA State Mapping

* Record session state at:

  ```text
  Before Login

  After Password

  Before MFA

  After MFA

  After Logout
  ```

* Compare:

  ```text
  Session ID

  Cookies

  Tokens

  Accessible Endpoints

  User Privileges
  ```

---

## MFA Bypass Testing

* After completing only the password stage:

  ```text
  Do Not Complete MFA
  ```

* Try accessing a known authenticated endpoint.

* Expected:

  ```text
  Access Denied or MFA Required
  ```

* Vulnerable:

  ```text
  Full Account Access Before MFA Completion
  ```

---

## Direct Navigation After Partial Authentication

* Test:

  ```text
  Password Accepted

  ↓

  MFA Page Displayed

  ↓

  Request /account Directly
  ```

* The UI requiring MFA is insufficient if backend endpoints already treat the session as fully authenticated.

---

## MFA Response Manipulation

* Do not assume changing a client-visible response such as:

  ```json
  {
    "success": false
  }
  ```

* To:

  ```json
  {
    "success": true
  }
  ```

* Bypasses MFA.

* Security depends on server-side state.

* Always verify access to protected functionality.

---

## OTP Testing

* Evaluate:

  ```text
  Length

  Expiration

  Attempt Limit

  Replay

  Account Binding

  Session Binding

  Rate Limiting

  Invalidation After Success
  ```

* Avoid high-volume guessing.

---

## OTP Replay

* Workflow:

  ```text
  Obtain Valid OTP

  ↓

  Use Successfully

  ↓

  Replay Same OTP
  ```

* Determine whether:

  ```text
  Rejected

  Reusable During Window

  Reusable Across Sessions
  ```

* Security expectations depend on the OTP design.

---

## MFA Recovery

* Recovery mechanisms may include:

  ```text
  Backup Codes

  Recovery Email

  Support Workflow

  Trusted Devices

  Recovery Questions
  ```

* These mechanisms should not be weaker than the authentication control they replace.

---

## Backup Codes

* Test whether backup codes are:

  ```text
  Single Use

  Random

  Properly Invalidated

  Rate Limited

  Bound to Correct Account
  ```

* After using one code:

  ```text
  Replay It
  ```

* Expected:

  ```text
  Rejected
  ```

---

## Trusted Devices

* Applications may issue a token to skip future MFA challenges.

* Investigate:

  ```text
  Token Lifetime

  Token Scope

  Device Binding

  Revocation

  Password Change Behavior

  Account Recovery Behavior
  ```

* Treat trusted-device tokens as authentication credentials.

---

## Authentication Tokens

* Authentication may use:

  ```text
  Session Cookies

  Bearer Tokens

  JWTs

  API Keys

  Refresh Tokens

  OAuth Tokens
  ```

* Identify which credential controls which security state.

---

## Bearer Tokens

* Example:

  ```http
  Authorization: Bearer eyJ...
  ```

* A bearer token generally grants access to whoever possesses it.

* Test:

  ```text
  Expiration

  Scope

  Revocation

  Account Binding

  Audience

  Reuse
  ```

---

## Access Tokens and Refresh Tokens

* Common model:

  ```text
  Access Token

  ↓

  Short-Lived API Access
  ```

* And:

  ```text
  Refresh Token

  ↓

  Obtain New Access Token
  ```

* Refresh tokens are especially sensitive because they may extend authentication for long periods.

---

## Refresh Token Testing

* Investigate:

  ```text
  Rotation

  Reuse

  Expiration

  Revocation

  Logout Behavior

  Password Change Behavior

  Device Binding
  ```

* Example:

  ```text
  Use Refresh Token

  ↓

  Receive New Tokens

  ↓

  Reuse Old Refresh Token

  ↓

  Observe Result
  ```

---

## JWT Structure

* A JSON Web Token commonly contains:

  ```text
  Header.Payload.Signature
  ```

* Example conceptual structure:

  ```text
  HEADER

  .

  PAYLOAD

  .

  SIGNATURE
  ```

* Burp Decoder can help inspect encoded components.

---

## JWT Claims

* Common claims include:

  ```text
  sub

  iss

  aud

  exp

  nbf

  iat

  jti
  ```

* Application-specific claims may include:

  ```text
  role

  user_id

  permissions

  tenant
  ```

---

## JWT Testing Principle

* Decoding a JWT is not the same as breaking it.

* Payload values are often readable by design.

* Security depends primarily on:

  ```text
  Signature Validation

  Claim Validation

  Key Management

  Algorithm Handling

  Expiration

  Audience / Issuer Validation
  ```

---

## JWT Modification Test

* In a controlled test account:

  ```text
  Decode Token

  ↓

  Modify Harmless Claim

  ↓

  Keep Invalid Signature

  ↓

  Send
  ```

* Expected:

  ```text
  Token Rejected
  ```

* If accepted, investigate signature validation.

---

## JWT Algorithm Handling

* Review the token header.

* Example:

  ```json
  {
    "alg": "RS256",
    "typ": "JWT"
  }
  ```

* Security testing may investigate whether the server strictly enforces the expected algorithm and key type.

* Do not assume vulnerability based solely on the header value.

---

## JWT Expiration

* Check:

  ```text
  exp
  ```

* Test whether expired tokens are rejected.

* Also consider:

  ```text
  nbf

  iat
  ```

* Where relevant.

---

## JWT Authorization Claims

* A token may contain:

  ```json
  {
    "role": "user"
  }
  ```

* Simply changing it to:

  ```json
  {
    "role": "admin"
  }
  ```

* Should invalidate the signature.

* If the application trusts unsigned or improperly verified modifications, severe authorization issues may exist.

---

## Session Swapping

* With two test accounts:

  ```text
  User A Session

  User B Session
  ```

* Swap authentication credentials between requests.

* Verify that the server consistently maps:

  ```text
  Credential

  →

  Correct Identity
  ```

* Look for endpoints that rely on client-supplied identity parameters instead of authenticated identity.

---

## Session Confusion

* Session confusion can occur when multiple authentication mechanisms conflict.

* Example request:

  ```text
  Cookie → User A

  Authorization Header → User B
  ```

* Ask:

  ```text
  Which Identity Wins?

  Do Different Components Choose Differently?

  Is Authorization Based on a Different Credential Than Authentication?
  ```

---

## Conflicting Authentication Credentials

* Test only with accounts you control.

* Compare:

  ```text
  Cookie A + Token A

  Cookie B + Token B

  Cookie A + Token B

  Cookie B + Token A
  ```

* Observe:

  ```text
  Displayed Identity

  Data Returned

  Action Owner

  Authorization Result
  ```

---

## Authentication Precedence

* Applications should have predictable credential handling.

* Potential mechanisms:

  ```text
  Session Cookie

  Bearer Token

  API Key

  Basic Authentication

  SSO Header
  ```

* If multiple are present, determine which one controls identity.

---

## Authentication vs Client-Supplied Identity

* Example:

  ```http
  Cookie: session=userA-session

  POST /api/profile
  user_id=2002
  ```

* Ask:

  ```text
  Does server use authenticated session identity?

  or

  Client-supplied user_id?
  ```

* This crosses into authorization testing but often emerges during session analysis.

---

## Session Binding

* Some applications bind sessions to contextual properties such as:

  ```text
  Device

  IP Address

  User Agent

  Client Certificate
  ```

* Determine whether binding is part of the intended security model.

* Be cautious: overly strict IP binding can create usability problems and is not universally required.

---

## Session Expiration

* Test:

  ```text
  Idle Timeout

  Absolute Timeout

  Token Expiration

  Refresh Behavior
  ```

* Example:

  ```text
  Login

  ↓

  Wait Beyond Documented Timeout

  ↓

  Replay Authenticated Request
  ```

* Expected behavior depends on application policy.

---

## Idle vs Absolute Timeout

* **Idle timeout**:

  ```text
  Session expires after inactivity.
  ```

* **Absolute timeout**:

  ```text
  Session expires after a maximum lifetime regardless of activity.
  ```

* Security-sensitive applications may implement both.

---

## Session Rotation During Privilege Changes

* If a user changes from:

  ```text
  Normal User

  ↓

  Privileged User
  ```

* The application should carefully manage session state.

* Investigate:

  ```text
  Session ID Rotation

  Privilege Refresh

  Old Session Validity

  Authorization Cache
  ```

---

## CSRF and Authentication State

* Authentication cookies may automatically accompany browser requests.

* This creates a connection between:

  ```text
  Session Authentication

  and

  CSRF Protection
  ```

* For state-changing actions, inspect:

  ```text
  CSRF Tokens

  SameSite Cookies

  Origin Validation

  Referer Validation
  ```

* Detailed CSRF testing may belong to a dedicated vulnerability workflow, but session behavior must still be understood.

---

## Burp Proxy Workflow

* Use Proxy to:

  ```text
  Capture Login

  Observe Cookies

  Track Redirects

  Capture MFA

  Capture Logout

  Observe Session Rotation
  ```

* Keep Intercept OFF during broad mapping unless modification is needed.

---

## Burp Repeater Workflow

* Repeater is ideal for:

  ```text
  Replaying Old Sessions

  Removing Authentication

  Swapping Tokens

  Testing Logout Invalidation

  Testing Reset Tokens

  Comparing MFA States

  Modifying JWTs

  Testing Credential Precedence
  ```

---

## Burp Intruder Workflow

* Intruder may help with controlled testing of:

  ```text
  Username Enumeration

  Small Credential Sets in Labs

  OTP Behavior

  Token Format Investigation

  Rate-Limit Observation
  ```

* Avoid unauthorized credential attacks and uncontrolled brute force.

---

## Burp Sequencer Workflow

* Sequencer can help assess the statistical quality of tokens such as:

  ```text
  Session IDs

  CSRF Tokens

  Reset Tokens
  ```

* Only when enough valid samples can be safely collected.

* Statistical weakness does not automatically equal exploitability.

---

## Burp Decoder Workflow

* Decoder can assist with:

  ```text
  Base64

  URL Encoding

  Hex

  JWT Components

  Custom Token Inspection
  ```

* Encoding is not encryption.

* A readable token is not automatically insecure.

---

## Burp Comparer Workflow

* Comparer is useful for:

  ```text
  Valid vs Invalid Login

  Existing vs Nonexistent Username

  Authenticated vs Unauthenticated

  Pre-MFA vs Post-MFA

  Active vs Logged-Out Session
  ```

* Look for subtle differences beyond visible messages.

---

## Burp Logger Workflow

* Logger can help reconstruct:

  ```text
  Login Sequence

  Token Refresh

  Background Authentication Calls

  Session Rotation

  Logout Requests

  SSO Exchanges
  ```

* This is valuable for complex modern applications.

---

## Authentication Testing Workflow

* Use:

  ```text
  Map Authentication Surface

  ↓

  Create Controlled Identities

  ↓

  Establish Unauthenticated Baseline

  ↓

  Capture Login Workflow

  ↓

  Record Session Before and After Login

  ↓

  Test Login Responses

  ↓

  Test Enumeration

  ↓

  Review Rate Limiting

  ↓

  Analyze Session Tokens

  ↓

  Test Rotation

  ↓

  Test Fixation

  ↓

  Test Logout Invalidation

  ↓

  Test Password Change / Reset Effects

  ↓

  Test MFA State

  ↓

  Test Remember-Me / Refresh Tokens

  ↓

  Test Session Confusion

  ↓

  Verify Final Server-Side Identity

  ↓

  Reproduce Findings
  ```

---

## Authentication State Matrix

| State                          | Expected Access                                 |
| ------------------------------ | ----------------------------------------------- |
| Unauthenticated                | Public resources only                           |
| Password accepted, MFA pending | Limited intermediate state                      |
| Fully authenticated            | User-authorized resources                       |
| Logged out                     | No authenticated access                         |
| Expired session                | No authenticated access                         |
| Revoked session                | No authenticated access                         |
| Password reset completed       | Behavior according to session revocation policy |

---

## Session Lifecycle Matrix

| Event               | What to Test                   |
| ------------------- | ------------------------------ |
| Initial visit       | Pre-auth session issued?       |
| Login               | Session rotated?               |
| MFA completion      | Session upgraded/rotated?      |
| Privilege change    | Session refreshed/rotated?     |
| Password change     | Other sessions affected?       |
| Password reset      | Old sessions/tokens affected?  |
| Logout              | Server-side invalidation?      |
| Log out all devices | All targeted sessions revoked? |
| Timeout             | Old session rejected?          |

---

## Authentication Attack Surface Map

* Document:

  ```text
  Application:

  Login:

  Registration:

  Logout:

  Password Change:

  Password Reset:

  MFA:

  Recovery:

  Remember Me:

  SSO:

  API Authentication:

  Session Cookie:

  Access Token:

  Refresh Token:

  JWT:

  CSRF Protection:

  Session Timeout:

  Concurrent Sessions:

  Device Management:
  ```

---

## Authentication Investigation Card

* Record:

  ```text
  Investigation:

  Endpoint:

  User:

  Role:

  Authentication State:

  Session Before:

  Session After:

  Credential Type:

  Baseline Request:

  Modified Request:

  Baseline Response:

  Modified Response:

  Expected Identity:

  Actual Identity:

  Expected Access:

  Actual Access:

  Token Rotated?:

  Token Invalidated?:

  Reproduced?:

  Security Impact:

  Evidence Preserved?:

  Follow-Up:
  ```

---

## Session Testing Checklist

* Verify:

  ```text
  [ ] Authentication surface mapped

  [ ] At least two controlled identities available where useful

  [ ] Unauthenticated baseline captured

  [ ] Authenticated baseline captured

  [ ] Login sequence mapped

  [ ] Pre-login session recorded

  [ ] Post-login session recorded

  [ ] Session rotation checked

  [ ] Session fixation considered

  [ ] Cookie attributes reviewed

  [ ] Session token format reviewed

  [ ] Username enumeration tested

  [ ] Login rate limiting reviewed

  [ ] Logout invalidation tested

  [ ] Old session replayed after logout

  [ ] Password change session behavior tested

  [ ] Password reset session behavior tested

  [ ] Concurrent sessions reviewed

  [ ] Session revocation tested

  [ ] Remember-me functionality reviewed

  [ ] Reset token reuse tested

  [ ] Reset token binding reviewed

  [ ] MFA intermediate state tested

  [ ] OTP replay / limits reviewed where applicable

  [ ] Recovery mechanisms reviewed

  [ ] Access tokens reviewed

  [ ] Refresh tokens reviewed

  [ ] JWT validation considered

  [ ] Session confusion tested where multiple credentials exist

  [ ] Final server-side identity verified

  [ ] Findings reproduced cleanly
  ```

---

## Testing Strategy Matrix

| Scenario                 | Primary Test                                |
| ------------------------ | ------------------------------------------- |
| Login                    | Valid/invalid differential                  |
| Username field           | Enumeration                                 |
| Session cookie           | Rotation and invalidation                   |
| Pre-login session        | Fixation                                    |
| Logout                   | Old-session replay                          |
| Password change          | Existing-session behavior                   |
| Password reset           | Token reuse and session revocation          |
| Remember me              | Long-lived token lifecycle                  |
| MFA                      | Intermediate-state access                   |
| OTP                      | Replay and attempt limits                   |
| Backup code              | Single-use enforcement                      |
| Bearer token             | Scope, expiry, revocation                   |
| Refresh token            | Rotation and reuse                          |
| JWT                      | Signature and claim validation              |
| Multiple auth mechanisms | Credential precedence                       |
| Multiple devices         | Session revocation                          |
| Protected endpoint       | Authenticated vs unauthenticated comparison |

---

## Troubleshooting — Session Appears Valid After Logout

* Check:

  ```text
  Was Request Served From Cache?

  ↓

  Did You Replay Exact Old Cookie?

  ↓

  Did Application Issue a New Session Automatically?

  ↓

  Is Endpoint Actually Authentication-Protected?

  ↓

  Does Sensitive Action Still Work?
  ```

* Verify server-side authenticated functionality.

---

## Troubleshooting — Session ID Does Not Change at Login

* Investigate:

  ```text
  Is Session Identifier Actually Authentication Credential?

  ↓

  Is Authentication State Stored Separately?

  ↓

  Can Pre-Login Session Be Attacker-Controlled?

  ↓

  Does Old Identifier Become Authenticated?
  ```

* Unchanged value alone does not prove session fixation.

---

## Troubleshooting — Different Login Responses

* Rule out:

  ```text
  CSRF Token Differences

  Dynamic IDs

  Timestamps

  Localization

  Random Content

  Rate Limiting
  ```

* Compare stable indicators.

---

## Troubleshooting — MFA Appears Bypassable

* Do not rely on browser navigation alone.

* Test:

  ```text
  Protected API

  Sensitive Account Page

  State-Changing Action
  ```

* Confirm whether the server actually grants full authentication.

---

## Troubleshooting — Old Password Reset Token Works

* Determine:

  ```text
  Was First Reset Actually Completed?

  Is Token Intended for Multiple Uses?

  Can It Change Password Repeatedly?

  Is Same Account Affected?

  Does New Token Invalidate Old Token?
  ```

* Measure real impact.

---

## Troubleshooting — JWT Modification Is Accepted

* Confirm:

  ```text
  Did Server Actually Use Modified Claim?

  Was Another Credential Authenticating Request?

  Was Response Public?

  Was Signature Recomputed Automatically by Tool?

  Is Token Even Used by Endpoint?
  ```

* Remove conflicting credentials and verify server-side identity.

---

## Troubleshooting — User Identity Changes Unexpectedly

* Check all credentials:

  ```text
  Cookie

  Authorization

  API Key

  Query Token

  Custom Headers
  ```

* One request may contain multiple identity sources.

---

## Troubleshooting — Session Tests Are Inconsistent

* Check:

  ```text
  Multiple Browser Tabs

  Automatic Token Refresh

  Service Workers

  Background API Requests

  Multiple Cookies

  Load Balancing

  Cache

  SSO Reauthentication
  ```

* Use Repeater for controlled testing.

---

## Avoiding False Positives

* Authentication observations may be misleading because of:

  ```text
  Cached Pages

  Client-Side State

  Automatic Refresh Tokens

  Background Login

  SSO Sessions

  Public Endpoints

  Stale UI

  Multiple Authentication Credentials
  ```

* Always verify:

  ```text
  Server-Side Protected Action
  ```

---

## Verify Server-Side Identity

* A strong identity check uses an endpoint such as:

  ```text
  GET /api/me

  GET /account

  GET /profile
  ```

* Or another endpoint that clearly reveals the authenticated test identity.

* Record:

  ```text
  Expected User:

  Actual User:
  ```

---

## Evidence Collection

* Preserve:

  ```text
  Authentication State

  User / Role

  Session Before

  Session After

  Original Request

  Modified Request

  Original Response

  Modified Response

  Token Lifecycle

  Logout / Reset Event

  Replay Result

  Server-Side Identity

  Security Impact
  ```

* Authentication findings often require a sequence, not one isolated request.

---

## Strong Session Invalidation Evidence

* Example:

  ```text
  Step 1:

  Login as User A
  ```

  ```text
  Step 2:

  Capture authenticated session ABC
  ```

  ```text
  Step 3:

  Logout normally
  ```

  ```text
  Step 4:

  Replay ABC manually
  ```

  ```text
  Step 5:

  Access protected account data successfully
  ```

* This demonstrates server-side session invalidation failure more clearly than merely observing a cookie.

---

## Strong MFA Bypass Evidence

* Example:

  ```text
  Step 1:

  Submit valid username and password
  ```

  ```text
  Step 2:

  Stop before MFA completion
  ```

  ```text
  Step 3:

  Use intermediate session to request protected endpoint
  ```

  ```text
  Step 4:

  Perform action requiring full authentication
  ```

* This proves server-side access rather than a UI-only bypass.

---

## Practical Exercise

* Use a deliberately vulnerable authentication lab or another application you are explicitly authorized to test.

### Task 1 — Map Authentication

* Record:

  ```text
  Login:

  Logout:

  Password Reset:

  Password Change:

  MFA:

  Session Cookie:
  ```

---

### Task 2 — Capture Pre-Login State

* Visit the application before authentication.

* Record:

  ```text
  Cookies:

  Session Identifier:

  Protected Endpoint Result:
  ```

---

### Task 3 — Login

* Authenticate with a test account.

* Record:

  ```text
  Login Request:

  Login Response:

  Session Before:

  Session After:

  Rotated?:
  ```

---

### Task 4 — Test Authentication Removal

* Send an authenticated request to Repeater.

* Remove the authentication credential.

* Record:

  ```text
  Authenticated Result:

  Unauthenticated Result:
  ```

---

### Task 5 — Test Session Swapping

* Using two controlled accounts:

  ```text
  User A

  User B
  ```

* Swap session credentials in a safe request.

* Record the server-side identity returned.

---

### Task 6 — Test Logout

* Save User A's session.

* Logout.

* Replay the old session.

* Record:

  ```text
  Old Session Accepted?:

  Protected Action Accessible?:
  ```

---

### Task 7 — Review Cookie Attributes

* Record:

  ```text
  Secure:

  HttpOnly:

  SameSite:

  Domain:

  Path:

  Expiration:
  ```

---

### Task 8 — Test Password Reset in a Lab

* Obtain a valid reset token for your own test account.

* Complete the reset.

* Test:

  ```text
  Token Reuse

  Old Session

  Remember-Me Token
  ```

* Record results.

---

### Task 9 — Test MFA State if Available

* Stop after valid password entry but before MFA completion.

* Request a protected endpoint.

* Record:

  ```text
  Intermediate Session:

  Endpoint:

  Result:
  ```

---

### Task 10 — Build Session Lifecycle

* Document:

  ```text
  Unauthenticated

  ↓

  Pre-Login Session

  ↓

  Login

  ↓

  Authenticated Session

  ↓

  MFA / Privilege Change

  ↓

  Logout

  ↓

  Invalidated Session
  ```

* Mark every token change.

---

## Investigation Journal

* Record:

  ```text
  Application:

  User A:

  User B:

  Roles:

  Login Endpoint:

  Logout Endpoint:

  Session Cookie:

  Access Token:

  Refresh Token:

  Pre-Login Session:

  Post-Login Session:

  Session Rotated?:

  Username Enumeration:

  Rate Limiting:

  Cookie Attributes:

  Session Fixation:

  Logout Invalidation:

  Password Change Behavior:

  Password Reset Behavior:

  Reset Token Reuse:

  MFA State:

  OTP Behavior:

  Remember-Me Behavior:

  Concurrent Sessions:

  Session Revocation:

  JWT Observations:

  Credential Precedence:

  Session Confusion:

  Security Impact:

  Evidence:

  Follow-Up:
  ```

---

## Common Mistakes

* Common mistakes include:

  * Treating authentication and authorization as the same thing.
  * Testing only the login page.
  * Ignoring password reset and account recovery.
  * Using only one test account.
  * Failing to record pre-login and post-login sessions.
  * Assuming an unchanged session ID automatically proves session fixation.
  * Assuming deleting a browser cookie means server-side logout occurred.
  * Failing to replay old sessions after logout.
  * Ignoring password-change and password-reset effects on existing sessions.
  * Looking only at visible login error messages during enumeration testing.
  * Treating one timing difference as proof of enumeration.
  * Performing uncontrolled brute force.
  * Ignoring remember-me and refresh tokens.
  * Treating readable JWT payloads as vulnerabilities.
  * Modifying JWT claims without checking whether the server actually trusts them.
  * Ignoring multiple simultaneous authentication credentials.
  * Confusing client-side MFA navigation with server-side MFA enforcement.
  * Failing to verify the actual authenticated identity after token manipulation.
  * Ignoring background token refresh.
  * Testing stale browser pages instead of protected server-side actions.
  * Forgetting to test session revocation across multiple devices.
  * Reporting authentication issues without documenting the full state transition.

---

## Interview Questions

1. What is the difference between authentication and authorization?
2. Why are sessions necessary in web applications?
3. Describe a typical session lifecycle.
4. What is a session identifier?
5. What properties should a secure session identifier have?
6. What cookie attributes are important for session security?
7. What does the `Secure` cookie attribute do?
8. What does `HttpOnly` do?
9. What does `SameSite` do?
10. What is session rotation?
11. When should a session identifier commonly be rotated?
12. What is session fixation?
13. How would you test for session fixation using Burp?
14. Why does an unchanged session ID not automatically prove session fixation?
15. How would you test logout functionality?
16. What is the difference between client-side and server-side logout?
17. Why should old sessions be tested after a password reset?
18. What is username enumeration?
19. Which response differences can reveal account existence?
20. Why must timing-based enumeration use multiple samples?
21. How would you evaluate login rate limiting?
22. What is a remember-me token?
23. What security properties should password-reset tokens have?
24. How would you test reset-token reuse?
25. What is reset-token binding?
26. How can host handling affect password-reset security?
27. How would you test whether MFA is enforced server-side?
28. Why is changing a client-side MFA response insufficient to prove bypass?
29. What should you test about OTPs?
30. What should you test about MFA backup codes?
31. What is a bearer token?
32. What is the difference between an access token and a refresh token?
33. What should be tested about refresh-token rotation?
34. What are the three major components of a JWT?
35. Why is a readable JWT payload not a vulnerability?
36. Which JWT claims are commonly security-relevant?
37. What happens if you modify a signed JWT without recomputing a valid signature?
38. What is session confusion?
39. How can conflicting authentication credentials create security issues?
40. Why should the final server-side identity always be verified?
41. How can Burp Sequencer assist session testing?
42. How can Burp Comparer assist authentication testing?
43. How can Burp Logger help map complex login flows?
44. What are common false positives during session invalidation testing?
45. What is the correct end-to-end methodology for session and authentication testing with Burp?

---

## Quick Revision

* Authentication:

  ```text
  Who are you?
  ```

* Authorization:

  ```text
  What can you do?
  ```

* Session lifecycle:

  ```text
  Unauthenticated

  ↓

  Login

  ↓

  Authenticated Session

  ↓

  Security State Changes

  ↓

  Logout / Expiry

  ↓

  Invalid Session
  ```

* Session rotation:

  ```text
  Pre-Login Session

  ≠

  Post-Login Session
  ```

* Session fixation concept:

  ```text
  Known Pre-Login Session

  ↓

  Victim Authenticates

  ↓

  Same Session Remains Valid

  ↓

  Potential Session Hijack
  ```

* Logout test:

  ```text
  Save Session

  ↓

  Logout

  ↓

  Replay Session

  ↓

  Expect Rejection
  ```

* MFA test:

  ```text
  Password Accepted

  ↓

  MFA Pending

  ↓

  Request Protected Resource

  ↓

  Must Be Rejected
  ```

* Reset token:

  ```text
  Random

  +

  Time Limited

  +

  Single Use

  +

  Account Bound
  ```

* JWT:

  ```text
  Header.Payload.Signature
  ```

* Strong testing principle:

  ```text
  Modify Credential

  ↓

  Send Request

  ↓

  Verify Actual Server-Side Identity
  ```

* Remember:

  ```text
  Cookie Deleted

  ≠

  Server Session Invalidated
  ```

  ```text
  Session ID Unchanged

  ≠

  Automatically Session Fixation
  ```

  ```text
  Readable JWT

  ≠

  Insecure JWT
  ```

  ```text
  MFA Page Skipped

  ≠

  MFA Bypassed
  ```

  ```text
  UI Shows Logged Out

  ≠

  Old Token Invalid
  ```

* Professional principle:

  ```text
  Map Identity

  +

  Track Session State

  +

  Test Every Transition

  +

  Replay Old Credentials

  +

  Verify Server-Side Identity

  =

  Reliable Authentication Testing
  ```

---

## Key Takeaways

* Authentication verifies identity, while authorization determines what that identity is permitted to do.
* Authentication testing must cover the complete lifecycle, not only the login form.
* Map login, logout, registration, password changes, password resets, MFA, recovery, remember-me functionality, SSO, API authentication, and token refresh mechanisms.
* Use multiple controlled accounts to distinguish account-specific behavior from session and identity-handling flaws.
* Establish both unauthenticated and authenticated baselines before modifying credentials.
* Record session identifiers before and after login, MFA, privilege changes, password changes, and other security-sensitive transitions.
* Session fixation requires more than an unchanged session ID; the pre-authentication session must be controllable or reusable in a way that creates security impact.
* Logout must invalidate authentication server-side where required; deleting a browser cookie alone is insufficient.
* Replay old sessions after logout, password reset, revocation, and other security-sensitive events to verify invalidation.
* Username enumeration may appear through messages, status codes, lengths, redirects, timing, or secondary workflows.
* Rate-limit testing should be controlled and should avoid locking or disrupting real user accounts.
* Cookie attributes such as `Secure`, `HttpOnly`, `SameSite`, `Domain`, and `Path` are important parts of session security.
* Password-reset tokens should be unpredictable, appropriately time-limited, account-bound, and invalidated according to their intended lifecycle.
* MFA must be enforced by server-side authorization state rather than only by client-side navigation.
* OTPs, backup codes, and trusted-device tokens require their own lifecycle, replay, rate-limit, and revocation testing.
* Remember-me and refresh tokens can outlive normal sessions and must be tested after logout, password changes, resets, and session revocation.
* JWT payload readability is normal; security depends on signature verification, algorithm handling, key management, and correct claim validation.
* When multiple credentials are present, determine which credential controls identity and whether different application layers interpret them consistently.
* Always verify the actual server-side identity and protected action after manipulating authentication state.
* Strong authentication findings document the complete state transition, original and modified credentials, server-side result, and reproducible security impact.
