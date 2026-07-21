# Authentication and Session Challenge

> **Module 18 — Capstone, Interview & Quick Reference**

> **Progress**

```text
████░░░░░░░░░░░ 4 / 15 Files
```

---

## Overview

* Authentication answers:

```text
Who Are You?
```

* Session management answers:

```text
How Does the Application Remember Who You Are?
```

* Together, authentication and session management form one of the most important security boundaries in a web application.

* A weakness may allow an attacker to:

  * Discover valid accounts.
  * Bypass authentication.
  * Abuse password recovery.
  * Circumvent MFA.
  * Hijack sessions.
  * Reuse invalidated sessions.
  * Maintain access after logout.
  * Exploit weak session lifecycle controls.
  * Access sensitive functionality as another user.

* This challenge requires you to test authentication as a complete lifecycle rather than testing only the login form.

```text
Registration

↓

Login

↓

Session Creation

↓

Authenticated Activity

↓

Privilege / Identity Changes

↓

Session Rotation or Revocation

↓

Logout

↓

Session Termination
```

---

## Objectives

* By completing this challenge, you should be able to:

  * Map authentication functionality.
  * Identify authentication endpoints.
  * Establish normal authentication baselines.
  * Test account enumeration.
  * Analyze login behavior.
  * Review rate limiting safely.
  * Test password policies.
  * Analyze password-reset workflows.
  * Test reset-token lifecycle.
  * Analyze MFA workflows.
  * Identify authentication-state transitions.
  * Analyze session cookies.
  * Test session fixation.
  * Verify session rotation.
  * Test logout invalidation.
  * Analyze session expiration.
  * Test session behavior after password changes.
  * Test session behavior after privilege changes.
  * Review concurrent-session handling.
  * Validate authentication findings professionally.
  * Preserve evidence without exposing unnecessary credentials or tokens.

---

# Challenge Scenario

* You have mapped an authorized application containing:

```text
Registration

Login

Password Reset

Account Settings

Password Change

MFA

Logout
```

* You have at least two controlled accounts.

* Your task is to determine whether the application securely manages identity from account creation through session termination.

---

# Challenge Deliverables

* Produce:

```text
Authentication-Surface.md

Authentication-Test-Matrix.md

Session-Lifecycle.md

Session-Test-Matrix.md

Validated-Findings/

Evidence/

Authentication-and-Session-Summary.md
```

---

# Phase 1 — Map the Authentication Surface

* Identify every feature related to identity.

---

## Look For

```text
Registration

Email Verification

Login

Remember Me

Password Reset

Password Change

MFA Enrollment

MFA Verification

Recovery Codes

Account Recovery

Email Change

Username Change

SSO

OAuth

Magic Links

Logout

Session Management
```

---

# Authentication Surface Template

| Feature         | Endpoint            | Method | Authentication | Sensitive Inputs   |
| --------------- | ------------------- | ------ | -------------- | ------------------ |
| Login           | `/login`            | POST   | No             | Username, password |
| Reset Request   | `/forgot-password`  | POST   | No             | Email              |
| Password Change | `/account/password` | POST   | Yes            | Old/new password   |
| Logout          | `/logout`           | POST   | Yes            | Session            |

---

# Phase 2 — Establish Authentication Baselines

* Before testing abnormal behavior, capture normal flows.

---

## Capture

```text
Successful Login

Failed Login

Logout

Password Reset Request

Password Reset Completion

Password Change

MFA Verification
```

---

## Record

```text
Request

Response

Status Code

Redirect

Cookies

Headers

Response Length

Response Time

Visible Message

Resulting Authentication State
```

---

# Phase 3 — Test Account Enumeration

* Determine whether the application reveals whether an account exists.

---

## Compare

```text
Valid Username

+

Incorrect Password
```

* Against:

```text
Invalid Username

+

Incorrect Password
```

---

## Compare

* [ ] Status code.
* [ ] Response body.
* [ ] Error text.
* [ ] Response length.
* [ ] Redirect behavior.
* [ ] Timing.
* [ ] Headers.
* [ ] Secondary effects.

---

## Secure Behavior

```text
Valid Account

and

Invalid Account

↓

Materially Equivalent External Response
```

---

# Enumeration Surfaces

* Test relevant controlled inputs across:

```text
Login

Registration

Password Reset

Username Recovery

MFA Recovery

Invitation

Email Change

Account Lookup
```

---

# Important Principle

```text
Generic Login Error

≠

No Enumeration
```

* Another endpoint may still disclose account existence.

---

# Phase 4 — Review Login Logic

* Test the authentication workflow systematically.

---

## Ask

```text
What Identifies the User?

What Proves Identity?

What State Is Created After Login?

Are There Multiple Login Paths?

Are Security Checks Consistent Across Them?
```

---

## Review

* [ ] Username/email normalization.
* [ ] Password handling.
* [ ] Alternative identifiers.
* [ ] Remember-me functionality.
* [ ] SSO paths.
* [ ] Mobile/API authentication.
* [ ] Legacy authentication endpoints.
* [ ] Error behavior.
* [ ] Redirect behavior.

---

# Phase 5 — Review Rate Limiting

* Perform only controlled testing within authorization and rate limits.

---

## Observe

```text
Repeated Failed Attempts

↓

Delay?

Block?

CAPTCHA?

Temporary Lockout?

IP-Based Control?

Account-Based Control?

No Visible Control?
```

---

## Record

```text
Attempt Count

Response Status

Response Time

Error Message

Lockout State

Recovery Behavior
```

---

## Important

* Do not perform uncontrolled credential attacks.

* Use:

  * Your own test accounts.
  * Small request counts.
  * Program-approved limits.

---

# Phase 6 — Analyze Password Policy

* Review account creation and password-change functionality.

---

## Test

* [ ] Minimum length.
* [ ] Maximum supported length.
* [ ] Weak-password rejection.
* [ ] Password confirmation.
* [ ] Password reuse behavior.
* [ ] Current-password requirement for sensitive changes.
* [ ] Password truncation behavior where relevant.

---

# Phase 7 — Map Password Reset

* Password reset is an alternative authentication mechanism.

---

## Workflow

```text
Reset Request

↓

Account Identification

↓

Reset Token Generated

↓

Token Delivered

↓

Token Presented

↓

New Password Submitted

↓

Token Invalidated

↓

Existing Sessions Reviewed
```

---

# Reset Workflow Questions

```text
Can Accounts Be Enumerated?

Is the Token Unpredictable?

Does the Token Expire?

Is It Single Use?

Is It Bound to the Correct Account?

Can It Be Replayed?

What Happens After Password Change?

What Happens to Existing Sessions?
```

---

# Phase 8 — Test Reset-Token Lifecycle

* Use only reset tokens generated for accounts you control.

---

## Test Sequence

```text
Generate Token

↓

Use Token Normally

↓

Attempt Reuse

↓

Expected:

Rejected
```

---

## Additional Tests

* [ ] Request multiple reset tokens.
* [ ] Determine whether older tokens remain valid.
* [ ] Check expiration.
* [ ] Check account binding.
* [ ] Check whether changing the password invalidates the token.
* [ ] Check whether completing one reset invalidates other tokens.

---

# Reset Token State Model

```text
Generated

↓

Valid

↓

Used / Expired / Revoked

↓

Invalid
```

---

# Phase 9 — Review Reset Request Construction

* Inspect reset-related requests for security-sensitive values.

---

## Look For

```text
Host

Origin

Forwarded Headers

Redirect Parameters

Callback URLs

Account Identifiers

Token Values
```

* Determine whether the application improperly trusts client-controlled data when constructing reset flows.

---

# Phase 10 — Map MFA

* If MFA exists, map the complete process.

---

## Workflow

```text
Username + Password

↓

Primary Authentication Accepted

↓

MFA Challenge

↓

MFA Verification

↓

Fully Authenticated Session
```

---

## Identify

```text
Enrollment

Challenge

Verification

Recovery Codes

Remember Device

Disable MFA

Reset MFA
```

---

# Phase 11 — Test MFA State Transitions

* Determine whether protected functionality is accessible before MFA completes.

---

## Expected

```text
Password Accepted

↓

Partial Authentication State

↓

MFA Required

↓

Full Session Only After Verification
```

---

## Test

* [ ] Direct navigation after password acceptance.
* [ ] API access before MFA completion.
* [ ] Reuse of pre-MFA session.
* [ ] Alternate endpoints.
* [ ] Recovery flow.
* [ ] MFA disable functionality.
* [ ] Remember-device functionality.

---

# Core Question

```text
Does the Server Enforce MFA State

or

Does the UI Merely Display an MFA Page?
```

---

# Phase 12 — Identify the Session Mechanism

* Determine how authenticated state is maintained.

---

## Common Mechanisms

```text
Session Cookie

Bearer Token

JWT

Refresh Token

Multiple Cookies

Hybrid Session
```

---

## Record

```text
Token Name

Location

Creation Point

Rotation Point

Expiration

Revocation Behavior

Associated Account

Associated Role
```

---

# Phase 13 — Analyze Cookie Attributes

* For session cookies, inspect:

```text
Secure

HttpOnly

SameSite

Domain

Path

Expires

Max-Age
```

---

## Example

```text
Set-Cookie: session=...;
Secure;
HttpOnly;
SameSite=Lax
```

---

## Ask

```text
Is the Cookie Sent Only Over HTTPS?

Can JavaScript Access It?

How Is Cross-Site Sending Controlled?

Is Scope Broader Than Necessary?

Is Persistence Appropriate?
```

---

# Phase 14 — Map the Session Lifecycle

```text
Unauthenticated

↓

Login

↓

Session Created

↓

Authenticated Activity

↓

Sensitive State Change

↓

Possible Session Rotation

↓

Logout

↓

Session Invalidated
```

---

## Additional Events

```text
Password Change

Email Change

MFA Enable

MFA Disable

Privilege Change

Account Disable

Password Reset
```

* Determine how each affects active sessions.

---

# Phase 15 — Test Session Fixation

* Compare session identifiers before and after authentication.

---

## Workflow

```text
Visit Application Unauthenticated

↓

Record Pre-Login Session

↓

Authenticate

↓

Record Post-Login Session

↓

Compare
```

---

## Expected

```text
Pre-Authentication Session

≠

Authenticated Session Identifier
```

* The application should prevent an attacker from forcing or preserving a known session identifier across authentication.

---

# Phase 16 — Test Session Rotation

* Sensitive authentication transitions may require session rotation.

---

## Review

```text
Anonymous → Authenticated

User → Privileged Role

MFA Incomplete → MFA Complete

Sensitive Reauthentication
```

---

## Record

```text
Before Token

After Token

Identity

Privilege

Rotation Observed?
```

---

# Phase 17 — Test Logout Invalidation

* Logging out should terminate the relevant authenticated session.

---

## Workflow

```text
Login

↓

Capture Authenticated Request

↓

Confirm It Works

↓

Logout

↓

Replay Previous Authenticated Request
```

---

## Expected

```text
Old Session

↓

Rejected
```

---

## Do Not Rely Only On

```text
Browser Redirected to Login
```

* The server must invalidate or otherwise reject the previous authenticated state.

---

# Phase 18 — Test Session Expiration

* Determine whether sessions expire appropriately.

---

## Types

```text
Idle Timeout

Absolute Timeout

Token Expiration

Refresh Token Expiration
```

---

## Record

```text
Created At

Last Used

Expected Expiration

Observed Expiration

Post-Expiration Behavior
```

---

# Phase 19 — Test Password-Change Effects

* A password change is a major security event.

---

## Workflow

```text
Session A

+

Session B

↓

Change Password in Session A

↓

Test Session B
```

---

## Ask

```text
Should Other Sessions Remain Active?

Does the Application Offer Session Revocation?

Does Policy Match Observed Behavior?

Are Sensitive Sessions Reauthenticated?
```

---

# Phase 20 — Test Password-Reset Effects

* Password reset may indicate account recovery after possible compromise.

---

## Test

```text
Session A Active

↓

Reset Password Through Recovery Flow

↓

Replay Session A
```

* Determine whether session behavior matches the application's intended security model.

---

# Phase 21 — Test Privilege-Change Effects

* Roles may change while sessions remain active.

---

## Example

```text
User Has Admin Role

↓

Admin Session Created

↓

Role Removed

↓

Old Session Reused
```

---

## Expected

* Authorization should reflect current privileges.

```text
Current Server-Side Permission

>

Stale Session Assumption
```

---

# Phase 22 — Test Account Disablement

* If supported:

```text
Active User Session

↓

Account Disabled

↓

Existing Session Replayed
```

* Determine whether access is appropriately revoked.

---

# Phase 23 — Review Concurrent Sessions

* Determine whether multiple sessions are allowed.

---

## Test

```text
Browser A

→ Login
```

```text
Browser B

→ Login
```

* Then test:

  * Logout from A.
  * Password change from A.
  * Session revocation from A.
  * Account security changes.

* Observe Browser B.

---

# Phase 24 — Review Session Management Features

* Some applications expose:

```text
Active Sessions

Devices

Login History

Revoke Session

Log Out Everywhere
```

---

## Test

* [ ] Session list accuracy.
* [ ] Session ownership.
* [ ] Revocation.
* [ ] Cross-user access controls.
* [ ] Device information exposure.
* [ ] Immediate invalidation.

---

# Phase 25 — Review Token Exposure

* Authentication tokens may leak through unsafe locations.

---

## Look For

```text
URL Query Strings

Browser History

Referer Headers

Logs

Error Messages

HTML

JavaScript

Local Storage

Third-Party Requests
```

---

## Important

```text
Authentication Secret

↓

Should Not Be Exposed Unnecessarily
```

---

# Phase 26 — Analyze JWTs Where Used

* If the application uses JWTs, inspect their structure and usage.

---

## Review

```text
Header

Payload

Signature

Claims

Expiration

Issuer

Audience

Subject

Roles

Permissions
```

---

## Ask

```text
Is the Signature Verified?

Are Expiration Claims Enforced?

Are Role Claims Trusted Safely?

Are Tokens Revocable When Necessary?

Are Sensitive Claims Exposed?
```

---

# Phase 27 — Test Authentication Consistency Across Interfaces

* Applications may expose multiple interfaces.

---

## Compare

```text
Web UI

REST API

GraphQL

Mobile API

Legacy API
```

---

## Ask

```text
Do All Interfaces Enforce the Same Authentication Rules?

Is MFA Required Everywhere?

Are Disabled Accounts Rejected Everywhere?

Are Session Rules Consistent?
```

---

# Phase 28 — Build the Authentication Test Matrix

| Test        | Baseline      | Mutation            | Expected            | Result  |
| ----------- | ------------- | ------------------- | ------------------- | ------- |
| Enumeration | Valid user    | Invalid user        | Equivalent response | Pending |
| Reset reuse | Valid reset   | Replay token        | Reject              | Pending |
| MFA state   | MFA required  | Access API early    | Reject              | Pending |
| Logout      | Valid session | Replay after logout | Reject              | Pending |

---

# Phase 29 — Build the Session Test Matrix

| Event             | Session A        | Session B | Expected Result          |
| ----------------- | ---------------- | --------- | ------------------------ |
| Login             | Active           | —         | Valid                    |
| Logout A          | Invalid          | —         | A rejected               |
| Password change A | Active/new state | Existing  | Policy dependent         |
| Role revoked      | Existing         | Existing  | New permissions enforced |
| Account disabled  | Existing         | Existing  | Access revoked           |

---

# Phase 30 — Validate Candidate Findings

* Do not report authentication weaknesses based only on unusual behavior.

---

## Validation Workflow

```text
Observation

↓

Reproduce

↓

Establish Baseline

↓

Use Controlled Accounts

↓

Verify Authentication Boundary Failure

↓

Verify Resulting Access

↓

Determine Impact

↓

Identify Root Cause
```

---

# Example — Account Enumeration

## Weak Evidence

```text
One Error Message Looked Different
```

## Stronger Evidence

```text
Controlled Valid Account

vs

Controlled Invalid Identifier

↓

Consistent Reproducible Difference

↓

Difference Reveals Account Existence
```

---

# Example — Logout Failure

## Weak Evidence

```text
Browser Still Shows Cached Page
```

## Strong Evidence

```text
Authenticated Request Works

↓

Logout

↓

Exact Authenticated Request Replayed

↓

Server Still Performs Protected Action
```

---

# Phase 31 — Collect Evidence

* Preserve:

```text
Baseline Request

Baseline Response

Modified Request

Modified Response

Session State

Account Identity

Timestamp

Final Access Result
```

---

## Redact

```text
Passwords

Reusable Session Tokens

Reset Tokens

Recovery Codes

Personal Data
```

* Unless the exact value is necessary for authorized evidence.

---

# Capstone Challenge

## Stage 1 — Authentication Mapping

* Identify every authentication-related endpoint.

* Produce:

```text
Authentication-Surface.md
```

---

## Stage 2 — Login Testing

* Test:

  * Valid login.
  * Invalid login.
  * Controlled enumeration differences.
  * Rate-limiting behavior within authorized limits.
  * Alternate login paths.

---

## Stage 3 — Password Reset

* Map the complete reset flow.

* Test:

  * Token reuse.
  * Multiple token behavior.
  * Expiration.
  * Account binding.
  * Session effects.

---

## Stage 4 — MFA

* If supported:

  * Map enrollment.
  * Map challenge.
  * Map verification.
  * Test pre-MFA access.
  * Test recovery.
  * Test disablement.

---

## Stage 5 — Session Lifecycle

* Record:

```text
Creation

Rotation

Expiration

Revocation

Logout
```

---

## Stage 6 — Security Events

* Test session behavior after:

```text
Password Change

Password Reset

Privilege Change

Account Disablement
```

---

## Stage 7 — Validation

* Validate every suspicious result with controlled accounts and reproducible evidence.

---

# Challenge Success Criteria

* You should be able to answer:

```text
Which Authentication Mechanisms Exist?

Can Account Existence Be Inferred?

How Is Login Protected?

How Does Password Reset Work?

How Are Reset Tokens Managed?

How Does MFA State Work?

What Creates an Authenticated Session?

Where Is the Session Stored?

When Does It Rotate?

When Does It Expire?

What Invalidates It?

What Happens After Logout?

What Happens After Password Change?

What Happens After Password Reset?

What Happens After Role Changes?

What Happens After Account Disablement?
```

---

# Authentication Testing Workflow

```text
Map Authentication Surface

↓

Capture Baselines

↓

Test Identity Disclosure

↓

Test Login Controls

↓

Test Recovery

↓

Test MFA

↓

Map Session Mechanism

↓

Test Session Lifecycle

↓

Test Security Events

↓

Validate Findings

↓

Collect Evidence

↓

Report
```

---

# Common Mistakes

* Testing only the login page.
* Ignoring registration.
* Ignoring password recovery.
* Ignoring MFA recovery.
* Testing only visible browser flows.
* Ignoring API authentication.
* Assuming generic error messages prevent enumeration.
* Performing uncontrolled brute-force testing.
* Triggering lockouts on real users.
* Testing reset tokens belonging to other users.
* Failing to check token reuse.
* Failing to check multiple reset tokens.
* Ignoring session creation.
* Ignoring session rotation.
* Assuming browser logout means server-side invalidation.
* Failing to replay old requests after logout.
* Ignoring session behavior after password changes.
* Ignoring session behavior after role changes.
* Ignoring concurrent sessions.
* Failing to separate User A and User B sessions.
* Exposing session tokens in screenshots.
* Treating cached pages as evidence of session persistence.
* Reporting cookie attributes without understanding practical impact.
* Failing to verify the final authentication state.

---

# Professional Tips

* Map every path that can establish or recover identity.
* Treat password reset as an authentication mechanism.
* Treat MFA recovery as part of the authentication boundary.
* Use only controlled accounts for active authentication testing.
* Compare complete responses when testing enumeration.
* Keep rate-limit testing small and authorized.
* Capture successful and failed baselines.
* Track authentication state transitions explicitly.
* Record session identifiers before and after login.
* Check rotation rather than assuming it occurs.
* Replay requests after logout to verify invalidation.
* Test multiple concurrent sessions.
* Check security-event effects on existing sessions.
* Test both browser and API authentication where available.
* Distinguish authentication failure from authorization failure.
* Verify server-side behavior rather than relying on UI behavior.
* Redact reusable authentication secrets from evidence.
* Use exact timestamps when session expiration matters.
* Validate findings repeatedly before reporting.
* Explain the failed security boundary and resulting impact.

---

# Practice Tasks

1. Select an authorized application with authentication.

2. Create two controlled accounts.

3. Map every authentication endpoint.

4. Capture a successful login.

5. Capture a failed login.

6. Compare valid-user and invalid-user responses.

7. Test registration for enumeration behavior.

8. Test password reset for enumeration behavior.

9. Review login rate limiting within authorized limits.

10. Record the session mechanism.

11. Inspect session cookie attributes.

12. Record the pre-login session identifier.

13. Log in.

14. Record the post-login session identifier.

15. Determine whether rotation occurred.

16. Capture an authenticated request.

17. Log out.

18. Replay the authenticated request.

19. Verify whether the old session is rejected.

20. Log in from two separate sessions.

21. Change the password from Session A.

22. Observe Session B.

23. Perform password reset using your own account.

24. Test reset-token reuse.

25. Generate multiple reset tokens.

26. Test older-token behavior.

27. Test reset-token expiration where practical.

28. Review how password reset affects existing sessions.

29. Enable MFA if available.

30. Map MFA state transitions.

31. Test protected endpoints before MFA completion.

32. Review MFA recovery.

33. Review MFA disablement.

34. Review active-session management if available.

35. Test session revocation.

36. Review JWTs if used.

37. Compare authentication enforcement across web and API interfaces.

38. Build the authentication test matrix.

39. Build the session test matrix.

40. Validate every suspicious result.

41. Collect minimal sufficient evidence.

42. Redact reusable secrets.

43. Write an authentication and session summary.

---

# Interview Questions

1. What is the difference between authentication and session management?
2. How do you map an application's authentication surface?
3. What authentication functionality should be tested besides login?
4. How do you test for username enumeration?
5. Why are generic error messages not sufficient to prevent enumeration?
6. How do you test login rate limiting safely?
7. What should you examine in a password policy?
8. Why is password reset considered an authentication mechanism?
9. How do you test password-reset tokens?
10. What should happen after a reset token is used?
11. What happens if multiple reset tokens are generated?
12. How do you test reset-token account binding?
13. What security-sensitive headers may affect password-reset workflows?
14. How do you test MFA bypass?
15. What is partial authentication state?
16. How do you determine whether MFA is enforced server-side?
17. What is a session identifier?
18. What cookie attributes are relevant to session security?
19. What is session fixation?
20. How do you test for session fixation?
21. What is session rotation?
22. When should sessions rotate?
23. How do you test logout invalidation?
24. Why is a redirect to the login page insufficient proof of logout?
25. What is the difference between idle and absolute session timeout?
26. What should happen to sessions after a password change?
27. What should happen to sessions after a password reset?
28. What happens when a user's role is revoked while a session is active?
29. How do you test account-disablement effects?
30. How do you test concurrent sessions?
31. What are active-session management features?
32. Where can authentication tokens accidentally leak?
33. What should you review in a JWT?
34. How do you compare authentication controls across APIs and browser interfaces?
35. How do you distinguish authentication issues from authorization issues?
36. How do you validate an account-enumeration finding?
37. How do you validate a session-invalidation finding?
38. What evidence should be collected for authentication vulnerabilities?
39. What sensitive information should be redacted from evidence?
40. Walk me through your complete authentication and session testing methodology.

---

# Quick Revision

* Authentication:

```text
Who Are You?
```

* Session:

```text
How Does the Application Remember You?
```

* Authentication lifecycle:

```text
Register

↓

Login

↓

MFA

↓

Session

↓

Security Events

↓

Logout
```

* Reset lifecycle:

```text
Request

↓

Token

↓

Verification

↓

Password Change

↓

Token Invalidation

↓

Session Review
```

* Session lifecycle:

```text
Create

↓

Rotate

↓

Use

↓

Expire / Revoke

↓

Reject
```

* Enumeration:

```text
Valid Identity

vs

Invalid Identity

↓

Compare Observable Behavior
```

* Session fixation:

```text
Pre-Login Session

vs

Post-Login Session
```

* Logout test:

```text
Authenticated Request

↓

Logout

↓

Replay

↓

Expected Rejection
```

* Security events:

```text
Password Change

Password Reset

Privilege Change

Account Disablement

↓

Reevaluate Active Sessions
```

---

# Authentication & Session Quick Checklist

```text
AUTHENTICATION SURFACE

[ ] Registration
[ ] Login
[ ] Password reset
[ ] Password change
[ ] MFA
[ ] Recovery
[ ] SSO / OAuth
[ ] Logout


ENUMERATION

[ ] Login
[ ] Registration
[ ] Password reset
[ ] Recovery
[ ] Invitations


LOGIN

[ ] Baselines
[ ] Error behavior
[ ] Rate limiting
[ ] Alternate paths
[ ] API authentication


PASSWORD RESET

[ ] Token generation
[ ] Token entropy considerations
[ ] Expiration
[ ] Single use
[ ] Account binding
[ ] Multiple tokens
[ ] Session effects


MFA

[ ] Enrollment
[ ] Challenge
[ ] Verification
[ ] Pre-MFA access
[ ] Recovery
[ ] Disablement
[ ] Remember device


SESSION

[ ] Creation
[ ] Cookie attributes
[ ] Fixation
[ ] Rotation
[ ] Expiration
[ ] Logout invalidation
[ ] Concurrent sessions
[ ] Revocation


SECURITY EVENTS

[ ] Password change
[ ] Password reset
[ ] Role change
[ ] Account disablement
[ ] MFA change


VALIDATION

[ ] Controlled accounts
[ ] Baseline
[ ] Reproduction
[ ] Server-side verification
[ ] Final state
[ ] Impact
[ ] Root cause


EVIDENCE

[ ] Requests
[ ] Responses
[ ] Account context
[ ] Session state
[ ] Timestamps
[ ] Secrets redacted
```

---

# Key Takeaways

* Authentication and session management should be tested as one connected identity lifecycle.
* Authentication testing extends beyond the login form to registration, recovery, password reset, MFA, alternate interfaces, and logout.
* Account enumeration should be tested across multiple identity-related workflows rather than only login.
* Response body, status, length, redirects, timing, headers, and secondary effects may all reveal account existence.
* Rate-limit testing must remain controlled and within authorization.
* Password-reset functionality is an alternative authentication mechanism and should receive the same level of scrutiny as login.
* Reset tokens should be appropriately unpredictable, time-limited, account-bound, and invalidated according to the intended security model.
* MFA should be enforced by server-side authentication state rather than merely by a visible challenge page.
* Session mechanisms may use cookies, bearer tokens, JWTs, refresh tokens, or combinations of these.
* Session security includes creation, rotation, use, expiration, revocation, and termination.
* Session fixation testing compares authentication state before and after login to determine whether a known session can survive the authentication boundary.
* Logout must be validated by replaying authenticated requests rather than relying only on browser redirects.
* Password changes, password resets, role changes, account disablement, and MFA changes are security events that may need to affect active sessions.
* Authorization should reflect current server-side privileges even when an older session was created with greater privileges.
* Concurrent-session testing reveals whether security events and revocation controls propagate correctly.
* Authentication controls should be compared across browser, REST, GraphQL, mobile, and legacy interfaces where applicable.
* Authentication tokens should not leak unnecessarily through URLs, logs, browser history, third parties, or client-side storage.
* JWT analysis should consider signature enforcement, expiration, issuer, audience, subject, roles, permissions, and revocation requirements.
* Authentication findings require reproducible evidence of a failed identity boundary rather than unusual UI behavior alone.
* Session findings require verification of actual server-side authenticated access.
* Evidence should preserve enough context to prove the issue while redacting passwords, reusable tokens, reset tokens, recovery codes, and unnecessary personal data.
* The complete methodology is **map authentication surface → capture baselines → test identity disclosure → test login controls → test recovery → test MFA → identify session mechanism → map session lifecycle → test fixation and rotation → test expiration and revocation → test security-event effects → validate findings → preserve evidence → report**.
