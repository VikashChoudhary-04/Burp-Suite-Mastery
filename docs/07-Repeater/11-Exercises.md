# Exercises

## Overview

- Knowledge becomes skill only through practice.

- These exercises are designed to help you apply everything learned throughout the Repeater module. Rather than following a list of clicks, you will practice observing application behavior, forming hypotheses, performing controlled experiments, and documenting evidence.

- Always perform these exercises against **authorized practice environments** such as PortSwigger Web Security Academy, OWASP Juice Shop, DVWA, or other environments where you have permission to test.

---

## Learning Objectives

- By the end of this lesson, you will be able to:

  * Build confidence using Repeater.
  * Conduct structured investigations.
  * Record meaningful observations.
  * Develop repeatable testing habits.
  * Apply a professional workflow during manual testing.

---

## Prerequisites

- Complete all previous Repeater lessons.

---

## Difficulty

🟢 Beginner → 🔴 Advanced

---

## Estimated Time

| Activity  |          Time |
| --------- | ------------: |
| Level 1   |        20 min |
| Level 2   |        30 min |
| Level 3   |        45 min |
| Level 4   |        60 min |
| **Total** | **≈ 155 min** |

---

## Rules

- For every exercise:

  * Establish a baseline.
  * Change only one variable at a time.
  * Record observations.
  * Compare responses.
  * Support conclusions with evidence.

---

## Level 1 — Observe

### Exercise 1

- Choose any request.

- Identify:

  * HTTP method
  * URL
  * Headers
  * Cookies
  * Parameters
  * Request body (if present)

- Do not modify anything.

- Your goal is simply to understand the request.

---

### Exercise 2

- Send the request to Repeater.

- Record:

  * Status code
  * Response headers
  * Response body
  * Response length

- This becomes your baseline.

---

### Exercise 3

- Write a short explanation of what you believe the request does.

---

## Level 2 — Modify

### Exercise 4

- Modify one query parameter.

- Record:

  * Original value
  * Modified value
  * Response differences

---

### Exercise 5

- Modify one request header.

- Document:

  * Why you chose it.
  * Whether the response changed.
  * Possible explanations.

---

### Exercise 6

- Modify one cookie.

- Compare:

  * Authentication state
  * Status code
  * Response body

---

## Level 3 — Investigate

### Exercise 7

- Choose one request.

- Write:

  * Objective
  * Hypothesis

- Then perform one controlled experiment.

- Collect evidence before writing a conclusion.

---

### Exercise 8

- Perform three independent experiments on the same request.

- Only one variable may change in each experiment.

- Record:

  * Modification
  * Response
  * Evidence
  * Conclusion

---

### Exercise 9

- Choose an API request (if available).

- Compare:

  * JSON structure
  * Status code
  * Headers
  * Response length

- Document every meaningful difference.

---

## Level 4 — Mini Pentest

- Choose an authorized practice application.

- Conduct a structured investigation.

- Your report should contain:

### Objective

- What are you trying to understand?

---

### Baseline

- Record the original request and response.

---

### Investigation Questions

- Write at least three questions.

- Examples:

  * What happens if this value changes?
  * How does the application validate this input?
  * How is authentication enforced?

---

### Hypotheses

- Write one hypothesis for each question.

---

### Experiments

- Perform one controlled experiment for each hypothesis.

---

### Evidence

- Include:

  * Requests
  * Responses
  * Status codes
  * Headers
  * Screenshots (optional)
  * Notes

---

### Conclusions

- Explain what you learned.

- Do not claim a vulnerability unless your evidence supports it.

---

## Reflection

- After completing the exercises, answer:

  1. Which experiment taught you the most?
  2. Which response surprised you?
  3. Which assumption proved incorrect?
  4. How did your investigation change after collecting evidence?
  5. What would you test next?

---

## Investigation Journal

| Step       | Your Notes |
| ---------- | ---------- |
| Objective  |            |
| Baseline   |            |
| Hypothesis |            |
| Experiment |            |
| Evidence   |            |
| Conclusion |            |
| Next Test  |            |

---

## Self Assessment

- Can you confidently:

  * Explain every experiment?
  * Justify every modification?
  * Support every conclusion?
  * Reproduce your results?
  * Document your investigation?

- If any answer is **No**, revisit the relevant lesson before continuing.

---

## Quick Revision

* Observe before modifying.
* Build a baseline.
* Form hypotheses.
* Change one variable.
* Collect evidence.
* Reflect on the results.

---

## Mastery Checklist

* [ ] Completed Level 1.
* [ ] Completed Level 2.
* [ ] Completed Level 3.
* [ ] Completed the Mini Pentest.
* [ ] Completed the reflection.
* [ ] Can explain every conclusion with evidence.
