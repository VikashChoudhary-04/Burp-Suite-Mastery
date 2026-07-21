# WebSocket Testing with Burp Suite

> **Module 16 — Modern Application Testing**

> **Progress**

```text
███████░░░░░░░░ 7 / 15 Files
```

---

## Overview

* Modern applications often require communication that is more interactive than traditional HTTP request-response traffic.

* Examples include:

  * Real-time chat.
  * Notifications.
  * Live dashboards.
  * Collaborative editing.
  * Trading interfaces.
  * Multiplayer applications.
  * Live status updates.
  * GraphQL subscriptions.

* WebSockets allow a browser and server to maintain a persistent, bidirectional communication channel.

* A simplified flow looks like:

  ```text
  Browser

  ↓

  HTTP WebSocket Handshake

  ↓

  Connection Upgraded

  ↓

  Persistent WebSocket Connection

  ↕

  Messages Flow in Both Directions
  ```

* WebSocket security testing requires understanding two distinct stages:

  ```text
  Connection Establishment

  +

  Message-Level Communication
  ```

* Burp Suite allows you to inspect both the initial handshake and the messages exchanged after the connection is established.

---

## Learning Objectives

* By the end of this lesson, you will be able to:

  * Understand what WebSockets are.
  * Understand the WebSocket handshake.
  * Distinguish HTTP traffic from WebSocket messages.
  * Identify WebSocket connections in Burp Suite.
  * Understand WebSocket message history.
  * Intercept and modify WebSocket messages.
  * Establish clean baseline messages.
  * Identify authentication and authorization context.
  * Map WebSocket message types and actions.
  * Identify user-controlled fields and object identifiers.
  * Test message-level authorization systematically.
  * Understand cross-site WebSocket hijacking.
  * Analyze Origin validation and cookie-based authentication.
  * Test message validation and state transitions.
  * Recognize replay and duplicate-action risks.
  * Build a structured WebSocket testing workflow.

---

## What Is a WebSocket?

* A WebSocket is a protocol that provides persistent two-way communication between a client and a server.

* Traditional HTTP commonly follows:

  ```text
  Client

  ↓ Request

  Server

  ↓ Response

  Client
  ```

* Each interaction generally follows a request-response model.

* WebSockets instead allow:

  ```text
  Client

  ↕

  Server
  ```

* Either side can send messages while the connection remains open.

---

## Why Applications Use WebSockets

* WebSockets are useful when applications require low-latency or real-time communication.

* Examples:

  ```text
  Chat Messages

  Notifications

  Live Prices

  Game Events

  Collaborative Updates

  Support Conversations

  Live Monitoring

  Background Status Changes
  ```

---

## HTTP vs WebSocket

| HTTP                               | WebSocket                                |
| ---------------------------------- | ---------------------------------------- |
| Request-response model             | Persistent bidirectional connection      |
| Client normally initiates requests | Client and server can both send messages |
| Individual HTTP requests           | Messages over an established connection  |
| Repeated HTTP overhead             | Long-lived communication channel         |

* WebSockets still begin with an HTTP request.

---

## The WebSocket Handshake

* A WebSocket connection normally begins with an HTTP handshake.

* Example:

  ```http
  GET /chat HTTP/1.1
  Host: example.test
  Upgrade: websocket
  Connection: Upgrade
  Sec-WebSocket-Key: <value>
  Sec-WebSocket-Version: 13
  Origin: https://example.test
  Cookie: session=<session>
  ```

* Important headers include:

  ```text
  Upgrade

  Connection

  Sec-WebSocket-Key

  Sec-WebSocket-Version

  Origin

  Cookie

  Authorization
  ```

---

## Server Upgrade Response

* A successful handshake commonly returns:

  ```http
  HTTP/1.1 101 Switching Protocols
  Upgrade: websocket
  Connection: Upgrade
  Sec-WebSocket-Accept: <value>
  ```

* The status:

  ```text
  101 Switching Protocols
  ```

* Indicates that the connection is switching from HTTP to WebSocket communication.

---

## Communication Lifecycle

```text
Browser

↓

HTTP Upgrade Request

↓

Server Validates Request

↓

101 Switching Protocols

↓

WebSocket Connection Established

↓

Client and Server Exchange Messages

↓

Connection Closed
```

---

## Two Security Layers

* WebSocket testing should examine:

  ```text
  1. Handshake Security

  2. Message Security
  ```

* A secure handshake does not guarantee secure message-level authorization.

* Likewise, secure message handling does not automatically mean the handshake is protected correctly.

---

## Finding WebSockets in Burp Suite

* Browse the application normally through Burp.

* Look for WebSocket-related traffic.

* Depending on the Burp interface and version, WebSocket traffic can be reviewed through relevant Proxy history and WebSocket message views.

* Look for handshake requests containing:

  ```text
  Upgrade: websocket
  ```

---

## WebSocket URLs

* WebSocket connections use:

  ```text
  ws://
  ```

* Or encrypted:

  ```text
  wss://
  ```

* Example:

  ```text
  wss://example.test/chat
  ```

---

## `ws://` vs `wss://`

* `ws://`:

  ```text
  Unencrypted WebSocket
  ```

* `wss://`:

  ```text
  WebSocket Over TLS
  ```

* For sensitive production communication, encrypted transport is generally expected.

---

## WebSocket Message History

* After a connection is established, messages may flow in both directions.

* Example client message:

  ```json
  {
    "type": "send_message",
    "recipient_id": 42,
    "message": "Hello"
  }
  ```

* Example server message:

  ```json
  {
    "type": "message_sent",
    "id": 781,
    "status": "delivered"
  }
  ```

* Burp can help inspect these messages.

---

## Message Directions

* Always distinguish:

  ```text
  Client → Server

  Server → Client
  ```

* Client-to-server messages often contain user-controlled actions.

* Server-to-client messages may expose:

  ```text
  Sensitive Data

  Object Identifiers

  Event Types

  Internal State

  Authorization Clues
  ```

---

## WebSocket Message Formats

* Messages may contain:

  ```text
  JSON

  Plain Text

  XML

  Encoded Data

  Binary Data

  GraphQL Messages

  Custom Protocols
  ```

* JSON is common but not universal.

---

## Example JSON Message

```json
{
  "action": "join_room",
  "room_id": 91
}
```

* Potential security-relevant fields:

  ```text
  action

  room_id
  ```

---

## WebSocket Attack-Surface Mapping

* For each message type, record:

  ```text
  Direction

  Message Type

  Action

  Object

  Object Identifier

  Parameters

  Authentication Context

  Role

  Tenant

  State Change

  Expected Authorization
  ```

---

## Message Type Inventory

| Direction       | Type             | Object  | State Change | Priority |
| --------------- | ---------------- | ------- | :----------: | :------: |
| Client → Server | `join_room`      | Room    |      Yes     |   High   |
| Client → Server | `send_message`   | Message |      Yes     |   High   |
| Server → Client | `notification`   | User    |      No      |  Medium  |
| Client → Server | `delete_message` | Message |      Yes     |   High   |

---

## Baseline First

* Before modifying a WebSocket message, preserve a legitimate baseline.

* Record:

  ```text
  Connection

  User

  Role

  Tenant

  Message

  Object

  Expected Result

  Server Response

  State Change
  ```

---

## Why Baselines Matter

* Without a baseline, unexpected behavior is difficult to interpret.

* Use:

  ```text
  Legitimate Message

  ↓

  Record Expected Behavior

  ↓

  Modify One Field

  ↓

  Send

  ↓

  Compare
  ```

---

## Intercepting WebSocket Messages

* Burp can intercept WebSocket messages so they can be inspected before forwarding.

* This allows controlled modification of:

  ```text
  Object IDs

  User IDs

  Message Content

  Action Names

  Parameters

  State Values
  ```

* Only modify traffic within authorized testing scope.

---

## Modify One Variable at a Time

* Avoid changing:

  ```text
  room_id

  +

  user_id

  +

  action

  +

  message
  ```

* Simultaneously.

* Prefer:

  ```text
  Baseline

  ↓

  Change room_id

  ↓

  Observe

  ↓

  Restore

  ↓

  Change Another Variable
  ```

---

## WebSocket Authentication

* Authentication may occur during:

  ```text
  Initial Handshake

  First WebSocket Message

  Token Message

  Existing Browser Session
  ```

---

## Cookie-Based Authentication

* A handshake may contain:

  ```http
  Cookie: session=<value>
  ```

* The server may associate the WebSocket connection with the authenticated browser session.

* Security questions include:

  ```text
  Is Authentication Required?

  Is the Session Validated?

  What Happens After Logout?

  What Happens When the Session Expires?

  Is the Connection Revoked?
  ```

---

## Token-Based Authentication

* Authentication may use:

  ```text
  Authorization Header

  Query Parameter

  Initial Authentication Message

  Subprotocol-Specific Token
  ```

* Example conceptual message:

  ```json
  {
    "type": "authenticate",
    "token": "<token>"
  }
  ```

---

## Authentication vs Authorization

* Authentication asks:

  ```text
  Who Is Connected?
  ```

* Authorization asks:

  ```text
  What Is This Connection Allowed to Do?
  ```

* A WebSocket can authenticate correctly while still failing to enforce authorization on individual messages.

---

## Message-Level Authorization

* Example:

  ```json
  {
    "action": "read_conversation",
    "conversation_id": 781
  }
  ```

* Security question:

  ```text
  Does the Server Verify That the Connected User
  Is Authorized to Access Conversation 781?
  ```

---

## Object-Level Authorization

* Suppose:

  ```text
  Conversation 781

  →

  User A
  ```

* And:

  ```text
  Conversation 905

  →

  User B
  ```

* With controlled test accounts, compare whether User A can request User B's object by changing only:

  ```text
  conversation_id
  ```

---

## Authorization Matrix

| Identity | Object        | Expected | Actual |
| -------- | ------------- | -------- | ------ |
| User A   | User A object | Allow    | ?      |
| User A   | User B object | Deny     | ?      |
| User B   | User B object | Allow    | ?      |
| User B   | User A object | Deny     | ?      |

---

## Function-Level Authorization

* Example message:

  ```json
  {
    "action": "delete_user",
    "user_id": 42
  }
  ```

* Expected:

  ```text
  Privileged Role Required
  ```

* Test whether the backend validates the role for the action rather than relying on the UI to hide privileged controls.

---

## Role-Based Testing

* With authorized controlled roles:

  ```text
  Standard User

  Moderator

  Administrator
  ```

* Compare which message types are accepted.

---

## Tenant Authorization

* WebSocket messages may contain:

  ```text
  organization_id

  tenant_id

  workspace_id

  room_id
  ```

* Security question:

  ```text
  Does the Server Independently Verify Tenant Membership?
  ```

---

## Subscription Authorization

* Real-time applications may allow users to subscribe to channels or topics.

* Example:

  ```json
  {
    "action": "subscribe",
    "channel": "account-42"
  }
  ```

* Security question:

  ```text
  Can a User Subscribe to a Channel
  Belonging to Another User or Tenant?
  ```

---

## Authorization Must Apply at Subscription Time

* The server should verify authorization when a client subscribes.

* But long-lived connections may also require continued security awareness if:

  ```text
  Permissions Change

  Session Expires

  User Logs Out

  Account Is Disabled
  ```

---

## Server-to-Client Data Exposure

* Do not inspect only client messages.

* Server messages may reveal:

  ```text
  Other Users' Data

  Hidden Object IDs

  Internal Events

  Sensitive Notifications

  Cross-Tenant Information
  ```

* Verify whether each connected user receives only authorized data.

---

## Origin Header

* The WebSocket handshake may include:

  ```http
  Origin: https://example.test
  ```

* Browsers automatically send an Origin header in relevant WebSocket handshake contexts.

* The server can use it as part of cross-origin protection.

---

## Cross-Site WebSocket Hijacking

* Cross-site WebSocket hijacking can occur when a WebSocket handshake:

  ```text
  Relies on Browser-Sent Credentials

  +

  Lacks Adequate Cross-Site Protection
  ```

* A malicious external page may attempt to establish a WebSocket connection using the victim's authenticated browser context.

---

## Typical Risk Conditions

* Risk may exist when:

  ```text
  Authentication Uses Cookies

  +

  Browser Sends Relevant Cookies

  +

  Server Accepts Untrusted Origin

  +

  No Additional Unpredictable Authentication / CSRF-Style Protection Exists
  ```

* Actual exploitability depends on browser behavior and application design.

---

## Origin Validation Testing

* Start with the legitimate handshake.

* Record:

  ```text
  Origin

  Cookies

  Authentication

  Response
  ```

* Controlled tests may compare behavior with an altered Origin value.

* Observe whether the server:

  ```text
  Rejects Connection

  Accepts Connection

  Requires Additional Authentication
  ```

---

## Important Principle

```text
Origin Accepted

≠

Automatically Exploitable
```

* You must determine whether:

  ```text
  Victim Credentials Are Included

  Sensitive Messages Can Be Sent or Read

  Meaningful Actions Are Possible
  ```

---

## SameSite Cookies

* Cookie behavior can affect whether browser credentials are available in cross-site scenarios.

* Consider:

  ```text
  SameSite

  Secure

  Domain

  Path
  ```

* Do not infer exploitability from Origin handling alone.

---

## Handshake Authorization

* The server may restrict which users can establish particular WebSocket connections.

* Example:

  ```text
  /admin/live-events
  ```

* Security question:

  ```text
  Is Access Checked During the Handshake?
  ```

---

## Handshake vs Message Authorization

* Even if:

  ```text
  User Is Allowed to Connect
  ```

* It does not mean:

  ```text
  User Is Allowed to Send Every Message
  ```

* Authorization must be enforced at the appropriate operation level.

---

## Message Validation

* WebSocket messages should be validated like any other untrusted input.

* Test relevant fields for:

  ```text
  Missing Values

  Null

  Empty Values

  Unexpected Types

  Boundary Values

  Invalid Enumerations

  Unexpected Object IDs
  ```

---

## Example

* Baseline:

  ```json
  {
    "action": "send_message",
    "room_id": 91,
    "message": "Hello"
  }
  ```

* Controlled tests might examine one field at a time.

---

## Missing Fields

* Remove one field.

* Observe whether the server:

  ```text
  Rejects

  Applies Default

  Produces Error

  Performs Unexpected Action
  ```

---

## Unexpected Fields

* Add a harmless unexpected property.

* Determine whether it is:

  ```text
  Ignored

  Rejected

  Processed

  Persisted
  ```

* This can reveal backend message-binding behavior.

---

## Type Validation

* Example expected:

  ```json
  {
    "room_id": 91
  }
  ```

* Controlled variation:

  ```json
  {
    "room_id": "91"
  }
  ```

* Observe:

  ```text
  Validation

  Coercion

  Error Handling

  Business Behavior
  ```

---

## Server-Controlled Fields

* A server message may contain:

  ```json
  {
    "message_id": 781,
    "sender_id": 42,
    "role": "user",
    "status": "sent"
  }
  ```

* Do not assume all these fields should be accepted from client messages.

* Determine which values are:

  ```text
  Client-Controlled

  vs

  Server-Controlled
  ```

---

## State-Changing Messages

* WebSocket actions may:

  ```text
  Send Messages

  Delete Objects

  Update Records

  Join Rooms

  Leave Rooms

  Approve Actions

  Trigger Transactions
  ```

* These require careful testing.

---

## State Testing Workflow

```text
Record Initial State

↓

Send One Controlled Message

↓

Observe Immediate Response

↓

Verify Final State

↓

Record Side Effects
```

---

## Message Replay

* Replaying a WebSocket message may cause:

  ```text
  Duplicate Messages

  Duplicate Actions

  Repeated Notifications

  Multiple Transactions

  Repeated State Changes
  ```

* Understand side effects before replaying.

---

## Replay Security Questions

```text
Can a Sensitive Action Be Repeated?

Should the Action Be One-Time?

Is Replay Detection Expected?

Does Repetition Cause Duplicate Effects?
```

---

## Message Ordering

* Some workflows depend on message order.

* Example:

  ```text
  Authenticate

  ↓

  Join Room

  ↓

  Send Message
  ```

* Security question:

  ```text
  Can Required Steps Be Skipped or Reordered?
  ```

---

## State Machine Testing

* Example expected workflow:

  ```text
  Connect

  ↓

  Authenticate

  ↓

  Subscribe

  ↓

  Perform Action
  ```

* Controlled tests may examine whether:

  ```text
  Action Before Authentication

  Subscribe Before Authorization

  Repeated Authentication

  Invalid Transition
  ```

* Is handled securely.

---

## Connection State

* Long-lived WebSocket connections introduce questions that short-lived HTTP requests may not.

* Example:

  ```text
  User Connects

  ↓

  User Logs Out in Another Tab

  ↓

  Existing WebSocket Remains Open
  ```

* Security question:

  ```text
  Does the Existing Connection Retain Privileges?
  ```

---

## Session Expiration

* Test within authorized environments whether an established connection behaves correctly after:

  ```text
  Session Expiry

  Logout

  Password Change

  Permission Change

  Account Disablement
  ```

* Expected behavior depends on application design and security requirements.

---

## Token Refresh

* Some applications refresh authentication while a WebSocket remains active.

* Record:

  ```text
  Token Lifecycle

  Reauthentication Messages

  Connection Reuse

  Expiry Handling
  ```

---

## GraphQL Subscriptions

* GraphQL subscriptions may operate over WebSockets.

* Messages may contain:

  ```text
  connection_init

  subscribe

  next

  error

  complete
  ```

* Exact protocol details vary.

---

## GraphQL Subscription Security

* Test:

  ```text
  Authentication During Connection

  Authorization During Subscription

  Object / Tenant Boundaries

  Sensitive Event Exposure

  Connection Revocation
  ```

---

## WebSocket Message Injection

* User-controlled message fields may eventually reach:

  ```text
  HTML Rendering

  Databases

  Commands

  Templates

  Logs

  Other Users
  ```

* Injection risk depends on how backend and frontend components process the data.

---

## Stored Data

* A WebSocket message may store content that is later delivered through:

  ```text
  WebSocket

  HTTP

  Email

  Notification

  Dashboard
  ```

* Trace the full data flow before evaluating impact.

---

## Output Encoding

* If user-controlled WebSocket data is rendered in a browser, verify whether the receiving client handles it safely.

* Security depends on:

  ```text
  Storage

  Transformation

  Output Context

  Encoding

  Sanitization
  ```

---

## Rate Limiting

* WebSocket connections may support many messages over one connection.

* Rate limiting may therefore need to operate at:

  ```text
  Connection Level

  Message Level

  Action Level

  User Level
  ```

---

## Safe Rate-Limit Testing

* Avoid flooding systems.

* Use small controlled tests to determine whether sensitive operations have reasonable protections.

* High-volume availability testing requires explicit authorization.

---

## Message Size

* Servers should handle unexpected message sizes safely.

* Do not send resource-exhausting payloads to production systems.

* Use controlled labs when exploring size-related behavior.

---

## Binary WebSocket Messages

* Some applications use binary messages.

* Analysis may require understanding:

  ```text
  Encoding

  Serialization

  Compression

  Custom Protocol
  ```

* Do not modify unknown binary formats blindly.

---

## WebSocket Extensions

* Handshakes may negotiate extensions.

* Example:

  ```text
  permessage-deflate
  ```

* Record negotiated behavior when relevant.

* Extension presence alone is not a vulnerability.

---

## WebSocket Subprotocols

* Handshakes may contain:

  ```http
  Sec-WebSocket-Protocol: <protocol>
  ```

* Subprotocols define higher-level communication conventions.

* Examples may include application-specific or GraphQL-related protocols.

---

## Subprotocol Security Questions

```text
Is the Expected Subprotocol Enforced?

Can an Unexpected Protocol Be Selected?

Does Authentication Depend on It?

Does Behavior Differ Across Protocols?
```

* Only test supported variations with a clear reason.

---

## Reconnection Behavior

* Applications may automatically reconnect after connection loss.

* Observe whether reconnection:

  ```text
  Reuses Old Credentials

  Refreshes Authentication

  Restores Subscriptions

  Revalidates Authorization
  ```

---

## WebSocket Architecture Map

```text
Browser

↓

HTTP Handshake

↓

Authentication

↓

WebSocket Connection

↓

Message Types

├── Authentication
├── Subscription
├── Read Actions
├── State Changes
└── Notifications

↓

Objects / Tenants / Roles
```

---

## WebSocket Connection Card

```text
URL:

Handshake Path:

Origin:

Authentication:

Cookie / Token:

User:

Role:

Tenant:

Subprotocol:

Extensions:

Connection Purpose:

Sensitive Data?:

State-Changing Actions?:

Scope Confirmed?:

Notes:
```

---

## WebSocket Message Card

```text
Direction:

Message Type:

Action:

Object:

Object ID:

Object Owner:

User:

Role:

Tenant:

Parameters:

State Changing?:

Expected Security Rule:

Baseline Message:

Modified Field:

Modified Message:

Server Response:

Final State:

Side Effects:

Evidence:

Conclusion:
```

---

## WebSocket Testing Workflow

```text
Define Scope

↓

Identify WebSocket Connection

↓

Capture Handshake

↓

Understand Authentication

↓

Record Origin Behavior

↓

Map Message Types

↓

Identify Objects and Parameters

↓

Map Roles / Tenants

↓

Preserve Baselines

↓

Define Security Hypothesis

↓

Modify One Variable

↓

Send Controlled Message

↓

Observe Response

↓

Verify Server State

↓

Test Session Lifecycle

↓

Validate Impact

↓

Document Evidence
```

---

## Step 1 — Identify Connections

* Browse all relevant application functionality.

* Record:

  ```text
  WebSocket URL

  Path

  Purpose

  Scope
  ```

---

## Step 2 — Analyze Handshake

* Record:

  ```text
  Origin

  Cookies

  Authorization

  Subprotocol

  Extensions

  Upgrade Response
  ```

---

## Step 3 — Identify Authentication

* Determine whether identity comes from:

  ```text
  Cookie

  Token

  Query Parameter

  Initial Message

  Other Mechanism
  ```

---

## Step 4 — Map Messages

* Record both:

  ```text
  Client → Server

  Server → Client
  ```

* Group messages by function.

---

## Step 5 — Identify Objects

* Look for:

  ```text
  User IDs

  Room IDs

  Conversation IDs

  Organization IDs

  Message IDs

  Resource IDs
  ```

---

## Step 6 — Map Authorization

* Record:

  ```text
  User

  Role

  Tenant

  Object Owner

  Expected Permission
  ```

---

## Step 7 — Establish Baselines

* Capture legitimate messages and expected responses.

---

## Step 8 — Test One Variable

* Modify one:

  ```text
  ID

  Action

  Property

  Value
  ```

* At a time.

---

## Step 9 — Verify State

* Do not rely only on an acknowledgement message.

* Confirm the real application state where possible.

---

## Step 10 — Review Session Lifecycle

* Where authorized, examine:

  ```text
  Logout

  Expiry

  Permission Change

  Reconnection
  ```

---

## Step 11 — Review Cross-Origin Behavior

* If cookie-authenticated, evaluate Origin handling and other cross-site protections.

---

## Step 12 — Validate Findings

* Confirm:

  ```text
  Reproducibility

  Identity

  Authorization Context

  Data / State

  Security Impact
  ```

---

## Example — Unauthorized Room Subscription

### Baseline

```json
{
  "action": "subscribe",
  "room_id": 91
}
```

* Room `91` belongs to controlled User A.

### Hypothesis

```text
Does the Server Verify That the Connected User
Is Authorized to Subscribe to the Requested Room?
```

### Controlled Test

* Using User A's connection:

  ```text
  Change room_id

  →

  Controlled User B Room
  ```

* Keep all other variables unchanged.

### Validate

* Determine whether User A receives:

  ```text
  User B Events

  Messages

  Notifications

  Sensitive Data
  ```

---

## Example — Function Authorization

### Message

```json
{
  "action": "delete_message",
  "message_id": 781
}
```

### Security Question

```text
Can a Standard User Delete Only Messages
They Are Authorized to Control?
```

* Test with controlled disposable objects.

* Verify final state.

---

## Example — Cross-Origin Handshake

### Legitimate Handshake

```text
Origin:

https://example.test
```

### Controlled Variation

```text
Origin:

https://controlled-origin.test
```

### Observe

```text
Connection Accepted?

Credentials Used?

Sensitive Messages Accessible?

State-Changing Actions Possible?
```

* Acceptance alone is not enough to establish a vulnerability.

---

## Example — Logout Lifecycle

```text
Authenticated User Opens WebSocket

↓

Connection Established

↓

User Logs Out

↓

Existing Connection Remains Open

↓

Controlled Message Sent
```

* Security question:

  ```text
  Should the Existing Connection Still Be Authorized?
  ```

* Interpret based on the application's intended session model.

---

## Example — Message Replay

### Original

```json
{
  "action": "send_invitation",
  "user_id": 42
}
```

* Before replaying, determine whether repetition may:

  ```text
  Send Multiple Emails

  Create Duplicate Invitations

  Trigger Multiple Side Effects
  ```

* Use controlled accounts and minimal requests.

---

## Evidence Requirements

* Strong WebSocket evidence should include:

  ```text
  WebSocket URL

  Handshake

  Authentication Context

  User

  Role

  Tenant

  Original Message

  Modified Message

  Server Response

  Object Ownership

  Final State

  Reproduction Steps

  Security Impact
  ```

---

## Anomaly vs Vulnerability

* Anomaly:

  ```text
  Unexpected Message Accepted
  ```

* Validated vulnerability:

  ```text
  Reproducible Security Rule Violation

  +

  Unauthorized Data or Action

  +

  Demonstrable Impact
  ```

---

## False Positive Control

* Before concluding:

  ```text
  Reconnect Cleanly

  ↓

  Restore Baseline

  ↓

  Confirm User Identity

  ↓

  Confirm Object Ownership

  ↓

  Repeat One Controlled Change

  ↓

  Verify Server State

  ↓

  Confirm Security Impact
  ```

---

## WebSocket Testing Checklist

### Handshake

* Review:

  ```text
  URL

  Origin

  Cookies

  Tokens

  Authentication

  Subprotocol

  Extensions
  ```

---

### Authentication

* Review:

  ```text
  Missing Authentication

  Invalid Authentication

  Session Expiry

  Logout

  Reconnection
  ```

---

### Authorization

* Review:

  ```text
  Object-Level

  Function-Level

  Role-Level

  Tenant-Level

  Subscription-Level
  ```

---

### Messages

* Review:

  ```text
  IDs

  Actions

  Parameters

  Missing Fields

  Unexpected Fields

  Types

  Boundaries
  ```

---

### State

* Review:

  ```text
  State Changes

  Replay

  Duplicate Actions

  Message Ordering

  Invalid Transitions
  ```

---

### Cross-Origin

* Review where relevant:

  ```text
  Origin Validation

  Cookie Authentication

  SameSite Behavior

  Additional Tokens

  Exploitability
  ```

---

### Server-to-Client

* Review:

  ```text
  Sensitive Data

  Cross-User Events

  Cross-Tenant Events

  Hidden Identifiers

  Unauthorized Subscriptions
  ```

---

## Investigation Journal

```text
Application:

Date:

Scope:

WebSocket URLs:

Handshake Paths:

Origins:

Authentication Mechanisms:

Cookies:

Tokens:

Subprotocols:

Extensions:

Users:

Roles:

Tenants:

Message Types:

Client-to-Server Messages:

Server-to-Client Messages:

Objects:

Object Identifiers:

Object Ownership:

Subscription Channels:

State-Changing Actions:

Authentication Tests:

Authorization Tests:

Origin Validation:

Session Expiry Behavior:

Logout Behavior:

Reconnection Behavior:

Replay Behavior:

Message Ordering:

Validation Behavior:

Sensitive Data Exposure:

High-Priority Messages:

Security Hypotheses:

Validated Findings:

False Positives:

Evidence:

Next Steps:
```

---

## Professional Tips

* Treat the handshake and message layer as separate security surfaces.
* Capture legitimate traffic before modifying WebSocket messages.
* Map both client-to-server and server-to-client messages.
* Record message types instead of treating the connection as one function.
* Identify object IDs, user IDs, tenant IDs, and channel names.
* Test authentication separately from message-level authorization.
* Use controlled accounts and known object ownership.
* Change one message field at a time.
* Verify actual server state after state-changing messages.
* Understand replay side effects before resending messages.
* Evaluate subscription authorization, not only direct actions.
* Review long-lived connection behavior after logout or permission changes.
* Consider Origin validation when browser credentials authenticate the handshake.
* Do not treat arbitrary Origin acceptance alone as proof of cross-site WebSocket hijacking.
* Inspect server-to-client traffic for unauthorized data exposure.
* Test state transitions and message ordering carefully.
* Avoid high-volume or resource-exhaustion tests without explicit authorization.
* Preserve the handshake and exact message evidence for validated findings.

---

## Common Mistakes

* Ignoring WebSockets because normal HTTP traffic appears secure.
* Looking only at the initial handshake.
* Treating all WebSocket messages as one API function.
* Ignoring server-to-client messages.
* Assuming authenticated connections authorize every message.
* Failing to test object-level authorization.
* Ignoring tenant boundaries.
* Ignoring subscription authorization.
* Changing several fields simultaneously.
* Replaying messages without understanding side effects.
* Assuming an acknowledgement means the state actually changed.
* Ignoring session expiration and logout behavior.
* Treating accepted cross-origin handshakes as automatically exploitable.
* Ignoring cookie SameSite behavior and other cross-site constraints.
* Failing to distinguish client-controlled and server-controlled fields.
* Sending malformed or oversized traffic that risks service disruption.
* Ignoring message ordering and workflow state.
* Failing to record the authentication context of the WebSocket connection.
* Reporting unusual behavior without proving unauthorized data access or action.
* Failing to preserve reproducible message evidence.

---

## Practice Tasks

### Task 1 — Select an Authorized WebSocket Application

* Use:

  ```text
  Deliberately Vulnerable Lab

  System You Own

  or

  Explicitly Authorized Target
  ```

---

### Task 2 — Identify the WebSocket

* Record:

  ```text
  URL

  Path

  Purpose

  ws:// or wss://
  ```

---

### Task 3 — Analyze the Handshake

* Record:

  ```text
  Origin

  Authentication

  Cookies

  Tokens

  Subprotocol

  Extensions

  Response Status
  ```

---

### Task 4 — Map Message Types

* Record at least five message types if available.

* Classify:

  ```text
  Client → Server

  Server → Client
  ```

---

### Task 5 — Identify Objects

* Record:

  ```text
  User IDs

  Room IDs

  Message IDs

  Conversation IDs

  Tenant IDs
  ```

* Where present.

---

### Task 6 — Build Baselines

* Preserve legitimate messages and expected responses.

---

### Task 7 — Test Controlled Object Authorization

* Using controlled accounts:

  ```text
  Own Object

  vs

  Other Controlled User Object
  ```

* Modify only the relevant identifier.

---

### Task 8 — Test Subscription Authorization

* If subscriptions or rooms exist, verify whether access is restricted appropriately.

---

### Task 9 — Analyze a State-Changing Message

* Record:

  ```text
  Initial State

  Message

  Response

  Final State
  ```

---

### Task 10 — Review Validation

* Test one harmless field with appropriate:

  ```text
  Missing

  Null

  Empty

  Type Variation
  ```

---

### Task 11 — Review Session Lifecycle

* In an authorized environment, observe behavior after:

  ```text
  Logout

  Session Expiry

  Reconnection
  ```

---

### Task 12 — Review Origin Handling

* If authentication relies on cookies, evaluate whether cross-origin connections are restricted appropriately.

---

### Task 13 — Review Replay Behavior

* Choose only a harmless controlled action.

* Determine whether duplicate execution occurs.

---

### Task 14 — Generate Security Hypotheses

* Write at least five questions such as:

  ```text
  Does this message verify object ownership?

  Can another tenant's channel be subscribed to?

  Does logout invalidate the existing connection?

  Does the server validate Origin appropriately?

  Can this state-changing message be replayed unexpectedly?
  ```

---

## Interview Questions

1. What is a WebSocket?
2. How does WebSocket communication differ from traditional HTTP?
3. Why do modern applications use WebSockets?
4. How is a WebSocket connection established?
5. What does `101 Switching Protocols` mean?
6. Which headers are important during a WebSocket handshake?
7. What is the difference between `ws://` and `wss://`?
8. How can you identify WebSocket traffic in Burp Suite?
9. Why should the handshake and message layer be tested separately?
10. What information should be recorded for each WebSocket message type?
11. How can Burp Suite be used to modify WebSocket messages?
12. Why should a baseline message be preserved?
13. What is message-level authorization?
14. How would you test object-level authorization in WebSocket messages?
15. Why are controlled test accounts useful?
16. What is function-level authorization in WebSocket communication?
17. How can tenant boundaries appear in WebSocket messages?
18. What is subscription authorization?
19. Why should server-to-client messages also be analyzed?
20. How can WebSocket authentication be implemented?
21. What is the difference between WebSocket authentication and authorization?
22. Why can long-lived connections create session-lifecycle security concerns?
23. What should happen to a WebSocket after logout?
24. Why can permission changes affect existing connections?
25. What is the Origin header used for in WebSocket handshakes?
26. What is cross-site WebSocket hijacking?
27. Under what conditions can cross-site WebSocket hijacking become possible?
28. Why does arbitrary Origin acceptance not automatically prove exploitability?
29. How can SameSite cookies affect cross-site WebSocket attacks?
30. How should WebSocket message validation be tested?
31. Why should client-controlled and server-controlled fields be distinguished?
32. What risks can arise from replaying WebSocket messages?
33. Why is message ordering security-relevant?
34. What is state-machine testing in WebSocket applications?
35. How can GraphQL subscriptions use WebSockets?
36. What authorization checks are important for GraphQL subscriptions?
37. How can WebSocket data lead to injection vulnerabilities?
38. Why must stored WebSocket data be traced to its output context?
39. How should rate limiting work for persistent WebSocket connections?
40. Why must high-volume WebSocket testing be handled carefully?
41. What are WebSocket subprotocols?
42. Why can reconnection behavior be security-relevant?
43. What evidence should be collected for a WebSocket vulnerability?
44. What is the difference between an anomaly and a validated WebSocket vulnerability?
45. Describe your complete methodology for testing an unfamiliar WebSocket application using Burp Suite.

---

## Quick Revision

* WebSocket:

  ```text
  Persistent

  +

  Bidirectional

  +

  Client ↔ Server
  ```

* Connection:

  ```text
  HTTP Handshake

  ↓

  101 Switching Protocols

  ↓

  Persistent WebSocket

  ↓

  Messages
  ```

* Security surfaces:

  ```text
  Handshake

  +

  Message Layer
  ```

* Message mapping:

  ```text
  Direction

  +

  Type

  +

  Action

  +

  Object

  +

  Parameters

  +

  Authorization
  ```

* Authentication:

  ```text
  Who Is Connected?
  ```

* Authorization:

  ```text
  What May They Do?
  ```

* Important rule:

  ```text
  Authenticated Connection

  ≠

  Every Message Authorized
  ```

* Subscription:

  ```text
  Channel Requested

  ↓

  Verify User / Role / Tenant

  ↓

  Deliver Authorized Events Only
  ```

* Cross-site risk:

  ```text
  Browser Credentials

  +

  Weak Cross-Origin Protection

  +

  Sensitive WebSocket Functionality
  ```

* Important rule:

  ```text
  Origin Accepted

  ≠

  Automatically Exploitable
  ```

* State testing:

  ```text
  Initial State

  ↓

  One Controlled Message

  ↓

  Final State
  ```

* Session lifecycle:

  ```text
  Connect

  ↓

  Logout / Expiry / Permission Change

  ↓

  Revalidate Connection Security
  ```

* Workflow:

  ```text
  Discover

  ↓

  Handshake

  ↓

  Authentication

  ↓

  Map Messages

  ↓

  Map Objects / Roles / Tenants

  ↓

  Baseline

  ↓

  Modify One Variable

  ↓

  Observe

  ↓

  Verify State

  ↓

  Validate Impact
  ```

---

## Key Takeaways

* WebSockets provide persistent bidirectional communication and require a different testing mindset from ordinary request-response HTTP.
* Every WebSocket assessment should examine both the initial HTTP handshake and the subsequent message layer.
* A successful `101 Switching Protocols` response establishes the upgraded connection but says nothing about whether individual messages are securely authorized.
* Map each message type by direction, action, object, parameters, identity, role, tenant, and state impact.
* Preserve legitimate baseline messages before making controlled modifications.
* Authentication of the WebSocket connection and authorization of individual messages are separate security requirements.
* Object-level, function-level, tenant-level, and subscription-level authorization may all need independent testing.
* Server-to-client messages are as important as client-to-server messages because unauthorized subscriptions may expose sensitive real-time data.
* Long-lived connections introduce session-lifecycle questions involving logout, expiry, account disablement, permission changes, and reconnection.
* Cookie-authenticated WebSockets should be evaluated for appropriate cross-origin protections.
* Accepting an arbitrary Origin is not by itself proof of cross-site WebSocket hijacking; exploitability depends on browser credentials, additional protections, and accessible sensitive functionality.
* WebSocket messages require the same careful server-side input validation as ordinary HTTP inputs.
* State-changing messages must be tested using controlled objects, with initial and final state verification.
* Replay can cause duplicate or repeated side effects, so messages should not be resent blindly.
* Message ordering and application state machines may reveal workflow weaknesses when required steps can be skipped or reordered.
* GraphQL subscriptions may introduce WebSocket-specific authentication, authorization, and event-exposure concerns.
* Rate-limit, complexity, message-size, and availability testing must avoid creating operational impact unless explicitly authorized.
* A valid WebSocket finding requires reproducible evidence of unauthorized data access, unauthorized action, or another meaningful security-rule violation.
* The professional WebSocket workflow is **discover connection → analyze handshake → understand authentication → map messages → map objects and authorization → establish baselines → modify one variable → observe response → verify state → review session lifecycle → validate impact → document evidence**.
