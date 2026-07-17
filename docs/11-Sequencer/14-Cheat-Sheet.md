# Sequencer Cheat Sheet

> **Purpose:** A quick reference for evaluating the unpredictability of security-critical values.

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

- Never begin analysis without knowing **what question you're trying to answer**.

---

## What to Analyze

| Value                         | Suitable for Sequencer? |
| ----------------------------- | ----------------------- |
| Session identifier            | ✅ Yes                   |
| Password reset token          | ✅ Yes                   |
| CSRF token                    | ✅ Yes                   |
| API authentication token      | ✅ Yes                   |
| One-time verification token   | ✅ Yes                   |
| Invitation or activation code | ✅ Usually               |
| Analytics cookie              | ❌ Usually No            |
| Request ID                    | ❌ Usually No            |
| Logging identifier            | ❌ Usually No            |

- Focus on values whose predictability could create a security risk.

---

## Core Concepts

| Concept          | Meaning                                                  |
| ---------------- | -------------------------------------------------------- |
| Unpredictability | Difficult to guess future values                         |
| Entropy          | Amount of uncertainty                                    |
| Bias             | Uneven distribution of values                            |
| Sample Size      | Number of collected values                               |
| Confidence       | Strength of statistical evidence                         |
| Validation       | Confirming observations through additional investigation |

---

## Investigation Questions

- Before collecting samples, ask:

  * Which value am I analyzing?
  * Why is it security-critical?
  * What would happen if it were predictable?
  * What evidence would support my conclusion?

---

## Collection Checklist

- Before Collection:

  * [ ] Correct token identified.
  * [ ] Investigation objective defined.
  * [ ] Consistent workflow selected.
  * [ ] Authorization confirmed.

- During Collection:

  * [ ] Same token type collected.
  * [ ] Stable application behavior.
  * [ ] No mixed datasets.
  * [ ] Unexpected changes documented.

- After Collection:

  * [ ] Dataset validated.
  * [ ] Analysis completed.
  * [ ] Results interpreted in context.
  * [ ] Significant observations reviewed.

---

## Interpreting Results

- Remember:

  * Statistical observations are evidence.
  * Evidence is not proof.
  * Context determines security impact.
  * Validation strengthens conclusions.

- Avoid treating individual statistical results as final answers.

---

## Common Pitfalls

- ❌ Analyzing the wrong value.

- ❌ Using very small datasets.

- ❌ Mixing different token types.

- ❌ Trusting appearance instead of evidence.

- ❌ Reporting observations without validation.

---

## Decision Tree

```text
Security-Critical Value?

        │

   Yes  ▼  No

Collect Samples

↓

Analyze

↓

Statistical Observation

↓

Meaningful?

  │       │
 No      Yes
   │      │
Record   Validate
          │
          ▼
Document Evidence
```

---

## Professional Workflow

- Experienced testers:

  * Define an objective.
  * Verify the correct token.
  * Collect consistent samples.
  * Interpret the complete report.
  * Validate unusual observations.
  * Document evidence and limitations.

---

## High-Value Targets

- Prioritize:

  * Session identifiers
  * Authentication tokens
  * Password reset tokens
  * CSRF tokens
  * API authentication tokens
  * Security-sensitive custom tokens

- These values directly affect authentication, authorization, or account security.

---

## Quick Self-Check

- Before reporting, ask yourself:

  * Did I analyze the correct value?
  * Was my sample collection consistent?
  * Did I gather enough evidence?
  * Have I considered the application context?
  * Did I validate important observations?

- If any answer is **No**, continue investigating.

---

## Golden Rules

1. Predictability—not appearance—is the security concern.
2. Collect quality data before analyzing.
3. Statistics support judgment; they do not replace it.
4. Context determines security impact.
5. Validate before reporting.
6. Evidence over assumptions.
