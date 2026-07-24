# HTTP Headers

> **Module Progress**

```text id="r6w9mt"
█████░░░░░░░░ 5 / 13 Lessons
```

- HTTP headers carry metadata and instructions alongside requests and responses.

- They can influence:

  * Routing.
  * Authentication.
  * Session handling.
  * Body parsing.
  * Content negotiation.
  * Cross-origin behavior.
  * Caching.
  * Proxy behavior.
  * Browser security controls.

- For Burp users, the goal is not to memorize every possible header.

- The better approach is to ask:

  > **What role does this header play in the HTTP conversation?**

---

## Header Structure

- A simplified HTTP/1.1 request:

  ```http id="m4q8vn"
  GET /account HTTP/1.1
  Host: example.com
  User-Agent: Mozilla/5.0
  Accept: text/html
  Cookie: session=abc123
  ```

- Headers generally follow:

  ```text id="p7n3kx"  
  Header-Name: Header-Value
  ```

- Examples:

  ```text id="v5m9qw"
  Host: example.com

  Content-Type: application/json

  Authorization: Bearer TOKEN
  ```

---

## A Better Header Mental Model

- Classify important headers by purpose.

  ```text id="n8q2wr"
  Routing / Destination

  Identity / Authentication

  Session State

  Body Interpretation

  Content Negotiation

  Origin / Browser Context

  Proxy / Network Context

  Caching

  Security Policy

  Application-Specific Metadata
  ```

- This is more useful than memorizing a long alphabetical list.

---

## 1 — Routing and Destination Headers

- The most familiar example is:

  ```http id="k3p7mv"
  Host: example.com
  ```

- In HTTP/1.1, `Host` identifies the intended host.

- This matters because multiple applications may share infrastructure.

- Conceptually:

  ```text id="x6m2pk"
  One Server / Reverse Proxy

  ↓

  Host: shop.example.com

  Host: api.example.com

  Host: admin.example.com
  
  ↓

  Different Routing Decisions
  ```

---

## Why Host Matters

- Applications may use host information for:

  * Virtual hosting.
  * URL generation.
  * Routing.
  * Password-reset links.
  * Cache keys.
  * Security decisions.

- Do not assume the `Host` value is merely cosmetic.

---

## 2 — Identity and Authentication Headers

- A common example:

  ```http id="q9n4rv"
  Authorization: Bearer TOKEN
  ```

- Other authentication schemes can also use `Authorization`.

- Conceptually:

  ```text id="r4m8qn"
  Client Presents Credential

  ↓

  Server Validates Credential

  ↓

  Identity / Access Decision
  ```  

- When reading a request, ask:

  > What information tells the server who this client is?

---

## Authorization Header

- Example:

  ```http id="t7p2kx"
  Authorization: Bearer eyJ...
  ```

- The value may represent:

  * OAuth access token.
  * JWT.
  * Opaque API token.
  * Another bearer credential.

- Do not infer the exact token type only from appearance.

- Understand how the application actually uses it.

---

## Credentials Are Sensitive

- Captured authorization values may grant access to user accounts or APIs.

- Treat them as sensitive evidence.

- Do not:

  * Publish them.
  * Commit them to GitHub.
  * Share them unnecessarily.
  * Reuse them outside authorization.

---

## 3 — Cookie

- Example:

  ```http id="m5q9vw"
  Cookie: session=abc123; theme=dark
  ```

- Cookies may carry:

  ```text id="p3n7kr"
  Session Identifiers

  Preferences

  Tracking Values

  CSRF-Related Values

  Application State
  ```  

- A request can contain multiple cookies.

---

## Cookie vs Set-Cookie

- Do not confuse:

  ```http id="n6q2mv"
  Cookie: session=abc123
  ```

- with:

  ```http id="k9p4rx"
  Set-Cookie: session=abc123
  ```

- Conceptually:

  ```text id="v2m8qw"
  Cookie

  Client → Server
  ```

  ```text id="q5n3pk"
  Set-Cookie

  Server → Client
  ```

- The server issues or updates cookies with `Set-Cookie`.

- The browser later sends applicable cookies using `Cookie`.

---

## 4 — Body Interpretation Headers

- The most important example is:

  ```http id="r8m4nv"
  Content-Type: application/json
  ```

- It tells the recipient how to interpret the message body.

---

## Common Content Types

- Examples:

  ```text id="t3q7mx"
  application/json

  application/x-www-form-urlencoded

  multipart/form-data

  application/xml

  text/plain

  text/html
  ```
  
- The same conceptual data can be represented differently depending on content type.

---

## Example — Form Encoding

```http id="p6n2kw"
Content-Type: application/x-www-form-urlencoded

username=alice&role=user
```

---

## Example — JSON

```http id="m9q4vr"
Content-Type: application/json

{
  "username": "alice",
  "role": "user"
}
```

- Understanding the parser expected by the server is essential before modifying request bodies.

---

## Content-Length

- Example:

  ```http id="x5n8pt"
  Content-Length: 42
  ```

- This participates in determining message-body length in relevant HTTP contexts.

- Message framing matters because HTTP processors must agree on:

  ```text id="q2m7kn"
  Where One Message Ends

  and

  Where Another Begins
  ```  

- This becomes critical in advanced HTTP desynchronization topics.

---

## Transfer-Encoding

- In HTTP/1.1 contexts, you may encounter:

  ```http id="n4p9mw"
  Transfer-Encoding: chunked
  ```

- This represents another mechanism for framing transferred message content.

- For now, remember:

  ```text id="v7m3qx"
  Content-Length

  and

  Transfer-Encoding

  ↓

  Can Influence Message Framing
  ```  

- Do not modify framing-related headers casually without understanding their effects.

---

## 5 — Content Negotiation Headers

- Clients can describe which response representations they prefer.

- Examples include:

  ```http id="k5n8pr"
  Accept: application/json
  ```

  ```http id="r3q9mv"
  Accept-Language: en-US
  ```

  ```http id="p8m2kw"
  Accept-Encoding: gzip, deflate
  ```

- These may influence what the server returns.

---

## Accept

- Example:

  ```http id="q7m3nx"
  Accept: application/json
  ```

- Conceptually:

  ```text id="t9p4kv"
  Client

  ↓

  "I Prefer JSON"

  ↓

  Server
  ```

- The server may honor, ignore, or reject the preference depending on implementation.

---

## Accept-Encoding

- Example:

  ```http id="n5m8qw"
  Accept-Encoding: gzip, deflate
  ```

- This indicates content codings the client can handle.

- Burp often helps make compressed traffic readable during testing, but understand that compression may exist in the actual HTTP exchange.

---

## 6 — User-Agent

- Example:

  ```http id="m6q2vx"
  User-Agent: Mozilla/5.0 ...
  ```

- This identifies information about the client software.

- Applications may use it for:

  * Analytics.
  * Compatibility decisions.
  * Device-specific behavior.
  * Bot detection.
  * Logging.

---

## Do Not Trust User-Agent as Identity

- A client can generally control its `User-Agent`.

- Therefore:

  ```text id="t8p3mw"
  User-Agent Says "Mobile"

  ≠

  Proof Client Is a Mobile Device
  ```

- Likewise:

  ```text id="q8r4nt"
  User-Agent Says "TrustedApp"

  ≠

  Strong Authentication
  ```

- Security decisions should not rely blindly on easily controlled metadata.

---

## 7 — Origin

- Example:

  ```http id="v2m7qx"
  Origin: https://app.example.com
  ```

- The `Origin` header communicates the origin associated with certain requests.

- An origin conceptually consists of:

  ```text id="p4n8kr"
  Scheme

  +

  Host

  +
  
  Port
  ```  

- For example:

  ```text id="n3m9pw"
  https://example.com
  ```

- and:

  ```text id="m7v3qn"
  https://example.com:8443
  ```

are different origins because the ports differ.

---

## Why Origin Matters

- `Origin` is important in:

  * CORS.
  * Some CSRF defenses.
  * Cross-origin request handling.

- But remember:

  > A header appearing in a request does not automatically make it a trustworthy authorization mechanism.

- Context matters.

---

## 8 — Referer

- Example:

  ```http id="p4n8kr"
  Referer: https://example.com/account
  ```

- Despite the historical misspelling, `Referer` is the actual HTTP header name.

- It can indicate the page from which a request originated or was navigated.

- Applications may use it for:

  * Analytics.
  * Navigation context.
  * Some security checks.

---

## Origin vs Referer

- A simplified distinction:

  ```text id="n3m9pw"
  Origin

  ↓

  Origin-Level Context
  ```

  ```text id="m7v3qn"
  Referer

  ↓

  May Include More Specific Source URL Information
  ```

- Exact browser behavior depends on request type and referrer policy.

- Do not assume both headers are always present.

---

## 9 — Forwarding and Proxy Headers

- In applications behind proxies, you may encounter headers such as:

  ```http id="p4n8kr"
  X-Forwarded-For: 203.0.113.10
  ```

  ```http id="n3m9pw"
  X-Forwarded-Host: example.com
  ```

  ```http id="m7v3qn"
  X-Forwarded-Proto: https
  ```

- These can communicate information about the original request through intermediary infrastructure.

---

## Trust Boundary Problem

- Consider:

  ```text id="p4n8kr"
  Client

  ↓
  
  Reverse Proxy
  
  ↓

  Application
  ```

- The application may trust headers inserted by the reverse proxy.

- The security question becomes:

  > Can an untrusted client influence a header the application incorrectly assumes came from trusted infrastructure?

- This is a recurring web security pattern.

---

## X-Forwarded-For

- Example:

  ```http id="n3m9pw"
  X-Forwarded-For: 203.0.113.10
  ```

- It is commonly associated with original client IP information through proxies.

- But:

  ```text id="m7v3qn"
  Header Value Exists

  ≠
  
  Value Is Automatically Trustworthy
  ```  

- Trust depends on how the infrastructure constructs, sanitizes, and consumes it.

---

## 10 — Caching Headers

- Common response headers include:

  ```http id="p4n8kr"
  Cache-Control: no-store
  ```

  ```http id="n3m9pw"
  ETag: "abc123"
  ```

  ```http id="m7v3qn"
  Expires: Wed, 22 Jul 2026 10:00:00 GMT
  ```

- Caching affects whether responses may be stored and reused.

- This can have security implications for sensitive content.

---

## Cache-Control

- Example:

  ```http id="p4n8kr"
  Cache-Control: no-store
  ```

- Different directives influence caching behavior.

- For sensitive responses, caching policy can matter because data may otherwise be stored in locations where it should not persist.

---

## 11 — Browser Security Response Headers

- Servers can send headers that instruct browsers to apply security policies.

- Examples include:

  ```http id="n3m9pw"
  Content-Security-Policy: ...
  ```

  ```http id="m7v3qn"
  Strict-Transport-Security: ...
  ```

  ```http id="p4n8kr"
  X-Content-Type-Options: nosniff
  ```

  ```http id="n3m9pw"
  Referrer-Policy: ...
  ```

- These are response-side security controls.

- Their presence does not automatically prove the application is secure, and absence does not automatically prove an exploitable vulnerability.

---

## Content-Security-Policy

- CSP can restrict which resources a browser is allowed to load or execute.

- Conceptually:

  ```text id="m7v3qn"
  Server Sends Policy

  ↓

  Browser Enforces Policy

  ↓

  Certain Client-Side Behaviors Restricted
  ```

- CSP is primarily a browser-enforced defense layer.

---

## Strict-Transport-Security

- HSTS instructs compatible browsers to prefer HTTPS for a host according to the policy.

- Conceptually:

  ```text id="p4n8kr"
  Server Announces HSTS

  ↓

  Browser Remembers Policy
    
  ↓

  Future Connections Use HTTPS
  ```

- This is different from simply redirecting HTTP to HTTPS.

---

## X-Content-Type-Options

- A common value is:

  ```http id="n3m9pw"
  X-Content-Type-Options: nosniff
  ```

- This influences browser MIME-type handling.

- Again, classify it as:

  ```text id="m7v3qn"
  Browser Security Policy
  ```

rather than memorizing it without context.

---

## 12 — Application-Specific Headers

- Applications frequently define custom headers.

- Examples:

  ```http id="p4n8kr"
  X-API-Key: ...
  X-Tenant-ID: 42
  X-Client-Version: 8
  X-Device-ID: ...
  ```

- Do not assume a header beginning with `X-` is unimportant.

- Ask:

  ```text id="n3m9pw"
  Who sets it?

  Can the client modify it?

  Does the server trust it?

  Does changing it affect identity, tenant, routing, or authorization?
  ```

---

## Header Names Are Case-Insensitive

- HTTP field names are case-insensitive.

- Conceptually:

  ```text id="m7v3qn"
  Content-Type

  content-type

  CONTENT-TYPE
  ```

- refer to the same field name semantically.

- However, application frameworks and intermediary systems may introduce implementation quirks.

- Do not rely on superficial capitalization as a security boundary.

---

## Duplicate Headers

- A request can sometimes contain repeated header fields.

- Example:

  ```http id="p4n8kr"
  X-Test: one
  X-Test: two
  ```

- Different systems may:

  * Combine them.
  * Use the first.
  * Use the last.
  * Reject the request.
  * Process them differently.

- This becomes important when multiple HTTP components disagree about parsing.

---

## Hop-by-Hop vs End-to-End Concepts

- Some headers relate primarily to a specific transport connection between neighboring HTTP participants.

- Others describe information intended to reach the final recipient.

- Conceptually:

  ```text id="n3m9pw"
  Client

  ↓
  
  Proxy

  ↓  

  Server
  ```

- Not every header is necessarily forwarded unchanged through every intermediary.

- This matters when analyzing complex architectures.

---

## Do Not Change Ten Headers at Once

- Suppose a request contains:

  ```text id="m7v3qn"
  Cookie

  Authorization

  Origin

  Referer

  User-Agent

  X-Client-Version
  ```

- Changing all of them simultaneously creates poor evidence.

- Use:

  ```text id="p4n8kr"
  Baseline

  ↓

  Hypothesis

  ↓

  Change One Relevant Variable

  ↓
  
  Observe

  ↓

  Compare
  ```

- This makes cause and effect easier to establish.

---

## Header Security Classification

- When you encounter an unfamiliar header, classify it.

### Identity

```text id="n3m9pw"
Does it identify or authenticate a user/client?
```

### Routing

```text id="m7v3qn"
Does it influence which host/service handles the request?
```

### Parsing

```text id="p4n8kr"
Does it influence how the body/message is interpreted?
```

### Origin

```text id="n3m9pw"
Does it describe where the request came from?
```

### Client Metadata

```text id="m7v3qn"
Does it describe browser/device/client characteristics?
```

### Trust Context

```text id="p4n8kr"
Does the server appear to trust information inserted by infrastructure?
```

### Policy

```text id="n3m9pw"
Does it instruct browsers, caches, or intermediaries how to behave?
```

- This classification makes unfamiliar traffic easier to understand.

---

## Example Request Analysis

- Consider:

  ```http id="m7v3qn"
  POST /api/orders HTTP/1.1
  Host: shop.example.com
  Authorization: Bearer TOKEN
  Cookie: currency=INR
  Content-Type: application/json
  Origin: https://shop.example.com
  Referer: https://shop.example.com/checkout
  X-Client-Version: 8

  {
    "productId": 4821
  }
  ```

- Classify:

  ```text id="p4n8kr"
  Host
  → Routing / Destination

  Authorization
  → Identity / Authentication

  Cookie
  → State / Preference

  Content-Type
  → Body Interpretation

  Origin
  → Origin Context

  Referer
  → Navigation / Source Context

  X-Client-Version
  → Application-Specific Client Metadata
  ```

- Now ask which values may influence security decisions.

---

## Example Response Analysis

```http id="n3m9pw"
HTTP/1.1 200 OK
Content-Type: application/json
Set-Cookie: session=xyz; Secure; HttpOnly
Cache-Control: no-store
Content-Security-Policy: default-src 'self'

{"status":"ok"}
```

- Classify:

  ```text id="m7v3qn"
  Content-Type
  → Response Representation

  Set-Cookie
  → Client State / Session

  Cache-Control
  → Caching Policy

  Content-Security-Policy
  → Browser Security Policy
  ```  

- Classification makes the response easier to reason about.

---

## Header Analysis Checklist

- When reading headers:

  ```text id="p4n8kr"
  [ ] Identify routing/destination information.

  [ ] Identify authentication information.

  [ ] Identify session cookies.

  [ ] Identify body parsing/framing headers.

  [ ] Identify Origin and Referer context.

  [ ] Identify proxy/forwarding headers.

  [ ] Identify custom application headers.

  [ ] Identify caching behavior.

  [ ] Identify browser security policy headers.

  [ ] Determine which values are client-controlled.

  [ ] Determine which values the server appears to trust.

  [ ] Avoid changing multiple unrelated headers simultaneously.  
  ```

---

## Key Takeaways

* HTTP headers carry security-relevant metadata and instructions.
* Classify headers by function rather than memorizing them blindly.
* `Host` influences destination and routing context.
* `Authorization` and some cookies may carry authentication/session credentials.
* `Content-Type` controls body interpretation.
* Message framing headers require careful handling.
* `Origin` and `Referer` provide browser/request context but serve different purposes.
* Forwarding headers cross important infrastructure trust boundaries.
* Response headers can control caching and browser security behavior.
* Custom headers may be security-sensitive.
* Header names are case-insensitive.
* Duplicate headers can create parsing differences.
* A client-controlled header should not automatically be treated as trustworthy.
* Modify headers based on hypotheses, not randomly.
