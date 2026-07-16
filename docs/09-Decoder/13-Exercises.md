# Exercises

## Overview

- Recognition is a practical skill.

- The goal of these exercises is not to memorize encoding formats, but to develop the habit of observing data, recognizing patterns, forming hypotheses, and verifying them with Decoder.

- Complete these exercises using authorized practice environments, sample values, or your own Burp Suite traffic.

---

## Learning Objectives

- By the end of these exercises, you should be able to:

  * Recognize common data formats.
  * Select appropriate Decoder transformations.
  * Explain your reasoning.
  * Verify transformed output.
  * Integrate Decoder naturally into your workflow.

---

## Difficulty

🟢 Beginner → 🔴 Advanced

---

## Estimated Time

| Level     |          Time |
| --------- | ------------: |
| Level 1   |        25 min |
| Level 2   |        35 min |
| Level 3   |        45 min |
| Level 4   |        60 min |
| **Total** | **≈ 165 min** |

---

## Rules

- For every exercise:

  * Observe first.
  * Form a hypothesis.
  * Apply one justified transformation.
  * Verify the result.
  * Explain your reasoning.

- Do not guess.

---

## Level 1 — Recognition

- For each value:

  1. Identify the most likely format.
  2. List the recognition clues.
  3. Choose the first Decoder transformation.

---

### Exercise 1

```text
QnVycCBTdWl0ZQ==
```

---

### Exercise 2

```text
%2Fadmin%2Fsettings
```

---

### Exercise 3

```text
48656c6c6f20576f726c64
```

---

### Exercise 4

```text
eyJhbGciOiJIUzI1NiJ9.eyJ1c2VyIjoidGVzdCJ9.signature
```

---

### Exercise 5

```text
5d41402abc4b2a76b9719d911017c592
```

---

## Level 2 — Context Matters

- For each scenario:

  * Explain where the value appears.
  * Explain why that context matters.
  * Decide whether Decoder should be used.

---

### Scenario 1

- A URL parameter contains:

  ```text
  %7B%22id%22%3A42%7D
  ```

---

### Scenario 2

- A cookie contains:

  ```text
  QWRtaW46VXNlcg==
  ```

---

### Scenario 3

- A JSON response contains:

  ```text
  48656c6c6f
  ```

---

## Level 3 — Investigation Workflow

- Choose an intercepted value from an authorized practice application.

- Document:

  * Observation
  * Recognition clues
  * Hypothesis
  * First transformation
  * Output
  * Verification
  * Conclusion

---

## Level 4 — Recognition Challenge

- Without using Decoder initially, classify each value.

- Explain:

  * Likely format
  * Recognition clues
  * First transformation
  * Whether another transformation may be required

```text
%252Fapi%252Fv1%252Fusers

SGVsbG8lMjBXb3JsZA==

48656c6c6f20576f726c64

eyJhbGciOiJIUzI1NiJ9.eyJyb2xlIjoiYWRtaW4ifQ.signature

2c26b46b68ffc68ff99b453c1d30413413422d706483bfa0f98a5e886266e7ae
```

- Only after making your predictions should you use Decoder.

- Compare your observations with the actual results.

---

## Investigation Journal

| Step              | Notes |
| ----------------- | ----- |
| Observation       |       |
| Recognition Clues |       |
| Hypothesis        |       |
| Transformation    |       |
| Output            |       |
| Verification      |       |
| Conclusion        |       |

---

## Reflection

- After completing the exercises, answer:

  1. Which format was easiest to recognize?
  2. Which format was most difficult?
  3. Did context change your interpretation?
  4. Did any value require multiple transformations?
  5. What pattern recognition skills improved the most?

---

## Self Assessment

- Can you confidently:

  * Recognize URL encoding?
  * Recognize Base64?
  * Recognize hexadecimal?
  * Recognize JWTs?
  * Distinguish hashes from encodings?
  * Explain encoding vs. encryption?
  * Explain why context matters?

- Any **No** answer identifies a topic to revisit.

---

## Professional Checklist

- Before using Decoder:

  * [ ] I observed the value.
  * [ ] I reviewed the surrounding context.
  * [ ] I identified recognition clues.  
  * [ ] I formed a hypothesis.
  * [ ] I applied one justified transformation.
  * [ ] I verified the output.

---

## Mastery Challenge

- Using an authorized practice application:

  1. Capture several transformed values.
  2. Predict each format before opening Decoder.
  3. Record your reasoning.
  4. Verify each prediction.
  5. Write a one-page summary describing how recognition influenced your investigation.
