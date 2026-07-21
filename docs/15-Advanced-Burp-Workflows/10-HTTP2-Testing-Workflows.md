# HTTP/2 Testing Workflows

> **Module 15 — Advanced Burp Workflows**

> **Progress**

```text
███████████░░░░ 11 / 15 Files
```

---

## Overview

* Modern web applications increasingly use **HTTP/2** instead of HTTP/1.1.

* HTTP/2 was designed to improve web performance by allowing more efficient communication between clients and servers.

* Important HTTP/2 features include:

  * Binary framing.
  * Multiplexing.
  * Header compression.
  * Streams.
  * More efficient connection reuse.
  * Concurrent request processing.

* From a penetration testing perspective, HTTP/2 is not simply:

  ```text
  HTTP/1.1

  but faster
  ```

* Differences in protocol parsing, request boundaries, header handling, and translation between HTTP versions can create security-relevant behavior.

* Applications may also contain mixed protocol chains:

  ```text
  Browser

  ↓ HTTP/2

  Front-End Proxy

  ↓ HTTP/1.1

  Back-End Server
  ```

* Or:

  ```text
  Client

  ↓ HTTP/2

  CDN

  ↓ HTTP/2

  Reverse Proxy

  ↓ HTTP/1.1

  Application
  ```

* These protocol boundaries matter because different components may interpret the same request differently.

* A professional HTTP/2 testing workflow therefore requires:

  ```text
  Identify Protocol

  +

  Understand Request Representation

  +

  Establish Baseline

  +

  Inspect Headers and Pseudo-Headers

  +

  Analyze Front-End / Back-End Behavior

  +

  Test Controlled Variations

  +

  Compare Responses

  +

  Validate Security Impact
  ```

---

## Learning Objectives

* By the end of this lesson, you will be able to:

  * Explain the major differences between HTTP/1.1 and HTTP/2.
  * Identify whether a target is using HTTP/2.
  * Understand HTTP/2 streams and multiplexing.
  * Understand HTTP/2 pseudo-headers.
  * Recognize important protocol translation boundaries.
  * Use Burp Suite to inspect and manipulate HTTP/2 requests.
  * Compare HTTP/1.1 and HTTP/2 behavior.
  * Investigate header normalization differences.
  * Understand why HTTP/2 can affect request smuggling research.
  * Recognize HTTP/2 downgrade scenarios.
  * Test ambiguous request handling safely.
  * Investigate duplicate and unusual headers.
  * Analyze routing and authority behavior.
  * Understand HTTP/2-specific request splitting concepts.
  * Build a structured HTTP/2 testing methodology.
  * Preserve reliable evidence for protocol-level findings.

---

## What Is HTTP/2?

* HTTP/2 is a major revision of the HTTP protocol designed to improve performance and efficiency.

* HTTP/1.1 commonly represents requests as text:

  ```http
  GET /account HTTP/1.1
  Host: example.test
  Cookie: session=abc123
  ```

* HTTP/2 uses a binary framing layer.

* Conceptually:

  ```text
  HTTP Semantics

  ↓

  Binary Frames

  ↓

  HTTP/2 Connection
  ```

* The meaning of familiar concepts such as:

  ```text
  Method

  Path

  Headers

  Body
  ```

* Still exists, but their wire representation differs.

---

## HTTP/1.1 vs HTTP/2

| Characteristic         | HTTP/1.1                   | HTTP/2                             |
| ---------------------- | -------------------------- | ---------------------------------- |
| Wire format            | Text-based                 | Binary framing                     |
| Request identification | Request sequence           | Streams                            |
| Multiplexing           | Limited                    | Native                             |
| Header compression     | No standard equivalent     | HPACK                              |
| Request line           | Explicit                   | Replaced by pseudo-headers         |
| Host routing           | `Host` header              | Primarily `:authority`             |
| Concurrent requests    | Often multiple connections | Multiple streams on one connection |
| Protocol parsing       | Text delimiters            | Frames and stream semantics        |

---

## Why HTTP/2 Matters for Security Testing

* Security behavior may differ because:

  ```text
  Client Speaks HTTP/2

  ↓

  Front-End Interprets Request

  ↓

  Request May Be Normalized or Downgraded

  ↓

  Back-End Interprets Result
  ```

* Potential security issues may arise when components disagree about:

  ```text
  Request Boundaries

  Header Meaning

  Header Duplication

  Request Length

  Routing

  Authority

  Path

  Protocol Translation
  ```

* The key concept is:

  ```text
  Interpretation Differences

  =

  Potential Security Boundary
  ```

---

## HTTP/2 Connections

* HTTP/2 allows multiple requests and responses to share one connection.

* HTTP/1.1 simplified model:

  ```text
  Connection

  Request A

  ↓

  Response A

  ↓

  Request B

  ↓

  Response B
  ```

* HTTP/2:

  ```text
  One Connection

  ├── Stream 1 → Request A
  ├── Stream 3 → Request B
  ├── Stream 5 → Request C
  └── Stream 7 → Request D
  ```

* Responses can progress concurrently.

---

## Streams

* Each HTTP/2 request-response exchange occurs within a logical **stream**.

* Conceptually:

  ```text
  HTTP/2 Connection

  ├── Stream 1
  │   ├── Request
  │   └── Response
  │
  ├── Stream 3
  │   ├── Request
  │   └── Response
  │
  └── Stream 5
      ├── Request
      └── Response
  ```

* Streams allow independent exchanges over the same connection.

---

## Multiplexing

* Multiplexing allows several requests to be active simultaneously.

* Instead of:

  ```text
  Request A

  Wait

  Response A

  Request B

  Wait

  Response B
  ```

* HTTP/2 can support:

  ```text
  Request A ─────┐

  Request B ─────┼──► Same Connection

  Request C ─────┘
  ```

* This can affect:

  ```text
  Timing Analysis

  Race Condition Testing

  Request Ordering Assumptions

  Traffic Reconstruction
  ```

---

## HTTP/2 Frames

* HTTP/2 communication is divided into frames.

* Common frame types include:

  ```text
  HEADERS

  DATA

  SETTINGS

  WINDOW_UPDATE

  RST_STREAM

  GOAWAY
  ```

* During most Burp-based application testing, you work with the reconstructed HTTP request rather than manually manipulating raw frames.

* However, understanding frames explains why HTTP/2 behaves differently from HTTP/1.1.

---

## HEADERS Frames

* Request metadata is transmitted through header-related frames.

* These represent values such as:

  ```text
  Method

  Scheme

  Authority

  Path

  Normal Headers
  ```

---

## DATA Frames

* Request or response bodies may be transmitted using:

  ```text
  DATA Frames
  ```

* Conceptually:

  ```text
  HEADERS

  ↓

  DATA

  ↓

  END_STREAM
  ```

* The exact framing may be abstracted by Burp.

---

## HTTP/2 Pseudo-Headers

* HTTP/2 replaces parts of the traditional HTTP/1.1 request line with special pseudo-headers.

* Common request pseudo-headers include:

  ```text
  :method

  :scheme

  :authority

  :path
  ```

* Example conceptual request:

  ```text
  :method: GET

  :scheme: https

  :authority: example.test

  :path: /account
  ```

---

## `:method`

* Represents the HTTP method.

* Example:

  ```text
  :method: GET
  ```

* Equivalent HTTP/1.1 concept:

  ```http
  GET / HTTP/1.1
  ```

---

## `:scheme`

* Represents the URI scheme.

* Example:

  ```text
  :scheme: https
  ```

* Common values:

  ```text
  http

  https
  ```

---

## `:authority`

* Represents authority information used for routing.

* Example:

  ```text
  :authority: example.test
  ```

* It broadly corresponds to the role traditionally associated with:

  ```http
  Host: example.test
  ```

* But protocol translation and application behavior can make differences security-relevant.

---

## `:path`

* Represents the request path and query string.

* Example:

  ```text
  :path: /account?id=42
  ```

* Equivalent conceptual HTTP/1.1 request target:

  ```http
  GET /account?id=42 HTTP/1.1
  ```

---

## Why Pseudo-Headers Matter

* Security controls may depend on:

  ```text
  Method

  Host / Authority

  Path

  Scheme
  ```

* If different infrastructure components interpret these values differently, unexpected routing or access-control behavior may occur.

* Always compare:

  ```text
  What Burp Sends

  ↓

  What Front-End Interprets

  ↓

  What Back-End Receives
  ```

---

## Identifying HTTP/2 in Burp

* When browsing an application through Burp, inspect request details for protocol information.

* Depending on Burp version and interface, HTTP/2 usage may be visible through request metadata or protocol indicators.

* You may also observe behavior such as:

  ```text
  HTTP/2 Requests

  Protocol Selection

  ALPN Negotiation

  HTTP/2-Specific Request Representation
  ```

* Do not assume every HTTPS application uses HTTP/2.

---

## ALPN

* **Application-Layer Protocol Negotiation (ALPN)** is commonly used during TLS negotiation to select the application protocol.

* Examples:

  ```text
  h2

  http/1.1
  ```

* Conceptually:

  ```text
  TLS Connection

  ↓

  ALPN Negotiation

  ↓

  h2 Selected

  ↓

  HTTP/2 Communication
  ```

---

## Protocol Detection Workflow

* Use:

  ```text
  Browse Target

  ↓

  Capture Request

  ↓

  Identify Negotiated Protocol

  ↓

  Record HTTP/1.1 or HTTP/2

  ↓

  Compare Important Endpoints
  ```

* Different endpoints or infrastructure paths may behave differently.

---

## Establish a Baseline

* Before modifying HTTP/2 behavior, capture a normal request.

* Record:

  ```text
  Method:

  Scheme:

  Authority:

  Path:

  Headers:

  Cookies:

  Body:

  Response Status:

  Response Length:

  Authentication State:

  Protocol:
  ```

* Send the request without changes.

* Confirm expected behavior.

---

## Baseline Principle

* Use:

  ```text
  Known-Good HTTP/2 Request

  ↓

  One Controlled Change

  ↓

  Response Comparison

  ↓

  Security Interpretation
  ```

* Avoid introducing multiple protocol anomalies simultaneously.

---

## Burp Repeater and HTTP/2

* Repeater is useful for controlled HTTP/2 testing.

* Workflow:

  ```text
  Capture Request

  ↓

  Send to Repeater

  ↓

  Confirm Baseline

  ↓

  Verify Protocol

  ↓

  Modify One Element

  ↓

  Send

  ↓

  Compare Response
  ```

* Depending on the target and Burp configuration, Repeater can help compare HTTP/1.1 and HTTP/2 behavior.

---

## Protocol Comparison

* A useful test is:

  ```text
  Same Logical Request

  ↓

  HTTP/2

  vs

  HTTP/1.1
  ```

* Compare:

  ```text
  Status

  Response Body

  Response Length

  Headers

  Redirects

  Authentication

  Routing

  Error Messages

  Timing
  ```

* Differences may indicate protocol-dependent processing.

---

## Why Protocol Differences Matter

* Example:

  ```text
  HTTP/1.1:

  403 Forbidden
  ```

* HTTP/2:

  ```text
  200 OK
  ```

* This does not automatically prove a vulnerability.

* Investigate:

  ```text
  Did Routing Change?

  Did Authentication Change?

  Were Headers Normalized Differently?

  Did Different Infrastructure Handle the Request?

  Was Cache Behavior Different?
  ```

---

## HTTP/2 Downgrading

* Many architectures accept HTTP/2 at the front end but communicate with back-end servers using HTTP/1.1.

* Example:

  ```text
  Client

  ↓ HTTP/2

  Reverse Proxy

  ↓ HTTP/1.1

  Application Server
  ```

* This process is often called:

  ```text
  HTTP/2 Downgrading
  ```

---

## Why Downgrading Matters

* The front end must translate:

  ```text
  HTTP/2 Representation

  ↓

  HTTP/1.1 Representation
  ```

* During translation, it may need to construct:

  ```text
  Request Line

  Host Header

  Content-Length

  Transfer-Encoding Behavior

  Header Formatting
  ```

* Security issues can arise if front-end and back-end components disagree about the translated request.

---

## Protocol Translation Boundary

* Think of the architecture as:

  ```text
  HTTP/2 Semantics

  ↓

  Translation Layer

  ↓

  HTTP/1.1 Semantics
  ```

* The translation layer becomes a security-relevant boundary.

* Ask:

  ```text
  What values are normalized?

  Which headers are generated?

  Which headers are removed?

  How is body length represented?

  How are duplicate headers handled?

  How is authority converted?
  ```

---

## HTTP Request Smuggling Context

* HTTP request smuggling generally involves disagreement between systems about where one request ends and another begins.

* Simplified model:

  ```text
  Front-End Interpretation

  ≠

  Back-End Interpretation
  ```

* Traditional HTTP/1.1 request smuggling often involves:

  ```text
  Content-Length

  Transfer-Encoding
  ```

* HTTP/2 changes the framing model but does not eliminate all desynchronization risks.

---

## HTTP/2 Request Smuggling Concept

* A possible architecture:

  ```text
  Client

  ↓ HTTP/2

  Front-End

  ↓ Downgraded HTTP/1.1

  Back-End
  ```

* If translation produces an ambiguous HTTP/1.1 request:

  ```text
  Front-End Sees:

  One Request
  ```

* While:

  ```text
  Back-End Sees:

  Different Request Boundaries
  ```

* Desynchronization may occur.

---

## H2.CL Concept

* One HTTP/2-related desynchronization class is commonly discussed as:

  ```text
  H2.CL
  ```

* Conceptually:

  ```text
  HTTP/2 Front-End

  ↓

  Downgrade

  ↓

  HTTP/1.1 Back-End Uses Content-Length

  ↓

  Length Interpretation Mismatch
  ```

* The security issue depends on the specific infrastructure and translation behavior.

---

## H2.TE Concept

* Another conceptual class is:

  ```text
  H2.TE
  ```

* It involves HTTP/2 requests interacting with HTTP/1.1-style:

  ```text
  Transfer-Encoding
  ```

* During downgrade.

* HTTP/2 itself does not use `Transfer-Encoding: chunked` in the same way as HTTP/1.1.

* Therefore, unusual acceptance or forwarding of transfer-related headers may become relevant at protocol translation boundaries.

---

## Safe Desynchronization Testing

* Request smuggling and desynchronization testing can interfere with other users or shared connections.

* Only perform such testing:

  ```text
  In Dedicated Labs

  or

  With Explicit Authorization and Safe Methodology
  ```

* Avoid uncontrolled testing against production systems.

* Prefer:

  ```text
  Non-Destructive Detection

  +

  Isolated Connections

  +

  Minimal Requests

  +

  Clear Scope
  ```

---

## Content-Length Investigation

* In HTTP/2, message framing does not fundamentally depend on `Content-Length` in the same way as HTTP/1.1.

* However, `Content-Length` may still appear as metadata.

* During downgrade:

  ```text
  HTTP/2 Body

  ↓

  Front-End Translation

  ↓

  HTTP/1.1 Content-Length
  ```

* Investigate whether inconsistent length information is:

  ```text
  Rejected

  Normalized

  Recalculated

  Forwarded
  ```

* Do not assume behavior without testing.

---

## Transfer-Encoding Investigation

* HTTP/2 does not use HTTP/1.1 chunked transfer coding.

* Therefore:

  ```text
  Transfer-Encoding: chunked
  ```

* Is not normal HTTP/2 framing behavior.

* Security testing may investigate how infrastructure handles unexpected transfer-related headers.

* Possible behavior:

  ```text
  Reject

  Strip

  Normalize

  Forward During Downgrade
  ```

---

## Header Normalization

* Infrastructure may normalize headers during processing.

* Examples:

  ```text
  Header Names

  Header Values

  Whitespace

  Duplicate Headers

  Invalid Characters

  Authority / Host Mapping
  ```

* Differences between layers can affect:

  ```text
  Routing

  Authentication

  Caching

  Access Control

  Request Parsing
  ```

---

## Duplicate Headers

* Duplicate headers can be interpreted differently by different systems.

* Conceptual example:

  ```text
  X-Role: user

  X-Role: admin
  ```

* Possible interpretations:

  ```text
  First Value

  Last Value

  Combined Value

  Reject Request
  ```

* If different layers choose differently, security controls may diverge.

---

## Duplicate Header Testing Workflow

* In an authorized lab:

  ```text
  Capture Baseline

  ↓

  Identify Security-Relevant Header

  ↓

  Add Controlled Duplicate

  ↓

  Send

  ↓

  Observe Response

  ↓

  Compare Protocol Versions

  ↓

  Determine Interpretation
  ```

* Use harmless values first.

---

## Security-Relevant Headers

* Examples worth understanding include:

  ```text
  Host / :authority

  Authorization

  Cookie

  Content-Length

  Transfer-Encoding

  X-Forwarded-For

  X-Forwarded-Host

  X-Original-URL

  X-Rewrite-URL

  Custom Role Headers
  ```

* Do not blindly manipulate all headers.

* Test based on observed architecture and application behavior.

---

## `Host` vs `:authority`

* HTTP/1.1 commonly routes using:

  ```http
  Host: example.test
  ```

* HTTP/2 uses:

  ```text
  :authority: example.test
  ```

* During protocol translation, infrastructure may map:

  ```text
  :authority

  ↓

  Host
  ```

* Differences or conflicts can become security-relevant.

---

## Authority Testing

* Ask:

  ```text
  Does :authority control routing?

  Is Host also accepted?

  What happens if they differ?

  Does front-end validation use one while back-end routing uses another?

  Are internal virtual hosts exposed?
  ```

* Use only authorized hostnames and safe test values.

---

## Routing Differential

* Example concept:

  ```text
  Front-End Validates:

  :authority = public.example
  ```

* But back end may route using:

  ```text
  Host = internal.example
  ```

* If conflicting routing metadata reaches different layers, behavior may diverge.

* Actual exploitability requires careful validation.

---

## Host Header Injection Context

* HTTP/2 does not eliminate host-based security issues.

* Applications may still use authority information for:

  ```text
  Password Reset Links

  Absolute URLs

  Routing

  Cache Keys

  Tenant Selection

  Redirects
  ```

* Determine whether:

  ```text
  :authority

  Host

  X-Forwarded-Host
  ```

* Influence application behavior.

---

## Path Handling

* HTTP/2 uses:

  ```text
  :path
  ```

* For the request target.

* Security-sensitive differences may involve:

  ```text
  Path Normalization

  Encoded Characters

  Duplicate Slashes

  Dot Segments

  Query Parsing

  Semicolon Handling

  Case Sensitivity
  ```

* Different layers may normalize paths differently.

---

## Path Normalization Workflow

* Establish baseline:

  ```text
  /admin
  ```

* Controlled variations may include safe equivalents such as:

  ```text
  //admin

  /./admin

  /public/../admin
  ```

* Only where appropriate and authorized.

* Observe whether:

  ```text
  Front-End Access Control

  and

  Back-End Routing
  ```

* Interpret the path consistently.

---

## Access-Control Differential

* Example:

  ```text
  Front-End:

  /admin → Blocked
  ```

* Variant:

  ```text
  /./admin
  ```

* If:

  ```text
  Front-End Sees Different Path

  ↓

  Allows Request

  ↓

  Back-End Normalizes to /admin
  ```

* An access-control bypass may exist.

* Validate the actual protected functionality before concluding.

---

## Method Handling

* HTTP/2 represents the method through:

  ```text
  :method
  ```

* Investigate application behavior for:

  ```text
  GET

  POST

  HEAD

  OPTIONS

  PUT

  PATCH

  DELETE
  ```

* Only where relevant.

---

## Method-Based Access Controls

* Example:

  ```text
  GET /admin

  → 403
  ```

* But:

  ```text
  HEAD /admin

  → ?
  ```

* Or:

  ```text
  POST /admin

  → ?
  ```

* Determine whether security controls are consistently applied across methods.

---

## Method Override Headers

* Some applications recognize:

  ```text
  X-HTTP-Method-Override

  X-Method-Override
  ```

* Example:

  ```text
  POST /resource

  X-HTTP-Method-Override: DELETE
  ```

* If supported, determine whether:

  ```text
  Access Control

  Routing

  Application Logic
  ```

* Interpret the effective method consistently.

---

## Scheme Handling

* HTTP/2 includes:

  ```text
  :scheme
  ```

* Commonly:

  ```text
  https
  ```

* Scheme information may influence:

  ```text
  Redirects

  Absolute URL Generation

  Secure Cookie Logic

  Proxy Behavior
  ```

* Infrastructure may also use headers such as:

  ```text
  X-Forwarded-Proto
  ```

* Conflicting scheme information can produce unexpected behavior.

---

## Proxy Headers

* Reverse proxies often add headers such as:

  ```text
  X-Forwarded-For

  X-Forwarded-Host

  X-Forwarded-Proto

  Forwarded
  ```

* Security controls may trust these values incorrectly.

* HTTP/2 does not change the need to test trust boundaries around proxy metadata.

---

## IP-Based Access Control

* Example:

  ```text
  X-Forwarded-For: 127.0.0.1
  ```

* Some poorly configured applications may trust user-supplied proxy headers.

* Testing should determine:

  ```text
  Which Layer Adds Header?

  Which Layer Trusts Header?

  Is Existing Header Replaced?

  Is It Appended?

  Can Client Supply It?
  ```

* Never assume a bypass based only on response differences.

---

## Header Case

* HTTP/2 header field names are represented in lowercase.

* Example:

  ```text
  authorization

  cookie

  content-type
  ```

* Applications and intermediaries should process headers appropriately.

* Custom infrastructure may still contain case-related assumptions.

---

## Forbidden or Connection-Specific Headers

* Some HTTP/1.1 connection-specific headers do not have the same role in HTTP/2.

* Examples include concepts around:

  ```text
  Connection

  Keep-Alive

  Transfer-Encoding

  Upgrade
  ```

* HTTP/2-aware infrastructure should handle inappropriate headers correctly.

* Unexpected acceptance may be worth investigating at downgrade boundaries.

---

## HTTP/2 Request Splitting Concepts

* Request splitting involves manipulating input so that one logical request may be interpreted as multiple requests downstream.

* HTTP/2's binary framing prevents some classic text-delimiter techniques at the direct protocol layer.

* However:

  ```text
  HTTP/2

  ↓

  Downgrade to HTTP/1.1

  ↓

  Textual Request Construction
  ```

* Can reintroduce parsing boundaries.

---

## Header Value Injection

* If an HTTP/2 header value is improperly inserted into a downgraded HTTP/1.1 request without safe validation, control characters could theoretically affect the generated request.

* Modern infrastructure should reject unsafe values.

* Security testing should focus on:

  ```text
  Does Front-End Validate?

  Does Translation Encode or Reject?

  What Does Back-End Receive?
  ```

* Perform only in safe labs or explicitly authorized environments.

---

## CRLF Considerations

* HTTP/1.1 uses:

  ```text
  CRLF
  ```

* To delimit header lines.

* HTTP/2 itself uses binary framing instead of textual CRLF-separated headers.

* But downgrade to HTTP/1.1 may convert HTTP/2 header metadata into text.

* Unsafe handling at this boundary can be security-relevant.

---

## Request Smuggling Detection Strategy

* A safe high-level workflow is:

  ```text
  Identify HTTP/2 Support

  ↓

  Determine Whether Downgrading Exists

  ↓

  Establish Baseline

  ↓

  Test Minimal Parsing Anomalies

  ↓

  Observe Timing / Response Differences

  ↓

  Use Dedicated Detection Techniques

  ↓

  Confirm in Isolated Conditions

  ↓

  Avoid Impacting Other Users
  ```

* Never begin with aggressive payload chains on shared production infrastructure.

---

## Timing-Based Clues

* Parsing disagreements may sometimes produce:

  ```text
  Delays

  Timeouts

  Unexpected Responses

  Connection Closure

  Subsequent Request Anomalies
  ```

* Timing alone does not prove request smuggling.

* It is an investigation signal.

---

## Response Queue Effects

* In desynchronization scenarios, unexpected effects may include:

  ```text
  Wrong Response

  Delayed Response

  Connection Reset

  Request Timeout

  Response Intended for Different Request
  ```

* These tests can be disruptive.

* Use dedicated labs whenever possible.

---

## HTTP/2 and Race Conditions

* HTTP/2 multiplexing can be useful for concurrency testing because multiple streams may share one connection.

* Conceptually:

  ```text
  One Connection

  ├── Stream A → Action
  ├── Stream B → Same Action
  └── Stream C → Same Action
  ```

* This can reduce some connection-level timing variation.

* Relevant scenarios may include:

  ```text
  Coupon Redemption

  Balance Updates

  Inventory

  Voting

  One-Time Tokens

  State Transitions
  ```

---

## Race Testing Principle

* Use:

  ```text
  Understand State

  ↓

  Establish Single-Request Baseline

  ↓

  Determine Expected Invariant

  ↓

  Send Controlled Concurrent Requests

  ↓

  Observe Final State

  ↓

  Reproduce Safely
  ```

* Do not perform high-volume concurrency tests without authorization.

---

## HTTP/2 Connection Reuse

* Multiple requests may share the same connection.

* This can matter when investigating:

  ```text
  Connection-Bound State

  Authentication

  Load Balancing

  Request Smuggling

  Race Conditions

  Back-End Routing
  ```

* Compare behavior across:

  ```text
  Same Connection

  vs

  Fresh Connection
  ```

---

## Connection-Specific Behavior

* If a result occurs only when requests share one connection, investigate:

  ```text
  Connection State

  Stream Interaction

  Back-End Connection Reuse

  Proxy Behavior

  Authentication Context
  ```

* This can help distinguish application bugs from infrastructure behavior.

---

## HTTP/2 and Caching

* Cache keys may depend on:

  ```text
  Authority

  Path

  Query

  Headers

  Scheme
  ```

* Protocol translation may affect how these values are normalized.

* Compare:

  ```text
  Cache-Control

  Age

  Vary

  Response Headers

  Response Body
  ```

* When investigating cache behavior.

---

## Cache-Key Differential

* Example:

  ```text
  Front-End Cache Key Uses:

  :authority
  ```

* Application may use:

  ```text
  Host
  ```

* If these values diverge, cache behavior may become inconsistent.

* Validate carefully before concluding cache poisoning or routing impact.

---

## HTTP/2 and Authentication

* Authentication mechanisms remain familiar:

  ```text
  Cookies

  Authorization Headers

  Tokens

  Client Certificates
  ```

* But protocol handling can affect:

  ```text
  Duplicate Headers

  Header Normalization

  Proxy Translation

  Connection Reuse
  ```

---

## Duplicate Authorization Headers

* Conceptually:

  ```text
  authorization: Bearer TOKEN-A

  authorization: Bearer TOKEN-B
  ```

* Different systems may:

  ```text
  Reject

  Use First

  Use Last

  Combine
  ```

* If authentication and application layers interpret duplicates differently, security problems may occur.

---

## Duplicate Cookie Handling

* Cookies may also be represented in ways that are combined or normalized by intermediaries.

* Investigate:

  ```text
  Duplicate Cookie Names

  Multiple Cookie Fields

  Conflicting Session Values

  Proxy Normalization
  ```

* Use controlled test accounts to avoid session confusion.

---

## Authentication Differential Workflow

* Use two authorized test sessions:

  ```text
  User A

  User B
  ```

* Establish baseline requests.

* Then investigate only justified ambiguities.

* Record:

  ```text
  Front-End Result

  Application Identity

  Response Content

  Session State
  ```

* The key question is:

  ```text
  Which identity did the server actually use?
  ```

---

## HTTP/2 and Access Control

* Test whether access-control behavior remains consistent across:

  ```text
  HTTP/1.1

  HTTP/2

  Path Variations

  Method Variations

  Authority Variations

  Header Normalization
  ```

* A difference is a lead, not automatically a vulnerability.

---

## Protocol Differential Testing

* Use:

  ```text
  Same User

  +

  Same Endpoint

  +

  Same Parameters

  +

  Same Authentication

  ```

* Change only:

  ```text
  Protocol
  ```

* Compare results.

* This isolates protocol-dependent behavior.

---

## Differential Testing Matrix

| Test               | HTTP/1.1 | HTTP/2 | Difference? |
| ------------------ | -------- | ------ | ----------- |
| Normal request     | Record   | Record |             |
| Protected endpoint | Record   | Record |             |
| Path normalization | Record   | Record |             |
| Duplicate header   | Record   | Record |             |
| Authority behavior | Record   | Record |             |
| Error handling     | Record   | Record |             |

---

## Error Handling

* Invalid or unusual HTTP/2 requests may produce:

  ```text
  400 Bad Request

  421 Misdirected Request

  431 Request Header Fields Too Large

  Stream Reset

  Connection Closure

  GOAWAY
  ```

* Error differences can reveal which component processed the request.

---

## `421 Misdirected Request`

* A `421` response may indicate that the server cannot produce a response for the combination of request and connection context.

* This can be relevant when investigating:

  ```text
  Authority

  Connection Reuse

  Virtual Hosting

  Routing
  ```

* Interpret it within the target architecture.

---

## Stream Reset

* A server may reset an individual stream without closing the entire HTTP/2 connection.

* Conceptually:

  ```text
  Connection Remains Open

  ↓

  One Stream Terminated
  ```

* This differs from some HTTP/1.1 connection-level failures.

---

## GOAWAY

* HTTP/2 can use a:

  ```text
  GOAWAY
  ```

* Frame to indicate that the connection is being shut down.

* Repeated GOAWAY behavior during testing may indicate:

  ```text
  Protocol Error

  Resource Limits

  Defensive Controls

  Server Shutdown

  Invalid Request Behavior
  ```

---

## Rate Limiting

* Determine whether rate limiting is consistent across:

  ```text
  HTTP/1.1

  HTTP/2

  Multiple Streams

  Multiple Connections
  ```

* Do not attempt to bypass operational safeguards on real systems without explicit authorization.

* Use safe request volumes.

---

## Stream-Level Concurrency

* HTTP/2 may allow many concurrent streams.

* Security controls should not rely solely on assumptions such as:

  ```text
  One Connection

  =

  One Request at a Time
  ```

* Relevant controls include:

  ```text
  Rate Limiting

  Transaction Locking

  One-Time Actions

  State Validation
  ```

---

## Traffic Analysis

* Because HTTP/2 supports concurrent streams, simple chronological request order may not fully represent application causality.

* Use:

  ```text
  Timestamps

  Stream Context

  Application IDs

  Request IDs

  Object IDs

  State Changes
  ```

* To reconstruct workflows accurately.

---

## HTTP/2 Timeline Example

* Example:

  ```text
  T1 — Stream 1 → GET /account

  T2 — Stream 3 → GET /notifications

  T3 — Stream 5 → POST /action

  T4 — Stream 3 ← Notification Response

  T5 — Stream 1 ← Account Response

  T6 — Stream 5 ← Action Response
  ```

* Response completion order may differ from request initiation order.

---

## Logger and HTTP/2

* Logger can help correlate:

  ```text
  Requests

  Responses

  Burp Tool Source

  Authentication Activity

  Timing

  Protocol-Related Behavior
  ```

* Use it alongside Repeater when investigating protocol differences.

---

## HTTP/2 Testing With Repeater

* Recommended workflow:

  ```text
  Capture

  ↓

  Send to Repeater

  ↓

  Confirm HTTP/2 Baseline

  ↓

  Duplicate Tab

  ↓

  Create HTTP/1.1 Comparison Where Supported

  ↓

  Modify One Variable

  ↓

  Compare
  ```

* Preserve the original request.

---

## HTTP/2 Testing With Comparer

* Comparer can help identify subtle differences between:

  ```text
  HTTP/1.1 Response

  vs

  HTTP/2 Response
  ```

* Compare:

  ```text
  Body

  Headers

  Error Messages

  Redirect Targets

  Dynamic Content
  ```

* Normalize expected dynamic values mentally before interpreting differences.

---

## HTTP/2 Testing With Intruder

* Intruder may be useful for controlled variation of:

  ```text
  Paths

  Header Values

  Methods

  Object IDs

  Authority Values
  ```

* Keep payload sets small and purposeful.

* Protocol-level testing should not become blind fuzzing.

---

## HTTP/2 Testing With Extensions

* Some Burp extensions assist with:

  ```text
  Request Smuggling Research

  Protocol Manipulation

  Header Analysis

  Race Condition Testing
  ```

* Before using an extension:

  ```text
  Understand What It Sends

  ↓

  Review Scope

  ↓

  Use a Lab First

  ↓

  Capture Baseline

  ↓

  Monitor Generated Traffic
  ```

---

## Infrastructure Mapping

* HTTP/2 findings often require understanding the infrastructure.

* Build a model:

  ```text
  Client

  ↓

  CDN?

  ↓

  WAF?

  ↓

  Load Balancer?

  ↓

  Reverse Proxy?

  ↓

  Application Server?
  ```

* Then determine likely protocol usage between layers.

---

## Protocol Chain Map

* Record:

  ```text
  Client → Edge:

  HTTP/2 or HTTP/1.1?

  Edge → Proxy:

  Unknown / HTTP/2 / HTTP/1.1?

  Proxy → Application:

  Unknown / HTTP/2 / HTTP/1.1?
  ```

* You may not know every internal hop.

* Mark unknowns explicitly rather than assuming.

---

## Fingerprinting Translation Behavior

* Clues may include:

  ```text
  Server Headers

  Error Messages

  Response Timing

  Header Rewriting

  Duplicate Header Behavior

  Protocol-Specific Differences
  ```

* Avoid overclaiming the exact architecture without evidence.

---

## HTTP/2 Testing Workflow

* Use:

  ```text
  Identify HTTP/2 Support

  ↓

  Capture Baseline

  ↓

  Map Pseudo-Headers

  ↓

  Identify Security-Relevant Headers

  ↓

  Compare HTTP/1.1 and HTTP/2

  ↓

  Map Likely Translation Boundaries

  ↓

  Test Routing and Authority

  ↓

  Test Path Normalization

  ↓

  Test Header Handling

  ↓

  Investigate Authentication Differences

  ↓

  Investigate Access-Control Differences

  ↓

  Assess Desynchronization Only Where Safe

  ↓

  Test Concurrency Where Relevant

  ↓

  Reproduce

  ↓

  Preserve Evidence
  ```

---

## HTTP/2 Attack Surface Map

* Document:

  ```text
  Target:

  Endpoint:

  HTTP/2 Supported?:

  HTTP/1.1 Supported?:

  ALPN:

  Method:

  Scheme:

  Authority:

  Path:

  Authentication:

  Security-Relevant Headers:

  Proxy Headers:

  Duplicate Header Behavior:

  Path Normalization:

  Protocol Differences:

  Suspected Downgrade?:

  Routing Behavior:

  Error Behavior:

  Connection Behavior:

  Concurrency-Relevant Actions:
  ```

---

## HTTP/2 Investigation Card

* Record:

  ```text
  Investigation:

  Target:

  Endpoint:

  User:

  Role:

  Protocol:

  Baseline Request:

  Baseline Response:

  Method:

  Scheme:

  Authority:

  Path:

  Header Changed:

  Original Value:

  Modified Value:

  HTTP/1.1 Result:

  HTTP/2 Result:

  Status Difference:

  Body Difference:

  Routing Difference:

  Authentication Difference:

  Access-Control Difference:

  Connection Effect:

  Suspected Translation Layer:

  Security Impact:

  Reproduced?:

  Evidence Preserved?:

  Follow-Up:
  ```

---

## HTTP/2 Testing Checklist

* Verify:

  ```text
  [ ] HTTP/2 support identified

  [ ] HTTP/1.1 support compared where relevant

  [ ] Baseline request captured

  [ ] Pseudo-headers understood

  [ ] :method reviewed

  [ ] :scheme reviewed

  [ ] :authority reviewed

  [ ] :path reviewed

  [ ] Authentication context confirmed

  [ ] Important normal headers reviewed

  [ ] Duplicate header behavior considered

  [ ] Authority / Host behavior considered

  [ ] Path normalization considered

  [ ] Method behavior considered

  [ ] Proxy headers considered

  [ ] Protocol translation considered

  [ ] Downgrade behavior considered

  [ ] Access-control differences investigated

  [ ] Authentication differences investigated

  [ ] Error handling reviewed

  [ ] Connection reuse considered

  [ ] Multiplexing considered

  [ ] Concurrency tested only where relevant

  [ ] Desynchronization testing performed only safely

  [ ] Findings reproduced cleanly
  ```

---

## Testing Strategy Matrix

| Scenario                    | Primary Investigation             |
| --------------------------- | --------------------------------- |
| HTTP/2 enabled target       | Establish HTTP/2 baseline         |
| HTTP/1.1 also supported     | Protocol differential testing     |
| Reverse proxy architecture  | Translation boundary analysis     |
| Conflicting routing values  | Authority / Host analysis         |
| Protected path              | Path normalization                |
| Custom proxy headers        | Trust-boundary analysis           |
| Duplicate headers           | Parser interpretation comparison  |
| Different auth behavior     | Authentication differential       |
| Different access result     | Access-control differential       |
| HTTP/2 → HTTP/1.1 downgrade | Desynchronization risk assessment |
| One-time action             | Controlled concurrency testing    |
| Unexpected response order   | Stream/timeline analysis          |
| Protocol errors             | Error and connection behavior     |

---

## Troubleshooting — Cannot Confirm HTTP/2

* Check:

  ```text
  Does Server Support HTTP/2?

  ↓

  Is HTTPS Being Used?

  ↓

  What Protocol Was Negotiated?

  ↓

  Is Burp Configuration Affecting Negotiation?

  ↓

  Does Another Endpoint Behave Differently?
  ```

---

## Troubleshooting — HTTP/2 Request Behaves Differently

* Compare:

  ```text
  Same Authentication?

  Same Cookies?

  Same Method?

  Same Path?

  Same Authority / Host?

  Same Body?

  Same Headers?

  Same Connection State?
  ```

* Isolate protocol as the only variable.

---

## Troubleshooting — Request Returns 400

* Check:

  ```text
  Invalid Header?

  Duplicate Header Rejected?

  Illegal HTTP/2 Header?

  Incorrect Content Length?

  Invalid Pseudo-Header?

  Proxy Rejection?

  Application Rejection?
  ```

* Return to the baseline and add one change at a time.

---

## Troubleshooting — Connection Closes

* Investigate:

  ```text
  Protocol Error

  Invalid Header

  Stream Error

  Defensive Control

  GOAWAY

  Back-End Failure
  ```

* Determine whether only one stream or the entire connection is affected.

---

## Troubleshooting — Authority Modification Has No Effect

* Possible explanations:

  ```text
  Front-End Overrides Value

  Host Routing Fixed

  Request Rejected Before Application

  Back-End Uses Different Header

  CDN Normalizes Authority
  ```

* Compare effective routing evidence.

---

## Troubleshooting — Duplicate Header Gives Inconsistent Results

* Check:

  ```text
  Header Order

  Protocol Version

  Connection Reuse

  Front-End Normalization

  Back-End Parsing

  Cache

  Authentication State
  ```

* Repeat controlled tests before drawing conclusions.

---

## Troubleshooting — Suspected Request Smuggling

* Do not immediately escalate payload complexity.

* Use:

  ```text
  Stop

  ↓

  Confirm Scope

  ↓

  Use Dedicated Safe Environment if Possible

  ↓

  Establish Fresh Connection

  ↓

  Reproduce Minimal Anomaly

  ↓

  Rule Out Network Noise

  ↓

  Validate Without Affecting Other Users
  ```

---

## Troubleshooting — Different Result Only on Same Connection

* Investigate:

  ```text
  Connection Reuse

  Back-End Connection State

  Authentication

  Stream Interaction

  Cache

  Desynchronization

  Load Balancer Behavior
  ```

* Compare with a fresh connection.

---

## Evidence Collection

* For protocol-related findings, preserve:

  ```text
  Original Baseline Request

  Modified Request

  Protocol Version

  Relevant Pseudo-Headers

  Relevant Normal Headers

  Authentication Context

  Response

  Comparison Request

  Connection Conditions

  Reproduction Steps

  Security Impact
  ```

* Protocol findings are difficult to reproduce if the exact request conditions are omitted.

---

## Differential Evidence

* Strong evidence often includes:

  ```text
  Control:

  HTTP/1.1 → Expected Security Behavior
  ```

* Compared with:

  ```text
  Test:

  HTTP/2 → Different Security Behavior
  ```

* While keeping:

  ```text
  User

  Endpoint

  Parameters

  Authentication

  Application State
  ```

* Constant.

---

## Avoiding False Positives

* Differences may result from:

  ```text
  Different Backend Node

  Cache

  Session Drift

  Dynamic Content

  Load Balancing

  Rate Limiting

  WAF Behavior

  Connection Reuse

  Testing Noise
  ```

* Reproduce several times under controlled conditions.

---

## Practical Exercise

* Use a deliberately vulnerable lab or another system you are explicitly authorized to test.

### Task 1 — Identify Protocol

* Capture one request.

* Record:

  ```text
  Target:

  Endpoint:

  Protocol:

  HTTP/2 Supported?:

  HTTP/1.1 Supported?:
  ```

---

### Task 2 — Map the Request

* Identify:

  ```text
  Method:

  Scheme:

  Authority:

  Path:

  Authentication:

  Important Headers:

  Body:
  ```

---

### Task 3 — Establish Baseline

* Send the request unchanged.

* Record:

  ```text
  Status:

  Length:

  Important Response Headers:

  Authentication State:

  Expected Behavior:
  ```

---

### Task 4 — Compare Protocol Versions

* Where supported, send the same logical request using:

  ```text
  HTTP/2

  and

  HTTP/1.1
  ```

* Compare:

  ```text
  Status

  Body

  Length

  Headers

  Timing
  ```

---

### Task 5 — Test One Harmless Header Variation

* Modify one non-destructive header.

* Record:

  ```text
  Original:

  Modified:

  HTTP/2 Result:

  HTTP/1.1 Result:
  ```

---

### Task 6 — Analyze Authority

* Record the normal authority value.

* In an authorized lab, test one safe alternate value relevant to the lab scenario.

* Observe:

  ```text
  Rejected?

  Routed Differently?

  Normalized?

  Ignored?
  ```

---

### Task 7 — Analyze Path Normalization

* Use a safe lab endpoint.

* Compare:

  ```text
  /path

  /./path
  ```

* Record whether security and routing behavior remain consistent.

---

### Task 8 — Observe Connection Behavior

* Send several normal HTTP/2 requests.

* Observe:

  ```text
  Shared Connection?

  Concurrent Streams?

  Different Response Order?

  Connection Errors?
  ```

---

### Task 9 — Build a Protocol Chain Hypothesis

* Document:

  ```text
  Client

  ↓

  Edge / CDN

  ↓

  Proxy

  ↓

  Application
  ```

* Mark each protocol as:

  ```text
  Known

  Suspected

  Unknown
  ```

---

### Task 10 — Restore Clean Baseline

* Remove all test modifications.

* Send the original request.

* Confirm expected behavior returns.

---

## Investigation Journal

* Record:

  ```text
  Application:

  Target:

  HTTP/2 Support:

  HTTP/1.1 Support:

  ALPN:

  Endpoint:

  Method:

  Scheme:

  Authority:

  Path:

  Authentication:

  User:

  Role:

  Baseline:

  HTTP/1.1 Result:

  HTTP/2 Result:

  Header Tests:

  Duplicate Header Tests:

  Authority Tests:

  Path Tests:

  Method Tests:

  Proxy Header Tests:

  Error Behavior:

  Connection Behavior:

  Multiplexing Observations:

  Suspected Downgrade:

  Suspected Translation Boundary:

  Security Difference:

  Reproduced:

  Evidence:

  Follow-Up:
  ```

---

## Common Mistakes

* Common mistakes include:

  * Treating HTTP/2 as merely a faster version of HTTP/1.1.
  * Ignoring pseudo-headers.
  * Assuming `Host` and `:authority` are always handled identically.
  * Ignoring HTTP/2-to-HTTP/1.1 downgrade boundaries.
  * Testing multiple protocol anomalies simultaneously.
  * Treating every HTTP/1.1 vs HTTP/2 difference as a vulnerability.
  * Ignoring authentication drift between comparison requests.
  * Forgetting that multiplexed responses may complete out of order.
  * Assuming response order equals application action order.
  * Blindly testing duplicate headers without understanding their purpose.
  * Performing aggressive request-smuggling tests against shared production infrastructure.
  * Treating a timeout as proof of desynchronization.
  * Ignoring path normalization differences.
  * Ignoring proxy-generated headers.
  * Forgetting connection reuse as a testing variable.
  * Running concurrency tests without considering side effects.
  * Failing to preserve the exact protocol and connection conditions needed for reproduction.
  * Overclaiming internal infrastructure based only on external clues.

---

## Interview Questions

1. What is HTTP/2?
2. What are the major differences between HTTP/1.1 and HTTP/2?
3. What is HTTP/2 multiplexing?
4. What is an HTTP/2 stream?
5. What are HTTP/2 frames?
6. What are HTTP/2 pseudo-headers?
7. What does `:method` represent?
8. What does `:scheme` represent?
9. What does `:authority` represent?
10. What does `:path` represent?
11. How does `:authority` relate to the HTTP/1.1 `Host` header?
12. What is ALPN?
13. How can you identify HTTP/2 usage during a Burp assessment?
14. What is HTTP/2 downgrading?
15. Why can HTTP/2-to-HTTP/1.1 translation create security risks?
16. What is HTTP request smuggling at a high level?
17. What does H2.CL conceptually refer to?
18. What does H2.TE conceptually refer to?
19. Why does HTTP/2 not use chunked transfer encoding like HTTP/1.1?
20. Why can duplicate headers be security-relevant?
21. How would you compare HTTP/1.1 and HTTP/2 behavior safely?
22. Why is path normalization important?
23. How can authority-routing differences create security issues?
24. Why should protocol differential testing change only one variable?
25. How can HTTP/2 multiplexing affect race-condition testing?
26. Why can response completion order differ from request order?
27. What does `421 Misdirected Request` generally indicate?
28. What is a stream reset?
29. What is a GOAWAY frame?
30. Why should request-smuggling testing be performed cautiously?
31. How can connection reuse affect HTTP/2 security testing?
32. What evidence should be preserved for a protocol-level finding?
33. Why does a timeout not prove request smuggling?
34. How can proxy headers create trust-boundary issues?
35. What is the correct methodology for investigating an HTTP/2-specific security difference?

---

## Quick Revision

* HTTP/2 model:

  ```text
  One Connection

  ↓

  Multiple Streams

  ↓

  Multiplexed Requests and Responses
  ```

* Pseudo-headers:

  ```text
  :method

  :scheme

  :authority

  :path
  ```

* Downgrade model:

  ```text
  Client

  ↓ HTTP/2

  Front-End

  ↓ Translation

  HTTP/1.1

  ↓

  Back-End
  ```

* Security principle:

  ```text
  Front-End Interpretation

  ≠

  Back-End Interpretation

  →

  Investigate
  ```

* Differential testing:

  ```text
  Same Request

  +

  Same User

  +

  Same State

  ↓

  HTTP/1.1

  vs

  HTTP/2
  ```

* Request smuggling concept:

  ```text
  Request Boundary Disagreement

  ↓

  Desynchronization
  ```

* Race-testing advantage:

  ```text
  One HTTP/2 Connection

  +

  Multiple Concurrent Streams

  →

  Tighter Concurrency
  ```

* Remember:

  ```text
  HTTP/2

  ≠

  HTTP/1.1 With Better Speed Only
  ```

  ```text
  :authority

  ≠

  Always Identical Handling to Host
  ```

  ```text
  Protocol Difference

  ≠

  Confirmed Vulnerability
  ```

  ```text
  Timeout

  ≠

  Confirmed Request Smuggling
  ```

  ```text
  Response Order

  ≠

  Request Causality
  ```

* Professional principle:

  ```text
  Understand Protocol

  +

  Map Translation Boundaries

  +

  Control Variables

  +

  Compare Interpretations

  +

  Validate Impact

  =

  Reliable HTTP/2 Testing
  ```

---

## Key Takeaways

* HTTP/2 uses binary framing, streams, multiplexing, and header compression rather than HTTP/1.1's traditional text-based wire format.
* HTTP semantics such as methods, paths, headers, authentication, and bodies still exist, but their protocol representation differs.
* The key HTTP/2 request pseudo-headers are `:method`, `:scheme`, `:authority`, and `:path`.
* Multiplexing allows multiple independent request-response streams to share one connection.
* Response completion order may differ from request initiation order, so traffic timelines require careful analysis.
* HTTP/2-to-HTTP/1.1 downgrade boundaries are security-relevant because infrastructure must translate between different framing models.
* Security issues may arise when front-end and back-end components interpret request boundaries, headers, paths, or routing information differently.
* `:authority` and `Host` behavior should be understood rather than assumed to be identical across all infrastructure.
* Duplicate headers can reveal parser inconsistencies when different components choose different values.
* Path normalization differences may create access-control or routing discrepancies.
* HTTP/2 does not use HTTP/1.1 chunked transfer encoding in the same way, making downgrade handling of transfer-related headers important in desynchronization research.
* H2.CL and H2.TE describe conceptual HTTP/2 downgrade desynchronization scenarios involving downstream HTTP/1.1 interpretation.
* Request-smuggling testing can affect shared infrastructure and should be performed only with explicit authorization and safe methodology.
* Timeouts and unusual responses are investigation signals, not automatic proof of desynchronization.
* HTTP/1.1 and HTTP/2 differential testing should keep user, authentication, endpoint, parameters, and application state constant.
* HTTP/2 multiplexing can support precise concurrency testing for race-condition research when performed safely.
* Connection reuse can itself be a testing variable and should be compared with fresh connections when behavior differs.
* Burp Repeater, Logger, Comparer, Intruder, and carefully selected extensions can support HTTP/2 investigation.
* Protocol-level findings require exact evidence about protocol version, headers, authentication context, connection conditions, and reproduction steps.
* The core HTTP/2 testing mindset is to identify where different systems may interpret the same logical request differently and then validate whether that difference creates a real security impact.
