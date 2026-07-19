# Parameters and Request Bodies

> **Module Progress**

```text id="r6w3mt"
███████░░░░░░ 7 / 13 Lessons
```

- Parameters are values supplied to an application that may influence how a request is processed.

- They can appear in many locations:

  ```text id="p5m8qn"
  URL Query

  Path

  Form Body

  JSON

  XML

  Multipart Data

  Headers

  Cookies
  ```  

- For Burp users, finding a parameter is only the beginning.

- The more important questions are:

  ```text id="v7n2kx"
  Can the client control it?

  ↓

  Does the server consume it?

  ↓

  What does it influence?

  ↓
  
  Is a security decision affected?
  ```

- This distinction prevents random parameter tampering from being mistaken for meaningful security testing.

---

## Parameter Mental Model

- Consider:

  ```http id="q8m3wr"
  POST /api/orders/4821?notify=true HTTP/1.1
  Host: shop.example.com
  Cookie: session=USER_A
  Content-Type: application/json

  {
    "quantity": 2,
    "shipping": "express"  
  }
  ```

- Potential inputs appear in several places:

  ```text id="n4p8mv"
  Path:
  4821

  Query:
  notify=true

  Cookie:
  session=USER_A

  JSON Body:
  quantity=2
  shipping=express
  ```

- Do not look only for:

  ```text id="x6m4qt"
  ?name=value  
  ```

- HTTP input exists throughout the message.

---

## Four Questions for Every Parameter

- For any interesting value, ask:

### 1. Is it present?

```text id="m2q9vr"
Can I identify the value in the request?
```

### 2. Is it client-controllable?

```text id="k7n2pw"
Can the client meaningfully alter what is sent?
```

### 3. Is it consumed?

```text id="r5m7qx"
Does changing it affect server processing?
```

### 4. Is the effect security-relevant?

```text id="t8p3mv"
Does it affect identity, authorization,
business logic, data, or another security boundary?
```

- These are different questions.

---

## Presence Does Not Mean Usage

- Suppose a request contains:

  ```text id="q5n8kr"
  debug=false
  ```

- Changing it to:

  ```text id="m4q2vn"
  debug=true
  ```

may do nothing.

- The parameter might be:

  * Ignored.
  * Legacy.
  * Client-side only.
  * Overridden server-side.
  * Used only in certain conditions.

- Do not infer importance from its name.

---

## Query Parameters

- Example:

  ```http id="x7n4pk"
  GET /search?q=burp&page=2&sort=date HTTP/1.1
  Host: example.com
  ```

- Parameters:

  ```text id="p6m3qw"
  q = burp

  page = 2

  sort = date
  ```

- Common uses include:

  * Search.
  * Filtering.
  * Pagination.
  * Sorting.
  * Object selection.
  * Feature flags.

---

## Security-Sensitive Query Parameters

- Examples might include:

  ```text id="v3n9mr"
  ?id=105

  ?account=105

  ?redirect=/dashboard

  ?file=report.pdf

  ?price=1000
  ```  

- These names suggest possible security relevance.

- But names alone are not proof.

---

## Path Parameters

- Input may appear directly in the path.

- Example:
  
  ```http id="k3m8qx"
  GET /api/users/105/orders/4821 HTTP/1.1
  Host: example.com
  ```

- Possible dynamic values:

  ```text id="r5q2nv"
  105

  4821
  ```

- Potential interpretation:

  ```text id="n8m4pk"
  105 → User Identifier

  4821 → Order Identifier
  ```

- Verify rather than assume.

---

## Form URL-Encoded Bodies

- Traditional HTML forms commonly use:

  ```http id="q2m7vx"
  Content-Type: application/x-www-form-urlencoded
  ```

- Example:

  ```http id="m7p3qw"
  POST /login HTTP/1.1
  Host: example.com
  Content-Type: application/x-www-form-urlencoded

  username=alice&password=example
  ```

- The body resembles a query string:

  ```text id="x4n9kr"
  username = alice

  password = example
  ```

---

## URL Encoding in Form Data

- A value such as:

  ```text id="t6m2pv"
  alice@example.com
  ```

- may appear as:

  ```text id="q7m4nr"
  alice%40example.com
  ```

- A space may be represented as:

  ```text id="p3n8mv"
  +
  ```

or percent-encoded depending on context.

- Understand the encoding before deciding what the application actually receives.

---

## JSON Bodies

- Modern APIs frequently use:

  ```http id="v8m3qx"
  Content-Type: application/json
  ```

- Example:

  ```json id="k5n2pr"  
  {
    "username": "alice",
    "age": 25,
    "active": true
  }
  ```

- JSON supports multiple data types.

---

## JSON Data Types

- Examples:

  ```json id="r2m8vw"
  {
    "string": "alice",
    "number": 25,
    "boolean": true,
    "nullValue": null,
    "array": ["one", "two"],
    "object": {
      "role": "user"
    }  
  }  
  ```

- Changing a value's type may produce different behavior.

- For example:

  ```json id="n6q4mx"  
  {
    "id": 105
  }
  ```

- is not necessarily equivalent to:

  ```json id="m7q4vx"
  {
    "id": "105"
  }
  ```

- Server frameworks may handle type differences differently.

---

## Nested JSON Objects

- Consider:

  ```json id="k4n9pr"  
  {
    "profile": {
      "name": "Alice",
      "address": {
        "city": "Delhi"
      }
    }
  }
  ```

- Parameters exist at multiple levels:

  ```text id="r6w3mt"
  profile.name

  profile.address.city
  ```

- Do not inspect only top-level keys.

---

## JSON Arrays

- Example:

  ```json id="p5m8qn"
  {
    "productIds": [101, 102, 103]
  }
  ```

- Questions include:

  ```text id="v7n2kx"
  Does order matter?

  Are duplicates accepted?

  Are all elements authorized?

  Is each object validated independently?

  What happens with an empty array?
  ```

- Arrays can introduce logic that scalar parameters do not.

---

## Optional JSON Properties

- Suppose a normal request sends:

  ```json id="q8m3wr"  
  {
    "displayName": "Alice"
  }
  ```

- Questions include:

  ```text id="n4p8mv"
  What happens if the field is omitted?

  What happens if it is null?

  What happens if an additional field is supplied?
  ```

- These tests help reveal how the server maps client input to application state.

---

## Missing vs Null vs Empty

- These values are not always equivalent:

  ```json id="x6m4qt"
  {}
  ```

  ```json id="m2q9vr"  
  {
    "name": null
  }
  ```

  ```json id="k7n2pw"
  {
    "name": ""  
  }
  ```

- Conceptually:

  ```text id="r5m7qx"
  Missing

  ≠

  Null

  ≠

  Empty String
  ```

- Applications may handle each differently.

---

## XML Bodies

- Some applications and APIs use XML.

- Example:

  ```http id="t8p3mv"
  POST /api/profile HTTP/1.1
  Host: example.com
  Content-Type: application/xml

  <profile>
      <name>Alice</name>
      <city>Delhi</city>
  </profile>
  ```

- Potential inputs:

  ```text id="q5n8kr"
  profile.name

  profile.city
  ```

- XML introduces its own parsing rules and security considerations.

- Understand the parser context before modifying structure.

---

## Multipart Form Data

- File uploads and complex forms often use:

  ```http id="m4q2vn"
  Content-Type: multipart/form-data; boundary=----Boundary123
  ```

- Simplified example:

  ```http id="x7n4pk"
  ------Boundary123
  Content-Disposition: form-data; name="username"

  alice
  ------Boundary123
  Content-Disposition: form-data; name="avatar"; filename="photo.jpg"
  Content-Type: image/jpeg

  [file data]
  ------Boundary123--
  ```

- Multipart requests contain separate parts.

---

## Multipart Inputs

- A multipart request may contain:

  ```text id="p6m3qw"
  Text Fields

  File Names

  File Content

  Per-Part Content-Type

  Additional Metadata
  ```  

- Do not think of a file upload as only:

  ```text id="v3n9mr"
  The File Bytes
  ```

- The surrounding metadata can also influence processing.

---

## File Name as Input

- Example:

  ```http id="k3m8qx"
  filename="photo.jpg"
  ```
  
- The filename is client-supplied metadata.

- The server should not automatically trust it as:

  * A safe path.
  * A safe extension.
  * Proof of file type.

---

## Content-Type as Input

- A multipart part may claim:

  ```http id="r5q2nv"
  Content-Type: image/jpeg  
  ```

- This is information supplied by the client.

- Conceptually:

  ```text id="n8m4pk"
  Client Claims "JPEG"

  ≠

  Server Has Verified Actual Content
  ```

- Strong validation should not blindly trust client-declared metadata.

---

## Header-Based Parameters

- Applications may consume custom headers:

  ```http id="q2m7vx"
  X-Tenant-ID: 42
  X-Device-ID: abc123
  X-Client-Version: 8
  ```

- These may influence:

  * Tenant selection.
  * Feature behavior.
  * Routing.
  * Client compatibility.

- Ask whether the server trusts them and whether they are independently validated.

---

## Cookie Parameters

- Cookies also contain client-supplied values.

- Example:

  ```http id="m7p3qw"
  Cookie: session=abc123; language=en; currency=INR
  ```

- Potential values:

  ```text id="x4n9kr"
  session = abc123

  language = en

  currency = INR
  ```

- Some may be security-sensitive.

- Others may affect only presentation.

---

## Identity Parameters

- Values related to identity deserve special attention.

- Examples:

  ```text id="t6m2pv"
  userId

  accountId

  owner

  email

  username

  tenantId
  ```  

- Ask:

  ```text id="q7m4nr"
  Does the server derive identity from the authenticated session?

  Or trust a client-supplied identifier?
  ```

- This distinction is central to authorization testing.

---

## Object Identifiers

- Applications often reference resources using:

  ```text id="p3n8mv"
  Numeric IDs

  UUIDs

  Slugs

  File Names

  Order Numbers

  Encoded Tokens
  ```

- Examples:

  ```text id="v8m3qx"
  /users/105

  /orders/ORD-4821

  /files/550e8400-e29b-41d4-a716-446655440000
  ```  

- An identifier being difficult to guess is not the same as proper authorization.

---

## Business Logic Parameters

- Some parameters influence business rules rather than technical routing.

- Examples:

  ```text id="k5n2pr"
  price

  quantity
  
  discount
  
  currency

  role

  status

  shippingMethod
  
  isPremium
  ```

- Ask:

  > Should the client be authoritative for this value?

- For example:

  ```text id="r2m8vw"
  Client Sends Price

  ↓

  Does Server Trust It?

  or

  ↓

  Does Server Recalculate From Trusted Data?
  ```  

---

## Server-Generated vs Client-Supplied Values

- Suppose the browser sends:

  ```json id="n6q4mx"  
  {
    "productId": 4821,
    "price": 1000
  }
  ```

- The presence of `price` raises a question:

  ```text id="m7q4vx"
  Is the server actually using this client-supplied price?

  Or recalculating it server-side?
  ```

- You cannot know from the request alone.

- Test observable behavior.

---

## Duplicate Parameters

- Example:

  ```text id="k4n9pr"
  ?id=105&id=106
  ```

- Different components may interpret this as:

  ```text id="r6w3mt"
  First Value

  Last Value

  Both Values

  Invalid Input
  ```

- This can matter when multiple layers parse the same request differently.

---

## Duplicate JSON Keys

- Technically, JSON object member names should be unique for interoperable behavior, but parsers may still encounter input like:

  ```json id="p5m8qn"
  {
    "role": "user",
    "role": "admin"  
  }
  ```

- Different parsers or components may handle duplicate keys differently.

- Do not assume universal behavior.

---

## Parameter Pollution Concept

- When the same logical parameter appears multiple times or in multiple locations, interpretation can become ambiguous.

- For example:

  ```http id="v7n2kx"
  POST /update?role=user HTTP/1.1
  Content-Type: application/x-www-form-urlencoded

  role=admin
  ```

- Now:

  ```text id="q8m3wr"
  Query Says:
  user

  Body Says:
  admin
  ```

- Which value wins?

  - That depends on application parsing.

---

## Same Parameter in Different Locations

- A value may appear in:

  ```text id="n4p8mv"
  Path

  Query

  Body

  Header

  Cookie
  ```  

- The application may:

  * Prefer one location.
  * Merge values.
  * Use different values in different layers.

- This can create surprising behavior.

---

## Client-Side Validation vs Server-Side Validation

- Suppose a form allows:

  ```text id="x6m4qt"
  Quantity:

  1–10
  ```

- The browser may prevent entering:

  ```text id="m2q9vr"
  1000
  ```

- But the underlying HTTP request can potentially be modified.

- The server must enforce:

  ```text id="k7n2pw"
  Allowed Range

  Business Rules

  Authorization

  Data Integrity
  ```  

- Client-side validation improves user experience.

- It is not a security boundary.

---

## Validation Types

- Server-side validation may include:

  ```text id="r5m7qx"
  Type Validation

  Length Validation

  Format Validation

  Range Validation

  Allowlisting

  Authorization

  Business Rule Validation
  ```  

- These controls solve different problems.

- A value can be syntactically valid but unauthorized.

---

## Example

- Suppose:

  ```json id="t8p3mv"  
  {
    "quantity": 5
  }
  ```

- The value may pass:

  ```text id="q5n8kr"
  Integer Validation

  Range Validation
  ```

- but still violate a business rule such as:

  ```text id="m4q2vn"
  Only 2 Units Available
  ```

- Validation and business logic are related but distinct.

---

## Mass Assignment Concept

- Some frameworks map request properties directly onto server-side objects.

- Normal request:

  ```json id="x7n4pk"
  {
    "displayName": "Alice"  
  }
  ```

- Suppose the underlying object also contains:

  ```text id="p6m3qw"
  role

  isAdmin

  accountStatus
  ```  

- A security question is:

  > Does the server explicitly allow only intended client-editable properties?

- If not, unintended properties may potentially be modified.

- This is commonly associated with **mass assignment** or **property-level authorization** issues.

---

## Do Not Add Random Fields Without Reason

- Better workflow:

  ```text id="v3n9mr"
  Observe API Responses / JavaScript / Related Requests

  ↓

  Identify Likely Object Properties

  ↓

  Understand Their Purpose

  ↓

  Form Hypothesis

  ↓

  Test Carefully
  ```

- This is stronger than blindly injecting hundreds of field names.

---

## Parameter Discovery Sources

- Potential parameters can be discovered from:

  ```text id="k3m8qx"
  Normal Requests

  HTML Forms
  
  JavaScript

  API Responses
  
  Error Messages

  Documentation
  
  Mobile/API Traffic

  Related Endpoints
  ```  

- The application itself often reveals its data model over time.

---

## Parameter Analysis by Security Category

- Classify parameters.

### Identity

```text id="r5q2nv"
userId

accountId

tenantId
```

### Authorization / Role

```text id="n8m4pk"
role

permission

isAdmin
```

### Object Selection

```text id="q2m7vx"
orderId

invoiceId

fileId
```

### Business Logic

```text id="m7p3qw"
price

quantity

discount

status
```

### Navigation

```text id="x4n9kr"
redirect

returnUrl

next
```

### File / Resource

```text id="t6m2pv"
file

path

template

image
```

- This helps prioritize investigation.

---

## One Variable at a Time

- Suppose:

  ```json id="q7m4nr"
  {
    "userId": 105,
    "role": "user",
    "price": 1000
  }
  ```

- Changing all three simultaneously produces ambiguous results.

- Prefer:

  ```text id="p3n8mv"
  Baseline

  ↓

  Change userId

  ↓
  
  Observe

  ↓

  Restore
  
  ↓

  Change role

  ↓

  Observe

  ↓

  Restore
  
  ↓

  Change price

  ↓

  Observe
  ```

- Controlled testing creates stronger evidence.

---

## Parameter Dependency

- Some parameters depend on others.

- Example:

  ```json id="v8m3qx"  
  {
    "country": "IN",
    "currency": "INR"
  }
  ```

- Changing only one may produce an error because the combination becomes invalid.

- Therefore:

  ```text id="k5n2pr"
  One Variable at a Time

  ```

is a strong default, but you must also understand legitimate parameter relationships.

---

## Parameter Removal

- Testing is not only about changing values.

- Ask:

  ```text id="r2m8vw"
  What happens if this parameter is removed?
  ```

- Possible outcomes:

  ```text id="n6q4mx"
  Default Value Applied

  Request Rejected

  Server Derives Value Internally

  Unexpected Behavior

  Parameter Was Never Required
  ```  

- Removal can reveal server-side assumptions.

---

## Empty Values

- Compare:

  ```text id="m7q4vx"
  role=user
  ```  

- with:

  ```text id="k4n9pr"
  role=
  ```  

- and:

  ```text id="r6w3mt"
  Parameter Removed Entirely
  ```

- These may produce different results.

---

## Type Changes

- For JSON:

  ```json id="p5m8qn"  
  {
    "quantity": 5
  }
  ```

- could be compared carefully with:

  ```json id="v7n2kx"
  {
    "quantity": "5"
  }
  ```

- or:

  ```json id="q8m3wr"
  {  
    "quantity": null
  }
  ```

- Different parser and validation paths may be triggered.

---

## Baseline First

- Before modifying parameters:

  ```text id="n4p8mv"
  Capture Normal Request

  ↓

  Understand Every Important Input

  ↓

  Observe Normal Response

  ↓

  Record Expected State

  ↓

  Form Hypothesis

  ↓

  Modify One Relevant Element

  ↓
  
  Compare
  ```

- Without a baseline, unexpected behavior is difficult to interpret.

---

## Example Investigation

- Request:

  ```http id="x6m4qt"
  PATCH /api/users/105 HTTP/1.1
  Host: example.com
  Authorization: Bearer USER_A_TOKEN
  Content-Type: application/json

  {
    "displayName": "Alice",
    "timezone": "UTC"
  }
  ```

- Analyze:

### Path identifier

```text id="m2q9vr"
105
```

- Potential question:

  ```text id="k7n2pw"
  Which user does 105 identify?
  ```

### Authentication

```text id="r5m7qx"
USER_A_TOKEN
```

- Potential question:

  ```text id="t8p3mv"
  Is USER_A authorized to modify user 105?
  ```

### Body fields

```text id="q5n8kr"
displayName

timezone
```

- Potential question:

  ```text id="m4q2vn"
  Which properties is this user allowed to modify?
  ```

- This converts parameter identification into security reasoning.

---

## Parameter Investigation Template

- For important parameters:

  ```text id="x7n4pk"
  Parameter:

  Location:

  Observed Value:

  Data Type:
  
  Client-Controlled?:
  
  Server Consumed?:

  Purpose:

  Security Category:

  Expected Validation:

  Expected Authorization:

  Baseline Behavior:

  Modified Behavior:

  Observable State Change:

  Conclusion:
  ```  

---

## Common Mistakes

- Avoid:

  ```text id="p6m3qw"
  Every parameter is vulnerable.
  ```  

  ```text id="v3n9mr"
  Parameter name proves what it does.
  ```  

  ```text id="k3m8qx"
  Hidden field means trusted field.
  ```

  ```text id="r5q2nv"
  Client-side validation means server-side validation exists.
  ```

  ```text id="n8m4pk"
  UUIDs remove the need for authorization.
  ```

  ```text id="q2m7vx"
  Only query strings and form bodies contain inputs.
  ```

  ```text id="m7p3qw"
  Changing many parameters at once is efficient testing.
  ```

- The goal is understanding, not random mutation.

---

## Parameter Analysis Checklist

- When analyzing request input:

  ```text id="x4n9kr"
  [ ] Identify path parameters.

  [ ] Identify query parameters.

  [ ] Identify body parameters.

  [ ] Identify relevant headers.

  [ ] Identify cookies.

  [ ] Determine the body format.

  [ ] Inspect nested objects and arrays.

  [ ] Identify optional fields.

  [ ] Distinguish missing, null, and empty values.

  [ ] Identify duplicate parameters.

  [ ] Determine which values are client-controlled.

  [ ] Determine which values the server actually consumes.

  [ ] Classify security-sensitive parameters.

  [ ] Identify expected validation.
  
  [ ] Identify expected authorization.

  [ ] Establish a baseline.

  [ ] Modify one relevant variable at a time.
  
  [ ] Verify actual state changes.
  ```

---

## Key Takeaways

* Parameters can appear throughout an HTTP request, not only in query strings.
* Parameter presence, client controllability, server consumption, and security impact are different concepts.
* Paths, queries, forms, JSON, XML, multipart data, headers, and cookies can all contain meaningful input.
* JSON introduces nested objects, arrays, data types, optional properties, and null handling.
* Multipart requests contain both content and client-supplied metadata.
* Client-declared filenames and content types should not automatically be trusted.
* Duplicate parameters and conflicting locations can create ambiguous parsing behavior.
* Client-side validation is not a server-side security boundary.
* Object identifiers require authorization regardless of predictability.
* Business logic values deserve special attention.
* Parameter removal and type changes can reveal server assumptions.
* Mass assignment concerns whether unintended properties can be modified.
* Establish a baseline and test based on hypotheses.
