# WebSocket Testing Workflows

> **Module 15 — Advanced Burp Workflows**

> **Progress**

```text
██████████░░░░░ 10 / 15 Files
```

---

## Overview

* Modern web applications do not always communicate exclusively through traditional HTTP request-response cycles.

* Applications that require real-time, bidirectional communication may use **WebSockets**.

* Common examples include:

  * Live chat systems.
  * Notification systems.
  * Collaborative applications.
  * Trading dashboards.
  * Multiplayer applications.
  * Real-time monitoring dashboards.
  * Customer support platforms.
  * Streaming updates.
  * Administrative consoles.

* Traditional HTTP generally follows:

  ```text
  Client

  ↓ Request

  Server

  ↓ Response

  Client
  ```

* WebSockets establish a persistent connection:

  ```text
  Client

  ⇅

  Persistent WebSocket Connection

  ⇅

  Server
  ```

* After the connection is established, both sides can exchange messages independently.

* For a penetration tester, this changes the testing methodology.

* Testing only Proxy HTTP history may reveal the WebSocket handshake but miss the messages carrying the application's actual functionality.

* A professional workflow therefore requires:

  ```text
  Identify WebSocket

  +

  Analyze Handshake

  +

  Observe Messages

  +

  Understand Message Structure

  +

  Replay and Modify

  +

  Test Security Boundaries

  +

  Validate Server-Side Impact
  ```

---

## Learning Objectives

* By the end of this lesson, you will be able to:

  * Explain how WebSockets differ from traditional HTTP.
  * Understand the WebSocket upgrade handshake.
  * Identify WebSocket connections in Burp Suite.
  * Analyze WebSocket message history.
  * Distinguish client-to-server and server-to-client messages.
  * Understand common WebSocket message formats.
  * Intercept and modify WebSocket messages.
  * Replay messages using Burp's WebSocket tooling.
  * Test authentication and authorization controls.
  * Investigate object-level authorization.
  * Test input handling safely.
  * Analyze origin validation.
  * Understand Cross-Site WebSocket Hijacking.
  * Investigate business logic and state transitions.
  * Correlate WebSocket messages with HTTP traffic.
  * Build a structured WebSocket testing methodology.
  * Preserve reliable evidence for WebSocket findings.

---

## What Is a WebSocket?

* WebSocket is a protocol that enables persistent, bidirectional communication between a client and server.

* Traditional HTTP:

  ```text
  Client

  ↓ Request

  Server

  ↓ Response

  Connection Ends or Is Reused
  ```

* WebSocket:

  ```text
  Client

  ⇅

  Persistent Connection

  ⇅

  Server
  ```

* Either side can send data after the connection has been established.

---

## Why Applications Use WebSockets

* WebSockets are useful when applications require near-real-time communication.

* Examples:

  ```text
  Chat Message Arrives

  ↓

  Server Immediately Sends Update

  ↓

  Browser Displays Message
  ```

* Without persistent communication, the browser may need to repeatedly poll:

  ```text
  Any New Message?

  Any New Message?

  Any New Message?
  ```

* WebSockets allow the server to push updates directly.

---

## Why WebSockets Matter for Security Testing

* Security-sensitive functionality may exist entirely inside WebSocket messages.

* Examples:

  ```text
  Send Message

  Delete Message

  Join Channel

  Subscribe to Account

  Update Document

  Execute Action

  Receive Private Notification

  Modify Order

  Trigger Administrative Function
  ```

* If you inspect only traditional HTTP traffic, you may miss these actions.

---

## HTTP vs WebSocket

| Characteristic         | HTTP                   | WebSocket                    |
| ---------------------- | ---------------------- | ---------------------------- |
| Communication          | Request-response       | Bidirectional                |
| Connection             | Usually transactional  | Persistent                   |
| Server push            | Limited / indirect     | Native                       |
| Initial negotiation    | Normal HTTP            | HTTP upgrade                 |
| Burp analysis          | Proxy HTTP history     | WebSocket history / messages |
| Typical security focus | Requests and responses | Handshake and message stream |

---

## WebSocket Connection Lifecycle

* A WebSocket connection generally follows:

  ```text
  Browser

  ↓

  HTTP Upgrade Request

  ↓

  Server Accepts Upgrade

  ↓

  WebSocket Connection Established

  ↓

  Client ⇄ Server Messages

  ↓

  Connection Closed
  ```

* Testing should consider both:

  ```text
  Handshake Security

  +

  Message Security
  ```

---

## The WebSocket Handshake

* WebSockets begin with an HTTP request.

* Example:

  ```http
  GET /chat HTTP/1.1
  Host: example.test
  Upgrade: websocket
  Connection: Upgrade
  Sec-WebSocket-Key: example-value
  Sec-WebSocket-Version: 13
  Origin: https://example.test
  Cookie: session=abc123
  ```

* Important fields may include:

  ```text
  Host

  Upgrade

  Connection

  Origin

  Cookie

  Authorization

  Sec-WebSocket-Key

  Sec-WebSocket-Version

  Subprotocol Information
  ```

---

## Server Upgrade Response

* A successful upgrade commonly returns:

  ```http
  HTTP/1.1 101 Switching Protocols
  Upgrade: websocket
  Connection: Upgrade
  Sec-WebSocket-Accept: calculated-value
  ```

* The key status is:

  ```text
  101 Switching Protocols
  ```

* After this point:

  ```text
  HTTP Request-Response

  ↓

  WebSocket Message Exchange
  ```

---

## Security-Relevant Handshake Fields

* Pay particular attention to:

  ```text
  Cookie

  Authorization

  Origin

  Host

  Query Parameters

  Custom Authentication Headers

  Subprotocol
  ```

* Ask:

  ```text
  How is the user authenticated?

  Is Origin validated?

  Does the URL contain a token?

  Are authorization decisions made only during connection establishment?

  Does the server support multiple subprotocols?

  Can the handshake be replayed with altered identity?
  ```

---

## Finding WebSocket Traffic in Burp

* Browse the target application normally through Burp.

* Look for:

  ```text
  HTTP Upgrade Requests

  101 Switching Protocols

  WebSocket Connections

  WebSocket Message History
  ```

* Trigger application features likely to use real-time communication.

* Examples:

  ```text
  Send Chat Message

  Open Notifications

  Join Live Session

  Edit Shared Document

  Watch Dashboard Update
  ```

---

## WebSocket History

* Burp can provide a history of messages exchanged over WebSocket connections.

* Conceptually:

  ```text
  Connection

  ├── Client → Server Message
  ├── Server → Client Message
  ├── Client → Server Message
  ├── Server → Client Message
  └── ...
  ```

* This history becomes the equivalent of Proxy HTTP history for WebSocket message analysis.

---

## Client-to-Server Messages

* These messages represent actions initiated by the client.

* Examples:

  ```json
  {
    "action": "send_message",
    "message": "Hello"
  }
  ```

* Or:

  ```json
  {
    "action": "subscribe",
    "channel": "notifications"
  }
  ```

* These are often primary testing targets because they may contain user-controlled input.

---

## Server-to-Client Messages

* These messages represent data pushed by the server.

* Example:

  ```json
  {
    "type": "message",
    "sender": "alice",
    "text": "Hello"
  }
  ```

* Or:

  ```json
  {
    "type": "notification",
    "account_id": 42,
    "message": "Payment received"
  }
  ```

* Analyze these for:

  ```text
  Sensitive Data

  Internal Identifiers

  Authorization Leakage

  Debug Information

  Unexpected Metadata

  Hidden Application State
  ```

---

## Common Message Formats

* WebSocket messages may contain:

  ```text
  JSON

  XML

  Plain Text

  Encoded Data

  Binary Data

  Custom Protocol Formats
  ```

* JSON is especially common.

* Example:

  ```json
  {
    "action": "get_order",
    "order_id": 7319
  }
  ```

---

## Mapping Message Structure

* Before modifying messages, identify their structure.

* Record:

  ```text
  Message Type:

  Action:

  Parameters:

  Object IDs:

  User IDs:

  Role Fields:

  Tokens:

  Timestamps:

  Nested Objects:

  Optional Fields:
  ```

* Treat WebSocket messages like API requests.

---

## Build a Message Inventory

* During application mapping, create an inventory.

* Example:

  ```text
  Connection:

  wss://example.test/socket
  ```

  ```text
  Message Types:

  authenticate

  subscribe

  send_message

  edit_message

  delete_message

  get_history
  ```

* This gives you a testable attack surface.

---

## Message Inventory Table

| Action         | Direction       | Important Fields   | Security Question                       |
| -------------- | --------------- | ------------------ | --------------------------------------- |
| `authenticate` | Client → Server | token              | Is authentication enforced?             |
| `subscribe`    | Client → Server | channel_id         | Can another user's channel be accessed? |
| `send_message` | Client → Server | recipient_id, text | Is authorization enforced?              |
| `edit_message` | Client → Server | message_id, text   | Can another user's message be modified? |
| `notification` | Server → Client | account_id, data   | Is private data leaked?                 |

---

## Establish a Baseline

* Before testing, capture normal behavior.

* Example:

  ```text
  User Clicks Send

  ↓

  Client Message A

  ↓

  Server Message B

  ↓

  UI Updates
  ```

* Record:

  ```text
  Original Message

  Response Message

  User Identity

  Object Ownership

  Expected UI Effect
  ```

* This becomes your baseline.

---

## Intercepting WebSocket Messages

* Burp can intercept WebSocket messages so they can be inspected before forwarding.

* Conceptually:

  ```text
  Client

  ↓

  WebSocket Message

  ↓

  Burp Intercept

  ├── Forward
  ├── Modify
  └── Drop

  ↓

  Server
  ```

* This enables controlled testing of client-generated messages.

---

## Modify One Field at a Time

* Suppose the original message is:

  ```json
  {
    "action": "get_order",
    "order_id": 7319
  }
  ```

* Change only:

  ```text
  7319

  →

  7320
  ```

* Then observe the result.

* Avoid immediately changing:

  ```text
  action

  order_id

  account_id

  role

  token
  ```

* Simultaneously.

* One-variable testing produces clearer evidence.

---

## Replaying WebSocket Messages

* Burp's WebSocket tooling can be used to resend or manipulate captured messages, depending on the available Burp version and workflow.

* Conceptually:

  ```text
  Captured Message

  ↓

  Send to WebSocket Testing Tool

  ↓

  Modify

  ↓

  Send

  ↓

  Observe Server Response
  ```

* This provides a workflow similar in spirit to HTTP Repeater.

---

## Why Replay Matters

* Replay allows you to test:

  ```text
  Parameter Changes

  Repeated Actions

  Authorization

  State Transitions

  Message Ordering

  Duplicate Operations

  Input Validation
  ```

* It separates testing from the application's normal UI.

---

## WebSocket Authentication Testing

* Determine how the connection is authenticated.

* Common mechanisms include:

  ```text
  Session Cookie in Handshake

  Authorization Header

  Token in Query Parameter

  Authentication Message After Connection

  Temporary Connection Token
  ```

* Example:

  ```text
  HTTP Handshake

  ↓

  WebSocket Connected

  ↓

  Client Sends:

  {"type":"auth","token":"..."}
  ```

---

## Authentication Questions

* Ask:

  ```text
  Can the connection be established without authentication?

  Can messages be sent before authentication?

  What happens after logout?

  Does an existing WebSocket remain active after session termination?

  Does token expiration affect an established connection?

  Can authentication state be changed mid-connection?

  Is authentication tied to the correct user?
  ```

---

## Testing Unauthenticated Connections

* In an authorized lab or assessment:

  ```text
  Capture Baseline Connection

  ↓

  Identify Authentication Material

  ↓

  Remove One Authentication Element

  ↓

  Reconnect

  ↓

  Observe

  ↓

  Attempt Harmless Authorized-Scope Message
  ```

* Distinguish:

  ```text
  Connection Accepted

  from

  Sensitive Action Authorized
  ```

* A server may permit connection establishment but restrict later messages.

---

## Authentication After Logout

* A useful security test is:

  ```text
  Login

  ↓

  Establish WebSocket

  ↓

  Logout Through HTTP

  ↓

  Keep Existing Socket Open

  ↓

  Attempt Safe Message

  ↓

  Observe
  ```

* Investigate whether the connection remains authorized after logout.

* Whether this is a vulnerability depends on:

  ```text
  Application Design

  Session Semantics

  Message Sensitivity

  Expected Revocation Behavior
  ```

---

## Authorization Testing

* Authentication answers:

  ```text
  Who are you?
  ```

* Authorization answers:

  ```text
  What are you allowed to do?
  ```

* WebSocket servers must enforce authorization on security-sensitive messages.

* Do not assume authorization is guaranteed simply because the handshake was authenticated.

---

## Object-Level Authorization

* Example baseline:

  ```json
  {
    "action": "get_document",
    "document_id": 500
  }
  ```

* User A owns:

  ```text
  document_id = 500
  ```

* User B owns:

  ```text
  document_id = 501
  ```

* Controlled test:

  ```text
  User B Session

  ↓

  Request document_id=500

  ↓

  Observe Server Response
  ```

* If User B receives User A's protected document, investigate for object-level authorization failure.

---

## IDOR/BOLA Testing Workflow

* Use:

  ```text
  Establish User A

  ↓

  Create or Identify Object A

  ↓

  Record Object ID

  ↓

  Establish User B

  ↓

  Capture Equivalent WebSocket Message

  ↓

  Replace Only Object ID

  ↓

  Send

  ↓

  Observe

  ↓

  Verify Ownership

  ↓

  Reproduce
  ```

* Maintain clear identity separation.

---

## Function-Level Authorization

* Messages may expose privileged actions.

* Example:

  ```json
  {
    "action": "delete_user",
    "user_id": 42
  }
  ```

* If this message is normally generated only by an administrator, ask:

  ```text
  Can a lower-privileged user construct and send the same message?
  ```

* Server-side authorization must enforce the privilege boundary.

---

## Hidden UI Does Not Equal Authorization

* A normal user may not see an administrative button.

* But the WebSocket message format may be discoverable through:

  ```text
  JavaScript

  Previous Traffic

  Documentation

  Application Behavior

  Authorized Admin Test Account
  ```

* The security question is:

  ```text
  Does the server reject unauthorized execution?
  ```

* UI restrictions alone are not sufficient access control.

---

## Subscription Authorization

* Real-time applications often use subscription messages.

* Example:

  ```json
  {
    "action": "subscribe",
    "channel": "account-100"
  }
  ```

* Test whether another authorized test account can change:

  ```text
  account-100

  →

  account-101
  ```

* Then determine whether unauthorized data is delivered.

---

## Private Channel Testing

* Investigate:

  ```text
  Chat Rooms

  Account Notifications

  Support Conversations

  Private Documents

  Team Channels

  Administrative Events
  ```

* Ask:

  ```text
  Is channel membership validated?

  Is access checked only at connection time?

  Can arbitrary channel identifiers be supplied?

  Does the server leak existence information?
  ```

---

## Cross-Site WebSocket Hijacking

* **Cross-Site WebSocket Hijacking (CSWSH)** may occur when a WebSocket handshake relies on automatically sent credentials, such as cookies, but does not adequately validate the request's origin.

* Conceptually:

  ```text
  Victim Logged Into Target

  ↓

  Victim Visits Attacker-Controlled Page

  ↓

  Page Opens WebSocket to Target

  ↓

  Browser Automatically Sends Relevant Cookies

  ↓

  Target Accepts Connection

  ↓

  Attacker-Controlled Page Interacts Through Victim Context
  ```

* The exact exploitability depends on browser behavior, cookie configuration, handshake requirements, origin validation, and application protocol.

---

## Origin Validation

* The WebSocket handshake may include:

  ```http
  Origin: https://example.test
  ```

* The server can use this to determine whether the connection originates from an expected web origin.

* Test questions include:

  ```text
  Is Origin required?

  Is Origin validated?

  Is arbitrary Origin accepted?

  Is missing Origin accepted?

  Is validation exact?

  Are untrusted subdomains accepted?
  ```

* Perform only within authorized scope.

---

## Origin Testing Workflow

* Use:

  ```text
  Capture Valid Handshake

  ↓

  Record Valid Origin

  ↓

  Establish Baseline

  ↓

  Modify Only Origin

  ↓

  Reconnect

  ↓

  Observe Upgrade Result

  ↓

  Test Whether Sensitive Messages Are Accepted
  ```

* Do not conclude vulnerability solely because:

  ```text
  101 Switching Protocols
  ```

* Confirm security impact.

---

## Cookie-Based Authentication and CSWSH

* Risk may increase when:

  ```text
  Browser Automatically Sends Authentication Cookie

  +

  Server Does Not Validate Origin

  +

  No Additional Unpredictable Authentication Material Is Required

  +

  WebSocket Supports Sensitive Actions or Data
  ```

* Validate the complete attack chain before reporting.

---

## Input Validation Testing

* WebSocket inputs should receive the same security scrutiny as HTTP inputs.

* Potential classes include:

  ```text
  Injection

  XSS

  Server-Side Template Injection

  Command Injection

  Path Traversal

  SQL Injection

  NoSQL Injection

  XML-Related Issues

  Business Logic Manipulation
  ```

* The actual test depends on how the server processes the message.

---

## Input Testing Methodology

* Use:

  ```text
  Identify Input

  ↓

  Understand Expected Type

  ↓

  Establish Baseline

  ↓

  Modify One Input

  ↓

  Observe Immediate Response

  ↓

  Observe Downstream Effect

  ↓

  Validate Safely
  ```

* Do not blindly inject payloads into every field.

---

## Stored WebSocket Data

* A WebSocket message may be stored and later rendered elsewhere.

* Example:

  ```text
  User Sends Chat Message

  ↓

  Server Stores Message

  ↓

  Another User Opens Chat

  ↓

  Stored Content Renders
  ```

* Therefore, testing may require observing:

  ```text
  Immediate WebSocket Response

  +

  Later HTTP Page

  +

  Another WebSocket Client
  ```

---

## WebSocket XSS Investigation

* Example workflow:

  ```text
  Identify User-Controlled Message Field

  ↓

  Send Harmless Marker

  ↓

  Determine Where It Appears

  ↓

  Identify Rendering Context

  ↓

  Test Appropriate Safe Payload

  ↓

  Validate Execution Context
  ```

* Do not assume that message reflection alone proves XSS.

---

## Message Type Manipulation

* Example:

  ```json
  {
    "type": "message",
    "text": "hello"
  }
  ```

* Test whether other known message types can be supplied.

* Example:

  ```text
  message

  notification

  admin_action

  system

  update
  ```

* Only test message types discovered through authorized application analysis.

---

## Parameter Removal

* Remove one field at a time.

* Example:

  ```json
  {
    "action": "update_profile",
    "user_id": 42,
    "name": "Alice"
  }
  ```

* Test without:

  ```text
  user_id
  ```

* Ask:

  ```text
  Does the server derive identity from the session?

  Does it use a default?

  Does it trust another field?

  Does behavior become inconsistent?
  ```

---

## Extra Parameter Testing

* Applications may accept undocumented fields.

* Example baseline:

  ```json
  {
    "name": "Alice"
  }
  ```

* If application evidence suggests additional fields exist, test controlled additions such as:

  ```json
  {
    "name": "Alice",
    "display_mode": "compact"
  }
  ```

* For security-sensitive fields, only test when justified by observed application behavior.

---

## Type Confusion Testing

* Example expected value:

  ```json
  {
    "quantity": 1
  }
  ```

* Controlled variations may include:

  ```text
  String

  Number

  Boolean

  Null

  Array

  Object
  ```

* Example:

  ```json
  {
    "quantity": "1"
  }
  ```

* Observe whether inconsistent type handling affects security logic.

---

## Numeric Boundary Testing

* For numeric fields, consider:

  ```text
  0

  Negative Values

  Maximum Expected Value

  Large Values

  Decimal Values

  Boundary Values
  ```

* Relevant areas include:

  ```text
  Quantity

  Price

  Credit

  Limits

  Pagination

  Object IDs
  ```

* Avoid destructive tests on production systems.

---

## Message Replay Testing

* Replaying a valid message can reveal whether an operation is idempotent.

* Example:

  ```text
  Original:

  Apply Coupon
  ```

* Replay:

  ```text
  Apply Coupon Again
  ```

* Questions:

  ```text
  Is the action duplicated?

  Is state checked?

  Is replay detected?

  Can value be applied multiple times?
  ```

---

## Duplicate Action Testing

* Relevant workflows may include:

  ```text
  Redeem Reward

  Submit Vote

  Claim Bonus

  Send Payment Instruction

  Apply Discount

  Accept Invitation

  Perform State Transition
  ```

* Use non-destructive lab scenarios whenever possible.

---

## Message Ordering

* Some workflows depend on message order.

* Expected:

  ```text
  A

  ↓

  B

  ↓

  C
  ```

* Test whether:

  ```text
  C

  ↓

  A
  ```

* Or:

  ```text
  A

  ↓

  C
  ```

* Produces unintended behavior.

---

## State Machine Testing

* Model the application as states.

* Example:

  ```text
  Created

  ↓

  Approved

  ↓

  Paid

  ↓

  Completed
  ```

* Test whether the server permits:

  ```text
  Created

  ↓

  Completed
  ```

* Without required intermediate authorization or validation.

---

## Business Logic Testing

* WebSocket workflows may contain logic flaws unrelated to traditional injection.

* Examples:

  ```text
  Skipping Required Steps

  Repeating One-Time Actions

  Manipulating State

  Acting on Another User's Object

  Subscribing to Unauthorized Channels

  Sending Messages Out of Sequence

  Reusing Expired Actions
  ```

* Understand the workflow before testing it.

---

## Race Condition Considerations

* Persistent connections may support rapid or concurrent message submission.

* Security-sensitive workflows may require investigation for:

  ```text
  Duplicate Redemption

  Double Spending

  Repeated State Transition

  Concurrent Modification

  Limit Bypass
  ```

* Race-condition testing should be performed carefully and only where authorized.

* Avoid uncontrolled high-volume testing against production systems.

---

## HTTP and WebSocket Correlation

* Many applications use both protocols.

* Example:

  ```text
  HTTP:

  POST /api/create-order

  ↓

  order_id=7319

  ↓

  WebSocket:

  {"action":"subscribe","order_id":7319}

  ↓

  WebSocket:

  {"type":"order_update","status":"paid"}
  ```

* Testing only one protocol may miss the full workflow.

---

## Authentication Across HTTP and WebSocket

* Example:

  ```text
  HTTP Login

  ↓

  Session Cookie Created

  ↓

  WebSocket Handshake Uses Cookie

  ↓

  Persistent Connection Established

  ↓

  HTTP Logout
  ```

* Then ask:

  ```text
  What happens to the WebSocket?
  ```

* Cross-protocol state is important.

---

## Object Lifecycle Across Protocols

* Example:

  ```text
  HTTP Creates Object

  ↓

  WebSocket Updates Object

  ↓

  HTTP Retrieves Object

  ↓

  WebSocket Deletes Object
  ```

* Build one unified application model rather than treating HTTP and WebSocket testing as unrelated activities.

---

## Session Rotation and Existing Connections

* Suppose:

  ```text
  Session A

  ↓

  WebSocket Connected

  ↓

  HTTP Reauthentication

  ↓

  Session B
  ```

* Investigate:

  ```text
  Does old WebSocket remain authenticated as Session A?

  Does it migrate to Session B?

  Is it disconnected?

  Can both sessions remain active?
  ```

* Interpret results according to intended application behavior.

---

## Multi-User Testing

* Use clearly separated sessions.

* Example:

  ```text
  Browser A

  User A

  Session A

  WebSocket A
  ```

  ```text
  Browser B

  User B

  Session B

  WebSocket B
  ```

* Avoid accidental session mixing.

---

## Multi-User Message Comparison

* Capture equivalent messages.

* User A:

  ```json
  {
    "action": "get_profile",
    "user_id": 100
  }
  ```

* User B:

  ```json
  {
    "action": "get_profile",
    "user_id": 200
  }
  ```

* Compare:

  ```text
  Message Structure

  Object Identifiers

  Authentication Context

  Server Responses
  ```

* Then design one controlled authorization test.

---

## Identity Must Remain Explicit

* Before each sensitive test, know:

  ```text
  Which User?

  Which Role?

  Which Session?

  Which WebSocket Connection?

  Which Object Owner?
  ```

* Persistent connections make accidental identity confusion particularly dangerous.

---

## Connection Reuse

* A WebSocket connection may remain active for a long time.

* State can change during that lifetime.

* Track:

  ```text
  Connection Open Time

  Authentication State

  Session Changes

  Role Changes

  Logout Events

  Token Expiration

  Reauthentication
  ```

---

## Reconnection Behavior

* Test normal reconnection behavior.

* Example:

  ```text
  Connection Lost

  ↓

  Browser Reconnects

  ↓

  Authentication Restored

  ↓

  Subscriptions Restored
  ```

* Investigate whether reconnection:

  ```text
  Uses Stale Credentials

  Restores Unauthorized Subscriptions

  Replays Sensitive Actions

  Changes Identity
  ```

---

## Error Message Analysis

* Malformed or unauthorized messages may produce useful responses.

* Example:

  ```json
  {
    "error": "channel 482 belongs to account 91"
  }
  ```

* This may disclose:

  ```text
  Internal IDs

  Object Relationships

  Backend Logic

  Validation Rules
  ```

* Record error differences carefully.

---

## Differential Response Analysis

* Compare:

  ```text
  Valid Object

  Invalid Object

  Another User's Object

  Missing Object

  Malformed Object
  ```

* Observe:

  ```text
  Message Type

  Error Text

  Timing

  Connection Behavior

  Data Returned
  ```

* Differences may reveal authorization or enumeration behavior.

---

## Connection Closure as a Signal

* The server may respond to invalid messages by:

  ```text
  Returning Error

  Ignoring Message

  Closing Connection

  Reauthenticating

  Rate Limiting
  ```

* Connection closure itself can be meaningful.

---

## Rate Limiting

* WebSocket actions may require independent rate limiting.

* Example:

  ```text
  HTTP Endpoint:

  Rate Limited
  ```

* But equivalent WebSocket action:

  ```text
  Not Rate Limited
  ```

* Test only at safe, authorized volumes.

* Do not perform uncontrolled denial-of-service testing.

---

## Message Size Handling

* Applications should handle unexpected message sizes safely.

* During authorized testing, use conservative size variations.

* Observe:

  ```text
  Validation Error

  Connection Closure

  Server Error

  Processing Delay
  ```

* Avoid excessive payload sizes that could create availability impact.

---

## Binary WebSocket Messages

* Some applications use binary frames.

* Analysis may require understanding:

  ```text
  Serialization Format

  Compression

  Protocol Buffers

  MessagePack

  Custom Binary Protocol
  ```

* Do not blindly mutate binary data.

* First determine the message structure.

---

## Encoded Messages

* Messages may contain:

  ```text
  Base64

  URL Encoding

  JWTs

  Compressed Data

  Nested JSON Strings
  ```

* Decode only as necessary to understand structure.

* Then determine whether the server validates the decoded content securely.

---

## Client-Side JavaScript Analysis

* JavaScript can reveal WebSocket behavior.

* Search for:

  ```text
  new WebSocket(

  wss://

  ws://

  send(

  onmessage

  message types

  action names

  channel names
  ```

* This may reveal functionality not immediately visible in normal UI testing.

---

## Discovering Hidden Message Types

* Application JavaScript may contain handlers such as:

  ```text
  case "notification":

  case "message":

  case "admin_event":

  case "user_update":
  ```

* Treat these as investigation leads.

* Do not assume every discovered message type is reachable or exploitable.

---

## WebSocket Testing With Logger

* Logger can help correlate:

  ```text
  Handshake Requests

  Authentication Traffic

  HTTP Actions

  Session Refresh

  Related Burp Activity
  ```

* Combine Logger with WebSocket message history to reconstruct the complete workflow.

---

## Example Unified Timeline

* Example:

  ```text
  T1 — HTTP POST /login

  T2 — Session Cookie Issued

  T3 — HTTP WebSocket Upgrade

  T4 — WebSocket authenticate message

  T5 — WebSocket subscribe message

  T6 — HTTP POST /api/action

  T7 — WebSocket notification received

  T8 — HTTP logout

  T9 — WebSocket message sent after logout
  ```

* This timeline may reveal security behavior that no single traffic view shows alone.

---

## WebSocket Testing With Match and Replace

* Match and Replace primarily concerns HTTP messages and configured traffic transformations.

* Be careful when modifying handshake-related values such as:

  ```text
  Cookie

  Origin

  Authorization

  Custom Headers
  ```

* A forgotten rule may change the WebSocket authentication context.

* Always inspect the effective handshake.

---

## WebSocket Testing With Session Handling

* If session-handling automation modifies cookies used by the WebSocket handshake:

  ```text
  Expected User A

  ↓

  Session Rule

  ↓

  Handshake Uses User B
  ```

* Your WebSocket identity may differ from what you intended.

* Verify identity after connection establishment.

---

## WebSocket Testing With Extensions

* Extensions may:

  ```text
  Analyze Messages

  Modify Messages

  Generate Traffic

  Add Custom Testing Features
  ```

* Review extension behavior before using it on sensitive WebSocket workflows.

* Prefer minimal tooling when establishing a baseline.

---

## WebSocket Security Testing Workflow

* Use:

  ```text
  Identify WebSocket Connection

  ↓

  Analyze Handshake

  ↓

  Determine Authentication

  ↓

  Capture Message History

  ↓

  Build Message Inventory

  ↓

  Map Actions and Parameters

  ↓

  Establish Baseline

  ↓

  Test Authentication

  ↓

  Test Authorization

  ↓

  Test Input Handling

  ↓

  Test State and Business Logic

  ↓

  Test Origin Controls

  ↓

  Correlate With HTTP

  ↓

  Reproduce Finding

  ↓

  Preserve Evidence
  ```

---

## WebSocket Attack Surface Map

* Document:

  ```text
  Connection URL:

  Handshake Authentication:

  Origin:

  Cookies:

  Authorization:

  Query Parameters:

  Subprotocol:

  Client Message Types:

  Server Message Types:

  Object IDs:

  User IDs:

  Channel IDs:

  Sensitive Actions:

  Subscription Actions:

  State Transitions:

  Input Fields:

  Error Messages:

  Reconnection Behavior:

  Logout Behavior:

  HTTP Dependencies:
  ```

---

## WebSocket Testing Checklist

* Verify:

  ```text
  [ ] WebSocket endpoints identified

  [ ] Handshake captured

  [ ] Authentication mechanism understood

  [ ] Origin behavior tested where appropriate

  [ ] Message types inventoried

  [ ] Client-to-server messages mapped

  [ ] Server-to-client messages mapped

  [ ] Object identifiers identified

  [ ] User-controlled fields identified

  [ ] Baseline behavior recorded

  [ ] Authentication tested

  [ ] Authorization tested

  [ ] Subscription authorization tested

  [ ] Input validation tested selectively

  [ ] Replay behavior considered

  [ ] Message ordering considered

  [ ] Business logic considered

  [ ] Logout behavior checked

  [ ] Reconnection behavior checked

  [ ] HTTP and WebSocket traffic correlated

  [ ] Identity remained explicit

  [ ] Final findings reproduced cleanly
  ```

---

## WebSocket Investigation Card

* Record:

  ```text
  Application:

  WebSocket URL:

  Connection Purpose:

  Handshake Request:

  Handshake Response:

  Authentication Mechanism:

  User:

  Role:

  Session:

  Origin:

  Subprotocol:

  Client Message:

  Server Response:

  Action:

  Parameters:

  Object ID:

  Object Owner:

  Baseline:

  Modified Field:

  Modified Value:

  Result:

  Authorization Result:

  Connection Closed?:

  State Changed?:

  Related HTTP Request:

  Security Impact:

  Reproduced?:

  Evidence Preserved?:

  Follow-Up:
  ```

---

## Testing Strategy Matrix

| Scenario                       | Primary Test                         |
| ------------------------------ | ------------------------------------ |
| Cookie-authenticated WebSocket | Authentication and Origin validation |
| Private channel subscription   | Subscription authorization           |
| Object ID in message           | Object-level authorization           |
| Admin-only action type         | Function-level authorization         |
| User-controlled text           | Context-aware input testing          |
| One-time action                | Replay testing                       |
| Multi-step workflow            | State-machine testing                |
| HTTP logout with open socket   | Session revocation behavior          |
| Token rotation                 | Existing connection behavior         |
| Multiple test users            | Cross-user authorization             |
| Hidden message types           | Server-side authorization            |
| Unexpected messages            | HTTP/WebSocket timeline correlation  |
| Binary protocol                | Decode structure before mutation     |

---

## Troubleshooting — No WebSocket Messages Appear

* Check:

  ```text
  Does Application Actually Use WebSockets?

  ↓

  Was Relevant Feature Triggered?

  ↓

  Was Upgrade Successful?

  ↓

  Is Correct WebSocket History Open?

  ↓

  Is Traffic Using Another Protocol?
  ```

---

## Troubleshooting — Handshake Fails

* Compare with the working baseline.

* Check:

  ```text
  Cookie

  Authorization

  Origin

  Host

  Query Parameters

  Required Headers

  Token Expiration

  Subprotocol

  Session State
  ```

---

## Troubleshooting — 101 Received but Messages Fail

* A successful handshake does not guarantee authorization for application messages.

* Check:

  ```text
  Post-Connection Authentication Required?

  ↓

  Subscription Required?

  ↓

  Message Format Correct?

  ↓

  Current Identity Authorized?

  ↓

  Required State Established?
  ```

---

## Troubleshooting — Modified Message Is Ignored

* Check:

  ```text
  Correct Connection?

  Correct Direction?

  Valid JSON / Format?

  Required Field Missing?

  Wrong Message Type?

  State Requirement?

  Sequence Requirement?

  Authorization Failure?
  ```

---

## Troubleshooting — Connection Closes After Modification

* Investigate:

  ```text
  Malformed Message

  Protocol Violation

  Authentication Failure

  Authorization Failure

  Invalid Message Type

  Server Defensive Behavior

  Rate Limit
  ```

* Compare with one minimal change from baseline.

---

## Troubleshooting — Wrong User Data Appears

* Immediately verify:

  ```text
  Current User

  Current Session

  WebSocket Connection Identity

  Subscription

  Object Ownership

  Session Automation

  Match and Replace Rules
  ```

* Determine whether this is:

  ```text
  Real Authorization Failure

  or

  Testing Identity Contamination
  ```

---

## Troubleshooting — Browser Works but Replayed Message Fails

* Compare:

  ```text
  Connection State

  Authentication State

  Previous Messages

  Subscription State

  Required Sequence

  Dynamic Tokens

  Object State
  ```

* The message may depend on prior workflow state.

---

## Troubleshooting — Different Results After Reconnection

* Track:

  ```text
  Old Connection Identity

  New Connection Identity

  Session Cookie

  Token

  Subscriptions

  Server-Side State
  ```

* Reconnection may establish a different security context.

---

## Evidence Collection

* For a WebSocket finding, preserve:

  ```text
  Handshake

  Authentication Context

  Relevant Original Message

  Modified Message

  Server Response

  Object Ownership

  User / Role

  Required Previous Messages

  HTTP Preconditions

  Resulting State Change
  ```

* A single WebSocket message may not be enough to reproduce the finding.

---

## Authorization Evidence Example

* Document:

  ```text
  User A owns document 500.

  ↓

  User B establishes authenticated WebSocket.

  ↓

  User B sends:

  {"action":"get_document","document_id":500}

  ↓

  Server returns User A's protected document.

  ↓

  Control Test:

  User B requests own document 501.

  ↓

  Compare behavior.
  ```

* This establishes:

  ```text
  Ownership

  +

  Identity

  +

  Unauthorized Action

  +

  Server Result
  ```

---

## Clean Reproduction

* Before finalizing a finding:

  ```text
  Disable Unrelated Extensions

  ↓

  Disable Unrelated Automation

  ↓

  Reconnect Fresh Session

  ↓

  Confirm User Identity

  ↓

  Reproduce Minimal Message Sequence

  ↓

  Capture Evidence

  ↓

  Repeat Control Test
  ```

* This reduces false conclusions.

---

## Practical Exercise

* Use a deliberately vulnerable WebSocket lab or another system you are explicitly authorized to test.

### Task 1 — Identify the WebSocket

* Trigger a real-time application feature.

* Record:

  ```text
  WebSocket URL:

  Upgrade Endpoint:

  Status:

  Origin:

  Authentication:
  ```

---

### Task 2 — Analyze the Handshake

* Identify:

  ```text
  Cookies:

  Authorization:

  Query Parameters:

  Origin:

  Subprotocol:

  Other Security-Relevant Headers:
  ```

---

### Task 3 — Capture Message History

* Perform one normal action.

* Record:

  ```text
  Client Message:

  Server Message:

  Direction:

  Action:

  Important Parameters:
  ```

---

### Task 4 — Build Message Inventory

* Trigger several application features.

* Create:

  ```text
  Message Type 1:

  Message Type 2:

  Message Type 3:

  Sensitive Parameters:

  Object IDs:
  ```

---

### Task 5 — Modify One Harmless Field

* Capture one message.

* Change one non-destructive value.

* Record:

  ```text
  Original:

  Modified:

  Response:

  Effect:
  ```

---

### Task 6 — Replay a Message

* Replay a safe action in the lab.

* Determine:

  ```text
  Was Action Repeated?

  Was Replay Rejected?

  Did State Change?

  Was Response Different?
  ```

---

### Task 7 — Test Authorization

* Using two authorized lab accounts:

  ```text
  User A

  User B
  ```

* Identify one object belonging to User A.

* Attempt the equivalent safe read operation as User B by modifying only the object identifier.

* Record the result.

---

### Task 8 — Correlate HTTP and WebSocket Traffic

* Identify one workflow involving both protocols.

* Build:

  ```text
  T1 — HTTP

  T2 — HTTP

  T3 — WebSocket

  T4 — WebSocket

  T5 — HTTP
  ```

* Explain how the requests and messages relate.

---

### Task 9 — Test Logout Behavior

* In the lab:

  ```text
  Login

  ↓

  Open WebSocket

  ↓

  Logout

  ↓

  Observe Existing Connection
  ```

* Record whether the connection:

  ```text
  Closes

  Remains Open but Unauthorized

  Remains Fully Authorized

  Requires Reauthentication
  ```

---

### Task 10 — Restore Clean State

* Close test connections.

* Disable temporary rules.

* Confirm the application returns to normal baseline behavior.

---

## Investigation Journal

* Record:

  ```text
  Application:

  WebSocket Endpoint:

  Connection Purpose:

  Handshake:

  Authentication:

  Origin:

  Session:

  User:

  Role:

  Message Types:

  Client Messages:

  Server Messages:

  Sensitive Actions:

  Object IDs:

  Channel IDs:

  User-Controlled Inputs:

  Baseline:

  Modified Messages:

  Authorization Tests:

  Subscription Tests:

  Replay Tests:

  State Tests:

  Origin Tests:

  Logout Behavior:

  Reconnection Behavior:

  Related HTTP Traffic:

  Unexpected Behavior:

  Security Impact:

  Reproduced:

  Evidence:

  Follow-Up:
  ```

---

## Common Mistakes

* Common mistakes include:

  * Testing only HTTP traffic and ignoring WebSocket messages.
  * Assuming a successful handshake means sensitive actions are authorized.
  * Ignoring authentication material in the handshake.
  * Failing to test authorization at the message level.
  * Confusing hidden UI functionality with server-side access control.
  * Changing multiple message fields simultaneously.
  * Ignoring subscription authorization.
  * Treating arbitrary Origin acceptance as automatically exploitable without validating the full CSWSH conditions.
  * Ignoring WebSocket connections after HTTP logout.
  * Forgetting that persistent connections may retain old authentication state.
  * Mixing multiple test-user sessions.
  * Replaying state-changing messages without considering side effects.
  * Blindly fuzzing binary or unknown message formats.
  * Ignoring message ordering and application state.
  * Testing WebSockets separately from related HTTP workflows.
  * Reporting reflected data without validating actual security impact.
  * Failing to preserve the handshake and prerequisite message sequence as evidence.

---

## Interview Questions

1. What is a WebSocket?
2. How does WebSocket communication differ from traditional HTTP?
3. How is a WebSocket connection established?
4. What does `101 Switching Protocols` indicate?
5. Which handshake headers are security-relevant?
6. Why is the `Origin` header important in WebSocket security?
7. How can you identify WebSocket traffic in Burp Suite?
8. What is the difference between client-to-server and server-to-client WebSocket messages?
9. Why should WebSocket messages be treated similarly to API requests during security testing?
10. How would you build a WebSocket message inventory?
11. How would you test authentication on a WebSocket endpoint?
12. Why does a successful connection not prove that sensitive actions are authorized?
13. How would you test object-level authorization in WebSocket messages?
14. What is subscription authorization?
15. What is Cross-Site WebSocket Hijacking?
16. Under what conditions can cookie-based WebSocket authentication contribute to CSWSH risk?
17. Why is accepting an arbitrary Origin not necessarily enough to prove exploitability?
18. How would you test logout behavior for an existing WebSocket connection?
19. Why are persistent connections important when analyzing session revocation?
20. How can message replay reveal business logic flaws?
21. Why should message ordering be tested?
22. What is state-machine testing in a WebSocket workflow?
23. How can HTTP and WebSocket traffic interact in one application workflow?
24. Why is multi-user session separation important?
25. How can JavaScript analysis reveal hidden WebSocket functionality?
26. What should you do before modifying binary WebSocket messages?
27. How can error messages reveal useful security information?
28. What evidence should be preserved for a WebSocket authorization vulnerability?
29. Why should one field be changed at a time?
30. What is the correct overall methodology for testing WebSocket applications?

---

## Quick Revision

* Connection lifecycle:

  ```text
  HTTP Upgrade

  ↓

  101 Switching Protocols

  ↓

  Persistent WebSocket

  ↓

  Bidirectional Messages
  ```

* Testing model:

  ```text
  Handshake

  +

  Authentication

  +

  Messages

  +

  Authorization

  +

  State
  ```

* Message workflow:

  ```text
  Capture

  ↓

  Understand

  ↓

  Modify One Field

  ↓

  Send

  ↓

  Observe

  ↓

  Validate
  ```

* Authorization workflow:

  ```text
  User A Owns Object

  ↓

  User B Authenticated

  ↓

  Modify Only Object ID

  ↓

  Send Message

  ↓

  Validate Server Enforcement
  ```

* CSWSH model:

  ```text
  Cookie-Based Authentication

  +

  Cross-Site Connection Possible

  +

  Weak Origin Validation

  +

  Sensitive WebSocket Capability

  =

  Potential CSWSH Risk
  ```

* Cross-protocol model:

  ```text
  HTTP

  ⇅

  Shared Authentication and State

  ⇅

  WebSocket
  ```

* Remember:

  ```text
  101 Switching Protocols

  ≠

  Authorized Sensitive Action
  ```

  ```text
  Hidden UI

  ≠

  Server-Side Authorization
  ```

  ```text
  Open WebSocket

  ≠

  Current Valid Session
  ```

  ```text
  Message Reflection

  ≠

  Confirmed Vulnerability
  ```

* Professional principle:

  ```text
  Understand Protocol

  +

  Track Identity

  +

  Map Messages

  +

  Test Server-Side Boundaries

  +

  Correlate State

  =

  Reliable WebSocket Testing
  ```

---

## Key Takeaways

* WebSockets provide persistent, bidirectional communication and require a different testing model from traditional HTTP.
* WebSocket connections begin with an HTTP upgrade handshake, commonly resulting in `101 Switching Protocols`.
* Analyze both the handshake and the subsequent message stream.
* Authentication may occur during the handshake, after connection establishment, or through a combination of mechanisms.
* A successful WebSocket connection does not prove that sensitive messages are properly authorized.
* Build an inventory of client and server message types before performing deeper testing.
* Treat structured WebSocket messages similarly to API requests by identifying actions, parameters, object IDs, and security boundaries.
* Change one message field at a time and compare against a known baseline.
* Test object-level and function-level authorization at the server side.
* Subscription mechanisms must enforce authorization before delivering private data.
* Cross-Site WebSocket Hijacking requires analysis of cookies, Origin validation, browser behavior, authentication requirements, and actual security impact.
* Persistent WebSocket connections should be evaluated across logout, token rotation, role changes, and reconnection.
* Replay and message-order testing can reveal business logic and state-machine flaws.
* Input validation testing should be targeted according to how message fields are processed.
* HTTP and WebSocket traffic often form one shared application workflow and should be analyzed together.
* Multi-user testing requires strict separation of identities, sessions, connections, and object ownership.
* JavaScript analysis can reveal hidden endpoints, message types, and application protocol details.
* Binary or encoded messages should be understood before mutation.
* Logger and WebSocket history can be combined to reconstruct complete cross-protocol timelines.
* Reliable WebSocket findings require clean reproduction with clear identity, ownership, handshake state, message sequence, and server-side impact.
