# Real Investigation Scenarios

> **Module Progress**

```text id="x6n2wt"
████████░░░░░░░ 8 / 15 Lessons
```

- Out-of-band evidence becomes meaningful only when interpreted within the application workflow.

- The following scenarios demonstrate how Collaborator can support investigations without turning every interaction into an automatic vulnerability finding.

- For each scenario, separate:

  ```text id="h5r9mv"
  Observation

  ↓

  Inference

  ↓  

  Validation

  ↓

  Conclusion
  ```

---

## Scenario 1 — Server-Side URL Processing

- An application allows users to submit a URL for content processing.

- The visible response only states:

  > "Request accepted."

- A unique Collaborator payload is used during an authorized test.

- Shortly afterward:

  ```text id="q4v7pk"
  DNS Interaction

  ↓

  HTTP Interaction
  ```

### Observation

- The unique Collaborator address received both DNS and HTTP activity after the test.

### Possible Inference

- A server-side or related component may have attempted to access the supplied external resource.

### Questions to Investigate

* Can the behavior be reproduced?
* Does it occur only when this feature is used?
* Which application component may be responsible?
* What restrictions exist on outbound requests?
* What security impact can actually be demonstrated?

Do not jump directly from "HTTP interaction" to "critical SSRF."

---

## Scenario 2 — DNS-Only Interaction

- A different feature produces only:

  ```text id="m8w3rn"
  Submitted Payload
  
  ↓

  DNS Interaction

  ↓

  No HTTP Interaction
  ```

### Observation

- Hostname resolution occurred.

### What It Does Not Prove

- DNS activity alone does not necessarily prove:

  * A successful HTTP connection.
  * Full outbound network access.
  * Access to internal resources.
  * A specific vulnerability class.

- Possible explanations require further investigation.

---

## Scenario 3 — Delayed Background Processing

- A user submits data for later processing.

- The application immediately responds successfully.

- Nothing appears in Collaborator.

- Several minutes later, an interaction arrives.

```text id="t2n6qy"
User Request

↓

Immediate Response

↓

Queue / Background Job

↓

Delay

↓

External Interaction
```

### Lesson

- The original HTTP response and the security-relevant behavior may occur at completely different times.

- Record timestamps and poll again when asynchronous processing is plausible.

---

## Scenario 4 — XML Processing Behavior

- An authorized assessment involves functionality that processes XML.

- The visible application response does not reveal whether external references are processed.

- An OAST interaction later appears.

### Observation

- Some component involved in the tested workflow interacted with the unique external identifier.

### Investigation Questions

* Can the interaction be reliably tied to XML processing?
* Is the behavior reproducible?
* What exact processing feature caused it?
* Does the behavior create a meaningful security impact?

OAST evidence can reveal hidden processing, but root cause still requires validation.

---

## Scenario 5 — Ambiguous Shared Payload

- A tester uses the same Collaborator payload in:

  * A URL field.
  * An XML workflow.
  * A document-processing feature.
  * An API parameter.

- Later, an HTTP interaction appears.

- The problem:

  > Which test caused it?

- Because the same payload was reused, correlation is weak.

### Better Design

```text id="k7p4vx"
URL Test      → Payload A

XML Test      → Payload B

Document Test → Payload C

API Test      → Payload D
```

- Unique payloads make later evidence much easier to interpret.

---

## Scenario 6 — Reproducible Interaction

- A unique payload produces an interaction.

- The tester repeats the authorized test with a new unique payload.

- The same pattern occurs again.

```text id="r3m8wt"
Test 1 → Payload A → Interaction

Test 2 → Payload B → Interaction

Test 3 → Payload C → Interaction
```

### Why This Matters

- Controlled reproducibility strengthens the relationship between:

  * The tested feature.
  * The supplied input.
  * The observed external behavior.

- It still does not automatically establish severity.

- Impact must be evaluated separately.

---

## Scenario 7 — No Interaction

- A test produces no Collaborator activity.

- Possible interpretations include:

```text id="w9q2nk"
No Interaction

├── No external behavior occurred
├── Wrong execution path
├── Processing delayed
├── Outbound traffic restricted
├── Input transformed or rejected
└── Test hypothesis incorrect
```

- "No interaction" is an observation.

- "Not vulnerable" is a conclusion requiring more evidence.

---

## Scenario 8 — Unexpected Source

- You test one application server, but the observed interaction appears to originate from infrastructure you did not expect.

- Possible explanations may include:

  * Background workers.
  * Proxies.
  * DNS infrastructure.
  * Shared services.
  * Security gateways.

- Do not assume:

  > "This IP address is definitely the vulnerable server."

- Network architecture can separate the component receiving input from the component making the external interaction.

---

## Investigation Comparison

- Consider three results:

| Test | Evidence       | Immediate Interpretation                   |
| ---- | -------------- | ------------------------------------------ |
| A    | No interaction | No OAST evidence observed                  |
| B    | DNS only       | Hostname resolution activity observed      |
| C    | DNS + HTTP     | Additional outbound communication observed |

- None of these rows alone establishes vulnerability severity.

- Context determines significance.

---

## Scenario Challenge

- An application imports data from a user-supplied external location.

- During an authorized test:

  1. You use a unique Collaborator payload.
  2. The application returns `Import scheduled`.
  3. Thirty seconds later, DNS activity appears.
  4. Five seconds after that, an HTTP interaction appears.
  5. Repeating the test with a new payload produces the same pattern.

- Ask yourself:

  * What observations can you state confidently?
  * How strong is the correlation?
  * What does reproducibility add?
  * What vulnerability hypothesis might deserve investigation?
  * What must still be established before assigning impact or severity?

- Build conclusions one piece of evidence at a time.

---

## Investigation Journal

- For significant OAST tests, record:

  * Feature tested.
  * Security hypothesis.
  * Unique payload identifier.
  * Request timestamp.
  * Immediate application response.
  * Interaction timestamp.
  * Interaction type.
  * Reproduction attempts.
  * Supported conclusions.
  * Remaining uncertainty.

- Good notes turn scattered interactions into defensible evidence.

---

## Key Takeaways

* DNS-only and HTTP interactions provide different evidence.
* Delayed processing is common in OAST investigations.
* Unique payloads significantly improve correlation.
* Reproducibility strengthens confidence.
* Unexpected source infrastructure should be interpreted cautiously.
* No interaction does not automatically prove security.
* Vulnerability classification and severity require additional context.
