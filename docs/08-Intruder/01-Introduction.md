# Introduction

## Overview

- Burp Intruder is one of the most powerful automation tools in Burp Suite.

- However, its true purpose is often misunderstood.

- Many people think Intruder exists to "brute force" applications.

- In reality, Intruder exists to automate repetitive experiments while allowing the tester to remain in control of the investigation.

- Automation should never replace understanding.

- Instead, it should help you answer important questions more efficiently.

---

## Learning Objectives

- By the end of this lesson, you will be able to:

  * Explain what Burp Intruder is.
  * Understand why Intruder exists.
  * Distinguish automation from investigation.
  * Know when Intruder should and should not be used.
  * Adopt the mindset of professional automation.

---

## Prerequisites

* HTTP Fundamentals
* Proxy
* Target
* Repeater
* Professional Investigation Workflow

---

## Difficulty

🟢 Beginner

---

## Estimated Time

| Activity   |         Time |
| ---------- | -----------: |
| Reading    |       15 min |
| Practice   |       20 min |
| Reflection |       15 min |
| **Total**  | **≈ 50 min** |

---

## Why Does Intruder Exist?

- Imagine you have already investigated a request using Repeater.

- You now want to answer:

  * How does the application respond to 500 different values?
  * Which identifiers produce different responses?
  * Which requests deserve manual investigation?

- Repeater can answer these questions one request at a time.

- Intruder performs the same experiment hundreds or thousands of times while you focus on analyzing the results.

---

## Mental Model

- Think of Repeater and Intruder as partners.

```text
Repeater

↓

Understand

↓

Hypothesis

↓

Intruder

↓

Scale

↓

Pattern Detection

↓

Repeater

↓

Manual Verification
```

- Automation begins **after** understanding.

---

## Repeater vs Intruder

- Suppose you are testing:

  ```http
  GET /profile?id=15
  ```

- Using Repeater:

  ```text
  id=15

  ↓

  id=16

  ↓

  id=17

  ↓

  Observe every response
  ```

- Using Intruder:

  ```text
  Generate IDs

  ↓

  Send hundreds of requests

  ↓

  Sort responses
  
  ↓

  Identify unusual responses

  ↓

  Return interesting requests to Repeater
  ```

- Intruder reduces repetition—not thinking.

---

## Professional Philosophy

- Experienced penetration testers rarely open Intruder first.

- Instead, they follow this sequence:

  ```text
  Understand the Request

  ↓

  Establish a Baseline

  ↓

  Write a Hypothesis

  ↓

  Validate with Repeater

  ↓

  Use Intruder to Scale

  ↓

  Review Results

  ↓

  Return to Repeater
  ```

- This workflow minimizes noise and keeps investigations focused.

---

## Performance & Safety

> [!IMPORTANT]
> Automation can generate a large number of requests.

- Before running an Intruder attack:

  * Confirm that the target is within your authorized scope.
  * Consider the potential impact of your request volume.
  * Use the smallest payload set that answers your question.
  * Stop the attack if it behaves unexpectedly.

- Professional testers aim to minimize unnecessary load while collecting sufficient evidence.

---

## Real Example

- Objective:

  - Determine whether a user identifier behaves consistently.

- Instead of manually testing:

  ```text
  100
  101
  102
  103
  104
  ...
  ```

- Use Intruder to send a controlled sequence.

- Then sort the results by:

  * Status code
  * Response length
  * Response time

- Investigate only the responses that differ meaningfully from the baseline.

---

## Field Notes

- Good automation begins with a good question.

- Poor automation begins with a large wordlist.

- The quality of your payloads matters far more than the size of your payloads.

---

## Common Mistakes

* Opening Intruder before understanding the request.
* Using huge payload lists without an objective.
* Ignoring the baseline response.
* Assuming the longest response is the most interesting.
* Forgetting to verify findings manually.

---

## Practice Exercises

1. Capture one request.
2. Explain what it does.
3. Write one investigation question.
4. Decide whether Repeater or Intruder is the better tool.
5. Explain your reasoning.

---

## Reflection

- Complete the following:

| Question                                    | Your Answer |
| ------------------------------------------- | ----------- |
| What am I trying to learn?                  |             |
| Why would automation help?                  |             |
| Could Repeater answer this instead?         |             |
| What result would require manual follow-up? |             |

---

## Interview Questions

1. What is Burp Intruder?
2. Why does Intruder exist?
3. When should you use Intruder instead of Repeater?
4. Why is automation not a substitute for manual investigation?
5. What precautions should you take before launching an automated attack?

---

## Quick Revision

* Intruder automates controlled experiments.
* Repeater builds understanding.
* Intruder scales understanding.
* Begin with a hypothesis.
* Automate only when it adds value.
* Always verify interesting results manually.

---

## Mastery Checklist

* [ ] I can explain the purpose of Intruder.
* [ ] I understand the relationship between Repeater and Intruder.
* [ ] I know when automation is appropriate.
* [ ] I understand why automation requires planning.
* [ ] I completed the practice exercise.
