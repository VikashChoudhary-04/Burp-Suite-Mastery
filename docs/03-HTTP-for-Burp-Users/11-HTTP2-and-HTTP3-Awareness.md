# HTTP/2 and HTTP/3 Awareness

> **Module Progress**

```text
███████████░░ 11 / 13 Lessons
```

- Learning HTTP through messages such as:

  ```http
  GET /account HTTP/1.1
  Host: example.com
  Cookie: session=abc123
  ```

is useful because the request-response model remains fundamental.

- However, modern web applications may communicate using:

  ```text
  HTTP/1.1

  HTTP/2

  HTTP/3
  ```

- These protocols share HTTP semantics but differ significantly in how messages are transported and represented.

- For Burp users, the goal is not to memorize every byte of each protocol.

- The goal is to understand:

  ```text
  What stays conceptually the same?

  What changes at the protocol layer?

  How does Burp represent these differences?

  Where can interpretation differences become security-relevant?
  ```

---

## HTTP Semantics vs Wire Representation

- Across modern HTTP versions, familiar concepts remain:

  ```text
  Methods

  URLs

  Headers

  Status Codes

  Request Bodies

  Response Bodies
  ```  

- For example, the semantic request:

  ```text
  GET /account

  Host / Authority:
  example.com
  ```

- can exist across different HTTP versions.

- But the bytes transmitted over the network are not necessarily represented the same way.

---

## High-Level Evolution

- A simplified progression:

  ```text
  HTTP/1.1

  Text-Oriented Message Representation

  ↓

  HTTP/2

  Binary Framing + Multiplexed Streams

  ↓

  HTTP/3

  HTTP Semantics over QUIC
  ```

- This evolution primarily improves transport efficiency, concurrency, and performance.

---

## HTTP/1.1 Recap

- A familiar HTTP/1.1 request:

  ```http
  GET /products?id=42 HTTP/1.1
  Host: shop.example.com
  Accept: application/json
  Cookie: session=abc123
  ```

- The message is visually text-oriented.

- Important characteristics include:

  ```text
  Request Line

  Header Fields
  
  Blank Line

  Optional Body
  ```  

---

## HTTP/1.1 Connection Behavior

- HTTP/1.1 supports persistent connections.

- Multiple requests may reuse a TCP connection.

- Conceptually:

  ```text
  TCP Connection

  ↓

  Request A

  ↓

  Response A

  ↓

  Request B

  ↓
  
  Response B
  ```

- Concurrency is more limited than the stream-based multiplexing provided by HTTP/2.

---

## Head-of-Line Concerns

- When many resources are needed, waiting for request-response sequencing can affect performance.

- Browsers historically worked around this partly by opening multiple connections.

- HTTP/2 introduced a more efficient multiplexed model.

---

## HTTP/2

- HTTP/2 retains HTTP semantics while changing how messages are represented and transported.

- Key concepts include:

  ```text
  Binary Framing

  Streams
  
  Multiplexing

  Header Compression

  Pseudo-Headers
  ```

---

## Binary Framing

- HTTP/2 does not transmit requests using the same plain-text wire format as HTTP/1.1.

- Instead, communication is divided into binary protocol frames.

- Conceptually:

  ```text
  HTTP Message

  ↓

  HTTP/2 Frames

  ↓

  Connection
  ```  

- This does not mean application data is encrypted merely because the framing is binary.

---

## Binary Does Not Mean Encrypted

- Remember:

  ```text
  Binary Representation

  ≠

  Encryption
  ```  

- HTTP/2 is commonly used over TLS on the public web, but binary framing and cryptographic protection are separate concepts.

---

## Streams

- HTTP/2 introduces streams.

- A stream is a logical sequence of frames associated with a request/response exchange.

- Conceptually:

  ```text
  One Connection

  ├── Stream 1
  │   └── Request / Response  
  │
  ├── Stream 3
  │   └── Request / Response
  │
  └── Stream 5
      └── Request / Response
  ```

- Multiple streams can coexist on the same connection.

---

## Multiplexing

- HTTP/2 can interleave frames belonging to multiple streams.

- Conceptually:

  ```text
  Single Connection

  Request A ───────────────┐

  Request B ────────┐      │

  Request C ────┐   │      │

                ▼   ▼      ▼

  Interleaved HTTP/2 Frames
  ```

- This allows multiple HTTP exchanges to progress concurrently over one connection.

---

## HTTP/1.1 vs HTTP/2 Concept

- Simplified comparison:

  ```text
  HTTP/1.1

  Connection
  │
  ├── Request A → Response A
  ├── Request B → Response B
  └── Request C → Response C
  ```

- versus:

  ```text
  HTTP/2

  Connection
  │
  ├── Stream A ─────┐
  ├── Stream B ─────┼── Multiplexed
  └── Stream C ─────┘
  ```

- This is a conceptual simplification, but it captures the major difference.

---

## Pseudo-Headers

- HTTP/2 represents information traditionally found in the HTTP/1.1 request line and `Host` header using special pseudo-header fields.

- Examples include:

  ```text
  :method

  :scheme

  :authority

  :path
  ```  

- A conceptual HTTP/2 request might contain:

  ```text
  :method: GET
  :scheme: https
  :authority: example.com
  :path: /account
  user-agent: Mozilla/5.0
  cookie: session=abc123
  ```

---

## :method

- Conceptually corresponds to the request method:

  ```text
  :method: GET
  ```

- Comparable HTTP/1.1 concept:

  ```http
  GET / HTTP/1.1
  ```

---

## :path

- Example:

  ```text
  :path: /products?id=42
  ```

- This carries request-target information such as path and query.

- Comparable HTTP/1.1 concept:

  ```http
  GET /products?id=42 HTTP/1.1
  ```

---

## :scheme

- Example:

  ```text
  :scheme: https
  ```

- This identifies the URI scheme associated with the request.

- Examples:

  ```text
  http

  https
  ```

---

## :authority

- Example:

  ```text
  :authority: shop.example.com
  ```

- This carries authority information conceptually related to the destination host information represented by `Host` in HTTP/1.1.

- Do not assume that every HTTP/1.1 header maps perfectly one-to-one into HTTP/2 representation.

---

## HTTP/1.1 to HTTP/2 Conceptual Mapping

- HTTP/1.1:

  ```http
  GET /account?id=105 HTTP/1.1
  Host: example.com
  Cookie: session=abc123
  ```

- Conceptual HTTP/2 representation:

  ```text
  :method: GET
  :scheme: https
  :authority: example.com
  :path: /account?id=105
  cookie: session=abc123
  ```

- The semantics are similar.

- The protocol representation differs.

---

## Pseudo-Header Rules Matter

- Pseudo-headers are not ordinary arbitrary headers.

- They have protocol-specific requirements concerning:

  * Which pseudo-headers are valid.
  * Their ordering relative to normal fields.
  * Which message types may contain them.

- Burp generally abstracts much of this complexity during ordinary manual testing.

---

## Header Names in HTTP/2

- HTTP/2 field names are transmitted in lowercase.

- For example:

  ```text
  content-type

  user-agent

  authorization
  ```

rather than relying on arbitrary capitalization.

- HTTP field-name semantics remain case-insensitive.

---

## Header Compression

- HTTP requests repeatedly send similar headers.

- Examples:

  ```text
  User-Agent

  Cookie

  Accept

  Authorization
  ```

- HTTP/2 uses **HPACK** header compression to reduce repeated header overhead.

- Conceptually:

  ```text
  Repeated Header Information

  ↓

  Compressed Representation

  ↓

  Less Data Transmitted
  ```

---

## HPACK

- For Burp users, the important idea is:

  ```text
  HPACK

  =

  HTTP/2 Header Compression Mechanism
  ```

- You generally do not need to manually encode HPACK while using Burp's normal request-editing interfaces.

- Burp presents headers in a human-readable form.

---

## Compression Does Not Change Header Meaning

- If:

  ```text
  Authorization: Bearer TOKEN
  ```

is compressed at the HTTP/2 protocol layer, the logical header still represents authentication information.

- Protocol compression changes representation.

- It does not change application semantics.

---

## Frames

- HTTP/2 communication uses frame types for different protocol functions.

- Examples include concepts such as:

  ```text
  HEADERS

  DATA

  SETTINGS

  RST_STREAM

  WINDOW_UPDATE
  ```  

- You do not need to memorize every frame to perform normal Burp testing.

- Understand the architecture:

  ```text
  HTTP Message

  ↓

  Frames

  ↓

  Streams

  ↓

  Connection
  ```

---

## HEADERS and DATA Concept

- Simplified:

  ```text
  HEADERS Frame

  ↓
  
  Request / Response Metadata
  ```

  ```text
  DATA Frame

  ↓

  Message Body Data  
  ```

- A single logical HTTP message may involve multiple frames.

---

## Burp Abstracts Protocol Complexity

- When using Burp Repeater, you may see a request represented in a familiar editable format even when HTTP/2 is used.

- Burp's interface allows you to focus on logical HTTP elements:

  ```text
  Method

  Path

  Headers
  
  Body
  ```

rather than manually constructing binary frames.

- This abstraction is useful, but you should still know which protocol is actually being used.

---

## Check the Protocol in Burp

- When analyzing traffic, pay attention to whether the request is using:

  ```text
  HTTP/1.1

  or

  HTTP/2
  ```

- Protocol version can affect:

  * Message framing.
  * Header representation.
  * Connection behavior.
  * Some advanced testing techniques.
  * How intermediaries translate requests.

---

## HTTP/2 and Repeater

- Burp Repeater can work with HTTP/2 requests.

- A useful mental model:

  ```text
  Human-Readable Request in Burp

  ↓

  Burp Constructs Correct Protocol Representation

  ↓

  HTTP/2 Communication With Server
  ```

- You manipulate the semantics.

- Burp handles much of the wire-level framing.

---

## Protocol Conversion

- A request may travel through multiple infrastructure components.

- Example:

  ```text
  Browser

  ↓

  HTTP/2

  ↓

  CDN / Reverse Proxy

  ↓

  HTTP/1.1
  
  ↓
  
  Backend Application
  ```

- or:

  ```text
  Browser

  ↓

  HTTP/2

    ↓

  Gateway

  ↓

  HTTP/2

  ↓

  Backend
  ```

- The protocol used on one connection does not guarantee the same protocol is used everywhere.

---

## Front End vs Back End

- A common architecture:

  ```text
  Client

  ↓

  Front-End Server / CDN / Reverse Proxy

  ↓

  Back-End Application
  ```

- Each component parses HTTP.

- Security becomes interesting when they do not interpret a request identically.

---

## Translation Boundaries

- Suppose:

  ```text
  Client → Front End

  HTTP/2
  ```

- but:

  ```text
  Front End → Back End

  HTTP/1.1
  ```

- The front end must translate the request.

- Conceptually:

  ```text
  HTTP/2 Semantics

  ↓

  Translation

  ↓

  HTTP/1.1 Message
  ```

- Translation introduces another interpretation boundary.

---

## Interpretation Differences

- A general security principle:

  ```text  
  Component A Interprets Request as X

  while

  Component B Interprets Request as Y
  ```

can create security problems.

- This principle appears in advanced topics involving:

  * Request desynchronization.
  * Proxy/backend parsing differences.
  * Protocol translation.
  * Ambiguous message boundaries.

- The important foundation is understanding that multiple parsers may process the same logical traffic.

---

## HTTP Request Smuggling Context

- Traditional HTTP request smuggling often involves disagreements about HTTP/1.1 message boundaries.

- Conceptually:

  ```text
  Front End

  ↓

  Interprets Boundary One Way

  ↓

  Back End

  ↓

  Interprets Boundary Another Way
  ```  

- The result may be request desynchronization.

- Detailed exploitation belongs in an advanced vulnerability module.

---

## HTTP/2 Desynchronization Awareness

- HTTP/2 removes some ambiguity found in HTTP/1.1 because message framing is explicit at the protocol layer.

- However, security issues can still arise around:

  ```text
  HTTP/2 → HTTP/1.1 Downgrading

  Header Translation

  Malformed or Ambiguous Values

  Different Validation Rules  
  ```

- Do not conclude:

  ```text
  HTTP/2

  =

  Request Smuggling Impossible
  ```

- The architecture matters.

---

## Protocol Downgrading

- In this context, "downgrading" can refer to an intermediary receiving HTTP/2 and forwarding the request to a backend using HTTP/1.1.

- Conceptually:

  ```text
  HTTP/2 Request

  ↓

  Proxy Translation

  ↓

  HTTP/1.1 Request
  ```  

- The translation must correctly preserve request meaning.

---

## Why This Matters to Burp Users

- Suppose Burp sends:

  ```text
  HTTP/2
  ```

to a front-end server.

- The backend may still receive:

  ```text
  HTTP/1.1
  ```

- Therefore:

  ```text
  What Burp Sends to the Front End

  ≠

  Necessarily the Exact Wire Representation
  Seen by the Backend
  ```

- This matters during advanced protocol testing.

---

## HTTP/3

- HTTP/3 carries HTTP semantics over **QUIC** rather than TCP.

- High-level model:

  ```text
  HTTP/1.1

  HTTP over TCP
  ```

  ```text
  HTTP/2

  HTTP Binary Framing over TCP
  ```

  ```text
  HTTP/3

  HTTP over QUIC
  ```

- QUIC itself runs over UDP.

---

## HTTP/3 Stack

- Conceptually:

  ```text
  HTTP/3

  ↓

  QUIC

  ↓

  UDP

  ↓
  
  IP
  ```

- This differs from:

  ```text
  HTTP/2

  ↓

  TLS

  ↓

  TCP

  ↓

  IP
  ```

- QUIC integrates transport and cryptographic functionality differently from the traditional TCP + TLS stack.

---

## QUIC

- QUIC provides features including:

  * Secure transport.
  * Stream multiplexing.
  * Faster connection establishment in relevant scenarios.
  * Improved handling of packet loss across independent streams compared with TCP-level multiplexing.

- For Burp users, conceptual awareness is usually more important initially than packet-level QUIC expertise.

---

## HTTP/2 Head-of-Line Limitation

- HTTP/2 multiplexes multiple streams over one TCP connection.

- But TCP delivers a reliable ordered byte stream.

- If packet loss occurs at the TCP layer, delivery can temporarily affect multiple HTTP/2 streams waiting on missing TCP data.

---

## HTTP/3 Improvement

- QUIC provides stream-aware transport behavior.

- Conceptually:

  ```text
  Loss Affecting One Stream

  ↓

  Does Not Necessarily Block Delivery
  of Unrelated Stream Data
  in the Same Way TCP Can
  ```

- This is one motivation behind HTTP/3.

---

## HTTP/3 Pseudo-Headers

- HTTP/3 retains HTTP field semantics including pseudo-header concepts similar to HTTP/2:

  ```text
  :method

  :scheme

  :authority

  :path
  ```  

- The transport and compression mechanisms differ, but the application-level HTTP concepts remain familiar.

---

## QPACK

- HTTP/3 uses **QPACK** for HTTP field compression.

- Simplified:

  ```text
  HTTP/2

  ↓

  HPACK
  ```  

  ```text
  HTTP/3

  ↓

  QPACK
  ```

- You do not need to manually encode either during ordinary Burp testing.

---

## HPACK vs QPACK

- At a high level:

  ```text
  HPACK

  Designed for HTTP/2
  ```

  ```text
  QPACK

  Designed for HTTP/3 / QUIC's stream model
  ```

- Both reduce repeated field overhead, but their designs reflect different transport characteristics.

---

## HTTP/3 Does Not Change Core Security Questions

- Whether the application uses HTTP/1.1, HTTP/2, or HTTP/3, you still ask:

  ```text
  Who is authenticated?

  What is authorized?

  Which inputs are trusted?

  How is state validated?

  How are objects identified?

  What does the server actually do?
  ```

- Protocol knowledge complements application-security reasoning.

- It does not replace it.

---

## ALPN Awareness

- During TLS negotiation, protocols can use **Application-Layer Protocol Negotiation (ALPN)** to agree on application protocols.

- Examples may include identifiers associated with:

  ```text
  HTTP/1.1

  HTTP/2
  ```  

- This helps client and server determine which protocol to use over a connection.

---

## HTTP/3 Discovery

- Browsers and servers may negotiate or discover HTTP/3 availability through mechanisms that can include advertised alternative services.

- The exact connection path may therefore vary depending on:

  * Client support.
  * Server configuration.
  * Network conditions.
  * Proxy behavior.

- Do not assume browser traffic always uses the same HTTP version.

---

## Burp Can Change the Connection Picture

- When a browser communicates through Burp:

  ```text
  Browser

  ↓

  Burp

  ↓

  Server
  ```  

- there are effectively separate communication legs:

  ```text
  Browser ↔ Burp

  and

  Burp ↔ Server
  ```  

- Each leg may have its own:

  * TLS connection.
  * HTTP protocol version.
  * Connection behavior.

- This is a fundamental interception-proxy concept.

---

## Do Not Assume End-to-End Protocol Identity

- For example:

  ```text
  Browser

  ↓

  HTTP/2

  ↓

  Burp

  ↓

  HTTP/1.1
  
  ↓

  Server  
  ```

or another protocol combination may be possible depending on configuration and support.

- Therefore:

  ```text
  Browser Protocol

  ≠

  Automatically Burp-to-Server Protocol
  ```  

- Always inspect the actual testing context.

---

## HTTP/2 Request Example

- Conceptual request:

  ```text
  :method: POST
  :scheme: https
  :authority: api.example.com
  :path: /api/orders
  content-type: application/json
  authorization: Bearer TOKEN

  {"productId":4821}
  ```

- Analyze it just as you would logically analyze HTTP/1.1:

  ```text
  Method:
  POST

  Destination:
  api.example.com

  Path:
  /api/orders

  Authentication:
  Bearer token

  Body Type:
  JSON

  Parameter:
  productId
  ```

- Protocol version does not remove the need for ordinary request analysis.

---

## HTTP/1.1 Equivalent Concept

- A roughly equivalent logical HTTP/1.1 request:

  ```http
  POST /api/orders HTTP/1.1
  Host: api.example.com
  Content-Type: application/json  
  Authorization: Bearer TOKEN

  {"productId":4821}
  ```

- The wire representation differs.

- The application intent is similar.

---

## Connection Reuse Matters

- Modern HTTP clients reuse connections heavily.

- This means multiple requests may share:

  ```text
  TCP Connection

  TLS Session / Connection

  HTTP/2 Connection

  QUIC Connection
  ```  

depending on protocol.

- For some advanced vulnerability classes, connection context matters.

---

## Per-Request vs Per-Connection State

- Most application-security reasoning focuses on request/session state.

- But infrastructure may also maintain connection-level state.

- Conceptually:

  ```text
  Application State

  ↓

  User / Session / Resource
  ```

- versus:

  ```text
  Connection State

  ↓

  Protocol / Transport Context
  ```

- Advanced testing sometimes requires distinguishing these.

---

## Burp Testing Principle

- When something behaves unexpectedly, ask:

  ```text
  Is this application logic?

  Is this HTTP semantics?

  Is this proxy behavior?

  Is this protocol-version behavior?

  Is this connection-specific?

  Is an intermediary translating the request?
  ```  

- This prevents incorrect conclusions.

---

## Protocol Comparison

| Feature                       | HTTP/1.1                                | HTTP/2             | HTTP/3                                 |
| ----------------------------- | --------------------------------------- | ------------------ | -------------------------------------- |
| HTTP semantics                | Yes                                     | Yes                | Yes                                    |
| Text-style wire message model | Yes                                     | No                 | No                                     |
| Binary framing                | No                                      | Yes                | Yes, via HTTP/3 framing over QUIC      |
| Multiplexed streams           | Limited compared with later versions    | Yes                | Yes                                    |
| Primary transport             | TCP                                     | TCP                | QUIC over UDP                          |
| Header/field compression      | No protocol-level equivalent like HPACK | HPACK              | QPACK                                  |
| Pseudo-headers                | No                                      | Yes                | Yes                                    |
| Commonly secured              | HTTPS/TLS                               | Usually TLS on web | QUIC includes cryptographic protection |

- This table is intentionally high-level.

---

## What You Need to Know for Burp

- You should be able to recognize:

  ```text
  HTTP/1.1

  ↓

  Text-Oriented Request-Line / Headers Model
  ```  

  ```text
  HTTP/2

  ↓

  Binary Frames

  Streams

  Multiplexing

  Pseudo-Headers
  
  HPACK
  ```

  ```text
  HTTP/3

  ↓

  QUIC
  
  UDP-Based Transport

  Streams

  QPACK
  ```  

- You do not need to manually construct low-level frames for ordinary web testing.

---

## Common Mistakes

- Avoid:

  ```text
  HTTP/2 is encrypted because it is binary.
  ```

  ```text
  HTTP/2 completely replaces HTTP semantics.
  ```

  ```text
  :authority is just an arbitrary custom header.
  ```

  ```text
  The browser and backend always use the same HTTP version.
  ```

  ```text
  HTTP/2 means request desynchronization issues cannot exist.
  ```

  ```text
  HTTP/3 is simply HTTP/2 over UDP.
  ```

  ```text
  Burp always shows the exact bytes transmitted on the wire.
  ```

  ```text
  Protocol knowledge makes application logic irrelevant.
  ```

- Each statement oversimplifies the architecture.

---

## Protocol Awareness Checklist

```text
[ ] Identify the HTTP version in use.

[ ] Understand the logical request regardless of version.

[ ] Recognize HTTP/2 pseudo-headers.

[ ] Understand that HTTP/2 uses binary framing.

[ ] Understand streams and multiplexing.

[ ] Know that HTTP/2 uses HPACK.

[ ] Know that HTTP/3 operates over QUIC.

[ ] Know that HTTP/3 uses QPACK.

[ ] Distinguish binary representation from encryption.

[ ] Consider front-end/back-end protocol differences.

[ ] Consider protocol translation boundaries.

[ ] Do not assume browser protocol equals backend protocol.

[ ] Remember that Burp abstracts low-level protocol details.

[ ] Keep application-security reasoning central.
```

---

## Key Takeaways

* HTTP semantics remain recognizable across HTTP/1.1, HTTP/2, and HTTP/3.
* HTTP/2 uses binary framing, streams, multiplexing, pseudo-headers, and HPACK.
* Binary framing is not encryption.
* `:method`, `:scheme`, `:authority`, and `:path` represent important HTTP/2 and HTTP/3 request metadata.
* Burp presents protocol traffic in a human-readable form and handles much of the low-level framing.
* Front-end and back-end systems may communicate using different HTTP versions.
* Protocol translation creates interpretation boundaries that matter in advanced security testing.
* HTTP/2 does not eliminate all request desynchronization concerns.
* HTTP/3 carries HTTP semantics over QUIC, which runs over UDP.
* HTTP/3 uses QPACK rather than HTTP/2's HPACK.
* Browser-to-Burp and Burp-to-server connections are separate protocol relationships.
* Always distinguish application behavior, HTTP semantics, intermediary behavior, and transport behavior.
