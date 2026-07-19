# HTTP Methods

> **Module Progress**

```text
████░░░░░░░░░ 4 / 13 Lessons
```

- An HTTP method describes the intended semantics of a request.

- Common methods include:

  ```text
  GET
  POST
  PUT
  PATCH
  DELETE
  HEAD
  OPTIONS
  ```

- When using Burp Suite, do not think of methods as merely different words at the beginning of requests.

- Methods communicate intent, influence routing and application behavior, and sometimes interact with authorization controls.

---

## The Request Method

- Consider:

  ```http
  GET /account HTTP/1.1
  Host: example.com
  ```

- The method is:

  ```text
  GET
  ```

- Now compare:

  ```http
  DELETE /account HTTP/1.1
  Host: example.com
  ```

- The resource may be the same:

  ```text
  /account
  ```

- but the intended operation is completely different.

- Conceptually:

  ```text
  Method

  +

  Resource

  =

  Requested Operation
  ```

---

## Common HTTP Methods

- A useful starting model is:

| Method  | Typical Intent                                     |
| ------- | -------------------------------------------------- |
| GET     | Retrieve a representation                          |
| POST    | Submit data or trigger processing                  |
| PUT     | Create or replace a resource representation        |
| PATCH   | Partially modify a resource                        |
| DELETE  | Remove a resource                                  |
| HEAD    | Retrieve response metadata without a response body |
| OPTIONS | Discover communication options/capabilities        |

- These are protocol semantics, not guarantees of how every application is implemented.

---

## GET

- `GET` is intended for retrieving a representation of a resource.

- Example:

  ```http
  GET /products/4821 HTTP/1.1
  Host: example.com
  ```

- Typical uses include:

  * Loading pages.
  * Retrieving API objects.
  * Searching.
  * Fetching images or scripts.
  * Reading account information.

---

## GET Parameters

- Input frequently appears in the query string:

  ```http
  GET /search?q=burp&page=2 HTTP/1.1
  Host: example.com
  ```

- Potential parameters:

  ```text
  q = burp

  page = 2
  ```

- Do not assume query parameters are harmless simply because the method is `GET`.

---

## GET Should Be Safe

- In HTTP semantics, a **safe method** is intended to be read-only from the client's perspective.

- `GET` is defined as safe.

- Conceptually:

  ```text
  GET Resource
    
  ↓
  
  Observe / Retrieve

  ↓

  No Requested State Change
  ```

- However:

  > An application can be implemented incorrectly.

- A `GET` request may still trigger state-changing behavior if developers misuse the method.

---

## Security Lesson — Do Not Assume GET Means Harmless

- Suppose:

  ```http
  GET /account/delete?id=105 HTTP/1.1
  Host: example.com
  ```

- The method says `GET`.

- The route name suggests a potentially destructive action.

- Do not conclude based only on either one.

- Observe what the server actually does.

---

## HEAD

- `HEAD` is closely related to `GET`.

- It requests the response metadata that would correspond to a `GET`, but without transferring the response body.

- Conceptually:

  ```text
  GET

  ↓

  Headers + Body
  ```
  
- versus:

  ```text
  HEAD

  ↓

  Headers Without Response Body
  ```

- A simplified example:

  ```http
  HEAD /report.pdf HTTP/1.1
  Host: example.com
  ```

---

## Why HEAD Matters

- `HEAD` can be useful for learning about a resource without retrieving its full body.

- Depending on server behavior, it may reveal:

  * Status.
  * Content type.
  * Content length.
  * Caching metadata.
  * Other response headers.

- Servers and frameworks may not always handle `HEAD` exactly as expected, so verify behavior rather than assuming.

---

## POST

- `POST` generally submits data to a resource for processing.

- Example:

  ```http
  POST /login HTTP/1.1
  Host: example.com
  Content-Type: application/x-www-form-urlencoded

  username=alice&password=example
  ```

- Common uses include:

  * Login.
  * Form submission.
  * Creating records.
  * Triggering actions.
  * Uploading data.
  * API operations.

---

## POST Is Broad

- Unlike some methods with more specific resource semantics, `POST` is used for many application-defined actions.

- Examples:

  ```text
  POST /login

  POST /orders

  POST /checkout

  POST /password-reset

  POST /api/search

  POST /logout
  ```

- You must understand the endpoint behavior rather than infer everything from the method.

---

## Security Myth — POST Is Not "More Secure" Than GET

- A common misconception is:

  ```text
  POST

  =

  Secure
  ```

- because parameters are not normally displayed in the browser address bar.

- This is false.

- Burp can inspect the request body directly.

- For example:

  ```http
  POST /login HTTP/1.1
  Host: example.com
  Content-Type: application/x-www-form-urlencoded

  username=alice&password=example
  ```

- The data is still part of the HTTP request.

---

## HTTPS Provides Transport Protection

- The broad distinction is:

  ```text
  GET vs POST

  ↓

  Request Semantics
  ```

- while:

  ```text
  HTTP vs HTTPS

  ↓

  Transport Security Context
  ```
  
-  Using `POST` does not automatically encrypt request data.

- HTTPS provides TLS protection for HTTP communication in transit.

---

## PUT

- `PUT` is generally used to create or replace the state of a resource at a target URI.

- Example:

  ```http
  PUT /api/users/105 HTTP/1.1
  Host: example.com
  Content-Type: application/json

  {
    "name": "Alice",
    "email": "alice@example.com"  
  }
  ```

- Conceptually:

  ```text
  Resource /api/users/105

  ↓

  Replace / Set Representation
  ```

- Exact behavior depends on the API design.

---

## PUT vs PATCH

- A useful conceptual distinction:

  ```text
  PUT

  ↓

  Replace or Set the Resource Representation
  ```

- while:

  ```text
  PATCH

  ↓

  Apply Partial Modifications
  ```

- Example:

  ```http
  PATCH /api/users/105 HTTP/1.1
  Host: example.com
  Content-Type: application/json

  {
    "displayName": "Alice"  
  }
  ```

- Only one field may be changed.

---

## PATCH

- `PATCH` is commonly used by APIs for partial updates.

- Potential examples:

  ```text
  PATCH /api/profile

  PATCH /api/users/105

  PATCH /api/orders/4821
  ```

- When analyzing these requests, identify:

  * Which object is targeted.
  * Which fields are supplied.
  * Which fields are omitted.
  * Which fields the server permits the user to modify.

---

## Security-Relevant Update Fields

- Suppose a normal request contains:

  ```json  
  {
    "displayName": "Alice"  
  }
  ```

- A tester may ask whether the server accepts additional security-sensitive properties.

- The correct investigation is not:

  > "Can I add arbitrary JSON?"

- It is:

  > **"Does the server enforce which properties this user is authorized to modify?"**

- This reasoning is relevant to issues such as improper property-level authorization or mass assignment.

---

## DELETE

- `DELETE` requests the removal of a resource.

- Example:

  ```http
  DELETE /api/orders/4821 HTTP/1.1
  Host: example.com
  Authorization: Bearer TOKEN
  ```

- Important questions include:

  ```text
  Who is authenticated?

  What does 4821 identify?

  Is this user authorized to delete it?

  Are business-state restrictions enforced?

  What actually happens after deletion?
  ```

- The method communicates intent.

- Authorization must still be enforced by the server.

---

## OPTIONS

- `OPTIONS` requests information about communication options available for a resource or server.

- Example:

  ```http
  OPTIONS /api/users HTTP/1.1
  Host: example.com
  ```

- Depending on context, responses may include information such as:

  ```http
  Allow: GET, POST, OPTIONS
  ```

- In browser cross-origin workflows, `OPTIONS` is also commonly associated with CORS preflight requests.

---

## CORS Preflight Concept

- A browser may send an `OPTIONS` request before certain cross-origin requests.

- Conceptually:

  ```text
  Browser

  ↓

  OPTIONS Preflight

  ↓

  Server Describes Allowed Cross-Origin Behavior

  ↓

  Browser Decides Whether to Continue
  ```

- Do not confuse a browser-generated preflight request with the application's primary business action.

---

## Safe Methods

- HTTP defines certain methods as **safe**, meaning their intended semantics are essentially read-only.

- Common safe methods include:

  ```text
  GET

  HEAD

  OPTIONS
  ```

- This describes intended semantics.

- It does not guarantee that a poorly designed application never changes state when they are used.

---

## Idempotent Methods

- An operation is idempotent when repeating the same intended request multiple times should have the same intended effect as performing it once.

- Commonly idempotent methods include:

  ```text
  GET

  HEAD

  PUT

  DELETE

  OPTIONS
  ```

- This does not mean every repeated response must be byte-for-byte identical.

---

## Idempotency Example

- Suppose:

  ```http
  DELETE /api/users/105 HTTP/1.1
  ```

- The first request may produce:

  ```text
  User 105 Deleted
  ```

- A second identical request may produce:

  ```text
  User 105 Not Found
  ```

- The responses differ.

- But the intended final resource state remains:

  ```text
  User 105 Does Not Exist
  ```

- That is the important concept.

---

## POST Is Generally Not Idempotent

- Consider:

  ```http
  POST /api/orders HTTP/1.1
  Content-Type: application/json

  {
    "product": 4821
  }
  ```

- Sending this twice may create:

  ```text
  Order A

  +

  Order B
  ```

- Therefore repeated requests can matter significantly.

- This is one reason to be careful when replaying state-changing requests in Burp.

---

## Professional Testing Warning

- Before repeatedly sending a request, determine whether it may:

  * Create records.
  * Submit payments.
  * Send emails.
  * Trigger notifications.
  * Delete resources.
  * Lock accounts.
  * Consume inventory.
  * Change production data.

- Burp makes replay easy.

- That does not mean every request should be replayed carelessly.

---

## Method Switching

- Suppose the application normally sends:

  ```http
  POST /api/resource HTTP/1.1
  ```

- A tester may investigate how the server behaves with another appropriate method.

- For example:

  ```text
  POST → PUT

  POST → PATCH

  POST → GET
  ```  

- This is called method switching or method tampering in some testing contexts.

- The security question is:

  > Does the server enforce security consistently across supported or unexpectedly accepted methods?

---

## Method-Based Authorization Assumptions

- Imagine an application protects:

  ```text
  POST /admin/users
  ```

- but mishandles:

  ```text
  PUT /admin/users
  ```

- or another method mapped to similar backend functionality.

- A security control should not depend on the assumption that clients will use only the method exposed by the normal UI.

- The server must correctly enforce authorization for every reachable operation.

---

## Do Not Blindly Cycle Methods

- Testing every endpoint with every possible method is usually poor methodology.

- Instead:

  ```text
  Observe Normal Behavior

  ↓

  Understand Endpoint Purpose

  ↓

  Identify Plausible Alternative Method

  ↓

  Form Hypothesis

  ↓

  Test Carefully

  ↓

  Compare Behavior
  ```

- Purposeful testing produces better evidence.

---

## Method and Route Together

- Applications often route requests based on both:

  ```text
  Method

  +

  Path
  ```  

- For example:

  ```text
  GET    /api/users/105
  PATCH  /api/users/105
  DELETE /api/users/105
  ```

- The path is identical.

- The requested operations differ.

---

## REST-Style Resource Model

- A simplified API might use:

  ```text
  GET /api/users

  ↓

  List Users
  ```

  ```text
  POST /api/users

  ↓

  Create User
  ```

  ```text
  GET /api/users/105

  ↓
  
  Retrieve User 105
  ```

  ```text
  PATCH /api/users/105

  ↓

  Modify User 105
  ```

  ```text
  DELETE /api/users/105

  ↓

  Delete User 105
  ```

- This pattern is common, but not universal.

- Do not assume every API follows REST conventions correctly.

---

## Same Endpoint, Different Security Questions

- Consider:

  ```text
  GET /api/users/105
  ```

- Question:

  ```text
  Can this user read object 105?
  ```

- Now:

  ```text
  PATCH /api/users/105
  ```

- Question:

  ```text
  Can this user modify object 105?
  ```

- Now:

  ```text
  DELETE /api/users/105
  ```

- Question:

  ```text
  Can this user delete object 105?
  ```

- Authorization may need to be checked separately for each operation.

---

## Method Override Mechanisms

- Some frameworks or applications support ways to represent an alternative method through request data.

- Examples may include mechanisms conceptually similar to:

  ```text
  _method=DELETE
  ```

- or method-override headers.

- These are application/framework dependent.

- Their presence can matter because the apparent HTTP method and the operation eventually executed by application logic may differ.

- Do not assume such mechanisms exist unless you observe evidence for them.

---

## Unsupported Methods

- A server may respond to an unsupported method with:

  ```http
  HTTP/1.1 405 Method Not Allowed
  ```

- It may also include:

  ```http
  Allow: GET, POST
  ```

- However, status codes alone are not always sufficient evidence.

- Inspect the complete response and actual application behavior.

---

## 404 vs 405

- Conceptually:

  ```text
  404 Not Found

  ↓

  Requested Resource Was Not Found
  ```  

- while:

  ```text
  405 Method Not Allowed

  ↓
  
  Resource Context Exists,
  But Method Is Not Supported
  ```

- Real applications may not always distinguish these perfectly.

- Treat response behavior as evidence, not absolute truth.

---

## Method Testing in Burp

- When analyzing an important request:

  ```text
  1. Identify Normal Method

  ↓

  2. Understand Intended Action

  ↓
  
  3. Determine Whether Request Changes State

  ↓

  4. Identify Object and User Context

  ↓
  
  5. Establish Baseline Response

  ↓

  6. Consider Whether Method Behavior Creates a Security Question

  ↓

  7. Change Only What Is Necessary

  ↓

  8. Compare Server Behavior
  ```

---

## Example Analysis

- Normal request:

  ```http
  PATCH /api/accounts/105 HTTP/1.1
  Host: example.com
  Authorization: Bearer USER_A_TOKEN
  Content-Type: application/json

  {
    "displayName": "Alice"
  }
  ```

- Ask:

### What method?

```text
PATCH
```

### Intended action?

```text
Partially modify account 105
```

### What object?

```text
105
```

### What identity?

```text
USER_A_TOKEN
```

### Expected security controls?

```text
USER_A must be authenticated.

USER_A must be authorized to modify account 105.

Only permitted fields should be modifiable.

The resulting state should satisfy business rules.
```

- The method is only one piece of the security model.

---

## Dangerous Assumptions

- Avoid:

  ```text
  GET is always harmless.
  ```

  ```text
  POST is secure because parameters are hidden from the URL.
  ```

  ```text
  PUT and PATCH are interchangeable.
  ```

  ```text
  DELETE automatically means the server actually deletes something.
  ```

  ```text
  OPTIONS always reveals every supported operation.
  ```

  ```text
  The UI uses only one method, so other methods cannot work.
  ```

  ```text
  A 405 response proves no alternate processing path exists anywhere.
  ```

- Test behavior systematically.

---

## HTTP Method Checklist

- When reading a request:

  ```text
  [ ] Identify the method.

  [ ] Understand the method's intended semantics.

  [ ] Determine whether the operation should be safe.

  [ ] Determine whether repeated requests may change state.

  [ ] Identify the resource being acted upon.

  [ ] Identify the authenticated user/session.

  [ ] Determine expected authorization checks.

  [ ] Identify whether the endpoint belongs to a larger REST-style pattern.

  [ ] Look for evidence of method override behavior.
  
  [ ] Do not assume the UI exposes every accepted operation.
  
  [ ] Avoid replaying destructive requests carelessly.
  ```  

---

## Key Takeaways

* HTTP methods describe the intended semantics of requests.
* `GET` typically retrieves resources and should have safe semantics.
* `POST` commonly submits data or triggers processing.
* `PUT` generally creates or replaces resource state.
* `PATCH` applies partial modifications.
* `DELETE` requests resource removal.
* `HEAD` retrieves response metadata without a response body.
* `OPTIONS` describes communication capabilities and is used in CORS preflights.
* Safe and idempotent are different concepts.
* `POST` does not provide transport security; HTTPS does.
* Applications may implement method semantics incorrectly.
* Authorization must be enforced across every reachable operation.
* Method switching should be hypothesis-driven, not random.
* Replaying state-changing requests can have real consequences.
