# Practical HTTP Analysis Lab

> **Module Progress**

```text
█████████████ 13 / 13 Lessons
```

- This capstone combines the concepts from the entire **HTTP for Burp Users** module.

- You will analyze a fictional application called:

  ```text
  AcmeShop
  ```

- The objective is not to fire random payloads or guess vulnerability names.

- Instead, you will practice the professional workflow:

  ```text
  Observe

  ↓

  Map

  ↓

  Understand

  ↓

  Hypothesize

  ↓

  Test

  ↓

  Compare

  ↓

  Verify

  ↓

  Conclude
  ```  

- All traffic in this lab is fictional and designed for educational analysis.

---

## Lab Objectives

- By completing this lab, you should be able to:

  * Read HTTP requests and responses systematically.
  * Identify methods, paths, headers, cookies, parameters, and bodies.
  * Understand redirects and authentication transitions.
  * Track session state.
  * Analyze cookie security attributes.
  * Distinguish authentication from authorization.
  * Map identity, object, and action.
  * Identify client-controlled data.
  * Analyze JSON and encoded values.
  * Reason about multi-step workflows.
  * Recognize HTTP/2 pseudo-header concepts.
  * Form testable security hypotheses.
  * Separate evidence from assumptions.
  * Produce an HTTP Investigation Journal.

---

## Rules of the Lab

- For every scenario:

  ```text
  Do Not Guess

  Do Not Overclaim

  Do Not Assume a Vulnerability From One Response  
  ```  

- Use:

  ```text
  Evidence

  Context

  Controlled Comparison

  Verification
  ```  

- Your goal is not to find the largest number of vulnerabilities.

- Your goal is to produce the strongest reasoning.

---

## Application Overview

- The fictional application contains:

  ```text
  shop.acme.test

  api.acme.test
  ```  

- Features include:

  * User registration.
  * Login.
  * MFA.
  * User profiles.
  * Product browsing.
  * Shopping cart.
  * Checkout.
  * Orders.
  * Password reset.
  * Administrative functionality.

- Two controlled accounts exist:

  ```text
  User A

  Email:
  alice@acme.test

  User ID:
  105
  ```  

  ```text
  User B

  Email:
  bob@acme.test
  
  User ID:
  106
  ```

- Assume both accounts belong to the tester and may be used for controlled authorization testing.

---

## Part 1 — Basic Request Analysis

- Analyze:

  ```http
  GET /products?category=laptops&page=2 HTTP/1.1
  Host: shop.acme.test
  User-Agent: Mozilla/5.0
  Accept: text/html
  Cookie: preference=dark
  ```

- Response:

  ```http
  HTTP/1.1 200 OK
  Content-Type: text/html; charset=UTF-8
  Cache-Control: private, max-age=0
  Content-Length: 8421

  <html>
  ...  
  </html>
  ```

---

## Task 1

- Identify:

  ```text
  HTTP Method:

  Path:

  Query Parameters:

  Host:
  
  Request Headers:
    
  Cookies:
  
  Response Status:

  Response Content Type:

  Character Encoding:

  Cache Directive:
  ```

---

## Questions

1. Which values are potentially client-controlled?
2. Does the request contain authentication evidence?
3. What does `charset=UTF-8` describe?
4. Does `preference=dark` necessarily identify a logged-in user?
5. What does `200 OK` prove?

---

## Expected Reasoning

- You should recognize:

  ```text
  Method:
  GET

  Path:
  /products

  Parameters:
  category=laptops
  page=2

  Cookie:
  preference=dark

  Response:
  200 OK

  Media Type:  
  text/html

  Charset:
  UTF-8
  ```

- Do not conclude that the user is authenticated merely because a cookie exists.

---

## Part 2 — Login and Session Creation

- User A submits:

  ```http
  POST /login HTTP/1.1
  Host: shop.acme.test
  Content-Type: application/x-www-form-urlencoded
  Cookie: session=ANON-7F92
  Content-Length: 49

  email=alice%40acme.test&password=ExamplePass123
  ```

- Response:

  ```http
  HTTP/1.1 302 Found
  Location: /mfa
  Set-Cookie: session=PREAUTH-A91C; Secure; HttpOnly; SameSite=Lax; Path=/
  Content-Length: 0
  ```

---

## Task 2

- Analyze:

  ```text
  Pre-Login Session:

  Post-Login Session:

  Redirect Destination:

  Cookie Attributes:

  Authentication State:
  ```

---

## Questions

1. Did the session identifier change?
2. What does that suggest?
3. Is User A fully authenticated yet?
4. What does `HttpOnly` protect against?
5. What does `Secure` control?
6. What does `SameSite=Lax` influence?
7. Is the `302` itself enforcing MFA?

---

## Expected Reasoning

- Observe:

  ```text
  ANON-7F92

  ↓

  PREAUTH-A91C  
  ```

- The session changed after password validation.

- This suggests session rotation occurred.

- However:

  ```text
  Password Accepted

  ≠

  MFA Completed
  ```  

- The redirect to `/mfa` guides the client.

- It does not itself prove that protected endpoints enforce MFA.

---

## Part 3 — MFA State Transition

- Request:

  ```http
  POST /mfa HTTP/1.1
  Host: shop.acme.test
  Cookie: session=PREAUTH-A91C
  Content-Type: application/x-www-form-urlencoded

  code=482913
  ```

- Response:

  ```http
  HTTP/1.1 302 Found
  Location: /dashboard
  Set-Cookie: session=AUTH-A772; Secure; HttpOnly; SameSite=Lax; Path=/
  ```

---

## Task 3

- Map the state transition.

  ```text
  Before:

  Action:

  After:
  ```

---

## Expected Model

```text
Partially Authenticated

↓

Valid MFA

↓

Fully Authenticated
```

- Session transition:

  ```text
  PREAUTH-A91C

  ↓

  AUTH-A772
  ```  

---

## Investigation Question

- You want to determine whether MFA is actually enforced.

- Which test is stronger?

### Test A

- Change:

  ```text
  code=482913
  ```

- to:

  ```text
  code=000000
  ```

### Test B

- Using:

  ```text
  session=PREAUTH-A91C
  ```

- directly request:

  ```text
  GET /dashboard
  ```

- Both tests may provide useful information.

- But Test B directly investigates whether the intermediate session receives access to a protected resource before MFA completion.

---

## Hypothesis

- Write:

  ```text
  Observation:

  Security Expectation:

  Possible Failure:

  Controlled Test:
  
  Verification:
  ```  
  
- Example structure:

  ```text
  Observation:
  A partially authenticated session exists before MFA completion.

  Security Expectation:
  Protected account resources should require completed MFA.

  Possible Failure:
  The application may rely on the redirect workflow without enforcing MFA server-side.

  Controlled Test:
  Request a protected resource using the pre-MFA session.

  Verification:
  Determine whether protected account data or functionality is returned.
  ```

---

## Part 4 — Authenticated API Request

- After MFA:

  ```http
  GET /api/users/105/profile HTTP/1.1
  Host: api.acme.test
  Authorization: Bearer eyJhbGciOi...REDACTED
  Accept: application/json
  ```

- Response:

  ```http
  HTTP/1.1 200 OK
  Content-Type: application/json

  {
    "id": 105,
    "name": "Alice",
    "email": "alice@acme.test",
    "phone": "+91-0000000000"
  }
  ```

---

## Task 4 — Identity, Object, Action

- Map:

  ```text
  Identity:

  Object:

  Action:
  ```

- Possible answer:

  ```text
  Identity:
  Authenticated identity represented by bearer token

  Object:
  User profile 105

  Action:
  Read
  ```

---

## Questions

1. Is the bearer token sensitive?
2. Does `200 OK` prove authorization is correctly implemented?
3. What additional information is required before testing another ID?
4. Why should the token be redacted from public reports?

---

## Part 5 — Controlled Authorization Test

- Known:

  ```text
  User A owns profile 105.

  User B owns profile 106.
  ```

- Baseline:

  ```http
  GET /api/users/105/profile HTTP/1.1
  Host: api.acme.test
  Authorization: Bearer USER_A_TOKEN
  ```

- Response:

  ```http
  HTTP/1.1 200 OK

  {
    "id": 105,
    "name": "Alice",
    "email": "alice@acme.test"
  }
  ```

- Controlled test:

  ```http
  GET /api/users/106/profile HTTP/1.1
  Host: api.acme.test
  Authorization: Bearer USER_A_TOKEN
  ```

- Response:

  ```http
  HTTP/1.1 200 OK

  {
    "id": 106,
    "name": "Bob",
    "email": "bob@acme.test"
  }
  ```

---

## Task 5

- Separate:

  ```text
  Evidence:

  Inference:

  Potential Impact:

  Additional Verification:
  ```  

---

## Strong Reasoning

- Evidence:

  ```text
  A controlled User A credential requested profile 106.

  Profile 106 is known to belong to controlled User B.

  The server returned User B's profile data with HTTP 200.
  ```

- Inference:

  ```text
  The endpoint appears to lack sufficient object-level authorization for profile access.
  ```

- Potential impact:

  ```text
  An authenticated user may be able to access another user's private profile information.
  ```

- Additional verification:

  ```text
  Confirm User B normally owns and can access profile 106.

  Repeat the cross-account request.

  Confirm the returned data matches User B's controlled account.
  ```

---

## What Not to Write

- Do not write:

  ```text
  Changing 105 to 106 proves every account is vulnerable.
  ```

- That exceeds the evidence.

- You verified a controlled cross-account case.

- Report only what the evidence supports.

---

## Part 6 — Validation vs Authorization

- Request:

  ```http
  GET /api/users/abc/profile HTTP/1.1
  Host: api.acme.test
  Authorization: Bearer USER_A_TOKEN
  ```

- Response:

  ```http
  HTTP/1.1 400 Bad Request

  {
    "error": "User ID must be numeric"
  }
  ```

- Request:

  ```http
  GET /api/users/106/profile HTTP/1.1
  Host: api.acme.test
  Authorization: Bearer USER_A_TOKEN
  ```

- Response:

  ```http
  HTTP/1.1 200 OK
  ```

---

## Task 6

- Explain the difference.

  ```text  
  abc

  ↓

  Validation Question
  ```  

  ```text
  106

  ↓

  Authorization Question
  ```

- The application correctly rejecting a nonnumeric ID does not prove it correctly enforces ownership.

- Remember:

  ```text
  Valid Input

  ≠

  Authorized Input
  ```  

---

## Part 7 — Encoded Object Identifier

- Request:

  ```http
  GET /account?ref=MTA1 HTTP/1.1
  Host: shop.acme.test
  Cookie: session=AUTH-A772
  ```

---

## Task 7

- The value:

  ```text
  MTA1
  ```

appears to be Base64.

- Decode it conceptually:

  ```text
  MTA1

  ↓

  105
  ```

---

## Questions

1. Is Base64 encryption?
2. Does encoding `105` make it a secure identifier?
3. What security control is still required?

- Answer conceptually:

  ```text
  Encoding

  ≠

  Authorization
  ```

- The server must still verify whether the authenticated user is allowed to access the referenced object.

---

## Part 8 — JSON Business Logic

- Normal request:

  ```http
  POST /api/cart/items HTTP/1.1
  Host: api.acme.test
  Authorization: Bearer USER_A_TOKEN
  Content-Type: application/json

  {
    "productId": 4821,
    "quantity": 1,
    "unitPrice": 79999  
  }
  ```

- Response:

  ```http
  HTTP/1.1 201 Created

  {
    "cartItemId": 771,
    "subtotal": 79999
  }
  ```

---

## Task 8

- Identify potentially client-controlled values:

  ```text
  productId

  quantity

  unitPrice
  ```

- Now form a hypothesis.

---

## Hypothesis Example

  ```text
  Observation:

  The client submits unitPrice.

  Security Expectation:

  The server should use authoritative product pricing.

  Possible Failure:

  The server may trust the client-supplied price.

  Controlled Test:

  Change only unitPrice while keeping product and quantity constant.

  Verification:

  Inspect the authoritative cart/order total and final transaction state.
  ```

---

## Important

- If changing:

  ```text
  79999
  ```

- to:

  ```text  
  1
  ```

- returns:

  ```text
  201 Created
  ```

that alone does not prove price manipulation.

- You must verify:

  ```text
  Cart Total

  Checkout Total

  Order Record

  Actual Charged Amount
  ```

where safe and authorized.

---

## Part 9 — Hidden Client State

- Checkout form:

  ```html
  <form action="/checkout/review" method="POST">
    <input type="hidden" name="cartId" value="771">
    <input type="hidden" name="discount" value="0">
    <input type="hidden" name="shippingFee" value="499">
  </form>
  ```

---

## Task 9

- Which values are security-relevant?

- Potentially:

  ```text
  cartId

  discount
  
  shippingFee
  ```

- But the existence of hidden fields does not prove the server trusts them.

---

## Core Rule

```text
Hidden

≠

Server-Controlled
```

- A tester should ask:

  ```text
  Does the server independently derive or validate these values?
  ```

---

## Part 10 — Checkout Workflow

- Normal sequence:

  ```text
  POST /cart

  ↓

  POST /checkout/address

  ↓

  POST /checkout/shipping

  ↓

  POST /checkout/payment

  ↓

  POST /checkout/confirm
  ```

- Assume payment has not yet been completed.

- A tester sends:

  ```http
  POST /checkout/confirm HTTP/1.1
  Host: shop.acme.test
  Cookie: session=AUTH-A772
  Content-Type: application/x-www-form-urlencoded

  cartId=771
  ```

- Response:

  ```http
  HTTP/1.1 302 Found
  Location: /orders/9912
  ```

---

## Task 10

- Do not immediately conclude:
  
  ```text
  Payment Bypass
  ```

- What must be verified?

---

## Verification Questions

```text
Was order 9912 actually created?

What is its payment state?

Can the order be fulfilled?

Was payment already authorized elsewhere?

Was the request merely redirecting to an incomplete order?

Did inventory or account state change?
```

- A redirect is evidence.

- It is not complete proof of impact.

---

## Workflow Model

- Expected:

  ```text
  Address

  ↓

  Shipping

  ↓
  
  Payment

  ↓

  Confirmation
  ```  

- Hypothesis:

  ```text
  The confirmation endpoint may not enforce
  the payment prerequisite server-side.
  ```

- Verification must determine the resulting authoritative state.

---

## Part 11 — CSRF Token Analysis

- Request:

  ```http
  POST /account/change-email HTTP/1.1
  Host: shop.acme.test
  Cookie: session=AUTH-A772
  Content-Type: application/x-www-form-urlencoded

  email=newalice%40acme.test&csrf=CSRF-88219
  ```

---

## Task 11

- The presence of:

  ```text
  csrf=CSRF-88219
  ```

- proves only:

  ```text
  A CSRF-related parameter exists.
  ```

- It does not prove correct protection.

---

## Controlled Questions

```text
What happens if the token is missing?

What happens if the token is modified?

Is it tied to the authenticated session?

Can another controlled user's token be substituted?

Is it validated for the relevant state-changing request?
```

---

## Core Rule

```text
Token Present

≠

Token Correctly Validated
```

---

## Part 12 — Password Reset Workflow

- Sequence:

  ```text
  POST /forgot-password
  
  ↓

  Reset Email Generated

  ↓

  GET /reset?token=RESET-A1B2

  ↓

  POST /reset-password
  ```

- Final request:

  ```http
  POST /reset-password HTTP/1.1
  Host: shop.acme.test
  Content-Type: application/x-www-form-urlencoded

  token=RESET-A1B2&password=NewExamplePass123
  ```

- Response:

  ```http
  HTTP/1.1 302 Found
  Location: /login?reset=success
  ```

---

## Task 12

- List the important lifecycle questions.

- Expected:

  ```text
  Is the token random enough?

  Is it tied to the intended account?

  Does it expire?

  Is it single-use?

  Can it be replayed?

  Does requesting a new token invalidate an old one?

  What happens to existing authenticated sessions after reset?
  ```  

---

## One-Time Token Test

- Normal lifecycle:

  ```text
  Token Generated

  ↓

  Token Used

  ↓

  Password Changed

  ↓

  Token Invalidated
  ```  

- Controlled test:

  ```text
  Attempt to reuse the same token
  after successful reset.
  ```

- Expected:

  ```text
  Rejected
  ```

- Do not perform this against accounts you do not control.

---

## Part 13 — Response Analysis

- Request:

  ```http
  GET /api/orders/999999 HTTP/1.1
  Host: api.acme.test
  Authorization: Bearer USER_A_TOKEN
  ```

- Response:

  ```http
  HTTP/1.1 500 Internal Server Error
  Content-Type: application/json

  {
    "error": "Database query failed near order_id"
  }
  ```

---

## Task 13

- Which conclusion is justified?

### A

```text
SQL injection confirmed.
```

### B

```text
The request triggered a database-related error.
Further controlled validation is required before
concluding SQL injection.
```

- Correct reasoning:

  ```text
  B
  ```

---

## Evidence vs Conclusion

- Evidence:

  ```text
  A database-related error appears in the response.
  ```

- Possible inference:

  ```text
  The supplied order identifier may influence
  a database-backed code path.
  ```

- Not yet proven:

  ```text
  Exploitable SQL injection.
  ```

---

## Part 14 — HTTP/2 Awareness

- Suppose Burp displays the request conceptually as:

  ```text
  :method: GET
  :scheme: https
  :authority: api.acme.test
  :path: /api/orders/9912
  authorization: Bearer USER_A_TOKEN
  accept: application/json
  ```

---

## Task 14

- Map:

  ```text
  Method:

  Scheme:

  Authority:

  Path:

  Authentication:
  ```  

- Expected:

  ```text
  Method:
  GET

  Scheme:
  https

  Authority:
  api.acme.test

  Path:
  /api/orders/9912

  Authentication:
  Bearer token
  ```

---

## Questions

1. Is `:authority` an ordinary arbitrary custom header?
2. Does HTTP/2 being binary mean the request is encrypted?
3. Which compression mechanism is associated with HTTP/2 headers?
4. Which mechanism is associated with HTTP/3?
5. What transport does HTTP/3 use?

- Expected concepts:

  ```text
  HTTP/2:
  HPACK

  HTTP/3:
  QPACK

  HTTP/3 Transport:
  QUIC over UDP
  ```

---

## Part 15 — Protocol Translation

- Architecture:

  ```text
  Browser

  ↓

  HTTP/2

  ↓

  Reverse Proxy

  ↓

  HTTP/1.1

  ↓

  Application Server  
  ```

---

## Task 15

- Identify the security-relevant boundary:

  ```text
  HTTP/2

  ↓

  Protocol Translation

  ↓

  HTTP/1.1
  ```  

- Question:

  > Why can this matter?

- Because:

  ```text
  Different Components

  May Parse or Represent Requests Differently
  ```

- This can become relevant in advanced testing involving:

  * Request desynchronization.
  * Header translation.
  * Message framing.
  * Proxy/backend interpretation differences.

- Do not attempt advanced protocol exploitation without appropriate authorization and methodology.

---

## Part 16 — Source and Sink Analysis

- Request:

  ```http
  GET /preview?url=https%3A%2F%2Fexample.com HTTP/1.1
  Host: shop.acme.test
  Cookie: session=AUTH-A772
  ```

---

## Task 16

- Potential source:

  ```text
  url parameter
  ```

- Possible sink:

  ```text
  Unknown
  ```

- You do not yet know whether the server:

  * Fetches the URL.
  * Reflects it.
  * Stores it.
  * Ignores it.
  * Validates it.

---

## Correct Reasoning

- Do not conclude:

  ```text
  SSRF
  ```

from the parameter name alone.

- Instead:

  ```text
  Observation:

  A client-controlled parameter named url exists.

  Hypothesis:

  The application may use the value for a server-side fetch.

  Test:

  Use safe, authorized evidence to determine whether
  the server initiates an outbound request.

  Verification:
  
  Confirm server-side interaction rather than
  client-side navigation or reflection.
  ```

- This demonstrates source-to-sink reasoning.

---

## Part 17 — Redirect Analysis

- Request:

  ```http
  GET /login?next=%2Faccount HTTP/1.1
  Host: shop.acme.test
  ```

- After authentication:

  ```http
  HTTP/1.1 302 Found
  Location: /account
  ```

---

## Task 17

- Observation:

  ```text
  next appears to influence post-login navigation.
  ```

- Do not conclude:

  ```text
  Open Redirect
  ```

until you determine whether external destinations are accepted.

- A parameter name is evidence of input.

- Not evidence of exploitability.

---

## Part 18 — Method Analysis

- Normal request:

  ```http
  GET /api/orders/9912 HTTP/1.1
  ```

- Tester sends:

  ```http
  DELETE /api/orders/9912 HTTP/1.1
  Authorization: Bearer USER_A_TOKEN
  ```

- Response:

  ```http
  HTTP/1.1 405 Method Not Allowed
  Allow: GET, HEAD
  ```

---

## Task 18

- Interpret:

  ```text
  405 Method Not Allowed
  ```

- This indicates that the resource does not allow the attempted method in the observed context.

- It does not prove:

  ```text
  All authorization controls are secure.
  ```

- It answers a method-handling question.

---

## Part 19 — HEAD and OPTIONS

- Request:

  ```http
  OPTIONS /api/orders/9912 HTTP/1.1
  Host: api.acme.test
  ```

- Response:

  ```http
  HTTP/1.1 204 No Content
  Allow: GET, HEAD, OPTIONS
  ```

---

## Questions

1. What information does `Allow` provide?
2. Does `OPTIONS` automatically reveal every backend capability?
3. Why should observed behavior be tested rather than assumed from one header?

- Expected reasoning:

  ```text
  Allow provides advertised method support.

  Advertised behavior should still be interpreted
  within actual server and intermediary behavior.
  ```

---

## Part 20 — Cookie Scope Analysis

- Response:

  ```http
  Set-Cookie: session=AUTH-A772;
  Secure;
  HttpOnly;
  SameSite=Lax;
  Domain=.acme.test;
  Path=/
  ```

---

## Task 20

- Analyze:

  ```text
  Secure:

  HttpOnly:

  SameSite:

  Domain:

  Path:
  ```  

- Then ask:

  ```text
  Is the cookie scoped more broadly than necessary?
  ```

- Do not automatically report broad scope as a vulnerability.

- Determine:

  * Which subdomains exist.
  * Which systems need the cookie.
  * Whether broader scope creates realistic security impact.

---

## Part 21 — State Verification

- Request:

  ```http
  POST /account/change-phone HTTP/1.1
  Host: shop.acme.test
  Cookie: session=AUTH-A772
  Content-Type: application/x-www-form-urlencoded

  phone=1234567890
  ```

- Response:

  ```http
  HTTP/1.1 500 Internal Server Error
  ```

---

## Task 21

- Which action should come next?

### A

- Conclude the update failed.

### B

- Request the account profile and verify the stored phone number.

- Correct:

  ```text
  B
  ```

- Core principle:

  ```text
  Response Status

  ≠

  Authoritative State
  ```  

- Always verify the actual outcome when security impact depends on state.

---

## Part 22 — One-Variable Mutation

- Baseline:

  ```http
  GET /api/orders/9912 HTTP/1.1
  Authorization: Bearer USER_A_TOKEN
  ```

- Modified test:

  ```http
  GET /api/orders/9913 HTTP/1.1
  Authorization: Bearer USER_A_TOKEN
  ```

- Only:

  ```text
  Object ID
  ```

changed.

- This is a strong controlled comparison.

---

## Weak Test

- Changing simultaneously:

  ```text
  Order ID

  Bearer Token

  HTTP Method

  Custom Role Header
  ```

creates ambiguous evidence.

- Core rule:

  ```text
  Change One Relevant Variable

  ↓  

  Observe

  ↓
  
  Understand
  
  ↓

  Then Continue
  ```

---

## Part 23 — Positive and Negative Controls

- Suppose testing order authorization.

- Use:

  ```text
  Positive Control:

  User A → User A Order
  ```  

  ```text
  Positive Control:

  User B → User B Order
  ```

  ```text
  Authorization Test:

  User A → User B Order
  ```

  ```text
  Negative / Comparison Control:

  Unauthenticated → User B Order
  ```

- These comparisons make conclusions much stronger.

---

## Part 24 — Investigation Challenge

- Analyze this complete request:

  ```http  
  POST /api/orders/9912/cancel HTTP/1.1
  Host: api.acme.test
  Authorization: Bearer USER_A_TOKEN
  Content-Type: application/json
  X-Requested-With: XMLHttpRequest

  {
    "userId": 105,
    "reason": "Changed my mind",
    "refundAmount": 79999
  }
  ```

---

## Task 24A — Map the Request

- Identify:

  ```text
  Method:

  Host:

  Path:

  Authentication:

  Object:

  Action:

  Content Type:

  Client-Controlled Inputs:
  ```  
  
---

## Task 24B — Trust Analysis

- Potentially client-controlled:

  ```text
  Order ID

  userId

  reason

  refundAmount
  
  Headers

  Bearer Credential
  ```  

- Security questions:

  ```text
  Does the server derive ownership from the authenticated identity?

  Does it trust userId?

  Does it independently determine the refund amount?
  
  Is the order eligible for cancellation?

  Has payment actually occurred?

  Has the order already shipped?

  Can the action be repeated?
  ```

---

## Task 24C — Build Three Hypotheses

- Example:

### Hypothesis 1 — Authorization

```text
The endpoint may trust userId or orderId
without verifying ownership against the authenticated identity.
```

### Hypothesis 2 — Business Logic

```text
The endpoint may trust the client-supplied refundAmount.
```

### Hypothesis 3 — Workflow State

```text
The endpoint may allow cancellation
after the order reaches a non-cancellable state.
```

- Notice:

  - One request can expose several testing ideas.

  - But each hypothesis should be tested separately.

---

## Part 25 — Full Investigation Journal

- Create an investigation entry using:

  ```text
  Target:

  Endpoint:

  Account / Role:

  Starting State:

  Observation:

  Identity:

  Object:

  Action:

  Client-Controlled Inputs:

  Expected Security Control:

    Hypothesis:

  Baseline Request:

  Controlled Modification:
  
  Expected Result:
  
  Observed Result:
  
  State Verification:
  
  Evidence:

  Inference:

  Assumptions:

  Conclusion:

  Next Test:
  ```  

---

## Example Investigation Journal

```text
Target:
api.acme.test

Endpoint:
GET /api/users/{id}/profile

Account / Role:
User A / standard user

Starting State:
Fully authenticated

Observation:
The user ID is supplied in the URL path.

Identity:
User A

Object:
User profile 106

Action:
Read

Client-Controlled Inputs:
Path user ID

Expected Security Control:
Object-level authorization

Hypothesis:
The endpoint may retrieve profiles by ID
without verifying ownership.

Baseline Request:
User A requests profile 105.

Baseline Result:
200 OK with User A profile.

Controlled Modification:
Change only 105 to 106.

Expected Result:
Access denied or appropriately restricted.

Observed Result:
200 OK with User B profile.

State Verification:
User B controlled account confirms profile 106
contains the returned information.

Evidence:
Cross-account profile data returned using User A credential.

Inference:
Object-level authorization appears insufficient.

Assumptions:
No claim is made about all users or all profile endpoints.

Conclusion:
Controlled evidence demonstrates unauthorized
cross-account profile access between the two test accounts.

Next Test:
Determine whether write operations enforce
the same ownership boundary.
```

---

## Part 26 — Evidence Classification Challenge

- Classify each statement.

### Statement 1

```text
Changing 105 to 106 returned different user data.
```

- Classification:

  ```text
  Evidence
  ```

  if directly observed.

---

### Statement 2

```text
The endpoint likely lacks object-level authorization.
```

- Classification:

  ```text
  Inference
  ```

based on evidence.

---

### Statement 3

```text
Every user account can be accessed.
```

- Classification:

  ```text
  Assumption
  ```

unless comprehensively demonstrated.

---

### Statement 4

```text
User B's controlled account confirms object 106 belongs to User B.
```

- Classification:

  ```text
  Evidence
  ```

---

## Part 27 — Finding Quality Challenge

- Weak finding:

  ```text
  IDOR found because changing the ID worked.
  ```

- Rewrite it mentally as:

  ```text
  An authenticated request from controlled User A
  successfully retrieved the private profile belonging
  to controlled User B after only the object identifier
  was changed from 105 to 106.

  The server returned HTTP 200 with User B's profile data,
  demonstrating insufficient object-level authorization
  for the tested endpoint.
  ```

- The second version explains:

  ```text  
  Who

  What

  Changed Variable
  
  Expected Boundary

  Observed Behavior

  Verified Impact
  ```

---

## Part 28 — Complete Application Map

- From the lab traffic, build:

  ```text
  shop.acme.test  
  │
  ├── /products
  ├── /login
  ├── /mfa
  ├── /dashboard
  ├── /account
  ├── /checkout
  │   ├── /address
  │   ├── /shipping
  │   ├── /payment
  │   └── /confirm
  ├── /forgot-password
  ├── /reset
  └── /reset-password
  ```

and:

  ```text
  api.acme.test  
  │
  ├── /api/users/{id}/profile
  ├── /api/cart/items
  └── /api/orders/{id}
      └── /cancel
  ```

- This is the beginning of an application attack-surface map.

---

## Part 29 — State Map

- Construct:

  ```text
  Anonymous

  ↓

  Password Validated

  ↓

  MFA Pending
  
  ↓
  
  MFA Completed

  ↓

  Authenticated
  ```  

- Checkout:

  ```text
  Cart Created

  ↓

  Address Added

  ↓
  
  Shipping Selected

  ↓

  Payment Authorized

  ↓

  Order Confirmed
  ```  

- Password reset:

  ```text
  Reset Requested

  ↓
  
  Token Issued
  
  ↓

  Token Validated

  ↓

  Password Changed

  ↓
  
  Token Invalidated
  ```  

- State maps expose security boundaries that isolated endpoint lists may miss.

---

## Part 30 — Final Capstone Questions

- Answer without looking back if possible.

  1. What is the difference between authentication and authorization?
  2. Why is a baseline required before controlled mutation?
  3. Why should one variable be changed at a time?
  4. Why does `200 OK` not prove exploit success?
  5. Why does `500 Internal Server Error` not prove SQL injection?
  6. Why does changing an object ID not automatically prove IDOR?
  7. What is the difference between validation and authorization?
  8. Why are hidden fields client-controlled?
  9. Why is Base64 not encryption?
  10. What is the difference between `Content-Type` and `Content-Encoding`?
  11. What is the purpose of `Secure` on a cookie?
  12. What is the purpose of `HttpOnly`?
  13. What does `SameSite` influence?
  14. Why should logout invalidate server-side session authority?
  15. Why must MFA be enforced server-side?
  16. Why should multi-step workflows be modeled as state machines?
  17. What is a positive control?
  18. What is a negative or comparison control?
  19. What is a source?
  20. What is a sink?
  21. What is the identity-object-action model?
  22. What are HTTP/2 pseudo-headers?
  23. What is HTTP/2 multiplexing?
  24. What is HPACK?
  25. What is QPACK?
  26. What transport does HTTP/3 use?
  27. Why can front-end/backend protocol translation matter?
  28. What is the difference between evidence and inference?
  29. Why must impact be independently verified?
  30. What makes a security finding defensible?

---

## Practical Burp Exercise

- Use only:

  ```text
  A Lab Environment

  A Local Application

  or

  A Target You Are Explicitly Authorized to Test
  ```

- Capture one complete workflow through Burp Proxy.

- Recommended:

  ```text
  Login

  ↓

  Authenticated Page

  ↓

  Profile Update

  ↓

  Logout
  ```

- For each request, document:

  ```text
  Method

  Path

  Parameters
  
  Headers

  Cookies

  Authentication State

  Response Status

  Redirects

  State Changes
  ```

- Then send one appropriate request to Repeater.

- Perform one harmless controlled modification.

- Compare:

  ```text
  Baseline

  vs

  Modified Request
  ```

- Document the result using the Investigation Journal template.

---

## Capstone Deliverable

- Create your own:

  ```text
  HTTP Investigation Journal
  ```

with at least five entries.

- Suggested categories:

  ```text
  1. Authentication

  2. Authorization

  3. Session Management

  4. Input / Encoding

  5. Multi-Step Workflow
  ```  

- Each entry must contain:

  ```text
  Observation

  Hypothesis

  Baseline

  Controlled Test

  Evidence

  Verification

  Conclusion
  ```

- Do not record a vulnerability unless the evidence supports one.

- A valid conclusion may be:

  ```text
  Hypothesis not supported by observed behavior.
  ```

- That is still a successful investigation.

---

## Self-Assessment

- Score yourself:

  ```text
  HTTP Request Analysis           /10

  HTTP Response Analysis          /10

  Headers and Cookies             /10

  Sessions and Authentication     /10
  
  Authorization Reasoning         /10

  Encoding Awareness              /10
  
  Workflow Analysis               /10
  
  HTTP/2 and HTTP/3 Awareness     /10
  
  Controlled Testing              /10

  Evidence-Based Conclusions      /10
  ```  

- Total:

  ```text
  /100
  ```

- Suggested interpretation:

  ```text
  90–100

  Strong HTTP foundation for Burp testing
  ```

  ```text
  75–89

  Good foundation; review weaker areas
  ```

  ```text
  60–74

  Functional understanding but more practice needed
  ```

  ```text
  Below 60

  Repeat key lessons before advanced Burp modules
  ```

- The score is a learning aid, not a certification.

---

## Final HTTP Analysis Workflow

- Memorize this:

  ```text
  Capture

  ↓

  Read

  ↓

  Map

  ↓

  Identify State

  ↓

  Identify Identity

  ↓

  Identify Object
  
  ↓

  Identify Action

  ↓

  Identify Inputs

  ↓
  
  Identify Trust Boundaries
  
  ↓

  Identify Expected Security Control

  ↓  

  Form Hypothesis

  ↓
  
  Establish Baseline

  ↓

  Change One Variable

  ↓

  Compare

  ↓

  Verify State / Impact

  ↓
  
  Reproduce

  ↓
  
  Document Evidence

  ↓
  
  Conclude Only What Evidence Supports
  ```

---

## Module Completion Checklist

```text
[x] HTTP foundations

[x] Requests

[x] Responses

[x] Methods and status codes

[x] Headers

[x] Parameters and body formats

[x] Cookies and sessions

[x] Authentication and authorization

[x] Content types and encoding

[x] Multi-step state

[x] HTTP/2 and HTTP/3 awareness

[x] Security reasoning methodology

[x] Practical HTTP analysis capstone
```

---

## Key Takeaways

* HTTP analysis is the foundation of effective Burp Suite usage.
* Every request should be understood before it is modified.
* Authentication, authorization, validation, and business logic are distinct security concerns.
* Client-controlled values should never be assumed trustworthy.
* Hidden or encoded values are not automatically protected.
* Sessions must be analyzed across their complete lifecycle.
* Multi-step applications should be understood as state machines.
* HTTP status codes are clues, not final conclusions.
* HTTP/2 and HTTP/3 change protocol representation without removing core HTTP security reasoning.
* Controlled comparisons produce stronger evidence than random payload testing.
* Positive and negative controls strengthen conclusions.
* Actual state and impact must be independently verified.
* Evidence, inference, and assumptions must remain separate.
* A professional tester reports only what the evidence supports.

---

## Module Complete

```text
03 – HTTP for Burp Users

█████████████ 13 / 13

COMPLETE
```

- You now have the HTTP foundation required to understand what Burp Suite is actually showing you.

- The next modules build on this foundation by applying these concepts directly to Burp's tools and professional web application testing workflows.
