# Common Mistakes

## Overview

- Every penetration tester makes mistakes.

- The difference between a beginner and an experienced tester is not that professionals never make mistakes—it is that they recognize them quickly, understand why they happened, and adjust their approach.

- This lesson covers the most common mistakes made while using Burp Repeater and provides practical guidance on avoiding them.

---

## Learning Objectives

- By the end of this lesson, you will be able to:

  * Recognize common Repeater mistakes.
  * Understand why they occur.
  * Avoid false conclusions.
  * Improve the quality of your investigations.
  * Develop disciplined testing habits.

---

## Prerequisites

* Complete all previous Repeater lessons.

---

## Difficulty

🟡 Beginner–Intermediate

---

## Estimated Time

| Activity   |         Time |
| ---------- | -----------: |
| Reading    |       20 min |
| Practice   |       30 min |
| Reflection |       15 min |
| **Total**  | **≈ 65 min** |

---

## Mistake #1 — Changing Multiple Variables at Once

- Changing several values simultaneously makes it impossible to determine which modification caused the application's behavior to change.

### Poor Approach

```text
Original

↓

Change ID

↓

Change Cookie

↓

Change Header

↓

Send
```

- If the response changes, you don't know which modification caused it.

### Better Approach

```text
Baseline

↓

Change ID

↓

Compare

↓

Reset

↓

Change Cookie

↓

Compare

↓

Reset

↓

Change Header

↓

Compare
```

> [!TIP]
> One experiment should answer one question.

---

## Real Example

**Original Request**

```http
GET /profile?id=15 HTTP/1.1
Cookie: session=abc123
```

**Incorrect Test**

```http
GET /profile?id=16 HTTP/1.1
Cookie: invalid-session
User-Agent: CustomAgent
```

- Three variables changed.

- You cannot confidently explain the result.

**Correct Test**

- Only modify:

  ```text
  id=15
  ```

  ↓

  ```text
  id=16
  ```

- Record the response.

- Reset.

- Then perform the next experiment.

---

## Mistake #2 — No Baseline

- Without the original response, comparisons become unreliable.

- Always send the unmodified request first.

- Record:

  * Status code
  * Response length
  * Important headers
  * Response body

- Everything else should be compared against this baseline.

---

## Mistake #3 — Ignoring Response Headers

- Many beginners immediately scroll to the HTML or JSON.

- Professionals inspect:

  * Status code
  * Headers
  * Cookies
  * Redirects
  * Cache information

before reading the body.

- Headers often explain application behavior.

---

## Real Example

- Suppose the response body looks identical.

- However, you notice:

  ```http
  Set-Cookie: session=new-session-id
  ```

- The application silently issued a new session.

- Ignoring headers would cause you to miss this important behavior.

---

## Mistake #4 — Chasing Every Error Message

- A **500 Internal Server Error** is not automatically a vulnerability.

- Ask:

  * Can I reproduce it?
  * What caused it?
  * Does it expose sensitive information?
  * Is it security-relevant?

- Unexpected behavior deserves investigation—not immediate conclusions.

---

## Mistake #5 — No Clear Objective

- Random modifications rarely produce meaningful results.

- Instead of:

  > "Let's change random values."

- Try:

  > "I want to understand how the application authorizes access to this resource."

- Clear objectives produce focused investigations.

---

## Mistake #6 — Poor Documentation

- If you cannot explain:

  * What you changed,
  * Why you changed it,
  * What happened,

then your investigation is incomplete.

- Good documentation saves time during reporting and makes your findings reproducible.

---

## Mistake #7 — Assuming Every Difference Is a Vulnerability

- Applications naturally behave differently when inputs change.

- Ask yourself:

  * Is this expected?
  * Does it violate the intended security model?
  * Do I have enough evidence?

- A different response is a clue—not proof.

---

## Mistake #8 — Ignoring Reproducibility

- A single unusual response is not enough.

- Repeat the experiment.

- Confirm the same result.

- Change only one variable.

- Reliable findings are reproducible.

---

## Professional Workflow

```text
Baseline

↓

One Question

↓

One Change

↓

Compare

↓

Document

↓

Repeat

↓

Conclusion
```

- Simple workflows reduce mistakes.

---

## Reflection Exercise

- Think about your last Burp lab.

- Answer:

  * Did you establish a baseline?
  * Did you define an objective?
  * Did you change only one variable?
  * Did you document every observation?
  * Did you reproduce unusual behavior?

---

## Practice Exercises

- Choose three requests.

- For each one:

  1. Record the baseline.
  2. Define one objective.
  3. Modify one variable.
  4. Compare responses.
  5. Write a short conclusion.

---

## Capstone Challenge

- Review one of your previous investigations.

- Identify:

  * At least three mistakes you made.
  * How those mistakes affected your conclusions.
  * How you would perform the investigation differently today.

---

## Investigation Journal

| Step             | Your Notes |
| ---------------- | ---------- |
| Mistake Observed |            |
| Why It Happened  |            |
| Correct Approach |            |
| Lesson Learned   |            |

---

## Interview Questions

1. Why should only one variable change during testing?
2. Why is a baseline response important?
3. Why are response headers valuable?
4. Does a different response always indicate a vulnerability?
5. Why should unusual behavior be reproduced before reporting?

---

## Quick Revision

* One change at a time.
* Always establish a baseline.
* Read headers before drawing conclusions.
* Document everything.
* Reproduce unusual behavior.
* Evidence comes before conclusions.

---

## Mastery Checklist

* [ ] I avoid changing multiple variables at once.
* [ ] I always establish a baseline.
* [ ] I inspect response headers.
* [ ] I define a clear objective before testing.
* [ ] I document my investigations.
* [ ] I completed the exercises.
* [ ] I completed the capstone challenge.
