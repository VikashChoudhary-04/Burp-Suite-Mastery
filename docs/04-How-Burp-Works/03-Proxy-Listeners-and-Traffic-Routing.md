# Proxy Listeners and Traffic Routing

> **Module 04 — How Burp Suite Works**

> **Progress**

```text id="d3r7wp"
███░░░░░░░░ 3 / 11 Lessons
```

- For traffic to pass through Burp Suite, Burp needs a network endpoint where clients can connect.

- That endpoint is a **proxy listener**.

- A common configuration is:

  ```text id="3ydv8x"
  Address:
  127.0.0.1

  Port:
  8080
  ```

- Conceptually:

  ```text id="pr83az"
  Browser

  ↓

  127.0.0.1:8080

  ↓

  Burp Proxy Listener

  ↓

  Burp Routing Logic

  ↓

  Target Server
  ```

- Understanding listeners and routing is essential because many apparent Burp problems are actually traffic-path problems.

- If a request never reaches the listener, Burp cannot intercept it.

- If Burp receives the request but cannot route it upstream, the browser cannot reach the target through Burp.

---

## Learning Objectives

- By the end of this lesson, you should be able to:

  * Explain what a Burp proxy listener is.
  * Understand IP address and port binding.
  * Distinguish loopback-only from externally reachable listeners.
  * Understand why `127.0.0.1:8080` is commonly used.
  * Recognize port conflicts.
  * Understand multiple proxy listeners.
  * Explain how external devices can route traffic through Burp.
  * Understand upstream proxy and SOCKS routing conceptually.
  * Understand host resolution and DNS overrides.
  * Explain invisible proxying conceptually.
  * Recognize the security risks of unnecessarily exposed listeners.
  * Troubleshoot traffic routing systematically.

---

## What Is a Proxy Listener?

- A proxy listener is a network socket Burp opens to accept incoming proxy connections.

- Conceptually:

  ```text id="56yby1"
  Client

  ↓

  IP Address + Port

  ↓

  Burp Listener
  ```  

- Example:

  ```text id="yqsq8c"
  127.0.0.1:8080
  ```

- means:

  ```text id="5dcnlh"
  IP:
  127.0.0.1

  Port:
  8080
  ```

- Burp waits for compatible incoming connections at that endpoint.

---

## Listener vs Interception

- Do not confuse these.

- A **listener** answers:

  ```text id="h2ym9q"
  Can traffic reach Burp?
  ```

- **Intercept** answers:

  ```text id="ue56az"
  Should Burp pause selected traffic
  for manual inspection?
  ```

- You can have:

  ```text id="s7l9mf"
  Listener Running

  +

  Intercept OFF
  ```

and traffic still passes through Burp.

---

## Basic Architecture

```text id="hw58po"
Browser Proxy Configuration

127.0.0.1:8080

↓

Burp Listener

127.0.0.1:8080

↓

Burp

↓

Target
```

- The client's proxy settings and Burp's listener configuration must agree.

---

## Matching Configuration

- Browser:

  ```text id="qxwpda"
  Proxy Host:
  127.0.0.1
  
  Proxy Port:
  8080
  ```

- Burp:

  ```text id="k6pr3n"
  Bind Address:
  127.0.0.1 / Loopback

  Bind Port:
  8080
  ```

- These configurations are compatible.

---

## Mismatched Port Example

- Browser:

  ```text id="m3wbz6"
  127.0.0.1:8080
  ```

- Burp:

  ```text id="g76mlc"
  127.0.0.1:8081
  ```

- Result:

  ```text id="x0r5sl"
  Browser

  ↓

  Attempts 8080

  ↓

  Burp Listening on 8081

  ↓

  Connection Fails
  ```
  
- The problem is routing, not HTTP.

---

## IP Address Binding

- A listener does not bind only to a port.

- It also binds to a network interface/address context.

- Common conceptual choices include:

  ```text id="zrtqk3"
  Loopback Only

  Specific Interface

  All Interfaces
  ```

- Each has different accessibility and security implications.

---

## Loopback

- The IPv4 loopback address is commonly:

  ```text id="9y7wcl"
  127.0.0.1
  ```

- It refers to the local machine.

- Conceptually:

  ```text id="mzw8qb"
  Browser on Computer A

  ↓

  127.0.0.1

  ↓

  Computer A
  ```

- Traffic does not need to leave the machine to reach Burp.

---

## Loopback-Only Listener

- A loopback listener is appropriate when:

  ```text id="q79jpc"
  Browser

  and

  Burp
  ```

run on the same machine.

- Architecture:

  ```text id="7pdh39"
  Local Browser

  ↓

  127.0.0.1:8080

  ↓

  Local Burp
  ```

- This is generally the safest default for ordinary desktop testing.

---

## Why Loopback Is Safer

- A loopback-only listener is not intended to accept ordinary connections from other machines on the network.

- Conceptually:

  ```text id="33wnw4"
  Other Device

  ─X─►

  127.0.0.1 on Your Computer
  ```

- This reduces unnecessary exposure.

---

## Binding to a Specific Network Interface

- Suppose your testing machine has:

  ```text id="8h2dpn"
  192.168.1.50
  ```

on a local network.

- Burp could be configured to listen on that specific interface.

- Conceptually:

  ```text id="e7u6pb"
  Mobile Device

  ↓

  192.168.1.50:8080

  ↓

  Burp
  ```

- This can be useful for testing traffic from another authorized device.

---

## Listening on All Interfaces

- A listener may be configured to accept connections on all suitable network interfaces.

- Conceptually:

  ```text id="hf6tzv"
  0.0.0.0:8080
  ```

often represents binding across IPv4 interfaces.

- This can make Burp reachable through multiple network addresses.

---

## Security Risk of Broad Binding

- Do not use broad listener exposure casually.

- If Burp listens on a network-accessible interface:

  ```text id="m3v2gs"
  Other Hosts

  ↓

  May Be Able to Reach Proxy Listener
  ```

depending on firewall and network configuration.

- Potential risks include:

  * Unauthorized proxy use.
  * Unexpected traffic entering Burp.
  * Exposure on untrusted networks.
  * Accidental routing of third-party traffic.
  * Increased attack surface.

- Use the narrowest listener exposure required for the task.

---

## Principle of Least Exposure

- Prefer:

  ```text id="4vn1se"
  Loopback Only
  ```

when only local applications need Burp.

- Use network-accessible bindings only when required, such as:

  ```text id="5chx12"
  Authorized Mobile Device Testing

  Remote Test Client

  Controlled Lab Device
  ```

and secure the environment appropriately.

---

## What Is a Port?

- A port identifies a logical network service endpoint on a host.

- Example:

  ```text id="j98qyu"
  127.0.0.1:8080
  ```

- means:

  ```text id="6bwctf"
  Host:
  127.0.0.1

  Port:
  8080
  ```

- The port helps the operating system deliver the connection to the correct listening process.

---

## Port 8080

- Burp commonly uses:

  ```text id="up12vg"
  8080
  ```

as a default proxy-listener port.

- But there is nothing inherently required about `8080`.

- You could use another available port, such as:

  ```text id="32n5vs"
  8081

  8888

  9000
  ```  
  
provided the client configuration matches.

---

## Port Conflicts

- Only compatible socket-binding arrangements can coexist.

- If another process already occupies the required address/port combination, Burp may fail to start the listener.

- Conceptually:

  ```text id="uw6nks"
  Application A

  ↓

  Uses 127.0.0.1:8080
  ```

- Then Burp attempts:

  ```text id="7qpn9z"
  Bind 127.0.0.1:8080
  ```

- Possible result:

  ```text id="2x5ev7"
  Bind Failure
  ```

---

## Symptoms of a Port Conflict

- You may observe:

  ```text id="23m0gz"
  Proxy Listener Cannot Start

  Browser Gets Connection Refused
  
  Listener Shows an Error

  Another Proxy Tool Is Running
  ```

- Common competing applications may include:

  * Another Burp instance.
  * OWASP ZAP.
  * Development proxies.
  * Debugging proxies.
  * Local services using the same port.

---

## Troubleshooting Port Conflicts

- Conceptual workflow:

  ```text id="82p5jq"
  Listener Failed?

  ↓

  Check Whether Port Is Already Used

  ↓

  Identify Process

  ↓

  Stop Conflicting Service
  or  
  Choose Another Port

  ↓

  Update Client Proxy Configuration  
  ```

- Do not change only Burp's port and forget the browser.

---

## Multiple Proxy Listeners

- Burp can use more than one proxy listener.

- Example:

  ```text id="b9rkz4"
  127.0.0.1:8080

  192.168.1.50:8081
  ```

- Possible purpose:

  ```text id="v2wj8e"
  Listener 1

  ↓

  Local Browser
  ```

  ```text id="18q4ks"
  Listener 2

  ↓

  Authorized Mobile Device
  ```

- Different listeners can support different traffic-routing requirements.

---

## Why Use Multiple Listeners?

- Possible scenarios include:

  ```text id="w9cr8q"
  Local Browser Testing

  Mobile Application Testing

  Separate Lab Environments

  Different Network Interfaces

  Special Proxy Behavior
  ```  

- Use them intentionally.

- More listeners mean more configuration complexity and potentially more exposure.

---

## Traffic From an External Device

- Suppose:

  ```text id="k5q2af"
  Laptop running Burp:  
  192.168.1.50
  ```

- and:

  ```text id="0y3pvl"
  Authorized Phone:
  192.168.1.60
  ```

- A possible architecture:

  ```text id="r43tka"
  Phone

  ↓

  Proxy:
  192.168.1.50:8080

  ↓

  Burp on Laptop

  ↓
  
  Target
  ```

- For this to work, several conditions must be satisfied.

---

## External Device Requirements

- Conceptually:

  ```text id="4d1zpg"
  [ ] Burp listener is reachable on network interface.

  [ ] Correct IP address is used.

  [ ] Correct port is used.

  [ ] Firewall allows connection.

  [ ] Devices can reach each other.

  [ ] Client is configured to use proxy.
  
  [ ] HTTPS trust is configured if interception is required.

  [ ] Testing is authorized.
  ```

- A failure in any layer can break proxying.

---

## 127.0.0.1 From Another Device

- A common mistake is configuring a phone with:

  ```text id="5x9uvc"
  Proxy:
  127.0.0.1:8080
  ```

- That means:

  ```text id="f6c0mh"
  Phone

  ↓

  Phone's Own Loopback Interface
  ```  

- It does **not** mean the laptop running Burp.

- The phone needs the reachable network address of the Burp machine, such as:

  ```text id="pv62nh"
  192.168.1.50
  ```

in this example.

---

## Firewall Considerations

- Even if Burp listens on:

  ```text id="81mwjg"
  192.168.1.50:8080
  ```

the operating-system firewall may block inbound connections.

- Conceptually:

  ```text id="9ak37p"
  Phone

  ↓
  
  Network

  ↓

  Firewall
  
  ─X─►

  Burp
  ```

- Always consider the host firewall when remote clients cannot connect.

---

## Listener Exposure and Untrusted Networks

- Suppose your laptop is connected to public Wi-Fi.

- A broadly exposed listener could potentially become reachable by other devices depending on network isolation and firewall rules.

- Avoid unnecessary exposure.

- Prefer:

  ```text id="6prw2m"
  Loopback

  or

  Controlled Private Interface
  ```  

with appropriate firewall restrictions.

---

## Traffic Routing After the Listener

- Receiving the request is only the first stage.

- Conceptually:

  ```text id="h56m7d"
  Client

  ↓

  Burp Listener

  ↓

  Request Parsing

  ↓

  Destination Determination

  ↓

  Routing Rules
  
  ↓
  
  DNS / Host Resolution

  ↓

  Upstream Connection

  ↓

  Target
  ```

- Burp must know how to reach the destination.

---

## Direct Upstream Connection

- The simplest path:

  ```text id="s8tn1j"
  Browser

  ↓

  Burp

  ↓

  Target
  ```  

- Burp connects directly toward the target using the configured network environment.

---

## Upstream Proxy

- Some environments require internet traffic to pass through another proxy.

- Architecture:

  ```text id="m7r4px"
  Browser

  ↓

  Burp
  
  ↓

  Upstream Proxy
  
  ↓

  Target
  ```

- Examples include:

  * Corporate networks.
  * Restricted enterprise environments.
  * Controlled test infrastructure.

---

## Why Upstream Proxy Rules Matter

- Suppose direct outbound connections are blocked.

- Without upstream proxy configuration:

  ```text id="f8x1dj"
  Burp

  ─X─►

  Internet
  ```  

- With required upstream proxy:

  ```text id="z2v0kw"
  Burp

  ↓

  Corporate Proxy

  ↓

  Internet
  ```

- The browser may appear broken through Burp even though Burp's local listener is functioning correctly.

---

## Proxy Chaining

- Conceptually:

  ```text id="e3m4sz"
  Client

  ↓

  Burp

  ↓

  Proxy A

  ↓

  Proxy B
  
  ↓
  
  Destination
  ```

- Each additional intermediary can affect:

  * DNS.
  * TLS.
  * Authentication.
  * Headers.
  * Source IP.
  * Connectivity.
  * Protocol behavior.

- Always know the intended routing chain.

---

## SOCKS Proxy

- SOCKS is a more general proxying mechanism that can route network connections.

- A possible architecture:

  ```text id="4d9n3y"
  Browser

  ↓

  Burp

  ↓

  SOCKS Proxy

  ↓
  
  Target
  ```

- This may be used in controlled environments for:

  * Network pivoting.
  * Lab routing.
  * Accessing isolated test networks.
  * Routing through approved infrastructure.

---

## HTTP Proxy vs SOCKS Concept

- Simplified:

  ```text id="x7m2cn"
  HTTP Proxy

  ↓

  Understands HTTP proxy semantics
  ```

  ```text id="4q9zk1"
  SOCKS Proxy

  ↓

  Provides more general connection forwarding
  ```

- The exact protocols and behavior differ.

- For this module, the important concept is that Burp's upstream route may include either.

---

## DNS and Routing

- Suppose Burp needs to reach:

  ```text id="p8d4ty"
  internal.acme.test
  ```

- It must ultimately determine how to route traffic toward the appropriate destination.

- Potential mechanisms include:

  ```text id="x4c7ms"
  DNS Resolution

  Hosts File
  
  Burp Hostname Resolution Rules

  Upstream Proxy Resolution

  SOCKS-Based Resolution
  ```  
  
- The actual path depends on configuration.

---

## Hostname Resolution Override

- In a controlled lab, suppose:

  ```text id="n2u7kg"
  staging.acme.test
  ```

- should resolve to:

  ```text id="h9k3wd"
  10.10.10.25
  ```

but normal DNS does not provide that mapping.

- A hostname-resolution override can conceptually create:

  ```text id="6m1srf"
  staging.acme.test

  ↓

  10.10.10.25
  ```  

while preserving the hostname in the HTTP request.

---

## Why Preserve the Hostname?

- Virtual hosting may depend on:

  ```http id="3w9vmt"
  Host: staging.acme.test
  ```

- or:

  ```text id="f7r2qb"
  :authority: staging.acme.test
  ```

- Connecting only to:

  ```text id="t5k1na"
  10.10.10.25
  ```

without the correct host context may reach the wrong virtual application.

---

## IP Routing vs HTTP Host Identity

- Think of:

  ```text id="c4v8he"
  IP Address

  ↓

  Where the network connection goes
  ```

- and:

  ```text id="0b6xpy"
  Host / :authority

  ↓

  Which logical application is requested
  ```

- They are related but not identical.

---

## Request Redirection / Routing Rules

- Advanced proxy configurations may intentionally redirect traffic.

- Conceptually:

  ```text id="n3e5sf"  
  Requested Host:

  app.example.com
  ```

- but route the connection toward:

  ```text id="7v1hca"
  10.10.10.20
  ```

while retaining the intended host identity.

- This is useful in controlled staging, testing, and virtual-host environments.

- Incorrect routing can also produce confusing results.

---

## Invisible Proxying Concept

- Normally, a client explicitly knows it is using a proxy.

- For example:

  ```text id="m8r6up"
  Browser Proxy Settings

  ↓

  Burp
  ```

- But sometimes traffic is redirected to Burp without the client being explicitly configured as an HTTP proxy.

- This creates a different problem:

  ```text id="f4c1sy"
  Client Thinks:

  "I am talking directly to the target."
  ```

- while:

  ```text id="9q2kve"
  Traffic Actually Reaches:

  Burp
  ```

- Burp calls support for scenarios like this **invisible proxying**.

---

## Explicit Proxy Request

- A proxy-aware HTTP client might send:

  ```http id="p3m6ad"
  GET http://example.com/account HTTP/1.1
  Host: example.com
  ```

- The absolute URL helps identify the destination.

---

## Transparently Redirected Request

- A client that believes it is speaking directly to the origin may send:

  ```http id="z1x8kc"
  GET /account HTTP/1.1
  Host: example.com
  ```

- Burp must infer the intended destination from available context.

- Invisible proxying helps Burp handle certain non-proxy-aware traffic scenarios.

---

## Invisible Proxying Is Not Usually Needed for Normal Browser Setup

- For standard browser testing:

  ```text id="w7f5bt"
  Browser Explicitly Configured

  ↓

  Burp Listener
  ```

you normally do not need invisible proxy support.

- Do not enable advanced options simply because they sound more powerful.

- Use them when the traffic architecture requires them.

---

## When Invisible Proxying May Be Relevant

- Conceptually, scenarios may include:

  ```text id="k1d9qe"
  Non-Proxy-Aware Client

  Traffic Redirected at Network Layer

  Certain Thick Clients

  Controlled Mobile / Embedded Testing

  Special Lab Routing
  ```  

- The exact setup can be more complex than ordinary browser proxying.

---

## Why Incorrect Invisible Proxy Settings Cause Problems

- If enabled unnecessarily or combined with incorrect routing assumptions, you may create:

  * Wrong destination inference.
  * TLS problems.
  * Routing loops.
  * Unexpected host handling.
  * Confusing request behavior.

- Start with the simplest correct architecture.

---

## Routing Loops

- A routing loop can occur when proxy traffic is accidentally sent back toward the same proxy repeatedly.

- Conceptually:

  ```text id="d4y8nu"
  Burp

  ↓
  
  Upstream Route

  ↓

  Burp

  ↓

  Upstream Route

  ↓

  Burp
  ```  

- Symptoms may include:

  * Requests never complete.
  * Repeated connections.
  * Timeouts.
  * Strange proxy errors.

- Always ensure upstream proxy settings do not point back into an unintended loop.

---

## Listener Running vs Reachable

- A listener can be technically running but still unreachable from the intended client.

- Example:

  ```text id="6x9fcp"
  Burp:

  Listening on 127.0.0.1:8080
  ```

- Phone:

  ```text id="2w5nra"
  Attempts:

  192.168.1.50:8080
  ```

- The listener is active, but only on loopback.

- Therefore:

  ```text id="y7m1bk"
  Running

  ≠

  Reachable From Every Interface
  ```

---

## Reachability Layers

- For an external client:

  ```text id="h2q8sv"
  Client Configuration

  ↓

  Network Reachability

  ↓

  Firewall

  ↓
  
  Listener Binding

  ↓

  Burp
  ```  

- All must align.

---

## Proxy Authentication

- Some upstream proxies require authentication.

- Architecture:

  ```text id="5n3xrt"
  Browser

  ↓

  Burp

  ↓

  Authenticated Upstream Proxy

  ↓
  
  Target
  ```

- If Burp cannot authenticate correctly, upstream requests may fail even though:

  ```text id="u9c6lg"
  Browser → Burp
  ```

works perfectly.

- Again, isolate the failing leg.

---

## Source IP Considerations

- When Burp connects upstream, the target sees the network source associated with Burp's onward connection path.

- Conceptually:

  ```text id="3m7r1w"
  Browser

  ↓

  Burp
  
  ↓
  
  Target Sees Burp-Side Network Path
  ```

- If Burp routes through:

  ```text id="s4h8pk"
  VPN

  SOCKS

  Corporate Proxy

  Testing Gateway
  ```

the apparent source may change accordingly.

- This can affect:

  * IP allowlists.
  * Rate limits.
  * Geolocation behavior.
  * Network access.

---

## VPN Routing

- A common architecture:

  ```text id="v2p5nf"
  Browser

  ↓

  Burp

  ↓

  VPN Interface

  ↓

  Authorized Internal Target
  ```

- If the VPN route is missing or Burp uses an unintended network path, the target may be unreachable.

- Check:

  ```text id="x8k3ql"
  Is VPN connected?

  Does route exist?

  Can Burp-side environment resolve host?

  Can Burp-side environment reach target?
  ```

---

## Burp Listener Security Checklist

- When creating a listener:

  ```text id="t5r1gc"
  [ ] Use the narrowest required bind address.

  [ ] Use an available port.

  [ ] Avoid unnecessary exposure to untrusted networks.

  [ ] Check host firewall rules.
  
  [ ] Know which clients are expected to connect.

  [ ] Remove temporary listeners when finished.

  [ ] Do not expose Burp as an unintended open proxy.

  [ ] Keep testing traffic within authorized scope.
  ```

---

## Traffic Routing Troubleshooting

- Use a layered workflow.

### Layer 1 — Client

- Ask:

  ```text id="j4m9vb"
  Is the client configured to use the correct proxy IP?

  Is the proxy port correct?

  Are bypass rules active?
  ```  

---

### Layer 2 — Listener

- Ask:

  ```text id="e7p2sz"
  Is the Burp listener running?

  Is it bound to the expected address?

  Is it using the expected port?
  ```

---

### Layer 3 — Network Reachability

- For remote devices:

  ```text id="m6k1xf"
  Can the client reach the Burp machine?

  Does a firewall block the port?

  Are both devices on reachable networks?
  ```  

---

### Layer 4 — Burp Routing

- Ask:

  ```text id="r9c4wh"
  Does Burp know the intended destination?

  Are upstream proxy rules correct?

  Is SOCKS configured correctly?

  Are hostname overrides correct?
  ```  

---

### Layer 5 — DNS

- Ask:

  ```text id="q3n7da"
  Can the destination hostname be resolved?

  Is the expected resolver being used?

  Is split DNS involved?

  Does the hostname need an override?
  ```

---

### Layer 6 — Upstream Connection

- Ask:

  ```text id="f1y8ke"
  Can Burp reach the destination IP and port?

  Is VPN routing required?

  Is an upstream proxy required?

  Is the server listening?
  ```  

---

### Layer 7 — TLS

- If HTTPS fails:

  ```text id="b5u2mz"
  Is browser trust configured?

  Can Burp establish upstream TLS?

  Is certificate validation failing?

  Is protocol negotiation failing?
  ```

- TLS is covered in detail in the next lesson after request lifecycle foundations.

---

### Layer 8 — HTTP/Application

- If connectivity works:

  ```text id="k7p4cs"
  Is the Host / authority correct?

  Is the request valid?

  Is authentication required?

  Is the application returning the error?
  ```  

- Only now should you focus primarily on application behavior.

---

## Common Failure Scenario 1

- Symptom:

  ```text id="x2g6nt"
  Browser says:

  Proxy server refusing connections
  ```  

- Likely investigation:

  ```text id="4v8kqm"
  Browser Proxy Address

  ↓

  Browser Proxy Port

  ↓

  Burp Listener Running?

  ↓
  
  Correct Bind Address?
  
  ↓
  
  Port Conflict?
  ```  

- This usually points toward the browser-to-Burp leg.

---

## Common Failure Scenario 2

- Symptom:

  ```text id="h9r3wy"
  Request appears in Burp

  but

  Target does not load
  ```

- The browser-to-Burp leg works.

- Investigate:

  ```text id="s6n1fc"
  Burp

  ↓

  DNS

  ↓

  Routing

  ↓

  Upstream Proxy

  ↓

  VPN

  ↓

  TLS

  ↓
  
  Target
  ```

- Do not keep changing browser proxy settings when the request already reaches Burp.

---

## Common Failure Scenario 3

- Symptom:

  ```text id="p8d2vx"
  Local browser works through Burp

  Phone cannot connect
  ```

- Investigate:

  ```text id="n5q7lb"
  Is listener loopback-only?

  ↓

  Is phone using laptop's network IP?
  
  ↓  

  Firewall?

  ↓
  
  Same/reachable network?

  ↓

  Correct port?
  ```

---

## Common Failure Scenario 4

- Symptom:

  ```text id="g3w9ka"
  Target loads normally without Burp

  but fails through Burp
  ```  

- This proves only:

  ```text id="c1f6zr"
  Direct client path works
  ```

- It does not prove:

  ```text id="u4h8mp"
  Burp-side path works
  ```

- Compare:

  ```text id="r2v5sn"
  Direct:

  Browser → Target
  ```

- with:

  ```text id="w7k3dt"
  Proxied:

  Browser → Burp → Target
  ```

- The second path contains additional failure points.

---

## Common Failure Scenario 5

- Symptom:

  ```text id="q9m4ce"
  No traffic appears in Burp
  ```
  
- Possible causes:

  ```text id="a6x2pg"
  Browser not using proxy

  Wrong proxy port

  Wrong proxy address

  Proxy bypass rule
  
  Using different browser profile

  Extension disabled

  Burp listener inactive

  Traffic using unsupported / different route
  ```

- Start at the client.

---

## Common Mistakes

- Avoid:

  ```text id="f4n8ys"
  If Burp listener says running,
  every device can connect to it.
  ```

- Incorrect.

- Binding and network reachability matter.

---

```text id="z1p6kw"
127.0.0.1 always means the computer running Burp.
```

- Incorrect.

- It means the loopback interface of whichever device is using that address.

---

```text id="m7c3vd"
Port 8080 must always be used.
```

- Incorrect.

- Any suitable available port can be used if configuration matches.

---

```text id="k2h9rx"
Binding to all interfaces is always better.
```

- Incorrect.

- It creates unnecessary exposure unless required.

---

```text id="y5w1qb"
If the request reaches Burp, browser proxy configuration must still be the problem.
```

- Usually incorrect.

- Once the request reaches Burp, investigate the onward path.

---

```text id="d8r4mf"
DNS always happens in the browser.
```

- Incorrect.

- Proxy architecture and routing configuration can change resolution responsibilities.

---

```text id="v3k7sj"
Invisible proxying should always be enabled.
```

- Incorrect.

- Use it only when the architecture requires it.

---

```text id="c6p2na"
Anything routed through Burp is safe to test.
```

- Incorrect.

- Routing does not define authorization.

---

## Professional Routing Mental Model

- Memorize:

  ```text id="e1q5hz"
  Client

  ↓

  Proxy Configuration

  ↓

  Network Path

  ↓

  Burp Listener

  ↓

  Interception / Processing

  ↓

  Destination Identification

  ↓

  Routing Rules

  ↓

  DNS / Host Resolution

  ↓

  Upstream Proxy / SOCKS / VPN

  ↓

  Target Connection

  ↓
  
  Application
  ```  

- When something breaks, identify the first stage where expected behavior stops.

---

## Practical Exercise

- Use a local authorized environment.

### Exercise 1

- Verify:

  ```text id="u8m2lc"
  Burp Listener:

  127.0.0.1:8080
  ```

and configure your dedicated browser to use the same endpoint.

- Confirm:

  ```text id="p3y7dw"
  Request Appears in HTTP History
  ```

with Intercept OFF.

---

### Exercise 2

- Change Burp's listener port to another available local port.

- Example:

  ```text id="s9f4kb"
  8081
  ```

- Without changing the browser, observe the failure.

- Then update the browser proxy to match.

- Confirm traffic works again.

- This demonstrates:

  ```text id="x6n1qt"
  Client Configuration

  must match

  Listener Configuration
  ```

---

### Exercise 3

- Return to the normal listener configuration after testing.

- Do not leave unnecessary listeners exposed.

---

## Listener and Routing Checklist

```text id="b2v8gc"
[ ] I understand what a proxy listener does.

[ ] I know the listener bind address.

[ ] I know the listener port.

[ ] I distinguish loopback from network-accessible interfaces.

[ ] I understand why 127.0.0.1 works only locally.

[ ] I know how port conflicts occur.

[ ] I understand why multiple listeners may be useful.

[ ] I use the narrowest required listener exposure.

[ ] I understand how an external device reaches Burp.

[ ] I consider host firewall rules.

[ ] I understand upstream proxy routing conceptually.

[ ] I understand SOCKS routing conceptually.

[ ] I understand that DNS behavior depends on routing architecture.

[ ] I understand hostname resolution overrides.

[ ] I understand invisible proxying conceptually.

[ ] I know routing and interception are separate.

[ ] I troubleshoot from client toward target in layers.

[ ] I distinguish traffic visibility from authorization.
```

---

## Key Takeaways

* A proxy listener is the network endpoint where Burp accepts client proxy traffic.
* Listener configuration includes both a bind address and a port.
* `127.0.0.1:8080` is commonly used for local browser testing.
* Loopback-only listeners reduce unnecessary network exposure.
* External devices must use a network-reachable address of the Burp machine, not their own `127.0.0.1`.
* Binding to all interfaces should only be done when required and appropriately secured.
* Port conflicts can prevent a listener from starting.
* Client proxy settings must match the listener's reachable address and port.
* Multiple listeners can support different clients and testing environments.
* Burp may route upstream directly, through another HTTP proxy, through SOCKS, or across VPN-controlled paths.
* DNS and hostname resolution depend on the actual routing architecture.
* IP routing and HTTP host identity are related but distinct.
* Invisible proxying is for specialized non-proxy-aware traffic scenarios and is not normally needed for standard browser testing.
* A running listener is not necessarily reachable from every device.
* Troubleshooting should follow the traffic path from client → listener → routing → DNS → upstream connection → TLS → HTTP/application.
* Use the narrowest network exposure necessary and test only systems within authorized scope.

