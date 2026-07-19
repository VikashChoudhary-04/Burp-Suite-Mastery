# Content Types and Encoding

> **Module Progress**

```text id="r7w3mt"
█████████░░░░ 9 / 13 Lessons
```

- HTTP messages carry data in many representations.

- During Burp testing, you may encounter:

  ```text id="p4m8qn"
  URL Encoding

  JSON

  XML

  HTML

  Multipart Form Data

  Base64
  
  HTML Entities
  
  Unicode Escapes

  Compressed Responses
  ```

- These concepts are often confused with one another.

- A useful foundation is to separate:

  ```text id="v6n2kx"
  Data Format

  Encoding

  Character Encoding

  Content Coding / Compression

  Encryption
  ```

- They solve different problems.

---

## The Core Mental Model

- Suppose an application wants to transmit the text:

  ```text id="q9m3wr"
  Hello World
  ```

- That data may be:

  ```text id="n5p8mv"
  Serialized into a format

  ↓

  Character-encoded into bytes

  ↓

  Possibly transformed or compressed

  ↓

  Transmitted through HTTP
  ```  

- The recipient reverses the appropriate transformations.

---

## Encoding Is Not Encryption

- This is one of the most important rules.

  ```text id="x7m4qt"
  Encoding

  =

  Representation Transformation
  ```

  ```text id="m3q9vr"
  Encryption

  =

  Confidentiality Protection Using Cryptography
  ```

- If a value can be trivially decoded without a secret key, do not describe it as encrypted.

---

## Base64 Example

- Original:

  ```text id="k8n2pw"
  admin:password
  ```

- Base64 representation:

  ```text id="r4m7qx"
  YWRtaW46cGFzc3dvcmQ=
  ```

- This may look unreadable at first glance.

- But:

  ```text id="t9p3mv"
  Base64

  ≠

  Encryption
  ```

- Anyone can decode standard Base64.

---

## Content-Type

- The `Content-Type` header describes the media type of the message content.

- Example:

  ```http id="q6n8kr"
  Content-Type: application/json
  ```

- This tells the recipient:

  ```text id="m5q2vn"
  Interpret the Body as JSON
  ```

- Other common examples:

  ```text id="x8n4pk"
  text/html

  text/plain

  application/json

  application/xml

  application/x-www-form-urlencoded

  multipart/form-data
  ```  

---

## Content-Type vs Content-Encoding

- Do not confuse:

  ```http id="p7m3qw"
  Content-Type: application/json
  ```

- with:

  ```http id="v2n9mr"
  Content-Encoding: gzip
  ```

- Conceptually:

  ```text id="k4m8qx"
  Content-Type

  ↓

  What Kind of Content Is This?
  ```

  ```text id="r6q2nv"
  Content-Encoding

  ↓

  What Transformation Was Applied to the Representation?
  ```

- A response can have both.

---

## Example

```http id="n9m4pk"
HTTP/1.1 200 OK
Content-Type: application/json
Content-Encoding: gzip
```

- Meaning:

  ```text id="q3m7vx"
  Underlying Media Type

  ↓

  JSON
  
  Compressed Representation

  ↓
  
  gzip  
  ```

- The body must be decoded/decompressed appropriately before reading the JSON representation.

---

## Serialization

- Serialization converts structured data into a transferable representation.

- For example, an application object:

  ```text id="m8p3qw"
  User

  Name = Alice

  Role = user
  ```

- might be serialized as JSON:

  ```json id="x5n9kr"  
  {
    "name": "Alice",
    "role": "user"
  }
  ```

- or XML:

  ```xml id="t7m2pv"
  <user>
    <name>Alice</name>
    <role>user</role>
  </user>
  ```

- Same conceptual data.

- Different serialization formats.

---

## JSON

- A common API content type:

  ```http id="q8m4nr"
  Content-Type: application/json
  ```

- Example:

  ```json id="p4n8mv"
  {
    "name": "Alice",
    "active": true,
    "age": 25
  }
  ```

- JSON supports:

  ```text id="v9m3qx"
  Strings

  Numbers

  Booleans

  Null

  Arrays

  Objects
  ```

---

## JSON Escaping

- Certain characters inside JSON strings require escaping.

- Example:

  ```json id="k6n2pr"
  {
    "message": "She said \"Hello\""
  }
  ```

- The backslashes are part of JSON representation rules.

- After parsing, the logical value is:

  ```text id="r3m8vw"
  She said "Hello"
  ```

---

## Unicode Escapes in JSON

- JSON may represent characters using Unicode escape sequences.

- Example:

  ```json id="n7q4mx"  
  {
    "letter": "\u0041"
  }
  ```

- `\u0041` represents:

  ```text id="m8q4vx"
  A
  ```

- Different representations can decode to the same logical character.

---

## XML

- Example:

  ```http id="k5n9pr"
  Content-Type: application/xml
  ```

- Body:

  ```xml id="r7w3mt"
  <user>
    <name>Alice</name>
    <role>user</role>
  </user>
  ```

- XML has different parsing rules and capabilities from JSON.

- Do not treat them as interchangeable merely because both represent structured data.

---

## HTML

- A typical page response may use:

  ```http id="p4m8qn"
  Content-Type: text/html
  ```

- Example:

  ```html id="v6n2kx"
  <h1>Hello Alice</h1>
  ```

- The browser interprets this as HTML markup rather than plain text.

---

## Plain Text

- Example:

  ```http id="q9m3wr"
  Content-Type: text/plain
  ```

- Body:

  ```text id="n5p8mv"
  Hello Alice
  ```

- The intended interpretation differs from HTML even if the characters look similar.

---

## MIME Type Matters

- Compare:

  ```http id="x7m4qt"
  Content-Type: text/html
  ```

- with:

  ```http id="m3q9vr"
  Content-Type: text/plain
  ```

- The same bytes may be interpreted differently by a browser depending on:

  * Content type.
  * Browser behavior.
  * Security headers.
  * Context.

- Representation context matters.

---

## URL Percent-Encoding

- URLs use percent-encoding to represent certain bytes.

- General form:

  ```text id="k8n2pw"
  %HH
  ```

- where `HH` is hexadecimal.

- Examples:

  ```text id="r4m7qx"
  Space → %20

  @ → %40

  / → %2F

  ? → %3F
  ```  

---

## Example

- Original:

  ```text id="t9p3mv"
  alice@example.com
  ```

- Encoded:

  ```text id="q6n8kr"
  alice%40example.com
  ```

- The representation changed.

- The logical value after decoding may remain:

  ```text id="m5q2vn"
  alice@example.com
  ```

---

## Form URL Encoding

- For:

  ```http id="x8n4pk"
  Content-Type: application/x-www-form-urlencoded
  ```

- form data may look like:

  ```text id="p7m3qw"
  name=Alice+Smith&email=alice%40example.com
  ```

- Here, a space may be represented as:

  ```text id="v2n9mr"  
  +
  ```

- depending on form encoding rules.

---

## URL Encoding Is Context-Sensitive

- Do not blindly encode an entire request.

- Different URL components have different syntax roles.

- For example:

  ```text id="k4m8qx"
  Path

  Query

  Parameter Name

  Parameter Value
  ```

may require different handling depending on context.

- Burp's Decoder and Inspector features can help understand transformations, but you should still know why the encoding exists.

---

## Double Encoding

- Suppose:

  ```text id="r6q2nv"
  /
  ```

- becomes:

  ```text id="n9m4pk"
  %2F
  ```

- If the percent character itself is encoded:

  ```text id="q3m7vx"
  %252F
  ```

- A decoding sequence might be:

  ```text id="m8p3qw"
  %252F

  ↓

  %2F

  ↓

  /
  ```

depending on how many decoding layers process the value.

---

## Why Multiple Decoding Layers Matter

- Architecture may involve:

  ```text id="x5n9kr"
  Client

  ↓

  Proxy / Gateway
  
  ↓  

  Framework

  ↓

  Application
  ```

- Different layers may decode values at different stages.

- A security question can arise when:

  ```text id="t7m2pv"
  Layer A Interprets One Representation

  while

  Layer B Interprets Another
  ```

- Do not assume one decoding step universally describes server behavior.

---

## Base64

- Base64 represents binary data using printable ASCII characters.

- Common visual characteristics include:

  ```text id="q8m4nr"
  A-Z

  a-z

  0-9

  +

  /
  
  =
  ```

- Variants such as Base64URL use different characters for URL-safe contexts.

---

## Base64URL

- Base64URL commonly replaces:

  ```text id="p4n8mv"
  + with -

  / with _
  ```

- Padding may also be omitted.

- JWT components commonly use Base64URL-style encoding.

- Again:

  ```text id="v9m3qx"
  Base64URL

  ≠

  Encryption
  ```

---

## Base64 Detection Is Not Proof

- A string ending in:

  ```text id="k6n2pr"  
  =
  ```

may look like Base64.

- But do not assume based solely on appearance.

- Try decoding and inspect whether the result is meaningful.

---

## Basic Authentication Example

- HTTP Basic authentication may use:

  ```http id="r3m8vw"
  Authorization: Basic YWxpY2U6cGFzc3dvcmQ=
  ```

- The encoded portion may represent:

  ```text id="n7q4mx"
  alice:password
  ```

- Base64 does not protect these credentials from anyone who can read the HTTP message.

- Transport security such as HTTPS is essential.

---

## HTML Entity Encoding

- HTML represents some characters using entities.

- Examples:

  ```text id="m8q4vx"
  <  → &lt;

  >  → &gt;

  &  → &amp;

  "  → &quot;
  ```

- Example:

  ```html id="k5n9pr"
  &lt;script&gt;
  ```

- may render as:

  ```text id="r7w3mt"
  <script>
  ```

depending on the HTML context.

---

## Encoding Depends on Output Context

- The correct representation depends on where data is used.

- Examples:

  ```text id="p4m8qn"
  HTML Text

  HTML Attribute

  JavaScript String

  URL

  CSS
  ```

- Each context has different parsing rules.

- This becomes important when studying injection vulnerabilities.

---

## Character Encoding

- Text must be represented as bytes for transmission.

- A common character encoding is:

  ```text id="v6n2kx"
  UTF-8
  ```

- A content type may specify a charset:

  ```http id="q9m3wr"
  Content-Type: text/html; charset=UTF-8
  ```

- This tells the recipient how text bytes should be interpreted as characters.

---

## Characters vs Bytes

- Conceptually:

  ```text id="n5p8mv"
  Character

  ↓

  Character Encoding

  ↓

  Bytes
  ```

- For ASCII characters, this often feels invisible.

- For multilingual text and special characters, encoding becomes more obvious.

---

## Unicode

- Unicode provides a standard system for representing characters across many writing systems.

- UTF-8 is one way to encode Unicode characters into bytes.

- Do not confuse:

  ```text id="x7m4qt"
  Unicode

  with

  UTF-8
  ```  

- Conceptually:

  ```text id="m3q9vr"
  Unicode

  ↓

  Character Repertoire / Code Points
  ```

  ```text id="k8n2pw"
  UTF-8

  ↓

  Encoding of Unicode Into Bytes
  ```

---

## Multipart Form Data

- Multipart requests divide content into parts using a boundary.

- Header:

  ```http id="r4m7qx"
  Content-Type: multipart/form-data; boundary=----Boundary123
  ```

- Body:

  ```http id="t9p3mv"
  ------Boundary123
  Content-Disposition: form-data; name="username"

  alice
  ------Boundary123
  Content-Disposition: form-data; name="avatar"; filename="photo.jpg"
  Content-Type: image/jpeg

  [file bytes]
  ------Boundary123--
  ```

---

## Boundary

- The boundary separates individual parts.

- Conceptually:

  ```text id="q6n8kr"
  Boundary

  Part 1

  Boundary

  Part 2

  Boundary
  ```

- The declared boundary and body formatting must correspond correctly for reliable parsing.

---

## Each Multipart Part Has Context

- A part may have headers such as:

  ```http id="m5q2vn"
  Content-Disposition: form-data; name="avatar"; filename="photo.jpg"
  Content-Type: image/jpeg
  ```

- These describe the individual part.

- The overall request also has its own headers.

- Do not confuse:

  ```text id="x8n4pk"
  Request-Level Content-Type
  ```

- with:

  ```text id="p7m3qw"
  Part-Level Content-Type
  ```

---

## Content-Encoding

- A response may be compressed:

  ```http id="v2n9mr"
  Content-Encoding: gzip
  ```

- Common content codings include:

  ```text id="k4m8qx"
  gzip

  br

  deflate
  ```

- Compression reduces transferred size.

- It does not provide confidentiality.

---

## Compression Is Not Encryption

```text id="r6q2nv"
gzip

≠

Encryption
```

- Compressed data may look unreadable as raw bytes.

- That does not mean it is cryptographically protected.

---

## Accept-Encoding

- A client may advertise:

  ```http id="n9m4pk"
  Accept-Encoding: gzip, deflate, br
  ```

- Conceptually:

  ```text id="q3m7vx"
  Client

  ↓

  "I Can Decode These Content Codings"

  ↓

  Server
  ```  

- The server may choose one and indicate it using `Content-Encoding`.

---

## Transfer-Encoding Is Different

- Do not confuse:

  ```http id="m8p3qw"
  Content-Encoding: gzip
  ```

- with:

  ```http id="x5n9kr"
  Transfer-Encoding: chunked
  ```

- Conceptually:

  ```text id="t7m2pv"
  Content-Encoding

  ↓

  Transforms the Representation
  ```

  ```text id="q8m4nr"
  Transfer-Encoding

  ↓

  Relates to Transfer / Message Framing in HTTP/1.1 Contexts
  ```

- They solve different problems.

---

## Chunked Transfer Concept

- A chunked HTTP/1.1 body is transferred in chunks rather than being framed by a single `Content-Length`.

- Simplified conceptual representation:

  ```text id="p4n8mv"
  Chunk Size

  Chunk Data

  Chunk Size

  Chunk Data

  Final Zero-Length Chunk
  ```

- Burp may abstract some of these details depending on the tool and protocol context.

---

## Encryption and HTTPS

- HTTPS uses TLS to protect HTTP communication in transit.

- Conceptually:

  ```text id="v9m3qx"
  HTTP Request

  ↓

  TLS Protection

  ↓

  Network

  ↓

  TLS Processing

  ↓

  HTTP Request
  ```

- Burp can inspect HTTPS traffic when configured as an authorized interception proxy because it participates in the TLS connections between client and server.

---

## Representation Layers

- A useful model:

  ```text id="k6n2pr"
  Application Data

  ↓

  Serialization
  (JSON / XML / Form Data)

  ↓

  Character Encoding
  (UTF-8)

  ↓

  Optional Content Coding
  (gzip / br)

  ↓

  HTTP Message

  ↓
  
  TLS Protection
  (HTTPS)

  ↓

  Network  
  ```

- Not every request uses every layer, but the model helps separate concepts.

---

## Decoding Workflow in Burp

- When you encounter an unfamiliar value:

  ```text id="r3m8vw"
  1. Identify Context

  ↓

  2. Identify Likely Representation

  ↓

  3. Decode One Layer

  ↓
  
  4. Inspect Result

  ↓

  5. Determine Whether Another Layer Exists

  ↓

  6. Understand Meaning Before Modification
  ```

- Do not repeatedly decode random strings until something vaguely readable appears.

---

## Example Layered Value

- Suppose a parameter contains:

  ```text id="n7q4mx"
  WVdSdGFXND0=
  ```

- First Base64 decode:

  ```text id="m8q4vx"
  YWRtaW4=
  ```

- Second Base64 decode:

  ```text id="k5n9pr"
  admin
  ```

- This demonstrates layered encoding.

- It does not imply encryption.

---

## Encoded Identifiers

- Suppose:

  ```text id="r7w3mt"
  /account?id=MTA1
  ```

- If:

  ```text id="p4m8qn"
  MTA1
  ```

- decodes from Base64 to:

  ```text id="v6n2kx"
  105
  ```

the identifier may simply be encoded.

- Do not conclude:

  ```text id="q9m3wr"
  Encoded ID

  =

  Secure ID
  ```

- Authorization must still be enforced.

---

## Encoding Does Not Create Authorization

- This principle is critical:

  ```text id="n5p8mv"
  105

  ↓

  Base64

  ↓

  MTA1
  ```

does not transform an object identifier into an access-control mechanism.

- Obfuscation and authorization are different concepts.

---

## Content-Type Mismatch

- Suppose:

  ```http id="x7m4qt"
  Content-Type: application/json
  ```

- but the body contains:

  ```text id="m3q9vr"
  username=alice&role=user
  ```

- Possible outcomes:

  ```text id="k8n2pw"
  Rejected

  Parsed Unexpectedly

  Ignored

  Handled by Lenient Parser
  ```

- Different frameworks behave differently.

- Content-type handling can reveal parser assumptions.

---

## Parser Differences

- In complex applications:

  ```text id="r4m7qx"
  Proxy

  ↓

  Web Server
  
  ↓  

  Framework

  ↓  

  Application
  ```  

different layers may interpret representations differently.

- Security issues can arise when:

  ```text id="t9p3mv"
  Security Layer Sees A

  Application Layer Sees B
  ```

- This general principle appears in several advanced vulnerability classes.

---

## Common Confusions

- Avoid:

  ```text id="q6n8kr"
  Base64 = Encryption
  ```

  ```text id="m5q2vn"
  URL Encoding = Encryption
  ```

  ```text id="x8n4pk"
  gzip = Encryption
  ```

  ```text id="p7m3qw"
  Content-Type = Character Encoding
  ```

  ```text id="v2n9mr"
  Content-Encoding = Content-Type
  ```

  ```text id="k4m8qx"
  Unicode = UTF-8
  ```

  ```text id="r6q2nv"
  Encoded Identifier = Authorized Identifier
  ```

- Each concept has a different role.

---

## Content and Encoding Analysis Checklist

```text id="n9m4pk"
[ ] Identify Content-Type.

[ ] Identify body serialization format.

[ ] Identify charset if relevant.

[ ] Look for URL percent-encoding.

[ ] Look for Base64/Base64URL where evidence suggests it.

[ ] Recognize HTML entities and escaping.

[ ] Identify nested/layered encodings.

[ ] Check Content-Encoding.

[ ] Distinguish compression from encryption.

[ ] Distinguish Content-Encoding from Transfer-Encoding.

[ ] Understand multipart boundaries and per-part headers.

[ ] Determine how many decoding layers exist.

[ ] Understand the logical value after decoding.

[ ] Never treat encoding as an access-control mechanism.
```

---

## Key Takeaways

* Content type, serialization, encoding, compression, character encoding, and encryption are different concepts.
* `Content-Type` describes the media type of message content.
* JSON, XML, form data, HTML, and multipart are different representations with different parsers.
* URL percent-encoding represents bytes using `%HH`.
* Base64 and Base64URL are encodings, not encryption.
* HTML entities and JSON escapes are context-specific representations.
* Unicode and UTF-8 are related but not identical concepts.
* `Content-Encoding` may describe compression such as gzip or Brotli.
* `Transfer-Encoding` serves a different HTTP role from `Content-Encoding`.
* Multiple application layers may decode or parse values differently.
* Layered encoding should be decoded systematically.
* Encoding an identifier does not provide authorization.
* Always determine what logical value the server ultimately processes.
