# Quick Revision

> **Estimated Reading Time:** 2–3 Minutes

- Use this page before:

  * Technical interviews
  * Bug bounty hunting
  * Penetration tests
  * Labs
  * CTFs
  * Practice sessions

---

## What is Comparer?

- Burp Comparer is an analysis tool that identifies differences between two pieces of data.

- It helps you compare:

  * HTTP requests
  * HTTP responses
  * JSON objects
  * Cookies
  * JWTs
  * Headers
  * API responses

- Comparer highlights differences.

- You determine their meaning.

---

## Investigation Workflow

```text
Define Question

↓

Choose Two Related Items

↓

Compare

↓

Classify Differences

↓

Validate

↓

Document
```

- Never compare data without first knowing what question you're trying to answer.

---

## Core Concepts

| Concept               | Meaning                                        |
| --------------------- | ---------------------------------------------- |
| Structural Difference | Data added or removed                          |
| Value Difference      | Existing value changed                         |
| Cosmetic Difference   | Formatting or presentation changed             |
| Behavioral Difference | Application behavior changed                   |
| Expected Variation    | Normal changes (timestamps, request IDs, etc.) |
| Validation            | Confirming observations manually               |

---

## High-Value Differences

- Prioritize investigating:

  * `403` → `200`
  * Role changes
  * Permission changes
  * User ID changes
  * Additional response fields
  * Missing security headers
  * New sensitive information

- These often deserve immediate follow-up testing.

---

## Expected Variation

- Usually **not** a finding:

  * Timestamps
  * Request IDs
  * Session IDs
  * CSRF tokens
  * Random values
  * Nonces

- Expected variation should not distract you from meaningful changes.

---

## Investigation Questions

- Before comparing, ask:

  1. What am I trying to learn?
  2. Which two items should I compare?
  3. Which differences would matter?
  4. How will I validate them?

---

## Comparer in the Burp Workflow

```text
Proxy

↓

Repeater

↓

Intruder (if needed)

↓

Comparer

↓

Validation

↓

Report
```

- Comparer supports investigation.

- It does not replace testing.

---

## Professional Rules

### Rule 1

- Compare with a purpose.

---

### Rule 2

- Context determines significance.

---

### Rule 3

- Behavior matters more than appearance.

---

### Rule 4

- Validate meaningful differences.

---

### Rule 5

- Document confirmed observations.

---

## Common Mistakes

- ❌ Comparing unrelated data.

- ❌ Ignoring context.

- ❌ Treating every difference as important.

- ❌ Reporting observations without validation.

- ❌ Chasing cosmetic changes.

---

## Interview Revision

- Can you confidently explain:

  * What Comparer does?
  * Why it exists?
  * Structural vs. value differences?
  * Cosmetic vs. behavioral changes?
  * Expected vs. unexpected variation?
  * Professional comparison workflow?
  * Real investigation scenarios?
  * Common mistakes?

- If not, revisit the relevant lesson.

---

## Investigation Checklist

- Before comparing:

  * [ ] I have a clear objective.
  * [ ] I selected related evidence.
  * [ ] I understand the expected behavior.

- After comparing:

  * [ ] I classified important differences.
  * [ ] I ignored expected variation.
  * [ ] I validated significant observations.
  * [ ] I documented confirmed findings.

---

## Module Summary

- Congratulations!

- You have completed the **Comparer** module.

- You now understand:

  * Why Comparer exists.
  * How to perform structured comparisons.
  * How to distinguish meaningful differences from expected variation.
  * Professional comparison workflows.
  * Real investigation scenarios.
  * Common mistakes.
  * Interview concepts.
  * Practical investigation techniques.

- Most importantly, you've learned to ask:

  > **"What changed, and why does it matter?"**

- That question lies at the heart of effective web application security testing.
