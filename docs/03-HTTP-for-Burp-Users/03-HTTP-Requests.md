# HTTP Requests

> **Module Progress**

```text id="r7w3mt"
███░░░░░░░░░ 3 / 13 Lessons
```

- An HTTP request is a message sent by a client to communicate an intended action to a server.

- When using Burp Suite, you will spend a large amount of time reading, modifying, replaying, and comparing requests.

- The goal is not merely to recognize their syntax.

- You should be able to look at an unfamiliar request and determine:

  * What action is being requested.
  * Which resource is involved.
  * What identifies the session.
  * Where user-controlled input exists.
  * How the body is formatted.
  * Which values may influence security decisions.

---

## Basic HTTP/1.1 Request Structure

- A simplified request looks like:

  ```http id="m5q8vn"
  POST /api/profile HTTP/1.1
  Host: example.com
  Cookie: session=abc123
  Content-Type: application/json
  Content-Length: 24

  {"displayName":"Alice"}
  ```

- Conceptually:
  
  ```text id="p8n3kx"
  Request Line

  Headers

  Blank Line
  
  Optional Message Body
  ```  

- Each section has a distinct purpose.

---

## The Request Line

- The first line:

  ```http id="v4m9qw"
  POST /api/profile HTTP/1.1
  ```

- contains three important components:
  
  ```text id="n7q2wr"
  POST
  │
  └── Method

  /api/profile
  │
  └── Request Target

  HTTP/1.1
  │
  └── HTTP Version
  ```

- Before reading anything else, this line gives you a high-level description of the requested operation.

---

## Read the Request Line First

- Ask:

  ```text id="k3p8mv"
  What method is being used?

  What resource is being targeted?

  Does the request target contain parameters?

  Which HTTP version is represented?
  ```  

- This creates context for everything that follows.

---

## Request Headers

- Headers provide metadata and instructions related to the request.

- Example:

  ```http id="x6m4pk"
  Host: example.com
  User-Agent: Mozilla/5.0
  Accept: application/json
  Cookie: session=abc123
  Content-Type: application/json
  Content-Length: 24
  ```

- Each header follows the conceptual structure:

  ```text id="q9n2rv"
  Header-Name: Header-Value
  ```

- Different headers influence different parts of HTTP processing.

---

## Not All Headers Are Equally Important

- Some headers may describe:

  * Destination.
  * Client preferences.
  * Authentication.
  * Session state.
  * Request origin.
  * Body format.
  * Message framing.
  * Caching behavior.

- Do not treat the header section as unimportant metadata.

- Headers can directly influence security behavior.

---

## Host

- Example:

  ```http id="r4m8qn"
  Host: example.com
  ```

- In HTTP/1.1, the `Host` header identifies the intended host for the request.

- This is important because a single server or infrastructure layer may serve multiple applications.

- Conceptually:

  ```text id="t7p2kx"
  Same IP Address

  ↓

  Host: app.example.com
  Host: admin.example.com
  Host: api.example.com

  ↓

  Potentially Different Applications
  ```  

---

## Cookie

- Example:

  ```http id="m5q9vw"
  Cookie: session=abc123
  ```

- Cookies often carry:

  * Session identifiers.
  * Preferences.
  * Tracking values.
  * Application state.

- A session cookie may help the server determine which authenticated user is making the request.

- Never assume every cookie is security-sensitive, but always understand what important cookies appear to represent.

---

## Authorization

- Another common authentication-related header is:

  ```http id="p3n7kr"
  Authorization: Bearer eyJ...
  ```

- Applications may use authorization headers instead of or alongside cookies.

- When reading a request, ask:

  > What tells the server who this client is?

- The answer may be:

  * Cookie.
  * Authorization header.
  * Client certificate.
  * Another application-specific mechanism.

---

## Content-Type

- Example:

  ```http id="n6q2mv"
  Content-Type: application/json
  ```

- This tells the recipient how the request body is represented.

- Other examples include:

  ```text id="k9p4rx"
  application/x-www-form-urlencoded

  multipart/form-data

  text/plain

  application/xml
  ```

- Understanding `Content-Type` helps you correctly interpret the body.

---

## Content-Length

- Example:

  ```http id="v2m8qw"
  Content-Length: 24
  ```

- This indicates the length of the message body in bytes for message framing in relevant HTTP contexts.

- Do not think of it as merely decorative.

- HTTP systems need to determine:

  ```text id="q5n3pk"
  Where Does This Message End?
  ```

- Message framing becomes especially important in advanced HTTP security topics.

---

## The Blank Line

- In an HTTP/1.x textual representation, a blank line separates the header section from the message body.

- Conceptually:

  ```text id="r8m4nv"
  Headers
  Header
  Header

  <blank line>

  Body
  ```

- Without understanding this boundary, raw HTTP messages can be confusing.

---

## The Request Body

- A request may contain a body.

- Example:

  ```http id="t3q7mx"
  POST /login HTTP/1.1
  Host: example.com
  Content-Type: application/x-www-form-urlencoded

  username=alice&password=example
  ```

- The body contains data supplied with the request.

---

## Requests Without Bodies

- Not every request has a body.

- Example:

  ```http id="p6n2kw"
  GET /products?page=2 HTTP/1.1
  Host: example.com
  Cookie: session=abc123
  ```

- Here, input appears in the query string rather than a body.

---

## Method Does Not Fully Define Data Location

- A common beginner assumption is:

  ```text id="m9q4vr"
  GET

  =

  Parameters Only in URL
  ```

- and:

  ```text id="x5n8pt"
  POST

  =
  
  Parameters Only in Body
  ```

- Do not rely on this model.

- Applications can place meaningful input in many locations regardless of the broad method semantics.

- Always inspect the actual request.

---

## Example — Form Request

```http id="q2m7kn"
POST /profile/update HTTP/1.1
Host: example.com
Cookie: session=abc123
Content-Type: application/x-www-form-urlencoded

displayName=Alice&email=alice%40example.com
```

- Potential inputs:

  ```text id="n4p9mw"
  displayName

  email
  ```

- Session context:

  ```text id="v7m3qx"
  session=abc123
  ```

---

## Example — JSON API Request

```http id="k5n8pr"
PATCH /api/users/105 HTTP/1.1
Host: api.example.com
Authorization: Bearer TOKEN
Content-Type: application/json

{
  "displayName": "Alice",
  "timezone": "UTC"
}
```

- Immediately identify:

  ```text id="r3q9mv"
  Method:
  PATCH

  Resource:
  /api/users/105

  Possible Object Identifier:
  105

  Authentication:
  Bearer TOKEN

  Body Format:
  JSON

  Input:
  displayName
  timezone
  ```

- This is the beginning of structured request analysis.

---

## Example — Multiple Input Locations

- Consider:

  ```http id="p8m2kw"
  POST /api/orders/4821?notify=true HTTP/1.1
  Host: shop.example.com
  Authorization: Bearer TOKEN
  Cookie: currency=INR
  X-Client-Version: 4
  Content-Type: application/json

  {
    "shippingMethod": "express"  
  }
  ```

- Potentially meaningful values exist in:

  ```text id="q7m3nx"
  Path:
  4821

  Query:
  notify=true

  Authorization Header:
  TOKEN

  Cookie:
  currency=INR

  Custom Header:
  X-Client-Version: 4

  Body:
  shippingMethod=express
  ```

- Do not focus only on the body.

---

## Custom Headers

- Applications often use non-standard headers.

- Examples:

  ```http id="t9p4kv"
  X-API-Key: ...
  X-User-ID: ...
  X-Client-Version: ...
  X-Requested-With: ...
  ```

- A custom header may be:

  * Informational.
  * Security-sensitive.
  * Trusted incorrectly.
  * Used for routing.
  * Ignored entirely.

- Its presence is a clue.

- Its behavior must be tested.

---

## Browser-Generated vs Application-Generated Headers

- Some headers are added automatically by the browser or HTTP client.

- Others may be added by application JavaScript or client logic.

- When analyzing a request, ask:

  ```text id="n5m8qw"
  Did the browser add this?

  Did application code add this?

  Did a proxy add this?

  Does the server actually use it?
  ```  

- This becomes useful when deciding which values are meaningful.

---

## User-Controlled Does Not Mean Vulnerable

- Suppose you can modify:

  ```http id="m6q2vx"
  User-Agent: Test
  ```

- That proves only that the client can send a different value.

- It does not prove a vulnerability.

- Use:

  ```text id="t8p3mw"
  Controllable Input

  ↓

  Server Processing

  ↓

  Security-Relevant Behavior?

  ↓

  Evidence
  ```

- Control is only the beginning of the investigation.

---

## Client-Side Restrictions Are Not Request Restrictions

- A form may allow only certain values.

- For example:

  ```text id="q8r4nt"
  Dropdown:

  Basic
  Premium
  ```

- But the underlying request may contain:

  ```http id="v2m7qx"
  plan=basic
  ```

- Burp allows the tester to ask:

  > What happens if the client sends a value the UI never offers?

- The server—not the interface—must decide whether the value is valid and authorized.

---

## Hidden Fields Are Still Client Input

- A form may contain:

  ```html id="p4n8kr"
  <input type="hidden" name="price" value="1000">
  ```

- "Hidden" means hidden from normal visual presentation.

- It does not mean:

  ```text id="n3m9pw"
  Trusted

  Secure

  Server-Controlled
  ```

- If the browser sends it, a client can potentially modify it.

- The server should validate security-sensitive values independently.

---

## Read Requests Top to Bottom

- A useful first-pass workflow is:

  ```text id="m7v3qn"
  1. Request Line

  ↓

  2. Destination / Host

  ↓

  3. Authentication / Session

  ↓

  4. Content Type

  ↓

  5. Important Headers

  ↓

  6. Cookies

  ↓

  7. Query Parameters

  ↓

  8. Body Parameters

  ↓

  9. Dynamic Identifiers

  ↓

  10. Security Assumptions
  ```  

- This prevents tunnel vision.

---

## Then Read by Security Meaning

- After understanding the syntax, ask:

  ```text id="p4n8kr"
  Identity

  ↓

  What identifies the user?
  ```

  ```text id="n3m9pw"
  Object

  ↓

  What resource is being accessed?
  ```

  ```text id="m7v3qn"
  Action

  ↓

  What operation is requested?
  ```

  ```text id="p4n8kr"
  Input

  ↓

  What values can influence behavior?
  ```

  ```text id="n3m9pw"
  Trust

  ↓

  Which client-supplied values might the server trust?
  ```

- This converts raw HTTP into an investigation model.

---

## Do Not Delete Headers Randomly

- A common learning habit is removing headers just to see what happens.

- Experiments are useful, but first understand the baseline.

- Some headers may affect:

  * Authentication.
  * CSRF protections.
  * Routing.
  * Body parsing.
  * Caching.
  * Content negotiation.

- Prefer:

  ```text id="m7v3qn"
  Understand Header
  
  ↓

  Form Hypothesis

  ↓  

  Change One Relevant Header

  ↓  

  Observe Result
  ```  

---

## Whitespace and Syntax Matter

- HTTP has defined syntax and parsing rules.

- Small changes in:

  * Line boundaries.
  * Header formatting.
  * Message framing.
  * Delimiters.

can affect how messages are interpreted.

- Burp often presents HTTP in a readable form, but remember:

  > HTTP messages are structured protocol data, not arbitrary text.

- This becomes especially important in advanced topics involving differences between HTTP parsers.

---

## Representation vs Wire Format

- Burp may display messages in a convenient normalized or editable representation.

- Depending on the HTTP version and tool context, what you see is not always a byte-for-byte depiction of exactly how data appeared on the network.

- This becomes particularly relevant with:

  * HTTP/2.
  * Header representation.
  * Connection management.
  * Protocol translation.

- For now, remember:

  ```text id="p4n8kr"
  Burp Display

  =

  Useful Testing Representation
  ```

- but not always:

  ```text id="n3m9pw"
  Exact Raw Network Bytes
  ```

---

## Request Analysis Example

- Consider:

  ```http id="m7v3qn"
  POST /api/orders/4821/cancel?notify=true HTTP/1.1
  Host: shop.example.com
  Cookie: session=USER_A
  Content-Type: application/json
  Origin: https://shop.example.com

  {
    "reason": "duplicate"
  }
  ```

- Ask:

### What action?

```text id="p4n8kr"
Cancel an order
```

### What object?

```text id="n3m9pw"
Order 4821
```

### What identifies the user?

```text id="m7v3qn"
session=USER_A
```

### What additional behavior is requested?

```text id="p4n8kr"
notify=true
```

### What input is supplied?

```text id="n3m9pw"
reason=duplicate
```

### What security assumptions might exist?

```text id="m7v3qn"
USER_A must be authenticated.

USER_A must be authorized to cancel order 4821.

The order must be in a cancellable state.

The server should validate supplied input.
```

- Now the request has become a security model.

---

## Request Analysis Template

- For important requests, use:

  ```text id="p4n8kr"
  Method:
  
  Host:

  Path:

  Query Parameters:

  Path Identifiers:

  Authentication Mechanism:

  Cookies:
  
  Important Headers:

  Content-Type:

  Body Format:
  
  Body Parameters:

  Security-Sensitive Values:

  Expected Server-Side Checks:

  Questions to Investigate:
  ```  

- This is particularly useful when learning unfamiliar APIs.

---

## Common Beginner Mistakes

- Avoid:

  ```text id="n3m9pw"
  Looking only at the URL
  ```

  ```text id="m7v3qn"
  Looking only at the body
  ```

  ```text id="p4n8kr"
  Ignoring cookies and authorization headers
  ```

  ```text id="n3m9pw"
  Assuming hidden fields are trusted
  ```

  ```text id="m7v3qn"
  Changing many values simultaneously
  ```

  ```text id="p4n8kr"
  Assuming every controllable value is vulnerable
  ```

- Read the complete request first.

---

## HTTP Request Checklist

- When you encounter an unfamiliar request:

  ```text id="n3m9pw"
  [ ] Identify the method.

  [ ] Identify the request target.  

  [ ] Identify the host.

  [ ] Identify authentication/session information.

  [ ] Identify query parameters.

  [ ] Identify dynamic path values.

  [ ] Identify important headers.
  
  [ ] Identify cookies.

  [ ] Identify Content-Type.

  [ ] Determine whether a body exists.
  
  [ ] Identify body format and parameters.

  [ ] Identify security-sensitive values.

  [ ] Determine the expected server-side security checks.

  [ ] Establish the normal baseline before modification.
  ```  

---

## Key Takeaways

* HTTP requests contain a request line, headers, a header/body boundary, and optionally a body.
* The request line identifies the method, target, and protocol representation.
* Headers can directly influence authentication, routing, parsing, and other behavior.
* Inputs may exist in paths, queries, headers, cookies, and bodies.
* Hidden or browser-restricted values are still client-side data.
* Client control alone does not prove vulnerability.
* Message syntax and framing matter.
* Burp's representation is optimized for testing and may not always equal raw wire bytes.
* Read requests first by structure, then by security meaning.
* Every important request should lead to questions about identity, object, action, input, and trust.
