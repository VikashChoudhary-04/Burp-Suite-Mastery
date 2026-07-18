# Verifying Your Environment

> **Module Progress**

```text id="r7w4mt"
██████░░░ 6 / 9 Lessons
```

- Installing Burp, configuring a proxy, and trusting the CA certificate are separate tasks.

- Before beginning security testing, verify that the entire environment works as one system.

- A reliable verification process should answer:

  > **Can traffic travel from the browser through Burp to the target and back correctly?**

---

## The End-to-End Model

- Your basic environment is:
  
  ```text id="m5q8vn"
  Testing Browser

  ↓

  Burp Proxy Listener

  ↓

  Burp Suite

  ↓
  
  Authorized Web Application

  ↓

  Burp Suite

  ↓

  Testing Browser
  ```

- Each connection in this chain must work.

---

## Why Verify in Stages?

- Suppose HTTPS fails.

- Without staged verification, you might change:

  * Listener settings.
  * Browser proxy settings.
  * Certificates.
  * TLS options.
  * Extensions.

all at once.

- That creates more uncertainty.

- Instead:

  ```text id="p8n3kx"
  Verify One Layer

  ↓

  Confirm It Works

  ↓

  Move to Next Layer
  ```  

- This creates a known-good baseline.

---

## Test 1 — Burp Starts Correctly

- Verify:

  ```text id="v4m9qw"
  [ ] Burp launches normally.

  [ ] Project starts successfully.

  [ ] Main workspace is accessible.

  [ ] No unexpected startup error blocks usage.
  ```

- If Burp itself is not functioning, stop here and resolve that first.

---

## Test 2 — Proxy Listener Exists

- Confirm the intended listener.

- A common local configuration is:

  ```text id="n7q2wr"
  127.0.0.1:8080
  ```

- Verify:

  ```text id="k3p8mv"
  [ ] Listener exists.

  [ ] Listener is enabled.

  [ ] Bind address is intentional.

  [ ] Port is known.
  ```

- Do not assume the default is active—verify it.

---

## Test 3 — Browser Proxy Matches

- Compare both sides.

### Burp

```text id="x6m4pk"
Address: 127.0.0.1
Port: 8080
```

### Browser

```text id="q9n2rv"
Proxy: 127.0.0.1
Port: 8080
```

- The settings should match.

- If they do not, correct the mismatch before continuing.

---

## Test 4 — Verify Traffic in HTTP History

- Use an authorized test destination.

- For basic connectivity testing, a harmless public test site such as:

  ```text id="r4m8qn"
  https://example.com
  ```

may be used when appropriate.

- Open the page through the testing browser.

- Then inspect:

  ```text id="t7p2kx"
  Proxy

  ↓

  HTTP History
  ```

- Look for the request.

---

## What HTTP History Proves

- If the request appears:

  ```text id="m5q9vw"
  Browser

  ↓

  Successfully Sent Traffic to Burp
  ```

- This is stronger evidence of correct proxy routing than simply observing that a page loaded.

---

## Test 5 — Intercept OFF

- Set interception to OFF.

- Then visit or refresh the test page.

- Expected behavior:

  ```text id="p3n7kr"
  Browser Sends Request
  
  ↓

  Burp Receives It

  ↓

  Request Passes Without Manual Pause

  ↓

  Page Loads

  ↓

  Request Appears in HTTP History  
  ```  

- This verifies transparent proxy flow through Burp.

---

## Test 6 — Intercept ON

- Enable interception.

- Generate a new request.

- Expected:

  ```text id="n6q2mv"
  Browser Sends Request

  ↓

  Burp Pauses Matching Request

  ↓

  Request Visible in Intercept

  ↓

  Tester Clicks Forward

  ↓

  Request Continues

  ↓

  Page Loads
  ```

- This confirms manual interception works.

---

## Important Observation

- When Intercept is ON, a browser page may appear to hang.

- This may simply mean:

  ```text id="k9p4rx"
  Request Is Waiting in Burp
  ```

- Check the Intercept view before assuming the network is broken.

---

## Test 7 — Verify HTTPS

- Visit an HTTPS test page through the configured testing browser.

- Verify:

  ```text id="v2m8qw"
  [ ] Page loads.

  [ ] No unexpected certificate warning appears.

  [ ] HTTPS request appears in HTTP history.

  [ ] Request and response can be inspected.
  ```

- This confirms basic TLS interception is working.

---

## Test 8 — Send a Request to Repeater

- Choose a harmless request from your authorized test environment.

- Send it to Repeater.

- Conceptually:

  ```text id="q5n3pk"
  HTTP History

  ↓

  Select Request

  ↓

  Send to Repeater

  ↓

  Open Repeater

  ↓  

  Send Request

  ↓

  Receive Response
  ```

- This verifies that captured traffic can move into a manual testing workflow.

---

## Why Repeater Is Part of Verification

- Burp is not useful merely because traffic appears in HTTP history.

- You also want to confirm:

  ```text id="r8m4nv"
  Capture

  ↓

  Transfer

  ↓

  Replay

  ↓

  Receive Response
  ```

- This validates the basic workflow used throughout later modules.

---

## Test 9 — Intercept Toggle Understanding

- Verify that you can intentionally switch between:

  ```text id="t3q7mx"
  Intercept ON

  ↓

  Pause Matching Traffic
  ```

- and:

  ```text id="p6n2kw"
  Intercept OFF

  ↓

  Allow Traffic to Pass
  ```

- You should always know which state Burp is in.

---

## Test 10 — Disable the Proxy

- Temporarily return the testing browser to its normal direct connection mode.

- Verify that you understand how to switch between:

  ```text id="m9q4vr"
  Browser

  ↓

  Burp
  ```

- and:

  ```text id="x5n8pt"
  Browser

  ↓

  Direct Connection
  ```

- This is particularly useful when using a dedicated profile or proxy-switching extension.

---

## Full Verification Sequence

- Use this order:

  ```text id="q2m7kn"
  Burp Launches

  ↓

  Listener Enabled

  ↓

  Browser Matches Listener

  ↓

  Traffic Appears in HTTP History
  
  ↓

  Intercept OFF Works

  ↓

  Intercept ON Works

  ↓

  HTTPS Works
  
  ↓
  
  Repeater Works

  ↓

  Proxy Can Be Intentionally Enabled/Disabled
  ```

- If every stage passes, your environment is ready for normal learning workflows.

---

## Verification Matrix

| Test            | Expected Result                         |
| --------------- | --------------------------------------- |
| Burp startup    | Main workspace opens                    |
| Listener        | Intended address/port enabled           |
| Browser routing | Matches Burp listener                   |
| HTTP history    | Browser requests appear                 |
| Intercept OFF   | Traffic passes normally                 |
| Intercept ON    | Request can be paused and forwarded     |
| HTTPS           | No unexpected trust error               |
| Repeater        | Captured request can be replayed        |
| Proxy switching | Routing can be intentionally controlled |

---

## If a Test Fails

- Do not continue blindly.

- Identify the first failing layer.

- For example:

  ```text id="n4p9mw"
  Burp Launches ✓

  Listener ✓

  Browser Match ✓

  HTTP History ✓
  
  HTTPS ✗
  ```

- Focus on:

  ```text id="v7m3qx"
  HTTPS / TLS Trust
  ```

- Do not reinstall Burp or redesign the listener unless evidence points there.

---

## Another Example

- Suppose:

  ```text id="k5n8pr"
  Burp Launches ✓

  Listener ✓

  Browser Match ?

  HTTP History ✗  
  ```

- Investigate:

  * Browser proxy configuration.
  * Active proxy profile.
  * Proxy type.
  * Listener address/port mismatch.
  * Conflicting routing software.

- Certificate troubleshooting is premature.

---

## Use a Known Baseline

- When troubleshooting, simplify the environment.

- A useful baseline may be:

  ```text id="r3q9mv"
  Fresh Burp Session

  ↓

  Known Listener

  ↓

  Burp Built-In Browser

  ↓

  Simple Authorized Test Destination
  ```

- If this works, add complexity one layer at a time.

---

## Environment Readiness Checklist

```text id="p8m2kw"
[ ] Burp launches correctly.

[ ] Project starts.

[ ] Proxy listener is enabled.

[ ] Listener address and port are known.

[ ] Browser routes through the listener.

[ ] Requests appear in HTTP history.

[ ] Intercept OFF behavior is understood.

[ ] Intercept ON can pause requests.

[ ] Requests can be forwarded.

[ ] HTTPS traffic is visible without unexpected trust errors.

[ ] Requests can be sent to Repeater.

[ ] Repeater receives responses.

[ ] Proxy routing can be intentionally enabled and disabled.
```

- If all boxes are checked, you have a functional basic Burp environment.

---

## Professional Habit — Test Before the Assessment

- Do not wait until an assessment begins to discover that:

  * Your certificate is missing.
  * Your listener is disabled.
  * Your proxy extension points to the wrong port.
  * Repeater cannot reach the target.

- Before authorized testing begins:

  ```text id="q7m3nx"
  Verify Environment

  ↓

  Verify Scope

  ↓

  Begin Assessment
  ```

- Setup validation should be routine.

---

## Key Takeaways

* Verify Burp as an end-to-end environment.
* Test one layer at a time.
* HTTP history confirms traffic is reaching Burp.
* Intercept ON and OFF have different expected behaviors.
* HTTPS should be verified separately from basic proxy connectivity.
* Repeater confirms the capture-to-manual-testing workflow.
* Troubleshoot the first failing layer.
* Use a simple known-good baseline when diagnosing problems.
* Verify your environment before beginning an assessment.
