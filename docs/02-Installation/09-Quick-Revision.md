# Quick Revision

> **Module Progress**

```text id="r5w9qt"
█████████ 9 / 9 Lessons ✓
```

- Congratulations! You have completed **Module 02 – Installation & Initial Setup**.

- This page summarizes the essential setup concepts you should remember before moving into HTTP fundamentals and deeper Burp workflows.

---

## The Complete Environment

- Your basic Burp environment is:

  ```text id="m8p3vn"
  Testing Browser

  ↓

  Burp Proxy Listener

  ↓
  
  Burp Suite

  ↓

  Authorized Target
  ```  

- For HTTPS:

  ```text id="x4n7qw"  
  Browser

  ⇅ TLS

  Burp

  ⇅ TLS

  Target
  ```  

- Every layer must function correctly.

---

## Installation Mental Model

- Use:

  ```text id="v2k9mr"
  Official Source

  ↓

  Correct Platform

  ↓

  Install

  ↓

  Launch

  ↓

  Verify Burp

  ↓

  Configure Networking
  ```

- Do not troubleshoot browser proxying before confirming Burp itself works.

---

## Projects vs Configuration

- Remember:

  ```text id="p6r2nk"
  Project

  =

  Testing Workspace / Assessment State
  ```

- while:

  ```text id="t9m4vx"
  Configuration

  =

  How Burp Behaves
  ```

- Keep these concepts separate.

---

## Temporary vs Persistent Work

- Temporary projects are useful for:

  * Learning.
  * Quick experiments.
  * Troubleshooting.
  * Short sessions.

- Persistent project workflows are useful when assessment state must be retained.

- Treat saved Burp data as sensitive.

---

## Proxy Listener

- A common local listener is:

  ```text id="w3q8pr"
  127.0.0.1:8080
  ```

- Meaning:

  ```text id="n7k2qm"
  127.0.0.1

  =

  Local Machine / Loopback
  ```

  ```text id="y5m9rv"
  8080

  =

  TCP Port
  ```

- The listener accepts proxy connections from the browser.

---

## The Matching Rule

- If Burp listens on:

  ```text id="k4p8nw"
  127.0.0.1:8080
  ```

- the browser should send proxy traffic to:

  ```text id="r8v3qt"
  127.0.0.1:8080
  ```

- A mismatch breaks the connection.

- Always compare both sides.

---

## Listener vs Intercept

- Do not confuse:

  ```text id="m2n7pk"
  Listener

  =

  Accepts Proxy Connections
  ```

- with:

  ```text id="q6m2kt"
  Intercept

  =

  Controls Whether Matching Traffic Pauses
  ```

- Burp can proxy and record traffic while Intercept is OFF.

---

## Browser Options

- Two common approaches are:

  ```text id="c8cvxe"
  Burp Built-In Browser
  ```

- or:

  ```text id="lrj7kb"
  Dedicated External Browser/Profile
  ```

- The built-in browser is useful for quick setup and troubleshooting.

- A dedicated external profile provides controlled isolation.

---

## Why Use a Dedicated Testing Browser?

- It separates:

  * Proxy settings.
  * Burp CA trust.
  * Testing extensions.
  * Cookies.
  * Sessions.
  * Lab accounts.

- Prefer:

  ```text id="25kn1j"
  Normal Browser

  ↓

  Normal Browsing
  ```

- and:

  ```text id="jc1tfb"
  Testing Browser

  ↓

  Burp

  ↓

  Authorized Testing
  ```

---

## HTTP Proxy vs SOCKS

- Do not assume:

  ```text id="cob62g"
  HTTP Proxy

  =

  SOCKS Proxy  
  ```

- They are different proxy mechanisms.

- Configure the browser according to the type of proxy service Burp provides for your workflow.

---

## HTTPS and Burp CA

- For normal HTTPS interception, the testing browser must trust Burp's CA appropriately.

- Conceptually:

  ```text id="hbjmc9"
  Burp CA Trusted

  ↓

  Burp-Generated Interception Certificates Accepted

  ↓

  HTTPS Traffic Can Be Inspected
  ```

- Certificate trust is security-sensitive.

- Use it intentionally.

---

## HTTPS Diagnostic Pattern

- Remember:

  ```text id="h7m4qx"
  HTTP Works

  HTTPS Fails

  ↓

  Investigate TLS / CA Trust
  ```

- Do not rebuild the entire proxy setup unless evidence points there.

---

## Certificate Trust Is Browser-Specific

- A certificate being installed somewhere on the computer does not automatically mean the active browser trusts it.

- Ask:

  ```text id="k8n3pr"
  Which Browser?

  ↓

  Which Profile?

  ↓

  Which Trust Store?

  ↓

  Is Burp CA Actually Trusted There?
  ```

---

## Verify With HTTP History

- One of the most important setup checks is:

  ```text id="amzq4j"
  Browser Request

  ↓

  Proxy → HTTP History
  
  ↓

  Request Appears
  ```

- If the request appears, the browser is routing traffic through Burp.

- A website loading by itself does not prove this.

---

## Intercept ON

```text id="uxt2rk"
Browser Request

↓

Burp Pauses Request

↓

Tester Reviews

↓

Forward

↓

Request Continues
```

- The browser may appear to hang while waiting.

- That can be normal.

---

## Intercept OFF

```text id="mv3e52"
Browser Request

↓

Burp Proxies Request Automatically

↓

Request Appears in HTTP History

↓

Page Loads
```

- Use this when you want normal browsing while recording traffic.

---

## Environment Verification Sequence

- Use:

  ```text id="c4u08e"
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
  ```

- Do not skip directly to advanced troubleshooting.

---

## Troubleshooting Decision Tree

```text id="c1etmn"
Does Burp Start?
│
├── No → Installation / Runtime
│
└── Yes
     ↓
Is Listener Active?
│
├── No → Listener / Port
│
└── Yes
     ↓
Does Browser Match Listener?
│
├── No → Browser Proxy
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
├── No → Connectivity
│
└── Yes
     ↓
Does HTTPS Work?
│
├── No → TLS / CA
│
└── Yes
     ↓
Environment Operational
```

- Find the first failing layer.

---

## Common Failure Patterns

| Observation                                    | Investigate First              |
| ---------------------------------------------- | ------------------------------ |
| Burp will not launch                           | Installation/runtime           |
| Proxy refuses connection                       | Listener/address/port          |
| Port already in use                            | Port conflict                  |
| Sites load but HTTP history is empty           | Browser bypassing Burp         |
| Browser hangs                                  | Intercept may be ON            |
| HTTP works, HTTPS fails                        | CA/TLS trust                   |
| Built-in browser works, external browser fails | External browser configuration |
| Target requires VPN and fails                  | Routing/VPN                    |
| Problems begin after extension install         | Extension/configuration        |
| Everything is confusing                        | Return to known-good baseline  |

---

## Port Conflict

- If another process uses:

  ```text id="t3o4df"
  127.0.0.1:8080
  ```

- either resolve the conflict or use another appropriate port.

- If Burp changes to:

  ```text id="2uyij3"
  127.0.0.1:8081
  ```

- the browser must also use:

  ```text id="k5oblv"
  127.0.0.1:8081
  ```

- Consistency matters more than the specific port number.

---

## Professional Lab Model

- Aim for:

  ```text id="mvnv8h"
  Dedicated Browser/Profile
          │
          ▼
       Burp Suite
          │
          ├── Isolated Project
          ├── Known Configuration
          ├── Controlled Extensions
          │
          ▼
  Authorized Target
          │
          ▼
  Notes + Evidence
  ```

- Keep the environment simple and intentional.

---

## Extension Principle

- Do not use:

  ```text id="e8j8hc"
  Install Everything

  ↓

  Hope It Helps
  ```

- Use:

  ```text id="txb4vz"
  Need

  ↓
  
  Evaluate

  ↓

  Understand

  ↓

  Install

  ↓

  Verify
  ```

- Every extension adds another variable.

---

## Evidence and Project Security

- Burp may capture:

  * Session cookies.
  * Tokens.
  * Credentials.
  * Personal information.
  * Internal URLs.
  * Vulnerability evidence.

- Do not casually upload raw Burp data or project files to public repositories.

- Treat assessment data as sensitive.

---

## Known-Good Baseline

- When troubleshooting becomes confusing, return to:

  ```text id="ql1edb"
  Fresh Burp Session

  ↓

  Known Configuration

  ↓
  
  Known Listener

  ↓

  Built-In Browser

  ↓

  Simple Authorized Test
  ```

- Then add complexity one layer at a time.

---

## Pre-Assessment Checklist

```text id="8gvw6w"
[ ] Authorization and scope confirmed.

[ ] Burp launches correctly.

[ ] Correct project created.

[ ] Listener verified.

[ ] Testing browser verified.

[ ] HTTP history receives traffic.

[ ] HTTPS interception works.

[ ] Repeater works.

[ ] Required VPN/network access works.

[ ] Extensions reviewed.

[ ] Notes/evidence workspace prepared.

[ ] Sensitive-data handling understood.
```

---

## Ten Things to Remember

1. Verify Burp before configuring the browser.
2. Projects and configurations are different concepts.
3. The browser proxy must match the Burp listener.
4. Listener and Intercept are not the same thing.
5. HTTP and SOCKS proxies are different.
6. HTTP history proves whether traffic reached Burp.
7. Burp CA trust enables normal HTTPS interception.
8. Troubleshoot the first failing layer.
9. Maintain a simple known-good baseline.
10. Keep testing environments isolated and organized.

---

## Final Mental Model

```text id="n0clgn"
Install

↓

Launch

↓

Listener

↓

Browser Routing

↓

HTTP Verification

↓

HTTPS Trust

↓

Repeater Verification

↓

Professional Environment
```

- If something fails:

  ```text id="q7m4vx"
  Observe

  ↓

  Locate First Failing Layer

  ↓

  Form Hypothesis

  ↓

  Change One Thing

  ↓

  Test Again
  ```  

- The same evidence-based reasoning used in penetration testing should also be used to troubleshoot your tools.

---

## Module Complete

- You have completed **Module 02 – Installation & Initial Setup**.

- You should now be able to:

  * Install and launch Burp correctly.
  * Understand projects and configuration conceptually.
  * Explain how a proxy listener works.
  * Configure a browser to route traffic through Burp.
  * Understand why Burp's CA is required for HTTPS interception.
  * Verify the complete browser-to-Burp workflow.
  * Troubleshoot common setup failures systematically.
  * Build an isolated and repeatable testing environment.

- The most important setup principle is:

  > **Find the first failing layer. Fix that layer before changing anything else.**
