# Exercises

> **Module Progress**

```text
█████████████░░ 13 / 15 Lessons
```

- These exercises are designed to strengthen your ability to evaluate the unpredictability of security-critical values using a structured, evidence-based approach.

- For every exercise:

  1. Define the objective.
  2. Identify the security-critical value.
  3. Form a hypothesis.
  4. Review the available evidence.
  5. Decide what additional validation is needed.
  6. Record your conclusions.

---

## Learning Objectives

- By completing these exercises, you should be able to:

  * Identify values suitable for Sequencer analysis.
  * Distinguish predictable-looking patterns from unsupported assumptions.
  * Understand the importance of sample size.
  * Interpret statistical observations cautiously.
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

  * Focus on evidence rather than appearance.    
  * Explain your reasoning.
  * Avoid unsupported conclusions.
  * Consider application context.
  * Identify what additional validation would strengthen confidence.

---

## Level 1 — Identify the Right Target

- Decide whether each value should be analyzed with Sequencer.

- Explain **why**.

---

### Exercise 1

- Session identifier

---

### Exercise 2

- Password reset token

---

### Exercise 3

- Analytics tracking cookie

---

### Exercise 4

- CSRF token

---

### Exercise 5

- Request ID used only for logging

---

## Level 2 — Reason About Predictability

- For each situation, answer:

  * Does the observation justify further investigation?
  * What additional evidence would you collect?

---

### Scenario 1

```text
ABC001
ABC002
ABC003
ABC004
```

---

### Scenario 2

```text
9F7A2C
41D8E1
A8C4F0
```

---

### Scenario 3

- A statistical test reports an unusual result, while the remaining tests appear normal.

---

### Scenario 4

- Only 30 samples have been collected.

---

### Scenario 5

- The application changed its authentication workflow during collection.

---

## Level 3 — Investigation Planning

- Describe your workflow for:

---

### Scenario 1

- Evaluating session identifiers.

---

### Scenario 2

- Reviewing password reset tokens.

---

### Scenario 3

- Assessing CSRF tokens.

---

### Scenario 4

- Analyzing API authentication tokens.

---

### Scenario 5

- Investigating a proprietary invitation code system.

- For each scenario, describe:

  * Objective
  * Token to analyze
  * Collection method
  * Validation steps
  * Expected outcome

---

## Level 4 — Prediction Challenge

- You collected 2,000 session identifiers.

- Sequencer reports:

  * Overall statistical results appear strong.
  * One individual statistical test reports an unexpected result.
  * No obvious repeating values are observed.
  * Collection conditions remained stable.

- Answer:

  1. Would you immediately report a vulnerability?
  2. Why or why not?
  3. What additional testing would you perform?
  4. How would you document the limitations of your conclusion?
  5. What evidence would increase your confidence?

---

## Investigation Journal

- Perform an authorized Sequencer assessment and record:

| Step                     | Notes |
| ------------------------ | ----- |
| Objective                |       |
| Token Type               |       |
| Sample Size              |       |
| Collection Conditions    |       |
| Statistical Observations |       |
| Validation Performed     |       |
| Final Assessment         |       |

---

## Reflection

- After completing the exercises, answer:

  1. Which scenario required the most reasoning?
  2. Which observations initially seemed important but required more evidence?
  3. How did application context influence your conclusions?
  4. Why is validation essential?
  5. What would you do differently during your next assessment?

---

## Self-Assessment

- Can you confidently:

  * Identify security-critical values?
  * Explain unpredictability?
  * Recognize the limits of statistical analysis?
  * Plan a professional Sequencer workflow?
  * Interpret observations without overreaching?
  * Validate important findings?

- Any **No** answer highlights a topic worth reviewing.

---

## Professional Checklist

- Before Analysis:

  * [ ] Correct value selected.
  * [ ] Investigation objective defined.
  * [ ] Consistent collection planned.
  * [ ] Appropriate sample size targeted.

- After Analysis:

  * [ ] Results interpreted in context.
  * [ ] Significant observations validated.
  * [ ] Limitations documented.
  * [ ] Conclusions supported by evidence.

---

## Mastery Challenge

- Choose an authorized practice application.

- Evaluate one security-critical value, such as a session identifier or CSRF token.

- For your assessment:

  * Define the objective.
  * Collect a consistent dataset.
  * Analyze the selected value.
  * Interpret the statistical observations.
  * Identify any limitations.
  * Validate notable observations.
  * Produce a concise investigation summary.

- Remember:

  - The goal is not to prove randomness.

- The goal is to determine whether the available evidence suggests that the value is sufficiently unpredictable for its intended security purpose.
