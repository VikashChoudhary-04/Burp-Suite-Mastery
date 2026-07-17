# Interview Questions

> **Module Progress**

```text
████████████░░░ 12 / 15 Lessons
```

- This lesson helps reinforce the concepts covered throughout the Comparer module.

- Try answering each question **before** checking documentation or your notes.

- The goal is to explain your reasoning clearly, just as you would in a technical interview.

---

## Beginner Questions

### 1. What is Burp Comparer?

- Explain its purpose without describing it as only a "diff tool."

---

### 2. When would you use Comparer?

- Describe at least three practical situations where it adds value during a penetration test.

---

### 3. What kinds of data can Comparer compare?

- Give examples from real assessments.

---

### 4. Does Comparer identify vulnerabilities?

- Explain what Comparer does and what still requires human analysis.

---

### 5. Why is manual comparison difficult?

- Discuss how complexity, response size, and human error affect investigations.

---

## Intermediate Questions

### 6. What is the difference between a cosmetic change and a behavioral change?

- Provide examples of each.

---

### 7. Why should expected variation be ignored?

- Name several values that commonly change between requests.

---

### 8. How would you compare responses from two different users?

- Describe your workflow from capturing requests through validating differences.

---

### 9. How does Comparer support authorization testing?

- Explain how it can help identify differences in access without claiming a vulnerability exists.

---

### 10. How can Comparer help during API testing?

- Discuss comparing response schemas, permissions, and exposed data.

---

## Advanced Questions

### 11. A response changes from `403 Forbidden` to `200 OK`.

- What are the possible explanations?

- What evidence would you collect before reporting a vulnerability?

---

### 12. You compare two JWT payloads and notice a role change.

- What additional testing would you perform?

---

### 13. Two responses differ only by timestamps and request IDs.

- How would you interpret this result?

---

### 14. Comparer highlights an additional `"permissions"` array.

- What investigative questions would you ask next?

---

### 15. Why is context critical when interpreting differences?

- Give an example where the same difference could be harmless in one situation but important in another.

---

## Scenario Questions

### Scenario 1

- You compare two responses:

  * Status changes from `403` to `200`
  * A `"downloadAllowed": true` field appears

- Walk through your investigation process.

---

### Scenario 2

- An API response for an administrator contains several additional fields.

- How would you determine whether those fields represent an authorization issue or expected behavior?

---

### Scenario 3

- Comparer highlights a changed session cookie after login.

- Is this expected?

- What would you verify?

---

### Scenario 4

- After an Intruder attack, one response differs by only a single JSON field.

- How would you determine whether the difference is meaningful?

---

### Scenario 5

- A response body increases by 2 KB after changing a parameter.

- What hypotheses would you consider before drawing conclusions?

---

## Discussion Questions

- These have no single correct answer.

- Explain your reasoning.

  * Why is "What changed?" such an important question in security testing?
  * When is a small difference more important than a large one?
  * How do you distinguish signal from noise?
  * Why should every meaningful difference generate another question?
  * How does Comparer complement Proxy, Repeater, and Intruder?

---

## Self-Assessment

- Can you confidently explain:

  * The purpose of Comparer?
  * Difference analysis?
  * Behavioral vs. cosmetic changes?
  * Expected vs. unexpected variation?
  * A professional comparison workflow?
  * Manual validation after comparison?
  * Common mistakes?
  * Real investigation scenarios?

- Any topic you cannot explain clearly is worth revisiting.

---

## Interview Tips

- During interviews:

  * Explain your reasoning step by step.
  * Use examples from practice labs or authorized assessments.
  * Distinguish observations from conclusions.
  * Emphasize validation before reporting.
  * Demonstrate a structured investigation process.

- Interviewers often value a clear analytical process more than perfect terminology.
