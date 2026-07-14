# Interview Questions

## Overview

- Understanding Burp Repeater is one thing.

- Explaining it clearly during an interview is another.

- This lesson prepares you for both technical interviews and practical discussions by focusing on reasoning, methodology, and real-world usage rather than memorized definitions.

---

## Learning Objectives

- By the end of this lesson, you will be able to:

  * Answer common interview questions confidently.
  * Explain why Repeater is important.
  * Demonstrate professional investigation methodology.
  * Reason through realistic scenarios.
  * Communicate findings clearly.

---

## Prerequisites

- Complete all previous Repeater lessons.

---

## Difficulty

🟠 Intermediate

---

## Estimated Time

| Activity        |         Time |
| --------------- | -----------: |
| Reading         |       20 min |
| Self Assessment |       30 min |
| Mock Interview  |       20 min |
| **Total**       | **≈ 70 min** |

---

## Section 1 — Fundamentals

### Question 1

**What is Burp Repeater?**

### Good Answer

- Burp Repeater is a manual testing tool that allows security testers to send, modify, and resend HTTP requests while analyzing server responses. It is primarily used to understand application behavior through controlled experiments.

---

### Question 2

**Why would you use Repeater instead of a browser?**

### Good Answer

- A browser automatically generates requests and limits manual control.

- Repeater allows me to:

  * Modify requests precisely.
  * Replay requests repeatedly.
  * Compare responses.
  * Test hypotheses without repeating browser actions.

---

### Question 3

**What is the difference between Proxy and Repeater?**

### Good Answer

- Proxy captures and intercepts live traffic.

- Repeater works with copies of requests, allowing manual modification and repeated testing without interrupting browser traffic.

---

### Question 4

**Why is Repeater considered a manual testing tool?**

### Good Answer

- Because every request is intentionally created or modified by the tester. Repeater does not automatically generate payloads or scan for vulnerabilities.

---

## Section 2 — Practical Questions

### Question 5

- A request contains:

  ```http
  GET /profile?id=25
  ```

- How would you begin investigating it?

### Good Answer

- I would:

  1. Send the original request to establish a baseline.
  2. Understand what the endpoint does.
  3. Change only the `id` value.
  4. Compare the responses.
  5. Record observations.
  6. Decide the next experiment based on evidence.

---

### Question 6

- Why should only one variable change during testing?

### Good Answer

- Changing one variable isolates the effect of that modification. If multiple values change simultaneously, it becomes difficult to determine which change caused the application's behavior.

---

### Question 7

- What information do you compare between responses?

### Good Answer

- I compare:

  * Status code
  * Headers
  * Cookies
  * Response body
  * Response length
  * Redirects
  * Error messages

- The objective is to understand why the response changed.

---

## Section 3 — Scenario-Based Questions

### Question 8

- You modify a parameter.

- The response changes from:

  ```text
  200 OK
  ```

- to

  ```text
  403 Forbidden
  ```

- What does this tell you?

### Good Answer

- It indicates the server processed the modified request differently. A 403 response suggests access was denied, but I would not draw conclusions immediately. I would investigate why the request was rejected and compare it with the baseline.

---

### Question 9

- You remove the session cookie and still receive:

  ```text
  200 OK
  ```

- What would you do next?

### Good Answer

- I would verify whether authentication is actually required for that endpoint.

- I would:

  * Repeat the experiment.
  * Compare with other endpoints.
  * Observe the returned data.
  * Determine whether the behavior is expected.

---

### Question 10

- You receive a 500 Internal Server Error.

- What is your next step?

### Good Answer

- I would reproduce the issue, identify which modification triggered it, inspect the response for useful details, and determine whether it is consistent before considering any security implications.

---

## Section 4 — Senior-Level Reasoning

### Question 11

- When is an investigation complete?

### Good Answer

- An investigation is complete when I have enough evidence to explain the application's behavior, support my conclusions, and determine appropriate next steps. If important questions remain unanswered, I continue testing.

---

### Question 12

- How do you avoid false positives?

### Good Answer

- I establish a baseline, modify one variable at a time, reproduce interesting behavior, collect evidence, and avoid conclusions that are not supported by consistent observations.

---

### Question 13

- How do you decide what to test next?

### Good Answer

- Each response informs the next experiment. I ask what new question the latest evidence raises and design a controlled test to answer it.

---

### Question 14

- What is more important: sending many requests or understanding each response?

### Good Answer

- Understanding each response. Manual testing is driven by analysis, not request volume. A few carefully designed experiments are often more valuable than hundreds of random requests.

---

## Mock Interview

- Answer the following without looking at previous lessons.

1. Explain Repeater in two minutes.
2. Describe the difference between Proxy and Repeater.
3. Explain why a baseline response is important.
4. Describe your investigation workflow.
5. Explain how you avoid false positives.
6. Walk through a recent lab using Repeater.

- If you cannot answer confidently, revisit the relevant lesson.

---

## Self Assessment

- Rate yourself from **1–5**.

| Skill                  | Rating |
| ---------------------- | :----: |
| Explaining Repeater    |        |
| Building hypotheses    |        |
| Request editing        |        |
| Response analysis      |        |
| Investigation workflow |        |
| Evidence collection    |        |
| Interview confidence   |        |

---

## Quick Revision

* Repeater supports manual investigation.
* Always establish a baseline.
* One variable per experiment.
* Evidence before conclusions.
* Responses guide the next test.
* Think like an investigator.

---

## Mastery Checklist

* [ ] I can explain Repeater clearly.
* [ ] I understand the difference between Proxy and Repeater.
* [ ] I can describe a professional workflow.
* [ ] I know how to analyze responses.
* [ ] I can answer scenario-based questions.
* [ ] I completed the mock interview.
