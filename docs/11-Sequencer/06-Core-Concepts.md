# Core Concepts

> **Module Progress**

```text
██████░░░░░░░░░ 6 / 15 Lessons
```

- Understanding a few core concepts makes Sequencer reports much easier to interpret.

- These concepts focus on security reasoning rather than advanced mathematics.

---

## Concept 1 — Unpredictability

- The primary goal of a security token is to be difficult for an attacker to predict.

- Examples include:

  * Session identifiers
  * Password reset tokens
  * CSRF tokens
  * Authentication tokens

- If an attacker can predict future values with meaningful accuracy, the security of the application may be at risk.

- Unpredictability is the property Sequencer is designed to evaluate.

---

## Concept 2 — Entropy

- Entropy is a measure of uncertainty or unpredictability.

- In practical terms:

  * Higher entropy generally means values are harder to guess.
  * Lower entropy generally means values may be easier to predict.

- Think of entropy as answering:

  > **"How much uncertainty does an attacker face when trying to guess the next value?"**

- Sequencer uses statistical analysis to estimate characteristics related to unpredictability, but interpreting those results still requires judgment.

---

## Concept 3 — Bias

- A bias exists when some values, characters, or patterns appear more frequently than expected.

- For example, imagine the first character of every session token is always:

  ```text
  A
  ```

- Even if the remaining characters vary, that fixed pattern reduces unpredictability.

- Bias does not automatically indicate a vulnerability, but it deserves investigation.

---

## Concept 4 — Sample Size

- Statistical conclusions become more reliable as the number of collected values increases.

- For example:

  * 10 tokens provide limited evidence.
  * 100 tokens provide stronger evidence.
  * 1,000+ tokens often provide a much better basis for analysis.

- Small datasets can produce misleading results.

---

## Concept 5 — Confidence

- Confidence reflects how much trust you can place in the statistical observations produced from the collected dataset.

- Confidence is influenced by factors such as:

  * Sample size
  * Sample quality
  * Consistent collection methods

- Higher confidence does not mean absolute certainty.

- It means the evidence is stronger.

---

## Concept 6 — Pattern vs. Coincidence

- Humans naturally search for patterns.

- Sometimes those patterns are meaningful.

- Sometimes they occur purely by chance.

- Professional testers ask:

  * Can this pattern be reproduced?
  * Does it persist across larger datasets?
  * Is there another explanation?

- Evidence distinguishes a real pattern from coincidence.

---

## Concept 7 — Statistical Evidence

- Sequencer produces statistical observations.

- These observations should be viewed as:

  * Supporting evidence
  * Investigation guidance
  * Indicators for further testing

- They should **not** be treated as automatic proof of security or insecurity.

---

## Core Concept Relationships

```text
Security Token

↓

Collect Samples

↓

Statistical Analysis

↓

Evidence

↓

Interpretation

↓

Validation

↓

Conclusion
```

- Notice that **interpretation** and **validation** remain essential steps.

---

## Prediction Challenge

- You collect 50 session tokens.

- Sequencer reports no obvious statistical concerns.

- Ask yourself:

  * Is 50 a sufficient sample?
  * Would collecting more values increase confidence?
  * Could a larger dataset reveal additional patterns?
  * Would you report the implementation as secure based only on these observations?

- Think about the limits of the available evidence.

---

## Quick Comparison

| Concept          | Practical Meaning                                 |
| ---------------- | ------------------------------------------------- |
| Unpredictability | Difficult to guess future values                  |
| Entropy          | Amount of uncertainty                             |
| Bias             | Uneven distribution of values                     |
| Sample Size      | Number of collected tokens                        |
| Confidence       | Strength of statistical evidence                  |
| Pattern          | Repeating behavior requiring investigation        |
| Validation       | Confirming conclusions through additional testing |

---

## Key Takeaways

* Sequencer evaluates evidence related to unpredictability.
* Higher entropy generally increases guessing difficulty.
* Bias may indicate reduced unpredictability and should be investigated.
* Larger, well-collected datasets produce stronger evidence.
* Statistical observations require interpretation and validation before drawing conclusions.
