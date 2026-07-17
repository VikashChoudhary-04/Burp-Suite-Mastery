# Mental Models

> **Module Progress**

```text
█████░░░░░░░░░░ 5 / 15 Lessons
```

- Sequencer is not about collecting impressive-looking statistics.

- It is about thinking critically about whether security-critical values are sufficiently unpredictable.

- These mental models will help you interpret Sequencer's findings more effectively.

---

## Mental Model 1 — Predictability Is the Enemy

- The security question is not:

  > "Is this random?"

- The security question is:

  > **"Could an attacker predict future values?"**

- A value only needs to be predictable enough for an attacker to gain an advantage.

- Think in terms of attacker capability, not mathematical perfection.

---

## Mental Model 2 — Evidence Over Appearance

- Humans naturally judge randomness by appearance.

- That instinct is unreliable.

- These values appear random:

  ```text
  8F3A12
  91BC44
  E72FD1
  ```

- Appearance alone tells you very little about how they were generated.

- Always rely on evidence rather than intuition.

---

## Mental Model 3 — Statistics Support Judgment

- Sequencer produces statistical observations.

- Those observations are evidence—not verdicts.

- Professional workflow:

  ```text
  Collect

  ↓

  Analyze

  ↓

  Interpret

  ↓

  Validate

  ↓

  Conclude
  ```

- Never skip the interpretation stage.

---

## Mental Model 4 — One Result Is Never the Whole Story

- No individual statistical test determines whether a token generator is secure.

- Instead, consider:

  * Overall assessment
  * Multiple statistical indicators
  * Sample quality
  * Collection method
  * Application context

- Strong conclusions require multiple pieces of evidence.

---

## Mental Model 5 — Sample Quality Matters

- Garbage in.

- Garbage out.

- Ask yourself:

  * Did I collect enough values?
  * Did all values come from the same process?
  * Did application behavior change during collection?
  * Am I analyzing the correct token?

- Poor data produces unreliable conclusions.

---

## Mental Model 6 — Context Determines Risk

- Suppose two values are predictable.

- One is:

  * A request ID used for logging.

- The other is:

  * A session identifier used for authentication.

- The statistical observation may be similar.

- The security impact is dramatically different.

- Always evaluate predictability in context.

---

## Mental Model 7 — Validation Completes the Investigation

- Suppose Sequencer suggests that session identifiers may not be sufficiently unpredictable.

- Do not stop there.

- Ask:

  * Can the pattern be reproduced?
  * Is there another explanation?
  * What additional evidence can I collect?
  * Does the observed behavior create an exploitable condition?

- Validation transforms observations into defensible conclusions.

---

## Prediction Challenge

- You collect 2,000 session identifiers.

- Sequencer reports generally strong statistical results, but one individual test produces an unusual outcome.

- Before reaching a conclusion, consider:

  * Does one unexpected result outweigh the others?
  * Should you collect more samples?
  * Could environmental factors affect the dataset?
  * What additional testing would strengthen your confidence?

- Focus on the complete body of evidence rather than a single number.

---

## Professional Habits

- Experienced testers:

  * Begin with a hypothesis.
  * Collect high-quality samples.
  * Interpret results carefully.
  * Avoid relying on appearance.
  * Validate significant observations.
  * Document evidence before reporting.

---

## Key Takeaways

* Predictability—not appearance—is the security concern.
* Statistical analysis informs judgment; it does not replace it.
* Multiple observations provide stronger evidence than a single result.
* Sample quality directly affects confidence.
* Context and validation are essential before reporting findings.
