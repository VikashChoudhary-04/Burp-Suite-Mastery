# WebSocket Testing with Burp Suite

> **Module 16 — Modern Application Testing**

> **Progress**

```text
███████████░░ 11 / 13 Files
```

---

## Overview

* WebSockets provide persistent, bidirectional communication between a client and server.

* Unlike traditional HTTP communication:

  ```text
  Client

  ↓

  HTTP Request

  ↓

  Server

  ↓

  HTTP Response
  ```

* A WebSocket connection begins with an HTTP handshake and then upgrades to a persistent communication channel:

  ```text
  Client

  ↓

  HTTP Upgrade Handshake

  ↓

  WebSocket Connection

  ⇅

  Bidirectional Messages
  ```

* WebSockets are commonly used for:

  * Real-time chat.
  * Notifications.
  * Collaboration tools.
  * Live dashboards.
  * Trading interfaces.
  * Gaming.
  * Support systems.
  * Streaming updates.
  * Administrative interfaces.
  * Real-time application events.

* Because communication continues after the initial HTTP handshake, WebSocket security testing requires examining both:

  ```text
  Connection Establishment

  and

  Individual Messages
  ```

* Burp Suite can intercept WebSocket handshakes, record message history, modify messages, replay messages, and help investigate authentication, authorization, input validation, and business-logic weaknesses.

---

## Learning Objectives

* By the end of this lesson, you will be able to:

  * Understand how WebSockets work.
  * Understand the WebSocket handshake.
  * Identify WebSocket traffic in Burp Suite.
  * Understand WebSocket message history.
  * Intercept and modify WebSocket messages.
  * Replay WebSocket messages safely.
  * Distinguish handshake security from message-level security.
  * Analyze WebSocket authentication.
  * Test authorization at the message level.
  * Investigate object-level authorization.
  * Test role boundaries.
  * Analyze session invalidation.
  * Understand origin validation.
  * Understand Cross-Site WebSocket Hijacking.
  * Test input validation safely.
  * Analyze message schemas and event types.
  * Investigate hidden WebSocket actions.
  * Test message sequencing and state transitions.
  * Understand replay and duplicate-message risks.
  * Analyze subscription authorization.
  * Test tenant isolation.
  * Investigate rate limiting and abuse controls safely.
  * Understand reconnection behavior.
  * Identify information disclosure through WebSocket messages.
  * Build a repeatable WebSocket testing methodology.

---

## What Is a WebSocket?

* A WebSocket is a communication protocol that provides a persistent full-duplex connection between a client and server.

* Full-duplex means:

  ```text
  Client ─────► Server

  Client ◄───── Server
  ```

* Both sides can send messages independently after the connection is established.

---

## HTTP vs WebSocket

### Traditional HTTP

```text
Request

↓

Response

↓

Connection Reused or Closed

↓

Next Request

↓

Next Response
```

### WebSocket

```text
HTTP Handshake

↓

Connection Upgraded

↓

Persistent Channel

⇅

Messages Continue in Both Directions
```

---

## Why Applications Use WebSockets

* WebSockets are useful when applications require frequent real-time updates.

* Examples:

  ```text
  Chat Message Arrives

  ↓

  Server Pushes Message Immediately
  ```

* Instead of:

  ```text
  Browser Polls Every Few Seconds

  ↓

  Server Checks for Updates
  ```

---

## WebSocket Lifecycle

```text
Browser / Client

↓

HTTP Upgrade Request

↓

Server Validates Handshake

↓

101 Switching Protocols

↓

WebSocket Connection Established

↓

Client and Server Exchange Messages

↓

Connection Closes or Reconnects
```

---

## WebSocket Handshake

* A WebSocket connection normally begins as an HTTP request.

* Example:

```http
GET /chat HTTP/1.1
Host: example.test
Upgrade: websocket
Connection: Upgrade
Sec-WebSocket-Key: dGhlIHNhbXBsZSBub25jZQ==
Sec-WebSocket-Version: 13
Origin: https://example.test
Cookie: session=CONTROLLED_SESSION
```

---

## Important Handshake Headers

* Common headers include:

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

## Server Response

* A successful upgrade commonly returns:

```http
HTTP/1.1 101 Switching Protocols
Upgrade: websocket
Connection: Upgrade
Sec-WebSocket-Accept: ...
```

* After this point, communication uses WebSocket frames rather than ordinary HTTP request-response exchanges.

---

## Important Principle

```text
WebSocket Starts With HTTP

But

Does Not Continue as Normal HTTP
```

---

## WebSocket Security Model

* Think of WebSocket testing in two layers:

  ```text
  Layer 1

  Connection / Handshake Security

  ↓

  Layer 2

  Message-Level Security
  ```

* Both must be tested.

---

## Layer 1 — Handshake Security

* Questions include:

  ```text
  Who Can Connect?

  Is Authentication Required?

  Is Origin Validated?

  Are Cookies Used?

  Are Tokens Used?

  Can Expired Sessions Connect?

  Is TLS Required?
  ```

---

## Layer 2 — Message Security

* Questions include:

  ```text
  What Actions Can Be Sent?

  Is Authorization Checked Per Message?

  Are Object IDs Trusted?

  Are Roles Enforced?

  Is Input Validated?

  Can Messages Be Replayed?

  Are State Transitions Enforced?
  ```

---

## Finding WebSocket Traffic in Burp Suite

* Use the application normally while Burp is configured as the proxy.

* WebSocket traffic can be observed through Burp's WebSockets-related history and message inspection functionality.

* Exact interface labels may vary slightly by Burp Suite version.

---

## Discovery Workflow

```text
Open Application

↓

Use Real-Time Features

↓

Observe HTTP Traffic

↓

Identify Upgrade Request

↓

Locate WebSocket Connection

↓

Review Message History
```

---

## Features Likely to Use WebSockets

* Look for:

  ```text
  Chat

  Live Notifications

  Live Status

  Real-Time Dashboard

  Collaboration

  Presence Indicators

  Live Support

  Streaming Events

  Trading Data

  Multiplayer Features
  ```

---

## WebSocket Test Card

```text
Feature:

WebSocket URL:

Handshake Endpoint:

Authenticated?:

Authentication Method:

Origin Header:

Subprotocol:

Connection ID:

User:

Role:

Tenant:

Client-to-Server Messages:

Server-to-Client Messages:

Message Format:

Object Identifiers:

Event Types:

Subscription Mechanism:

Reconnect Behavior:

Notes:
```

---

## Establish a Baseline

* Before modifying messages:

  ```text
  Connect Normally

  ↓

  Perform One Legitimate Action

  ↓

  Record Client Message

  ↓

  Record Server Response / Event

  ↓

  Record Resulting Application State
  ```

---

## Example Message

```json
{
  "type": "send_message",
  "conversation_id": 731,
  "message": "Hello"
}
```

* Record:

  ```text
  Event Type

  Object ID

  User-Controlled Fields

  Server Response

  Side Effects
  ```

---

## Message Formats

* WebSocket messages may use:

  ```text
  JSON

  XML

  Plain Text

  Binary

  Protocol-Specific Formats
  ```

* JSON is common in web applications.

---

## Message Direction

* Distinguish:

  ```text
  Client → Server

  Server → Client
  ```

* Client messages often request actions.

* Server messages often deliver events or results.

---

## Example

### Client → Server

```json
{
  "action": "subscribe",
  "channel": "notifications"
}
```

### Server → Client

```json
{
  "event": "notification",
  "message": "New activity"
}
```

---

## WebSocket Message History

* Message history allows you to study:

  ```text
  Direction

  Timing

  Message Content

  Connection

  Event Sequence
  ```

* Use it to reconstruct application behavior before testing.

---

## Build a Message Inventory

```text
Message Type:

Direction:

Purpose:

Parameters:

Object IDs:

Requires Authentication?:

Expected Role:

Expected State:

Response / Event:
```

---

## Example Inventory

```text
send_message

subscribe

unsubscribe

typing

mark_read

delete_message

edit_message

join_room

leave_room
```

* Actual event names depend on the application.

---

## Intercepting WebSocket Messages

* Burp can intercept WebSocket messages before they reach the destination.

* Conceptually:

  ```text
  Client

  ↓

  Burp Intercepts Message

  ↓

  Inspect / Modify / Forward / Drop

  ↓

  Server
  ```

---

## Why Interception Matters

* The browser UI may restrict actions.

* Burp lets you test the actual protocol message.

* Example:

  ```text
  UI Allows:

  conversation_id = 731

  Burp Test:

  conversation_id = 732
  ```

* Use only controlled objects.

---

## Change One Variable at a Time

* Example baseline:

```json
{
  "type": "open_document",
  "document_id": 1001
}
```

* Controlled modification:

```json
{
  "type": "open_document",
  "document_id": 1002
}
```

* Keep everything else unchanged.

---

## Important Principle

```text
WebSocket Message

Must Be Treated

Like Any Other Untrusted Client Input
```

---

## Authentication

* WebSocket authentication may occur through:

  ```text
  Session Cookies

  Authorization Header

  Query Token

  First Authentication Message

  Temporary Connection Token
  ```

---

## Cookie-Based Authentication

* Example handshake:

```http
GET /socket HTTP/1.1
Host: example.test
Upgrade: websocket
Connection: Upgrade
Cookie: session=CONTROLLED_SESSION
```

* The server may authenticate the connection using the existing browser session.

---

## Token-Based Authentication

* A token may appear in:

  ```text
  Authorization Header

  Query String

  Cookie

  Initial WebSocket Message
  ```

* Determine how credentials are transmitted and validated.

---

## First-Message Authentication

* Some systems establish the connection first and authenticate afterward.

* Example:

```json
{
  "type": "authenticate",
  "token": "CONTROLLED_TOKEN"
}
```

* Security questions:

  ```text
  What Can Be Done Before Authentication?

  Is Authentication Required Before Subscribing?

  Can Messages Be Sent Before Authentication Completes?
  ```

---

## Authentication Test Matrix

```text
Valid Session

No Session

Invalid Session

Expired Session

Logged-Out Session

Lower-Privilege Session

Different Controlled User
```

---

## Session Invalidation

* Test:

  ```text
  Establish WebSocket

  ↓

  Log Out Through Application

  ↓

  Attempt Existing WebSocket Action
  ```

* Security question:

  ```text
  Does the Existing Connection Remain Authorized?
  ```

---

## Important Principle

```text
HTTP Session Invalidated

Should Be Reflected

Where Required

In Existing WebSocket Authorization
```

* Exact expected behavior depends on the application's session model.

---

## Password Change

* Some security-sensitive applications invalidate sessions after:

  ```text
  Password Change

  Account Lock

  Role Change

  Security Reset
  ```

* Determine whether existing WebSocket connections follow the intended session policy.

---

## Authorization

* Authentication answers:

  ```text
  Who Are You?
  ```

* Authorization answers:

  ```text
  Are You Allowed to Perform This Action?
  ```

* WebSocket connections require both.

---

## Critical Principle

```text
Authenticated WebSocket Connection

≠

Authorization for Every Message
```

---

## Message-Level Authorization

* Every sensitive message should enforce authorization based on:

  ```text
  Current User

  Role

  Object Ownership

  Tenant

  Current State

  Requested Action
  ```

---

## Example

```json
{
  "type": "delete_message",
  "message_id": 555
}
```

* Security question:

  ```text
  Does the Server Verify That the Current User
  May Delete Message 555?
  ```

---

## IDOR / BOLA Through WebSockets

* Object-level authorization flaws can occur inside WebSocket messages.

* Example:

```json
{
  "type": "get_order",
  "order_id": 1001
}
```

* Controlled test:

```json
{
  "type": "get_order",
  "order_id": 1002
}
```

* Use two controlled accounts and known objects.

---

## Two-Account Testing

```text
User A

Owns Object A

User B

Owns Object B
```

* Test:

  ```text
  User A Requests Object A

  ↓

  Baseline Success

  User A Requests Object B

  ↓

  Compare
  ```

---

## Object Authorization Matrix

| User   | Object   | Expected          |
| ------ | -------- | ----------------- |
| User A | Object A | Allowed           |
| User A | Object B | Denied if private |
| User B | Object B | Allowed           |
| User B | Object A | Denied if private |

---

## Role Authorization

* Example roles:

  ```text
  User

  Moderator

  Administrator
  ```

* A low-privilege client may know administrative message names from frontend code or observed traffic.

* The server must still enforce authorization.

---

## Example

```json
{
  "type": "ban_user",
  "user_id": 731
}
```

* Security question:

  ```text
  Can a Normal User Send an Administrator-Only Event?
  ```

* Test only with controlled accounts.

---

## Hidden Actions

* Frontend JavaScript may reveal message types not exposed in the current UI.

* Examples:

  ```text
  admin_action

  update_role

  delete_room

  export_data
  ```

* Discovery does not prove accessibility.

* Authorization must be tested safely.

---

## Message Schema Mapping

* For each message type, identify:

  ```text
  Required Fields

  Optional Fields

  IDs

  Boolean Flags

  Roles

  State Values

  Nested Objects
  ```

---

## Example

```json
{
  "type": "update_profile",
  "data": {
    "display_name": "Test",
    "status": "online"
  }
}
```

* Test server-side enforcement rather than trusting UI restrictions.

---

## Extra Fields

* Some message handlers may accept undocumented fields.

* Example baseline:

```json
{
  "type": "update_profile",
  "display_name": "Test"
}
```

* Controlled test:

```json
{
  "type": "update_profile",
  "display_name": "Test",
  "role": "admin"
}
```

* Use only a controlled test account.

* This overlaps with mass-assignment testing.

---

## Input Validation

* WebSocket messages may contain:

  ```text
  Text

  Numbers

  IDs

  URLs

  Filenames

  HTML

  Search Queries

  JSON Objects
  ```

* Apply the same validation principles used for HTTP APIs.

---

## Input Test Matrix

```text
Expected Value

Empty Value

Missing Field

Wrong Type

Boundary Value

Unexpected Field

Long but Safe Value

Special Characters
```

* Keep tests controlled.

---

## Type Validation

* Example expected:

```json
{
  "quantity": 2
}
```

* Controlled tests:

```json
{
  "quantity": "2"
}
```

```json
{
  "quantity": -1
}
```

```json
{
  "quantity": null
}
```

* Observe validation and business logic.

---

## Missing Fields

* Remove one field at a time.

* Example:

```json
{
  "type": "send_message",
  "conversation_id": 731
}
```

* Observe whether defaults or unsafe assumptions appear.

---

## Unexpected Fields

* Add harmless unexpected fields.

* Determine whether the server:

  ```text
  Ignores

  Rejects

  Stores

  Processes
  ```

---

## Message Replay

* WebSocket messages may sometimes be replayed.

* Example:

  ```text
  Send Action

  ↓

  Action Completes

  ↓

  Replay Same Message
  ```

* Determine whether replay is expected.

---

## Replay-Sensitive Actions

* Pay special attention to:

  ```text
  Purchases

  Reward Claims

  State Changes

  Invitations

  Transfers

  One-Time Actions

  Administrative Operations
  ```

---

## Important Principle

```text
Replayable Message

≠

Automatically Vulnerable
```

* The impact depends on whether the underlying action should be repeatable.

---

## Duplicate Processing

* Example:

```json
{
  "type": "claim_reward",
  "reward_id": 42
}
```

* Sequential replay:

  ```text
  First → Success

  Second → Should Be Rejected if One-Time
  ```

* If duplicate effects occur, investigate business logic and concurrency separately.

---

## Message IDs

* Protocols may include:

  ```text
  request_id

  message_id

  nonce

  sequence

  event_id
  ```

* Determine whether these fields provide:

  ```text
  Correlation

  Ordering

  Deduplication

  Replay Protection
  ```

---

## Message Sequencing

* Some WebSocket workflows expect events in a specific order.

* Example:

  ```text
  Join Room

  ↓

  Subscribe

  ↓

  Send Message
  ```

* Security question:

  ```text
  Can Later Actions Be Performed Without Required Earlier Steps?
  ```

---

## State Machine Testing

* Example:

  ```text
  Created

  ↓

  Approved

  ↓

  Completed
  ```

* Attempt only safe state transitions.

* Determine whether the server enforces valid sequencing.

---

## Out-of-Order Messages

* Example expected:

  ```text
  Authenticate

  ↓

  Subscribe

  ↓

  Read Events
  ```

* Controlled test:

  ```text
  Subscribe Before Authenticate
  ```

* Observe behavior.

---

## Subscription Systems

* Many WebSocket applications use subscriptions.

* Example:

```json
{
  "type": "subscribe",
  "channel": "orders:731"
}
```

* Security question:

  ```text
  Can the User Subscribe to Channels They Should Not Access?
  ```

---

## Subscription Authorization

* Test with controlled objects:

  ```text
  User A

  ↓

  Subscribe to User A Channel

  ↓

  Baseline

  User A

  ↓

  Subscribe to User B Controlled Channel

  ↓

  Compare
  ```

---

## Important Principle

```text
Authorization Must Apply

When Subscribing

and

When Delivering Sensitive Events
```

* Defense should not rely solely on client-generated channel names.

---

## Predictable Channels

* Examples:

  ```text
  user:1001

  user:1002

  order:731

  project:55
  ```

* Predictability is not itself a vulnerability.

* Missing authorization is the issue.

---

## Tenant Isolation

* Multi-tenant applications may use WebSockets for:

  ```text
  Notifications

  Dashboards

  Collaboration

  Administrative Events
  ```

* Verify that tenant context is enforced server-side.

---

## Tenant Test

```text
Tenant A User

↓

Subscribe to Tenant A Resource

↓

Baseline

Tenant A User

↓

Attempt Controlled Tenant B Resource

↓

Compare
```

---

## Server-Pushed Data

* WebSockets allow the server to push data without a client request for every event.

* This creates a unique testing question:

  ```text
  Is Sensitive Data Being Delivered to Connections
  That Should Not Receive It?
  ```

---

## Passive Observation

* Before modifying anything, inspect server-pushed events for:

  ```text
  Internal IDs

  Email Addresses

  Tokens

  Debug Data

  Tenant Data

  Hidden Fields

  Internal Status

  Other Users' Information
  ```

---

## Information Disclosure

* A WebSocket may expose more fields than the UI displays.

* Example:

```json
{
  "user_id": 731,
  "display_name": "Test User",
  "internal_role_code": 4,
  "internal_note": "..."
}
```

* Determine whether hidden fields are actually sensitive.

---

## Data Minimization

* Security question:

  ```text
  Does the Client Receive More Data
  Than It Needs?
  ```

* This is especially important for broadcast events.

---

## Broadcast Channels

* Some systems broadcast events to groups.

* Verify:

  ```text
  Group Membership

  Tenant Membership

  Room Membership

  Role Requirements
  ```

---

## Origin Header

* Browsers may send:

```http
Origin: https://example.test
```

* Servers can use this to validate where browser-based connection requests originate.

---

## Why Origin Matters

* Browser WebSocket connections may automatically include ambient credentials such as cookies.

* If the server accepts connections from untrusted origins while relying on cookies, cross-site attacks may become possible.

---

## Cross-Site WebSocket Hijacking

* Cross-Site WebSocket Hijacking, or CSWSH, can occur when:

  ```text
  WebSocket Uses Cookie Authentication

  +

  Browser Sends Cookies Automatically

  +

  Server Does Not Properly Validate Origin

  +

  Attacker-Controlled Site Can Establish Connection
  ```

---

## Conceptual Flow

```text
Victim Logged In

↓

Victim Visits Untrusted Site

↓

Site Opens WebSocket to Target

↓

Browser Sends Relevant Session Cookies

↓

Target Accepts Connection

↓

Untrusted Site Interacts With Authenticated WebSocket
```

* Actual exploitability depends on browser, cookie, origin, protocol, and application behavior.

---

## CSWSH Testing Questions

```text
Does the WebSocket Use Cookie Authentication?

Is Origin Validated?

Are Cross-Site Origins Accepted?

Can Sensitive Data Be Read?

Can Sensitive Actions Be Sent?

Are Additional Tokens Required?
```

---

## Origin Test Matrix

```text
Expected Origin

Missing Origin

Different Controlled Origin

Malformed Origin

Null Origin if Relevant
```

* Test only within authorization and using controlled infrastructure where needed.

---

## Important Principle

```text
Missing Origin Validation

≠

Automatically Exploitable CSWSH
```

* Demonstrate the required authentication and impact conditions.

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
## CSRF Tokens and WebSockets

* Some WebSocket handshakes or authentication messages may require anti-CSRF-style tokens.

* Determine whether such tokens are:

  ```text
  Required

  Session-Bound

  Validated

  Reusable

  Predictable
  ```

---

## Query Parameters

* WebSocket URLs may contain:

  ```text
  Token

  User ID

  Tenant ID

  Channel

  Version
  ```

* Example:

  ```text
  wss://example.test/socket?token=...
  ```

* Treat sensitive query parameters carefully because URLs may appear in logs or telemetry.

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

* Sensitive production traffic should generally use secure transport.

---

## Important Principle

```text
HTTPS Application

Should Not Downgrade Sensitive Real-Time Traffic

To Insecure WebSocket Transport
```

---

## WebSocket Subprotocols

* The handshake may include:

```http
Sec-WebSocket-Protocol: example-protocol
```

* Subprotocols define application-level communication conventions.

* Test whether unexpected protocol selections are handled safely.

---

## Reconnection

* Applications often reconnect automatically when:

  ```text
  Network Changes

  Tab Sleeps

  Token Expires

  Server Restarts

  Connection Drops
  ```

* Reconnection may trigger fresh authentication or reuse old credentials.

---

## Reconnection Test

```text
Connect

↓

Change Authentication State

↓

Disconnect

↓

Reconnect

↓

Observe Credentials and Authorization
```

---

## Token Refresh

* If tokens expire during a long-lived connection:

  ```text
  Does Connection Continue?

  Is Reauthentication Required?

  Is Token Refreshed?

  Are Permissions Re-Evaluated?
  ```

---

## Role Changes During Connection

* Example:

  ```text
  User Connects as Administrator

  ↓

  Role Is Reduced

  ↓

  Existing Connection Remains Open
  ```

* Security question:

  ```text
  Are Existing Permissions Re-Evaluated?
  ```

---

## Account Disablement

* Test with controlled accounts:

  ```text
  Connect

  ↓

  Disable Account Through Authorized Admin Flow

  ↓

  Attempt Existing WebSocket Action
  ```

* Compare with intended security policy.

---

## Message Tampering

* Common fields to investigate include:

  ```text
  User ID

  Object ID

  Tenant ID

  Role

  Price

  Quantity

  Status

  Channel

  Recipient

  Sender
  ```

* Never assume a field is trusted simply because the UI generated it.

---

## Sender Identity

* Example:

```json
{
  "type": "send_message",
  "sender_id": 1001,
  "recipient_id": 1002,
  "message": "Hello"
}
```

* Security question:

  ```text
  Does the Server Trust sender_id

  or

  Derive Sender Identity From the Authenticated Session?
  ```

---

## Secure Principle

```text
Security-Sensitive Identity

Should Be Derived Server-Side

Where Possible
```

---

## Price and Quantity

* Real-time commerce systems may send:

```json
{
  "type": "place_order",
  "item_id": 731,
  "quantity": 1,
  "price": 100
}
```

* Determine which fields are authoritative.

* Use sandbox or explicitly authorized test environments.

---

## Business Logic

* WebSocket testing is not limited to technical input validation.

* Investigate:

  ```text
  Can Actions Be Performed Out of Order?

  Can Limits Be Bypassed?

  Can One-Time Actions Be Replayed?

  Can State Be Manipulated?

  Can Another User's Object Be Referenced?

  Can Hidden Actions Be Invoked?
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

* WebSocket messages may bypass HTTP endpoint rate controls if separate protections are not implemented.

* Test safely with small controlled request counts.

---

## Rate-Limit Questions

```text
Are Limits Per Connection?

Per Account?

Per IP?

Per Action?

Per Object?

Across Reconnects?
```

---

## Avoid Flooding

* Do not send high-volume message floods.

* A small number of controlled messages is normally enough to test enforcement behavior.

---

## Important Safety Rule

```text
WebSocket Testing

≠

Stress Testing
```

---

## Connection Limits

* Applications may limit:

  ```text
  Connections Per User

  Connections Per IP

  Subscriptions Per Connection
  ```

* Test boundaries only when safe and authorized.

---

## Message Size

* Servers may enforce maximum frame or message sizes.

* Test near known boundaries.

* Avoid extremely large payloads that could create availability impact.

---

## Binary Messages

* Some WebSocket protocols use binary frames.

* Burp may still help observe traffic, but interpretation depends on the application protocol.

* Do not modify unknown binary structures blindly.

---

## Compression

* WebSocket extensions may support compression.

* This can affect message transport and security analysis.

* Focus first on application-level behavior unless compression itself is in scope.

---

## Error Messages

* Invalid WebSocket messages may trigger detailed errors.

* Look for:

  ```text
  Stack Traces

  Internal Class Names

  Database Errors

  Schema Details

  Internal IDs

  Debug Information
  ```

---

## Error-Based Discovery

* A safe malformed message may reveal expected fields.

* Example:

```json
{
  "type": "unknown_test_action"
}
```

* Response might indicate:

  ```text
  Unsupported event type
  ```

* Avoid broad aggressive fuzzing unless explicitly authorized.

---

## Connection State

* WebSocket behavior may depend on connection-level state.

* Example:

  ```text
  Connected

  ↓

  Authenticated

  ↓

  Joined Room

  ↓

  Subscribed

  ↓

  Active
  ```

* Record state before each test.

---

## State-Dependent Authorization

* A message may be valid only after:

  ```text
  Authentication

  Membership Check

  Subscription

  Previous Workflow Step
  ```

* Attempting it directly can reveal missing state enforcement.

---

## Multiple Connections

* The same controlled user may open multiple WebSocket connections.

* Security questions:

  ```text
  Is State Shared Correctly?

  Are Limits Shared?

  Does Logout Close All Connections?

  Do Permissions Update Across Connections?
  ```

---

## Cross-Connection State

* Example:

  ```text
  Connection A Changes State

  ↓

  Connection B Uses Old State
  ```

* This may expose stale authorization or concurrency issues.

---

## WebSocket Race Conditions

* Concurrent messages may target the same resource.

* Example:

  ```text
  Claim Reward A ─────►

  Claim Reward B ─────►
  ```

* Apply the same concurrency principles used for HTTP race testing.

---

## WebSocket Race Invariant

```text
Concurrent WebSocket Messages

Must Not Violate

Business or Security Invariants
```

* Keep request counts minimal.

---

## WebSocket Testing Workflow

```text
Define Scope

↓

Identify WebSocket Features

↓

Capture Handshake

↓

Record Authentication Mechanism

↓

Establish Legitimate Connection

↓

Map Message Types

↓

Record Message Schemas

↓

Establish Baselines

↓

Test Handshake Authentication

↓

Test Origin Validation

↓

Test Message-Level Authorization

↓

Test Object Ownership

↓

Test Role Boundaries

↓

Test Subscription Authorization

↓

Test Input Validation

↓

Test Message Replay

↓

Test State and Sequence Enforcement

↓

Test Session Invalidation

↓

Test Reconnection Behavior

↓

Review Server-Pushed Data

↓

Test Limits Safely

↓

Verify Final State

↓

Validate Impact

↓

Document Evidence
```

---

## Step 1 — Identify WebSocket Features

* Use application functionality normally.

* Look for real-time behavior.

---

## Step 2 — Capture the Handshake

* Record:

  ```text
  URL

  Cookies

  Tokens

  Origin

  Subprotocol

  Query Parameters
  ```

---

## Step 3 — Establish Baseline

* Connect normally.

* Perform one legitimate action.

---

## Step 4 — Build Message Inventory

* Map:

  ```text
  Client → Server

  Server → Client
  ```

---

## Step 5 — Map Schemas

* Identify:

  ```text
  Action Names

  IDs

  Roles

  State Values

  Nested Fields
  ```

---

## Step 6 — Test Authentication

* Compare valid and invalid authentication states.

---

## Step 7 — Test Origin Controls

* Determine whether browser-based cross-origin connection attempts are appropriately restricted.

---

## Step 8 — Test Authorization

* Use controlled accounts and objects.

---

## Step 9 — Test Subscriptions

* Attempt only controlled cross-user or cross-tenant subscriptions.

---

## Step 10 — Test Input Validation

* Change one field at a time.

---

## Step 11 — Test Replay

* Replay state-changing messages where safe.

---

## Step 12 — Test Sequencing

* Attempt safe out-of-order actions.

---

## Step 13 — Test Session Changes

* Review:

  ```text
  Logout

  Password Change

  Role Change

  Account Disablement
  ```

* Where authorized.

---

## Step 14 — Test Reconnection

* Determine whether fresh authorization occurs.

---

## Step 15 — Review Server Events

* Look for unnecessary sensitive data.

---

## Step 16 — Verify Final State

* Do not rely only on WebSocket response messages.

---

## Example — Object Authorization

### Baseline

```json
{
  "type": "get_document",
  "document_id": 1001
}
```

* User A owns document `1001`.

### Controlled Test

```json
{
  "type": "get_document",
  "document_id": 1002
}
```

* User B owns controlled document `1002`.

### Expected

```text
Access Denied

or

No Sensitive Data Returned
```

* Based on the intended permission model.

---

## Example — Subscription Authorization

### Baseline

```json
{
  "type": "subscribe",
  "channel": "user:1001"
}
```

### Controlled Test

```json
{
  "type": "subscribe",
  "channel": "user:1002"
}
```

* Verify whether User A receives User B's controlled events.

---

## Example — Session Invalidation

```text
Login

↓

Open WebSocket

↓

Perform Baseline Action

↓

Log Out

↓

Reuse Existing Connection

↓

Attempt Same Action

↓

Observe Result
```

* Determine whether continued access violates intended session behavior.

---

## Example — Message Replay

```json
{
  "type": "claim_reward",
  "reward_id": 42
}
```

### Sequential Test

```text
Send Once

↓

Verify Result

↓

Replay

↓

Verify Final State
```

* Determine whether duplicate effects occur.

---

## Example — State Enforcement

### Expected Workflow

```text
Join Room

↓

Send Message
```

### Controlled Test

```text
Connect

↓

Send Message Without Joining
```

* Determine whether the server independently validates membership.

---

## Example — Sender Identity

### Baseline

```json
{
  "type": "send_message",
  "sender_id": 1001,
  "recipient_id": 1002,
  "message": "Test"
}
```

### Controlled Test

* Modify only:

  ```text
  sender_id
  ```

* Verify whether server-side identity is derived from authentication or trusted from client input.

---

## Evidence Requirements

* Strong WebSocket evidence should include:

  ```text
  Feature

  WebSocket URL

  User / Role

  Handshake

  Authentication Method

  Origin Behavior

  Baseline Message

  Modified Message

  Changed Field

  Server Response / Event

  Final Application State

  Authorization Context

  Reproduction Steps

  Security Impact
  ```

---

## Strong Authorization Evidence

```text
User A

↓

Legitimate Access to Object A

↓

Modify Only Object ID

↓

Request Controlled Object B

↓

Unauthorized Sensitive Data or Action

↓

Verify Final State
```

---

## Strong CSWSH Evidence

```text
Cookie-Authenticated WebSocket

+

Untrusted Origin Accepted

+

Victim Credentials Applied

+

Sensitive Data Read or Action Performed

=

Validated Impact
```

* Missing origin validation alone is insufficient.

---

## Anomaly vs Vulnerability

* Anomaly:

  ```text
  WebSocket Accepts Unknown Field
  ```

* Vulnerability:

  ```text
  Unknown Field Changes Security-Sensitive State
  ```

* Anomaly:

  ```text
  Cross-Origin Handshake Accepted
  ```

* Vulnerability:

  ```text
  Cross-Origin Connection Uses Victim Authentication
  and Exposes Sensitive Capability
  ```

---

## False Positive Causes

* Common causes include:

  ```text
  Public Broadcast Channels

  Intentionally Shared Rooms

  Non-Sensitive Server Events

  Client-Side-Only IDs Ignored by Server

  Public WebSocket Endpoints

  Expected Session Persistence

  Intended Replayable Actions

  Cached UI State

  Async Processing

  Reconnection With Fresh Authorization
  ```

---

## False Positive Control

```text
Understand Feature

↓

Establish Baseline

↓

Identify Intended Permission Model

↓

Change One Variable

↓

Observe Protocol Result

↓

Verify Final State

↓

Use Controlled Accounts

↓

Repeat

↓

Confirm Meaningful Security Impact
```

---

## WebSocket Testing Checklist

### Discovery

* Identify:

  ```text
  WebSocket URL

  Real-Time Features

  Upgrade Request

  Subprotocol
  ```

---

### Authentication

* Review:

  ```text
  Cookies

  Tokens

  Query Parameters

  First-Message Authentication

  Session Expiration
  ```

---

### Origin

* Test:

  ```text
  Expected Origin

  Missing Origin

  Controlled Different Origin
  ```

---

### Authorization

* Test:

  ```text
  Object Ownership

  Role Boundaries

  Tenant Boundaries

  Hidden Actions
  ```

---

### Subscriptions

* Review:

  ```text
  Channels

  Topics

  Rooms

  User Streams

  Tenant Streams
  ```

---

### Input

* Test:

  ```text
  Missing Fields

  Wrong Types

  Boundary Values

  Extra Fields

  Safe Special Characters
  ```

---

### State

* Review:

  ```text
  Message Order

  Required Previous Actions

  One-Time Operations

  Replay

  Duplicate Processing
  ```

---

### Session Lifecycle

* Test where relevant:

  ```text
  Logout

  Password Change

  Role Change

  Disablement

  Reconnect
  ```

---

### Data Exposure

* Review:

  ```text
  Server-Pushed Events

  Hidden Fields

  Other Users' Data

  Internal Metadata
  ```

---

### Limits

* Review safely:

  ```text
  Message Frequency

  Connection Count

  Subscription Count

  Message Size
  ```

---

## WebSocket Investigation Journal

```text
Application:

Date:

Scope:

Feature:

WebSocket URL:

Handshake Endpoint:

User:

Role:

Tenant:

Authentication Method:

Cookie Authentication?:

Token Authentication?:

Origin:

Origin Validation:

Subprotocol:

Query Parameters:

Client Message Types:

Server Message Types:

Message Schemas:

Object IDs:

User IDs:

Tenant IDs:

Channels:

Subscriptions:

Baseline Messages:

Authentication Tests:

Authorization Tests:

Object-Level Tests:

Role Tests:

Tenant Tests:

Subscription Tests:

Origin Tests:

CSWSH Conditions:

Input Validation Tests:

Extra Field Tests:

Replay Tests:

Sequence Tests:

State Transition Tests:

Session Invalidation Tests:

Logout Behavior:

Role Change Behavior:

Reconnection Behavior:

Server-Pushed Data:

Information Disclosure:

Rate-Limit Observations:

Connection Limits:

Async Behavior:

Validated Findings:

False Positives:

Evidence:

Next Steps:
```

---

## Professional Tips

* Treat the WebSocket handshake and WebSocket messages as separate security layers.
* Start by using the application normally and mapping legitimate messages.
* Record both client-to-server and server-to-client traffic.
* Build a message inventory before modifying requests.
* Change one message field at a time.
* Never assume an authenticated connection authorizes every action.
* Test object-level authorization inside WebSocket messages.
* Use two controlled accounts for cross-user tests.
* Test role boundaries using controlled low- and high-privilege accounts.
* Review subscription authorization carefully.
* Verify tenant isolation for real-time events.
* Determine whether sender identity is derived server-side.
* Inspect hidden or extra message fields carefully.
* Test message sequencing and state transitions.
* Distinguish replay vulnerabilities from race conditions.
* Verify actual application state after replaying state-changing messages.
* Review logout and session invalidation behavior for persistent connections.
* Test reconnection after authentication state changes.
* Review how role changes affect existing connections.
* Investigate origin validation when cookie-based authentication is used.
* Do not report missing origin validation as CSWSH without demonstrating the required exploit conditions.
* Review server-pushed data for excessive information disclosure.
* Treat predictable channel names as an observation unless authorization is missing.
* Keep rate-limit and connection-limit tests low volume.
* Do not turn WebSocket testing into stress testing.
* Avoid blindly fuzzing unknown binary protocols.
* Record connection state before every state-dependent test.
* Verify final backend state rather than trusting protocol responses alone.
* Document both protocol-level evidence and real security impact.

---

## Common Mistakes

* Treating WebSockets exactly like normal HTTP requests.
* Testing only the handshake and ignoring messages.
* Testing only messages and ignoring handshake security.
* Failing to map normal message behavior first.
* Modifying several fields simultaneously.
* Assuming authentication means authorization.
* Ignoring object IDs inside messages.
* Ignoring role enforcement.
* Ignoring tenant isolation.
* Ignoring subscription authorization.
* Treating predictable channel names as vulnerabilities.
* Assuming sender IDs supplied by the client are trusted without testing.
* Ignoring hidden actions found in frontend code.
* Ignoring extra fields.
* Ignoring message ordering.
* Confusing replay vulnerabilities with race conditions.
* Trusting a success response without verifying state.
* Ignoring logout behavior on persistent connections.
* Ignoring token expiration.
* Ignoring role changes during active connections.
* Ignoring reconnection behavior.
* Reporting missing origin validation as exploitable CSWSH without proof.
* Ignoring server-pushed information disclosure.
* Sending excessive message floods.
* Testing connection limits aggressively.
* Using extremely large messages unnecessarily.
* Blindly modifying binary frames.
* Ignoring asynchronous processing.
* Failing to use controlled accounts.
* Failing to reproduce findings.
* Reporting protocol anomalies without meaningful impact.

---

## Practice Tasks

### Task 1 — Select an Authorized WebSocket Application

* Use:

  ```text
  Deliberately Vulnerable Lab

  Local Application

  System You Own

  or

  Explicitly Authorized Target
  ```

---

### Task 2 — Identify the WebSocket Handshake

* Record:

  ```text
  URL

  Upgrade Headers

  Origin

  Authentication

  Subprotocol
  ```

---

### Task 3 — Build a Message Inventory

* Use one real-time feature.

* Record all observed client and server message types.

---

### Task 4 — Establish a Baseline

* Perform one legitimate action.

* Record:

  ```text
  Message

  Response

  Final State
  ```

---

### Task 5 — Modify One Safe Field

* Change one controlled object identifier or harmless value.

* Compare behavior.

---

### Task 6 — Test Authentication

* Compare:

  ```text
  Valid Session

  Logged-Out Session

  Invalid Session
  ```

* Where safe.

---

### Task 7 — Test Object Authorization

* Use two controlled users and objects.

---

### Task 8 — Test Subscription Authorization

* Attempt a controlled cross-user subscription.

---

### Task 9 — Test Message Validation

* Try:

  ```text
  Missing Field

  Wrong Type

  Extra Harmless Field

  Boundary Value
  ```

---

### Task 10 — Test Replay

* Replay one safe state-changing message.

* Verify final state.

---

### Task 11 — Test Sequence Enforcement

* Attempt one safe message before its normal prerequisite.

---

### Task 12 — Test Logout Behavior

* Establish a connection.

* Log out.

* Attempt one safe action through the existing connection.

---

### Task 13 — Review Origin Handling

* Record how the server handles expected and modified origin conditions.

---

### Task 14 — Build a WebSocket Security Matrix

* Document:

  ```text
  Handshake

  Authentication

  Origin

  Authorization

  Subscriptions

  Input

  Replay

  State

  Session Lifecycle

  Data Exposure
  ```

---

## Interview Questions

1. What is a WebSocket?
2. How does WebSocket communication differ from traditional HTTP?
3. What is full-duplex communication?
4. How is a WebSocket connection established?
5. What does HTTP `101 Switching Protocols` mean?
6. Which headers are important in a WebSocket handshake?
7. What is the purpose of the `Upgrade: websocket` header?
8. Why should WebSocket security be divided into handshake and message-level testing?
9. How can you identify WebSocket traffic in Burp Suite?
10. What should you record when establishing a WebSocket baseline?
11. What message formats can WebSockets use?
12. Why should client-to-server and server-to-client messages be analyzed separately?
13. How can Burp Suite help modify WebSocket messages?
14. Why should you change one message field at a time?
15. How can WebSockets authenticate users?
16. What is first-message authentication?
17. What security risks exist before first-message authentication completes?
18. Why does an authenticated WebSocket connection not automatically authorize every message?
19. How can IDOR or BOLA occur through WebSocket messages?
20. How would you test WebSocket object-level authorization?
21. How would you test role authorization?
22. What are hidden WebSocket actions?
23. How can frontend JavaScript assist WebSocket testing?
24. Why should extra message fields be tested?
25. What input-validation tests apply to WebSocket messages?
26. What is WebSocket message replay?
27. When does replay become a security vulnerability?
28. What are message IDs or sequence numbers used for?
29. What is message-sequence testing?
30. How can state-machine flaws occur in WebSocket applications?
31. What is subscription authorization?
32. Why are predictable channel names not automatically vulnerabilities?
33. How would you test tenant isolation in WebSocket subscriptions?
34. What risks exist with server-pushed data?
35. What is Cross-Site WebSocket Hijacking?
36. What conditions are generally required for CSWSH?
37. Why is the `Origin` header important for cookie-authenticated WebSockets?
38. Why does missing origin validation not automatically prove CSWSH?
39. What is the difference between `ws://` and `wss://`?
40. How should session logout affect an existing WebSocket connection?
41. What security issues can arise when roles change during an active connection?
42. Why should reconnection behavior be tested?
43. Why should sender identity be derived server-side where possible?
44. How can business-logic vulnerabilities occur through WebSocket messages?
45. How should WebSocket rate limiting be tested safely?
46. Why should WebSocket security testing not become stress testing?
47. How can WebSocket race conditions occur?
48. What evidence is required for a strong WebSocket authorization finding?
49. What evidence is required to demonstrate meaningful CSWSH impact?
50. How can GraphQL subscriptions use WebSockets?
51. What authorization checks are important for GraphQL subscriptions?
52. How can WebSocket data lead to injection vulnerabilities?
53. Why must stored WebSocket data be traced to its final output context?
54. How can SameSite cookie behavior affect cross-site WebSocket attack feasibility?
55. Describe your complete methodology for testing WebSocket security using Burp Suite.

---

## Quick Revision

* WebSocket lifecycle:

  ```text
  HTTP Upgrade

  ↓

  101 Switching Protocols

  ↓

  Persistent Connection

  ⇅

  Bidirectional Messages
  ```

* Two security layers:

  ```text
  Handshake

  +

  Messages
  ```

* Handshake tests:

  ```text
  Authentication

  Origin

  Cookies

  Tokens

  TLS
  ```

* Message tests:

  ```text
  Authorization

  Object IDs

  Roles

  Input

  Replay

  State

  Subscriptions
  ```

* Critical principle:

  ```text
  Authenticated Connection

  ≠

  Authorization for Every Message
  ```

* Object testing:

  ```text
  User A + Object A

  ↓

  Baseline

  User A + Controlled Object B

  ↓

  Compare
  ```

* Subscription testing:

  ```text
  Authorized Channel

  vs

  Unauthorized Controlled Channel
  ```

* CSWSH:

  ```text
  Cookie Authentication

  +

  Weak Origin Validation

  +

  Cross-Site Connection

  +

  Sensitive Capability

  →

  Potential CSWSH
  ```

* Important:

  ```text
  Missing Origin Validation

  ≠

  Automatically Exploitable CSWSH
  ```

* Session lifecycle:

  ```text
  Login

  ↓

  Connect

  ↓

  Logout / Role Change

  ↓

  Existing Connection Re-Evaluated?
  ```

* Replay:

  ```text
  Send

  ↓

  Complete

  ↓

  Replay

  ↓

  Verify Final State
  ```

* Professional workflow:

  ```text
  Identify WebSocket

  ↓

  Capture Handshake

  ↓

  Map Messages

  ↓

  Establish Baseline

  ↓

  Test Authentication and Origin

  ↓

  Test Message Authorization

  ↓

  Test Objects / Roles / Tenants

  ↓

  Test Input / Replay / Sequence

  ↓

  Test Session Lifecycle

  ↓

  Review Server-Pushed Data

  ↓

  Verify Final State

  ↓

  Validate Impact
  ```

---

## Key Takeaways

* WebSockets begin with an HTTP upgrade handshake and then establish a persistent bidirectional communication channel.
* WebSocket security testing must examine both the initial handshake and every security-sensitive message exchanged afterward.
* Burp Suite can help capture WebSocket handshakes, inspect message history, intercept messages, modify values, and investigate application behavior.
* Always map legitimate WebSocket behavior and build a message inventory before modifying traffic.
* An authenticated WebSocket connection does not automatically authorize every action sent through that connection.
* Sensitive WebSocket messages require server-side authorization based on the authenticated user, role, object ownership, tenant, and current state.
* IDOR/BOLA vulnerabilities can occur when client-controlled object identifiers inside WebSocket messages are not independently authorized.
* Subscription systems require authorization just like ordinary API endpoints; predictable channel names are not vulnerabilities by themselves.
* Multi-tenant WebSocket systems must prevent users from subscribing to or receiving events belonging to other tenants.
* Client-supplied sender identities, roles, prices, statuses, and other security-sensitive fields should not be trusted without server-side validation.
* WebSocket messages require the same rigorous input validation as HTTP API requests.
* Hidden message types and extra fields may expose functionality not visible through the normal UI, but actual authorization and impact must be validated.
* Replay testing is important for one-time and state-changing actions, but replayability alone does not automatically constitute a vulnerability.
* Message ordering and state-machine enforcement should be tested because the server must not rely solely on the client to follow the intended workflow.
* Existing WebSocket connections should follow the application's intended security policy when users log out, change passwords, lose privileges, or have accounts disabled.
* Reconnection behavior should be tested to determine whether authentication and authorization are refreshed correctly.
* Cross-Site WebSocket Hijacking generally requires a combination of browser-applied credentials, insufficient origin protection, cross-site connectivity, and meaningful sensitive capability.
* Missing `Origin` validation alone is not enough to prove exploitable CSWSH.
* Server-pushed messages should be reviewed for unnecessary sensitive data, cross-user exposure, and cross-tenant leakage.
* GraphQL subscriptions carried over WebSockets require authentication, subscription authorization, object and tenant boundary checks, and review of sensitive event exposure.
* User-controlled WebSocket data may cross multiple processing boundaries, so injection testing should trace data through storage, transformation, and final rendering or execution contexts.
* Stored WebSocket-originated data may later surface through HTTP pages, dashboards, notifications, email, or other clients; output encoding and sanitization must be evaluated at the eventual sink.
* SameSite, Secure, Domain, and Path cookie attributes can materially affect cross-site WebSocket attack feasibility and should be considered alongside Origin validation.
* WebSocket rate-limit and connection-limit testing must remain low volume and should not become stress testing.
* Final application state must be verified after state-changing WebSocket tests rather than relying only on protocol responses.
* Strong findings demonstrate a clear difference between expected and actual security behavior using controlled accounts, controlled objects, reproducible messages, and meaningful impact.
* The professional WebSocket testing workflow is **identify real-time functionality → capture the upgrade handshake → understand authentication and origin controls → map client/server messages → establish legitimate baselines → test message-level authorization → test objects, roles, tenants, and subscriptions → test input validation → test replay and sequence enforcement → test session invalidation and reconnection → inspect server-pushed data → verify final state → validate impact → document evidence**.
