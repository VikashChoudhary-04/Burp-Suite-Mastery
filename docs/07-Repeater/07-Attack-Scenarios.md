# Attack Scenarios

## Overview

- This lesson demonstrates how Burp Repeater is used during real-world web application security investigations.

- The purpose is **not** to teach exploitation.

- Instead, it teaches you how to ask good questions, design controlled experiments, collect evidence, and determine whether additional investigation is justified.

- A professional tester is an investigator first and an attacker second.

---

## Learning Objectives

- By the end of this lesson, you will be able to:

  * Convert observations into testable hypotheses.
  * Design structured investigations.
  * Use Repeater to validate application behavior.
  * Collect evidence systematically.
  * Avoid jumping to unsupported conclusions.

---

## Prerequisites

* HTTP Fundamentals
* Proxy
* Target
* Repeater Introduction
* Interface
* Behind the Scenes
* Request Editing
* Response Analysis
* Professional Workflow

---

## Difficulty

🟠 Intermediate

---

## Estimated Time

| Activity  |          Time |
| --------- | ------------: |
| Reading   |        25 min |
| Practice  |        60 min |
| Exercises |        30 min |
| Challenge |        30 min |
| **Total** | **≈ 145 min** |

---

## Why This Lesson Exists

- Many beginners ask:

  > "Which payload should I try?"

- Professionals ask:

  > "What is this request supposed to do, and what assumptions is the server making?"

- Changing that mindset dramatically improves the quality of your testing.

---

## Investigation Framework

- Every scenario in this lesson follows the same process.

```text
Observe

↓

Question

↓

Hypothesis

↓

Controlled Experiment

↓

Evidence

↓

Conclusion

↓

Next Test
```

- Do not skip any step.

---

## Scenario 1 — Can I Access Data That Should Belong to Someone Else?

### Objective

- Understand how the application handles object identifiers.

### Observation

- The request contains an identifier.

- Example:

  ```http
  GET /profile?id=15 HTTP/1.1
  ```

### Questions

* Is the identifier predictable?
* Does changing it alter the response?
* Is access consistently restricted?

### Experiment

- Modify only the identifier.

- Record:

  * Status code
  * Response length
  * Returned data
  * Error messages

### Evidence

- Document every difference compared to the baseline.

### Conclusion

- Do not conclude that a vulnerability exists until you have sufficient evidence that the application violates its intended authorization rules.

---

## Scenario 2 — What Happens Without Authentication?

### Objective

- Understand how the application behaves when authentication information changes.

### Observation

- The request contains session information.

- Examples include:

  * Cookies
  * Authorization headers
  * Bearer tokens

### Questions

* What happens if authentication is removed?
* Does the server reject the request?
* Is a redirect issued?
* Is a new session created?

### Experiment

- Modify only the authentication mechanism.

- Record every change.

---

## Scenario 3 — Can Client-Controlled Values Influence the Result?

### Objective

- Identify values supplied by the client that affect application behavior.

- Examples include:

  * Quantity
  * Currency
  * Language
  * Search filters
  * Sort options

### Questions

* Does changing the value alter the response?
* Is server-side validation apparent?
* Are unexpected values handled consistently?

Your goal is to understand how the application processes user input.

---

## Scenario 4 — How Does the Application Validate Input?

### Objective

- Observe the application's validation behavior.

- Possible experiments include changing:

  * Length
  * Character type
  * Format
  * Required fields

- Questions:

  * Does the application reject invalid input?
  * Is the error consistent?
  * Does the validation appear to occur on the client, the server, or both?

---

## Scenario 5 — Does the Application Behave Consistently?

- Consistency often reveals implementation details.

- Examples:

  Request A
  
  ↓

  200 OK

  Request B

  ↓

  403 Forbidden

  Request C

  ↓

  500 Internal Server Error

- Ask yourself:

  * Why are the responses different?
  * Which input caused the change?
  * Is the behavior expected?

---

## Scenario 6 — How Does the API Respond?

- For API endpoints, compare:

  * Status codes
  * JSON structure
  * Error objects
  * Response fields
  * Missing fields

- Questions:

  * Does every response follow the same structure?
  * Are sensitive fields exposed?
  * Are error messages informative?

- Understanding API behavior is often more valuable than searching for payloads.

---

## Scenario 7 — Can I Reproduce the Result?

- Reproducibility is essential.

- If you observe unusual behavior:

  1. Repeat the experiment.
  2. Confirm the same outcome.
  3. Change only one variable.
  4. Compare the responses again.

- A result that cannot be reproduced should be investigated further before drawing conclusions.

---

## Professional Workflow

```text
Interesting Request

↓

Understand Function

↓

Establish Baseline

↓

Write Hypothesis

↓

Modify One Value

↓

Collect Evidence

↓

Repeat

↓

Decide Next Experiment
```

- This process remains effective regardless of the technology behind the application.

---

## Field Notes

- Experienced testers rarely perform random modifications.

- Every request answers a specific question.

- If you cannot explain why you are changing a value, reconsider the experiment before clicking **Send**.

---

## Common Mistakes

* Testing without an objective.
* Running multiple experiments simultaneously.
* Ignoring inconsistent behavior.
* Treating every error message as a security issue.
* Drawing conclusions from a single response.

---

## Practice Exercises

- Choose three different requests.

- For each request:

  1. Define one investigation question.
  2. Record the baseline.
  3. Write a hypothesis.
  4. Perform one controlled modification.
  5. Compare the responses.
  6. Record the evidence.
  7. Decide what you would test next.

---

## Capstone Challenge

- Perform a complete investigation against an authorized practice application.

- Your report should include:

  * Objective
  * Baseline
  * Three investigation questions
  * Three hypotheses
  * Three controlled experiments
  * Evidence
  * Conclusions
  * Recommended next steps

- Do not attempt to prove a vulnerability.

- Instead, demonstrate a structured investigation process.

---

## Investigation Journal

| Step                   | Your Notes |
| ---------------------- | ---------- |
| Observation            |            |
| Investigation Question |            |
| Hypothesis             |            |
| Experiment             |            |
| Evidence               |            |
| Conclusion             |            |
| Next Test              |            |

---

## Interview Questions

1. Why should testing begin with a question instead of a payload?
2. Why is a baseline response important?
3. Why should experiments be reproducible?
4. Why should only one variable change at a time?
5. What evidence should be collected before drawing conclusions?
6. How does a structured investigation improve testing quality?

---

## Quick Revision

* Ask questions before sending requests.
* Build hypotheses.
* Test one variable at a time.
* Collect evidence.
* Verify observations.
* Reproduce unusual behavior.
* Let each result guide the next experiment.

---

## Mastery Checklist

* [ ] I begin investigations with clear questions.
* [ ] I establish a baseline before testing.
* [ ] I design controlled experiments.
* [ ] I collect evidence systematically.
* [ ] I avoid unsupported conclusions.
* [ ] I completed the exercises.
* [ ] I completed the capstone challenge.
