# Proxy Listeners and Settings

## Overview

- Burp Proxy must receive browser or client traffic before it can intercept, inspect, or modify that traffic.

- A **proxy listener** is the network endpoint where Burp waits for incoming proxy connections.

- A listener is primarily defined by:

  * A listening address.
  * A listening port.
  * Listener-specific behavior and options.

- A common local configuration is:

  ```text
  Address: 127.0.0.1
  Port: 8080
  ```

- Understanding listeners and Proxy settings is essential for:

  * Reliable browser-to-Burp connectivity.
  * HTTPS interception.
  * Testing multiple clients.
  * Troubleshooting proxy failures.
  * Avoiding unintended network exposure.
  * Building controlled testing environments.

---

## Learning Objectives

- By the end of this lesson, you will be able to:

  * Explain what a Burp Proxy listener does.
  * Understand listening addresses and ports.
  * Configure a browser to use a Burp listener.
  * Distinguish loopback-only listeners from externally reachable listeners.
  * Understand listener binding conflicts.
  * Configure multiple listeners when necessary.
  * Recognize common Proxy settings.
  * Understand invisible proxying conceptually.
  * Troubleshoot common listener and connectivity problems.
  * Configure listeners safely during authorized testing.

---

## Core Mental Model

```text
Browser / Client
      │
      │ Proxy Configuration
      ▼
IP Address : Port
      │
      ▼
Burp Proxy Listener
      │
      ▼
Burp Processes Request
      │
      ▼
Target Application
```

- The client must know where Burp is listening.

- Burp must successfully bind to that address and port.

- Both sides must agree on the connection path.

```text
Client Proxy Destination

=

Burp Listener Address + Port
```

- If they do not match, traffic will not pass through Burp correctly.

---

## What Is a Proxy Listener?

- A Proxy listener is a network socket opened by Burp to accept incoming proxy connections.

- Conceptually:

  ```text
  Burp

  ↓

  Listen on:

  127.0.0.1:8080
  ```

- A browser configured with:

  ```text
  HTTP Proxy: 127.0.0.1
  Port: 8080
  ```

  can send its traffic to that listener.

- Burp then processes the traffic according to the configured Proxy behavior.

---

## Default Local Listener

- A common Burp configuration uses:

  ```text
  127.0.0.1:8080
  ```

- `127.0.0.1` is a loopback address.

- This means the listener is intended for connections originating from the same machine.

```text
Browser
Same Machine
    │
    ▼
127.0.0.1:8080
    │
    ▼
Burp
```

- This is generally the preferred configuration for ordinary local browser testing.

---

## Understanding Listening Addresses

- The listening address determines which network interface or interfaces can accept connections.

- Common configurations include:

  * Loopback only.
  * A specific local interface.
  * All interfaces.

### Loopback Only

```text
127.0.0.1
```

- Suitable when Burp and the browser are running on the same machine.

- This minimizes unnecessary network exposure.

### Specific Interface

- Burp can listen on a specific network interface when another authorized device must connect to it.

- Example use cases include:

  * Testing a mobile application from a lab device.
  * Testing another virtual machine.
  * Routing an authorized client through Burp.

### All Interfaces

- Binding to all interfaces can make the listener reachable through multiple network interfaces.

- This should be used only when necessary and with an understanding of the exposure created.

- Do not expose a Burp listener broadly without a clear testing requirement and appropriate network controls.

---

## Understanding Ports

- A port identifies the specific network service accepting connections on an address.

- A common Burp listener port is:

  ```text
  8080
  ```

- The client configuration must use the same port.

```text
Burp Listener
127.0.0.1:8080

Browser Proxy
127.0.0.1:8080

Result:
Connection can reach Burp
```

- If the browser uses a different port:

```text
Burp:
127.0.0.1:8080

Browser:
127.0.0.1:8081
```

- The browser will not reach that Burp listener unless another listener exists on `8081`.

---

## Port Conflicts

- Only one process can normally bind to the same address and port combination at a time.

- If another application is already using:

  ```text
  127.0.0.1:8080
  ```

  Burp may be unable to start its listener there.

- Symptoms may include:

  * Listener startup failure.
  * Browser proxy errors.
  * Traffic reaching another application.
  * Burp receiving no requests.

- On Linux, you can inspect listening services with commands such as:

  ```bash
  ss -ltn
  ```

- To focus on port `8080`:

  ```bash
  ss -ltn | grep ':8080'
  ```

- You can also inspect which process is using the port:

  ```bash
  sudo lsof -i :8080
  ```

- If the port is occupied, either:

  * Stop the conflicting service when appropriate.
  * Change the conflicting service's configuration.
  * Configure Burp to use another available port.

---

## Browser Proxy Configuration

- For a local Burp listener:

  ```text
  127.0.0.1:8080
  ```

- The browser proxy settings should point to the same destination.

- Conceptually:

```text
HTTP Proxy:
127.0.0.1

Port:
8080
```

- HTTPS traffic also needs to pass through Burp for HTTPS interception.

- Depending on the browser or proxy-management extension, this may be configured through:

  * A shared proxy setting for HTTP and HTTPS.
  * Separate HTTP and HTTPS proxy fields.
  * A browser extension profile.
  * Burp's built-in browser.

---

## Burp's Built-In Browser

- Burp provides a built-in browser that is preconfigured to work with Burp.

- This can reduce setup problems involving:

  * Manual proxy configuration.
  * Certificate installation.
  * Browser profile conflicts.
  * Proxy extensions.

- The built-in browser is useful when you want a dedicated testing browser with minimal configuration.

- An external browser remains useful when testing:

  * Existing browser profiles.
  * Specific extensions.
  * Application behavior tied to a particular browser environment.
  * Custom proxy-routing configurations.

---

## Multiple Proxy Listeners

- Burp can use multiple listeners when a testing environment requires different connection paths.

- Example:

```text
Listener 1
127.0.0.1:8080
    │
    └── Local testing browser

Listener 2
192.168.x.x:8081
    │
    └── Authorized lab device
```

- Multiple listeners can help separate:

  * Local browser traffic.
  * Mobile testing traffic.
  * Virtual-machine traffic.
  * Different lab environments.

- Do not create additional listeners unless they serve a clear purpose.

- Simpler configurations are easier to troubleshoot and safer to manage.

---

## Listener Binding

- A listener must successfully bind to its configured address and port.

- If binding succeeds:

  ```text
  Listener Running
  ```

- If binding fails:

  ```text
  No Active Listener
        ↓
  Client Cannot Reach Burp
  ```

- Common causes include:

  * Port already in use.
  * Invalid listening address.
  * Network-interface changes.
  * Permission or operating-system restrictions.
  * Misconfigured listener settings.

---

## Proxy Settings

- Burp's Proxy settings control more than the listener itself.

- Depending on the Burp version and configuration, Proxy-related settings may include:

  * Proxy listeners.
  * Request interception rules.
  * Response interception rules.
  * WebSocket interception rules.
  * Match and Replace rules.
  * TLS-related behavior.
  * Request and response handling.
  * Invisible proxying options.

- Settings should be changed deliberately.

- Complex configurations can modify traffic in ways that are easy to forget during testing.

---

## Request Interception Rules

- Interception rules determine which requests Burp pauses when interception is enabled.

- Without useful rules, you may intercept large amounts of irrelevant traffic.

- Rules can help focus interception based on characteristics such as:

  * Target scope.
  * File type.
  * Host.
  * URL.
  * HTTP method.

- A common professional approach is:

  ```text
  Define Scope
      ↓
  Configure Useful Interception Rules
      ↓
  Intercept Relevant Traffic
  ```

- This reduces noise and improves workflow efficiency.

---

## Response Interception

- Burp can also intercept responses before they reach the client.

- This can be useful when investigating:

  * Client-side behavior.
  * Response headers.
  * Redirects.
  * Cookies.
  * Security controls implemented in the browser.
  * Server-provided configuration.

- Response interception is usually enabled only when needed because intercepting every response can significantly interrupt browsing.

---

## Match and Replace

- Match and Replace rules can automatically modify traffic passing through the Proxy.

- Examples of controlled uses include:

  * Adding a test header.
  * Replacing a request header.
  * Modifying a predictable value.
  * Testing how an application behaves with a consistent transformation.

- Conceptually:

```text
Original Request
      ↓
Match Rule
      ↓
Automatic Replacement
      ↓
Modified Request
```

- Automatic modifications can create confusing results if forgotten.

- Document active rules and disable them when they are no longer required.

---

## Invisible Proxying

- Some clients do not support normal explicit proxy configuration.

- Burp provides invisible proxying capabilities for specialized testing scenarios.

- Conceptually, an explicit proxy-aware client knows it is communicating through a proxy.

```text
Proxy-Aware Client
      ↓
Burp
      ↓
Server
```

- A non-proxy-aware client may require a different traffic-routing arrangement.

```text
Client
      ↓
Traffic Redirected
      ↓
Burp Invisible Proxy Listener
      ↓
Server
```

- Invisible proxying is an advanced configuration.

- It should not be enabled as a generic troubleshooting option.

- Incorrect configuration can cause:

  * Connection failures.
  * TLS problems.
  * Unexpected routing behavior.
  * Confusing request formatting.

---

## HTTPS and Listener Configuration

- A working TCP connection to Burp does not automatically mean HTTPS interception is fully configured.

- HTTPS interception typically involves:

```text
Client
    ↓
Burp Listener
    ↓
TLS Interception
    ↓
Target Server
```

- The client must trust Burp's CA certificate for normal certificate validation to succeed.

- Common HTTPS problems include:

  * Burp listener not running.
  * Browser not configured to use Burp.
  * Burp CA certificate not trusted.
  * Certificate installed in the wrong browser profile.
  * Proxy settings applying only to HTTP.
  * TLS-related application restrictions.

- Troubleshoot these layers separately rather than changing several settings simultaneously.

---

## Listener Troubleshooting Workflow

- When traffic is not reaching Burp, use a structured process.

```text
1. Is Burp Running?
        ↓
2. Is the Proxy Listener Running?
        ↓
3. Is the Listener Address Correct?
        ↓
4. Is the Listener Port Correct?
        ↓
5. Does the Browser Use the Same Address and Port?
        ↓
6. Is Another Process Using the Port?
        ↓
7. Does Plain HTTP Traffic Reach Burp?
        ↓
8. If HTTP Works, Is the Problem Specific to HTTPS?
        ↓
9. Check Certificate / TLS Configuration
```

- This isolates the failing layer.

---

## Problem: Proxy Server Refusing Connections

- A browser may display an error indicating that the proxy server is refusing connections.

- Common causes include:

  * Burp is not running.
  * The listener is disabled.
  * The browser is using the wrong port.
  * The browser is using the wrong address.
  * Burp failed to bind to the configured port.

- Verify:

  ```text
  Burp Listener:
  127.0.0.1:8080

  Browser Proxy:
  127.0.0.1:8080
  ```

- Then confirm that the listener is active.

---

## Problem: Browser Loads Normally but Burp Shows No Traffic

- Possible causes include:

  * Browser proxy configuration is disabled.
  * A proxy extension is using the wrong profile.
  * Traffic is bypassing the proxy.
  * The browser is using a different network configuration.
  * You are checking the wrong Burp project or history view.

- Test with a simple known request and verify whether it appears in HTTP history.

---

## Problem: HTTP Works but HTTPS Fails

- If plain HTTP traffic reaches Burp but HTTPS does not work correctly, investigate the TLS layer.

- Check:

  * Whether HTTPS is routed through the proxy.
  * Whether the Burp CA certificate is installed.
  * Whether the certificate is trusted.
  * Whether the application uses certificate pinning or another TLS restriction.

- Do not assume the listener itself is broken if HTTP traffic works.

---

## Problem: Pages Stop Loading with Intercept ON

- This is often expected behavior rather than a listener failure.

```text
Browser Sends Request
      ↓
Burp Intercepts Request
      ↓
Request Waits
      ↓
Browser Appears to Hang
```

- Either:

  * Forward the intercepted request.
  * Drop it intentionally.
  * Turn interception off.

- The listener can be functioning correctly even while the browser appears to wait.

---

## Problem: Wrong Proxy Type

- Some browser configurations distinguish between:

  * HTTP proxy.
  * HTTPS proxy.
  * SOCKS proxy.

- Burp's standard explicit Proxy listener should be configured according to the browser's HTTP/HTTPS proxy behavior.

- Accidentally routing traffic as SOCKS when that is not the intended setup can cause connection problems.

- Verify the proxy type as part of troubleshooting.

---

## External Device Testing

- Sometimes Burp runs on one machine while traffic originates from another authorized device.

```text
Mobile / VM / Lab Device
        │
        ▼
Tester Machine IP : Listener Port
        │
        ▼
Burp
        │
        ▼
Target
```

- This may require:

  * Binding Burp to an appropriate network interface.
  * Configuring the device to use the tester machine as its proxy.
  * Allowing the connection through local firewall rules.
  * Installing the Burp CA certificate where appropriate.
  * Ensuring both devices can communicate over the network.

- Only expose the listener to networks and devices required for the authorized assessment.

---

## Security Considerations

- A Burp listener can process sensitive traffic.

- Avoid unnecessary exposure.

- Prefer:

  ```text
  Loopback Only
  ```

  when testing locally.

- When remote-device access is necessary:

  * Bind only where required.
  * Use trusted lab networks.
  * Restrict firewall access where appropriate.
  * Remove unnecessary listeners after testing.
  * Avoid exposing Burp listeners to untrusted networks.

---

## Minimal Configuration Principle

- Use the simplest configuration that supports the test.

```text
Simple Local Test

Browser
    ↓
127.0.0.1:8080
    ↓
Burp
```

- Add complexity only when required.

- Unnecessary complexity may include:

  * Multiple listeners.
  * Invisible proxying.
  * Automatic Match and Replace rules.
  * Broad network bindings.
  * Complex interception rules.

- Simpler configurations are easier to understand, reproduce, and troubleshoot.

---

## Professional Workflow

```text
Start Burp
    ↓
Verify Listener
    ↓
Configure Client Proxy
    ↓
Test Basic Connectivity
    ↓
Verify HTTP History
    ↓
Verify HTTPS Interception
    ↓
Define Scope
    ↓
Configure Interception Rules
    ↓
Begin Authorized Testing
```

- Validate the environment before beginning vulnerability testing.

- This prevents configuration problems from being mistaken for application behavior.

---

## Example Configuration

- Local browser testing:

  ```text
  Burp Listener

  Address:
  127.0.0.1

  Port:
  8080
  ```

- Browser:

  ```text
  HTTP Proxy:
  127.0.0.1

  Port:
  8080

  HTTPS:
  Routed through the same intended Burp proxy configuration
  ```

- Expected result:

  ```text
  Browser Request
        ↓
  Burp Proxy
        ↓
  HTTP History
        ↓
  Target Server
  ```

---

## Troubleshooting Example

- Suppose the browser reports:

  ```text
  The proxy server is refusing connections
  ```

- Investigate systematically.

### Step 1 — Confirm Burp Is Running

- Verify that Burp Suite is open.

### Step 2 — Confirm the Listener

- Verify that a listener exists and is running on:

  ```text
  127.0.0.1:8080
  ```

### Step 3 — Confirm Browser Settings

- Verify that the browser points to:

  ```text
  127.0.0.1
  Port 8080
  ```

### Step 4 — Check Port Usage

- On Linux:

  ```bash
  ss -ltn | grep ':8080'
  ```

- If necessary:

  ```bash
  sudo lsof -i :8080
  ```

### Step 5 — Test Again

- Send a simple request and check Burp's HTTP history.

- Change only one configuration variable at a time.

---

## Common Mistakes

* Configuring the browser to use a different port from Burp.

* Assuming Burp is listening simply because the application is open.

* Binding a listener broadly when loopback-only access is sufficient.

* Enabling invisible proxying without understanding why it is needed.

* Forgetting that another process may already use port `8080`.

* Changing several proxy settings simultaneously during troubleshooting.

* Forgetting active Match and Replace rules.

* Confusing an intercepted request waiting in Burp with a failed network connection.

* Assuming every HTTPS problem is caused by the listener.

* Leaving unnecessary externally reachable listeners active after testing.

---

## Professional Tips

* Verify the listener before troubleshooting the browser.

* Keep local listeners bound to loopback unless another device genuinely needs access.

* Record non-default listener configurations in engagement notes.

* Use separate listeners only when they provide a clear operational benefit.

* Troubleshoot HTTP connectivity before diagnosing HTTPS certificate problems.

* Check active automatic traffic-modification rules when application behavior appears inconsistent.

* Change one setting at a time so the cause of a problem remains observable.

* Restore temporary Proxy configuration changes after completing the relevant test.

---

## Field Notes

- Many apparent Burp failures are configuration mismatches rather than software failures.

- Think in layers:

```text
Client Configuration

↓

Network Connection

↓

Burp Listener

↓

Proxy Processing

↓

TLS

↓

Target Application
```

- Identify which layer fails before changing configuration.

- A disciplined troubleshooting process is faster than repeatedly changing random settings.

---

## Practice Tasks

1. Open Burp Suite and locate the Proxy listener configuration.

2. Identify:

   * Listening address.
   * Listening port.
   * Whether the listener is running.

3. Configure a browser to use the listener.

4. Visit an authorized test page and confirm that the request appears in HTTP history.

5. Turn Intercept ON and observe the request waiting in Burp.

6. Forward the request.

7. Turn Intercept OFF and confirm that traffic still appears in HTTP history.

8. Change the listener to another available local port in a lab environment.

9. Update the browser to use the new port.

10. Verify that traffic works again.

11. Restore the original configuration.

12. Review active Proxy settings and identify any automatic traffic-modification rules.

---

## Investigation Exercise

- Scenario:

  ```text
  Burp Listener:
  127.0.0.1:8080

  Browser:
  127.0.0.1:8081
  ```

- Determine:

  * Why the browser cannot reach the intended listener.
  * Which configuration should be corrected.
  * How you would verify the fix.

- Then consider:

  ```text
  Browser:
  127.0.0.1:8080

  Burp Listener:
  Disabled
  ```

- Explain why matching address and port values are not sufficient when the listener itself is inactive.

---

## Interview Questions

1. What is a Burp Proxy listener?

2. What information defines a listener?

3. Why is `127.0.0.1:8080` commonly used for local Burp testing?

4. What is the difference between binding to loopback and binding to all interfaces?

5. Why might Burp fail to start a listener?

6. How would you determine whether another process is using port `8080`?

7. Why can a browser appear to hang when Intercept is ON?

8. What should you investigate if HTTP works through Burp but HTTPS fails?

9. Why should externally reachable listeners be configured carefully?

10. When might multiple Proxy listeners be useful?

11. What are request interception rules?

12. What risks can forgotten Match and Replace rules create?

13. What is invisible proxying conceptually?

14. Why should invisible proxying not be enabled as a generic troubleshooting step?

15. How would you systematically troubleshoot a browser that cannot connect through Burp?

---

## Quick Revision

```text
Proxy Listener
=
Address + Port
```

```text
Typical Local Configuration

127.0.0.1:8080
```

```text
Client Proxy Configuration
Must Match
Burp Listener
```

```text
Local Testing

Prefer Loopback
```

```text
No Traffic?

Check:
Burp
→ Listener
→ Address
→ Port
→ Browser Proxy
→ Port Conflict
```

```text
HTTP Works
HTTPS Fails

Investigate:
HTTPS Proxy Routing
+
CA Trust
+
TLS Behavior
```

```text
Browser Appears Frozen

Check:
Intercept ON
```

```text
Complex Configuration

Use Only When Required
```

---

## Summary

- A Proxy listener is the network endpoint through which client traffic enters Burp.

- Reliable interception depends on the client and Burp using compatible address and port settings.

- Loopback listeners are generally appropriate for local testing, while externally reachable listeners should be enabled only when required.

- Proxy settings can also control interception rules, response interception, Match and Replace behavior, and advanced routing features.

- When problems occur, troubleshoot systematically from the client configuration through the listener, network connection, TLS layer, and target application.

- A well-understood Proxy configuration provides a stable foundation for every later Burp Suite testing workflow.
