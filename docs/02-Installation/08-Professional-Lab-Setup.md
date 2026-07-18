# Professional Lab Setup

> **Module Progress**

```text id="r7w3mt"
████████░ 8 / 9 Lessons
```

- A working Burp installation is enough to begin learning.

- A **professional lab setup** goes further.

- It should be:

  ```text id="p4m8qn"
  Predictable

  +

  Isolated

  +

  Organized

  +

  Reproducible

  +

  Safe
  ```

- The objective is to create an environment where you can focus on security testing instead of repeatedly fixing your tools.

---

## The Core Environment

- A practical testing environment may look like:

  ```text id="v6n2kx"
  Dedicated Testing Browser

  ↓

  Burp Suite

  ↓

  Authorized Lab / Target
  ```

- Supporting this workflow are:

  ```text id="q9m3wr"
  Burp Projects

  Testing Notes

  Evidence Storage

  Extensions

  Proxy Configuration

  Certificates
  ```  

- Each component should have a clear purpose.

---

## 1 — Use a Dedicated Testing Browser

- Avoid using your everyday browser environment for security testing.

- Prefer:

  ```text id="n5p8mv"
  Normal Browser/Profile

  ↓

  Personal Browsing
  ```

- and:

  ```text id="x7m4qt"
  Testing Browser/Profile

  ↓

  Burp

  ↓

  Authorized Security Testing
  ```

- This separates testing configuration from normal browsing.

---

## Why Browser Isolation Matters

- A testing browser may contain:

  * Burp proxy settings.
  * Burp CA trust.
  * Testing extensions.
  * Lab cookies.
  * Multiple test accounts.
  * Modified browser behavior.

- Keeping this separate reduces accidental interference with normal browsing.

---

## 2 — Keep Certificate Trust Isolated

- Burp's CA certificate should be trusted only where it is intentionally needed.

- Prefer:

  ```text id="m3q9vr"
  Dedicated Testing Profile

  ↓

  Burp CA Trusted
  ```

- Avoid unnecessary system-wide trust when a narrower testing setup is sufficient.

- Certificate trust is a security-sensitive configuration.

---

## 3 — Use Intentional Proxy Switching

- You should know whether your browser is currently using:

  ```text id="k8n2pw"
  Direct Connection
  ```

- or:

  ```text id="r4m7qx"
  Burp Proxy
  ```

- A proxy-switching extension may improve convenience, but only if you understand which configuration controls the browser.

- Avoid overlapping unknown proxy settings.

---

## 4 — Separate Assessment Projects

- Do not mix unrelated work unnecessarily.

- Prefer:

  ```text id="t9p3mv"
  Lab A

  ↓

  Burp Project A
  ```

  ```text id="q6n8kr"
  Assessment B

  ↓

  Burp Project B
  ```

- Separation improves:

  * Organization.
  * Evidence handling.
  * Troubleshooting.
  * Reporting.

---

## 5 — Create a Testing Workspace

- A simple filesystem structure can keep supporting evidence organized.

- For example:

  ```text id="m5q2vn"
  Burp-Testing/
  │
  ├── Labs/
  ├── Projects/
  ├── Notes/
  ├── Screenshots/
  ├── Reports/
  └── Exports/
  ```

- For a specific authorized target:

  ```text id="x8n4pk"
  Target-Name/  
  │
  ├── notes/
  ├── screenshots/
  ├── requests/
  ├── responses/
  └── findings/
  ```

- Adapt the structure to your workflow.

- Consistency matters more than the exact folder names.

---

## 6 — Maintain an Investigation Journal

- For important tests, record:

  ```text id="p7m3qw"
  Date:

  Target:

  Feature:

  User / Role:

  Baseline:

  Hypothesis:

  Request:

  Controlled Change:

  Observation:

  Evidence:

  Current Conclusion:

  Next Step:
  ```

- This prevents useful observations from becoming disconnected from context.

---

## 7 — Use Clear Evidence Names

- Avoid:

  ```text id="v2n9mr"
  Screenshot1.png

  Screenshot2.png

  final-final-request.txt
  ```

- Prefer descriptive names such as:

  ```text id="k4m8qx"
  authorization-baseline-user-a.png

  authorization-test-object-b.png

  profile-update-request.txt
  ```

- Good evidence naming saves time during reporting.

---

## 8 — Keep Raw Evidence

- When a significant observation occurs, preserve enough original evidence to reproduce and explain it.

- Depending on the investigation, this may include:

  * Original request.
  * Original response.
  * Modified request.
  * Modified response.
  * Relevant screenshots.
  * Timestamps.
  * User/role context.

- Do not rely only on memory.

---

## 9 — Protect Sensitive Data

- Burp and supporting files may contain:

  * Session cookies.
  * API keys.
  * Authorization tokens.
  * Personal information.
  * Internal application details.
  * Vulnerability evidence.

- Treat this material as sensitive.

- Do not commit raw assessment data to public repositories.

---

## 10 — Keep Extensions Under Control

- Extensions can significantly improve Burp workflows.

- They can also create:

  * Performance overhead.
  * Unexpected behavior.
  * Additional attack surface.
  * Configuration complexity.

- Use:

  ```text id="r6q2nv"
  Need

  ↓

  Evaluate Extension

  ↓

  Understand Purpose

  ↓

  Install

  ↓

  Observe Behavior
  ```

- not:

  ```text id="n9m4pk"
  Interesting Extension

  ↓

  Install Everything
  ```

- A smaller understood toolset is often better than an overloaded environment.

---

## 11 — Record Important Extensions

- Maintain a simple list:

  ```text id="q3m7vx"
  Extension:

  Purpose:

  Source:

  Why Installed:

  Last Reviewed:
  ```

- Periodically remove extensions you no longer use.

---

## 12 — Keep Burp Updated

- Burp updates may provide:

  * Security fixes.
  * Protocol improvements.
  * Scanner enhancements.
  * New functionality.
  * Bug fixes.

- However, during sensitive professional work, understand your organization's update and change-control requirements.

- Predictability matters as well as freshness.

---

## 13 — Use Safe Practice Targets

- Practice only against:

  * Systems you own.
  * Intentionally vulnerable applications.
  * Authorized training platforms.
  * Explicitly authorized targets.

- Examples of intentionally vulnerable applications commonly used for training include:

  * OWASP Juice Shop.
  * Web Security Academy labs.
  * DVWA.

- Always follow the relevant environment's rules.

---

## 14 — Separate Lab and Real Assessment Habits

- A lab may allow aggressive experimentation.

- A production assessment may require:

  * Rate limits.
  * Testing windows.
  * Restricted techniques.
  * Data-handling rules.
  * Specific scope boundaries.

- Do not assume:

  ```text id="m8p3qw"
  Allowed in Lab

  =

  Allowed in Client Environment
  ```

- Authorization determines acceptable testing behavior.

---

## 15 — Build a Known-Good Baseline

- Maintain a simple environment you know works.

- For example:

  ```text id="x5n9kr"
  Burp

  ↓

  Default / Known Configuration

  ↓

  Known Listener

  ↓

  Dedicated Testing Browser

  ↓

  Correct CA Trust

  ↓

  Simple Authorized Test
  ```

- When advanced configuration breaks, return to this baseline.

---

## 16 — Avoid Configuration Drift

- Over time you may accumulate:

  * Custom proxy rules.
  * Extensions.
  * Network settings.
  * Match-and-replace rules.
  * Session handling behavior.
  * TLS changes.

- Periodically ask:

  > Do I still know why each important customization exists?

- If not, review the environment.

---

## 17 — Separate User Roles During Testing

- Authorization testing often requires multiple identities.

- For example:

  ```text id="t7m2pv"
  User A

  User B

  Admin
  ```

- Use deliberate session separation through appropriate browser profiles, containers, or other controlled mechanisms.

- Avoid accidentally testing with the wrong session.

---

## 18 — Pre-Assessment Checklist

- Before beginning authorized testing:

  ```text id="q8m4nr"
  [ ] Scope reviewed.
  
  [ ] Rules of engagement reviewed.

  [ ] Burp updated appropriately.

  [ ] Project created.

  [ ] Listener verified.

  [ ] Testing browser verified.

  [ ] HTTPS interception verified.

  [ ] Required VPN verified.

  [ ] Required test accounts available.

  [ ] Notes/evidence workspace created.

  [ ] Extensions reviewed.

  [ ] Sensitive-data handling understood.
  ```

- Do this before active testing begins.

---

## 19 — Post-Assessment Hygiene

- After an assessment:

  * Secure evidence.
  * Export required material appropriately.
  * Remove temporary sensitive data when policy requires.
  * Close unnecessary sessions.
  * Review project retention requirements.
  * Remove target-specific configuration if no longer needed.

- Professional testing includes responsible cleanup.

---

## Recommended Environment Model

```text id="p4n8mv"
Dedicated Browser/Profile
        │
        ▼
     Burp Suite
        │
        ├── Isolated Project
        ├── Controlled Extensions
        ├── Known Configuration
        │
        ▼
Authorized Target / Lab
        │
        ▼
Notes + Evidence
        │
        ▼
Validated Findings
```

- This creates a repeatable workflow.

---

## Minimal vs Advanced Setup

- Do not over-engineer your environment too early.

- Start:

  ```text id="v9m3qx"
  Burp

  +

  Dedicated Browser

  +

  Known Proxy

  +

  CA Trust

  +

  Notes
  ```

- Add complexity only when it solves a real problem.

- For example:

  ```text id="k6n2pr"
  Need Faster Proxy Switching?

  ↓

  Add Proxy Switcher
  ```

  ```text id="r3m8vw"
  Need Specific Analysis Capability?

  ↓

  Evaluate Extension
  ```

- Purpose should drive customization.

---

## Professional Lab Checklist

```text id="n7q4mx"
[ ] Dedicated testing browser/profile.

[ ] Burp CA trust isolated appropriately.

[ ] Proxy switching understood.

[ ] Separate projects for unrelated work.

[ ] Organized notes and evidence.

[ ] Sensitive data protected.

[ ] Extensions intentionally selected.

[ ] Known-good baseline maintained.

[ ] Multiple user sessions separated.

[ ] Safe practice targets used.

[ ] Scope checked before testing.

[ ] Cleanup performed after assessments.
```

---

## Key Takeaways

* A professional Burp environment should be predictable and isolated.
* Use a dedicated testing browser or profile.
* Limit CA trust to the environments that need it.
* Keep projects and evidence organized.
* Protect captured assessment data.
* Install extensions intentionally.
* Maintain a known-good baseline.
* Separate user sessions carefully.
* Lab permissions do not automatically apply to production assessments.
* Add complexity only when it solves a real testing problem.
* Setup discipline directly improves testing quality.
