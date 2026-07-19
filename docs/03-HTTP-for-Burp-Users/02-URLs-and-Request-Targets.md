# URLs and Request Targets

> **Module Progress**

```text id="r7w3mt"
██░░░░░░░░░░░ 2 / 13 Lessons
```

- URLs appear everywhere during web application testing.

- However, the URL displayed in a browser address bar and the request target visible inside an HTTP request are not always identical representations.

- Understanding how URLs are structured helps you recognize:

  * Hosts.
  * Ports.
  * Paths.
  * Query parameters.
  * Fragments.
  * Encoded characters.
  * Security-sensitive identifiers.

---

## URL Mental Model

- Consider:

  ```text id="p4m8qn"
  https://example.com:8443/account/orders?id=4821&view=full#details
  ```

- This can be divided into:

  ```text id="v6n2kx"
  https
    │
    └── Scheme

  example.com
    │
    └── Host

  8443
    │
    └── Port

  /account/orders
    │
    └── Path

  ?id=4821&view=full
    │
    └── Query String

  #details
    │
    └── Fragment  
  ```

- Each component has a different purpose.

---

## General URL Structure

- A simplified URL structure is:

  ```text id="q9m3wr"
  scheme://host:port/path?query#fragment
  ```

- Not every component must always be explicitly present.

- For example:

  ```text id="n5p8mv"
  https://example.com/login
  ```

- contains:

  ```text id="x7m4qt"
  Scheme: https

  Host: example.com

  Path: /login
  ```  

- The port is implied by the scheme's normal default unless another port is specified.

---

## Scheme

- The scheme indicates how the resource should be accessed.

- Common examples include:

  ```text id="m3q9vr"
  http

  https
  ```

- For web security testing, the distinction is important.

- Conceptually:

  ```text id="k8n2pw"
  http

  ↓

  HTTP Communication
  ```  

  ```text id="r4m7qx"
  https

  ↓

  HTTP Carried Over TLS-Protected Communication
  ```  

- HTTPS does not replace HTTP.

- It protects HTTP communication using TLS.

---

## Host

- The host identifies the network destination.

- Example:

  ```text id="t9p3mv"
  example.com
  ```  

- In HTTP/1.1 requests, host information is commonly represented through the `Host` header:

  ```http id="q6n8kr"
  GET /login HTTP/1.1
  Host: example.com
  ```

- The host is security-relevant because many servers host multiple applications on the same infrastructure.

---

## Subdomains

- A host may contain subdomains:

  ```text id="m5q2vn"
  api.example.com

  admin.example.com

  shop.example.com
  ```

- These may represent:

  * Different applications.
  * APIs.
  * Administrative interfaces.
  * Different infrastructure.
  * Different trust boundaries.

- Do not assume all subdomains behave identically.

---

## Port

- A port identifies the network service endpoint.

- Examples:

  ```text id="x8n4pk"
  https://example.com:8443

  http://example.com:8080
  ```

- Common defaults include:

  ```text id="p7m3qw"
  HTTP  → 80

  HTTPS → 443
  ```

- When the default port is used, it is often omitted from the visible URL.

---

## Non-Standard Ports

- Applications may run on ports such as:

  ```text id="v2n9mr"
  8080

  8000
  
  8443

  3000
  ```

- A different port may expose:

  * Another application.
  * Development interface.
  * API service.
  * Administrative service.

- But a port number alone does not prove what service exists.

- Observe actual behavior.

---

## Path

- The path identifies a resource or route.

- Example:

  ```text id="k4m8qx"
  /account/orders
  ```

- Other examples:

  ```text id="r6q2nv"
  /login

  /api/users
  
  /admin/settings

  /files/report.pdf
  ```

- Paths are central to attack-surface mapping.

---

## Path Segments

- Consider:

  ```text id="n9m4pk"
  /api/users/105/orders/4821
  ```

- Possible path segments include:

  ```text id="q3m7vx"
  api

  users

  105

  orders

  4821
  ```

- Some segments may be static routing components.

- Others may be dynamic identifiers.

- For example:

  ```text id="m8p3qw"
  105
  ```

might identify a user.

  ```text id="x5n9kr"
  4821  
  ```

might identify an order.

- Do not assume—investigate.

---

## Path Parameters

- Some applications place variable values directly in the path.

- Example:

  ```http id="t7m2pv"
  GET /users/105 HTTP/1.1
  Host: example.com
  ```

- Here:

  ```text id="q8m4nr"
  105
  ```

may act as an object identifier.

- These values can be security-relevant when they influence access to resources.

---

## Query String

- A query string begins after:

  ```text id="p4n8mv"  
  ?
  ```

- Example:

  ```text id="v9m3qx"
  /search?q=burp&page=2
  ```

- The query string is:

  ```text id="k6n2pr"
  q=burp&page=2
  ```

- It contains two name-value pairs:

  ```text id="r3m8vw"
  q = burp

  page = 2
  ```

---

## Query Parameters

- Query parameters commonly use:

  ```text id="n7q4mx"
  name=value
  ```

- Multiple parameters are often separated using:

  ```text id="m8q4vx"
  &
  ```

- Example:

  ```text id="k5n9pr"
  /products?category=laptop&sort=price&page=3
  ```

- Potential parameters:

  ```text id="r7w3mt"
  category = laptop

  sort = price

  page = 3
  ```

- These values may influence server behavior.

---

## Path vs Query

- Compare:

  ```text id="p4m8qn"
  /users/105
  ```

- with:

  ```text id="v6n2kx"
  /users?id=105
  ```

- Both may represent similar application functionality.

- But the identifier appears in different locations.

- Burp users should learn to recognize input regardless of where it appears.

---

## Fragment

- A fragment begins with:

  ```text id="q9m3wr"
  #
  ```

- Example:

  ```text id="n5p8mv"
  https://example.com/account#security
  ```

- The fragment is:

  ```text id="x7m4qt"
  security
  ```

- A crucial HTTP concept:

  > **The URL fragment is normally handled client-side and is not sent to the server as part of the ordinary HTTP request target.**

---

## Fragment Example

- Browser URL:

  ```text id="m3q9vr"
  https://example.com/page?id=10#comments
  ```

- A typical HTTP/1.1 request might look like:

  ```http id="k8n2pw"
  GET /page?id=10 HTTP/1.1
  Host: example.com
  ```

- Notice:

  ```text id="r4m7qx"
  #comments
  ```

is absent.

---

## Why Fragment Behavior Matters

- If you see sensitive data only after `#`, do not automatically assume the server receives it in the initial HTTP request.

- However, client-side JavaScript can read fragments and later use their contents in:

  * DOM operations.
  * API requests.
  * Navigation logic.
  * Client-side routing.

- So:

  ```text id="t9p3mv"
  Fragment

  Normally Not Sent Directly in HTTP Request

  ↓

  But Client-Side Code May Process It
  ```

- Context matters.

---

## Browser URL vs HTTP Request Target

- Suppose the browser displays:

  ```text id="q6n8kr"  
  https://example.com/account?id=105
  ```

- A typical HTTP/1.1 request may begin:

  ```http id="m5q2vn"
  GET /account?id=105 HTTP/1.1
  Host: example.com
  ```

- The request line does not necessarily repeat the full browser URL.

- Instead, you often see:

  ```text id="x8n4pk"
  /account?id=105
  ```

as the request target, while the host appears separately.

---

## Request Line Breakdown

- Example:

  ```http id="p7m3qw"
  GET /account?id=105 HTTP/1.1
  ```

- Breakdown:

  ```text id="v2n9mr"
  GET
  │
  └── Method

  /account?id=105  
  │
  └── Request Target

  HTTP/1.1  
  │
  └── Protocol Version
  ```

- This line tells you what action is being requested and where.

---

## Request Target Forms

- For normal web application testing, the most familiar request target is often:

  ```text id="k4m8qx"
  /path?query
  ```

- However, HTTP supports different request-target forms depending on the request context.

- You do not need to memorize every form immediately.

- The key lesson is:

  > Do not assume every HTTP request line must look exactly like a browser URL.

---

## Percent-Encoding

- URLs cannot always represent every character directly in every context.

- Percent-encoding represents bytes using:

  ```text id="r6q2nv"
  %HH
  ```

where `HH` is a hexadecimal value.

- Common examples:

  ```text id="n9m4pk"
  Space → %20

  /     → %2F

  ?     → %3F

  =     → %3D
  ```

- Encoding changes representation.

- It does not automatically change the underlying meaning after decoding.

---

## Example

- Readable value:

  ```text id="q3m7vx"
  hello world
  ```

- Percent-encoded:

  ```text id="m8p3qw"
  hello%20world
  ```

- Applications may decode this before processing it.

---

## Encoding Is Context-Dependent

- Do not assume:

  > "Encoded means safe."

- or:

  > "Encoded means malicious."

- Encoding is simply a representation mechanism.

- Security behavior depends on:

  * Where decoding occurs.
  * How many times decoding occurs.
  * Which component performs decoding.
  * How the decoded value is used.

---

## Double Encoding Concept

- Sometimes data may pass through multiple decoding layers.

- Conceptually:

  ```text id="x5n9kr"
  Encoded Value

  ↓

  Layer 1 Decodes

  ↓

  Still Encoded Representation
  
  ↓

  Layer 2 Decodes

  ↓
  
  Final Value
  ```

- This becomes relevant in advanced vulnerability classes, but the foundation is simple:

  > Always ask what representation the server actually processes.

---

## Absolute vs Relative URLs

- An absolute URL includes components such as scheme and host:

  ```text id="t7m2pv"
  https://example.com/account
  ```

- A relative reference may look like:

  ```text id="q8m4nr"
  /account
  ```

- or:

  ```text id="p4n8mv"
  ../account
  ```

- Browsers resolve relative references using context from the current document or base URL.

---

## Security-Sensitive URL Components

- When inspecting URLs, pay particular attention to values that appear to control:

  ```text id="v9m3qx"
  Identity

  Object Selection

  Redirect Destinations

  File Paths

  Search Input
  
  Pagination

  Filtering

  Business Actions
  ```

- Examples:

  ```text id="k6n2pr"
  ?id=105

  ?user=alice

  ?redirect=/dashboard

  ?file=report.pdf

  /order/4821
  ```

- Their names provide clues—not proof of vulnerability.

---

## Do Not Trust Parameter Names

- A parameter named:

  ```text id="r3m8vw"
  admin=false
  ```

may be security-sensitive.

- Or it may be ignored completely.

- Likewise:

  ```text id="n7q4mx"
  id=105
  ```

- might represent:

  * User ID.
  * Product ID.
  * Internal database key.
  * Harmless UI state.

- Observe server behavior before concluding what a value controls.

---

## Duplicate Parameters

- Requests can sometimes contain repeated parameter names:

  ```text id="m8q4vx"
  /search?id=10&id=20
  ```

- Different components may interpret duplicates differently.

- For example:

  ```text id="k5n9pr"
  First Value

  Last Value

  All Values

  Rejected as Invalid
  ```

- This becomes important when multiple HTTP-processing layers interpret requests differently.

- The key foundation:

  > Do not assume every server parses ambiguous input identically.

---

## Parameter Order

- Consider:

  ```text id="r7w3mt"
  /search?a=1&b=2
  ```

- versus:

  ```text id="p4m8qn"
  /search?b=2&a=1
  ```

- Many applications treat these equivalently.

- Some application logic, signatures, caches, or intermediary systems may not.

- Again, actual behavior matters more than assumptions.

---

## Reading URLs in Burp

- When you encounter a request, mentally break it into:

  ```text id="v6n2kx"
  Scheme

  Host

  Port

  Path

  Query Parameters

  Fragment Context if Relevant
  ```

- Then ask:

  ```text id="q9m3wr"
  Which values are static?

  Which values change?
  
  Which values identify objects?

  Which values influence behavior?
  
  Which parts actually reached the server?

  Which parts were processed only by the client?
  ```

---

## Example Analysis

- Consider:

  ```text id="n5p8mv"
  https://shop.example.com:8443/orders/4821?format=pdf#preview
  ```

- Breakdown:

  ```text id="x7m4qt"
  Scheme:
  https

  Host:
  shop.example.com
  
  Port:
  8443

  Path:
  /orders/4821

  Possible Object Identifier:
  4821

  Query Parameter:
  format=pdf

  Fragment:
  preview
  ```

- A typical initial server request would not include:

  ```text id="m3q9vr"
  #preview
  ```

- but client-side code may still use it.

---

## Security Reasoning Example

- Suppose:

  ```text id="k8n2pw"
  GET /api/invoices/4821?download=true HTTP/1.1
  Host: example.com
  Cookie: session=USER_A
  ```

- Potential investigation questions:

  ```text id="r4m7qx"
  What does 4821 identify?

  Does USER_A own this object?

  What does download=true change?

  Is authorization checked independently of the identifier?

  Does changing the query alter only presentation or server behavior?
  ```  

- HTTP understanding creates better security questions.

---

## URL Analysis Checklist

- When inspecting a URL or request target:

  ```text id="t9p3mv"
  [ ] Identify the scheme.

  [ ] Identify the host.

  [ ] Identify explicit or implied port.

  [ ] Break down the path.

  [ ] Identify dynamic path segments.

  [ ] Identify query parameters.

  [ ] Recognize percent-encoded values.

  [ ] Determine whether a fragment exists.

  [ ] Remember fragments are normally not sent directly to the server.
  
  [ ] Identify values that may influence security decisions.

  [ ] Avoid assuming parameter names reveal actual behavior.
  ```

---

## Key Takeaways

* A URL can contain scheme, host, port, path, query, and fragment components.
* The browser URL and HTTP request target are related but not identical representations.
* Paths may contain dynamic identifiers.
* Query strings contain parameters that can influence server behavior.
* URL fragments are normally not sent directly in ordinary HTTP requests.
* Client-side code can still process fragments.
* Percent-encoding changes representation, not inherently security meaning.
* Different layers may decode or parse input differently.
* Duplicate parameters can be interpreted differently by different systems.
* Parameter names are clues, not evidence.
* Break URLs into components before forming security hypotheses.
