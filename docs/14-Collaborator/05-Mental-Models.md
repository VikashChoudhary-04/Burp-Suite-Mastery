# Mental Models

> **Module Progress**

```text
█████░░░░░░░░░░ 5 / 15 Lessons
```

- Burp Collaborator can reveal behavior that would otherwise remain invisible.

- However, observing an external interaction is only one part of the investigation.

- Professional testers carefully separate:

  * What they observed.
  * What they infer.
  * What they can prove.

- These mental models help prevent weak assumptions from becoming incorrect findings.

---

## Mental Model 1 — Interaction ≠ Vulnerability

- A Collaborator interaction proves that some form of external activity occurred.

- It does not automatically prove:

  * Which vulnerability caused it.
  * Whether exploitation is possible.
  * What security impact exists.
  * Which exact backend component initiated it.

- Think:

  > **"I have evidence of behavior. Now I need to explain why it happened."**

---

## Mental Model 2 — Observation → Inference → Conclusion

- Keep these three stages separate.

  ```text
  Observation

  ↓

  Inference

  ↓
  
  Validation
  
  ↓

  Conclusion
  ```

### Observation

- What directly happened?

- Example:

  > A DNS lookup was recorded for a unique Collaborator payload.

### Inference

- What might explain it?

- Example:

  > A server-side component may have processed the supplied hostname.

### Conclusion

- What can the complete evidence support after validation?

- Do not jump directly from observation to vulnerability classification.

---

## Mental Model 3 — Correlation Creates Confidence

- An interaction is much more useful when you can confidently associate it with a specific test.

- Strong correlation may involve:

  * Unique payloads.
  * Clear timestamps.
  * Recorded requests.
  * Organized notes.
  * Reproducible behavior.

- Poor correlation creates uncertainty.

---

## Mental Model 4 — Protocols Tell Different Stories

- Not every interaction means the same thing.

- For example:

  ```text
  DNS Interaction
        ↓
  Hostname resolution occurred

  HTTP Interaction
        ↓
  Additional network communication occurred
  ```

- One may provide stronger evidence of outbound connectivity than the other.

- Interpret each protocol according to what it actually demonstrates.

---

## Mental Model 5 — Time Is Part of the Evidence

- Out-of-band behavior may be delayed.

- Applications may use:

  * Background jobs.
  * Queues.
  * Scheduled processing.
  * Separate backend services.

- Do not assume that no immediate interaction means nothing happened.

- Poll again when delayed processing is plausible.

---

## Mental Model 6 — Absence of Evidence Is Not Evidence of Absence

- If Collaborator records nothing, several explanations are possible.

- For example:

  * The application never attempted an interaction.
  * The tested input never reached the relevant component.
  * Outbound traffic was blocked.
  * Processing was delayed.
  * The test did not reach the correct execution path.

- A silent Collaborator result does not automatically prove security.

---

## Mental Model 7 — Source IP ≠ Root Cause

- An observed interaction may originate from infrastructure different from the system that received your original request.

- Possible intermediaries include:

  * Proxies.
  * Background workers.
  * DNS resolvers.
  * Security gateways.
  * Supporting services.

- Avoid making architectural claims that the evidence cannot support.

---

## Mental Model 8 — Reproducibility Strengthens Findings

- A single unexpected interaction may be interesting.

- A controlled, repeatable interaction associated with unique test inputs provides much stronger evidence.

- When appropriate and authorized, ask:

  > **"Can I reproduce this behavior consistently?"**

- Reproducibility helps distinguish meaningful application behavior from unrelated activity.

---

## Mental Model 9 — OAST Complements Direct Testing

- Collaborator should not become the answer to every testing problem.

- Use direct request-response evidence when it is sufficient.

- Use OAST when important behavior may occur outside that visible channel.

```text
Direct Evidence Available?

     │
 Yes │ No / Incomplete
     ▼
Use Direct     Consider OAST
Evidence            ↓
               Correlate Results
                    ↓
                 Validate
```

- Choose the simplest method that provides reliable evidence.

---

## Collaborator Challenge

- You test three different application features using three unique Collaborator payloads.

- Results:

  * **Feature A:** No interaction.
  * **Feature B:** One DNS interaction.
  * **Feature C:** DNS followed by HTTP.

- Ask yourself:

  * What can you directly state about each result?
  * Which result provides the strongest evidence of outbound communication?
  * Which conclusions would still require manual investigation?
  * Why would it be incorrect to immediately label all three features secure or vulnerable?

- Think in terms of evidence strength rather than binary answers.

---

## Professional Habits

- Experienced testers:

  * Separate observations from conclusions.
  * Use unique payloads for strong correlation.
  * Interpret each interaction type carefully.
  * Account for asynchronous behavior.
  * Avoid over-attributing source infrastructure.
  * Reproduce significant behavior when appropriate.
  * Continue manual investigation after an interaction appears.

---

## Key Takeaways

* An interaction is evidence—not automatically a vulnerability.
* Separate observation, inference, validation, and conclusion.
* Strong correlation increases confidence.
* DNS and HTTP interactions provide different evidence.
* No interaction does not prove the absence of a vulnerability.
* Reproducibility strengthens security findings.
* OAST complements rather than replaces direct testing.
