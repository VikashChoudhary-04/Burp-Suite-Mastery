# Browser, Burp, and Server Communication

> **Module 04 — How Burp Suite Works**

> **Progress**

```text
██░░░░░░░░░ 2 / 11 Lessons
```

- When Burp Suite is configured as a proxy, web communication no longer consists of one simple browser-to-server connection.

- Instead, Burp becomes an active intermediary:

  ```text
  Browser

  ↓

  Burp Suite

  ↓
  
  Target Infrastructure

  ↓

  Application
  ```

- A more useful model is:

  ```text
  Browser ↔ Burp

  and

  Burp ↔ Target
  ```

- These are separate communication relationships.

- Understanding exactly how traffic moves through them explains:

  * Why browsers need proxy configuration.
  * Why Burp needs a proxy listener.
  * Why HTTPS interception requires trust in Burp's CA certificate.
  * How Burp determines the destination server.
  * Where DNS resolution occurs.
  * Why requests may be transformed before being forwarded.
  * Why connections can be reused independently.
  * Why browser behavior and upstream behavior are not always identical.

---

## Learning Objectives

- By the end of this lesson, you should be able to:

  * Trace a request from browser to Burp to the target.
  * Explain how a browser sends HTTP traffic to an explicit proxy.
  * Understand absolute-form and origin-form request targets.
  * Explain the purpose of the HTTP `CONNECT` method.
  * Understand how Burp determines the destination host and port.
  * Explain where DNS resolution may occur.
  * Distinguish client-side and server-side connections.
  * Understand connection reuse.
  * Explain why Burp may normalize or reconstruct requests.
  * Understand why browser-side protocol behavior may differ from server-side protocol behavior.

---

## Start With Direct Communication

- Without Burp, suppose a browser visits:

  ```text
  http://example.com/account
  ```

- A simplified process is:

  ```text
  Browser

  ↓

  Resolve example.com

  ↓

  Connect to Server
  
  ↓

  Send HTTP Request

  ↓

  Receive HTTP Response
  ```  

- For HTTPS:

  ```text
  Browser

  ↓

  Resolve example.com

  ↓
  
  Connect to Server
  
  ↓

  Establish TLS
  
  ↓
  
  Exchange HTTP
  
  ↓

  Receive Response
  ```  

- The browser communicates toward the destination directly.

---

## Communication Through Burp

- With an explicit proxy configured:

  ```text
  Browser
  
  ↓

  Burp Proxy Listener

  ↓

  Target Server
  ```  

- The browser's immediate network destination becomes the proxy.

- For example:

  ```text
  Proxy Host:
  127.0.0.1

  Proxy Port:
  8080
  ```  

- Conceptually:

  ```text
  Browser

  ↓

  127.0.0.1:8080
  
  ↓
  
  Burp
  ```  

- Burp then determines where the request should go next.

---

## Two Different Destinations

- This distinction is important.

- Suppose the user visits:

  ```text
  https://example.com/account
  ```

- The browser's configured proxy destination may be:

  ```text
  127.0.0.1:8080
  ```

- while the intended web destination is:

  ```text
  example.com:443
  ```

- Conceptually:

  ```text
  Browser

  ↓

  Burp
  127.0.0.1:8080

  ↓

  Target
  example.com:443
  ```

- These addresses serve different purposes.

---

## Proxy Listener Destination

- The browser needs to know:

  ```text
  Where should I send proxied traffic?
  ```

- That may be:

  ```text
  127.0.0.1:8080
  ```

- Burp's listener receives that traffic.

---

## Application Destination

- Burp needs to know:

  ```text
  Where does this HTTP request ultimately belong?
  ```

- That may be:

  ```text
  example.com:443
  ```

- Burp uses request and connection information to route traffic appropriately.

---

## HTTP Through an Explicit Proxy

- HTTP requests sent to an explicit proxy can use a different request-target form from requests sent directly to an origin server.

- A direct request may look like:

  ```http
  GET /account HTTP/1.1
  Host: example.com
  ```

- The request target is:

  ```text
  /account
  ```

- This is commonly called **origin-form**.

---

## Absolute-Form Requests

- When communicating with an HTTP proxy, a client may send:

  ```http
  GET http://example.com/account HTTP/1.1
  Host: example.com
  ```

- Notice:

  ```text
  http://example.com/account
  ```

- rather than only:

  ```text
  /account
  ```

- This is **absolute-form**.

- It helps the proxy identify the intended destination.

---

## Origin-Form vs Absolute-Form

- Origin-form:

  ```http
  GET /account HTTP/1.1
  Host: example.com
  ```

- Absolute-form:

  ```http
  GET http://example.com/account HTTP/1.1
  Host: example.com
  ```

- Conceptually:

  ```text
  Direct Origin Communication

  ↓

  Origin-Form Often Used
  ```

  ```text
  Explicit HTTP Proxy Communication

  ↓

  Absolute-Form May Be Used
  ```

- Burp handles the necessary proxy semantics and presents requests in a convenient testing interface.

---

## Burp May Reconstruct the Upstream Request

- The exact bytes sent:

  ```text
  Browser → Burp
  ```

do not necessarily have to be identical to the bytes sent:

  ```text
  Burp → Server
  ```

- For example:

  ```text
  Browser → Burp

  GET http://example.com/account HTTP/1.1
  Host: example.com
  ```

- Burp may communicate upstream using the appropriate origin-facing representation:

  ```text
  Burp → Server

  GET /account HTTP/1.1
  Host: example.com
  ```

- The logical HTTP request remains equivalent.

- The representation used on each connection may differ.

---

## Important Mental Model

- Do not think:

  ```text
  Browser Bytes

  ↓

  Copied Unchanged

  ↓

  Server
  ```  

- Instead think:

  ```text
  Browser Message

  ↓

  Burp Parses It

  ↓

  Burp Represents / Processes It
  
  ↓
  
  Burp Sends Appropriate Upstream Message
  ```

- This distinction becomes increasingly important with HTTPS, HTTP/2, protocol conversion, and request normalization.

---

## HTTPS Creates a Special Problem

- Suppose the browser visits:

  ```text
  https://example.com
  ```

- Without a proxy:

  ```text
  Browser

  ↓

  TLS
  
  ↓

  example.com
  ```

- The browser expects to establish a secure TLS relationship associated with `example.com`.

- But when Burp is configured as an explicit proxy, the browser first communicates with Burp.

- How does it request an HTTPS tunnel?

- One important mechanism is:

  ```text
  CONNECT
  ```

---

## The CONNECT Method

- A browser may send the proxy a request conceptually like:

  ```http
  CONNECT example.com:443 HTTP/1.1
  Host: example.com:443
  ```

- This tells the proxy:

  ```text
  Establish communication toward:

  example.com

  Port:

  443
  ```

- The target here is an authority rather than a normal resource path.

---

## Simplified CONNECT Flow

```text
Browser

↓

CONNECT example.com:443

↓

Burp

↓

Connection Toward example.com:443
```

- After the proxy accepts the request, communication for the HTTPS destination can proceed through the proxy relationship.

---

## CONNECT Is Not a Normal Page Request

- Compare:

  ```http
  GET /account HTTP/1.1
  Host: example.com
  ```

- with:

  ```http
  CONNECT example.com:443 HTTP/1.1
  Host: example.com:443
  ```

- `GET` requests a resource.

- `CONNECT` asks the proxy to establish a tunnel-style connection toward a destination authority.

- This distinction matters when understanding HTTPS proxying.

---

## A Generic CONNECT Proxy

- A basic proxy can create a tunnel:

  ```text
  Browser

  ↓

  Encrypted TLS Traffic

  ↓

  Proxy Tunnel

  ↓

  Server  
  ```

- If the proxy merely tunnels encrypted bytes, it cannot automatically inspect the decrypted HTTP messages inside that TLS connection.

- Burp needs a different interception model to inspect HTTPS plaintext.

---

## Burp HTTPS Interception

- For HTTPS inspection, Burp effectively establishes separate TLS relationships:

  ```text
  Browser

  ↓

  TLS Connection 1
  
  ↓

  Burp

  ↓
      
  TLS Connection 2

  ↓

  Target Server
  ```

- This allows Burp to access the HTTP messages between the TLS layers.

- Conceptually:

  ```text
  Browser
     │
     │ Encrypted
     ▼
    Burp
     │
     │ HTTP visible inside Burp
     │
     │ Re-encrypted
     ▼
  Server
  ```

- The detailed certificate and TLS process is covered in Lesson 04.

---

## Browser-Side TLS

- The browser establishes a TLS relationship with Burp for the intercepted destination.

- For the browser to accept this without certificate warnings, it must trust Burp's CA certificate used for interception.

- Conceptually:

  ```text
  Browser

  ↓

  Trusts Burp CA

  ↓

  Accepts Burp-Generated Certificate
  for example.com
  ```

---

## Server-Side TLS

- Separately, Burp establishes TLS with the real destination:

  ```text
  Burp

  ↓
  
  TLS

  ↓

  example.com
  ```  
  
- Burp therefore occupies a position where it can:

  ```text
  Decrypt Browser-Side Traffic

  ↓

  Inspect / Modify HTTP

  ↓

  Encrypt Traffic Toward Server
  ```

- This is the technical foundation of HTTPS interception.

---

## DNS Resolution

- Before a connection can be established to a hostname such as:

  ```text
  example.com
  ```

- the hostname usually needs to be resolved to an IP address somewhere in the connection process.

- With a proxy, an important question is:

  > Which component performs the relevant DNS resolution?

- The answer can depend on:

  * Proxy type.
  * Request type.
  * Browser configuration.
  * Burp configuration.
  * Upstream proxying.
  * SOCKS behavior.
  * DNS overrides.

---

## Direct Communication Example

- Without Burp:

  ```text
  Browser

  ↓
  
  Resolve example.com

  ↓

  93.x.x.x

  ↓

  Connect
  ```  

- The client environment commonly performs destination resolution as part of direct communication.

---

## Explicit Proxy Example

- With an explicit HTTP proxy:

  ```text
  Browser

  ↓

  Connect to Burp Listener

  ↓

  Tell Burp Intended Destination
  
  ↓
  
  Burp Resolves / Routes Destination
  
  ↓

  Burp Connects Upstream
  ```

- The browser does not necessarily need to create the direct destination connection itself.

- Burp may perform the relevant upstream destination resolution.

---

## Why DNS Location Matters

- Suppose:

  ```text
  Browser DNS

  and

  Burp DNS
  ```

produce different answers.

- Potential reasons include:

  ```text
  VPN

  Corporate DNS

  Hosts File

  Split-Horizon DNS

  DNS Override

  Different Network Namespace
  
  Proxy Configuration
  ```

- Then behavior may differ depending on which component resolves the hostname.

---

## Troubleshooting Example

- Suppose the browser works normally without Burp:

  ```text
  Browser

  ↓
  
  Target Loads
  ```

- But through Burp:

  ```text
  Browser

  ↓

  Burp

  ↓

  DNS / Connection Failure
  ```

- The problem may not be the browser.

- It may involve:

  ```text
  Burp-Side DNS Resolution

  Upstream Connectivity

  Proxy Configuration

  TLS Negotiation
  ```

- Understanding the communication path helps isolate the failure.

---

## DNS Overrides

- Burp can be configured in ways that affect how particular hostnames are routed or resolved.

- Conceptually:

  ```text
  example.test

  ↓

  Specific IP Address
  ```  

- This can be useful in controlled testing environments where:

  * DNS is unavailable.
  * A staging host needs direct mapping.
  * A virtual host must be tested against a specific server.

- Always ensure routing changes remain within authorized scope.

---

## Hostname vs IP Address

- The hostname may remain security-relevant even after DNS resolution.

- For example:

  ```text
  Host: example.com
  ```

- or HTTP/2:

  ```text
  :authority: example.com
  ```

may determine which virtual application the server serves.

- Therefore:

  ```text
  Destination IP

  ≠

  Complete Application Identity
  ```

- Multiple hostnames may share one IP address.

---

## Virtual Hosting

- One server IP might host:

  ```text
  shop.example.com

  api.example.com

  admin.example.com
  ```  

- The HTTP host/authority information helps the infrastructure determine which application should receive the request.

- Conceptually:

  ```text
  Same IP

  ↓

  Host / Authority

  ↓

  Different Virtual Application
  ```

---

## Connection Establishment

- A simplified HTTP communication path through Burp:

  ```text
  1. Browser connects to Burp listener

  2. Browser sends proxy-aware request

  3. Burp identifies destination

  4. Burp resolves/routes destination

  5. Burp creates or reuses upstream connection

  6. Burp sends request

  7. Server responds
  
  8. Burp receives response

  9. Burp forwards response to browser
  ```  
  
- For HTTPS, TLS relationships are also involved.

---

## Connection Reuse

- Creating a new network connection for every single request would be inefficient.

- Modern HTTP commonly reuses connections.

- For example:

  ```text
  Burp ↔ Server Connection

  ├── Request 1
  ├── Response 1
  ├── Request 2
  ├── Response 2
  └── Request 3
  ```

- The exact behavior depends on protocol and server support.

---

## Browser-Side and Server-Side Reuse Are Independent

- Because the two communication legs are separate:

  ```text
  Browser ↔ Burp

  Burp ↔ Server
  ```  

their connection lifecycles do not have to match one-to-one.

- Do not assume:

  ```text
  One Browser Connection

  =

  Exactly One Server Connection
  ```

- Burp may manage upstream connections independently.

---

## Example

- Conceptually:

  ```text
  Browser Connection A

  ↓

  Burp

  ├── Reuse Server Connection X
  ├── Open Server Connection Y
  └── Close Server Connection X
  ```

- Actual connection management depends on:

  * HTTP version.
  * Destination.
  * TLS state.
  * Connection headers.
  * Server behavior.
  * Burp configuration.

- The key concept is independence.

---

## HTTP/1.1 Keep-Alive

- HTTP/1.1 commonly uses persistent connections.

- Conceptually:

  ```text
  TCP Connection

  ↓

  GET /page

  ↓

  200 OK

  ↓

  GET /image

  ↓

  200 OK
  ```  
  
rather than opening a new TCP connection for every request.

---

## HTTP/2 Multiplexing

- HTTP/2 can carry multiple streams over one connection.

- Conceptually:

  ```text
  One Connection

  ├── Stream 1
  ├── Stream 3
  ├── Stream 5
  └── Stream 7
  ```

- This means connection behavior differs substantially from simple HTTP/1.1 mental models.

---

## Protocol Versions Can Differ Across Burp

- Because Burp terminates one communication leg and creates another, protocol versions may differ.

- Conceptually:

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

- or:

  ```text
  Browser

  ↓

  HTTP/1.1
  
  ↓

  Burp

  ↓

  HTTP/2

  ↓

  Server
  ```  

depending on support and configuration.

---

## Why This Matters

- Never automatically assume:

  ```text
  Protocol Browser Uses

  =

  Protocol Server Receives
  ```  

- Burp may negotiate and represent the upstream request independently.

- This matters during advanced testing involving:

  * HTTP/2 behavior.
  * Protocol downgrading.
  * Request smuggling.
  * Request desynchronization.
  * Connection-specific behavior.

---

## Request Normalization

- A proxy may parse and reconstruct requests.

- This can affect representation such as:

  ```text
  Request Target Form

  Header Formatting
  
  Protocol-Specific Fields

  Connection-Specific Headers

  HTTP Version Representation
  ```

- Therefore:

  ```text
  What Browser Sent to Burp

  ≠

  Necessarily Byte-for-Byte What Burp Sent Upstream
  ```

- The logical semantics may remain equivalent while the wire representation changes.

---

## Hop-by-Hop Concepts

- Some HTTP fields relate to a specific connection rather than the entire end-to-end request path.

- These are often described as **hop-by-hop** concerns.

- Conceptually:

  ```text
  Browser ↔ Burp

  is one hop relationship
  ```

  ```text
  Burp ↔ Server

  is another hop relationship
  ```

- Certain connection-specific information may therefore be handled differently across the proxy.

---

## End-to-End vs Hop-by-Hop Mental Model

- End-to-end information is intended to remain meaningful across the request path.

- Connection-specific information may apply only to one communication leg.

- Conceptually:

  ```text
  Browser

  [Connection A]

  Burp

  [Connection B]

  Server
  ```

- Not every connection-level property must remain identical across both.

---

## Destination Determination

- When Burp receives a request, it needs enough information to determine:

  ```text
  Scheme

  Hostname

  Port

  Path
  ```  

- Depending on the request/protocol, this information may come from concepts such as:

  ```text
  Absolute URL

  Host Header

  CONNECT Target
  
  HTTP/2 :scheme

  HTTP/2 :authority

  HTTP/2 :path
  ```  

- Burp uses protocol context to route the request correctly.

---

## Example — HTTP Request

- Browser wants:

  ```text
  http://example.com/products?id=42
  ```

- Proxy-aware request may conceptually contain:

  ```http
  GET http://example.com/products?id=42 HTTP/1.1
  Host: example.com
  ```

- Burp can determine:

  ```text
  Scheme:
  http

  Host:
  example.com

  Default Port:
  80

  Path:
  /products?id=42
  ```

---

## Example — HTTPS Request

- Browser wants:

  ```text
  https://example.com/account
  ```

- Simplified proxy process:

  ```text
  Browser

  ↓

  CONNECT example.com:443

  ↓

  Burp establishes interception context

  ↓

  TLS between Browser and Burp

  ↓

  HTTP request becomes visible to Burp

  ↓

  Burp communicates securely with example.com

  ↓

  Server response returns through Burp
  ```  

- The real process contains additional TLS details, but this model is sufficient for now.

---

## Example — HTTP/2 Request

- Burp may work with logical fields such as:

  ```text
  :method: GET
  :scheme: https
  :authority: example.com
  :path: /account
  ```

- These provide the same broad destination concepts:

  ```text
  Method

  Scheme

  Authority

  Path
  ```  

even though HTTP/2 wire representation differs from HTTP/1.1.

---

## Responses Follow the Reverse Path

- Suppose the server returns:

  ```http
  HTTP/1.1 302 Found
  Location: /login
  Set-Cookie: session=abc123
  ```

- The flow is:

  ```text
  Server

  ↓
  
  Burp Receives Response

  ↓

  Burp Records / Processes Response

  ↓

  Browser Receives Response

  ↓

  Browser Handles Redirect and Cookie
  ```

- The browser may then generate another request.

---

## One User Action Can Produce Many Requests

- Suppose you click:

  ```text
  Dashboard
  ```

- The browser may generate:

  ```text
  GET /dashboard

  GET /styles.css

  GET /app.js

  GET /api/profile

  GET /api/notifications

  GET /logo.svg
  ```

- Each may independently travel:

  ```text
  Browser

  ↓

  Burp

  ↓

  Relevant Server  
  ```

- This explains why HTTP history can fill quickly.

---

## Requests May Go to Different Hosts

- A single webpage may contact:

  ```text
  app.example.com

  api.example.com

  cdn.example.net

  analytics.example.org
  ```  

- Burp can proxy all appropriately configured traffic, but each destination may require separate upstream connections and DNS resolution.

- Conceptually:

  ```text
  Browser

  ↓

  Burp

  ├── app.example.com
  ├── api.example.com
  ├── CDN
  └── Third-Party Service
  ```

- This is one reason scope control matters.

---

## Third-Party Traffic

- A target webpage may generate traffic to systems that are not authorized for testing.

- Examples:

  ```text
  Analytics Providers

  Payment Processors

  CDNs

  Authentication Providers

  Advertising Services
  ```

- Seeing traffic in Burp does not mean it is in scope.

- Remember:

  ```text
  Visible in Burp

  ≠

  Authorized to Test
  ```

---

## Upstream Proxies

- Burp may itself be configured to use another proxy.

- Conceptually:

  ```text
  Browser

  ↓

  Burp

  ↓

  Corporate / Upstream Proxy
  
  ↓
    
  Target  
  ```

- Now the communication path contains another intermediary.

- This may affect:

  * Routing.
  * Authentication.
  * DNS.
  * TLS.
  * Request headers.
  * Connectivity.

---

## SOCKS Proxying

- In some environments, Burp traffic may be routed through a SOCKS proxy.

- Conceptually:

  ```text
  Browser

  ↓

  Burp

  ↓

  SOCKS Proxy

  ↓

  Destination
  ```

- This can change routing and potentially where DNS resolution occurs depending on configuration.

- The important lesson is:

  ```text
  Always Know the Actual Traffic Path
  ```

---

## Burp's Embedded Browser

- Burp also provides an embedded browser designed to simplify proxying through Burp.

- Conceptually:

  ```text
  Burp Browser

  ↓

  Preconfigured Burp Proxying

  ↓

  Target
  ```

- This reduces some manual browser proxy and certificate setup.

- However, the underlying architecture remains:

  ```text
  Browser-Side Communication

  ↓

  Burp

  ↓

  Server-Side Communication
  ```  

---

## External Browser

- With an external browser:

  ```text
  Firefox / Chrome

  ↓
  
  Manual or Extension-Based Proxy Configuration
  
  ↓

  Burp Listener
  ```

- You must ensure:

  * Correct proxy host.
  * Correct proxy port.
  * Burp listener is active.
  * HTTPS trust is configured appropriately.  
  * Proxy bypass rules do not unexpectedly exclude the target.

---

## Proxy Bypass

- Browsers or operating systems may bypass proxies for certain destinations.

- Examples might include:

  ```text
  localhost

  127.0.0.1

  Private Networks
  
  Configured Exceptions
  ```

- If traffic bypasses Burp:

  ```text  
  Browser

  ────────────► Target
  ```

- instead of:

  ```text
  Browser

  ↓

  Burp

  ↓

  Target
  ```

then it will not appear in Burp's expected proxy history.

---

## Localhost Testing

- This becomes especially relevant when testing:

  ```text
  http://localhost:3000
  ```

- or:

  ```text
  http://127.0.0.1
  ```

- Some browser configurations may bypass proxies for local destinations.

- If a local lab does not appear in Burp, check:

  ```text
  Proxy Bypass Rules

  Browser Settings

  Burp Listener

  Target URL
  ```

before assuming Burp is malfunctioning.

---

## Interception vs Routing

- These are separate concepts.

### Routing

```text
Does the traffic pass through Burp?
```

### Interception

```text
Does Burp pause the message for manual action?
```

- Therefore:

  ```text
  Traffic Routed Through Burp

  +

  Intercept OFF

  =

  Traffic Still Visible in History    
  but Automatically Forwarded
  ```

---

## Communication Failure Model

- When a page fails through Burp, isolate the path.

  ```text
  Browser

  ↓

  Can it reach Burp?
  ```  

- Then:

  ```text
  Burp
  
  ↓

  Can it resolve the target?  
  ```

- Then:

  ```text
  Burp

  ↓

  Can it connect to the target?
  ```

- Then:

  ```text
  TLS

  ↓
  
  Can secure negotiation succeed?
  ```

- Then:

  ```text
  HTTP

  ↓

  Does the application respond correctly?
  ```

- This layered reasoning is more effective than randomly changing settings.

---

## Troubleshooting Decision Flow

```text
No Request in Burp?

↓

Check Browser Proxy Configuration

↓

Check Proxy Listener

↓

Check Proxy Bypass Rules
```

- If the request appears in Burp but fails upstream:

  ```text
  Request Reaches Burp

  ↓

  Check DNS

  ↓

  Check Upstream Connectivity

  ↓
  
  Check TLS

  ↓

  Check Proxy / SOCKS Rules

  ↓

  Check Target Behavior
  ```  

---

## Common Mistakes

- Avoid:

```text
The browser connects directly to the target
and Burp somehow watches the connection.
```

- Incorrect when using Burp as an explicit intercepting proxy.

---

```text
The browser proxy port and target port are the same thing.
```

- Incorrect.

- They belong to different communication legs.

---

```text
The browser must always resolve the target hostname itself.
```

- Incorrect.

- Proxy architecture can change where relevant destination resolution occurs.

---

```text
Burp forwards every byte exactly as the browser sent it.
```

- Incorrect as a general assumption.

- Burp parses, represents, and may reconstruct protocol messages.

---

```text
One browser connection always maps to one server connection.
```

- Incorrect.

- Connection management is independent across proxy legs.

---

```text
The browser and server must use the same HTTP version.
```

- Incorrect.

- Burp may negotiate protocols independently.

---

```text
If traffic is in HTTP history, it is authorized to test.
```

- Incorrect.

- Scope and authorization are separate from visibility.

---

```text
Intercept OFF means the browser connects directly to the server.
```

- Incorrect.

- Traffic may still flow through Burp automatically.

---

## Professional Investigation Model

- Whenever you troubleshoot or analyze Burp communication, map:

  ```text
  Client

  ↓

  Proxy Configuration

  ↓

  Burp Listener

  ↓

  Interception Rules

  ↓

  Destination Determination

  ↓

  DNS / Routing

  ↓

  Upstream Connection

  ↓

  TLS

  ↓
  
  HTTP Protocol

  ↓

  Target Infrastructure
  
  ↓
  
  Application
  ```

- Then trace the response in reverse.

---

## Communication Checklist

```text
[ ] I know which browser/client generated the request.

[ ] I know how that client is configured to reach Burp.

[ ] I know the Burp listener address and port.

[ ] I distinguish routing from interception.

[ ] I understand the intended destination hostname and port.

[ ] I understand absolute-form vs origin-form conceptually.

[ ] I understand the purpose of CONNECT for HTTPS proxying.

[ ] I know that HTTPS interception creates separate TLS relationships.

[ ] I understand that DNS resolution may occur at different points.

[ ] I know browser-side and server-side connections are independent.

[ ] I do not assume one client connection equals one upstream connection.

[ ] I do not assume the same HTTP version exists on both sides.

[ ] I understand Burp may reconstruct or normalize requests.

[ ] I check proxy bypass rules when traffic is missing.

[ ] I distinguish visible traffic from authorized scope.
```

---

## Key Takeaways

* A browser configured to use Burp sends proxy traffic to Burp's listener rather than creating the same direct application-layer relationship with the destination.
* The proxy listener address and target application address belong to different communication legs.
* HTTP proxy requests may use absolute-form request targets, while origin servers commonly receive origin-form requests.
* HTTPS proxying commonly begins with the `CONNECT` method to identify a destination authority.
* Burp HTTPS interception uses separate browser-side and server-side TLS relationships.
* DNS resolution responsibilities can change depending on proxy and routing configuration.
* Browser-side and server-side connections are managed independently.
* Connection reuse means there is not necessarily a one-to-one mapping between browser and server connections.
* HTTP protocol versions can differ across the two sides of Burp.
* Burp may parse, normalize, or reconstruct requests rather than forwarding every byte unchanged.
* A single webpage can generate requests to many different hosts.
* Traffic visible in Burp may include third-party systems outside authorized scope.
* Routing traffic through Burp and manually intercepting traffic are separate concepts.
* Understanding the complete communication path is essential for troubleshooting Burp correctly.
