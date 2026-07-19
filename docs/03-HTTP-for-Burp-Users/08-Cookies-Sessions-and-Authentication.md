# Cookies, Sessions, and Authentication

> **Module Progress**

```text id="r7w3mt"
████████░░░░░ 8 / 13 Lessons
```

- HTTP is fundamentally request-response oriented and generally stateless at the protocol level.

- Yet modern applications need to remember:

  * Who is logged in.
  * Which account is active.
  * What permissions a user has.
  * Shopping-cart contents.
  * Preferences.
  * Multi-step workflow state.

- Applications build this state on top of HTTP using mechanisms such as:

  ```text id="p4m8qn"
  Cookies

  Session Identifiers

  Authorization Tokens

  Server-Side Session State
  ```

- Understanding these mechanisms is essential for using Burp effectively.

---

## Stateless HTTP Mental Model

- Without application-level state:

  ```text id="v6n2kx"
  Request 1

  ↓

  Response 1

  
  Request 2

  ↓

  Response 2  
  ```

- The second request does not automatically inherit a logged-in identity merely because the first request occurred earlier.

- The application must associate requests with state.

---

## Application Session Model

- A common session-based login flow is:

  ```text id="q9m3wr"
  User Submits Credentials

  ↓

  Server Validates Credentials

  ↓

  Server Creates Authenticated Session

  ↓

  Server Returns Session Identifier

  ↓

  Browser Stores Identifier

  ↓

  Browser Sends Identifier on Later Requests

  ↓

  Server Maps Identifier to Session State
  ```

- This creates the experience of staying logged in across many HTTP requests.

---

## Cookie vs Session

- These terms are related but not identical.

- A **cookie** is data stored by the client and sent according to cookie rules.

- A **session** is application state associated with a user or interaction.

- A cookie may contain:

  ```text id="n5p8mv"
  Session Identifier
  ```

- while the actual session state may exist:

  ```text id="x7m4qt"
  Server-Side
  ```

- Conceptually:

  ```text id="m3q9vr"
  Browser Cookie

  session=abc123

  ↓

  Server Looks Up abc123

  ↓

  Authenticated Session for User A
  ```

---

## Set-Cookie

- Servers issue or update cookies using:

  ```http id="k8n2pw"
  Set-Cookie: session=abc123
  ```

- This appears in a response.

- Conceptually:

  ```text id="r4m7qx"
  Server

  ↓

  Set-Cookie

  ↓

  Browser Stores Cookie
  ```  

---

## Cookie

- The browser later sends applicable cookies using:

  ```http id="t9p3mv"
  Cookie: session=abc123
  ```

- Conceptually:

  ```text id="q6n8kr"
  Browser

  ↓

  Cookie

  ↓

  Server
  ```

- Remember:
  
  ```text id="m5q2vn"
  Set-Cookie

  Server → Client
  ```

  ```text id="x8n4pk"
  Cookie

  Client → Server
  ```

---

## Example Login Flow

- Request:

  ```http id="p7m3qw"
  POST /login HTTP/1.1
  Host: example.com
  Content-Type: application/x-www-form-urlencoded

  username=alice&password=example
  ```

- Response:

  ```http id="v2n9mr"
  HTTP/1.1 302 Found
  Location: /dashboard
  Set-Cookie: session=abc123; Secure; HttpOnly
  ```

- Next request:

  ```http id="k4m8qx"
  GET /dashboard HTTP/1.1
  Host: example.com
  Cookie: session=abc123
  ```

- The cookie may now identify the authenticated session.

---

## Authentication vs Authorization

- This distinction is fundamental.

### Authentication

- Authentication asks:

  ```text id="r6q2nv"
  Who Are You?
  ```

- Examples:

  * Password login.
  * MFA.
  * Session validation.
  * API token validation.

---

### Authorization

- Authorization asks:

  ```text id="n9m4pk"
  Are You Allowed to Perform This Action
  on This Resource?
  ```

- Examples:

  * Can User A read User B's invoice?
  * Can a normal user access an admin endpoint?
  * Can this account modify this order?
  * Can this token perform this API operation?

---

## Mental Model

```text id="q3m7vx"
Authentication

↓

Identity Established

↓

Authorization

↓

Permission Evaluated
```

- A user can be correctly authenticated and still encounter broken authorization.

---

## Authentication Success Does Not Mean Universal Access

- Suppose:

  ```text id="m8p3qw"
  User A

  ↓

  Valid Session

  ↓

  GET /api/invoices/4821
  ```  

- The server must ask:

  ```text id="x5n9kr"
  Is User A Allowed to Access Invoice 4821?
  ```

- It is not enough to ask:

  ```text id="t7m2pv"
  Is This Session Valid?
  ```

- Authentication and object-level authorization are separate checks.

---

## Session Identifiers

- A session identifier might look like:

  ```text id="q8m4nr"
  session=abc123xyz
  ```

- or:

  ```text id="p4n8mv"
  JSESSIONID=...

  PHPSESSID=...

  connect.sid=...
  ```

- Names vary by technology.

- The identifier often acts as a credential.

- If another party obtains a valid authenticated session identifier, the application may treat requests carrying it as belonging to that user.

---

## Treat Session IDs as Secrets

- Do not expose real session identifiers in:

  * GitHub repositories.
  * Screenshots intended for public use.
  * Public reports.
  * Chat logs.
  * Training material.

- Redact them where appropriate.

- Example:

  ```text id="v9m3qx"
  session=<REDACTED>
  ```

---

## Cookie Scope

- Cookies are governed by attributes and browser rules that determine when they are sent.

- Important attributes include:

  ```text id="k6n2pr"
  Domain

  Path

  Secure

  HttpOnly

  SameSite

  Expires

  Max-Age
  ```  

- Each controls a different aspect of cookie behavior.

---

## Domain

- Example:

  ```http id="r3m8vw"
  Set-Cookie: session=abc123; Domain=example.com
  ```

- The `Domain` attribute influences which hosts can receive the cookie under browser cookie rules.

- Cookie scope should be no broader than necessary.

---

## Host-Only Cookies

- If a cookie is set without a `Domain` attribute, it is generally host-only.

- Conceptually:

  ```text id="n7q4mx"
  Set by:

  app.example.com

  ↓

  Sent Back Only to That Host
  according to host-only rules
  ```

- This is narrower than explicitly allowing a broader domain scope.

---

## Path

- Example:

  ```http id="m8q4vx"
  Set-Cookie: preference=dark; Path=/account
  ```

- The `Path` attribute influences which request paths receive the cookie.

- Conceptually:

  ```text id="k5n9pr"
  /account

  ↓

  Cookie Applicable
  ```

- but possibly not unrelated paths.

- Do not treat `Path` as a strong authorization boundary.

---

## Secure

- Example:

  ```http id="r7w3mt"
  Set-Cookie: session=abc123; Secure
  ```

- The `Secure` attribute instructs browsers to send the cookie only over secure transport contexts, normally HTTPS.

- Conceptually:

  ```text id="p4m8qn"
  Secure Cookie

  ↓

  HTTPS Requests
  ```

- This helps protect cookies from being transmitted over unencrypted HTTP connections.

---

## HttpOnly

- Example:

  ```http id="v6n2kx"
  Set-Cookie: session=abc123; HttpOnly
  ```

- `HttpOnly` restricts access to the cookie through common client-side JavaScript cookie APIs.

- Conceptually:

  ```text id="q9m3wr"
  JavaScript

  ↓

  Cannot Normally Read HttpOnly Cookie
  ```

- But the browser can still send the cookie automatically with applicable HTTP requests.

---

## HttpOnly Does Not Prevent All Session Attacks

- Do not conclude:

  ```text id="n5p8mv"
  HttpOnly

  =

  Session Completely Safe
  ```

- It mitigates certain client-side cookie theft scenarios.

- It does not solve:

  * Broken authorization.
  * Session fixation.
  * Weak session invalidation.
  * CSRF by itself.
  * Other session-management flaws.

- Security controls solve specific problems.

---

## SameSite

- The `SameSite` attribute influences whether cookies are sent in certain cross-site request contexts.

- Common values include:

  ```text id="x7m4qt"
  Strict

  Lax
  
  None
  ```

- This is particularly relevant to cross-site request behavior and CSRF defenses.

---

## SameSite=Strict

- Conceptually:

  ```text id="m3q9vr"
  Cross-Site Context

  ↓
  
  Cookie Strongly Restricted
  ```

- This provides stronger cross-site restrictions but may affect legitimate navigation flows.

---

## SameSite=Lax

- `Lax` allows cookies in some cross-site navigation situations while restricting many other cross-site request contexts.

- It is a balance between usability and cross-site protection.

- Exact browser behavior matters.

---

## SameSite=None

- Example:

  ```http id="k8n2pw"
  Set-Cookie: session=abc123; SameSite=None; Secure
  ```

- This allows broader cross-site cookie use.

- Modern browsers require `Secure` with `SameSite=None`.

- This is common when legitimate cross-site functionality is required.

---

## Expires and Max-Age

- Cookies may be persistent.

- Example:

  ```http id="r4m7qx"
  Set-Cookie: preference=dark; Max-Age=86400
  ```

or use an expiration date.

- These attributes determine how long a browser may retain the cookie.

---

## Session Cookies vs Persistent Cookies

- Conceptually:

  ```text id="t9p3mv"
  Session Cookie

  ↓

  Usually No Explicit Persistent Lifetime

  ↓

  Browser Session-Oriented
  ```

- versus:

  ```text id="q6n8kr"
  Persistent Cookie

  ↓

  Expires / Max-Age

  ↓

  Stored Until Expiration or Removal
  ```

- Browser behavior and application design still matter.

---

## Session Creation

- During authentication, identify:

  ```text id="m5q2vn"
  Cookie Before Login

  Cookie After Login
  ```

- Questions:

  ```text id="x8n4pk"
  Was a new session created?

  Did the identifier change?

  Was the existing session upgraded?

  Were multiple cookies involved?
  ```

- This helps you understand session lifecycle.

---

## Session Rotation

- A secure application commonly rotates or replaces session identifiers at important authentication transitions.

- Conceptually:

  ```text id="p7m3qw"
  Anonymous Session ID

  ↓

  Successful Login

  ↓

  New Authenticated Session ID
  ```

- This helps reduce session fixation risks.

---

## Session Fixation Concept

- A dangerous pattern would be:

  ```text id="v2n9mr"
  Attacker Knows / Controls Session ID

  ↓

  Victim Authenticates Using Same Session

  ↓

  Session ID Remains Unchanged

  ↓

  Known Identifier Becomes Authenticated
  ```

- Proper session rotation after authentication helps prevent this.

---

## Test Session Rotation Carefully

- Compare:

  ```text id="k4m8qx"
  Before Login:
  session=AAA
  ```

- with:

  ```text id="r6q2nv"
  After Login:
  session=BBB
  ```

- A change may indicate rotation.

- No change deserves investigation, but does not automatically prove exploitability.

- Understand the full session mechanism.

---

## Logout

- A secure logout process should terminate or invalidate the authenticated session appropriately.

- Conceptually:

  ```text id="n9m4pk"
  Authenticated Session

  ↓

  Logout

  ↓

  Session Invalidated

  ↓  

  Old Credential No Longer Grants Access
  ```

---

## Cookie Deletion vs Server Invalidation

- A logout response may clear a browser cookie:

  ```http id="q3m7vx"
  Set-Cookie: session=; Max-Age=0
  ```

- But ask:

  > Was the server-side session actually invalidated?

- If an old session identifier can still be replayed successfully, simply deleting the browser's local cookie may be insufficient.

---

## Logout Verification Workflow

```text id="m8p3qw"
Capture Authenticated Request

↓

Save Session Credential Securely

↓

Log Out Normally

↓

Replay Previously Authenticated Request

↓

Observe Whether Old Session Still Works
```

- Perform this only within authorized testing scope.

---

## Password Change and Session Lifecycle

- Important account events may need to affect existing sessions.

- Examples:

  ```text id="x5n9kr"
  Password Change

  Password Reset

  Account Disable

  Privilege Change
  ```

- Questions:

  ```text id="t7m2pv"
  Should old sessions remain valid?

  Should all sessions be invalidated?
  
  Should only selected sessions be revoked?
  ```

- Expected behavior depends on application security requirements.

---

## Bearer Tokens

- Some applications use:

  ```http id="q8m4nr"
  Authorization: Bearer TOKEN
  ```

- rather than cookie-based session identifiers.

- Conceptually:

  ```text id="p4n8mv"
  Client Possesses Token

  ↓

  Client Sends Token

  ↓

  Server Validates Token

  ↓
  
  Identity / Permissions Determined
  ```

- A bearer token is generally usable by whoever possesses it, subject to its validity and application controls.

- Treat it as sensitive.

---

## Cookies vs Bearer Tokens

- Simplified comparison:

  ```text id="v9m3qx"
  Cookie-Based Session

  Browser Often Sends Cookie Automatically
  according to cookie rules
  ```

  ```text id="k6n2pr"
  Bearer Token

  Client Explicitly Places Token
  in Authorization Header
  ```

- Both can represent authenticated state.

- Their browser behavior and security properties differ.

---

## JWTs

- Some bearer tokens use JSON Web Token format.

- A JWT often visually resembles:

  ```text id="r3m8vw"
  xxxxx.yyyyy.zzzzz
  ```

- But:

  ```text id="n7q4mx"
  Looks Like JWT

  ≠

  Automatically Secure

  ≠

  Automatically Insecure
  ```

- Security depends on:

  * Signature verification.
  * Algorithm handling.
  * Claims validation.
  * Expiration.
  * Key management.
  * Authorization logic.

---

## JWT Claims vs Authorization

- A token may contain claims such as:

  ```json id="m8q4vx"
  {
    "sub": "105",
    "role": "user"
  }
  ```

- The presence of a claim does not itself prove how the server uses it.

- Ask:

  ```text id="k5n9pr"
  Is the token integrity protected?

  Does the server validate it correctly?

  Does the server trust this claim?

  Is authorization enforced independently where needed?
  ```  

---

## Authentication State in Burp

- When comparing authenticated and unauthenticated requests, focus on what changes.

- Example:

### Before Login

```http id="r7w3mt"
GET /account HTTP/1.1
Host: example.com
```

### After Login

```http id="p4m8qn"
GET /account HTTP/1.1
Host: example.com
Cookie: session=abc123
```

- The session cookie may be the critical difference.

---

## Two-User Testing

- One of the strongest authorization-testing models uses two controlled accounts.

- Example:

  ```text id="v6n2kx"
  User A

  Owns Object A
  ```

  ```text id="q9m3wr"
  User B

  Owns Object B
  ```

- Then reason about:

  ```text id="n5p8mv"
  User A Session + Object A

  ↓

  Expected Allowed
  ```  

  ```text id="x7m4qt"
  User A Session + Object B

  ↓

  Expected Authorization Decision
  ```

- This separates identity from object ownership.

---

## Why Two Accounts Matter

- Without two controlled identities, it can be difficult to distinguish:

  ```text id="m3q9vr"
  Public Data

  Own Data

  Another User's Data

  Admin-Only Data
  ```

- Two-user testing provides a stronger baseline.

---

## Horizontal Authorization

- Horizontal authorization concerns users at similar privilege levels.

- Example:

  ```text id="k8n2pw"
  User A

  ↓

  Attempts to Access User B's Resource
  ```

- Potential examples:

  * Invoices.
  * Orders.
  * Profiles.
  * Messages.

---

## Vertical Authorization

- Vertical authorization concerns different privilege levels.

- Example:

  ```text id="r4m7qx"
  Normal User

  ↓

  Attempts Admin-Only Operation
  ```

- Potential examples:

  * User management.
  * Role changes.
  * Administrative reports.
  * System configuration.

---

## Context-Dependent Authorization

- Authorization may also depend on:

  * Resource ownership.
  * Workflow state.
  * Organization/tenant.
  * Time.
  * Account status.
  * Relationship between users.

- Do not reduce all authorization testing to:

  ```text id="t9p3mv"
  Admin vs User
  ```

- Real applications often have richer permission models.

---

## Multi-Factor Authentication

- A simplified MFA flow:

  ```text id="q6n8kr"
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

- Security questions include:

  ```text id="m5q2vn"
  When is the authenticated session created?

  Can protected resources be accessed before MFA completes?

  Can workflow steps be skipped?

  Is MFA state enforced server-side?
  ```  

- This connects authentication with multi-step state.

---

## Remember-Me Functionality

- Applications may offer persistent login mechanisms.

- Conceptually:

  ```text id="x8n4pk"
  Normal Session Ends

  ↓

  Persistent Credential Remains

  ↓

  Application Creates New Authenticated Session Later
  ```

- Persistent authentication credentials require strong protection because their lifetime may exceed ordinary sessions.

---

## Session Timeout

- Applications may implement:

  ```text id="p7m3qw"
  Idle Timeout

  Absolute Timeout
  ```

### Idle Timeout

```text id="v2n9mr"
No Activity for Defined Period

↓

Session Expires
```

### Absolute Timeout

```text id="k4m8qx"
Session Reaches Maximum Lifetime

↓

Expires Regardless of Activity
```

- Security requirements determine appropriate behavior.

---

## Concurrent Sessions

- Applications may allow:

  ```text id="r6q2nv"
  User Logged In on Laptop

  +

  User Logged In on Phone
  ```

- Questions may include:

  ```text id="n9m4pk"
  Are multiple sessions allowed?

  Can users view active sessions?

  Can sessions be revoked?

  What happens after password reset?
  ```  

- There is no universal requirement to prohibit concurrent sessions.

- Evaluate against application requirements.

---

## Session Testing Mental Model

- Think in lifecycle stages:

  ```text id="q3m7vx"
  Anonymous

  ↓

  Authentication Attempt

  ↓

  Authenticated

  ↓

  Privilege / State Changes

  ↓

  Logout / Expiration / Revocation
  ```

- At each transition, ask:

  ```text id="m8p3qw"
  What credential exists?

  Did it change?

  What does it authorize?

  When does it stop working?
  ```  

---

## Example Investigation

- Login response:

  ```http id="x5n9kr"
  HTTP/1.1 302 Found
  Location: /dashboard
  Set-Cookie: session=NEW123; Secure; HttpOnly; SameSite=Lax
  ```

- Analyze:
  
  ```text id="t7m2pv"
  New Session Identifier:  
  NEW123

  Secure:
  Present

  HttpOnly:
  Present

  SameSite:
  Lax  

  Redirect:
  Dashboard
  ```

- Then investigate:

  ```text id="q8m4nr"
  Was the session rotated from pre-login state?

  Does the session actually access authenticated resources?

  What happens after logout?

  What happens after password change?

  Are authorization checks enforced per resource/action?
  ```  

- This is much stronger than merely checking whether cookie flags exist.

---

## Cookie Flags Are Not the Whole Session Assessment

- A cookie may have:

  ```text id="p4n8mv"
  Secure

  HttpOnly

  SameSite
  ```  

and the application may still have:

  * Broken authorization.
  * Session fixation.
  * Weak logout.
  * Excessive session lifetime.

- Likewise, a missing attribute should be evaluated in context before assigning impact.

---

## Authentication Analysis Checklist

```text id="v9m3qx"
[ ] Identify authentication mechanism.

[ ] Identify session/token credential.

[ ] Compare anonymous vs authenticated requests.

[ ] Observe pre-login and post-login session identifiers.

[ ] Check session rotation at authentication.

[ ] Inspect relevant cookie attributes.

[ ] Understand cookie scope.

[ ] Test logout invalidation appropriately.

[ ] Understand password-change/reset session behavior.

[ ] Identify MFA state transitions if present.

[ ] Distinguish authentication from authorization.

[ ] Use controlled two-user testing where appropriate.

[ ] Test horizontal authorization.

[ ] Test vertical authorization.

[ ] Verify server-side behavior, not just UI restrictions.

[ ] Protect captured credentials and session data.
```

---

## Key Takeaways

* HTTP does not automatically maintain login state; applications build state on top of it.
* Cookies and sessions are related but different concepts.
* `Set-Cookie` travels server-to-client; `Cookie` travels client-to-server.
* Session identifiers often function as sensitive credentials.
* `Secure`, `HttpOnly`, `SameSite`, `Domain`, and `Path` control different aspects of cookie behavior.
* Authentication answers "Who are you?"
* Authorization answers "Are you allowed to do this?"
* Correct authentication does not imply correct authorization.
* Session rotation is important around authentication transitions.
* Logout should invalidate session authority appropriately, not merely alter browser state.
* Bearer tokens and JWTs can represent authenticated state and must be treated as secrets.
* Two-user testing provides a strong model for authorization analysis.
* Horizontal, vertical, and context-dependent authorization are distinct.
* Evaluate the complete session lifecycle: creation, use, rotation, expiration, revocation, and logout.
