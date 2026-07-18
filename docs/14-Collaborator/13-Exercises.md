# Exercises

> **Module Progress**

```text id="r9w2mt"
█████████████░░ 13 / 15 Lessons
```

- These exercises reinforce professional OAST reasoning.

- For each exercise, separate:

  ```text id="k5p7vn"
  Observation

  ↓

  Inference

  ↓

  Validation

  ↓

  Conclusion
  ```

- Do not assign a vulnerability label until the evidence supports it.

---

## Exercise 1 — DNS-Only Evidence

- During an authorized test, you use a unique Collaborator payload.

- Results:

  ```text id="x3m8qr"
  Request Submitted
  
  ↓

  DNS Interaction

  ↓

  No HTTP Interaction
  ```

- Answer:

  1. What can you directly observe?
  2. What does the DNS interaction demonstrate?
  3. What does it not demonstrate?
  4. What additional investigation would you perform?

---

## Exercise 2 — DNS Followed by HTTP

- Another test produces:

  ```text id="q6n4wt"
  Unique Payload

  ↓

  DNS

  ↓

  HTTP
  ```

- Answer:

  1. How does this evidence differ from DNS alone?
  2. Does this automatically confirm SSRF?
  3. What additional information is required?
  4. What determines the final severity?

---

## Exercise 3 — Design a Correlation Strategy

- You need to investigate five different application features.

- Design a payload strategy that allows you to distinguish interactions from each feature.

- Your plan should include:

  * Unique identifiers.
  * Test names.
  * Submission timestamps.
  * Original requests.
  * Interaction records.

- Explain why your design produces stronger evidence than reusing one payload.

---

## Exercise 4 — Delayed Interaction

- You submit a test at:

  ```text id="v2r9pk"
  14:00
  ```

- The application immediately returns a normal response.

- At:

  ```text id="m7q3nx"
  14:12
  ```

- Collaborator records an HTTP interaction.

- Answer:

  1. What might explain the delay?
  2. Why is the timestamp important?
  3. How would you strengthen correlation?
  4. What should you avoid assuming about the backend architecture?

---

## Exercise 5 — Shared Payload Problem

- A tester uses one Collaborator payload in:

  * A URL input.
  * An API parameter.
  * An XML-processing feature.
  * A document importer.

- Thirty minutes later, one DNS interaction appears.

- Answer:

  1. What is the main investigative problem?
  2. Can the triggering feature be identified confidently?
  3. How should the testing strategy be redesigned?

---

## Exercise 6 — Unexpected Source

- Collaborator records an interaction from an IP address different from the application's known public address.

- Answer:

  1. Does this prove another server is vulnerable?
  2. What infrastructure could explain the difference?
  3. What claims can safely be made?
  4. What additional evidence would improve attribution?

---

## Exercise 7 — No Interaction

- You perform an authorized OAST test and observe nothing.

- List at least five possible explanations.

- Then answer:

  > Why would writing "Not vulnerable" in your notes be premature?

---

## Exercise 8 — Reproducibility

- You perform the same controlled test three times using fresh payloads.

```text id="p8w5rv"
Test 1 → Payload A → DNS + HTTP

Test 2 → Payload B → DNS + HTTP

Test 3 → Payload C → DNS + HTTP
```

- Answer:

  1. What does reproducibility add to the evidence?
  2. Does it automatically establish high severity?
  3. What questions still need answering?

---

## Exercise 9 — Evidence Strength vs Severity

- Compare:

### Finding A

- Reliable DNS and HTTP interactions occur repeatedly, but the demonstrated security impact is limited.

### Finding B

- A weaker initial interaction leads to validated behavior affecting a sensitive security boundary.

- Answer:

  1. Which has stronger OAST interaction evidence?
  2. Which might have greater security severity?
  3. Why are these separate questions?

---

## Exercise 10 — Build an Evidence Chain

- Create an investigation record containing:

  ```text id="n4k7qm"
  Security Hypothesis

  ↓

  Feature Tested
  
  ↓

  Unique Payload

  ↓

  Original Request

  ↓
  
  Timestamp

  ↓

  Interaction

  ↓

  Reproduction

  ↓

  Root Cause

  ↓

  Impact

  ↓

  Conclusion
  ```

- Explain why each stage matters.

---

## Mastery Challenge — Ambiguous Investigation

- An application allows users to schedule remote content imports.

- During an authorized assessment:

  1. You submit a unique Collaborator hostname.
  2. The application responds:

    ```text id="t3m9vx"
    Import scheduled
    ```

  3. Twenty seconds later, DNS activity appears.
  4. Two seconds later, an HTTP interaction appears.
  5. Five minutes later, another HTTP interaction occurs.
  6. Repeating the test with a fresh payload produces the same pattern.
  7. The interaction source differs from the application's public IP.

- Analyze the scenario under these headings:

### Observation

- What facts can you directly state?

### Inference

- What reasonable hypotheses could explain the behavior?

### Uncertainty

- What remains unknown?

### Validation

- What would you investigate next within authorization?

### Impact

- What must be established before assigning severity?

### Conclusion

- Write the smallest defensible conclusion supported by the current evidence.

---

## Practical Journal Exercise

- Create a reusable OAST investigation template:

  ```text id="y5r2nw"
  Feature:

  Security Hypothesis:

  Authorization Confirmed:

  Payload Identifier:

  Request Time:

  Original Request Saved:
  
  Immediate Response:

  Interaction Type:

  Interaction Time:

  Reproduced:

  Root Cause Established:

  Impact Established:

  Current Conclusion:

  Remaining Questions:
  ```

- Use this structure during future authorized Collaborator practice.

---

## Self-Assessment Checklist

- You should now be able to:

  * Interpret DNS-only evidence correctly.
  * Distinguish DNS from HTTP interactions.
  * Design strong payload correlation.
  * Account for asynchronous behavior.
  * Handle unexpected source information cautiously.
  * Explain why no interaction is inconclusive.
  * Use reproducibility to strengthen evidence.
  * Separate evidence strength from severity.
  * Build a defensible OAST evidence chain.

---

## Key Takeaways

* Good OAST testing is an evidence-correlation problem.
* Unique payloads improve investigative confidence.
* Timing and interaction sequences matter.
* Reproducibility strengthens correlation.
* Source attribution requires caution.
* No interaction does not prove security.
* Impact—not callback quantity—determines severity.
