# Professional Workflow

> **Module Progress**

```text id="kk8n2j"
███████░░░░░░░░ 7 / 15 Lessons
```

- Professional testers do not run Sequencer against every value they encounter.

- They first identify **which values are security-critical**, then collect quality samples, interpret the results, and validate their conclusions.

- The goal is to answer one question:

  > **Could an attacker reasonably predict future values?**

---

## Step 1 — Identify Security-Critical Values

- Start by identifying values that protect authentication, authorization, or user identity.

- Common candidates include:

  * Session identifiers
  * Authentication tokens
  * Password reset tokens
  * CSRF tokens
  * One-time verification tokens
  * API authentication tokens
  * Custom security identifiers

- Not every identifier needs Sequencer analysis.

- Focus on values whose predictability could create a security risk.

---

## Step 2 — Define the Investigation

- Before collecting anything, define your objective.

- Examples:

  * Evaluate session identifier unpredictability.
  * Assess password reset token quality.
  * Compare authentication token generation.
  * Review custom token implementation.

- A clear objective prevents collecting irrelevant data.

---

## Step 3 — Collect Consistent Samples

- Collect values under consistent conditions whenever possible.

- For example:

  * Same application workflow
  * Same token type
  * Same environment
  * Same generation process

- Avoid mixing unrelated token types in a single dataset.

- Consistency improves the quality of your analysis.

---

## Step 4 — Run the Analysis

- Once you have collected a sufficiently large and consistent sample:

  1. Select the correct token.
  2. Run Sequencer.
  3. Review the statistical output.
  4. Note any observations that deserve investigation.

- Remember:

  - The report is the beginning of the investigation—not the end.

---

## Step 5 — Interpret the Results

- Ask questions such as:

  * Do the results suggest reduced unpredictability?
  * Are there unusual statistical observations?
  * Could sample quality affect the outcome?
  * Is additional collection required?

- Interpret the complete set of observations rather than focusing on a single statistic.

---

## Step 6 — Validate Your Findings

- If Sequencer suggests a potential weakness:

  * Repeat the collection process.
  * Gather additional samples if needed.
  * Check whether the observation is reproducible.
  * Consider alternative explanations.
  * Assess whether the issue creates an exploitable condition.

- Validation is essential before reporting a security finding.

---

## Workflow Diagram

```text id="v9u89b"
Identify Target

↓

Define Objective

↓

Collect Samples

↓

Run Sequencer

↓

Interpret Results

↓

Validate

↓

Document Findings
```

---

## Real-World Examples

### Session Management

- Objective:

  - Determine whether session identifiers appear sufficiently unpredictable.

---

### Password Reset

- Objective:

  - Evaluate the unpredictability of password reset tokens.

---

### API Authentication

- Objective:

  - Assess whether API authentication tokens follow predictable patterns.

---

### Custom Token Systems

- Objective:

  - Review proprietary identifiers used for security-sensitive operations.

---

## Prediction Challenge

- You collect 1,500 session identifiers from an authorized test environment.

- Sequencer reports generally strong statistical results, but one test indicates a possible bias.

- Before reporting, consider:

  * Would you repeat the collection?
  * Could application behavior have changed during testing?
  * Does the observed bias materially reduce security?
  * What additional evidence would strengthen your conclusion?

- Professional reporting requires more than a single statistical observation.

---

## Professional Checklist

- Before Analysis:

  * [ ] I identified the correct token.
  * [ ] I defined a clear objective.
  * [ ] I collected consistent samples.
  * [ ] I avoided mixing unrelated values.

- After Analysis:

  * [ ] I reviewed the complete report.
  * [ ] I interpreted the results in context.
  * [ ] I validated unusual observations.
  * [ ] I documented evidence and limitations.

---

## Common Pitfalls

- Avoid:

  * Analyzing the wrong value.
  * Using very small sample sizes.
  * Mixing different token types.
  * Treating one failed statistical test as proof.
  * Reporting conclusions without validation.

---

## Key Takeaways

* Start with the security-critical value.
* Define an investigation objective before collecting data.
* Collect high-quality, consistent samples.
* Interpret statistical observations carefully.
* Validate significant findings before reporting them.
