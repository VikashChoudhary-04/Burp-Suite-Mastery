# Collaborator Cheat Sheet

> **Module Progress**

```text id="r5w9qt"
██████████████░ 14 / 15 Lessons
```

- This cheat sheet summarizes the essential Burp Collaborator and OAST concepts for quick reference during practice, assessments, and interview preparation.

---

## Core Idea

- Burp Collaborator helps observe application behavior that occurs outside the normal request-response channel.

- Remember:

  > **Interaction ≠ Vulnerability**

- The correct progression is:

  ```text id="m8p3vn"
  Interaction

  ↓

  Evidence

  ↓

  Correlation

  ↓

  Validation

  ↓

  Impact Assessment

  ↓

  Confirmed Finding
  ```

---

## What Is OAST?

- **OAST** means:

  > **Out-of-Band Application Security Testing**

- Use it when security-relevant behavior may occur outside the immediate HTTP response.

---

## Traditional vs OAST Testing

```text id="x4n7qw"
Traditional

Tester → Request → Application → Response → Tester
```

```text id="v2k9mr"
OAST

Tester → Application
             ↓
      External Interaction
             ↓
        Collaborator
             ↓
           Tester
```

- OAST provides an additional observation channel.

---

## Main Concepts

| Concept      | Meaning                                                |
| ------------ | ------------------------------------------------------ |
| OAST         | Testing outside the immediate request-response channel |
| Payload      | Unique identifier used for correlation                 |
| Interaction  | External activity recorded by Collaborator             |
| Polling      | Checking for newly recorded interactions               |
| Correlation  | Connecting an interaction to a specific test           |
| Attribution  | Determining what component likely caused activity      |
| Reproduction | Repeating behavior under controlled conditions         |

---

## Interaction Types

### DNS

- Generally indicates:

  > Hostname resolution activity occurred.

- Does **not automatically prove**:

  * Successful HTTP communication
  * Arbitrary network access
  * Internal access
  * SSRF

---

### HTTP

- Indicates that an HTTP request reached the Collaborator infrastructure.

- Provides additional evidence of outbound communication.

- Still requires context and validation.

---

### SMTP

- May appear in workflows involving email-related processing.

- Interpret according to the tested functionality.

---

## Evidence Strength

- Conceptually:

  ```text id="p6r2nk"
  DNS Only
     ↓
  Evidence of Resolution

  DNS + HTTP
     ↓
  Additional Evidence of Communication

  Reproducible DNS + HTTP
     ↓
  Stronger Correlation

  Validated Security Impact
     ↓
  Defensible Finding
  ```

- Stronger interaction evidence does not automatically mean higher severity.

---

## Professional Workflow

```text id="t9m4vx"
Define Hypothesis

↓

Understand Feature

↓

Confirm Authorization

↓

Generate Unique Payload

↓

Record Original Request

↓

Submit Controlled Test

↓

Poll for Interactions

↓

Correlate Evidence

↓

Reproduce if Appropriate

↓

Establish Root Cause

↓

Assess Impact

↓

Report Confirmed Finding
```

---

## Observation Framework

- When an interaction appears, think:

  ```text id="w3q8pr"
  What did I observe?

  ↓

  What does it prove?

  ↓

  What does it NOT prove?

  ↓

  Can I correlate it?

  ↓

  Can I reproduce it?

  ↓

  What is the security impact?
  ```

- Never skip directly from interaction to vulnerability label.

---

## Correlation Checklist

- Record:

  * Feature tested
  * Unique payload
  * Original request
  * Submission timestamp
  * Immediate response
  * Interaction type
  * Interaction timestamp
  * Reproduction result

- Strong correlation creates stronger findings.

---

## Unique Payload Rule

- Prefer:

  ```text id="n7k2qm"
  Test A → Payload A

  Test B → Payload B

  Test C → Payload C
  ```

- Avoid:

  ```text id="y5m9rv"
  Test A ─┐
  Test B ─┼→ Same Payload
  Test C ─┘
  ```

- Payload reuse creates ambiguity.

---

## Delayed Interaction Checklist

- If nothing appears immediately, consider:

  * Background workers
  * Queues
  * Scheduled processing
  * Retry mechanisms
  * Deferred execution

- Poll again when delayed processing is plausible.

---

## No Interaction Checklist

- No interaction may mean:

  ```text id="k4p8nw"
  No external behavior occurred
  
  OR

  Wrong execution path
  
  OR
  
  Outbound traffic blocked

  OR

  Processing delayed

  OR

  Input transformed

  OR

  Incorrect hypothesis
  ```

- Therefore:

  > **No interaction ≠ proof of security**

---

## Source Attribution Warning

- The observed source may be:

  * Application server
  * Background worker
  * Proxy
  * DNS resolver
  * Gateway
  * Shared infrastructure

- Never assume:

  > "Source IP = vulnerable application server."

- Attribute only what the evidence supports.

---

## Common OAST Investigation Areas

- Collaborator may assist investigations involving behavior associated with:

  * Blind SSRF
  * Blind XXE
  * Blind command injection
  * Certain blind SSTI scenarios
  * Asynchronous server-side processing
  * Other external interaction behaviors

- An interaction alone does not confirm any of these vulnerability classes.

---

## Safety Checklist

- Before OAST testing:

  * Confirm authorization.
  * Confirm external callbacks are permitted.
  * Respect third-party boundaries.
  * Avoid sensitive data in payloads.
  * Minimize unnecessary callbacks.
  * Consider production impact.
  * Protect collected evidence.
  * Determine whether public OAST infrastructure is acceptable.

---

## Common Mistakes

- Avoid:

  * Reusing payloads everywhere.
  * Calling every interaction SSRF.
  * Over-interpreting DNS.
  * Ignoring delayed callbacks.
  * Treating no interaction as proof of security.
  * Assuming source IP identifies the vulnerable server.
  * Confusing evidence strength with severity.
  * Reporting raw Collaborator output without validation.
  * Testing without appropriate authorization.

---

## Evidence vs Severity

- Remember:

  ```text id="r8v3qt"
  Interaction Strength
        ≠
  Vulnerability Severity  
  ```

- Severity depends on factors such as:

  * Attacker control
  * Security boundaries affected
  * Reachable resources
  * Data exposure
  * Realistic business impact

---

## Interview Framework

- For Collaborator scenario questions, answer using:

  ```text id="m2n7pk"
  Observation

  ↓

  What It Proves

  ↓

  What It Does Not Prove

  ↓

  Correlation

  ↓

  Validation

  ↓

  Impact

  ↓

  Conclusion
  ```

- This demonstrates disciplined security reasoning.

---

## Rapid Revision

### OAST?

- Out-of-Band Application Security Testing.

### Collaborator?

- Observes external interactions associated with unique test identifiers.

### DNS?

- Evidence of hostname resolution activity.

### HTTP?

- Evidence that an HTTP request reached Collaborator.

### Unique payloads?

- Improve correlation.

### Polling?

- Retrieves recorded interactions.

### Delayed callback?

- May indicate asynchronous processing.

### Interaction = vulnerability?

- No.

### No interaction = secure?

- No.

### Source IP = vulnerable server?

- Not necessarily.

### Severity based on?

- Validated security impact.

---

## Golden Rules

1. Start with a hypothesis.
2. Confirm authorization.
3. Use unique payloads.
4. Preserve original requests.
5. Track timestamps.
6. Interpret protocols precisely.
7. Account for delayed processing.
8. Reproduce significant behavior when appropriate.
9. Establish impact separately.
10. Never claim more than the evidence supports.
