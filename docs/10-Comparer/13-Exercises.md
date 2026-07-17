# Exercises

> **Module Progress**

```text
█████████████░░ 13 / 15 Lessons
```

- These exercises are designed to strengthen your ability to identify, classify, and investigate meaningful differences.

- Do not rush through them.

- For every exercise:

  1. Observe.
  2. Compare.
  3. Classify.
  4. Form a hypothesis.
  5. Validate.
  6. Record your conclusions.

---

## Learning Objectives

- By completing these exercises, you should be able to:

  * Compare related requests and responses.
  * Distinguish cosmetic changes from behavioral changes.
  * Recognize expected variation.
  * Prioritize meaningful differences.
  * Plan follow-up investigations.

---

## Estimated Time

| Level     |          Time |
| --------- | ------------: |
| Level 1   |        30 min |
| Level 2   |        40 min |
| Level 3   |        50 min |
| Level 4   |        60 min |
| **Total** | **≈ 180 min** |

---

## Rules

- For every exercise:

  * Compare only related evidence.
  * Explain *why* a difference matters.
  * Avoid unsupported conclusions.
  * Validate important observations.
  * Document your reasoning.

---

## Level 1 — Spot the Difference

- For each pair, identify:

  * Added values
  * Removed values
  * Modified values
  * Expected changes
  * Potentially meaningful changes

---

### Exercise 1

- Response A

  ```http
  HTTP/1.1 200 OK

  {
    "role": "user"  
  }
  ```

- Response B

  ```http  
  HTTP/1.1 200 OK

  {
    "role": "admin"
  }
  ```

---

### Exercise 2

```http
HTTP/1.1 403 Forbidden
```

↓

```http
HTTP/1.1 200 OK
```

---

### Exercise 3

```json
{
  "name": "Alice"
}
```

↓

```json
{
  "name": "Alice",
  "email": "alice@example.com"
}
```

---

## Level 2 — Classification

- Classify each difference as:

  * Structural or Value
  * Cosmetic or Behavioral
  * Expected or Unexpected

---

### Scenario 1

- Timestamp changed.

---

### Scenario 2

- New `"permissions"` array added.

---

### Scenario 3

- Request ID changed.

---

### Scenario 4

`"role": "user"` became `"role": "admin"`.

---

### Scenario 5

- Response length increased because a new data section appeared.

---

## Level 3 — Investigation Planning

- For each situation:

  - Describe:

    * Your comparison goal.
    * The two items to compare.  
    * The differences you expect.
    * How you would validate them.

---

### Scenario 1

- Authorization testing

---

### Scenario 2

- JWT comparison

---

### Scenario 3

- Cookie analysis

---

### Scenario 4

- API schema comparison

---

### Scenario 5

- Intruder response validation

---

## Level 4 — Difference Challenge

- You compare two API responses.

- Comparer reports:

  * Updated timestamp
  * New request ID
  * `403` → `200`
  * `"role": "user"` → `"role": "admin"`
  * Added `"permissions"` array
  * New `"downloadAllowed": true`
  * Response body increased by 1.5 KB

- Answer:

  1. Rank the differences from highest to lowest investigative priority.
  2. Explain your reasoning.
  3. Which changes are probably expected?
  4. Which changes require manual validation?
  5. Which differences might indicate an authorization issue?

---

## Investigation Journal

- Complete this table for one real comparison performed in an authorized practice environment.

| Step              | Notes |
| ----------------- | ----- |
| Goal              |       |
| Items Compared    |       |
| Differences Found |       |
| Classification    |       |
| Validation        |       |
| Conclusion        |       |

---

## Reflection

- After finishing the exercises, answer:

  1. Which differences were easiest to interpret?
  2. Which required the most context?
  3. Which comparison was most realistic?
  4. Did any differences initially appear important but later prove insignificant?
  5. How did classifying differences improve your investigation?

---

## Self Assessment

- Can you confidently:

  * Compare requests?
  * Compare responses?
  * Distinguish cosmetic vs. behavioral changes?
  * Recognize expected variation?
  * Prioritize investigation?
  * Validate meaningful observations?

- Any **No** answer identifies an area to revisit.

---

## Professional Checklist

- Before using Comparer:

  * [ ] I have a clear investigation goal.
  * [ ] The two items are directly related.
  * [ ] I understand the expected behavior.

- After comparing:

  * [ ] I classified every important difference.
  * [ ] I ignored expected variation.
  * [ ] I validated significant observations.
  * [ ] I documented confirmed findings.

---

## Mastery Challenge

- Choose an authorized practice application.

- Perform:

  * User vs. Admin comparison
  * Before vs. After login comparison
  * JWT comparison
  * Cookie comparison
  * API response comparison

- For each:

  * State your hypothesis.
  * Record the differences.
  * Explain which changes mattered.
  * Validate your observations.
  * Summarize your findings.
