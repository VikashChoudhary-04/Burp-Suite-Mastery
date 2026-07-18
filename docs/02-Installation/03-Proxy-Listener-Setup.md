# Proxy Listener Setup

> **Module Progress**

```text id="r4w9mt"
███░░░░░░ 3 / 9 Lessons
```

- Before a browser can send traffic through Burp Suite, Burp must be listening for proxy connections.

- This is the role of the **proxy listener**.

- Understanding listeners makes problems such as:

  * "Proxy server refusing connections"
  * No traffic appearing in Burp
  * Port conflicts
  * Incorrect bind addresses

much easier to troubleshoot.

---

## The Core Mental Model

- Without Burp:

  ```text id="p8m3qn"
  Browser

  ↓

  Web Application
  ```

- With Burp configured as an intercepting proxy:

  ```text id="v5n9kx"
  Browser

  ↓

  Burp Proxy Listener

  ↓

  Burp Suite

  ↓

  Web Application
  ```

- The browser needs a specific network destination where it can send proxy traffic.

- The listener provides that destination.

---

## What Is a Listener?

- A network service generally listens on:

  ```text id="q2m7wr"
  IP Address

  +

  Port  
  ```

- Together, they identify where incoming connections should arrive.

- A common Burp local proxy configuration is:

  ```text id="n6p4mv"
  127.0.0.1:8080
  ```

- This means:

  ```text id="x8q3kt"
  127.0.0.1

  =

  Local Machine  
  ```

and:

  ```text id="m4v9pn"
  8080

  =

  TCP Port  
  ```

---

## Understanding 127.0.0.1

- `127.0.0.1` is a loopback address.

- It refers back to the local machine.

- Conceptually:

  ```text id="r7n2qw"
  Browser

  and

  Burp Suite

  Running on Same Computer

  ↓

  Communicate Through Loopback
  ```

- Traffic does not need to leave the machine for the browser to connect to Burp.

---

## Understanding Port 8080

- A computer can run many network services simultaneously.

- Ports help distinguish them.

- For example:

  ```text id="k3m8vx"
  Application A → Port 3000

  Application B → Port 8000
  
  Burp Proxy → Port 8080
  ```

- Port `8080` is commonly used as Burp's default proxy listener port, although the configuration can be changed.

---

## The Listener and Browser Must Match

- Suppose Burp listens on:

  ```text id="t5q9nr"
  127.0.0.1:8080
  ```

- The browser proxy must point to the same destination.
  
  ```text id="w2m7pk"
  Browser Proxy
  
  Host: 127.0.0.1

  Port: 8080
  ```

- If they do not match:

  ```text id="p9n4qm"
  Browser → 127.0.0.1:9090

  Burp → 127.0.0.1:8080
  ```

the browser is sending traffic somewhere Burp is not listening.

---

## Bind Address

- A listener must bind to a network interface or address.

- For local learning environments, a loopback-only configuration is usually appropriate.

- Conceptually:

  ```text id="v6m2rx"
  Loopback Only

  ↓

  Accept Connections From Local Machine
  ```

- This reduces unnecessary network exposure.

---

## Why Bind Address Matters

- Compare:

  ```text id="q8n3mw"
  127.0.0.1
  
  ↓

  Local Access
  ```

with a listener exposed on broader network interfaces.

- Broader binding may allow connections from other systems depending on network and firewall configuration.

- Do not expose proxy listeners unnecessarily.

- Use the minimum network exposure required for your authorized testing environment.

---

## Listener Status

- Before configuring the browser, verify that the listener is active.

- Conceptually:

  ```text id="n4p9kv"
  Burp Running

  ↓

  Proxy Listener Exists

  ↓

  Listener Enabled

  ↓

  Address and Port Confirmed
  ```

- If the listener is disabled, the browser may attempt to connect but receive no proxy service.

---

## Port Conflict

- Only one process can normally bind the same specific IP-and-port combination at a time.

- Suppose another application already uses:

  ```text id="m7q2pn"
  127.0.0.1:8080
  ```

- Burp may be unable to start its listener there.

- Conceptually:

  ```text id="x5n8qr"
  Application A

  ↓

  Owns Port 8080

  ↓

  Burp Attempts Same Bind

  ↓

  Conflict
  ```

- This can cause listener startup problems.

---

## Resolving a Port Conflict

- You have two general options.

### Option 1 — Free the Port

- Identify whether another process legitimately needs the port.

- If appropriate, stop or reconfigure that process.

### Option 2 — Change Burp's Listener Port

- For example:

  ```text id="r3m9vw"
  Burp

  127.0.0.1:8081
  ```

- Then update the browser to match:

  ```text id="k6p4nt"
  Browser Proxy

  127.0.0.1:8081
  ```

- The exact port number is less important than consistency.

---

## Verify at the Operating-System Level

- When troubleshooting, it can be useful to confirm whether a process is actually listening on the expected port.

- On Linux, examples include:

  ```bash id="f7m2qx"
  ss -ltn
  ```

or filtering for a specific port:

  ```bash id="p4n8kv"
  ss -ltn | grep 8080
  ```

- Depending on the system, other tools may include:

  ```bash id="m9q3wr"
  lsof -i :8080
  ```

- The objective is to answer:

  > Is something actually listening on this port?

---

## Interpreting the Result

- If you see a listening socket associated with the expected address and port:

  ```text id="v2k7pn"
  LISTEN
  
  ↓

  127.0.0.1:8080
  ```

the network listener probably exists.

- You can then investigate the next layer:

  ```text id="q5m8rx"
  Browser Configuration
  ```

- If nothing is listening, troubleshoot Burp's listener before changing browser certificates or application settings.

---

## "Proxy Server Refusing Connections"

- This error often suggests a connectivity problem between the browser and configured proxy.

- Use this sequence:

  ```text id="n8p3mw"
  Is Burp Running?

  ↓

  Is Proxy Listener Enabled?

  ↓

  What Address Is It Bound To?

  ↓
  
  What Port Is It Using?
  
  ↓

  Does Browser Configuration Match?

  ↓

  Is Another Process Using the Port?

  ↓

  Is Local Security Software Interfering?
  ```

- Do not immediately troubleshoot HTTPS certificates.

- The browser must first be able to reach the proxy.

---

## Listener vs Intercept

- These are different concepts.

  ```text id="t4m9qv"
  Proxy Listener

  =

  Accepts Browser Proxy Connections
  ```

- while:

  ```text id="r7n2pk"
  Intercept

  =

  Controls Whether Matching Traffic Is Paused for Manual Review
  ```

- A listener can be working even when interception is off.

- Traffic may still appear in HTTP history.

---

## Important Troubleshooting Insight

- Suppose:

  * Browser pages load through Burp.
  * Requests appear in HTTP history.
  * Intercept is OFF.

- Is the proxy broken?

  - No.

- The listener may be functioning perfectly.

- Intercept OFF usually means requests are allowed to pass without being paused.

---

## Listener Troubleshooting Ladder

- Use this order:

  ```text id="k9m4vx"
  1. Burp Process Running?

  ↓

  2. Listener Enabled?

  ↓

  3. Correct Bind Address?

  ↓

  4. Correct Port?

  ↓

  5. Port Actually Listening?
  
  ↓
  
  6. Browser Points to Same Address/Port?

  ↓

  7. HTTP Traffic Works?

  ↓
  
  8. HTTPS Trust Works?
  ```

- Troubleshoot from lower layers upward.

---

## Example Diagnosis

- Suppose:

  ```text id="p6n3qw"
  Burp Listener:
  127.0.0.1:8080
  ```

- Browser:

  ```text id="x8m2kr"
  Proxy:
  127.0.0.1:8080
  ```

- Operating system:

  ```text id="v5q9nt"
  LISTEN:
  127.0.0.1:8080
  ```

but HTTPS sites show certificate errors.

- The listener is probably not the main problem.

- Move to:

  ```text id="m3p7rx"
  HTTPS / CA Certificate Trust
  ```

- This layered reasoning prevents unnecessary configuration changes.

---

## Security Principle

- Do not expose Burp's proxy listener to external interfaces unless your authorized testing setup specifically requires it and you understand the implications.

- Prefer:

  ```text id="q4n8mv"  
  Minimum Necessary Exposure
  ```

- A proxy can process sensitive traffic.

- Treat listener configuration as a security control.

---

## Professional Listener Checklist

```text id="r9m2pk"
[ ] Burp is running.

[ ] Listener is enabled.

[ ] Bind address is intentional.

[ ] Port is known.

[ ] No unintended port conflict exists.

[ ] Browser proxy matches listener.

[ ] Listener is not unnecessarily exposed.

[ ] Basic HTTP connectivity is verified before HTTPS troubleshooting.
```

---

## Key Takeaways

* A proxy listener accepts browser connections into Burp.
* A listener is identified by an address and port.
* `127.0.0.1:8080` is a common local Burp configuration.
* Browser and listener settings must match.
* Loopback binding is appropriate for many local setups.
* Port conflicts can prevent listeners from starting correctly.
* Listener and Intercept are different concepts.
* Verify basic connectivity before troubleshooting HTTPS.
* Use layered troubleshooting rather than changing random settings.
* Avoid exposing proxy listeners unnecessarily.
