# Common Mistakes

> **Module Progress**

```text id="v5k8wr"
███████████░░░░ 11 / 15 Lessons
```

- Burp Collaborator can provide powerful evidence of hidden server-side behavior.

- However, poor test design or incorrect interpretation can quickly turn useful evidence into unreliable conclusions.

- The following mistakes are among the most common problems in OAST investigations.

---

## Mistake 1 — Reusing One Payload Everywhere

- Using the same Collaborator payload across many unrelated tests creates weak correlation.

- If an interaction appears later, you may not know which input caused it.

### Better Approach

- Use distinct identifiers whenever practical.

  ```text id="q3n7mx"
  Test A → Payload A

  Test B → Payload B

  Test C → Payload C
  ```

- Unique payloads make attribution to a test case much clearer.

---

## Mistake 2 — Interaction = Vulnerability

- A Collaborator interaction is evidence of behavior.

- It is not automatically proof of:

  * SSRF
  * XXE
  * Command injection
  * SSTI
  * Any other specific vulnerability

### Better Approach

- Follow:

  ```text id="m8p4vt"
  Interaction
  
  ↓

  Observation
  
  ↓

  Investigation
  
  ↓

  Validation

  ↓

  Vulnerability Classification
  ```

- Classify only after the evidence supports the conclusion.

---

## Mistake 3 — Over-Interpreting DNS

- A DNS interaction may demonstrate hostname resolution.

- It does not automatically demonstrate:

  * Successful HTTP communication.
  * Arbitrary network access.
  * Internal network access.
  * High-impact exploitation.

### Better Approach

- State exactly what was observed, then investigate further.

---

## Mistake 4 — Ignoring Delayed Interactions

- Some testers poll once, see nothing, and move on.

- This can miss behavior triggered by:

  * Background workers
  * Queues
  * Scheduled processing
  * Retry mechanisms

### Better Approach

- Consider the application's processing model and poll again when delayed execution is plausible.

---

## Mistake 5 — "No Interaction" = "Secure"

- The absence of an OAST interaction has multiple possible explanations.

```text id="x6r2qn"
No Interaction

├── No external behavior
├── Wrong execution path
├── Outbound traffic blocked
├── Processing delayed
├── Input transformed
└── Incorrect test hypothesis
```

### Better Approach

- Treat "no interaction observed" as an observation—not proof of security.

---

## Mistake 6 — Ignoring the Original Request

- An interaction without its triggering request or workflow loses important context.

### Better Approach

- Preserve:

  * Original request.
  * Tested parameter or feature.
  * Payload identifier.
  * Timestamp.
  * Immediate response.
  * Interaction details.

- Evidence should form a traceable chain.

---

## Mistake 7 — Assuming the Source IP Is the Vulnerable Server

- The observed source may belong to:

  * A proxy.
  * A background worker.
  * A DNS resolver.
  * A gateway.
  * Shared infrastructure.

### Better Approach

- Avoid architectural claims unless additional evidence supports them.

---

## Mistake 8 — Failing to Reproduce Significant Behavior

- One unexpected interaction can be valuable, but reproducibility often strengthens confidence substantially.

### Better Approach

- When appropriate and authorized, repeat the controlled test using a fresh unique payload.

- Compare the results.

---

## Mistake 9 — Confusing Interaction Strength With Severity

- DNS followed by HTTP may provide stronger evidence of outbound communication than DNS alone.

- That does not automatically make the vulnerability "High" or "Critical."

- Severity depends on:

  * Attacker control.
  * Security boundaries crossed.
  * Reachable resources.
  * Data exposure.
  * Realistic impact.

### Better Approach

- Assess evidence strength and security impact separately.

---

## Mistake 10 — Testing Without OAST Authorization

- External callbacks may cross organizational boundaries.

### Better Approach

- Confirm that the rules of engagement permit:

  * External interactions.
  * The chosen OAST infrastructure.
  * The tested workflow.
  * The expected data flow.

- Technical capability does not equal authorization.

---

## Mistake 11 — Putting Sensitive Data in Payloads

- Embedding credentials, tokens, personal data, or confidential information into externally observable identifiers can create unnecessary exposure.

### Better Approach

- Use minimal, non-sensitive unique identifiers for correlation.

---

## Mistake 12 — Generating Excessive Callbacks

- Repeated tests can create unnecessary:

  * Network traffic.
  * Backend processing.
  * Logs.
  * Monitoring alerts.

### Better Approach

- Generate only enough evidence to answer the security question confidently.

---

## Mistake 13 — Poor Note-Taking

- During a large assessment, dozens of tests may occur.

- Without clear notes, delayed interactions can become difficult to correlate.

### Better Approach

- Maintain a simple payload journal:

  ```text id="k9w3pr"
  Test

  ↓

  Payload

  ↓

  Submission Time
  
  ↓

  Observed Interaction

  ↓

  Validation Status
  ```

- Organization improves evidence quality.

---

## Mistake 14 — Reporting Raw Collaborator Output

- A screenshot of an interaction is not a complete vulnerability report.

- A professional finding should explain:

  * What was tested.
  * What happened.
  * Why the interaction occurred.
  * How it was validated.
  * What security impact exists.
  * How the issue can be remediated.

### Better Approach

- Use Collaborator evidence as one part of a complete technical argument.

---

## Collaborator Challenge

- A tester submits the same Collaborator payload into ten different inputs.

- Two hours later, a DNS interaction appears from an unfamiliar IP address.

- They report:

  > "Critical SSRF confirmed on the application server."

- Identify the problems.

- Consider:

  * Correlation.
  * DNS interpretation.
  * Source attribution.
  * Vulnerability classification.
  * Severity.
  * Reproducibility.

- How would you redesign the investigation to produce defensible evidence?

---

## Professional Habits

- Experienced testers:

  * Use unique payloads.
  * Preserve original requests.
  * Track timing.
  * Account for delayed behavior.
  * Reproduce important observations.
  * Separate evidence strength from severity.
  * Avoid unsupported attribution.
  * Respect authorization boundaries.
  * Report validated findings—not raw interactions.

---

## Key Takeaways

* Payload reuse weakens correlation.
* An interaction does not automatically identify a vulnerability.
* DNS evidence must be interpreted precisely.
* No interaction does not prove security.
* Source attribution requires caution.
* Reproducibility strengthens confidence.
* Severity depends on validated impact.
* Collaborator output is evidence—not a complete report.
