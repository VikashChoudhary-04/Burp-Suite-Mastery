# Exercises

## Overview

- Knowledge becomes skill only through practice.

- These exercises are designed to help you apply the concepts learned throughout the Intruder module using authorized practice environments such as PortSwigger Web Security Academy, OWASP Juice Shop, DVWA, or other systems where you have permission to test.

- The focus is on designing good experiments, analyzing results, and making evidence-based decisions.

---

## Learning Objectives

- By the end of these exercises, you should be able to:

  * Design focused Intruder attacks.
  * Select appropriate attack types.
  * Choose suitable payload positions and payload types.
  * Analyze response patterns efficiently.
  * Verify interesting results manually.

---

## Prerequisites

- Complete all previous Intruder lessons.

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

  * Define an objective.
  * Build a baseline in Repeater.
  * Select the simplest attack type.
  * Use the smallest practical payload set.
  * Record observations.
  * Verify interesting responses manually.

---

## Level 1 — Planning

### Exercise 1

- Choose one HTTP request.

- Identify:

  * Payload positions
  * Suitable attack type
  * Suitable payload type

- Explain your choices.

---

### Exercise 2

- Write one investigation question.

- Example:

  > "How does this endpoint behave when nearby resource identifiers are requested?"

- Design an Intruder attack that answers the question.

---

## Level 2 — Automation

### Exercise 3

- Configure a Sniper attack.

- Record:

  * Payload position
  * Payload values  
  * Expected number of requests

---

### Exercise 4

- Run a small Intruder attack.

- After completion:

  * Sort by status code.
  * Sort by response length.
  * Identify two unusual responses.

- Explain why they stand out.

---

## Level 3 — Analysis

### Exercise 5

- Choose one interesting response.

- Return it to Repeater.

- Document:

  * Baseline
  * Difference observed
  * Manual verification
  * Conclusion

---

### Exercise 6

- Compare three responses.

- Record:

  * Status code
  * Response length
  * Key differences
  * Possible explanations

- Remember:

  - Differences are observations—not conclusions.

---

## Level 4 — Capstone Investigation

- Conduct a complete Intruder investigation.

- Your report should include:

### Objective

- What question are you trying to answer?

---

### Baseline

- Document the original request and response.

---

### Hypothesis

- What do you expect to observe?

---

### Payload Design

- Record:

  * Payload positions
  * Payload type
  * Attack type
  * Payload values

- Explain why each choice supports your objective.

---

### Execution

- Record:

  * Number of requests
  * Duration
  * Observations

---

### Results Analysis

- Review:

  * Status codes
  * Response lengths
  * Response times (when relevant)
  * Grep matches
  * Interesting responses

---

### Manual Verification

- Return interesting responses to Repeater.

- Confirm reproducibility.

- Collect evidence.

---

### Conclusion

- Answer:

  * Did the results support your hypothesis?
  * What evidence supports your conclusion?
  * What would you investigate next?

---

## Investigation Journal

| Step            | Notes |
| --------------- | ----- |
| Objective       |       |
| Baseline        |       |
| Hypothesis      |       |
| Payload Design  |       |
| Attack Type     |       |
| Observations    |       |
| Evidence        |       |
| Conclusion      |       |
| Next Experiment |       |

---

## Reflection

- After completing the exercises, answer:

  1. Which attack type did you use most often?
  2. Which response surprised you?
  3. What made one response more interesting than the others?
  4. Did your hypothesis change after reviewing the evidence?
  5. What would you improve in your next experiment?

---

## Self Assessment

- Can you confidently:

  * Select payload positions?
  * Choose payload types?
  * Select attack types?
  * Design focused payload sets?
  * Analyze Intruder results?
  * Return to Repeater for verification?
  * Explain every conclusion with evidence?

- Any **No** answer identifies a topic to review.

---

## Professional Checklist

- Before every Intruder attack:

  * [ ] I understand the request.
  * [ ] I have a baseline.
  * [ ] I have a clear objective.
  * [ ] I have a hypothesis.
  * [ ] I selected the correct attack type.
  * [ ] My payload list is focused.
  * [ ] My request count is reasonable.
  * [ ] My target is within my authorized scope.

---

## Mastery Challenge

- Using an authorized practice application:

  1. Capture a request.
  2. Build a baseline.
  3. Design a focused Intruder attack.
  4. Analyze the results.
  5. Verify interesting responses manually.
  6. Write a one-page investigation report supported by evidence.
