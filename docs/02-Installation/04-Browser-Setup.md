# Browser Setup

> **Module Progress**

```text id="r7w3mt"
████░░░░░ 4 / 9 Lessons
```

- For Burp Suite to observe browser traffic, the browser must send its web requests through Burp's proxy listener.

- There are two common approaches:

  ```text id="m4q8vn"
  Burp's Built-In Browser
  ```

or:

  ```text id="p7n3kx"
  Dedicated External Browser

  ↓

  Configured to Use Burp Proxy
  ```

- Both approaches are useful.

- The important concept is understanding how traffic reaches Burp.

---

## The Core Traffic Model

- Without Burp:

  ```text id="v5m9qw"
  Browser

  ↓

  Internet / Web Application
  ```

- With Burp:

  ```text id="n8q2wr"
  Browser

  ↓

  Burp Proxy Listener

  ↓

  Web Application
  ```

- The browser must know where the proxy listener exists.

- For a common local configuration:

  ```text id="k3p7mv"
  Host: 127.0.0.1

  Port: 8080
  ```

---

## Option 1 — Burp's Built-In Browser

- Modern Burp versions provide a browser that can be launched directly from Burp.

- This is often the simplest way to begin learning.

- Conceptually:

  ```text id="x6m2pk"
  Burp

  ↓

  Open Browser

  ↓

  Preconfigured Browser Environment
  
  ↓

  Traffic Flows Through Burp
  ```

- This reduces manual proxy configuration.

---

## Why the Built-In Browser Is Useful

- It is convenient for:

  * Initial setup verification.
  * Labs.
  * Quick testing.
  * Troubleshooting external-browser configuration.

- If traffic works in Burp's browser but not your external browser, that provides an important clue:

  ```text id="q9n4rv"
  Burp Core Setup

  Probably Working

  ↓
  
  External Browser Configuration

  Needs Investigation
  ```

- This makes the built-in browser useful as a troubleshooting baseline.

---

## Option 2 — Dedicated External Browser

- Many testers also use a dedicated Firefox or Chromium-based browser profile configured for Burp.

- The key word is:

  > **Dedicated**

- Avoid turning your everyday browsing environment into a permanent security-testing proxy environment.

---

## Why Use a Dedicated Profile?

- A dedicated profile helps isolate:

  * Proxy configuration.
  * Burp CA certificate trust.
  * Testing extensions.
  * Cookies.
  * Sessions.
  * Lab accounts.
  * Assessment-specific state.

- Conceptually:

  ```text id="r4m8qn"
  Everyday Browser Profile

  ↓

  Normal Browsing
  ```

and:

  ```text id="t7p2kx"
  Security Testing Profile

  ↓

  Burp Proxy

  ↓

  Authorized Targets
  ```

- Separation reduces confusion and accidental exposure.

---

## Manual Proxy Configuration

- For a common local setup, configure the browser's HTTP proxy to:

  ```text id="m5q9vw"
  127.0.0.1
  ```

- with port:

  ```text id="p3n7kr"
  8080
  ```

- The exact interface varies by browser and version.

- The conceptual requirement is:

  ```text id="x8m4qt"
  Browser Proxy Destination

  =

  Burp Listener Destination  
  ```
  
---

## HTTP and HTTPS Proxying

- Depending on browser configuration, you may see separate settings for:

  * HTTP proxy.
  * HTTPS proxy.
  * SOCKS proxy.

- Do not configure fields randomly.

- For normal Burp HTTP/HTTPS interception, ensure the browser's web traffic is routed to the Burp HTTP proxy listener according to the browser's current proxy configuration model.

---

## Do Not Confuse SOCKS With HTTP Proxy

- These are different proxy mechanisms.

- If Burp is configured as an HTTP proxy listener, pointing the browser's SOCKS configuration at it may cause unexpected failures.

- Think:

  ```text id="n6q2mv"
  Browser HTTP Proxy

  ↓

  Burp HTTP Proxy Listener
  ```

- not:

  ```text id="k9p4rx"
  Browser SOCKS Configuration

  ↓
  
  Assume It Is Equivalent
  ```

- Match the proxy type to the service you are using.

---

## Proxy Switching Extensions

- Browser extensions such as proxy switchers can make it easier to toggle between:

  ```text id="v2m8qw"
  Direct Connection
  ```

- and:

  ```text id="q5n3pk"
  Burp Proxy
  ```

- This is convenient when switching frequently between normal browsing and testing.

- However, proxy extensions introduce another configuration layer.

---

## Avoid Conflicting Proxy Configuration

- Suppose you configure:

  ```text id="r8m4nv"
  Browser Manual Proxy

  ↓

  127.0.0.1:8080
  ```

and also use a proxy-switching extension with a different configuration.

- Now you may not know which setting controls the traffic.

- Prefer a clear setup.

- For example:

  ```text id="t3q7mx"
  Approach A

  Browser Manual Proxy Only
  ```

- or:

  ```text id="p6n2kw"
  Approach B

  Proxy Extension Controls Switching
  ```

- Understand which mechanism is active.

---

## The Matching Rule

- Always compare:

  ```text id="m9q4vr"
  Burp Listener

  Address: 127.0.0.1

  Port: 8080
  ```

- with:

  ```text id="x5n8pt"
  Browser Proxy

  Address: 127.0.0.1

  Port: 8080
  ```

- If one side uses a different port:

  ```text id="q2m7kn"
  Browser → 8081

  Burp → 8080
  ```

the setup will fail unless another valid proxy exists on `8081`.

---

## First Verification — Use HTTP History

- Do not rely only on whether Intercept pauses a request.

- A better verification is:

  ```text id="n4p9mw"
  Open Browser

  ↓

  Visit Test Page

  ↓

  Check Burp Proxy HTTP History
  ```

- If the request appears in HTTP history, traffic is reaching Burp.

---

## Intercept ON vs OFF

- Remember:

  ```text id="v7m3qx"
  Intercept ON

  ↓

  Matching Requests May Pause
  ```

- while:

  ```text id="k5n8pr"
  Intercept OFF

  ↓

  Requests Normally Pass Through

  ↓

  Still May Appear in HTTP History
  ```

- Therefore:

  > A page loading normally while Intercept is OFF does not mean Burp is being bypassed.

- Check HTTP history.

---

## Controlled Interception Test

- To verify manual interception:

  ```text id="r3q9mv"
  Enable Intercept

  ↓

  Visit an Authorized Test Page

  ↓

  Request Pauses in Burp
  
  ↓

  Inspect Request

  ↓

  Forward
  
  ↓

  Page Continues Loading
  ```

- This confirms the interception workflow.

---

## If the Browser Cannot Load Anything

- Use this sequence:

  ```text id="p8m2kw"
  Is Burp Running?

  ↓

  Is Listener Enabled?

  ↓

  Does Browser Proxy Match Listener?

  ↓

  Is Correct Proxy Type Configured?

  ↓
  
  Is Another Proxy/VPN Interfering?

  ↓

  Does Burp's Built-In Browser Work?
  ```

- Do not immediately change certificate settings.

- A certificate cannot fix a browser that cannot reach the proxy at all.

---

## If HTTP Works but HTTPS Fails

- This is an important diagnostic distinction.

  ```text id="x6n4qt"
  HTTP Works

  ↓

  Proxy Connectivity Exists
  ```

- but:

  ```text id="m2p8rv"
  HTTPS Fails With Trust Error

  ↓

  Investigate CA Certificate Trust
  ```

- That is the focus of the next lesson.

---

## VPNs and Other Proxies

- Your environment may also include:

  * VPN software.
  * Corporate proxies.
  * System-wide proxies.
  * Privacy tools.
  * Browser extensions.
  * Security software.

- These can alter network behavior.

- When troubleshooting, simplify the path when possible.

  ```text id="q7m3nx"
  Browser

  ↓

  Burp

  ↓

  Target
  ```

- The fewer unknown routing layers, the easier the diagnosis.

---

## Dedicated Testing Sessions

- For professional or lab work, consider separating sessions by:

  * Browser profile.
  * User account.
  * Target environment.
  * Burp project.

- This helps prevent session confusion.

- For example:

  ```text id="t9p4kv"
  Testing Profile

  User A Session
  ```

- and:

  ```text id="n5m8qw"
  Separate Profile / Container

  User B Session  
  ```

- This can be particularly useful during authorization testing.

---

## Browser Setup Troubleshooting Matrix

| Observation                                | Likely Area to Investigate              |
| ------------------------------------------ | --------------------------------------- |
| Burp browser works, external browser fails | External browser proxy configuration    |
| Nothing loads after enabling proxy         | Listener/proxy mismatch or connectivity |
| HTTP works, HTTPS gives trust errors       | CA certificate trust                    |
| Traffic loads but does not pause           | Intercept state/rules                   |
| Traffic appears in HTTP history            | Browser is routing through Burp         |
| Wrong application/session appears          | Browser profile/session isolation       |

- Use observations to narrow the failing layer.

---

## Professional Browser Checklist

```text id="r6q2mp"
[ ] Dedicated testing browser/profile used.

[ ] Proxy destination matches Burp listener.

[ ] Proxy type is configured correctly.

[ ] No unknown proxy extension conflict exists.

[ ] Burp HTTP history receives traffic.

[ ] Intercept behavior is understood.

[ ] HTTP connectivity works.

[ ] HTTPS trust is configured separately.

[ ] Testing sessions are isolated when needed.
```

---

## Key Takeaways

* The browser must route traffic through Burp's proxy listener.
* Burp's built-in browser is the simplest starting point.
* A dedicated external browser profile provides useful isolation.
* Browser proxy address and port must match the Burp listener.
* HTTP proxy and SOCKS proxy are not interchangeable.
* Avoid conflicting manual and extension-based proxy settings.
* HTTP history is a reliable way to confirm traffic reaches Burp.
* Intercept OFF does not mean Burp is bypassed.
* If HTTP works but HTTPS fails, investigate certificate trust.
* Use the built-in browser as a troubleshooting baseline.
