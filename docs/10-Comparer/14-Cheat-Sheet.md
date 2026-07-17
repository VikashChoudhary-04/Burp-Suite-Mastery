# Comparer Cheat Sheet

> **Purpose:** A quick reference for performing structured comparisons during penetration testing.

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

- Never compare data without first defining **what you're trying to learn**.

---

## What to Compare

| Investigation         | Compare                        |
| --------------------- | ------------------------------ |
| Authorization Testing | User vs. Admin responses       |
| Authentication        | Before vs. After login         |
| IDOR                  | Object A vs. Object B          |
| API Testing           | Anonymous vs. Authenticated    |
| JWT Analysis          | Token A vs. Token B            |
| Cookie Analysis       | Before vs. After state changes |
| Intruder Results      | Interesting responses          |

---

## Difference Categories

### Structural

* New fields
* Removed fields
* Added headers
* Removed cookies

Usually changes the structure of the data.

---

## Value

- Examples:

  ```text
  role=user
  ```

  ↓

  ```text
  role=admin
  ```

- Structure stays the same.

- Value changes.

---

## Cosmetic

- Examples:

  * Whitespace
  * Formatting
  * Pretty printing
  * Line ordering (when not significant)

- Usually low priority.

---

## Behavioral

- Examples:

  * `403 → 200`
  * New permissions
  * Extra account data
  * Changed authorization
  * Additional API capabilities

- Highest priority.

---

## Expected Variation

- Normally changes between requests:

  * Timestamp
  * Request ID
  * Session ID
  * CSRF token
  * Nonce
  * Random values

- Recognize these quickly and move on.

---

## High-Value Differences

- Prioritize investigating:

  * Status code changes
  * Authorization headers
  * User IDs
  * Roles
  * Permissions
  * Account ownership
  * Security headers
  * Response schema changes

- These often indicate meaningful behavioral differences.

---

## Comparison Decision Tree

```text
Difference Found

        │

        ▼

    Expected?

     │     │
    Yes    No
     │     │
  Ignore  Classify
             │
             ▼
         Behavioral?
          │      │
         Yes     No
          │      │
       Validate  Record
```

- Evidence determines the next step.

---

## Investigation Checklist

- Before comparing:

  * [ ] I know my objective.
  * [ ] I selected related evidence.
  * [ ] I understand the expected behavior.

- During comparison:

  * [ ] I reviewed every highlighted difference.
  * [ ] I separated signal from noise.
  * [ ] I prioritized behavioral changes.

- After comparison:

  * [ ] I validated important observations.
  * [ ] I documented confirmed findings.
  * [ ] I planned follow-up testing if needed.

---

## Common Mistakes

- ❌ Comparing unrelated responses.

- ❌ Treating every difference as important.

- ❌ Ignoring expected variation.

- ❌ Reporting observations without validation.

- ❌ Stopping after the first interesting difference.

---

## Comparer in the Burp Workflow

| Tool         | Purpose                         |
| ------------ | ------------------------------- |
| Proxy        | Capture traffic                 |
| Repeater     | Manual investigation            |
| Intruder     | Automated testing               |
| Decoder      | Understand transformed data     |
| **Comparer** | Identify meaningful differences |

- Comparer helps answer:

  > **"What changed?"**

---

## Difference Prioritization

- Highest Priority:

  * Authorization decisions
  * User identity
  * Role changes
  * Permissions
  * Sensitive data
  * Status codes

- Medium Priority:

  * Response size
  * Response schema
  * Security headers
  * Cookies

- Lower Priority:

  * Timestamps
  * Request IDs
  * Formatting
  * Whitespace

- Always verify before reporting.

---

## Professional Habits

- Experienced testers:

  * Compare with a purpose.
  * Focus on behavior.
  * Ignore expected variation.
  * Validate manually.
  * Record observations immediately.

---

## Quick Difference Quiz

- Which difference deserves the highest priority?

* Timestamp changed
* `403` → `200`
* Request ID changed
* Pretty-printed JSON
* `"role": "admin"` added

- Explain your reasoning before investigating.

---

## Golden Rules

1. Compare with a question.
2. Context determines meaning.
3. Small changes can have large effects.
4. Validate before concluding.
5. Evidence over assumptions.
