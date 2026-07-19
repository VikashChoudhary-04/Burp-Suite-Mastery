# HTTP Responses and Status Codes

> **Module Progress**

```text id="r7w3mt"
██████░░░░░░░ 6 / 13 Lessons
```

- An HTTP response is the server's reply to a request.

- When testing with Burp Suite, the response tells you what the application did—or at least what it chose to reveal—after processing your request.

- A professional tester does not look only at:

  ```text id="p4m8qn"
  200

  403

  500
  ```  

- Instead, analyze the complete response:

  ```text id="v6n2kx"
  Status

  +

  Headers

  +
  
  Body

  +

  Observable State Change

  +

  Application Context
  ```

- The status code is evidence.

- It is not the entire conclusion.

---

## Basic HTTP/1.1 Response Structure

- A simplified response:

  ```http id="q9m3wr"
  HTTP/1.1 200 OK
  Content-Type: application/json
  Content-Length: 37
  Cache-Control: no-store

  {
    "name": "Alice",
    "id": 105
  }
  ```

- Conceptually:

  ```text id="n5p8mv"
  Status Line

  Headers

  Blank Line

  Optional Response Body
  ```

---

## Status Line

- The first line:

  ```http id="x7m4qt"
  HTTP/1.1 200 OK
  ```

- contains:

  ```text id="m3q9vr"
  HTTP/1.1  
  │
  └── Protocol Version

  200  
  │
  └── Status Code

  OK
  │
  └── Reason Phrase
  ```

- The numeric status code is the most important standardized part of this line for interpretation.

---

## Status Code Categories

- HTTP status codes are grouped broadly by their first digit:

| Range | Category      |
| ----- | ------------- |
| `1xx` | Informational |
| `2xx` | Successful    |
| `3xx` | Redirection   |
| `4xx` | Client Error  |
| `5xx` | Server Error  |

- This gives you an initial classification—not a complete security conclusion.

---

## 1xx — Informational

- `1xx` responses indicate that processing or protocol communication is continuing in some way.

- Examples include:

  ```text id="k8n2pw"
  100 Continue

  101 Switching Protocols

  103 Early Hints
  ```

- You will encounter them less frequently during basic manual testing than `2xx–5xx`, but they are part of HTTP behavior.

---

## 100 Continue

- A client may use:

  ```http id="r4m7qx"
  Expect: 100-continue
  ```

before sending a potentially large body.

- The server may respond:

  ```http id="t9p3mv"  
  HTTP/1.1 100 Continue
  ```

before the final response.

- Conceptually:

  ```text id="q6n8kr"
  Client Sends Headers

  ↓

  Server Indicates Continue

  ↓

  Client Sends Body

  ↓

  Server Sends Final Response
  ```

---

## 101 Switching Protocols

- A `101` response can indicate agreement to switch protocols.

- A common context is a WebSocket upgrade.

- Conceptually:

  ```text id="m5q2vn"
  HTTP Handshake
  
  ↓

  101 Switching Protocols

  ↓

  Different Communication Protocol Continues
  ```

- This is why not every application interaction remains ordinary request-response HTTP forever.

---

## 2xx — Successful

- `2xx` generally indicates successful processing.

- Common examples:

  ```text id="x8n4pk"
  200 OK

  201 Created

  202 Accepted

  204 No Content
  ```  

- But remember:

  > "Successful HTTP processing" does not automatically mean "security control worked correctly."

---

## 200 OK

- A common successful response:

  ```http id="p7m3qw"
  HTTP/1.1 200 OK
  Content-Type: application/json

  {"status":"success"}
  ```  

- However, some applications also return `200` for application-level failures:

  ```http id="v2n9mr"
  HTTP/1.1 200 OK
  Content-Type: application/json

  {
    "error": "Invalid password"
  }
  ```

- Therefore:

  ```text id="k4m8qx"
  200

  ≠

  Business Action Definitely Succeeded
  ```

- Read the body and observe state.

---

## 201 Created

- `201 Created` generally indicates successful creation of a resource.

- Example:

  ```http id="r6q2nv"
  HTTP/1.1 201 Created
  Location: /api/users/106
  Content-Type: application/json

  {
    "id": 106
  }
  ```

- Potential evidence:

  ```text id="n9m4pk"
  A New Resource Was Created

  Resource Identifier May Be 106

  Location Header May Identify It
  ```

---

## 202 Accepted

- `202 Accepted` generally means the request has been accepted for processing, but processing may not yet be complete.

- Conceptually:

  ```text id="q3m7vx"
  Request Accepted

  ↓

  Background / Deferred Processing

  ↓
  
  Final Outcome May Occur Later
  ```

- This matters when testing asynchronous workflows.

- A `202` does not necessarily prove the requested operation has completed.

---

## 204 No Content

- `204 No Content` indicates successful processing with no response body.

- Example:

  ```http id="m8p3qw"
  HTTP/1.1 204 No Content
  ```

- Common contexts include:

  * Updates.
  * Deletions.
  * API actions.

- Do not interpret an empty body as lack of meaningful behavior.

- The state change may still have occurred.

---

## 3xx — Redirection

- `3xx` responses often instruct the client to take another action, commonly navigating to another location.

- Common examples:

  ```text id="x5n9kr"
  301 Moved Permanently

  302 Found

  303 See Other

  307 Temporary Redirect

  308 Permanent Redirect

  304 Not Modified
  ```

---

## Location Header

- Redirect responses commonly use:

  ```http id="t7m2pv"
  Location: /dashboard
  ```

- Example:

  ```http id="q8m4nr"
  HTTP/1.1 302 Found
  Location: /dashboard
  ```

- Conceptually:

  ```text id="p4n8mv"
  Client Requests Resource

  ↓
  
  Server Returns Redirect

  ↓

  Client Follows Location

  ↓

  New Request Generated
  ```

---

## Redirects Create Multiple Requests

- Suppose login produces:

  ```text id="v9m3qx"
  POST /login

  ↓

  302 Location: /dashboard

  ↓

  GET /dashboard
  ```

- If you inspect only the final dashboard request, you may miss:

  * Credential submission.
  * Session creation.
  * Authentication response.
  * Cookie issuance.

- Always understand the full sequence.

---

## 301 vs 302 vs 307 vs 308

- At a high level:

  ```text id="k6n2pr"
  301 / 308

  ↓

  Permanent Redirect Semantics
  ```

  ```text id="r3m8vw"
  302 / 307

  ↓

  Temporary Redirect Semantics
  ```

- A key distinction is how method/body preservation is defined.

- `307` and `308` explicitly preserve the request method and body when redirecting.

- Historical client behavior around `301` and `302` may convert some requests to `GET`.

- This can matter when analyzing request flows.

---

## 303 See Other

- `303` tells the client to retrieve another resource, typically using `GET`.

- A common pattern:

  ```text id="n7q4mx"
  POST Form

  ↓

  303 See Other

  ↓

  GET Result Page
  ```  

- This helps avoid accidental repeated form submission during navigation.

---

## 304 Not Modified

- `304 Not Modified` is related to caching.

- It tells the client that a cached representation may still be used.

- Conceptually:

  ```text id="m8q4vx"
  Client Has Cached Resource

  ↓

  Conditional Request

  ↓

  304 Not Modified

  ↓

  Client Reuses Cached Representation
  ```

- It is not the same as an ordinary redirect.

---

## 4xx — Client Error

- `4xx` responses indicate that the request could not be fulfilled as submitted, often because of authentication, authorization, syntax, resource, or rate-limit conditions.

- Common examples:

  ```text id="k5n9pr"
  400 Bad Request

  401 Unauthorized

  403 Forbidden

  404 Not Found

  405 Method Not Allowed

  409 Conflict

  415 Unsupported Media Type

  422 Unprocessable Content

  429 Too Many Requests
  ```  

---

## 400 Bad Request

- `400` generally indicates that the server considers the request invalid.

- Possible causes:

  * Malformed syntax.
  * Invalid parameter format.
  * Missing required data.
  * Request parsing failure.

- During testing, a `400` after modification may indicate:

  ```text id="r7w3mt"
  Your Change Broke Request Syntax
  ```

- rather than:

  ```text id="p4m8qn"
  Security Control Blocked the Attack
  ```

- Distinguish parsing failures from security decisions.

---

## 401 Unauthorized

- Despite the name, `401` generally means:

  > Authentication is required or the supplied authentication is invalid.

- Conceptually:

  ```text id="v6n2kx"
  Who Are You?

  ↓

  Not Authenticated Successfully

  ↓

  401
  ```

- A response may include:

  ```http id="q9m3wr"
  WWW-Authenticate: ...
  ```

- depending on the authentication scheme.

---

## 403 Forbidden

- `403` generally means the server understood the request but refuses to fulfill it.

- A useful conceptual distinction:

  ```text id="n5p8mv"
  401

  ↓

  Authentication Problem
  ```  

  ```text id="x7m4qt"
  403

  ↓

  Request Recognized but Access Refused
  ```

- Real applications may use these codes inconsistently.

- Always inspect actual behavior.

---

## 401 vs 403 Security Reasoning

- Suppose:

  ```text id="m3q9vr"
  No Session

  ↓

  401
  ```

- and:

  ```text id="k8n2pw"
  Valid Low-Privilege Session

  ↓

  403
  ```

- This may suggest:

  ```text id="r4m7qx"
  Authentication Works

  ↓

  Authorization Denies the Authenticated User
  ```

- That is a useful hypothesis.

- It is not automatically proof of correct access control.

---

## 404 Not Found

- `404` means the requested resource was not found—or at least that the server chose to respond that way.

- Security-sensitive applications sometimes intentionally return `404` instead of `403` to avoid revealing whether a protected resource exists.

- Therefore:

  ```text id="t9p3mv"
  404

  ≠

  Absolute Proof Resource Does Not Exist
  ```

- Context matters.

---

## 405 Method Not Allowed

- Example:

  ```http id="q6n8kr"
  HTTP/1.1 405 Method Not Allowed
  Allow: GET, POST
  ```

- This indicates the request method is not supported for the target resource according to the server's response.

- Useful evidence, but still inspect actual behavior and architecture.

---

## 409 Conflict

- `409 Conflict` may indicate the request conflicts with current resource state.

- Examples:

  * Duplicate resource.
  * Version conflict.
  * Invalid state transition.

- Conceptually:

  ```text id="m5q2vn"
  Request Is Structurally Valid

  ↓

  But Conflicts With Current State
  ```

- This can provide useful clues about business logic.

---

## 415 Unsupported Media Type

- Example:

  ```http id="x8n4pk"
  HTTP/1.1 415 Unsupported Media Type
  ```

- This may occur when the server does not support the supplied body format.

- For example:

  ```text id="p7m3qw"
  Expected:
  application/json

  Received:
  text/plain
  ```

- This can help identify parser expectations.

---

## 422 Unprocessable Content

- `422` generally indicates the server understood the content type and syntax but could not process the supplied instructions or values.

- Conceptually:

  ```text id="v2n9mr"
  Syntax Understood

  ↓

  Semantic / Validation Problem
  ```  

- Frameworks differ in how they use `400` and `422`.

---

## 429 Too Many Requests

- Example:

  ```http id="k4m8qx"
  HTTP/1.1 429 Too Many Requests
  Retry-After: 60
  ```

- This generally indicates rate limiting.

- Possible contexts:

  * Login attempts.
  * OTP requests.
  * API requests.
  * Password resets.

---

## Rate-Limit Interpretation

- Do not conclude a rate limit is effective from one `429`.

- Ask:

  ```text id="r6q2nv"
  What Is Being Limited?

  Per Account?

  Per Session?

  Per IP?

  Per Endpoint?

  For How Long?

  Does the Restricted Action Actually Stop?
  ```

- Testing must remain within authorization and engagement limits.

---

## 5xx — Server Error

- `5xx` responses indicate that the server encountered a problem while handling the request.

- Common examples:

  ```text id="n9m4pk"
  500 Internal Server Error

  502 Bad Gateway

  503 Service Unavailable

  504 Gateway Timeout
  ```

---

## 500 Internal Server Error

- A `500` may indicate:

  * Unhandled application exception.
  * Backend failure.
  * Unexpected input.
  * Programming error.

- A tester should not immediately conclude:

  ```text id="q3m7vx"
  500

  =

  Vulnerability
  ```

- An error is evidence of unexpected behavior.

- Impact still needs investigation.

---

## Error Messages Can Leak Information

- A poorly handled `500` response might expose:

  * Stack traces.
  * File paths.
  * Framework versions.
  * Database errors.
  * Internal service names.

- Example concept:

  ```text id="m8p3qw"
  Unexpected Input

  ↓

  Application Exception

  ↓

  Verbose Error Response

  ↓

  Internal Information Disclosure
  ```

- Preserve relevant evidence carefully.

---

## 502 Bad Gateway

- `502` commonly indicates an intermediary such as a gateway or reverse proxy received an invalid response from an upstream service.

- Architecture may look like:

  ```text id="x5n9kr"
  Client

  ↓

  Gateway / Reverse Proxy

  ↓

  Backend
  ```

- A `502` can provide clues that multiple infrastructure layers are involved.

---

## 503 Service Unavailable

- `503` may indicate:

  * Temporary overload.
  * Maintenance.
  * Backend unavailable.
  * Service intentionally unavailable.

- Do not repeatedly hammer a struggling service.

- Testing must remain controlled.

---

## 504 Gateway Timeout

- `504` generally means a gateway or proxy did not receive a timely response from an upstream system.

- Conceptually:

  ```text id="t7m2pv"
  Client

  ↓
  
  Gateway

  ↓

  Backend Takes Too Long

  ↓

  504
  ```

- This may help identify backend dependencies or timing behavior.

---

## Status Code Does Not Equal Security Outcome

- This is one of the most important lessons.

- Consider:

  ```http id="q8m4nr"
  HTTP/1.1 403 Forbidden
  Content-Type: application/json

  {
    "error": "Access denied",
    "email": "victim@example.com",
    "accountId": 105
  }
  ```

- The status says:

  ```text id="p4n8mv"
  Forbidden
  ```

- but the body may still disclose information.

- Therefore:

  ```text id="v9m3qx"
  403

  ≠

  No Security Impact
  ```

---

## Another Example — 200 With Failure

```http id="k6n2pr"
HTTP/1.1 200 OK
Content-Type: application/json

{
  "success": false,
  "message": "Permission denied"
}
```

- The HTTP exchange succeeded.

- The application action failed.

- Therefore:

  ```text id="r3m8vw"
  200

  ≠
  
  Business Action Succeeded
  ```

---

## Another Example — Redirect After Success

- Suppose:

  ```text id="n7q4mx"
  POST /login

  ↓

  302 /dashboard
  ```  

- This may indicate successful authentication.

- But verify:

  * Was a session cookie issued?
  * Does `/dashboard` actually load authenticated content?
  * Did server-side state change?

- Do not infer too much from the redirect alone.

---

## Response Headers Matter

- Do not inspect only status and body.

- Response headers may reveal:

  ```text id="m8q4vx"
  Set-Cookie

  Location

  Content-Type

  Cache-Control

  WWW-Authenticate

  CORS Headers
  
  Security Policies

  Server / Infrastructure Clues
  ```

- Each may change the interpretation of the response.

---

## Set-Cookie

- Example:

  ```http id="k5n9pr"
  Set-Cookie: session=xyz123; Secure; HttpOnly; SameSite=Lax
  ```

- This may indicate:

  * New session creation.
  * Session rotation.
  * Updated application state.

- During login testing, compare cookies before and after authentication.

---

## Location

- Example:

  ```http id="r7w3mt"
  Location: /dashboard
  ```

- This tells the client where to navigate for a redirect response.

- Inspect whether the destination is:

  * Relative.
  * Absolute.
  * User-controlled.
  * Security-sensitive.

---

## Content-Type

- Example:

  ```http id="p4m8qn"
  Content-Type: application/json
  ```

- This helps interpret the body.

- A response may contain:

  ```text id="v6n2kx"
  HTML

  JSON
  
  XML

  JavaScript

  File Data
  ```

- Do not analyze the body without understanding its representation.

---

## Response Body

- The body may contain:

  * Requested data.
  * Error messages.
  * Validation details.
  * Object identifiers.
  * Application state.
  * Debug information.

- A small response can be highly significant.

---

## Compare Responses Systematically

- Suppose you modify one request parameter.

- Compare:

  ```text id="q9m3wr"
  Status Code

  Response Length

  Headers

  Body Content

  Redirect Location

  Cookies

  Timing
  
  Observable Application State
  ```

- Do not compare only:

  ```text id="n5p8mv"
  200 vs 403
  ```

- Differences elsewhere may be more meaningful.

---

## Response Length as a Clue

- Imagine:

  ```text id="x7m4qt"
  Baseline:

  403
  Length: 120
  ```

- Modified request:

  ```text id="m3q9vr"
  403
  Length: 8,420
  ```

- The status code stayed the same.

- The response changed dramatically.

- That deserves investigation.

---

## Similar Status, Different Meaning

- Two responses:

  ```text id="k8n2pw"
  200
  Length: 1,200
  ```

- and:

  ```text id="r4m7qx"
  200
  Length: 9,500
  ```

may represent completely different outcomes.

- Likewise, identical lengths do not guarantee identical content.

- Use multiple comparison signals.

---

## Observable State Is Strong Evidence

- Suppose a request to change an email address returns:

  ```text id="t9p3mv"
  HTTP/1.1 500 Internal Server Error
  ```

- But refreshing the account page shows the email was actually changed.

- Then:

  ```text id="q6n8kr"
  500

  ≠

  Operation Failed
  ```

- Always verify important state-changing operations independently.

---

## Baseline Response

- Before modifying a request, establish the normal response.

- Record:

  ```text id="m5q2vn"
  Status:

  Length:

  Important Headers:

  Body Pattern:

  Cookies:

  Redirect:

  Timing:

  Expected State:
  ```

- Then change one variable and compare.

---

## Response Analysis Workflow

- Use:

  ```text id="x8n4pk"
  1. Read Status Code

  ↓

  2. Read Important Headers

  ↓

  3. Understand Content-Type
  
  ↓
  
  4. Read Body

  ↓

  5. Identify Redirects / Cookies

  ↓

  6. Compare With Baseline

  ↓

  7. Verify Observable State
  
  ↓
  
  8. Form Conclusion
  ```  

---

## Common Mistakes

- Avoid:

  ```text id="p7m3qw"
  200 = Success
  ```

  ```text id="v2n9mr"
  403 = Secure
  ```

  ```text id="k4m8qx"
  404 = Resource Definitely Does Not Exist
  ```

  ```text id="r6q2nv"
  500 = Vulnerability
  ```

  ```text id="n9m4pk"
  Empty Body = Nothing Happened
  ```

  ```text id="q3m7vx"
  Same Status = Same Response
  ```

- Status codes provide context.

- They do not replace investigation.

---

## Response Analysis Checklist

- When analyzing a response:

  ```text id="m8p3qw"
  [ ] Identify the status code.

  [ ] Understand its general category.
  
  [ ] Inspect important response headers.

  [ ] Identify Content-Type.

  [ ] Inspect the complete body.

  [ ] Check for Set-Cookie.

  [ ] Check for redirects and Location.

  [ ] Compare response length/content with baseline.

  [ ] Look for information leakage.

  [ ] Determine whether application state changed.

  [ ] Do not treat status code alone as the security conclusion.
  ```  

---

## Key Takeaways

* HTTP responses contain a status line, headers, and optionally a body.
* `1xx` provides informational/protocol-level responses.
* `2xx` indicates successful HTTP-level processing.
* `3xx` often represents redirects or caching behavior.
* `4xx` represents request-side errors or denied conditions.
* `5xx` represents server-side failures.
* `401` generally concerns authentication; `403` generally concerns refusal after the request is understood.
* `404` does not always prove a resource does not exist.
* `429` commonly indicates rate limiting.
* `500` is evidence of an error, not automatic proof of vulnerability.
* Redirects create additional HTTP requests that must be understood as a sequence.
* Response headers can be as important as response bodies.
* A status code does not prove whether a security control succeeded or failed.
* Compare status, headers, body, length, timing, cookies, redirects, and actual state.
