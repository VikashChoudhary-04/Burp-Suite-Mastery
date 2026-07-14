# Professional Workflow

## Overview

- Knowing how to use Burp Repeater is valuable.

- Knowing **when**, **why**, and **how** to use it during a real penetration test is what separates professionals from beginners.

- This lesson teaches a structured workflow for investigating web applications using Burp Repeater. Rather than sending random requests, you will learn to build hypotheses, collect evidence, validate observations, and document findings.

---

## Learning Objectives

- By the end of this lesson, you will be able to:

  * Apply Repeater within a structured testing methodology.
  * Build and test hypotheses.
  * Perform controlled experiments.
  * Record evidence for every observation.
  * Avoid common testing mistakes.
  * Produce repeatable and defensible results.

---

## Prerequisites

* HTTP for Burp Users
* Proxy
* Target
* Repeater Introduction
* Repeater Interface
* Behind the Scenes
* Request Editing
* Response Analysis

---

## Difficulty

🟠 Intermediate

---

## Estimated Time

| Activity  |          Time |
| --------- | ------------: |
| Reading   |        20 min |
| Practice  |        45 min |
| Exercises |        20 min |
| Challenge |        35 min |
| **Total** | **≈ 120 min** |

---

## Why This Exists

- Many beginners use Repeater like this:

  ```text
  Capture Request

  ↓

  Change Something
  
  ↓

  Click Send
  
  ↓

  Repeat
  ```

- This approach often produces confusing results.

- Professionals follow a repeatable investigation process that makes every request purposeful.

---

## Mental Model

- Think like a scientist.

```text
Observe

↓

Question

↓

Hypothesis

↓

Experiment

↓

Evidence

↓

Conclusion

↓

Next Experiment
```

- Every request should answer a specific question.

---

## The Professional Workflow

```text
Capture Request
        │
        ▼
Understand the Request
        │
        ▼
Establish a Baseline
        │
        ▼
Identify a Test Objective
        │
        ▼
Create a Hypothesis
        │
        ▼
Modify One Variable
        │
        ▼
Send Request
        │
        ▼
Compare Responses
        │
        ▼
Collect Evidence
        │
        ▼
Design the Next Test
```

- Never skip the baseline or hypothesis.

---

### Step 1 — Capture a Good Request

- Choose a request that is relevant to your objective.

- Examples include:

  * Viewing a profile
  * Updating account settings
  * Downloading an invoice
  * Viewing an order
  * Calling an API endpoint

- The better the request, the more meaningful your investigation.

---

### Step 2 — Understand the Request

- Before changing anything, answer:

  * What does this request do?
  * Which user initiated it?
  * Which parameters control the action?
  * Which headers appear important?
  * Is authentication involved?

> [!IMPORTANT]
> Never modify a request you don't understand.

---

### Step 3 — Establish a Baseline

- Send the original request without modification.

- Record:

  * Status code
  * Headers
  * Response body
  * Response length
  * Redirect behavior

- Everything you observe later will be compared against this baseline.

---

### Step 4 — Define the Objective

- Do not test randomly.

- Examples:

  * Verify authorization.
  * Test input validation.
  * Understand business logic.
  * Investigate session handling.
  * Examine API behavior.

- A clear objective guides the rest of the investigation.

---

### Step 5 — Create a Hypothesis

- Examples:

  * "Changing the user ID should return another user's profile."
  * "Removing the session cookie should require authentication."
  * "Changing the HTTP method should be rejected."

- A hypothesis should be specific and testable.

---

### Step 6 — Modify One Variable

- Examples:

  * One parameter
  * One cookie
  * One header
  * One JSON field
  * One HTTP method

- Avoid changing multiple values simultaneously.

---

### Step 7 — Send and Observe

- After sending the request, compare it with the baseline.

- Ask:

  * What changed?
  * What stayed the same?
  * Was the change expected?
  * Does the evidence support my hypothesis?

- Observation comes before interpretation.

---

### Step 8 — Collect Evidence

- Document everything that supports your conclusions.

- Useful evidence includes:

  * Original request
  * Modified request
  * Original response
  * Modified response
  * Status codes
  * Relevant response snippets
  * Screenshots (where appropriate)

- Good reports are built on good evidence.

---

### Step 9 — Design the Next Experiment

- Every result should guide your next test.

- Example:

  ```text
  Change user ID

  ↓

  Response changes

  ↓

  Investigate authorization
  
  ↓

  Change session
  
  ↓

  Compare results

  ↓

  Confirm or reject hypothesis
  ```

- Investigations evolve one experiment at a time.

---

## Real-World Workflow

- Imagine testing an account page.

```text
Capture Request

↓

Baseline

↓

Change account ID

↓

Response changes

↓

Check authorization

↓

Test another account

↓

Document findings

↓

Prepare report
```

- This workflow is repeatable across many types of assessments.

---

## Field Notes

- Experienced testers spend more time thinking than clicking.

- Before every request, ask yourself:

  * Why am I sending this?
  * What do I expect?
  * How will I interpret the response?
  * What evidence will I need?

- These questions improve the quality of your investigations.

---

## Common Mistakes

* Skipping the baseline.
* Testing without a hypothesis.
* Changing multiple values at once.
* Ignoring inconsistent results.
* Failing to document evidence.
* Jumping to conclusions.

---

## Practice Exercises

- Choose three different requests.

- For each request:

  1. Define the objective.
  2. Record the baseline.
  3. Write a hypothesis.
  4. Modify one variable.
  5. Send the request.
  6. Compare the responses.
  7. Record the evidence.
  8. Decide on the next experiment.

---

## Capstone Challenge

- Perform a complete investigation against an authorized practice application.

- Your investigation should include:

  * Objective
  * Baseline
  * Three hypotheses
  * Three controlled experiments
  * Evidence for every observation
  * Final conclusion

- Do not focus on finding a vulnerability.

- Focus on demonstrating a disciplined methodology.

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

## Interview Questions

1. Why is a baseline important?
2. What makes a good hypothesis?
3. Why should only one variable change during an experiment?
4. What evidence should be collected during testing?
5. Why is documentation important?
6. How does a professional investigation differ from random testing?

---

## Quick Revision

* Understand before modifying.
* Always establish a baseline.
* Define a clear objective.
* Build a hypothesis.
* Change one variable.
* Collect evidence.
* Let each result guide the next experiment.

---

## Mastery Checklist

* [ ] I can follow a structured Repeater workflow.
* [ ] I always establish a baseline.
* [ ] I create hypotheses before testing.
* [ ] I collect evidence for every conclusion.
* [ ] I completed the practice exercises.
* [ ] I completed the capstone challenge.
