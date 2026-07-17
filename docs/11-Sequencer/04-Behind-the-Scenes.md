# Behind the Scenes

> **Module Progress**

```text
████░░░░░░░░░░░ 4 / 15 Lessons
```

- When you click **Analyze**, Burp Sequencer does far more than simply display collected tokens.

- It performs a series of statistical evaluations designed to help determine whether the collected values appear sufficiently unpredictable.

- Understanding this process helps you interpret Sequencer's results correctly.

---

## Step 1 — Collect Values

- Everything begins with data.

- Sequencer first gathers a large number of security-critical values, such as:

  * Session identifiers
  * Authentication tokens
  * Password reset tokens
  * CSRF tokens

- A larger dataset generally provides more meaningful statistical analysis than a small sample.

- Poor input leads to poor conclusions.

---

## Step 2 — Extract the Target

- Sequencer isolates the value you selected.

- For example:

  ```http
  Set-Cookie: SESSIONID=4B7E9A8F...
  ```

- Only the session identifier is analyzed.

- The surrounding HTTP request or response is not the focus.

- Correct extraction is essential.

---

## Step 3 — Perform Statistical Tests

- Sequencer applies multiple statistical tests to the collected dataset.

- Conceptually, it asks questions such as:

  * Are values evenly distributed?
  * Do some patterns occur more often than expected?  
  * Are certain bits unusually predictable?
  * Is there measurable bias?

- No single test determines whether a generator is secure.

- The overall picture matters.

---

## Step 4 — Summarize the Evidence

- After completing its analysis, Sequencer summarizes the statistical observations.

- The report helps you identify:

  * Strong indicators of unpredictability
  * Areas that deserve further investigation
  * Possible statistical weaknesses

- These observations support your investigation.

- They are not proof of a vulnerability.

---

## What Sequencer Does *Not* Do

- Sequencer does **not**:

  * Read application source code.
  * Understand the token generation algorithm.
  * Determine whether a cryptographic library is implemented correctly.
  * Guarantee that a generator is secure.
  * Guarantee that a generator is insecure.

- It evaluates the evidence available from the collected values.

---

## Why Sample Size Matters

- Imagine flipping a fair coin:

- Five flips may produce:

  ```text
  HHHHH
  ```

- That does not prove the coin is biased.

- Five thousand flips provide much stronger evidence.

- The same principle applies to session tokens.

- Small datasets can produce misleading conclusions.

---

## Investigation Workflow

```text
Collect Values

↓

Extract Target

↓

Run Statistical Tests

↓

Review Results

↓

Interpret Evidence

↓

Validate Findings
```

- Sequencer provides evidence.

- The tester provides judgment.

---

## Prediction Challenge

- Suppose Sequencer reports that one statistical test produced an unexpected result.

- Ask yourself:

  * Does one failed test prove predictability?
  * Could the result be caused by sample size?
  * Should additional samples be collected?
  * What follow-up testing would increase confidence?

- Avoid treating a single statistic as definitive proof.

---

## Common Misunderstandings

### "Sequencer can tell me the algorithm."

- No.

- It analyzes the output—not the implementation.

---

### "Passing every test guarantees security."

- No.

- Statistical analysis cannot prove a generator is secure.

---

### "One failed test proves vulnerability."

- No.

- Unexpected statistical observations require further investigation.

---

## Key Takeaways

* Sequencer analyzes collected values, not application code.
* Multiple statistical tests contribute to the overall assessment.
* Sample quality and sample size influence the results.
* Statistical evidence supports investigation but does not replace validation.
* Security conclusions should be based on evidence, context, and follow-up testing.
