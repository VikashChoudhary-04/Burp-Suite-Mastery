# State, Sessions, Cookies, and Authentication Through Burp

> **Module 04 — How Burp Suite Works**

> **Progress**

```text
█████████░░ 9 / 11 Lessons
```

- Web applications are often described as if each HTTP request exists independently.

- In reality, modern applications maintain **state** across many requests.

- That state may depend on:

  ```text
  Cookies

  Session IDs

  Authorization Headers

  Bearer Tokens

  CSRF Tokens

  Refresh Tokens

  Nonces

  Timestamps

  Signatures

  Server-Side Session Data
  ```

- Burp Suite sits between the client and server and allows you to observe and manipulate many of these values.

- However, one of the most important concepts in professional Burp usage is:

  ```text
  Browser State

  ≠
  
  Copied Request State

  ≠

  Server-Side Session State
  ```

- A request copied into Repeater may contain authentication data that was valid when captured but is no longer valid later.

- A browser may receive a new session cookie while an older Repeater tab still contains the previous cookie.

- A logout operation may invalidate a server-side session even though the old cookie remains visible inside Burp.

- Understanding these distinctions is essential for reliable authentication, authorization, session-management, and multi-user testing.

---

## Learning Objectives

- By the end of this lesson, you should be able to:

  * Explain what application state means.
  * Distinguish client-side and server-side state.
  * Understand how cookies maintain sessions.
  * Explain bearer-token authentication conceptually.
  * Understand CSRF tokens and their relationship to sessions.
  * Distinguish browser state from copied-request state.
  * Explain why Repeater requests become stale.
  * Understand session rotation and token refresh.
  * Explain logout and server-side invalidation.
  * Understand Burp's cookie-management concepts.
  * Understand session handling rules and macros conceptually.
  * Perform controlled multi-user authorization testing.
  * Avoid session contamination between test accounts.
  * Recognize common causes of authentication failures in Burp.
  * Build a systematic workflow for stateful testing.

---

## Why State Exists

- HTTP is fundamentally request-response based.

- A simplified interaction:

  ```text
  Request

  ↓

  Response
  ```

- Then another:

  ```text
  Request

  ↓

  Response
  ```  

- Applications still need to remember things such as:

  ```text
  Who is the user?

  Is the user authenticated?
  
  What permissions do they have?
  
  What step of a workflow are they on?

  What items are in their cart?

  Has a security challenge been completed?
  ```  

- Applications therefore maintain state across requests.

---

## Stateless Protocol, Stateful Application

- A useful mental model:

  ```text
  HTTP Messages

  can be individually independent

  while

  Application Behavior

  depends on previous events
  ```  
  
- Example:

  ```text
  Login

  ↓
  
  Session Created

  ↓

  Account Request
  
  ↓

  Server Recognizes Session
  
  ↓

  Authenticated Response
  ```

- Without the session state, the account request may fail.

---

## Three Layers of State

- Think about state in three major places:

  ```text
  1. Client-Side State

  2. Request-Copy State
  
  3. Server-Side State
  ```

- Understanding where a value exists prevents confusion.

---

### 1 — Client-Side State

- The browser may store:

  ```text
  Cookies

  Local Storage

  Session Storage

  Tokens

  Cached Application Data  
  ```

- For example:

  ```text
  Cookie:

  session=abc123
  ```

- The browser may automatically include that cookie in later requests.

---

### 2 — Request-Copy State

- Suppose Burp captures:

  ```http
  GET /account HTTP/1.1
  Host: example.com
  Cookie: session=abc123
  ```

- and you send it to Repeater.

- Repeater now contains a copy:

  ```text
  session=abc123
  ```

- That value does not necessarily stay synchronized with the browser.

---

## 3 — Server-Side State

- The server may store information associated with the session.

- Conceptually:

  ```text
  session=abc123

  ↓

  Server Session Store

  ↓  

  User: Alice

  Authenticated: Yes
  
  Role: User
  ```  

- The cookie may only be an identifier.

- The important authentication state may exist primarily on the server.

---

## Complete Session Mental Model

```text
Browser

Cookie: session=abc123

↓

Burp

↓

Server

↓

Session Store

abc123 → Alice → Authenticated
```

- The browser presents the identifier.

- The server decides what that identifier means.

---

## Cookies

- Cookies are commonly used to maintain application state.

- A server may respond:

  ```http
  HTTP/1.1 200 OK
  Set-Cookie: session=abc123; Secure; HttpOnly
  ```

- The browser stores:

  ```text
  session=abc123
  ```

- Later:

  ```http
  GET /account HTTP/1.1
  Host: example.com
  Cookie: session=abc123
  ```

- The server uses the cookie to identify the session.

---

## Set-Cookie vs Cookie

- These have different directions.

- Server to client:

  ```http
  Set-Cookie: session=abc123
  ```

- Client to server:

  ```http
  Cookie: session=abc123
  ```

- Mental model:

  ```text
  Server

  ↓

  Set-Cookie

  ↓

  Browser Stores Cookie
  
  ↓
  
  Future Request

  ↓
  
  Cookie Header
  
  ↓
  
  Server
  ```

---

## Cookie Attributes

- Common attributes include:

  ```text
  Secure

  HttpOnly

  SameSite
  
  Domain
  
  Path
  
  Expires

  Max-Age
  ```

- These influence how browsers store and send cookies.

---

## Secure

- Example:

  ```http
  Set-Cookie: session=abc123; Secure
  ```  

- The `Secure` attribute tells compatible browsers to send the cookie only over secure transport such as HTTPS.

---

## HttpOnly

- Example:

  ```http
  Set-Cookie: session=abc123; HttpOnly
  ```  

- `HttpOnly` restricts ordinary client-side JavaScript access to the cookie.

- It does not prevent Burp from seeing the cookie in HTTP traffic.

- Burp operates at the HTTP message level.

---

## SameSite

- Example:

  ```http
  Set-Cookie: session=abc123; SameSite=Lax
  ```

- `SameSite` influences whether browsers include cookies in certain cross-site request contexts.

- Common values include:

  ```text
  Strict

  Lax

  None
  ```  

- This is primarily browser behavior.

- A manually constructed Repeater request does not automatically reproduce every browser-side decision.

---

## Domain and Path

- A cookie may be restricted to:

  ```text
  Specific Domain

  Specific Path
  ```

- Example:

  ```http
  Set-Cookie: session=abc123; Domain=example.com; Path=/account
  ```

- The browser determines when the cookie should be sent.

- When manually editing requests in Burp, you can construct combinations the browser might not naturally send.

- That is useful for testing, but you must understand the difference.

---

## Browser Cookie Behavior vs Manual Requests

- Browser:

  ```text
  Cookie Rules

  ↓

  Browser Decides Whether to Send
  ```

- Repeater:

  ```text
  Request Contains Whatever Cookie Header Is Present

  ↓

  Burp Sends Request
  ```

- Therefore:

  ```text
  Browser Cookie Policy

  ≠

  Automatic Restriction on Every Manually Crafted Burp Request
  ```

- This distinction is important during session testing.

---

## Session Cookies

- A common authentication model:

  ```text
  Username + Password

  ↓

  Login Request

  ↓

  Server Validates Credentials

  ↓

  Server Creates Session

  ↓

  Set-Cookie: session=abc123
  
  ↓
  
  Browser Sends session=abc123
  
  ↓

  Authenticated Requests
  ```

- Burp lets you observe every step.

---

## Login Through Burp

- Example:

  ```http
  POST /login HTTP/1.1
  Host: example.com
  Content-Type: application/x-www-form-urlencoded

  username=alice&password=ExamplePassword
  ```

- Response:

  ```http
  HTTP/1.1 302 Found
  Set-Cookie: session=abc123; Secure; HttpOnly
  Location: /account
  ```

- Then:

  ```http
  GET /account HTTP/1.1
  Host: example.com
  Cookie: session=abc123
  ```

- This sequence reveals how authentication state is established.

---

## Authentication Is Often More Than One Request

- Modern login workflows may involve:

  ```text
  Initial Login

  ↓

  CSRF Token

  ↓

  Credential Submission

  ↓
  
  MFA Challenge
  
  ↓
  
  Redirect
  
  ↓

  Session Creation

  ↓
  
  Token Exchange
  
  ↓
  
  Authenticated Application
  ```

- Do not assume:

  ```text
  One POST /login

  =
  
  Complete Authentication Process
  ```

- Use Proxy history to understand the full workflow.

---

## Bearer Tokens

- Some applications use tokens instead of or alongside cookies.

- Example:

  ```http
  Authorization: Bearer eyJ...
  ```

- Conceptually:

  ```text
  Client

  ↓

  Authorization Token

  ↓  

  Server

  ↓

  Validate Token

  ↓

  Determine Identity / Permissions
  ```  

- Bearer tokens are common in APIs and modern applications.

---

## Cookie Authentication vs Bearer Authentication

- Cookie example:

  ```http
  Cookie: session=abc123
  ```

- Bearer example:

  ```http
  Authorization: Bearer eyJ...
  ```

- Both can represent authentication state, but they are transmitted differently.

- Always inspect the actual request.

---

## Multiple Authentication Mechanisms

- An application may use:

  ```text
  Session Cookie

  +

  Bearer Token

  +

  CSRF Token
  ```  

or different mechanisms for different endpoints.

- Example:

  ```text
  Web UI

  ↓
  
  Cookie Session
  ```

- while:

  ```text
  API

  ↓
  
  Bearer Token
  ```

- Do not assume one authentication mechanism applies everywhere.

---

## Access Tokens and Refresh Tokens

- Token-based applications may use:

  ```text
  Access Token

  Refresh Token
  ```

- Conceptually:
 
  ```text
  Access Token

  ↓
  
  Shorter-Lived API Access
  ```

- and:

  ```text
  Refresh Token

  ↓

  Obtain New Access Token
  ```

- A copied request may contain an expired access token while the browser has already refreshed it.

---

## Token Refresh Flow

- Example:

  ```text
  Access Token A

  ↓

  Expires
  
  ↓

  Browser Uses Refresh Mechanism

  ↓

  Access Token B Issued

  ↓

  Browser Uses Token B
  ```

- But Repeater may still contain:

  ```text
  Access Token A
  ```

- Result:

  ```text
  Browser Works

  Repeater Returns 401
  ```

- This may simply be stale authentication state.

---

## The Stale Request Problem

- This is one of the most common Burp issues.

- Timeline:

  ```text
  10:00

  Capture Request

  session=A
  ```

  ```text
  10:10

  Application Rotates Session
  
  session=B
  ```

  ```text
  10:20

  Send Old Repeater Request

  session=A
  ```  

- Possible result:

  ```text
  401 Unauthorized

  403 Forbidden

  Redirect to Login

  Session Expired
  ```  

- Do not immediately interpret this as application security behavior.

- First verify the request state.

---

## Browser State vs Repeater State

- Browser now:

  ```text
  Cookie: session=B
  ```

- Repeater still:

  ```text
  Cookie: session=A
  ```

- Therefore:

  ```text
  Browser Authenticated

  does not imply
  
  Old Repeater Request Authenticated
  ```

---

## How to Diagnose Stale Authentication

- Compare:

  ```text
  Latest Browser Request
  ```

- with:

  ```text
  Repeater Request
  ```

- Check:

  ```text
  Cookie

  Authorization

  CSRF Token

  Timestamp

  Nonce

  Signature

  Relevant Custom Headers
  ```

- Update only what is necessary.

---

## Session Rotation

- Applications may change session identifiers after important events.

- Examples:

  ```text
  Login

  Privilege Change

  Password Change

  MFA Completion

  Security Event
  ```

- Conceptually:

  ```text
  Old Session A

  ↓

  Authentication Event

  ↓

  New Session B
  ```  

- This can help defend against session fixation and related risks.

---

## Rotation Example

- Before login:

  ```text
  session=guest123
  ```

- After login:

  ```text
  session=user789
  ```

- If an old Repeater tab still uses:

  ```text
  session=guest123
  ```

it may no longer represent the authenticated user.

---

## Session Invalidation

- A server may invalidate a session.

- Example:

  ```text
  session=abc123

  ↓

  Logout

  ↓

  Server Marks Session Invalid
  ```

- The browser or Burp may still display the old value.

- But:

  ```text
  Possessing Old Cookie Value

  ≠
  
  Having Valid Server Session
  ```

---

## Logout Testing

- Suppose:

  ```text
  1. Login

  2. Capture Authenticated Request
  
  3. Copy Request to Repeater
  
  4. Logout in Browser

  5. Replay Old Request
  ```

- Possible secure behavior:

  ```text
  Old Session Rejected
  ```

- Potentially interesting behavior:

  ```text
  Old Session Still Fully Valid
  ```

- However, interpretation depends on the application's intended session model.

---

## Logout Does Not Delete Burp History

- After logout:

  ```text
  Browser Session

  may be invalidated
  ```

- but Burp may still contain:

  ```text
  Old Cookies

  Old Authorization Headers

  Old Requests

  Old Responses
  ```

in history and tool tabs.

- Treat them as sensitive data.

---

## CSRF Tokens

- A CSRF token is often used to help verify that a state-changing request originated from an expected application workflow.

- Example:

  ````http
  POST /profile/email HTTP/1.1
  Host: example.com
  Cookie: session=abc123
  Content-Type: application/x-www-form-urlencoded

  csrf=token789&email=alice@example.test
  ```

- The server may validate both:

  ```text
  Session

  +

  CSRF Token
  ```

---

## CSRF Tokens May Be Session-Bound

- Conceptually:

  ```text
  Session A

  ↓

  CSRF Token A
  ```

- and:

  ```text
  Session B

  ↓
  
  CSRF Token B
  ```

- Mixing:

  ```text
  Session B

  +

  CSRF Token A
  ```

may fail.

---

## CSRF Tokens May Rotate

- A token may change:

  ```text
  Per Session

  Per Login

  Per Request

  Per Form

  After Use
  ```

depending on the application.

- Therefore an old Repeater request may fail even when the session cookie is current.

---

## Dynamic Values

- Stateful requests may include values such as:

  ```text
  CSRF Token

  Nonce

  Timestamp

  One-Time Token

  Request Signature

  Workflow ID

  Transaction ID
  ```

- These values may expire or become invalid after use.

- A successful replay requires understanding which values are dynamic.

---

## Signed Requests

- Some APIs calculate signatures from request data.

- Conceptually:

  ```text
  Method

  +

  Path

  +

  Timestamp

  +

  Body

  +

  Secret / Key Material
  
  ↓
  
  Signature
  ```

- If you modify:

  ```text
  Body
  ```

- without updating:

  ```text
  Signature
  ```

the server may reject the request.

- This is not necessarily evidence that the tested parameter is protected by authorization.

- It may simply be signature validation.

---

## Time-Sensitive Requests

- A request may contain:

  ```text
  timestamp=...
  ```  

- The server may accept only a limited time window.

- Old Repeater request:

  ```text
  Valid Authentication

  +

  Expired Timestamp
  
  ↓

  Rejected
  ```

- Again:

  ```text
  Request Failure

  ≠

  Automatically Authentication Failure
  ```

- Identify the exact state dependency.

---

## Nonces

- A nonce is a value intended to be used once or uniquely within a context.

- Conceptually:

  ```text
  Request 1

  nonce=ABC

  ↓

  Accepted
  ```

- Replay:

  ```text
  Request 2

  nonce=ABC

  ↓

  Rejected
  ```  

if replay protection exists.

- This can make copied requests appear broken.

---

## Server-Side Workflow State

- Applications may require steps in order.

- Example:

  ```text
  Step 1

  Create Checkout
  ```

  ```text
  Step 2

  Select Shipping
  ```

  ```text
  Step 3

  Confirm Payment
  ```

- A request copied from Step 3 may fail later because the server-side workflow state no longer exists.

---

## Request State Is More Than Authentication

- When replaying requests, consider:

  ```text
  Authentication State

  Authorization State

  CSRF State

  Workflow State

  Token State

  Time State
  
  Server-Side Object State
  ```

- A request can fail because any one of these changed.

---

## Burp Cookie Management

- Burp contains mechanisms that can help track and use cookies during testing.

- Conceptually:

  ```text
  Response

  ↓

  Set-Cookie

  ↓

  Cookie Information Stored

  ↓

  Potentially Used by Burp Workflows
  ```  

- Exact behavior depends on the tool and configuration.

- Do not assume every tool tab automatically uses the newest cookie in every situation.

---

## The Safe Mental Model

- Instead of assuming synchronization:

  ```text
  Inspect the Exact Outgoing Request
  ```

- Verify:

  ```text
  Which Cookie?

  Which Token?
  
  Which User?

  Which Session?
  ```  

- The message being sent is the source of truth.

---

## Session Handling Rules

- Burp can automate actions before requests are sent.

- Conceptually:

  ```text
  Request

  ↓

  Session Handling Rule

  ↓

  Possible Update / Action

  ↓
  
  Final Request

  ↓

  Target
  ```

- Possible purposes include:

  ```text
  Update Cookies

  Refresh Authentication

  Run Login Workflow

  Add Dynamic Tokens

  Maintain Session
  ```  

- These are especially useful during automated testing.

---

## Why Session Handling Rules Exist

- Imagine Scanner or Intruder sends many requests.

- During testing:

  ```text
  Session Expires
  ```

- Without session handling:

  ```text
  Remaining Requests

  ↓

  401 / Login Page

  ↓
  
  Testing Quality Degrades
  ```

- With controlled session maintenance:

  ```text
  Session Detected as Invalid

  ↓

  Authentication Workflow

  ↓

  Fresh Session

  ↓

  Testing Continues
  ```
  
---

## Macros

- A macro is conceptually a predefined sequence of requests that Burp can execute as part of a workflow.

- Example:

  ```text
  GET /login

  ↓

  Extract CSRF Token

  ↓

  POST /login

  ↓

  Receive Session Cookie
  ```

- This sequence can support automated session handling.

---

## Macro Mental Model

```text
Need Fresh Session

↓

Run Macro

↓

Perform Required Requests

↓

Extract Dynamic State

↓

Update Session

↓

Continue Original Test
```

- Macros automate workflows that would otherwise require manual browser interaction.

---

## Macros Are Powerful but Context-Sensitive

- A login workflow may depend on:

  ```text
  MFA

  Captcha

  Device Binding

  JavaScript

  External Identity Provider

  One-Time Codes
  
  Anti-Automation Controls
  ```

- Not every authentication workflow can be reliably reproduced with a simple macro.

- Understand the application before automating it.

---

## Session Validation

- Before refreshing a session, Burp needs some way to determine whether the current session is valid.

- Conceptually:

  ```text
  Send Request

  ↓

  Inspect Response
  
  ↓

  Authenticated?
  
  Yes → Continue

  No → Refresh Session
  ```

- Possible indicators:

  ```text
  Status Code

  Redirect
  
  Response Text

  Known Logged-In Element

  Known Logged-Out Element
  ```

- Choose reliable indicators.

---

## Weak Session Validation

- Example:

  ```text
  If status = 200

  then session is valid
  ```

- This can be unreliable because login pages may also return:

  ```text
  200 OK
  ```

- A stronger indicator may be application-specific content.

---

## Authentication State Machines

- Think of authentication as states:

  ```text
  Unauthenticated

  ↓

  Credentials Accepted

  ↓

  MFA Pending

  ↓

  Authenticated

  ↓

  Session Expired

  ↓

  Unauthenticated
  ```  

- Some applications have additional states:

  ```text
  Password Reset Required

  Terms Acceptance Required

  Email Verification Pending
  
  Restricted Account

  Privileged Reauthentication Required
  ```

- Burp requests must respect or deliberately test these state transitions.

---

## Multi-User Testing

- Authorization testing often requires multiple accounts.

- Example:

  ```text
  User A

  owns

  /object/100
  ```

- and:

  ```text
  User B

  owns

  /object/200
  ```  

- A controlled test may ask whether User B can access User A's object.

---

## Two-User Mental Model

```text
Session A

↓

User A

↓

Object A
```

```text
Session B

↓

User B

↓

Object B
```

- Test:

  ```text
  Session B

  +

  Object A Identifier
  ```  

- Then observe the server's authorization decision.

---

## Change One Security Variable at a Time

- Suppose User A request:

  ```http
  GET /api/orders/100 HTTP/1.1
  Cookie: session=A
  ```

- To test authorization as User B:

  ```http
  GET /api/orders/100 HTTP/1.1
  Cookie: session=B
  ```

- Ideally preserve the object identifier while changing the authenticated identity.

- This isolates:

  ```text
  Authorization Decision
  ```

---

## Session Contamination

- A common testing mistake is accidentally mixing authentication material.

- Example:

  ```text
  Cookie from User A
  
  +

  Bearer Token from User B
  ```

- or:

  ```text
  Session B

  +

  CSRF Token A
  ```

- The resulting behavior may be meaningless or misleading.

---

## Label Your Sessions

- When testing multiple accounts, use clear labels:

  ```text
  USER_A

  USER_B

  ADMIN
  ```  

- Document:

  ```text
  Account

  Role

  Session Cookie
  
  Relevant Token

  Owned Object IDs
  ```

- Do not rely on memory.

---

## Multi-User Repeater Tabs

- A useful workflow:

  ```text
  Tab Group / Request A

  ↓

  User A Session
  ```

  ```text
  Tab Group / Request B

  ↓

  User B Session
  ```  

- Keep identities clearly separated.

- Before sending, verify:

  ```text
  Which user am I?

  Which object am I accessing?

  What result should be expected?
  ```  

---

## Authorization vs Authentication

- Authentication asks:

  ```text
  Who are you?
  ```

- Authorization asks:

  ```text
  Are you allowed to do this?
  ```

- Example:

  ```text
  Valid Session B

  ↓
  
  Authenticated as User B
  ```

- Then:

  ```text
  GET /users/A/private-data
  ```

- The key question becomes:

  ```text
  Is User B authorized to access User A's data?
  ```

---

## Removing Authentication

- A useful baseline test may compare:

  ```text
  Valid Authenticated Request
  ```

- with:

  ```text
  No Authentication
  ```

- Example:

  ```http
  GET /account HTTP/1.1
  Host: example.com
  ```

- versus:

  ```http
  GET /account HTTP/1.1
  Host: example.com
  Cookie: session=abc123
  ```
  
- Observe the difference.

---

## Do Not Confuse 401 and 403 Mechanically

- Common semantics:

  ```text
  401

  Authentication required or invalid
  ```

  ```text
  403

  Request understood but not permitted
  ```  

- However, applications do not always use these codes consistently.

- Always inspect:

  ```text
  Status

  Headers

  Body

  Redirects

  Actual Data Exposure
  ```  

---

## Redirect-to-Login Behavior

- An unauthenticated request may return:

  ```http
  HTTP/1.1 302 Found
  Location: /login
  ```

- If Repeater does not automatically follow the workflow the same way a browser does, you may see the raw redirect.

- This is useful evidence.

---

## Browser Automatically Hides Complexity

- A browser may automatically:

  ```text
  Store Cookies

  Follow Redirects

  Execute JavaScript

  Refresh Tokens

  Submit Background Requests
  ```  

- Repeater often exposes individual requests more directly.

- Therefore:

  ```text
  Browser Appears to "Just Work"

  while

  Repeater Requires State Awareness
  ```  

---

## JavaScript-Managed Authentication

- Some applications store tokens in:

  ```text
  Memory

  Local Storage

  Session Storage
  ```

- JavaScript then adds:

  ```http
  Authorization: Bearer ...
  ```

to API requests.

- Burp sees the resulting HTTP header even though the token did not originate from a cookie.

---

## Local Storage Is Not Automatically a Cookie

- If an application stores:

  ```text
  access_token
  ```

in local storage, the browser does not automatically send it as a cookie.

- Application JavaScript may explicitly read it and construct:

  ```http
  Authorization: Bearer ...
  ```

- Understand where authentication values originate.

---

## Token Rotation

- Some systems rotate tokens after use or after refresh.

- Timeline:

  ```text
  Token A

  ↓

  Request

  ↓

  Token B Issued

  ↓
  
  Future Requests Use B
  ```

- An old copied request with Token A may fail.

- Always compare with fresh traffic.

---

## Parallel Requests and State

- Modern applications may issue many requests simultaneously.

  ```text
  Browser

  ├── /profile
  ├── /notifications
  ├── /messages
  └── /refresh-token
  ```

- One request may update authentication state while others are in flight.

- This can make traffic appear inconsistent.

- Use timestamps and sequence carefully.

---

## Race Conditions and Session State

- Some security tests intentionally send concurrent requests.

- State may change between them.

- Example:

  ```text
  Request A

  ↓
  
  Uses Token X
  ```

- and simultaneously:

  ```text
  Request B

  ↓

  Uses Token X
  ```  

- The server's state transitions may affect the outcome.

- This is more advanced than ordinary replay and requires careful control.

---

## Logout Across Devices or Sessions

- Applications may implement logout differently.

- Possible models:

  ```text
  Invalidate Current Session Only
  ```

- or:

  ```text
  Invalidate All Sessions
  ```

- or:

  ```text
  Revoke Access Token but Leave Other Sessions
  ```

- Do not assume one universal behavior.

- Test only within authorized scope.

---

## Password Change Behavior

- After a password change, an application may:

  ```text
  Keep Existing Sessions Valid
  ```

- or:

  ```text
  Invalidate Current Session
  ```

- or:

  ```text
  Invalidate All Other Sessions
  ```

- Security requirements determine whether behavior is appropriate.

- Burp can help observe the exact session transitions.

---

## Privilege Changes

- Suppose:

  ```text
  User

  ↓

  Promoted to Admin
  ```

- Questions include:

  ```text
  Does existing session gain new privileges immediately?

  Is reauthentication required?

  Is a new token issued?

  Are old authorization claims cached?
  ```

- These are state-management questions.

---

## JWT-Like Tokens

- Some applications encode claims inside signed tokens.

- Conceptually:

  ```text
  Header

  Payload

  Signature
  ```

- The payload may contain:

  ```text
  User ID

  Role

  Expiration

  Issuer
  
  Audience
  ```

- Reading a token's payload does not mean you can safely modify it and have the server accept it.

- The server may validate cryptographic integrity and claims.

---

## Token Contents vs Server State

- A token may be:

  ```text
  Mostly Self-Contained
  ```

- or still depend on:

  ```text
  Server Revocation State

  User Status

  Session Database

  Key Rotation

  Authorization Database
  ```  

- Do not assume all token authentication is completely stateless.

---

## Authentication Caching

- Different infrastructure layers may cache:

  ```text
  Authorization Decisions

  Session Data

  Identity Information
  ```

- This can occasionally create short-lived inconsistencies after:

  ```text
  Logout

  Role Change

  Account Disablement
  ```

- Reproduce carefully before drawing conclusions.

---

## Common Failure — Browser Works, Repeater Gets 401

- Likely checks:

  ```text
  Stale Cookie?

  Expired Bearer Token?

  Missing Updated Authorization Header?

  Token Refresh Occurred?

  Different Host?
  
  Different Authentication Mechanism?
  ```  

- Compare against the latest browser request.

---

## Common Failure — Repeater Gets CSRF Error

- Check:

  ```text
  CSRF Token Stale?

  Token Bound to Different Session?

  Token Already Used?

  Required Header Missing?

  Workflow Step Missing?
  ```

- Do not change unrelated parameters until the baseline works.

---

## Common Failure — Request Worked Once, Then Fails

- Possible causes:

  ```text
  One-Time Token

  Nonce

  Transaction Already Completed

  CSRF Rotation

  Session Rotation

  Server-Side Workflow State
  ```

- Determine whether the endpoint is replayable.

---

## Common Failure — User B Still Appears as User A

- Check for mixed authentication:

  ```text
  Cookie A still present?

  Authorization Token A still present?

  Secondary Authentication Cookie?

  Cached Header?

  Session Handling Rule Replacing Cookie?

  Extension Modifying Request?
  ```  

- Changing one cookie may not change the entire authenticated identity.

---

## Common Failure — Logout but Old Request Still Works

- Possible explanations:

  ```text
  Logout Did Not Invalidate Server Session

  Different Session Token Still Present

  Endpoint Is Public

  Cached Response

  Multiple Authentication Mechanisms

  Logout Only Cleared Browser Cookie
  ```  

- Verify actual server behavior before reporting.

---

## Common Failure — Login Page Returns 200

- Do not conclude:

  ```text
  Session Valid
  ```

- based only on:

  ```text
  200 OK
  ```

- Inspect the content.

- The application may redirect or silently serve a login page.

---

## Common Failure — Automated Tool Loses Authentication

- Possible causes:

  ```text
  Session Expired During Scan

  CSRF Token Rotated

  Refresh Token Flow Missing

  Session Handling Rule Incorrect

  Macro Failed

  MFA Required
  
  Anti-Automation Triggered
  ```

- Automation requires state management.

---

## Systematic Stateful Testing Workflow

- Use this sequence.

  ```text
  1. Establish Identity

  2. Capture Fresh Baseline

  3. Identify Authentication Material

  4. Identify Dynamic State

  5. Reproduce Baseline

  6. Change One Variable
  
  7. Compare Response
  
  8. Refresh State When Needed

  9. Confirm Identity Before Conclusions

  10. Document Evidence
  ```  

---

### Step 1 — Establish Identity

- Know:

  ```text
  Which account?

  Which role?

  Which permissions?

  Which objects belong to this account?
  ```  

- Example:

  ```text
  User A

  Role: Standard User

  Owns Object: 100
  ```  

---

### Step 2 — Capture Fresh Baseline

- Capture a request that currently works.

- Verify:

  ```text
  Expected Status

  Expected Response

  Expected User Identity
  ```  

- Do not begin from a broken baseline.

---

## Step 3 — Identify Authentication Material

- Look for:

  ```text
  Session Cookie

  Authorization Header
  
  Bearer Token

  API Key

  Custom Auth Header
  ```

- Determine what actually authenticates the request.

---

### Step 4 — Identify Dynamic State

- Look for:

  ```text
  CSRF Token

  Nonce

  Timestamp

  Signature

  Workflow ID

  One-Time Token
  ```

- These may affect replayability.

---

### Step 5 — Reproduce Baseline

- Send the unmodified request from Repeater.

- Expected:

  ```text
  Original Behavior Reproduced
  ```

- If not:

  ```text
  Fix State First
  ```

- Do not test vulnerabilities on an invalid baseline.

---

### Step 6 — Change One Variable

- Examples:

  ```text
  User Identity

  Object ID

  Role-Related Parameter

  Authentication Header

  One Business Parameter
  ```

- Keep everything else stable where possible.

---

### Step 7 — Compare

- Inspect:

  ```text
  Status Code

  Response Length

  Body

  Headers

  Redirects

  Data Returned

  Server-Side Effect
  ```

- Do not rely on status code alone.

---

### Step 8 — Refresh State

- If authentication or dynamic values expire:

  ```text
  Capture Fresh Request

  or

  Use Controlled Session Handling
  ```

- Then repeat the test.

---

### Step 9 — Confirm Identity

- Before claiming authorization behavior, verify:

  ```text
  Who did the server treat this request as?
  ```

- This prevents false conclusions caused by session contamination.

---

### Step 10 — Document Evidence

- Record:

  ```text
  Account A

  Account B
  
  Original Request
  
  Modified Request
  
  Authentication Difference
  
  Object Ownership

  Observed Response
  
  Expected Behavior
  
  Actual Behavior
  ```

- Clear state documentation makes findings reproducible.

---

## Multi-User Authorization Example

- Assume authorized test accounts:

  ```text
  Alice

  Bob
  ```  

- Alice owns:

  ```text
  /api/documents/100
  ```  

- Bob owns:

  ```text
  /api/documents/200
  ```  

- Baseline Alice:

  ```http
  GET /api/documents/100 HTTP/1.1
  Host: example.test
  Cookie: session=ALICE
  ```

- Response:

  ```text
  200

  Alice's Document
  ```  

- Baseline Bob:

  ```http
  GET /api/documents/200 HTTP/1.1
  Host: example.test
  Cookie: session=BOB
  ```
  
- Response:

  ```text
  200

  Bob's Document
  ```

- Controlled authorization test:

  ```http
  GET /api/documents/100 HTTP/1.1
  Host: example.test
  Cookie: session=BOB
  ```

- Now ask:

  ```text
  Does the server enforce ownership?
  ```
  
- The key variable is:

  ```text
  Authenticated Identity
  ```

- while the target object remains:

  ```text
  Document 100
  ```

---

## Why Baselines Matter

- Without baselines, suppose Bob's request returns:

  ```text
  403
  ```

- You might think authorization is enforced.

- But perhaps:

  ```text
  Bob's Session Expired
  ```

- A fresh baseline confirms whether Bob is actually authenticated.

- Professional sequence:

  ```text
  Bob → Own Object → Works

  ↓

  Bob → Alice Object → Test
  ```

- This isolates authorization.

---

## Session Testing Checklist

```text
[ ] I know which account is authenticated.

[ ] I know the account's role.

[ ] I know which authentication mechanism the request uses.

[ ] I distinguish Set-Cookie from Cookie.

[ ] I understand browser cookie behavior.

[ ] I know manually crafted Burp requests can differ from natural browser behavior.

[ ] I distinguish browser state from Repeater state.

[ ] I understand server-side session state.

[ ] I know copied requests can become stale.

[ ] I compare old requests with fresh browser traffic.

[ ] I check cookies and Authorization headers before replay.

[ ] I check CSRF tokens and dynamic values.

[ ] I understand session rotation.

[ ] I understand token refresh conceptually.

[ ] I understand logout may invalidate server-side state.

[ ] I know old credentials may remain visible in Burp after logout.

[ ] I understand session handling rules conceptually.

[ ] I understand macros conceptually.

[ ] I verify session validity using reliable indicators.

[ ] I keep multiple test-user sessions clearly separated.

[ ] I establish a working baseline before authorization testing.

[ ] I change one security variable at a time.

[ ] I verify actual identity before drawing conclusions.

[ ] I treat authentication data in Burp projects as sensitive.
```

---

## Common Mistakes

- Avoid:

  ```text
  The browser is logged in, so every Repeater tab must also be logged in.
  ```

- Incorrect.

- Copied requests may contain stale authentication.

---

```text
The cookie still exists, so the server session must still be valid.
```

- Incorrect.

- The server may have invalidated it.

---

```text
Changing one session cookie always changes the authenticated user.
```

- Incorrect.

- Other tokens or authentication mechanisms may also be present.

---

```text
401 always proves the endpoint requires authentication.
```

- Too simplistic.

- The request may contain expired or malformed state.

---

```text
403 always proves authorization is correctly enforced.
```

- Incorrect.

- First verify the request was authenticated as the intended user.

---

```text
A 200 response proves the session is valid.
```

- Incorrect.

- A login page may also return 200.

---

```text
CSRF tokens are always global and reusable.
```

- Incorrect.

- They may be session-bound, request-bound, rotating, or one-time.

---

```text
Logging out removes sensitive authentication data from Burp.
```

- Incorrect.

- Burp may retain historical requests and copied values.

---

```text
Bearer tokens never depend on server-side state.
```

- Incorrect.

- Revocation and other server-side checks may still apply.

---

```text
Repeating the same request should always produce the same result.
```

- Incorrect.

- State, time, nonces, workflow progression, and server-side changes may alter behavior.

---

```text
Multi-user testing only requires changing the object ID.
```

- Incorrect.

- You must carefully control authenticated identity and object ownership.

---

## Professional Mental Model

- Memorize:

  ```text
  Browser State

  ↓

  Cookies / Tokens / Client Storage

  ↓

  HTTP Request

  ↓

  Burp

  ↓

  Copied Request State

  ↓

  Server

  ↓

  Server-Side Session / Authorization State
  ```  

- Then remember:

  ```text
  Browser State

  can change
  ```

- while:

  ```text
  Repeater Copy

  can remain unchanged
  ```  

- and:

  ```text
  Server State

  can invalidate both.
  ```  

- For reliable testing:

  ```text
  Fresh Baseline

  ↓

  Identify Authentication
  
  ↓

  Identify Dynamic State
  
  ↓

  Reproduce
  
  ↓

  Change One Variable
  
  ↓

  Compare

  ↓

  Confirm Identity
  
  ↓

  Conclude  
  ```

---

## Practical Exercise

- Use only authorized accounts and practice environments.

### Exercise 1 — Observe Session Creation

1. Log out of a practice application.
2. Clear or identify existing session state.
3. Log in through Burp.
4. Find the login response.
5. Identify any `Set-Cookie` or token issuance.
6. Find the next authenticated request.
7. Identify how authentication is transmitted.

- Document:

  ```text
  Login

  ↓

  Session / Token Issued

  ↓
  
  Authenticated Request
  ```

---

### Exercise 2 — Browser vs Repeater State

1. Capture an authenticated harmless GET request.
2. Send it to Repeater.
3. Confirm the baseline works.
4. Log out in the browser.
5. Send the old Repeater request again.
6. Compare the behavior.

- Determine:

  ```text
  Was the server-side session invalidated?
  ```

---

### Exercise 3 — Session Rotation

- If your practice application rotates sessions:

  1. Record the session identifier before login.
  2. Log in.
  3. Record the session identifier afterward.
  4. Compare them.

- Determine whether:

  ```text
  Session ID Rotated After Authentication
  ```

- Do not publish real session values.

---

### Exercise 4 — Stale Request Diagnosis

1. Capture a request.
2. Allow the session or token to change naturally.
3. Compare the old Repeater request with a fresh browser request.
4. Identify changed state fields.

- Examples:

  ```text
  Cookie

  Bearer Token

  CSRF Token

  Timestamp
  ```  

---

### Exercise 5 — Two-User Authorization Baseline

- Using two authorized practice accounts:

  ```text
  User A

  User B
  ```  

- Document:

  ```text
  User A → Own Resource → Expected Success

  User B → Own Resource → Expected Success
  ```

- Then perform only a permitted authorization test by changing one variable at a time.

- Record the exact authenticated identity for each request.

---

## Key Takeaways

* Web applications maintain state across HTTP requests using cookies, tokens, server-side sessions, workflow data, and other mechanisms.
* Browser state, copied-request state, and server-side session state are separate concepts.
* A request copied into Repeater does not necessarily stay synchronized with the browser.
* `Set-Cookie` travels from server to client, while `Cookie` travels from client to server.
* Browser cookie policies and manually constructed Burp requests behave differently.
* Authentication may use cookies, bearer tokens, or multiple mechanisms simultaneously.
* Access tokens may expire or rotate while the browser automatically refreshes them, leaving copied Burp requests stale.
* Session identifiers may rotate after authentication or privilege changes.
* Possessing an old cookie does not prove the associated server-side session remains valid.
* Logout testing must distinguish browser-side cookie removal from actual server-side invalidation.
* CSRF tokens may be tied to sessions, workflows, forms, or individual requests.
* Nonces, timestamps, signatures, and workflow state can make requests non-replayable.
* Burp session handling rules and macros can automate session maintenance, but they must accurately reproduce application behavior.
* Multi-user authorization testing requires strict separation of identities, sessions, tokens, and owned objects.
* A valid baseline should be established before changing security-relevant variables.
* Authentication asks who the user is; authorization asks what that authenticated user may do.
* Status codes alone are insufficient for determining authentication or authorization behavior.
* Before drawing conclusions, inspect the exact outgoing request and verify which identity the server processed.
* Authentication data retained in Burp history, projects, and tool tabs should be treated as sensitive.
