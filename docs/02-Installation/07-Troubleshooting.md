# Troubleshooting

> **Module Progress**

```text id="r5w9qt"
███████░░ 7 / 9 Lessons
```

- Burp Suite setup problems become much easier to solve when troubleshooting follows a logical sequence.

- The most important rule is:

  > **Identify the first failing layer before changing configuration.**

- Avoid randomly changing proxy, certificate, listener, browser, and network settings simultaneously.

---

## The Troubleshooting Model

- Think of the environment as layers:

  ```text id="m8p3vn"  
  Burp Process

  ↓

  Proxy Listener

  ↓

  Browser Proxy Configuration
  
  ↓

  Browser → Burp Connectivity

  ↓

  HTTP Proxying
  
  ↓

  HTTPS / TLS Trust

  ↓
  
  Burp → Target Connectivity

  ↓

  Application Behavior
  ```

- Start from the top.

- Find the first layer that fails.

---

## Problem 1 — Burp Does Not Start

- Possible areas include:

  * Installation failure.
  * Runtime problems.
  * Corrupted installation.
  * Operating-system compatibility.
  * Insufficient system resources.

- Ask:

  ```text id="x4n7qw"
  Did Burp Install Successfully?

  ↓

  Does the Application Launch?

  ↓

  Is an Error Displayed?

  ↓

  Does a Clean Restart Change Anything?
  ```

- Do not troubleshoot browser proxy settings if Burp itself cannot run.

---

## Problem 2 — "Proxy Server Is Refusing Connections"

- This usually indicates that the browser cannot connect to the configured proxy.

- Check:

  ```text id="v2k9mr"
  Is Burp Running?

  ↓

  Is Proxy Listener Enabled?

  ↓

  What Address Is It Using?

  ↓

  What Port Is It Using?
  
  ↓

  Does Browser Match Exactly?

  ↓

  Is Something Else Using the Port?
  ```

- A common expected pairing is:

  ```text id="p6r2nk"
  Burp Listener

  127.0.0.1:8080
  ```

- and:

  ```text id="t9m4vx"
  Browser Proxy

  127.0.0.1:8080
  ```

---

## Problem 3 — Port 8080 Is Already in Use

- Another process may already own the listener address and port.

- Symptoms may include:

  * Listener failing to start.
  * Bind errors.
  * Unexpected service responses.

- On Linux, useful diagnostic commands may include:

  ```bash id="w3q8pr"
  ss -ltn | grep 8080
  ```

- or:

  ```bash id="n7k2qm"
  lsof -i :8080
  ```

- Determine what is actually using the port.

---

## Port Conflict Solution

- Either:

  ```text id="y5m9rv"
  Stop / Reconfigure Conflicting Process
  ```

- or change Burp to another available port:

  ```text id="k4p8nw"
  Burp → 127.0.0.1:8081
  ```

- Then update the browser:

  ```text id="r8v3qt"
  Browser → 127.0.0.1:8081
  ```

- Both sides must match.

---

## Problem 4 — Browser Loads Normally but Burp Sees Nothing

- This is an important scenario.

- If websites load but:

  ```text id="m2n7pk"
  Proxy → HTTP History

  Shows Nothing
  ```

the browser may be bypassing Burp entirely.

- Investigate:

  * Browser proxy disabled.
  * Wrong proxy profile selected.
  * Proxy-switching extension disabled.
  * Browser using system proxy unexpectedly.
  * Browser using a different profile.
  * Manual proxy configuration overridden.

- The fact that a page loads does **not** prove it passed through Burp.

- HTTP history provides stronger evidence.

---

## Problem 5 — Google Loads Normally but Burp Sees Nothing

- Do not conclude:

  > "Burp must be working because Google loaded."

- Instead:

  ```text id="q6m2kt"
  Google Loads

  +

  No Request in HTTP History

  ↓

  Traffic Probably Did Not Pass Through Burp
  ```

- Verify the browser's active proxy path.

---

## Problem 6 — Browser Page Hangs

- First check:

  ```text id="c8cvxe"
  Proxy → Intercept
  ```

- If Intercept is ON, the request may simply be waiting for you.

- Conceptually:

  ```text id="lrj7kb"
  Browser Sends Request

  ↓

  Burp Pauses It
  
  ↓

  Browser Waits

  ↓

  Tester Forwards Request

  ↓

  Page Continues
  ```

- This is expected behavior.

---

## Multiple Requests May Be Waiting

- Modern pages often generate many requests.

- After forwarding one request, another may immediately appear.

- If your objective is normal browsing while recording traffic:

  ```text id="25kn1j"
  Turn Intercept OFF

  ↓

  Traffic Continues Automatically

  ↓

  Review HTTP History
  ```

---

## Problem 7 — No Traffic in HTTP History

- Use:

  ```text id="jc1tfb"
  Burp Running?

  ↓

  Listener Enabled?

  ↓

  Browser Proxy Enabled?

  ↓

  Address Matches?

  ↓

  Port Matches?

  ↓

  Correct Proxy Type?

  ↓

  Conflicting Extension?

  ↓

  Built-In Browser Works?
  ```

- If Burp's built-in browser works, focus on external browser configuration.

---

## Problem 8 — HTTP Works but HTTPS Fails

- This is a strong diagnostic clue.

  ```text id="cob62g"
  HTTP Through Burp ✓

  HTTPS Through Burp ✗
  ```

- Basic proxy connectivity probably exists.

- Investigate:

  * Burp CA trust.
  * Wrong certificate store.
  * Wrong browser profile.
  * Special TLS behavior.
  * Another TLS-intercepting layer.

- Do not immediately rebuild the proxy listener.

---

## Problem 9 — Certificate Warning

- Possible causes include:

  ```text id="hbjmc9"
  Burp CA Not Installed

  OR

  Installed in Wrong Trust Store

  OR

  Wrong Browser Profile
  
  OR

  Certificate Trust Not Enabled Correctly

  OR

  Special Application TLS Behavior
  ```

- Verify which browser/profile is actually being used.

---

## Problem 10 — SSL/TLS Protocol Errors

- Errors such as TLS handshake failures do not always mean:

  > "The CA certificate is missing."

- Possible causes can include:

  * Incorrect proxy type.
  * TLS incompatibility.
  * Another proxy layer.
  * VPN interference.
  * Special target configuration.
  * Certificate pinning in specialized clients.

- Use evidence from earlier layers.

---

## Problem 11 — SOCKS Misconfiguration

- Do not assume:

  ```text id="n0clgn"
  SOCKS Proxy

  =

  HTTP Proxy
  ```

- If Burp is listening as an HTTP proxy, configure the browser accordingly.

- Accidentally pointing SOCKS settings at an HTTP proxy listener can create confusing connection failures.

---

## Problem 12 — Conflicting Manual Proxy and Extension

- Suppose Firefox has:

  ```text id="h7m4qx"
  Manual Proxy

  127.0.0.1:8080
  ```

while a proxy-switching extension points somewhere else.

- Now troubleshooting becomes ambiguous.

- Choose a clear source of truth:

  ```text id="k8n3pr"
  Manual Browser Configuration
  ```

- or:

  ```text id="amzq4j"
  Proxy-Switching Extension
  ```

- Know which one controls traffic.

---

## Problem 13 — Built-In Browser Works, Firefox Does Not

- This is valuable evidence.

  ```text id="uxt2rk"
  Burp Built-In Browser ✓

  External Firefox ✗
  ```

- Likely areas:

  * Firefox proxy settings.
  * Firefox certificate trust.
  * Firefox profile.
  * Proxy extension.
  * Browser-specific networking behavior.

- Burp itself is less likely to be the primary problem.

---

## Problem 14 — Repeater Cannot Reach Target

- Ask:

  ```text id="mv3e52"
  Does Browser Traffic Through Burp Work?

  ↓

  Can Burp Resolve Target?

  ↓

  Can Burp Establish Connection?

  ↓  

  Is VPN Required?

  ↓

  Is Upstream Proxy Required?

  ↓

  Is Target Actually Reachable?
  ```

- Browser proxy configuration is not the only network path Burp may need.

- Burp itself must reach the target.

---

## Problem 15 — VPN Changes Everything

- Some labs and assessments require a VPN.

- VPN software can affect:

  * Routing.  
  * DNS.
  * Interface selection.
  * Target reachability.

- Think:

  ```text id="c4u08e"
  Browser

  ↓

  Burp

  ↓

  VPN / Network Route

  ↓

  Target
  ```

- If a target is reachable only through a VPN, verify that Burp's outbound traffic follows the required route.

---

## Problem 16 — Target Works Outside Burp but Not Through Burp

- Compare:

  ```text id="c1etmn"
  Browser → Target

  ✓
  ```

- with:

  ```text id="t3o4df"
  Browser → Burp → Target

  ✗
  ```

- The additional failing layer is Burp.

- Investigate:

  * Burp outbound connectivity.
  * Upstream proxy requirements.
  * TLS behavior.
  * DNS behavior.
  * Scope-specific network requirements.

---

## Problem 17 — Target Works Through Burp but Not With Intercept ON

- Check whether requests are simply paused.

  ```text id="2uyij3"
  Intercept ON

  ↓

  Request Waiting

  ↓

  Forward / Drop Decision Required
  ```

- This is usually workflow behavior, not a connectivity failure.

---

## Problem 18 — Unexpected Behavior After Installing Extensions

- Extensions can modify or influence Burp behavior.

- If a problem begins after an extension change:

  ```text id="k5oblv"
  Known Working State

  ↓

  Extension Added

  ↓

  Problem Appears
  ```  

consider testing from a clean environment or disabling the relevant extension.

- Do not assume core Burp is responsible.

---

## Problem 19 — Everything Became Confusing

- Return to a minimal baseline.

```text id="mvnv8h"
Fresh Burp Session

↓

Default Configuration

↓

Known Listener

↓

Burp Built-In Browser

↓

Simple Test Destination
```

- Verify this first.

- Then add:

  ```text id="e8j8hc"
  External Browser

  ↓

  Custom Proxy

  ↓

  Extensions

  ↓

  VPN

  ↓
  
  Advanced Configuration
  ```

- one layer at a time.

---

## The Universal Troubleshooting Decision Tree

- Use this whenever setup fails:

  ```text id="txb4vz"
  Does Burp Start?  
  │
  ├── No → Installation / Runtime  
  │
  └── Yes
       ↓
  Is Listener Active?
  │
  ├── No → Listener / Port Conflict
  │
  └── Yes
       ↓
  Does Browser Match Listener?  
  │
  ├── No → Browser Proxy Configuration  
  │
  └── Yes
       ↓
  Does Traffic Appear in HTTP History?  
  │
  ├── No → Routing / Proxy Configuration  
  │
  └── Yes
       ↓
  Does HTTP Work?
  │
  ├── No → Proxy / Network Connectivity  
  │
  └── Yes
       ↓
  Does HTTPS Work?
  │
  ├── No → CA / TLS Investigation
  │
  └── Yes
       ↓
  Does Target-Specific Workflow Work?
  │
  ├── No → VPN / DNS / Target / Special TLS
  │
  └── Yes → Environment Operational
  ```

---

## Troubleshooting Journal

- Record:

  ```text id="ql1edb"
  Problem:

  Expected Behavior:

  Observed Behavior:

  Burp Version:

  Browser/Profile:

  Listener Address:

  Listener Port:

  HTTP History Traffic:

  HTTP Works:

  HTTPS Works:

  Built-In Browser Works:

  External Browser Works:
  
  VPN Active:
  
  Extensions Active:

  Last Configuration Change:

  Current Hypothesis:

  Next Controlled Test:
  ```

- This prevents repeated random changes.

---

## Golden Troubleshooting Rules

1. Start with the first failing layer.
2. Change one thing at a time.
3. Verify after every meaningful change.
4. Use HTTP history as evidence of proxy routing.
5. Separate connectivity from interception.
6. Separate HTTP proxying from HTTPS trust.
7. Use the built-in browser as a baseline.
8. Consider VPNs, extensions, and other proxies.
9. Return to defaults when configuration drift becomes unclear.
10. Diagnose before reinstalling.

---

## Key Takeaways

* Most Burp setup problems are configuration problems, not installation problems.
* "Proxy refused connection" usually points toward listener/connectivity issues.
* A page loading does not prove traffic passed through Burp.
* HTTP history is one of the best routing checks.
* A hanging page may simply be waiting in Intercept.
* HTTP working while HTTPS fails points toward TLS-specific investigation.
* SOCKS and HTTP proxies are different.
* VPNs and extensions add troubleshooting layers.
* A clean baseline helps isolate complex problems.
* Systematic troubleshooting is faster than random configuration changes.

