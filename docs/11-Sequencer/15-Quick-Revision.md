# Quick Revision

> **Estimated Reading Time:** 2–3 Minutes

- Use this page before:

  * Technical interviews
  * Penetration tests
  * Bug bounty hunting
  * Labs
  * CTFs
  * Practice sessions

---

## What is Sequencer?

- Burp Sequencer evaluates the unpredictability of security-critical values using statistical analysis.

- Common targets include:

  * Session identifiers
  * Authentication tokens
  * Password reset tokens
  * CSRF tokens
  * API authentication tokens
  * Other security-sensitive identifiers

- Its purpose is to help answer:

  > **"Could an attacker reasonably predict future values?"**

---

## Investigation Workflow

```text
Identify Security-Critical Value

↓

Define Objective

↓

Collect Consistent Samples

↓

Run Sequencer

↓

Interpret Results

↓

Validate

↓

Document Findings
```

- Remember:

  - Sequencer provides evidence.

- You provide judgment.

---

## Core Concepts

| Concept          | Meaning                                                  |
| ---------------- | -------------------------------------------------------- |
| Unpredictability | Difficult to guess future values                         |
| Entropy          | Degree of uncertainty                                    |
| Bias             | Uneven statistical distribution                          |
| Sample Size      | Number of collected values                               |
| Confidence       | Strength of the statistical evidence                     |
| Validation       | Confirming observations through additional investigation |

---

## High-Value Targets

- Prioritize analyzing:

  * Session identifiers
  * Password reset tokens
  * CSRF tokens
  * Authentication tokens
  * Security-sensitive custom tokens

- Avoid spending time analyzing values that do not protect security-critical functions.

---

## What Sequencer Does

- ✔ Collects security-critical values.

- ✔ Performs statistical analysis.

- ✔ Highlights observations that deserve investigation.

- ✔ Supports evidence-based assessments.

---

## What Sequencer Does NOT Do

- ✘ Prove randomness.

- ✘ Guarantee security.

- ✘ Identify the implementation algorithm.

- ✘ Confirm exploitability.

Human interpretation remains essential.

---

## Investigation Questions

- Before analysis, ask:

  1. What value am I evaluating?
  2. Why is it security-critical?
  3. What would predictability allow an attacker to do?
  4. What evidence would justify a conclusion?

---

## Professional Rules

### Rule 1

- Select the correct token.

---

### Rule 2

- Collect consistent samples.

---

### Rule 3

- Interpret the complete report.

---

### Rule 4

- Validate unusual observations.

---

### Rule 5

- Document evidence and limitations.

---

## Common Mistakes

- ❌ Analyzing the wrong value.

- ❌ Using very small datasets.

- ❌ Mixing unrelated token types.

- ❌ Trusting appearance instead of evidence.

- ❌ Reporting conclusions without validation.

---

## Interview Revision

- Can you confidently explain:

  * What Sequencer does?
  * Why predictability matters?
  * Entropy?
  * Statistical bias?
  * Sample size?
  * Confidence?
  * Validation?
  * Professional workflow?
  * Common mistakes?
  * Investigation scenarios?

- If not, revisit the relevant lesson.

---

## Investigation Checklist

- Before Analysis:

  * [ ] Correct value selected.
  * [ ] Objective defined.
  * [ ] Consistent collection planned.
  * [ ] Authorization confirmed.

- After Analysis:

  * [ ] Results interpreted.
  * [ ] Significant observations validated.
  * [ ] Context considered.
  * [ ] Findings documented.

---

## Module Summary

- Congratulations!

- You have completed the **Sequencer** module.

- You now understand:

  * Why Sequencer exists.
  * How to evaluate security-critical values.
  * The importance of unpredictability.
  * Core statistical concepts.
  * Professional investigation workflows.
  * Real-world assessment scenarios.
  * Common mistakes.
  * Interview concepts.
  * Practical investigation techniques.

- Most importantly, you've learned to ask:

  > **"Does the available evidence suggest these values are sufficiently unpredictable for their intended security purpose?"**

- That question is far more valuable than simply asking whether something "looks random."
