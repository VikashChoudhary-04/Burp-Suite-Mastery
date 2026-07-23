# Interface and Proxy Architecture

> **Module 05 — Proxy**

---

## Overview

- Effective use of Burp Proxy requires understanding both its interface and its position in the application communication path.

- Proxy is not simply a screen where requests appear.

- It is a traffic-processing layer that receives client connections, handles HTTP and HTTPS communication, records traffic, applies configured rules, and forwards messages between the client and target application.

- This lesson builds the architectural mental model required to use Proxy confidently and troubleshoot it systematically.

---

## Learning Objectives

- By the end of this lesson, you will be able to:

  * Identify the major areas of the Proxy interface.
  * Explain the purpose of Intercept, HTTP history, WebSockets history, and Proxy settings.
  * Understand how traffic reaches Burp through a proxy listener.
  * Explain the basic HTTP proxying flow.
  * Understand HTTPS interception at a conceptual level.
  * Distinguish client-to-Burp and Burp-to-server connections.
  * Understand how Burp records and processes proxied traffic.
  * Recognize common architectural causes of proxy failures.
  * Build a reliable mental model for later Proxy workflows.

---

## Proxy Interface

- Depending on the Burp Suite version and edition, exact labels and interface placement may change.

- The core Proxy capabilities generally include:

  ```text
  Proxy
  │
  ├── Intercept
  ├── HTTP History
  ├── WebSockets History
  └── Proxy Settings
  ```

- Each area serves a different purpose.

---

## Intercept

- Intercept provides direct control over selected traffic before it continues through Burp.

- It can be used to:

  * Pause requests.
  * Inspect request contents.
  * Modify request contents.
  * Forward requests.
  * Drop requests.
  * Send requests to other Burp tools.
  * Control selected response interception where configured.

- Conceptually:

  ```text
  Browser
      │
      ▼
  Request Paused in Intercept
      │
      ├── Inspect
      ├── Modify
      ├── Forward
      └── Drop
  ```

- Intercept is useful when real-time control is required.

- It is not necessary for every request during normal browsing.

---

## HTTP History

- HTTP history records HTTP requests and responses that pass through Burp Proxy.

- It provides a chronological view of application traffic.

- Typical information includes:

  * Host.
  * HTTP method.
  * URL.
  * Parameters.
  * Status code.
  * MIME type.
  * Response length.
  * Request contents.
  * Response contents.

- HTTP history is often more useful than continuous interception during application exploration.

- A common workflow is:

  ```text
  Browse Normally

  ↓

  HTTP History Records Traffic

  ↓

  Filter and Review Requests

  ↓

  Select Security-Relevant Request

  ↓

  Send to Appropriate Burp Tool
  ```

---

## WebSockets History

- WebSockets history records messages exchanged over WebSocket connections observed through Burp.

- WebSockets differ from ordinary request-response HTTP communication because they can maintain a persistent bidirectional connection.

- A simplified flow is:

  ```text
  HTTP Upgrade Handshake

  ↓

  WebSocket Connection Established

  ↓

  Client ↔ Server Messages
  ```

- WebSockets history can help inspect:

  * Client-to-server messages.
  * Server-to-client messages.
  * Authentication-related data.
  * Object identifiers.
  * Real-time actions.
  * Subscription events.
  * Application-specific message formats.

---

## Proxy Settings

- Proxy settings control how Burp accepts and processes proxied traffic.

- Depending on the Burp version, settings may include:

  * Proxy listeners.
  * Request interception rules.
  * Response interception rules.
  * Match and Replace rules.
  * TLS-related behavior.
  * Other traffic-processing options.

- Configuration should be changed deliberately because Proxy settings can affect all traffic routed through Burp.

---

## Core Proxy Architecture

- The simplest architectural model is:

  ```text
  Client
    │
    ▼
  Burp Proxy
    │
    ▼
  Server
  ```

- A more accurate model separates the communication into two sides:

  ```text
  Client
    │
    │ Client-to-Proxy Connection
    ▼
  Burp Proxy
    │
    │ Proxy-to-Server Connection
    ▼
  Target Server
  ```

- Burp acts as an intermediary between these two connections.

---

## Why Two Connections Matter

- When using Burp, the browser is not usually communicating directly with the target server at the transport level.

- Instead:

  ```text
  Connection 1

  Browser
      ↕
  Burp
  ```

- And:

  ```text
  Connection 2

  Burp
      ↕
  Target Server
  ```

- Burp receives traffic from the client, processes it, and then communicates with the destination server.

- This architecture enables interception and inspection.

---

## Proxy Listener

- A proxy listener is the local network endpoint where Burp waits for incoming proxy connections.

- A common default configuration is:

  ```text
  Address: 127.0.0.1
  Port:    8080
  ```

- The browser must be configured to send appropriate traffic to that listener.

- Conceptually:

  ```text
  Browser Proxy Configuration

  127.0.0.1:8080
        │
        ▼
  Burp Proxy Listener
        │
        ▼
  Burp Processes Traffic
  ```

---

## Loopback Address

- `127.0.0.1` is a loopback address.

- It refers to the local machine.

- When Burp listens only on loopback:

  * Local applications can connect to the listener.
  * Other devices generally cannot connect directly to that listener through the network.

- This is a sensible default for many desktop testing environments.

- Exposing a proxy listener to other interfaces should only be done intentionally and with an understanding of the security implications.

---

## Listener Port

- A listener also requires a TCP port.

- Burp commonly uses:

  ```text
  8080
  ```

- The browser and Burp must agree on the destination port.

- For example:

  ```text
  Browser configured for:

  127.0.0.1:8080
  ```

  must correspond to:

  ```text
  Burp listener:

  127.0.0.1:8080
  ```

- If the ports do not match, traffic will not reach the intended listener.

---

## HTTP Proxy Flow

- For ordinary HTTP traffic, a simplified flow is:

  ```text
  Browser
      │
      │ HTTP Request
      ▼
  Burp Listener
      │
      ▼
  Burp Proxy Processing
      │
      ▼
  Target Server
      │
      │ HTTP Response
      ▼
  Burp Proxy
      │
      ▼
  Browser
  ```

- Burp can record the traffic and apply configured processing rules as it passes through.

---

## HTTPS Adds TLS

- HTTPS protects HTTP traffic using TLS.

- Without an intercepting proxy, the simplified relationship is:

  ```text
  Browser
      │
      │ Encrypted TLS Connection
      ▼
  HTTPS Server
  ```

- An intercepting proxy introduces an intermediary.

- Conceptually:

  ```text
  Browser
      │
      │ TLS
      ▼
  Burp
      │
      │ TLS
      ▼
  Target Server
  ```

- Burp must be able to establish trusted communication with the browser for HTTPS interception to work without certificate warnings.

---

## Burp CA Certificate

- Burp uses its own Certificate Authority capability to support HTTPS interception.

- In an authorized testing browser, the Burp CA certificate can be trusted so that Burp-generated certificates for intercepted HTTPS destinations are accepted.

- Conceptually:

  ```text
  Browser
      │
      │ Trusts Burp CA
      ▼
  Burp
      │
      │ Establishes Separate TLS Connection
      ▼
  Target Server
  ```

- This allows Burp to inspect HTTP messages carried inside HTTPS connections.

---

## HTTPS Interception Mental Model

- A useful conceptual model is:

  ```text
  Browser
      │
      │ HTTPS
      ▼
  Burp Proxy
      │
      │ Inspect HTTP Message
      │
      │ Apply Proxy Processing
      ▼
  HTTPS
      │
      ▼
  Target Server
  ```

- The browser still sees an HTTPS connection.

- The target server still receives HTTPS communication from Burp.

- Burp operates between them.

---

## Certificate Trust Problems

- If the browser does not trust the Burp CA certificate, HTTPS sites may produce certificate errors.

- This can appear as:

  * Certificate warnings.
  * TLS failures.
  * Pages refusing to load.
  * Browser security errors.

- The correct troubleshooting question is not immediately:

  ```text
  Is the website broken?
  ```

- Instead ask:

  ```text
  Is the Browser Using Burp?

  ↓

  Is the Listener Running?

  ↓

  Is HTTPS Being Intercepted?

  ↓

  Does the Browser Trust the Correct Burp CA?
  ```

---

## Traffic Processing Pipeline

- A useful high-level model for proxied traffic is:

  ```text
  Client Sends Request

  ↓

  Proxy Listener Receives Request

  ↓

  Burp Applies Relevant Proxy Processing

  ↓

  Request May Be Intercepted

  ↓

  Request Is Forwarded

  ↓

  Target Server Processes Request

  ↓

  Response Returns Through Burp

  ↓

  Burp Records / Processes Response

  ↓

  Response Reaches Client
  ```

- Exact internal behavior depends on configuration and protocol details, but this model is sufficient for understanding normal Proxy workflows.

---

## Interception Is Only One Processing Stage

- Interception should not be confused with the entire Proxy system.

- Proxy can still:

  * Receive traffic.
  * Forward traffic.
  * Record HTTP history.
  * Populate application information.
  * Apply configured rules.

- even when manual request interception is disabled.

- Therefore:

  ```text
  Intercept OFF

  ≠

  Proxy OFF
  ```

---

## Example: Intercept OFF

```text
Browser
    │
    ▼
Burp Proxy
    │
    ├── Record in HTTP History
    │
    ▼
Target Server
```

- Traffic continues automatically.

- The tester can review it afterward.

---

## Example: Intercept ON

```text
Browser
    │
    ▼
Burp Proxy
    │
    ▼
Paused Request
    │
    ├── Inspect
    ├── Modify
    ├── Forward
    └── Drop
```

- Traffic pauses when it matches the applicable interception behavior.

---

## Proxy and Target Scope

- Burp may receive traffic for many destinations.

- Examples include:

  * The intended target.
  * Search engines.
  * Browser update services.
  * Analytics providers.
  * Content delivery networks.
  * Third-party APIs.
  * Extensions.
  * Background browser requests.

- This is why scope matters.

- Conceptually:

  ```text
  All Observed Traffic
        │
        ├── In Scope
        │
        └── Out of Scope
  ```

- Scope helps separate relevant assessment traffic from unrelated communication.

---

## Traffic Visibility Does Not Equal Authorization

- A proxy can technically observe traffic that is not authorized for active testing.

- Therefore:

  ```text
  Burp Can See It

  ≠

  You May Test It
  ```

- Authorization and rules of engagement always take priority over technical capability.

---

## Proxy and DNS

- Proxy troubleshooting sometimes becomes confusing because multiple systems participate in loading a web page.

- A request may depend on:

  * Browser configuration.
  * Proxy listener availability.
  * DNS resolution.
  * Network connectivity.
  * TLS negotiation.
  * Target server availability.
  * Burp configuration.

- A failure at any stage can appear to the user as:

  ```text
  Page Did Not Load
  ```

- Troubleshooting should identify which layer failed.

---

## Proxy Architecture Troubleshooting Model

```text
Browser
    │
    ▼
Is Proxy Configuration Correct?
    │
    ▼
Is Burp Listener Running?
    │
    ▼
Can Burp Process the Connection?
    │
    ▼
Does DNS / Network Resolution Work?
    │
    ▼
Does TLS Succeed?
    │
    ▼
Can the Target Server Respond?
    │
    ▼
Does the Response Return to Browser?
```

- This layered approach is more reliable than changing random settings.

---

## Common Architecture Problems

### Browser Is Not Using Burp

- Symptoms may include:

  * Pages load normally.
  * No expected traffic appears in Burp.

- Check:

  * Browser proxy settings.
  * Proxy extension state.
  * Correct host and port.

---

### Burp Listener Is Not Running

- Symptoms may include:

  * Browser reports proxy connection failure.
  * No traffic reaches Burp.

- Check:

  * Listener status.
  * Listener address.
  * Listener port.
  * Port conflicts.

---

### Wrong Listener Port

- Example:

  ```text
  Browser → 127.0.0.1:8081
  Burp    → 127.0.0.1:8080
  ```

- The configurations do not match.

---

### Port Conflict

- Another process may already be using the configured port.

- This can prevent Burp from binding successfully.

- Troubleshooting should verify which process owns the port before changing configuration.

---

### HTTPS Trust Problem

- HTTP may work while HTTPS fails.

- This often suggests investigating:

  * CA certificate trust.
  * TLS behavior.
  * Browser certificate configuration.

---

### Intercept Is Holding Requests

- A page may appear to hang because a request is waiting in Intercept.

- Check whether:

  ```text
  Intercept Is ON
  ```

- Then inspect, forward, or drop the pending request as appropriate.

---

## Professional Interface Workflow

```text
Start Burp

↓

Verify Proxy Listener

↓

Verify Browser Proxy Configuration

↓

Confirm HTTPS Trust

↓

Define / Confirm Scope

↓

Browse with Intercept OFF

↓

Review HTTP History

↓

Use Intercept Only When Needed

↓

Send Selected Requests to Other Tools
```

- This workflow minimizes unnecessary interruption while maintaining visibility.

---

## Interface Discipline

- A professional tester should understand which Proxy area answers which question.

### Need to pause a request?

```text
Intercept
```

### Need to review previous HTTP traffic?

```text
HTTP History
```

### Need to inspect WebSocket messages?

```text
WebSockets History
```

### Need to configure listeners or traffic-processing behavior?

```text
Proxy Settings
```

- Knowing where to look reduces unnecessary configuration changes.

---

## Professional Tips

- Verify the listener before troubleshooting application behavior.

- Keep the browser and Burp listener configuration synchronized.

- Use a dedicated testing browser profile where practical.

- Keep Intercept off during broad exploration unless real-time modification is required.

- Use HTTP history to select requests for deeper investigation.

- Treat HTTPS certificate errors as a trust or TLS problem until proven otherwise.

- Do not expose proxy listeners to external network interfaces without a specific need and appropriate safeguards.

- Troubleshoot one layer at a time.

- Confirm scope before active testing.

---

## Common Mistakes

- Assuming Intercept OFF means Proxy is disabled.

- Changing multiple Proxy settings at once while troubleshooting.

- Forgetting which port the browser is configured to use.

- Running multiple tools on the same listener port.

- Installing or trusting the wrong CA certificate.

- Exposing a listener unnecessarily.

- Treating every page-loading failure as a Burp bug.

- Ignoring DNS, TLS, or target availability when troubleshooting.

- Forgetting that background browser traffic may also pass through Burp.

- Performing active tests against out-of-scope traffic merely because it appears in HTTP history.

---

## Practice Tasks

1. Open Burp Suite and locate the Proxy area.

2. Identify:

   * Intercept.
   * HTTP history.
   * WebSockets history.
   * Proxy settings.

3. Locate the active proxy listener.

4. Record:

   ```text
   Listener Address:
   Listener Port:
   Running:
   ```

5. Verify that your authorized testing browser is configured to use the same listener.

6. With Intercept off:

   * Browse an authorized lab.
   * Confirm traffic appears in HTTP history.

7. Turn Intercept on:

   * Trigger one controlled request.
   * Observe that it pauses.
   * Forward it.

8. Turn Intercept off again.

9. Compare:

   ```text
   Intercept ON
   ```

   with:

   ```text
   Intercept OFF
   ```

10. Explain why Proxy still functions in both states.

11. Visit an HTTPS endpoint in your authorized lab and confirm that HTTPS interception works correctly.

12. Draw the two-connection model:

   ```text
   Browser ↔ Burp ↔ Server
   ```

13. Identify which connection exists on each side of Burp.

---

## Interview Questions

1. What are the main areas of the Burp Proxy interface?

2. What is the purpose of a proxy listener?

3. What is the common default Burp Proxy listener address and port?

4. What does `127.0.0.1` represent?

5. Why does the browser need to be configured to use Burp's listener?

6. Why can Burp inspect HTTPS traffic?

7. What role does the Burp CA certificate play?

8. What is the difference between the client-to-Burp and Burp-to-server connections?

9. Does turning Intercept off disable Proxy?

10. Why might a page appear to hang when Intercept is enabled?

11. What could cause HTTP traffic to work while HTTPS traffic fails?

12. Why should proxy troubleshooting be performed layer by layer?

13. What is the difference between HTTP history and Intercept?

14. Why might unrelated third-party traffic appear in Burp?

15. Why does visibility in Proxy not imply authorization to test a destination?

---

## Quick Revision

```text
Proxy Interface
    │
    ├── Intercept
    ├── HTTP History
    ├── WebSockets History
    └── Proxy Settings
```

```text
Browser
    │
    │ Connection 1
    ▼
Burp
    │
    │ Connection 2
    ▼
Target Server
```

```text
Browser Proxy Configuration
        ↓
Burp Listener
        ↓
Proxy Processing
        ↓
Target Server
```

```text
Intercept OFF
    ≠
Proxy OFF
```

```text
HTTPS Interception
    =
Browser Trusts Burp CA
    +
Burp Handles Client-Side TLS
    +
Burp Establishes Server-Side TLS
```

```text
Page Failure
    ↓
Check Browser Configuration
    ↓
Check Listener
    ↓
Check Network / DNS
    ↓
Check TLS
    ↓
Check Target
```

---

## Key Takeaways

- Burp Proxy consists of several related capabilities, including Intercept, HTTP history, WebSockets history, and Proxy settings.

- A proxy listener receives client traffic and is the entry point into Burp's proxying workflow.

- Burp operates between the client and server, creating a useful two-connection mental model.

- HTTPS interception depends on TLS handling and appropriate trust of the Burp CA in the authorized testing client.

- Intercept is only one part of Proxy; disabling interception does not disable traffic proxying or HTTP history.

- Reliable troubleshooting requires checking each layer systematically rather than changing random settings.

- Traffic visibility and technical capability never override assessment scope or authorization.
