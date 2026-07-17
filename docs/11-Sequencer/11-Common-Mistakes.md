# Common Mistakes

> **Module Progress**

```text id="g3i98g"
███████████░░░░ 11 / 15 Lessons
```

- Sequencer provides valuable statistical evidence, but incorrect assumptions can easily lead to poor conclusions.

- Understanding these common mistakes will help you perform more reliable security assessments.

---

## Mistake 1 — Analyzing the Wrong Value

- One of the most common errors is selecting the wrong token.

- Examples include:

  * Request IDs
  * Tracking cookies
  * Analytics identifiers
  * Logging values

- Instead of:

  * Session identifiers
  * Authentication tokens
  * Password reset tokens
  * CSRF tokens

- Always verify that the value is security-critical before analyzing it.

---

## Mistake 2 — Using Too Few Samples

- Statistical analysis depends on sufficient data.

- Drawing conclusions from a very small dataset can produce misleading results.

- Instead of asking:

  > "Can I analyze this now?"

- Ask:

  > **"Do I have enough evidence to support a conclusion?"**

---

## Mistake 3 — Mixing Different Token Types

- Avoid combining:

  * Session identifiers
  * Password reset tokens
  * CSRF tokens
  * API tokens

- Each may use a completely different generation process.

- Mixed datasets weaken statistical confidence.

---

## Mistake 4 — Trusting Appearance

- Values that *look* random are not necessarily generated securely.

- Likewise, values that appear structured are not automatically insecure.

- Never judge unpredictability by appearance alone.

---

## Mistake 5 — Treating Statistics as Proof

- Sequencer reports statistical observations.

- It does **not**:

  * Prove predictability.
  * Prove security.
  * Identify the implementation algorithm.
  * Confirm exploitability.

- Professional conclusions require interpretation and validation.

---

## Mistake 6 — Ignoring Context

- Suppose Sequencer identifies possible predictability.

- Ask:

  * What does this value protect?
  * Does predictability create meaningful risk?
  * Can an attacker exploit it?

- Context determines security impact.

---

## Mistake 7 — Skipping Validation

- An unusual statistical result should trigger further investigation—not immediate reporting.

- Professional workflow:

  ```text id="c8h94w"
  Observe

  ↓

  Interpret

  ↓

  Validate

  ↓

  Conclude
  ```

- Validation increases confidence in your findings.

---

## Mistake 8 — Ignoring Collection Conditions

- Changes during collection may influence the dataset.

- Examples include:

  * Session expiration
  * CAPTCHA
  * Authentication changes
  * Infrastructure updates
  * Rate limiting

- Always document conditions that may affect your analysis.

---

## Mistake 9 — Reporting Without Evidence

- Avoid writing:

  > "Sequencer says the tokens are weak."

- Instead explain:

  * What was analyzed.
  * How samples were collected.
  * What statistical observations were made.
  * What validation was performed.
  * Why your conclusion is justified.

- Evidence is more persuasive than tool output.

---

## Prediction Challenge

- You collect 80 session identifiers.

- Sequencer reports no obvious statistical weaknesses.

- Would you immediately conclude that the implementation is secure?

- Consider:

  * Is the sample size sufficient?
  * Were the samples collected consistently?
  * Would additional testing improve confidence?
  * What are the limits of your evidence?

---

## Prevention Checklist

- Before Analysis:

  * [ ] Correct token selected.
  * [ ] Clear investigation objective.
  * [ ] Consistent collection method.
  * [ ] Adequate sample size.

- After Analysis:

  * [ ] Reviewed all observations.
  * [ ] Considered application context.
  * [ ] Validated significant findings.
  * [ ] Documented evidence and limitations.

---

## Key Takeaways

* Select the correct token before analysis.
* Build a reliable dataset before interpreting statistics.
* Treat Sequencer as an evidence-gathering tool.
* Consider the security context of every observation.
* Validate findings before reporting them.
