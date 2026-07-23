# Authentication & Token Testing in Modern Applications

> **Module 16 — Modern Application Testing**

> **Progress**

```text
██████░░░░░░░ 6 / 13 Files
```

---

## Overview

* Modern applications use many different mechanisms to establish and maintain user identity.

* Common examples include:

  * Session cookies.
  * Bearer tokens.
  * JSON Web Tokens (JWTs).
  * API keys.
  * OAuth access tokens.
  * Refresh tokens.
  * Single Sign-On (SSO).
  * Multi-Factor Authentication (MFA).
  * Device or session identifiers.

* Authentication testing is not simply:

  ```text
  Can I Log In?
  ```

* A professional assessment asks:

  ```text
  How Is Identity Established?

  ↓

  How Is Identity Maintained?

  ↓

  How Are Credentials Validated?

  ↓

  How Are Sessions Refreshed?

  ↓

  How Are Privileges Enforced?

  ↓

  How Is Access Revoked?
  ```

* Burp Suite allows these flows to be captured, replayed, modified, and compared systematically.

---

## Learning Objectives

* By the end of this lesson, you will be able to:

  * Map modern authentication flows.
  * Distinguish authentication from authorization.
  * Identify session cookies, bearer tokens, JWTs, API keys, and refresh tokens.
  * Analyze login and logout workflows.
  * Understand token lifecycle and trust boundaries.
  * Test missing, invalid, expired, and replaced credentials.
  * Analyze JWT structure safely.
  * Understand JWT signature and claim validation.
  * Test session fixation and rotation concepts.
  * Evaluate logout and revocation behavior.
  * Analyze refresh-token workflows.
  * Test authentication consistency across APIs, GraphQL, and WebSockets.
  * Understand MFA workflow security.
  * Recognize account enumeration and authentication error leakage.
  * Build a repeatable authentication-testing methodology.

---

## Authentication vs Authorization

* Authentication asks:

  ```text
  Who Are You?
  ```

* Authorization asks:

  ```text
  What Are You Allowed to Do?
  ```

* These are separate controls.

---

## Important Principle

```text
Authenticated

≠

Authorized
```

* A valid token may prove identity while the application still fails to enforce:

  ```text
  Object Ownership

  Role Permissions

  Tenant Boundaries

  Function Restrictions
  ```

---

## Modern Authentication Architecture

```text
User

↓

Login Interface

↓

Authentication Service

↓

Credential Validation

↓

Session / Token Issued

↓

Application Requests

↓

Authentication Checked

↓

Authorization Checked

↓

Protected Resource
```

* Each stage creates security questions.

---

## Authentication Attack Surface

* Map:

  ```text
  Registration

  Login

  Logout

  Password Reset

  Password Change

  MFA

  Session Creation

  Token Issuance

  Token Refresh

  Token Revocation

  Account Recovery

  SSO

  API Authentication
  ```

---

## Start With Workflow Mapping

* Before modifying requests, understand the complete legitimate flow.

* Record:

  ```text
  Endpoint

  Method

  Parameters

  Cookies

  Tokens

  Redirects

  Responses

  State Changes
  ```

---

## Authentication Flow Map

```text
Unauthenticated

↓

Submit Credentials

↓

Credentials Validated

↓

Authentication Artifact Issued

↓

Authenticated Session

↓

Refresh / Renewal

↓

Logout / Expiry / Revocation

↓

Unauthenticated
```

---

## Burp Workflow

```text
Proxy

↓

Capture Authentication Flow

↓

HTTP History

↓

Identify Credentials and Tokens

↓

Send Requests to Repeater

↓

Establish Baselines

↓

Modify One Variable

↓

Compare Responses

↓

Verify Authentication State

↓

Document Evidence
```

---

## Baseline Authentication Requests

* Preserve legitimate examples of:

  ```text
  Successful Login

  Failed Login

  Authenticated Request

  Token Refresh

  Logout

  Password Reset

  MFA Verification
  ```

* Baselines make later comparisons reliable.

---

## Identify Authentication Artifacts

* Look for:

  ```text
  Cookie: session=...

  Authorization: Bearer ...

  X-API-Key: ...

  access_token

  refresh_token

  id_token
  ```

* Determine the purpose of each artifact.

---

## Authentication Artifact Card

```text
Artifact:

Type:

Issued By:

Issued During:

Stored Where:

Sent Where:

Purpose:

Lifetime:

Refreshable?:

Revocable?:

Bound to User?:

Bound to Client?:

Privilege Information?:

Observed Security Controls:
```

---

## Session Cookies

* Traditional web applications commonly use cookies.

* Example:

  ```http
  Cookie: session=abc123
  ```

* The server maps the session identifier to authenticated state.

---

## Important Cookie Attributes

* Review:

  ```text
  Secure

  HttpOnly

  SameSite

  Domain

  Path

  Max-Age

  Expires
  ```

---

## `Secure`

* The `Secure` attribute instructs browsers to send the cookie only over secure transport.

* Sensitive authentication cookies generally should not be exposed over plaintext connections.

---

## `HttpOnly`

* `HttpOnly` restricts normal JavaScript access to the cookie.

* This can reduce certain token-theft opportunities through client-side script execution.

* It does not prevent every form of session abuse.

---

## `SameSite`

* `SameSite` influences when cookies are sent in cross-site contexts.

* Common values:

  ```text
  Strict

  Lax

  None
  ```

* Its security relevance often overlaps with:

  ```text
  CSRF

  Cross-Site WebSocket Hijacking

  Cross-Origin Authentication Flows
  ```

---

## Cookie Scope

* Review:

  ```text
  Domain

  Path
  ```

* Overly broad cookie scope may increase exposure across related application components.

---

## Session Identifier Security

* Session identifiers should be:

  ```text
  Unpredictable

  Unique

  Sufficiently Random

  Protected in Transit

  Properly Invalidated
  ```

---

## Session Fixation

* Session fixation occurs when an attacker can cause a victim to authenticate using a session identifier already known to the attacker.

* A key security expectation is:

  ```text
  Pre-Authentication Session

  ↓

  Login

  ↓

  New Authenticated Session Identifier
  ```

---

## Session Rotation Test

* Record the session identifier:

  ```text
  Before Login
  ```

* Then compare it with:

  ```text
  After Login
  ```

* Determine whether sensitive privilege transitions trigger appropriate session rotation.

---

## Important Principle

```text
Authentication State Changes

Should Trigger Appropriate Session Security Controls
```

* Relevant transitions may include:

  ```text
  Login

  Privilege Elevation

  Password Change

  MFA Completion
  ```

---

## Session Logout

* A logout function should do more than change the visible page.

* Security question:

  ```text
  Is the Server-Side Authentication State Actually Invalidated?
  ```

---

## Logout Testing Workflow

```text
Authenticate

↓

Capture Authenticated Request

↓

Log Out

↓

Replay Previous Authenticated Request

↓

Observe Result

↓

Verify Session Invalidity
```

* Use only your own controlled session.

---

## Client-Side Logout vs Server-Side Logout

* Weak logout behavior may occur if the application only:

  ```text
  Deletes Browser Token

  or

  Redirects to Login
  ```

* While the original authentication artifact remains valid server-side.

---

## Session Expiration

* Sessions may expire based on:

  ```text
  Absolute Lifetime

  Idle Timeout

  Token Expiry

  Server Revocation
  ```

* Understand which model the application uses.

---

## Password Change and Sessions

* After a password change, determine expected behavior for:

  ```text
  Current Session

  Other Active Sessions

  Refresh Tokens

  Remember-Me Sessions
  ```

* Requirements vary by application risk and design.

---

## Bearer Tokens

* APIs commonly use:

  ```http
  Authorization: Bearer <token>
  ```

* A bearer token generally means:

  ```text
  Possession of Token

  →

  Ability to Present Token
  ```

* Therefore, token confidentiality is critical.

---

## Bearer Token Testing

* Controlled tests may include:

  ```text
  Remove Token

  Replace With Invalid Token

  Replace With Another Controlled User's Token

  Use Expired Token

  Use Token After Logout

  Use Token After Revocation
  ```

---

## Missing Token

* Baseline:

  ```http
  GET /api/profile
  Authorization: Bearer <token>
  ```

* Controlled variation:

  ```text
  Remove Authorization Header
  ```

* Observe whether the endpoint:

  ```text
  Rejects Request

  Returns Public Data

  Returns Sensitive Data

  Behaves Unexpectedly
  ```

---

## Invalid Token

* Modify the token so it is no longer valid.

* Expected behavior should generally be a clean authentication failure.

* Observe:

  ```text
  Status

  Error

  Data

  Server Behavior
  ```

---

## Token Replacement

* With two controlled users:

  ```text
  User A Token

  User B Token
  ```

* Determine whether identity changes correctly when the credential changes.

* Do not confuse expected identity switching with an authorization flaw.

---

## JSON Web Tokens

* JWT stands for:

  ```text
  JSON Web Token
  ```

* A common signed JWT contains three Base64URL-encoded components:

  ```text
  Header.Payload.Signature
  ```

---

## JWT Structure

```text
xxxxx.yyyyy.zzzzz
```

* Conceptually:

  ```text
  Header

  .

  Payload

  .

  Signature
  ```

---

## JWT Header

* Example:

  ```json
  {
    "alg": "RS256",
    "typ": "JWT"
  }
  ```

* The header may describe:

  ```text
  Algorithm

  Token Type

  Key Identifier
  ```

---

## JWT Payload

* Example:

  ```json
  {
    "sub": "42",
    "role": "user",
    "iat": 1784630000,
    "exp": 1784633600
  }
  ```

* The payload contains claims.

---

## JWT Signature

* The signature allows the server to verify token integrity and authenticity according to the configured signing mechanism.

* Modifying the payload should not produce a valid trusted token unless the server's verification process accepts the resulting signature correctly.

---

## Important Principle

```text
JWT Payload Is Encoded

≠

Encrypted
```

* Base64URL-decoding a JWT payload may reveal its contents.

* Do not place secrets in JWT claims merely because the token looks unreadable.

---

## Common JWT Claims

| Claim | Meaning    |
| ----- | ---------- |
| `sub` | Subject    |
| `iss` | Issuer     |
| `aud` | Audience   |
| `exp` | Expiration |
| `nbf` | Not Before |
| `iat` | Issued At  |
| `jti` | JWT ID     |

* Applications may also use custom claims such as:

  ```text
  role

  permissions

  tenant

  organization
  ```

---

## JWT Analysis Workflow

```text
Capture Token

↓

Identify Structure

↓

Decode Header

↓

Decode Payload

↓

Map Claims

↓

Understand Signing Algorithm

↓

Identify Trust Decisions

↓

Test Validation Carefully
```

---

## JWT Claim Questions

* Ask:

  ```text
  Which Claim Identifies the User?

  Which Claim Identifies the Role?

  Which Claim Identifies the Tenant?

  Is Expiration Enforced?

  Is Audience Enforced?

  Is Issuer Enforced?

  Which Claims Are Trusted for Authorization?
  ```

---

## JWT Modification Principle

* Simply changing:

  ```json
  {
    "role": "user"
  }
  ```

* To:

  ```json
  {
    "role": "admin"
  }
  ```

* Should invalidate a properly signed token.

* A modified payload becoming trusted without valid authorization may indicate serious verification problems.

---

## Signature Validation

* The server should verify signatures according to the intended cryptographic configuration.

* Testing should determine whether tampered tokens are rejected.

---

## Algorithm Handling

* JWT implementations must safely enforce expected algorithms.

* Security questions include:

  ```text
  Is the Expected Algorithm Enforced?

  Are Unsigned Tokens Rejected?

  Are Unexpected Algorithms Rejected?

  Is Key Selection Secure?
  ```

---

## `alg: none`

* Historically, insecure JWT implementations sometimes accepted unsigned tokens using:

  ```json
  {
    "alg": "none"
  }
  ```

* Modern secure implementations should reject unsigned tokens when signatures are required.

* Test only within authorized scope.

---

## Algorithm Confusion

* JWT verification can become vulnerable if an implementation incorrectly handles algorithm selection or key types.

* Do not assume vulnerability merely because multiple algorithms exist.

* A finding requires demonstrating that an invalid or attacker-controlled token is accepted.

---

## `kid` Claim

* JWT headers may contain:

  ```json
  {
    "kid": "key-1"
  }
  ```

* `kid` means:

  ```text
  Key ID
  ```

* It helps the verifier select the correct verification key.

* Treat it as untrusted input that should be handled safely.

---

## JWKS

* Public-key-based JWT systems may expose a JSON Web Key Set.

* JWKS can provide public verification keys.

* Public verification keys are not secrets.

* Security depends on whether the server securely determines which keys are trusted.

---

## `iss` — Issuer

* The issuer identifies who issued the token.

* Security question:

  ```text
  Does the Application Accept Tokens Only From Trusted Issuers?
  ```

---

## `aud` — Audience

* The audience identifies the intended recipient or service.

* Security question:

  ```text
  Can a Token Intended for One Service Be Reused Against Another?
  ```

---

## `exp` — Expiration

* `exp` indicates when the token should expire.

* Test whether expired tokens are rejected.

---

## `nbf` — Not Before

* `nbf` indicates when the token becomes valid.

* Tokens should not normally be accepted before the intended time.

---

## JWT Authorization Claims

* Applications may place authorization information inside claims.

* Example:

  ```json
  {
    "role": "admin",
    "tenant_id": "91"
  }
  ```

* Security depends on:

  ```text
  Signature Validation

  +

  Trusted Issuer

  +

  Claim Validation

  +

  Correct Authorization Logic
  ```

---

## Access Tokens

* Access tokens are commonly short-lived credentials used to access protected resources.

* They may be:

  ```text
  JWTs

  Opaque Tokens

  Other Formats
  ```

* Do not assume every access token is a JWT.

---

## Refresh Tokens

* Refresh tokens are used to obtain new access tokens.

* They are often longer-lived and highly sensitive.

* Conceptual flow:

  ```text
  Access Token Expires

  ↓

  Client Sends Refresh Token

  ↓

  Server Validates Refresh Token

  ↓

  New Access Token Issued
  ```

---

## Refresh Token Security Questions

```text
Is the Refresh Token Rotated?

Can It Be Reused?

Is It Revoked on Logout?

Is It Bound to the Correct Client or Session?

What Happens After Password Change?

What Happens After Account Disablement?
```

---

## Refresh Token Rotation

* Some systems issue a new refresh token whenever the old one is used.

* Conceptually:

  ```text
  Refresh Token A

  ↓ Use

  Access Token B

  +

  Refresh Token B

  ↓

  Refresh Token A Invalidated
  ```

* This can reduce replay risk.

---

## Refresh Token Replay

* If rotation is expected, controlled testing can determine whether an older refresh token remains reusable.

* Avoid excessive requests.

* Record exact token generations carefully.

---

## Token Revocation

* Tokens may be invalidated through:

  ```text
  Logout

  Revocation Lists

  Session State

  Key Rotation

  Short Expiration

  Refresh Token Revocation
  ```

* JWTs are not automatically revocable merely because they are JWTs.

---

## Stateless vs Stateful Tokens

* A token may be validated:

  ```text
  Statelessly
  ```

* Using cryptographic claims alone.

* Or with:

  ```text
  Server-Side State
  ```

* Such as session records or revocation checks.

* Understand the actual architecture before interpreting logout behavior.

---

## API Keys

* API keys may identify:

  ```text
  User

  Application

  Integration

  Service
  ```

* They may appear in:

  ```text
  Headers

  Query Parameters

  Request Bodies
  ```

---

## API Key Security Questions

```text
What Does the Key Authenticate?

What Permissions Does It Grant?

Can It Be Rotated?

Can It Be Revoked?

Is It Exposed Client-Side?

Is It Restricted by Scope?
```

---

## Public vs Secret API Keys

* Not every value named:

  ```text
  api_key
  ```

* Is secret.

* Some keys are intentionally public identifiers.

* Determine actual privilege and sensitivity before reporting exposure.

---

## Authentication in REST APIs

* REST endpoints may use:

  ```text
  Bearer Tokens

  Session Cookies

  API Keys

  Signed Requests
  ```

* Verify authentication consistently across endpoints and versions.

---

## Authentication in GraphQL

* GraphQL commonly authenticates at the HTTP transport layer.

* But authorization occurs across:

  ```text
  Queries

  Mutations

  Fields

  Objects

  Resolvers
  ```

* A valid token does not guarantee correct resolver authorization.

---

## Authentication in WebSockets

* WebSockets may authenticate through:

  ```text
  Handshake Cookies

  Authorization Headers

  Query Tokens

  Initial Authentication Messages
  ```

* Long-lived connections create additional lifecycle concerns.

---

## Cross-Protocol Consistency

* The same application may expose:

  ```text
  Web UI

  REST API

  GraphQL

  WebSocket
  ```

* Security controls should remain consistent.

---

## Example

```text
User Logs Out

↓

Web Session Invalidated

But

↓

Old WebSocket Remains Authorized
```

* This may indicate inconsistent revocation across application channels.

---

## Multi-Factor Authentication

* MFA requires more than one authentication factor.

* Examples:

  ```text
  Password + TOTP

  Password + Push Approval

  Password + Security Key
  ```

---

## MFA Workflow

```text
Username + Password

↓

Primary Authentication Valid

↓

MFA Challenge

↓

Second Factor Validated

↓

Fully Authenticated Session
```

---

## MFA Security Questions

```text
Can Protected Resources Be Accessed Before MFA Completion?

Can the MFA Step Be Skipped?

Is MFA State Bound to the Correct User?

Can Old Challenges Be Reused?

Are Recovery Flows Weaker Than MFA?
```

---

## Partial Authentication State

* Applications may create temporary sessions after password validation but before MFA completion.

* Security question:

  ```text
  What Can This Partially Authenticated Session Access?
  ```

* Protected functionality should not become available prematurely.

---

## MFA Step Skipping

* A professional test examines whether navigating directly to protected endpoints bypasses the second factor.

* Do not rely only on UI redirects.

---

## MFA Challenge Reuse

* Depending on the mechanism, challenges or codes should have appropriate:

  ```text
  Expiration

  Single-Use Behavior

  Attempt Limits

  User Binding
  ```

---

## Recovery Codes

* Recovery codes can bypass the normal second-factor mechanism.

* They should generally be:

  ```text
  Unpredictable

  Single Use

  Protected

  Revocable / Regenerable
  ```

---

## Remembered Devices

* Applications may allow:

  ```text
  Trust This Device
  ```

* Determine:

  ```text
  How Trust Is Represented

  How Long It Lasts

  Whether It Can Be Revoked

  Whether It Is Bound Appropriately
  ```

---

## Password Reset

* Password-reset workflows are part of authentication security.

* Typical flow:

  ```text
  Request Reset

  ↓

  Receive Reset Token

  ↓

  Validate Token

  ↓

  Set New Password

  ↓

  Token Invalidated
  ```

---

## Reset Token Security

* Reset tokens should generally be:

  ```text
  Unpredictable

  Time-Limited

  Single Purpose

  Bound to Correct Account

  Invalidated After Use
  ```

---

## Reset Token Replay

* After successfully using a controlled reset token:

  ```text
  Replay Same Token
  ```

* Determine whether it remains valid.

* Use only your own test account.

---

## Reset Token Leakage

* Review whether sensitive reset tokens appear in:

  ```text
  URLs

  Referrer Paths

  Logs

  Analytics

  Third-Party Requests
  ```

* Actual exposure depends on application behavior.

---

## Password Change

* Test whether password change requires appropriate proof of identity.

* Depending on application design, this may include:

  ```text
  Current Password

  MFA

  Recent Authentication
  ```

---

## Email or Identity Change

* Changing high-value identity attributes may require stronger verification.

* Examples:

  ```text
  Email

  Phone Number

  Recovery Address

  MFA Device
  ```

* These operations can affect account ownership.

---

## Account Enumeration

* Authentication endpoints may reveal whether an account exists.

* Differences may appear in:

  ```text
  Error Message

  Status Code

  Response Length

  Timing

  Password Reset Behavior

  Registration Behavior
  ```

---

## Example

* Existing account:

  ```text
  Incorrect password
  ```

* Nonexistent account:

  ```text
  User not found
  ```

* This may permit username or email enumeration.

---

## Enumeration Is Contextual

* Account existence may or may not be sensitive depending on the application.

* Assess:

  ```text
  User Privacy

  Application Type

  Attack Enablement

  Rate Limits

  Overall Impact
  ```

---

## Timing Differences

* Authentication behavior may differ in response time for:

  ```text
  Existing User

  Nonexistent User
  ```

* Timing analysis requires repeated measurements because network noise can create misleading results.

---

## Brute-Force Protection

* Authentication systems may implement:

  ```text
  Rate Limiting

  Temporary Lockout

  Progressive Delay

  CAPTCHA

  MFA

  Risk-Based Controls
  ```

* Testing must remain within authorized limits.

---

## Safe Rate-Limit Testing

* Use:

  ```text
  Controlled Accounts

  Small Request Counts

  Agreed Testing Limits

  Clear Stop Conditions
  ```

* Do not cause account lockouts or service disruption unintentionally.

---

## Login CSRF

* Some authentication workflows may be vulnerable if an attacker can force a victim's browser into an attacker-controlled authenticated session.

* Security impact depends on what actions or sensitive data may then be associated with that session.

* Evaluate the complete workflow rather than treating missing CSRF tokens alone as proof.

---

## Session Confusion

* Modern applications may maintain several identity artifacts simultaneously.

* Example:

  ```text
  Session Cookie

  +

  Access Token

  +

  Refresh Token
  ```

* Test which artifact actually determines identity for each request.

---

## Conflicting Credentials

* A request may contain multiple authentication signals.

* Example:

  ```http
  Cookie: session=<User-A>
  Authorization: Bearer <User-B>
  ```

* Security question:

  ```text
  Which Identity Does the Server Trust?
  ```

* Controlled tests can reveal precedence and inconsistency.

---

## Credential Precedence

* Record behavior when multiple credentials are present.

* Unexpected precedence may create:

  ```text
  Identity Confusion

  Authorization Inconsistency

  Logging Ambiguity
  ```

---

## Token Scope

* Tokens may be restricted by:

  ```text
  Permissions

  Audience

  Resource

  Tenant

  Application

  Operation
  ```

* Test whether scope restrictions are actually enforced.

---

## Least Privilege

* A token should grant only the permissions required for its purpose.

* Security question:

  ```text
  Does This Token Have More Access Than Intended?
  ```

---

## Token Storage

* Client applications may store tokens in:

  ```text
  Cookies

  Memory

  Local Storage

  Session Storage

  Mobile Secure Storage
  ```

* Security implications depend on application architecture and threat model.

---

## Browser Storage

* Tokens stored in browser-accessible storage may be exposed to successful script execution within the application's origin.

* Cookies with `HttpOnly` cannot normally be read directly by JavaScript.

* However, cookie-based authentication introduces other considerations such as CSRF.

---

## Token Leakage Locations

* Review whether credentials appear in:

  ```text
  URLs

  Query Strings

  Browser History

  Referrer Headers

  Logs

  Error Messages

  Analytics

  Third-Party Requests
  ```

---

## Tokens in URLs

* Sensitive tokens in URLs may be exposed through multiple channels.

* Prefer understanding:

  ```text
  Token Purpose

  Lifetime

  Exposure Path

  Reusability

  Impact
  ```

* Before concluding severity.

---

## Cache Behavior

* Sensitive authenticated responses should not be unintentionally exposed through shared caching.

* Review relevant:

  ```text
  Cache-Control

  Authorization Context

  CDN Behavior

  Shared Cache Behavior
  ```

* Only test cache interactions within authorized scope.

---

## OAuth and OpenID Connect

* Modern applications frequently use OAuth 2.0 and OpenID Connect.

* Simplified roles may include:

  ```text
  User

  Client Application

  Authorization Server

  Resource Server
  ```

* OAuth primarily concerns delegated authorization.

* OpenID Connect adds an identity layer.

---

## Common OAuth Artifacts

```text
Authorization Code

Access Token

Refresh Token

State

Redirect URI

Scope
```

* OpenID Connect may additionally use:

  ```text
  ID Token

  Nonce
  ```

---

## OAuth Testing Focus

* Important areas include:

  ```text
  Redirect URI Validation

  State Handling

  Authorization Code Binding

  PKCE

  Token Audience

  Scope Enforcement

  Token Leakage

  Account Linking
  ```

* Detailed OAuth testing can be treated as a dedicated advanced topic.

---

## `state`

* OAuth flows commonly use:

  ```text
  state
  ```

* To help bind authorization responses to the initiating client flow and mitigate request-forgery risks.

* Its implementation should be validated in context.

---

## PKCE

* PKCE stands for:

  ```text
  Proof Key for Code Exchange
  ```

* It strengthens authorization-code flows by binding the authorization request to the token exchange.

---

## ID Token vs Access Token

* An ID token communicates authentication information about the user to the client.

* An access token is intended to authorize access to protected resources.

* They should not be treated as interchangeable without explicit protocol design.

---

## SSO

* Single Sign-On may involve:

  ```text
  OAuth

  OpenID Connect

  SAML

  Enterprise Identity Providers
  ```

* Authentication boundaries become distributed across multiple systems.

---

## SSO Security Questions

```text
Who Authenticates the User?

Who Issues the Assertion or Token?

Who Validates It?

Which Claims Are Trusted?

How Is the Account Linked?

How Is Logout Handled?
```

---

## Authentication State Matrix

| State                           | Expected Access                         |
| ------------------------------- | --------------------------------------- |
| Unauthenticated                 | Public only                             |
| Password validated, MFA pending | Limited transitional access             |
| Fully authenticated             | Authorized user access                  |
| Logged out                      | Public only                             |
| Expired                         | Reauthentication or refresh as designed |
| Revoked                         | Denied                                  |

* Compare actual behavior against expected state.

---

## Credential Test Matrix

| Test                    | Expected                     |
| ----------------------- | ---------------------------- |
| No credential           | Reject protected access      |
| Invalid credential      | Reject                       |
| Expired credential      | Reject or controlled refresh |
| Valid User A credential | User A identity              |
| Valid User B credential | User B identity              |
| Revoked credential      | Reject                       |
| Logged-out session      | Reject                       |

---

## Token Lifecycle Map

```text
Issue

↓

Use

↓

Expire

↓

Refresh

↓

Rotate

↓

Revoke

↓

Invalidate
```

* Not every system implements every stage in the same way.

---

## Authentication Testing Workflow

```text
Define Scope

↓

Map Authentication Entry Points

↓

Capture Legitimate Flows

↓

Identify Authentication Artifacts

↓

Map Token / Session Lifecycle

↓

Establish Baselines

↓

Test Missing / Invalid Credentials

↓

Test Controlled Credential Replacement

↓

Analyze Session Rotation

↓

Analyze Expiration

↓

Analyze Refresh

↓

Analyze Logout / Revocation

↓

Review MFA / Recovery

↓

Check Cross-Protocol Consistency

↓

Validate Security Impact

↓

Document Evidence
```

---

## Step 1 — Map Entry Points

* Identify:

  ```text
  Login

  Registration

  Password Reset

  MFA

  SSO

  API Authentication

  Token Refresh

  Logout
  ```

---

## Step 2 — Capture Legitimate Flows

* Complete each workflow normally.

* Preserve requests and responses.

---

## Step 3 — Identify Credentials

* Record:

  ```text
  Cookies

  Access Tokens

  Refresh Tokens

  JWTs

  API Keys

  Temporary MFA State
  ```

---

## Step 4 — Determine Identity Source

* Ask:

  ```text
  Which Credential Actually Establishes Identity?
  ```

* Test only with controlled credentials.

---

## Step 5 — Map Lifecycle

* Determine:

  ```text
  Issuance

  Lifetime

  Refresh

  Rotation

  Revocation

  Expiration
  ```

---

## Step 6 — Test Missing Credentials

* Remove one authentication artifact at a time.

* Observe actual access.

---

## Step 7 — Test Invalid Credentials

* Use intentionally invalid controlled values.

* Verify clean rejection.

---

## Step 8 — Test Credential Replacement

* Replace User A's credential with User B's controlled credential.

* Verify identity and authorization behavior.

---

## Step 9 — Test Session Rotation

* Compare identifiers:

  ```text
  Before Login

  After Login

  After Privilege Change
  ```

* Where applicable.

---

## Step 10 — Test Logout

* Replay a previously valid authenticated request after logout.

* Determine whether the credential remains usable.

---

## Step 11 — Test Expiration and Refresh

* Observe:

  ```text
  Expired Access Token

  Refresh Request

  New Access Token

  Refresh Token Rotation
  ```

---

## Step 12 — Test MFA State

* Determine what access exists:

  ```text
  Before MFA

  After MFA

  After Recovery
  ```

---

## Step 13 — Check Other Protocols

* Compare authentication state across:

  ```text
  HTTP

  REST

  GraphQL

  WebSocket
  ```

---

## Step 14 — Validate Findings

* Confirm:

  ```text
  Reproducibility

  Authentication State

  Identity

  Authorization

  Token Validity

  Security Impact
  ```

---

## Example — Logout Invalidation

### Baseline

```text
Login

↓

Capture Authenticated GET /api/profile

↓

200 OK
```

### Test

```text
Logout

↓

Replay Same Authenticated Request
```

### Expected

```text
Credential Rejected

or

Session No Longer Authorized
```

* Interpret according to the application's documented session model.

---

## Example — Session Rotation

```text
Anonymous Session:

ABC123

↓

Login

↓

Authenticated Session:

XYZ789
```

* This demonstrates session identifier rotation.

* If the identifier remains unchanged, investigate whether actual fixation is possible before reporting.

---

## Example — Expired Token

```text
Valid Access Token

↓

Token Expires

↓

Protected Request

↓

Authentication Failure

or

Controlled Refresh Flow
```

* Verify actual expiry enforcement.

---

## Example — Conflicting Credentials

### Request

```http
GET /api/profile HTTP/1.1
Cookie: session=<User-A-Session>
Authorization: Bearer <User-B-Token>
```

### Question

```text
Which Identity Does the Server Use?
```

* Use only controlled accounts.

* Record whether behavior is consistent and secure.

---

## Example — MFA State

```text
Password Accepted

↓

Temporary Authentication State

↓

Before MFA Completion

↓

Request Protected Resource
```

* Determine whether sensitive functionality becomes available prematurely.

---

## Example — Refresh Rotation

```text
Refresh Token A

↓

Request New Access Token

↓

Receive Refresh Token B

↓

Replay Refresh Token A
```

* If rotation is expected, determine whether Token A is rejected.

---

## Evidence Requirements

* Strong authentication findings should include:

  ```text
  Endpoint

  Authentication Mechanism

  User / Role

  Original Credential State

  Modified Credential State

  Original Request

  Modified Request

  Responses

  Session / Token Lifecycle

  Reproduction Steps

  Demonstrated Security Impact
  ```

---

## Anomaly vs Vulnerability

* Anomaly:

  ```text
  Session ID Did Not Change
  ```

* Validated vulnerability:

  ```text
  Attacker Can Fix Known Session

  +

  Victim Authenticates

  +

  Attacker Reuses Same Session

  +

  Unauthorized Account Access
  ```

* Always validate exploitability.

---

## Another Example

* Anomaly:

  ```text
  Logout Does Not Immediately Destroy a JWT
  ```

* This may be expected in a stateless short-lived token architecture.

* A finding depends on:

  ```text
  Intended Revocation Model

  Token Lifetime

  Refresh Revocation

  Security Requirements

  Demonstrable Impact
  ```

---

## False Positive Control

* Before concluding:

  ```text
  Restore Clean Baseline

  ↓

  Confirm Credential Used

  ↓

  Confirm User Identity

  ↓

  Confirm Token Generation

  ↓

  Repeat Controlled Test

  ↓

  Verify Authentication State

  ↓

  Verify Security Impact
  ```

---

## Authentication Testing Checklist

### Login

* Review:

  ```text
  Successful Login

  Failed Login

  Error Differences

  Session Creation

  Token Issuance

  Session Rotation
  ```

---

### Cookies

* Review:

  ```text
  Secure

  HttpOnly

  SameSite

  Domain

  Path

  Lifetime
  ```

---

### Tokens

* Review:

  ```text
  Missing

  Invalid

  Expired

  Replaced

  Revoked

  Scope
  ```

---

### JWTs

* Review:

  ```text
  Header

  Payload

  Signature

  Algorithm

  Issuer

  Audience

  Expiration

  Authorization Claims

  Key Selection
  ```

---

### Refresh

* Review:

  ```text
  Rotation

  Replay

  Expiry

  Revocation

  Logout

  Password Change
  ```

---

### MFA

* Review:

  ```text
  Step Skipping

  Partial Authentication

  Challenge Binding

  Reuse

  Recovery

  Remembered Devices
  ```

---

### Logout

* Review:

  ```text
  Session Invalidity

  Access Token Behavior

  Refresh Token Behavior

  WebSocket State

  Other Sessions
  ```

---

### Recovery

* Review:

  ```text
  Password Reset

  Token Lifetime

  Single Use

  Account Binding

  Session Effects
  ```

---

### Cross-Protocol

* Review:

  ```text
  Web

  REST

  GraphQL

  WebSocket
  ```

* Confirm consistent authentication state.

---

## Authentication Investigation Journal

```text
Application:

Date:

Scope:

Login Endpoint:

Logout Endpoint:

Registration:

Password Reset:

Password Change:

MFA:

SSO:

Authentication Mechanisms:

Session Cookies:

Cookie Attributes:

Access Tokens:

Refresh Tokens:

JWTs:

API Keys:

Token Storage:

Token Lifetime:

Session Rotation:

Session Expiration:

Logout Behavior:

Refresh Rotation:

Revocation:

Password Change Effects:

MFA State:

Recovery Codes:

Remembered Devices:

Account Enumeration:

Rate Limiting:

Cross-Protocol Behavior:

REST Authentication:

GraphQL Authentication:

WebSocket Authentication:

High-Priority Hypotheses:

Validated Findings:

False Positives:

Evidence:

Next Steps:
```

---

## Professional Tips

* Map the entire authentication lifecycle instead of testing only the login form.
* Keep authentication and authorization as separate testing questions.
* Identify exactly which credential establishes identity for each request.
* Preserve successful and failed authentication baselines.
* Track cookies, access tokens, refresh tokens, JWTs, and temporary authentication states separately.
* Use controlled accounts when replacing credentials.
* Analyze session rotation at important authentication-state transitions.
* Verify logout server-side by replaying previously valid controlled requests.
* Understand token architecture before interpreting revocation behavior.
* Decode JWTs to understand claims, but remember that decoding does not validate trust.
* Treat JWT payload data as readable unless separately encrypted.
* Verify signature, issuer, audience, expiry, and claim handling conceptually as separate controls.
* Do not assume every token is a JWT.
* Treat refresh tokens as high-value credentials.
* Track token generations carefully when testing rotation.
* Test MFA as a workflow rather than only testing the code-entry screen.
* Examine partial-authentication states for premature access.
* Include password reset and recovery mechanisms in the authentication attack surface.
* Compare authentication state across HTTP APIs, GraphQL, and WebSockets.
* Treat unusual behavior as a hypothesis until exploitability and impact are demonstrated.

---

## Common Mistakes

* Treating authentication and authorization as the same control.
* Testing only the login page.
* Ignoring logout.
* Ignoring token refresh.
* Ignoring session rotation.
* Assuming a JWT is encrypted.
* Modifying JWT claims and assuming acceptance without verifying the signature behavior.
* Treating every JWT implementation as vulnerable to historical attacks.
* Assuming every API key is secret.
* Assuming logout must instantly invalidate every stateless access token without understanding architecture.
* Ignoring refresh-token revocation.
* Testing with uncontrolled third-party accounts.
* Changing several authentication artifacts simultaneously.
* Failing to determine credential precedence.
* Ignoring MFA partial-authentication states.
* Ignoring recovery flows.
* Treating different login errors as automatically high severity.
* Performing excessive brute-force or rate-limit testing.
* Ignoring authentication differences across REST, GraphQL, and WebSockets.
* Reporting missing cookie attributes without evaluating actual security context.
* Confusing a security hardening recommendation with an exploitable vulnerability.
* Failing to verify actual authentication state after each test.

---

## Practice Tasks

### Task 1 — Select an Authorized Application

* Use:

  ```text
  Deliberately Vulnerable Lab

  System You Own

  or

  Explicitly Authorized Target
  ```

---

### Task 2 — Map Authentication Entry Points

* Identify:

  ```text
  Login

  Logout

  Registration

  Password Reset

  Password Change

  MFA

  Token Refresh
  ```

---

### Task 3 — Capture a Complete Login

* Record:

  ```text
  Requests

  Responses

  Cookies

  Tokens

  Redirects
  ```

---

### Task 4 — Identify Authentication Artifacts

* Classify each as:

  ```text
  Session Cookie

  Access Token

  Refresh Token

  JWT

  API Key

  Temporary State
  ```

---

### Task 5 — Review Cookie Attributes

* Record:

  ```text
  Secure

  HttpOnly

  SameSite

  Domain

  Path
  ```

---

### Task 6 — Analyze a JWT

* If the application legitimately uses one, identify:

  ```text
  Header

  Claims

  Algorithm

  Expiration

  Issuer

  Audience
  ```

* Do not expose real credentials in repository documentation.

---

### Task 7 — Test Missing Authentication

* Remove one authentication artifact from a controlled protected request.

* Record the result.

---

### Task 8 — Test Controlled Credential Replacement

* With two test accounts, compare identity behavior.

---

### Task 9 — Review Session Rotation

* Compare session identifiers before and after authentication.

---

### Task 10 — Test Logout

* Replay a previously valid controlled request after logout.

* Record whether authentication remains valid.

---

### Task 11 — Map Token Refresh

* Record:

  ```text
  Access Token

  Expiration

  Refresh Request

  New Token

  Rotation Behavior
  ```

---

### Task 12 — Review MFA

* If available, map:

  ```text
  Pre-MFA State

  MFA Challenge

  Post-MFA State

  Recovery
  ```

---

### Task 13 — Compare Protocols

* Determine whether authentication state is consistent across:

  ```text
  Web

  REST

  GraphQL

  WebSocket
  ```

* Where available.

---

### Task 14 — Generate Security Hypotheses

* Write at least five questions such as:

  ```text
  Is the session rotated after login?

  Is the old credential invalid after logout?

  Is refresh-token rotation enforced?

  Can protected resources be accessed before MFA completion?

  Are authentication states consistent across WebSockets and HTTP?
  ```

---

## Interview Questions

1. What is authentication?
2. What is the difference between authentication and authorization?
3. What authentication mechanisms are common in modern applications?
4. How would you map an application's authentication attack surface?
5. Why should successful and failed authentication requests be preserved as baselines?
6. What is a session cookie?
7. What do the `Secure`, `HttpOnly`, and `SameSite` cookie attributes do?
8. What is session fixation?
9. Why should session identifiers often rotate after login?
10. How would you test whether logout actually invalidates a session?
11. What is the difference between client-side and server-side logout?
12. What is a bearer token?
13. How would you test bearer-token authentication?
14. What is a JWT?
15. What are the three common parts of a signed JWT?
16. Is a JWT payload encrypted by default?
17. What are common registered JWT claims?
18. What is the purpose of a JWT signature?
19. Why should tampered JWTs be rejected?
20. What is the historical `alg: none` issue?
21. What is JWT algorithm confusion?
22. What is `kid` used for?
23. What is JWKS?
24. Why are `iss` and `aud` validation important?
25. What is the difference between an access token and a refresh token?
26. What is refresh-token rotation?
27. Why can refresh-token replay be security-relevant?
28. How can tokens be revoked?
29. What is the difference between stateless and stateful token validation?
30. Are all API keys secret?
31. What is token scope?
32. Why can storing tokens in browser-accessible storage affect security?
33. Where can authentication tokens leak?
34. What is MFA?
35. What is a partial-authentication state?
36. How would you test for MFA step skipping?
37. What security properties should password-reset tokens have?
38. What is account enumeration?
39. Why are timing-based enumeration tests difficult to validate?
40. How should authentication rate-limit testing be performed safely?
41. What are conflicting credentials?
42. Why is credential precedence security-relevant?
43. What is OAuth 2.0 broadly used for?
44. What does OpenID Connect add?
45. What are `state` and PKCE used for conceptually?
46. What is the difference between an ID token and an access token?
47. Why should authentication be tested across REST, GraphQL, and WebSockets?
48. Why might logout behavior differ for a stateless JWT?
49. What evidence is required for a strong authentication vulnerability report?
50. Describe your complete methodology for testing authentication in a modern application using Burp Suite.

---

## Quick Revision

* Authentication:

  ```text
  Who Are You?
  ```

* Authorization:

  ```text
  What May You Do?
  ```

* Important rule:

  ```text
  Authenticated

  ≠

  Authorized
  ```

* Authentication lifecycle:

  ```text
  Login

  ↓

  Issue Credential

  ↓

  Use

  ↓

  Refresh

  ↓

  Expire

  ↓

  Revoke
  ```

* Common artifacts:

  ```text
  Session Cookie

  Bearer Token

  JWT

  API Key

  Access Token

  Refresh Token
  ```

* Session fixation defense:

  ```text
  Pre-Login Session

  ↓

  Authenticate

  ↓

  Rotate Session Identifier
  ```

* Logout test:

  ```text
  Authenticate

  ↓

  Capture Request

  ↓

  Logout

  ↓

  Replay Request

  ↓

  Verify Invalidity
  ```

* JWT:

  ```text
  Header

  .

  Payload

  .

  Signature
  ```

* Important JWT rule:

  ```text
  Encoded

  ≠

  Encrypted
  ```

* JWT trust:

  ```text
  Signature

  +

  Issuer

  +

  Audience

  +

  Time Claims

  +

  Authorization Logic
  ```

* Refresh:

  ```text
  Expired Access Token

  ↓

  Refresh Token

  ↓

  New Access Token
  ```

* MFA:

  ```text
  Primary Factor

  ↓

  Second Factor

  ↓

  Fully Authenticated
  ```

* Cross-protocol review:

  ```text
  Web

  REST

  GraphQL

  WebSocket
  ```

* Professional workflow:

  ```text
  Map

  ↓

  Capture

  ↓

  Identify Credentials

  ↓

  Understand Lifecycle

  ↓

  Baseline

  ↓

  Modify One Variable

  ↓

  Verify Authentication State

  ↓

  Validate Impact
  ```

---

## Key Takeaways

* Modern authentication testing covers the complete identity lifecycle rather than only the login form.
* Authentication and authorization must always be analyzed as separate security controls.
* Map registration, login, logout, password reset, password change, MFA, SSO, token refresh, revocation, and recovery functionality.
* Identify exactly which cookies, tokens, keys, or temporary states establish identity.
* Session cookies should be analyzed for transport protection, browser-access restrictions, cross-site behavior, scope, lifetime, rotation, and invalidation.
* Session identifiers should be handled securely across authentication and privilege transitions.
* Logout must be evaluated by checking actual server-side or token-level authentication behavior rather than relying on UI changes.
* Bearer tokens are sensitive because possession commonly enables their use.
* JWTs are encoded structures, not encrypted secrets by default.
* JWT security depends on correct signature verification, algorithm handling, trusted key selection, issuer, audience, time claims, and authorization logic.
* Historical JWT weaknesses should not be assumed to exist; they require controlled validation.
* Access tokens and refresh tokens have different purposes and security lifecycles.
* Refresh-token rotation and revocation can reduce replay risk when implemented correctly.
* Stateless authentication architecture must be understood before interpreting token invalidation behavior.
* MFA testing must include partial-authentication states, step skipping, challenge handling, recovery mechanisms, and remembered-device behavior.
* Password reset and account recovery are part of the authentication boundary and may be weaker than normal login.
* Account enumeration findings require contextual impact analysis.
* Authentication rate-limit testing should use controlled accounts, low request volumes, and explicit authorization.
* Conflicting credentials can reveal identity-precedence inconsistencies.
* Authentication state should be evaluated consistently across traditional web requests, REST APIs, GraphQL, and WebSockets.
* Unexpected authentication behavior is only a hypothesis until reproducibility, identity state, and security impact are demonstrated.
* The professional authentication workflow is **map entry points → capture legitimate flows → identify authentication artifacts → map lifecycle → establish baselines → test one credential condition at a time → verify identity and state → review refresh/revocation/MFA → compare protocols → validate impact → document evidence**.
