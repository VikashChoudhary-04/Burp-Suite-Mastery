# Intercepting Proxy Architecture

> **Module 04 — How Burp Suite Works**

> **Progress**

```text
█░░░░░░░░░░ 1 / 11 Lessons
```

- Burp Suite is often described as an **intercepting proxy**.

- That definition is correct, but understanding Burp professionally requires a deeper mental model.

- Burp is not simply "watching" traffic traveling directly between your browser and a server.

- When configured as a proxy, Burp becomes an active intermediary in the communication path.

- A useful starting model is:

  ```text
  Browser

  ↓

  Burp Suite

  ↓

  Target Server
  ```  

- But the real architecture is better understood as:

  ```text
  Browser

  ↓ Connection 1

  Burp Suite
  
  ↓ Connection 2

  Target Server
  ```

- This distinction explains how Burp can inspect, modify, delay, replay, and analyze web traffic.

---

## Learning Objectives

- By the end of this lesson, you should be able to:

  * Explain what a forward proxy is.
  * Explain what makes Burp an intercepting proxy.
  * Understand that Burp creates separate communication relationships.
  * Distinguish interception from passive observation.
  * Explain why the server communicates with Burp rather than directly with the browser at the transport layer.
  * Understand how Burp can modify requests before forwarding them.
  * Distinguish a proxy from a packet sniffer.
  * Build the correct mental model for later Burp modules.

---

## Normal Browser Communication

- Without an explicitly configured interception proxy, a simplified connection looks like:

  ```text
  Browser

  ↓

  Target Server
  ```

- For HTTPS:

  ```text
  Browser

  ↓

  TLS Connection

  ↓

  Target Server
  ```  

- The browser creates a request.

- Example:

  ```http
  GET /account HTTP/1.1
  Host: example.com
  ```

- The server processes it and returns a response.

  ```http
  HTTP/1.1 200 OK
  Content-Type: text/html

  <html>
  ...
  </html>
  ```

- Conceptually:

  ```text
  Browser

  Request
  ──────────────►

  Server

  Response
  ◄──────────────
  ```

- Burp is not involved.

---

## Introducing a Proxy

- A proxy acts as an intermediary.

- Instead of the browser communicating through the same direct application-layer relationship with the destination, the configured path becomes:

  ```text
  Browser

  ↓

  Proxy

  ↓

  Server
  ```
  
- The browser sends traffic through the proxy.

- The proxy then communicates onward with the destination.

---

## Forward Proxy

- Burp primarily behaves as a **forward proxy** from the browser's perspective.

- A forward proxy acts on behalf of clients.

- Conceptually:

  ```text
  Client

  ↓

  Forward Proxy

  ↓

  Internet / Destination Server
  ```

- Examples of clients could include:

  ```text
  Browser

  Mobile Application

  API Client

  Other HTTP-Capable Software
  ```  

if configured appropriately.

---

## Reverse Proxy vs Forward Proxy

- Do not confuse the two concepts.

- A forward proxy commonly sits conceptually near the client:

  ```text
  Client

  ↓

  Forward Proxy

  ↓

  Server
  ```

- A reverse proxy commonly sits conceptually near the server:

  ```text
  Client

  ↓

  Reverse Proxy

  ↓

  Backend Server
  ```

- Examples of reverse-proxy roles include:

  * Load balancing.
  * TLS termination.
  * Caching.
  * Web application firewalling.
  * Routing requests to backend services.

- During a penetration test, both may exist simultaneously.

- Example:

  ```text
  Browser

  ↓

  Burp Forward Proxy

  ↓

  CDN / Reverse Proxy

  ↓

  Application Server
  ```  

- Understanding these layers becomes important when interpreting application behavior.

---

## What Makes Burp an Intercepting Proxy?

- A normal proxy may simply forward traffic.

- Burp can additionally pause selected messages before forwarding them.

- Conceptually:

  ```text
  Browser

  ↓

  Request

  ↓

  Burp Interception Point

  ↓

  Pause

  ↓

  Inspect / Modify / Forward / Drop

  ↓

  Server
  ```  

- This is why it is called an **intercepting proxy**.

---

## Interception Does Not Mean Passive Observation

- This distinction is important.

- A passive network observer may conceptually:

  ```text
  Observe Traffic

  ↓

  Analyze Copies of Packets
  ```

- Burp instead participates directly in the communication path.

- Conceptually:

  ```text
  Browser

  ↓

  Burp Receives Request

  ↓

  Burp Decides What to Forward
  
  ↓
  
  Server Receives Forwarded Request
  ```

- Burp is an active intermediary.

---

## Two Communication Legs

- The most important architecture concept in this lesson is:

  ```text
  Browser ↔ Burp

  and

  Burp ↔ Server
  ```

- These are separate communication legs.

- A more accurate diagram is:

  ```text
  ┌─────────┐
  │ Browser │
  └────┬────┘
       │
       │ Client-side connection
       │
       ▼
  ┌────────────┐
  │ Burp Suite │
  └─────┬──────┘
        │
        │ Server-side connection
        │
        ▼
  ┌───────────────┐
  │ Target Server │
  └───────────────┘
  ```

- This becomes especially important for HTTPS.

---

## Why Two Connections Matter

- Because Burp sits between the endpoints, it can independently interact with both sides.

- Conceptually:

  ```text
  Browser believes:

  "I am communicating through my configured proxy."
  ```  

- Burp:

  ```text
  Receives browser traffic

  ↓

  Processes it

  ↓  

  Creates / reuses onward communication with target
  ```

- The target server receives traffic through the connection established by Burp.

---

## The Server Does Not Receive the Browser's Original Network Connection

- At the transport level, the server-side connection is established from Burp's side of the proxy relationship.

- Conceptually:

  ```text
  Browser TCP/TLS Relationship

  terminates at

  Burp
  ```

- Then:

  ```text
  Burp TCP/TLS Relationship

  continues toward

  Server
  ```

- The exact behavior depends on protocol and configuration, but the key mental model remains:

  ```text
  Two Separate Legs
  ```

- not:

  ```text
  One Untouched End-to-End Connection
  with Burp merely watching
  ```

---

## Why Burp Can Modify Requests

- Suppose the browser creates:

  ```http
  GET /account?id=105 HTTP/1.1
  Host: example.com
  Cookie: session=abc123
  ```

- With interception enabled, Burp receives it before forwarding it onward.

- At this point:

  ```text
  Browser Request

  ↓

  Burp
  ```  

- The target application has not yet processed the forwarded request.

- You can change:

  ```text
  id=105
  ```  

- to:

  ```text
  id=106
  ```

- Burp can then send:

  ```http
  GET /account?id=106 HTTP/1.1
  Host: example.com
  Cookie: session=abc123
  ```

to the server.

- The server processes the request Burp actually forwards.

---

## Original Browser Intent vs Forwarded Request

- Think of two versions:

  ```text
  Browser-Generated Request

  ↓

  Burp Modification
  
  ↓

  Forwarded Request
  ```

- They may be identical.

- Or they may differ.

- Example:

  ```text
  Browser Generated:

  quantity=1
  ```  

- Modified in Burp:

  ```text
  quantity=10
  ```

- Server receives:

  ```text
  quantity=10
  ```

assuming Burp forwards the modified request successfully.

---

## Burp Is Not Changing the Application Source Code

- When modifying:

  ```text
  price=1000
  ```

- to:

  ```text
  price=1
  ```

- you are not editing:

  * JavaScript source code on the server.
  * Backend source code.
  * Database records directly.
  * Application configuration.

- You are modifying the **HTTP message** sent to the application.

- The application then decides how to process that input.

---

## Why This Is Powerful

- Browsers normally constrain users through interfaces.

- For example:

  ```text
  Dropdown:

  Quantity
  1
  2
  3
  4
  5
  ```

- The UI may not offer:

  ```text
  100
  ```

- But the actual HTTP request might contain:

  ```text
  quantity=5
  ```

- Burp allows a tester to investigate what happens if the server receives:

  ```text
  quantity=100
  ```

- This reveals an important principle:

  ```text
  User Interface Restrictions

  ≠

  Server-Side Security Controls
  ```

---

## The Browser Is Only One HTTP Client

- Applications often assume users interact through the intended UI.

- But security testing treats HTTP as the real interface.

- Conceptually:

  ```text
  Visible UI

  ↓

  Browser Logic

  ↓
  
  HTTP Request

  ↓

  Server
  ```

- Burp allows testing at the HTTP layer:

  ```text
  HTTP Request

  ↓

  Controlled Modification

  ↓

  Server Behavior
  ```

- This is why understanding HTTP before Burp was essential.

---

## Request Direction

- Normal request direction:

  ```text
  Browser

  ↓

  Burp

  ↓

  Server
  ```  

- Burp may:

  ```text
  Inspect

  Modify

  Forward

  Drop

  Log
  ```  

the request depending on configuration and tool usage.

---

## Response Direction

- Responses travel back:

  ```text
  Server

  ↓

  Burp

  ↓  

  Browser
  ```  

- Burp can inspect responses as well.

- Depending on configuration and workflow, responses can also be intercepted or modified.

---

## Complete Communication Model

- A simplified exchange:

  ```text
  1. Browser creates request

          ↓

  2. Browser sends request to Burp

          ↓

  3. Burp receives request

          ↓

  4. Burp processes interception rules

          ↓

  5. Request is forwarded

          ↓

  6. Server receives request
  
          ↓

  7. Server processes request

          ↓

  8. Server returns response to Burp

          ↓

  9. Burp receives response

          ↓

  10. Burp processes / records response

          ↓

  11. Response reaches browser

          ↓

  12. Browser renders / processes result
  ```

- This lifecycle is foundational to almost every Burp tool.

---

## Intercept OFF

- When interception is off:

  ```text
  Browser

  ↓

  Burp

  ↓

  Forward Automatically

  ↓

  Server
  ```

- Burp is still acting as the proxy.

- This is critical.

- Do not confuse:

  ```text
  Intercept OFF
  ```

- with:

  ```text
  Burp Proxy Disabled
  ```

- They are different concepts.

---

## Intercept OFF Does Not Mean Burp Is Bypassed

- With the browser still configured to use Burp:

  ```text
  Intercept OFF

  ↓

  Traffic Still Passes Through Burp
  ```  

- Requests can still appear in:

  ```text
  Proxy

  ↓

  HTTP History
  ```

even though they were not manually paused.

---

## Intercept ON

- With interception enabled:

  ```text
  Browser

  ↓

  Burp

  ↓

  Request Paused
  ```

- You can then decide what happens.

- Typical actions include:

  ```text
  Forward

  Drop

  Modify then Forward
  ```

- Until the request is forwarded or dropped, the browser may appear to wait.

---

## Why Pages Sometimes Appear to Freeze

- Suppose a webpage needs:

  ```text
  HTML

  CSS

  JavaScript

  Images

  API Requests
  ```  

- If interception is enabled, many requests may pause.

- The browser may appear stuck because:

  ```text
  Browser Is Waiting

  ↓

  Burp Has Paused Required Request
  ```

- This is expected behavior, not necessarily a network failure.

---

## Burp vs Packet Sniffer

- Burp Suite and packet-analysis tools solve different problems.

- A simplified packet sniffer model:

  ```text
  Network Traffic

  ↓

  Capture Packets

  ↓

  Analyze Protocol Data
  ```  

- A Burp model:

  ```text
  Application HTTP Traffic

  ↓

  Proxy Intermediation

  ↓

  Inspect / Modify / Replay
  ```

---

## Burp and Wireshark Are Not the Same Type of Tool

- A packet analyzer such as Wireshark can inspect lower-level network traffic and many protocols.

- Burp specializes in application-security testing, especially web protocols and workflows.

- Conceptually:

  ```text
  Wireshark

  Packets / Protocol Analysis
  ```

  ```text
  Burp

  Web Application Traffic Manipulation
  ```

- They can complement each other.

---

## Burp Does Not Automatically See All Device Traffic

- Burp sees traffic that is:

  * Routed through its configured listener.
  * Supported by its proxying capabilities.
  * Generated by clients configured to use it or otherwise redirected appropriately.

- Do not assume:

  ```text
  Burp Running

  =

  Every Network Packet Automatically Visible
  ```

- That is not how the proxy model works.

---

## Proxy Listener Concept

- Burp needs a place to receive proxy traffic.

- A common listener configuration is:

  ```text
  Address:
  127.0.0.1

  Port:
  8080
  ```

- Conceptually:

  ```text
  Browser

  ↓

  127.0.0.1:8080

  ↓

  Burp Proxy Listener
  ```

- The listener accepts traffic from the configured client.

- Proxy listeners are covered in detail later in this module.

---

## Why 127.0.0.1?

- `127.0.0.1` refers to the local machine through the loopback interface.

- If the browser and Burp are running on the same machine:

  ```text
  Browser

  ↓

  127.0.0.1:8080

  ↓

  Burp on Same Computer
  ```

- This is a common local testing configuration.

---

## The Proxy Port Is Not the Target Port

- Suppose:

  ```text
  Burp Listener:
  127.0.0.1:8080
  ```

- and the target is:

  ```text
  https://example.com
  ```

- Port `8080` here is the local port where the browser contacts Burp.

- It does not mean the target application itself runs on port `8080`.

- Conceptually:

  ```text
  Browser

  ↓

  Burp Listener
  127.0.0.1:8080

  ↓

  Target
  example.com:443
  ```

- These are different connections.

---

## Burp as a Controlled Middle Layer

- A useful architecture model:

  ```text
  ┌───────────────┐
  │    Browser    │
  └───────┬───────┘
          │
          │ HTTP / HTTPS via proxy
          ▼
  ┌───────────────┐  
  │  Burp Proxy   │
  │               │
  │ Inspect       │
  │ Modify        │
  │ Log           │
  │ Route         │
  └───────┬───────┘
          │
          │ HTTP / HTTPS
          ▼
  ┌───────────────┐
  │ Target Server │
  └───────────────┘
  ```

- This is more accurate than imagining Burp as a transparent magnifying glass.

---

## Burp as an Application-Layer Testing Platform

- Burp does more than proxy traffic.

- Once a request is captured, it can move into other tools.

- Conceptually:

  ```text
  Proxy

  ↓

  Interesting Request

  ├── Repeater
  ├── Intruder
  ├── Scanner
  ├── Comparer
  └── Other Tools / Extensions
  ```

- The proxy architecture provides the traffic that feeds the broader testing platform.

---

## Example — Login Request

- Browser generates:

  ```http
  POST /login HTTP/1.1
  Host: example.com
  Content-Type: application/x-www-form-urlencoded

  username=alice&password=ExamplePass
  ```

- Burp receives it.

- A tester can inspect:

  ```text
  Method:
  POST

  Endpoint:
  /login

  Parameters:
  username
  password

  Content Type:
  application/x-www-form-urlencoded
  ```

- Then forward it unchanged.

- This is passive observation through an active proxy architecture.

---

## Example — Controlled Modification

- Suppose a profile request is:

  ```http
  GET /api/profile/105 HTTP/1.1
  Host: example.com
  Cookie: session=abc123
  ```

- Burp can modify:

  ```text
  105
  ```

before forwarding.

- But professional methodology still requires:

  ```text
  Baseline

  ↓

  Known Ownership
  
  ↓

  Controlled Modification

  ↓

  Response Comparison

  ↓

  Impact Verification
  ```  

- The ability to modify traffic does not remove the need for evidence-based reasoning.

---

## Browser DevTools vs Burp

- Browser developer tools can inspect many requests generated by the browser.

- Burp adds capabilities such as:

  ```text  
  Pause Before Server Processing

  Modify Raw Requests

  Replay Requests Independently

  Systematically Vary Inputs

  Route Requests Between Security Tools

  Maintain Testing History
  ```

- The tools overlap in some visibility but serve different purposes.

---

## Burp's Position Creates Power and Responsibility

- Because Burp sits in the communication path, it can alter application behavior.

- For example:

  ```text
  Original Request

  ↓

  Modified Request

  ↓

  State-Changing Server Action
  ```

- Therefore testing must remain:

  ```text
  Authorized

  Scoped

  Controlled

  Minimally Disruptive
  ```

- Interception gives technical capability, not permission.

---

## Common Architecture Mistakes

- Avoid these mental models:

  ```text  
  Burp only watches traffic.
  ```

- Incorrect.

- Burp actively proxies traffic.

---

```text
Intercept OFF means traffic bypasses Burp.
```

- Incorrect.

- Traffic can still pass through the proxy automatically.

---

```text
Burp edits the server's source code when I modify a request.
```

- Incorrect.

- Burp modifies messages sent to the server.

---

```text
The browser and server always maintain one untouched end-to-end connection through Burp.
```

- Incorrect.

- Think in separate communication legs.

---

```text
If Burp is open, it automatically captures all network traffic.
```

- Incorrect.

- Traffic must reach Burp through appropriate proxy/routing configuration.

---

```text
Port 8080 must be the target application's port.
```

- Incorrect.

- It is commonly the local Burp proxy-listener port.

---

```text
A browser UI restriction means the server enforces the same restriction.
```

- Incorrect.

- Server-side behavior must be tested independently.

---

## Professional Mental Model

- When using Burp, think:

  ```text
  Client Creates Message

  ↓

  Burp Receives Message

  ↓

  I Can Observe or Modify It

  ↓

  Burp Sends Message Onward

  ↓

  Server Interprets What It Receives

  ↓

  Server Responds

  ↓
  
  Burp Receives Response

  ↓
  
  Client Receives Response
  ```

- The key phrase is:

  ```text
  Server Interprets What It Receives
  ```  

- not:

  ```text  
  Server Knows What the Browser Originally Intended
  ```

---

## Investigation Questions

- When analyzing unusual behavior, ask:

  ```text
  Did the browser generate the request I expected?

  Did the request reach Burp?

  Was interception enabled?

  Did I modify anything?

  What exactly did Burp forward?

  Did the server respond?

  Did Burp receive the response?

  Did the browser receive the response?

  Could another intermediary exist between Burp and the application?
  ```

- This reasoning is useful both for testing and troubleshooting.

---

## Architecture Checklist

```text
[ ] Browser/client is configured to send traffic through Burp.

[ ] Burp proxy listener is running.

[ ] I understand the listener address and port.

[ ] I distinguish Intercept OFF from proxy bypass.

[ ] I understand Browser ↔ Burp as one communication leg.

[ ] I understand Burp ↔ Server as another communication leg.

[ ] I know Burp can modify the message before forwarding it.

[ ] I understand that the server processes the forwarded request.

[ ] I distinguish Burp from passive packet sniffing.

[ ] I understand that Burp does not automatically capture all device traffic.

[ ] I recognize that other intermediaries may exist after Burp.

[ ] I test only authorized systems.
```

---

## Key Takeaways

* Burp Suite acts as an intercepting forward proxy between a client and target applications.
* Burp is an active intermediary, not merely a passive traffic observer.
* The most useful architecture model is `Browser ↔ Burp` and `Burp ↔ Server`.
* Burp receives requests before forwarding them, which allows controlled inspection and modification.
* The server processes the request that Burp actually forwards.
* Intercept OFF does not mean Burp is bypassed.
* A proxy listener receives client traffic, commonly on `127.0.0.1:8080` for local setups.
* The Burp listener port and target application's destination port are separate concepts.
* Burp sees traffic routed through it; simply running Burp does not expose every network packet.
* UI restrictions are not substitutes for server-side security controls.
* Burp's proxy architecture feeds requests into tools such as Repeater, Intruder, Scanner, and Comparer.
* Understanding this architecture is essential before studying HTTPS interception and Burp's CA certificate.
