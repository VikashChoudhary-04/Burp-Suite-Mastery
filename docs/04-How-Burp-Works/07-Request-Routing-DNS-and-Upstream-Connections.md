# Request Routing, DNS, and Upstream Connections

> **Module 04 — How Burp Suite Works**

> **Progress**

```text
███████░░░░ 7 / 11 Lessons
```

- When a browser sends a request through Burp Suite, Burp must determine where that request should go and how to reach the destination.

- This involves several related but distinct concepts:

  ```text
  Scheme

  Hostname

  Port

  DNS Resolution

  Network Route

  TLS / SNI

  HTTP Host or :authority

  Upstream Proxy Configuration
  ```  

- A request may look simple:

  ```http
  GET /account HTTP/1.1
  Host: example.com
  ```

- but Burp may need to perform a sequence such as:

  ```text
  Determine Destination

  ↓

  Resolve Hostname

  ↓

  Choose Network Route

  ↓

  Open or Reuse Connection

  ↓

  Negotiate TLS if Required

  ↓
  
  Send HTTP Request
  
  ↓

  Receive Response
  ```  

- Understanding this process is essential for troubleshooting situations where:

  ```text
  The website works directly

  but
  
  fails through Burp
  ```

- or where requests reach the wrong virtual host, fail DNS resolution, encounter TLS errors, or behave differently through a VPN or upstream proxy.

---

## Learning Objectives

- By the end of this lesson, you should be able to:

  * Explain how Burp determines a request's destination.
  * Distinguish scheme, hostname, IP address, and port.
  * Understand DNS resolution conceptually.
  * Explain the relationship between DNS and network routing.
  * Distinguish HTTP `Host`, HTTP/2 `:authority`, and TLS SNI.
  * Understand connection establishment and reuse.
  * Explain upstream proxy routing.
  * Understand SOCKS proxy routing conceptually.
  * Recognize how VPNs affect Burp connectivity.
  * Understand hostname resolution overrides.
  * Distinguish redirects from network routing.
  * Understand multi-host application architectures.
  * Diagnose common upstream connection failures systematically.
  * Explain why direct browser access may work while proxied access fails.

---

## The Complete Routing Mental Model

- A useful simplified model is:

  ```text
  Browser

  ↓

  Burp Listener

  ↓

  Determine Destination
  
  ↓

  Resolve / Route

  ↓

  Connect Upstream

  ↓

  TLS if HTTPS

  ↓

  Send HTTP

  ↓
  
  Target Infrastructure
  ```

- For the response:

  ```text
  Target Infrastructure

  ↓

  Burp

  ↓

  Browser
  ```  

- Each stage can fail independently.

---

## Step 1 — Determine the Destination

- Before Burp can send a request, it must know:

  ```text
  Scheme

  Hostname

  Port
  ```  

- Example:

  ```text
  https://example.com/account
  ```

- can be understood as:

  ```text
  Scheme:

  https
  ```

  ```text
  Hostname:

  example.com
  ```

  ```text
  Default Port:

  443
  ```

  ```text
  Path:

  /account  
  ```

- These components serve different purposes.

---

## Scheme

- The scheme indicates the communication type.

- Common examples:

  ```text
  http
  ```

- and:

  ```text
  https
  ```

- Typical defaults:

  | Scheme | Default Port |
  | ------ | -----------: |
  | HTTP   |           80 |
  | HTTPS  |          443 |

- However, applications can use non-default ports.

- Example:

  ```text
  https://example.com:8443
  ```

- means:

  ```text
  Scheme: HTTPS

  Hostname: example.com

  Port: 8443
  ```

- Do not assume HTTPS always means port 443.

---

## Hostname vs IP Address

- A hostname:

  ```text
  example.com
  ```

is a human-readable identifier.

- A network connection ultimately needs an address that can be routed, commonly an IP address.

- Conceptually:

  ```text
  example.com

  ↓

  DNS Resolution

  ↓

  IP Address
  ```  

- Example:

  ```text
  example.com

  ↓

  203.0.113.10
  ```  

- The IP shown here is only illustrative.

---

## DNS Resolution

- DNS translates names into network addressing information.

- Simplified:

  ```text
  Burp needs to reach:

  api.example.com

  ↓

  DNS Query

  ↓

  IP Address Returned

  ↓

  Burp Connects to IP
  ```  

- DNS is therefore part of destination discovery.

---

## DNS Is Not the Same as Routing

- DNS answers:

  ```text
  What address corresponds to this hostname?
  ```

- Routing answers:

  ```text
  How should network traffic reach that address?
  ```

- Conceptually:

  ```text
  Hostname

  ↓

  DNS
  
  ↓

  IP Address

  ↓

  Routing

  ↓

  Network Path
  ```

- A hostname may resolve successfully while the network route still fails.

---

## Example — DNS Works but Connection Fails

- Suppose:

  ```text
  internal.example.com

  ↓
  
  10.20.30.40
  ```

- DNS succeeded.

- But Burp receives:

  ```text
  Connection timed out
  ```

- Possible explanation:

  ```text
  DNS Resolution

  ✓

  Network Reachability

  ✗
  ```

- Perhaps the IP is reachable only through:

  ```text
  VPN

  Corporate Network

  Specific Route

  Jump Environment
  ```

- Do not diagnose every connection failure as DNS.

---

## Example — DNS Failure

- Burp attempts to reach:

  ```text
  api.internal.example
  ```

- but cannot resolve it.

- Possible causes:

  ```text  
  Hostname Typo

  Internal DNS Required

  VPN DNS Not Available

  Resolver Configuration Problem

  Split DNS

  Temporary DNS Failure
  ```

- In this case, Burp may never reach the TCP/TLS connection stage.

---

## Destination Port

- After determining the address, Burp must connect to the correct port.

- Examples:

  ```text
  HTTP

  example.com:80
  ```

  ```text
  HTTPS

  example.com:443
  ```

  ```text
  Custom Application

  example.com:8443
  ```

- A correct hostname with the wrong port can still fail completely.

---

## Connection Establishment

- Conceptually:

  ```text
  Burp

  ↓

  Resolve Destination

  ↓

  Connect to IP:Port

  ↓

  Connection Established

  ↓

  Send Protocol Traffic
  ```

- For HTTPS:

  ```text
  Burp

  ↓

  Connect to IP:Port
  
  ↓

  TLS Negotiation

  ↓

  HTTP Over TLS
  ```  

---

## HTTP Destination Identity

- In HTTP/1.1, the `Host` header identifies the intended host.

- Example:

  ```http
  GET /account HTTP/1.1
  Host: example.com
  ```

- This is especially important when multiple websites share infrastructure.

---

## Virtual Hosting

- One server IP may host many applications:

  ```text
  203.0.113.10

  ├── app.example.com
  ├── api.example.com
  └── admin.example.com
  ```

- The server may use the requested hostname to decide which application should handle the request.

- Conceptually:

  ```text
  Same IP

  +

  Different Hostname

  ↓

  Different Application
  ```

---

## Host Header Matters

- Suppose Burp connects to:

  ```text
  203.0.113.10
  ```

- and sends:

  ```http
  GET / HTTP/1.1
  Host: app.example.com
  ```

- The infrastructure may route the request to:

  ```text
  Application A
  ```

- If instead:

  ```http
  Host: api.example.com
  ```

- it may route to:

  ```text
  Application B
  ```

- The IP address alone does not always identify the final application.

---

## HTTP/2 :authority

- HTTP/2 represents authority information differently.

- Conceptually:

  ```text
  :authority: example.com
  ```

- serves a role related to the HTTP/1.1:

  ```text
  Host: example.com
  ```

- Burp normally presents protocol details in a way suitable for editing and analysis.

- The key principle is:

  ```text
  HTTP Request Host Identity

  must remain conceptually distinct from

  Network Destination IP
  ```  

---

## TLS SNI

- For HTTPS, hostname information may be needed before HTTP is sent.

- TLS can use:

  ```text
  Server Name Indication
  ```

- or:

  ```text
  SNI
  ```

- Conceptually:

  ```text
  Burp

  ↓

  Connects to Shared IP

  ↓

  TLS says intended hostname:
  
  example.com

  ↓

  Server chooses certificate / virtual service
  ```  

---

## SNI vs Host Header

- These operate at different stages.

  ```text
  TLS Layer

  ↓

  SNI
  ```  

- then:

  ```text
  HTTP Layer

  ↓

  Host / :authority
  ```

- A useful model:

  ```text
  SNI

  helps identify the intended TLS virtual host
  ```

- while:

  ```text
  Host / :authority

  identifies the intended HTTP authority
  ```

- They often contain related hostname values, but they are not the same protocol field.

---

## Why Mismatches Matter

- Imagine:

  ```text
  Network IP:

  203.0.113.10
  ```  

  ```text
  SNI:

  app.example.com
  ```  

  ```text
  Host:

  api.example.com
  ```

- This unusual combination may cause:

  ```text
  Wrong Certificate

  Wrong Virtual Host
  
  Routing Differences

  Application Rejection

  Unexpected Response
  ```

depending on infrastructure.

- Understanding each layer prevents confusion.

---

## Three Destination Identities

- A useful mental model is:

  ```text
  Network Destination

  IP + Port
  ```

  ```text
  TLS Identity
  
  SNI / Certificate Hostname
  ```  

  ```text
  HTTP Identity

  Host / :authority
  ```

- These frequently align:

  ```text
  example.com

  ↓

  DNS → 203.0.113.10

  ↓

  TLS SNI → example.com

  ↓

  HTTP Host → example.com
  ```

- but advanced testing and unusual infrastructure can make them differ.

---

## Connection Reuse

- Burp does not necessarily create a completely new network connection for every HTTP request.

- Conceptually:

  ```text
  Request 1

  ↓

  Existing Connection

  ↓

  Server
  ```

- then:

  ```text
  Request 2

  ↓

  Reuse Connection

  ↓

  Server
  ```

- This can improve efficiency.

---

## HTTP/1.1 Keep-Alive

- HTTP/1.1 commonly allows multiple requests over persistent connections.

- Simplified:

  ```text
  Burp

  ================ Connection ================

  Request 1 →

  ← Response 1
  
  Request 2 →
  
  ← Response 2
  ```

- This reduces repeated connection setup.

---

## HTTP/2 Multiplexing

- HTTP/2 can carry multiple logical streams over one connection.

  ```text
  Single Connection

  ├── Request Stream A
  ├── Request Stream B
  └── Request Stream C
  ```

- This means:

  ```text
  One Upstream Connection

  ≠

  Only One HTTP Request
  ```  

- Connection behavior can therefore be more complex than a simple request-per-connection model.

---

## Why Connection Reuse Matters

- Connection reuse may affect:

  ```text
  Performance

  Authentication Behavior

  Connection-Bound State

  Troubleshooting

  Race Conditions

  Protocol Testing
  ```

- Most routine testing does not require manually managing these details, but understanding them helps explain unexpected behavior.

---

## Burp as a Separate Upstream Client

- Remember:

  ```text
  Browser

  ↓
  
  Connection to Burp
  ```

- and:

  ```text
  Burp

  ↓

  Connection to Server
  ```

are separate.

- Therefore:

  ```text
  Browser's Network Connection

  ≠

  Burp's Upstream Network Connection
  ```  

- Burp becomes the network client from the server's perspective.

---

## Source Connection Differences

- Without Burp:

  ```text
  Browser

  ↓

  Target
  ```  

- With Burp:

  ```text
  Browser

  ↓

  Burp

  ↓
  
  Target
  ```  

- The target-side connection is created by Burp.

- This can affect behavior involving:

  ```text
  TLS Fingerprints

  Connection Reuse
  
  HTTP Version
  
  Proxy Routing

  Client Certificates

  Network Source
  ```

---

## Direct Browser Works, Burp Fails

- This is a common troubleshooting scenario.

  ```text
  Browser Direct

  ↓

  Website Works
  ```

- but:

  ```text
  Browser

  ↓

  Burp

  ↓

  Website Fails
  ```  

- This proves something important:

  ```text
  The application is reachable somehow

  but

  the Burp-mediated path differs.
  ```

- Now identify where.

---

## Compare the Two Paths

- Direct:

  ```text
  Browser

  ↓

  DNS

  ↓

  Network Route

  ↓

  TLS

  ↓

  Target
  ```

- Through Burp:

  ```text
  Browser

  ↓
  
  Burp Listener

  ↓

  Burp DNS / Routing

  ↓

  Burp Upstream Connection

  ↓

  TLS

  ↓

  Target
  ```  

- The second path introduces additional components.

---

## Diagnostic Questions

- Ask:

  ```text
  Does browser traffic reach Burp?

  ↓

  Can Burp resolve the hostname?
  
  ↓

  Can Burp reach the resolved IP and port?

  ↓

  Does Burp use the correct VPN/network route?

  ↓

  Does upstream TLS succeed?
  
  ↓

  Is the correct SNI used?

  ↓

  Is Host/:authority correct?
  
  ↓

  Is an upstream proxy required?
  
  ↓

  Is mTLS required?
  
  ↓

  Is another Burp rule changing traffic?
  ```

- Work layer by layer.

---

## VPN Routing

- Some targets are reachable only through a VPN.

- Example:

  ```text
  Target

  10.50.20.15
  ```

- Without VPN:

  ```text
  No Route
  ```

- With VPN:

  ```text
  VPN Interface

  ↓

  Private Network Route

  ↓

  10.50.20.15
  ```  

- Burp's upstream connections must use a network path capable of reaching the target.

---

## VPN + Burp Architecture

- Conceptually:

  ```text
  Browser

  ↓

  Burp
  
  ↓
  
  Operating System Routing

  ↓

  VPN Interface

  ↓
  
  Target
  ```

- If the operating system routes Burp's traffic through the VPN correctly, the target may be reachable.

---

## Why Browser Direct May Work but Burp Through VPN Fails

- Possible reasons:

  ```text
  Browser Uses Different Proxy

  Burp Uses Different DNS

  VPN Split Tunneling
  
  Upstream Proxy Required

  SOCKS Configuration

  Different Hostname Resolution
  
  IPv4 vs IPv6 Route Differences
  ```  

- Do not assume the VPN applies identically to every application.

---

## Split Tunneling

- A VPN may route only selected networks through the tunnel.

- Conceptually:

  ```text
  10.0.0.0/8

  ↓
  
  VPN
  ```

- while:

  ```text
  Public Internet

  ↓

  Normal Gateway
  ```

- This is split tunneling.

- Burp's destination determines which route the operating system selects.

---

## Internal DNS and VPNs

- Some VPNs provide DNS servers that resolve internal names.

- Example:

  ```text
  portal.corp.internal
  ```

may resolve only while connected to the VPN.

- Possible flow:

  ```text
  VPN Connected

  ↓

  Internal DNS Available

  ↓

  Hostname Resolves

  ↓

  Private IP Returned

  ↓
  
  VPN Route Reaches Target
  ```
  
- If Burp uses a different resolution path, the name may fail.

---

## Upstream Proxies

- Sometimes Burp itself must send traffic through another proxy.

- Architecture:

  ```text
  Browser

  ↓

  Burp

  ↓

  Upstream Proxy
  
  ↓

  Target
  ```  

- This may be required in:

  ```text
  Corporate Networks

  Controlled Labs

  Special Routing Environments
  
  Authenticated Proxy Environments
  ```

---

## Why Use an Upstream Proxy?

- Possible reasons:

  ```text
  Network Policy

  Internet Access Requires Proxy
  
  Traffic Must Exit Through Specific Gateway

  Testing Architecture Requires Chained Proxy

  Special Routing Requirement
  ```  

- Burp can act as one proxy while forwarding selected traffic through another.

---

## Proxy Chaining

- Conceptually:

  ```text
  Browser

  ↓

  Burp

  ↓

  Corporate Proxy

  ↓

  Internet

  ↓

  Target
  ```  

- Each layer has its own responsibility.

- A failure at the corporate proxy may appear to the browser as a failure through Burp.

---

## Upstream Proxy Authentication

- An upstream proxy may require authentication.

- Conceptually:

  ```text
  Burp

  ↓
  
  Upstream Proxy

  ↓

  Authentication Required

  ↓
  
  Target
  ```

- If credentials or authentication configuration are missing, Burp may be unable to reach external destinations even though direct browser access works through another configured path.

---

## SOCKS Proxy

- SOCKS provides a more general proxying mechanism than an HTTP-specific upstream proxy.

- Conceptually:

  ```text
  Burp

  ↓
  
  SOCKS Proxy
  
  ↓
  
  Destination
  ```  

- SOCKS can be useful when traffic must traverse:

  ```text
  SSH Tunnel

  Pivot Host

  Special Network Gateway

  Testing Lab Route
  ```

---

## HTTP Upstream Proxy vs SOCKS

- Simplified distinction:

  ```text
  HTTP Proxy

  understands HTTP proxy semantics
  ```  

- while:

  ```text
  SOCKS

  provides more general connection forwarding
  ```

- Exact behavior depends on versions and configuration.

---

## DNS Through SOCKS

- An important question is:

  ```text
  Who resolves the hostname?
  ```

- Possible models:

  ```text
  Burp resolves locally

  ↓

  Connect through SOCKS to IP
  ```

- or:

  ```text
  Hostname resolution occurs through proxy path
  ```

depending on configuration.

- This matters for internal or hidden DNS namespaces.

---

## Example — Local DNS Cannot Resolve Internal Host

- Suppose:

  ```text
  app.lab.internal
  ```

can only be resolved from a remote testing network.

- If Burp tries:

  ```text
  Local DNS

  ↓

  app.lab.internal

  ↓

  NXDOMAIN / Failure
  ```  

the request fails before the remote path is useful.

- A routing configuration that resolves through the appropriate environment may be required.

---

## Hostname Resolution Overrides

- Testing environments sometimes require manually mapping a hostname to a specific IP.

- Conceptually:

  ```text
  app.example.test

  ↓

  192.0.2.50
  ```

instead of relying on ordinary DNS.

- This can be achieved through mechanisms such as:

  ```text
  Hosts File

  Burp Hostname Resolution Configuration
  
  Controlled DNS
  ```

depending on the environment.

---

## Why Overrides Are Useful

- Examples:

  ```text
  Pre-Production Environment

  Internal Application

  Virtual Host Testing

  Temporary Lab
  
  DNS Not Yet Published
  
  Specific Backend Node
  ```  

- The hostname may still be required for:

  ```text
  SNI

  Host Header

  Application Routing
  ```

even though DNS is manually overridden.

---

## IP Override Does Not Mean Replace the Host Header

- Suppose:

  ```text
  app.example.test

  ↓
  
  192.0.2.50
  ```

- You may still need:

  ```http
  Host: app.example.test
  ```

because the server may use virtual hosting.

- Sending:

  ```http
  Host: 192.0.2.50
  ```
  
could produce a different application or default virtual host.

---

## Correct Override Mental Model

```text
Logical Hostname

app.example.test

↓

Override Resolution

↓

192.0.2.50
```

- while preserving:

  ```text
  TLS SNI:

  app.example.test
  ```  

- and:

  ```text
  HTTP Host:

  app.example.test
  ```

- where appropriate.

---

## DNS Caching

- DNS results may be cached at different layers.

- Possible caches:

  ```text
  Browser

  Operating System
  
  Resolver

  Application

  Network Infrastructure
  ```  

- This can create situations where:

  ```text
  DNS Record Changed

  but

  Old Address Still Used Temporarily
  ```  

- When troubleshooting, consider stale resolution.

---

## IPv4 vs IPv6

- A hostname may resolve to:

  ```text
  A Record

  ↓

  IPv4
  ```

- and/or:

  ```text
  AAAA Record

  ↓

  IPv6
  ```  
  
- Different applications or network paths may prefer different address families.

- Possible scenario:

  ```text
  Browser Direct

  ↓
  
  IPv6 Works
  ```

- while:

  ```text
  Burp Path

  ↓

  IPv4 Route Fails
  ```

- or the reverse.

- Address-family differences can explain inconsistent connectivity.

---

## Redirects Are Not Network Routing

- Suppose:

  ```text
  GET /old
  ```

- returns:

  ```http
  HTTP/1.1 302 Found
  Location: https://new.example.com/home
  ```

- This is an HTTP redirect.

- The server tells the client to make another request.

---

## Redirect Flow

```text
Request to old.example.com

↓

302 Location: new.example.com

↓

Browser creates new request

↓

Request to new.example.com
```

- This is application-layer behavior.

---

## Network Routing Is Different

- Network routing determines:

  ```text
  How packets reach the selected destination.
  ```

- A redirect determines:

  ```text
  Which new HTTP resource the client should request.
  ```

- Therefore:

  ```text
  HTTP Redirect

  ≠

  Network Route
  ```

---

## Redirects Can Change Hosts

- Example:

  ```text
  https://example.com/login
  ```

- returns:

  ```text
  302

  https://auth.example.net/login
  ```

- Now the browser creates traffic to a new host.

- Burp must independently:

  ```text
  Resolve auth.example.net

  ↓
  
  Route to it
  
  ↓

  Establish connection

  ↓

  Negotiate TLS

  ↓
  
  Send request
  ```

- One successful host does not guarantee the redirected host will work.

---

## Multi-Host Applications

- Modern applications often depend on many hosts.

- Example:

  ```text
  www.example.com

  api.example.com
  
  auth.example.com
  
  static.examplecdn.com
  
  uploads.example.com
  ```  

- A page may fail if only one dependency is unreachable.

---

## Example — Main Page Works but Login Fails

- Flow:

  ```text
  www.example.com

  ↓
  
  Loads Successfully
  ```

- Login sends request to:

  ```text
  auth.example.com
  ```

- If Burp cannot resolve or reach that host:

  ```text
  Main Site Works

  but

  Login Fails
  ```

- Always inspect all hosts involved in the workflow.

---

## API Host Differences

- A front-end may load from:

  ```text
  app.example.com
  ```

- while API traffic goes to:

  ```text
  api.example.com
  ```

- Security testing must account for both.

- Do not assume:

  ```text
  One Browser Tab

  =
  
  One Host
  ```

---

## CDN and Third-Party Dependencies

- Applications may use:

  ```text
  CDN

  Authentication Provider

  Payment Provider

  Analytics

  Storage Service

  Captcha

  External APIs
  ```  
  
- These hosts may follow different routes and trust requirements.

- Only test systems covered by authorization.

---

## Host Header Modification and Routing

- During testing, modifying:

  ```http
  Host: example.com
  ```

- may affect:

  ```text
  Virtual Host Selection

  Reverse Proxy Routing
  
  Cache Behavior

  Application URL Generation
  ```

- But the network connection destination may remain unchanged depending on how the request is sent.

- This distinction is critical.

---

## Logical Destination vs Connection Destination

- Imagine a request is connected to:

  ```text
  192.0.2.50:443
  ```

- but contains:

  ```text
  SNI: app.example.com
  ```

- and:

  ```http
  Host: app.example.com
  ```

- These values collectively influence different layers.

- Do not collapse them into a single concept called "the host."

---

## Repeater Routing

- When sending a request from Repeater, Burp itself becomes the initiating client.

- Conceptually:

  ```text
  Repeater

  ↓
  
  Burp Determines Target

  ↓

  DNS / Route

  ↓

  Target
  ```  

- If you modify request destination details, ensure the intended:

  ```text
  Scheme

  Host

  Port

  SNI / Host Context
  ```  

remain consistent with your test.

---

## Proxy History vs Repeater Destination

- A request captured from the browser may originally target:

  ```text
  https://api.example.com
  ```

- After sending it to Repeater, it becomes an independent request.

- If you change the target destination in Repeater, the new request may follow a different routing path.

- Do not assume it remains tied to the original browser connection.

---

## DNS Rebinding and Changing Addresses

- Some hostnames may resolve to different addresses over time.

- Reasons include:

  ```text
  Load Balancing

  CDNs

  Failover

  DNS Rotation

  Dynamic Infrastructure
  ```

- Therefore repeated connections may not always reach the same backend.

- This can matter when reproducing inconsistent behavior.

---

## Load Balancers

- Architecture:

  ```text
  Burp

  ↓

  Load Balancer

  ├── Backend A
  ├── Backend B
  └── Backend C
  ```

- Two identical requests may reach different backend servers.

- This can explain:

  ```text
  Inconsistent Sessions

  Different Headers

  Different Error Messages

  Intermittent Behavior
  ```  

depending on infrastructure.

---

## Sticky Sessions

- Some systems attempt to keep a client associated with one backend.

- Possible mechanisms:

  ```text
  Cookie

  Source IP
  
  Load Balancer State
  ```

- Burp's role as the upstream client can influence connection or session behavior.

---

## Reverse Proxies

- A reverse proxy sits in front of application servers.

  ```text
  Burp

  ↓

  Reverse Proxy

  ↓
  
  Application
  ```

- It may handle:

  ```text
  TLS Termination

  Routing

  Caching

  Header Modification

  Authentication

  Rate Limiting
  ```  
  
- The response you observe may therefore reflect multiple infrastructure layers.

---

## WAF Routing

- A Web Application Firewall may sit in the path:

  ```text
  Burp

  ↓

  WAF
  
  ↓
  
  Application
  ```

- The WAF may:

  ```text
  Allow Request

  Block Request

  Challenge Client

  Modify Request

  Rate Limit
  ```

- A failure to reach expected application behavior does not necessarily mean network routing failed.

- Determine which layer responded.

---

## Transparent Network Intermediaries

- Networks may contain components such as:

  ```text
  Firewall

  NAT

  Security Gateway

  TLS Inspection Proxy
  
  Load Balancer
  ```

- These may influence traffic even when Burp configuration is correct.

- Troubleshooting requires distinguishing:

  ```text
  Burp Problem

  from

  Network Environment Problem
  ```  

---

## Common Failure — Connection Refused

- Conceptually:

  ```text
  Burp

  ↓

  IP:Port
  
  ↓

  Immediate Refusal
  ```  

- Possible causes:

  ```text
  No Service Listening

  Wrong Port
  
  Firewall Rejection
  
  Service Down
  
  Incorrect IP
  ```  

- This differs from DNS failure.

---

## Common Failure — Timeout

- Conceptually:

  ```text
  Burp

  ↓

  Attempt Connection
  
  ↓
  
  No Useful Response
  
  ↓

  Timeout
  ```

- Possible causes:

  ```text
  Firewall Drop

  Missing VPN Route

  Unreachable Network

  Server Not Responding

  Incorrect IP

  Proxy Path Failure
  ```  

- A timeout is not the same as a refusal.

---

## Common Failure — TLS Error

- Flow:

  ```text
  DNS

  ✓

  Connection

  ✓

  TLS

  ✗
  ```  

- Possible causes:

  ```text
  Wrong Hostname

  SNI Problem

  Certificate Problem
  
  Unsupported TLS

  mTLS Requirement
  
  Wrong Service on Port

  Network TLS Interception
  ```  

- This narrows the troubleshooting layer.

---

## Common Failure — Wrong Website Appears

- Suppose you expect:

  ```text
  app.example.com
  ```

but receive a generic default page.

- Possible causes:

  ```text
  Wrong Host Header

  Wrong SNI

  Wrong IP
  
  Virtual Host Misrouting
  
  Incorrect Resolution Override
  ```  

- The network connection may have succeeded perfectly while application routing was wrong.

---

## Common Failure — Browser Direct Works, Burp Gets Timeout

- Investigate:

  ```text
  Does browser use VPN-specific route?

  Does browser use system proxy?
  
  Does Burp use required upstream proxy?

  Can Burp resolve the same address?

  Is split tunneling involved?

  Is IPv4/IPv6 different?
  ```  

- Compare paths rather than randomly changing settings.

---

## Common Failure — Burp Browser Works, External Browser Fails

- This usually points toward the client-to-Burp side.

- Investigate:

  ```text
  External Browser Proxy Settings

  Proxy Bypass Rules

  Burp CA Trust
  
  Correct Browser Profile

  Browser Extensions
  ```

- Because:

  ```text
  Burp Browser Works

  ↓

  Burp's Upstream Path Likely Works
  ```  

---

## Common Failure — One Host Works, Another Does Not

- Example:

  ```text
  example.com
  
  ✓
  ```

- but:

  ```text
  api.example.com

  ✗
  ```

- Investigate per-host differences:

  ```text
  DNS

  Port

  TLS

  SNI

  VPN Route
  
  Upstream Proxy Rule

  Authorization Scope
  
  Different Infrastructure
  ```  

- Do not assume all subdomains share identical networking.

---

## Common Failure — IP Works but Hostname Does Not

- If:

  ```text
  https://192.0.2.50
  ```

- connects somehow, but:

  ```text
  https://app.example.test
  ```

- does not, possible issue:

  ```text
  DNS Resolution
  ```

- However, testing HTTPS by IP can produce different:

  ```text
  Certificate

  SNI

  Virtual Host

  Application Routing
  ```  

behavior.

- Therefore IP testing is only diagnostic evidence, not necessarily an equivalent request.

---

## Common Failure — Hostname Resolves but Wrong Backend Appears

- Possible causes:

  ```text
  Stale DNS

  Wrong Hosts File Entry

  Load Balancer

  Incorrect Override

  CDN Edge Difference
  
  Virtual Host Mismatch
  ```

- Inspect:

  ```text
  Resolved Address

  SNI

  Host Header

  Response Headers
  
  Certificate
  ```  

together.

---

## Systematic Troubleshooting Workflow

- When a target fails through Burp, use this sequence.

  ```text
  1. Client → Burp

  2. Destination Identification

  3. DNS Resolution
  
  4. Network Route
  
  5. TCP Connection

  6. TLS

  7. HTTP Routing
  
  8. Application Response
  ```

- Do not skip randomly between layers.

---

## Stage 1 — Client to Burp

- Ask:

  ```text
  Is browser configured to use Burp?

  Is listener running?

  Does request appear in HTTP history?
  ```

- If no:

  ```text
  Problem is before upstream routing.
  ```

---

## Stage 2 — Destination Identification

- Verify:

  ```text
  Correct Scheme?

  Correct Hostname?

  Correct Port?

  Correct URL?
  ```  

- A typo here makes every later diagnostic misleading.

---

## Stage 3 — DNS

- Ask:

  ```text
  Can hostname resolve?

  Is internal DNS required?

  Is VPN DNS active?

  Is override configured correctly?
  ```

- If DNS fails, do not troubleshoot TLS yet.

---

## Stage 4 — Network Route

- Ask:

  ```text
  Is target public or private?
    
  Is VPN required?

  Is upstream proxy required?

  Is SOCKS required?

  Does route exist?
  ```  

---

## Stage 5 — Connection

- Distinguish:

  ```text
  Connection Refused

  vs

  Timeout
  ```

- They suggest different classes of failure.

---

## Stage 6 — TLS

- For HTTPS, verify:

  ```text
  Correct SNI?

  Correct Port?
  
  Server Certificate?
  
  TLS Compatibility?

  mTLS?
  
  Network TLS Inspection?
  ```

---

## Stage 7 — HTTP Routing

- Verify:

  ```text
  Host Header / :authority

  Virtual Host

  Redirect Target

  Reverse Proxy Routing
  ```

- A successful TLS connection can still reach the wrong application.

---

## Stage 8 — Application

- Finally inspect:

  ```text
  Status Code

  Response Body
  
  Authentication

  WAF Behavior
  
  Application Errors
  
  Redirects
  ```

- At this point, the network may be working correctly.

---

## Diagnostic Matrix

| Observation                       | Likely Area to Investigate         |
| --------------------------------- | ---------------------------------- |
| Nothing appears in Burp           | Browser proxy / listener           |
| Hostname cannot resolve           | DNS                                |
| Immediate connection refusal      | Port / service / firewall          |
| Long timeout                      | Route / VPN / firewall             |
| TLS handshake error               | TLS / SNI / mTLS / wrong service   |
| Wrong site loads                  | Host / SNI / virtual hosting       |
| Main site works, API fails        | Per-host DNS/routing/TLS           |
| Burp Browser works, Firefox fails | External browser configuration     |
| Direct browser works, Burp fails  | Burp upstream path                 |
| HTTP works, HTTPS fails           | CONNECT / TLS / certificate / SNI  |
| 403 from edge infrastructure      | WAF / gateway / application policy |

- Use observations to narrow the layer.

---

## Professional Investigation Example

- Scenario:

  ```text
  https://portal.lab.internal
  ```

works directly while connected to VPN.

- Through Burp:

  ```text
  Connection timeout
  ```

- Investigation:

  ```text
  Step 1

  Request appears in HTTP history?
  
  Yes
  ```

- Therefore:

  ```text
  Browser → Burp works.
  ```

- Next:

  ```text
  Step 2

  Does portal.lab.internal resolve?
  ```  

- Suppose:

  ```text
  10.40.20.15
  ```

- Yes.

- Next:

  ```text
  Step 3

  Can Burp's upstream path reach 10.40.20.15:443?
  ```

- Suppose no.

- Likely investigation area:

  ```text
  VPN Routing / Split Tunnel / Proxy Path
  ```

- not:

  ```text
  Burp CA Certificate
  ```

- This is how layered troubleshooting prevents wasted effort.

---

## Professional Investigation Example — Wrong Virtual Host

- Suppose an override maps:

  ```text
  admin.example.test

  ↓

  192.0.2.50
  ```

- Connection succeeds, but response is:

  ```text
  Default Server Page
  ```

- Investigate:

  ```text
  SNI

  Host Header
  
  Virtual Host Configuration
  ```

- If the request contains:

  ```http
  Host: 192.0.2.50
  ```

- instead of:

  ```http
  Host: admin.example.test
  ```

the server may select the default virtual host.

---

## Professional Investigation Example — Redirected Host Failure

- Initial request:

  ```text
  https://app.example.com
  ```

works.

- Response:

  ```text
  302 → https://login.identity.example.net
  ```

- Then browser fails.

- Do not conclude:

  ```text
  app.example.com is broken through Burp.
  ```

- Instead inspect:

  ```text
  login.identity.example.net

  ↓

  DNS?

  ↓

  Route?

  ↓
  
  TLS?
  
  ↓
  
  Proxy Rule?
  ```

- The failure belongs to the second destination.

---

## Routing and Authorization

- Technical routing capability does not define testing permission.

- You may discover:

  ```text
  Internal Hostnames

  Alternate Backends
  
  Admin Hosts

  Development Environments
  ```

through traffic.

- Before testing them:

  ```text
  Check Explicit Scope and Authorization
  ```

- Never assume related infrastructure is automatically authorized.

---

## Common Mistakes

- Avoid:

  ```text
  DNS and routing are the same thing.
  ```

- Incorrect.

- DNS identifies addresses; routing determines paths.

---

```text
The Host header tells the network where packets go.
```

- Incomplete and often incorrect.

- Network connections use destination addresses and ports; `Host` operates at the HTTP layer.

---

```text
SNI and Host are exactly the same field.
```

- Incorrect.

- They belong to different protocol layers.

---

```text
If the IP is correct, the right website must load.
```

- Incorrect.

- Virtual hosting may depend on SNI and HTTP host identity.

---

```text
HTTPS always uses port 443.
```

- Incorrect.

- 443 is the default, not a requirement.

---

```text
One HTTP request always creates one new TCP connection.
```

- Incorrect.

- Connections may be reused or multiplexed.

---

```text
If the browser works directly, Burp must use the exact same network path.
```

- Incorrect.

- Burp creates its own upstream connections.

---

```text
A redirect is a network-routing operation.
```

- Incorrect.

- It instructs the client to create another HTTP request.

---

```text
If one subdomain works, every subdomain should work.
```

- Incorrect.

- Different hosts may use completely different infrastructure.

---

```text
Changing the Host header always changes the actual network destination.
```

- Incorrect.

- HTTP identity and connection destination are distinct concepts.

---

```text
Every timeout is a DNS problem.
```

- Incorrect.

- A timeout usually occurs after or independently of name resolution and may indicate routing or filtering.

---

```text
Installing Burp CA fixes all upstream connection problems.
```

- Incorrect.

- The Burp CA primarily addresses client trust in intercepted TLS, not DNS, VPN routes, upstream proxies, or arbitrary connectivity failures.

---

## Professional Mental Model

- Memorize:

  ```text
  Logical Destination

  Scheme + Host + Port

  ↓

  DNS / Resolution Override

  ↓

  IP Address

  ↓

  Network Route

  ↓

  TCP Connection

  ↓

  TLS + SNI if HTTPS
  
  ↓
  
  HTTP Host / :authority
  
  ↓
  
  Reverse Proxy / Gateway / Application
  ```  

- For proxied browsing:

  ```text
  Browser

  ↓
  
  Burp
  
  ↓
  
  Burp Determines Upstream Destination
  
  ↓

  DNS
  
  ↓

  VPN / Route / Proxy / SOCKS

  ↓

  Target IP:Port

  ↓

  TLS

  ↓

  HTTP
  
  ↓
      
  Application
  ```

- And remember:

  ```text
  DNS Identity

  Network Destination

  TLS Identity
  
  HTTP Identity
  ```

are related but distinct.

---

## Practical Exercise

- Use only an authorized practice environment.

### Exercise 1 — Identify Destination Components

- Choose a harmless HTTPS request from HTTP history.

- Identify:

  ```text
  Scheme

  Hostname

  Port

  Path

  Host / :authority
  ```  

- Then write the conceptual flow:

  ```text
  Hostname

  ↓

  DNS

  ↓

  IP

  ↓

  Port

  ↓

  TLS

  ↓

  HTTP
  ```  

---

### Exercise 2 — Observe Multiple Hosts

- Load one modern web page through Burp.

- In HTTP history, identify all distinct hosts contacted.

- Classify them as:

  ```text
  Main Application

  API

  Static Content
  
  Third Party
  ```

- Do not actively test third-party hosts unless explicitly authorized.

---

### Exercise 3 — Trace a Redirect

- Find a harmless redirect.

- Document:

  ```text
  Original Host

  ↓

  3xx Response
  
  ↓
  
  Location

  ↓

  New Host

  ↓

  New Request  
  ```

- Determine whether DNS and TLS must occur for a different destination.

---

### Exercise 4 — Compare Direct and Proxied Paths

- Conceptually document:

  ```text
  Direct Browser Path
  ```  

- and:

  ```text
  Browser → Burp → Target Path
  ```

- List every additional component introduced by Burp.

- This exercise helps with troubleshooting.

---

### Exercise 5 — Virtual Host Mental Model

- Given:

  ```text
  app.example.test → 192.0.2.50

  api.example.test → 192.0.2.50
  ```  

explain why the same IP can serve different applications.

- Identify the roles of:

  ```text
  SNI

  Host / :authority
  ```

---

## Routing Checklist

```text
[ ] I understand scheme, hostname, port, and path.

[ ] I understand DNS resolution.

[ ] I distinguish DNS from network routing.

[ ] I understand that a hostname may resolve but remain unreachable.

[ ] I understand HTTP Host.

[ ] I understand HTTP/2 :authority conceptually.

[ ] I understand TLS SNI.

[ ] I distinguish SNI from Host / :authority.

[ ] I understand virtual hosting.

[ ] I know one IP can serve multiple applications.

[ ] I understand connection establishment and reuse.

[ ] I understand Burp creates separate upstream connections.

[ ] I understand VPN routing conceptually.

[ ] I understand split tunneling conceptually.

[ ] I understand upstream proxies.

[ ] I understand SOCKS routing conceptually.

[ ] I understand hostname resolution overrides.

[ ] I know an IP override should not automatically replace logical hostname identity.

[ ] I distinguish redirects from network routing.

[ ] I understand multi-host application dependencies.

[ ] I can troubleshoot direct-browser-works-but-Burp-fails scenarios layer by layer.

[ ] I distinguish DNS, timeout, refusal, TLS, HTTP routing, and application failures.

[ ] I verify authorization before testing newly discovered hosts.
```

---

## Key Takeaways

* Burp must determine the scheme, hostname, port, and route for upstream requests.
* DNS resolution and network routing are separate processes.
* A hostname can resolve successfully while the resulting IP remains unreachable.
* HTTP `Host` or HTTP/2 `:authority` identifies the logical HTTP destination, while the network connection uses an IP address and port.
* TLS SNI communicates hostname context during TLS negotiation and is distinct from the HTTP host field.
* One IP address can serve many virtual hosts, so correct hostname context matters.
* Burp creates its own upstream connections rather than simply reusing the browser's direct connection.
* Connections may be reused, and HTTP/2 may multiplex many logical requests over one connection.
* VPNs, split tunneling, upstream proxies, SOCKS proxies, and internal DNS can all affect Burp's ability to reach a target.
* Hostname overrides can map logical names to specific IPs while preserving SNI and HTTP host identity.
* HTTP redirects create new requests; they are not network-routing operations.
* Modern applications often depend on multiple hosts, each with independent DNS, routing, TLS, and authorization considerations.
* A direct browser path and a Burp-mediated path may differ significantly.
* Troubleshooting should proceed in order: client → Burp, destination, DNS, route, connection, TLS, HTTP routing, then application behavior.
* Technical ability to route traffic to a discovered host does not mean that host is authorized for testing.
