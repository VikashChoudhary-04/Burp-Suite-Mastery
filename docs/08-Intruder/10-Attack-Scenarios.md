# Attack Scenarios

## Overview

- Burp Intruder is not tied to a specific vulnerability.

- Instead, it automates repetitive experiments that help you understand application behavior.

- This lesson presents realistic investigation scenarios where Intruder can efficiently answer well-defined questions during authorized security assessments.

- The focus is on **investigation methodology**, not exploitation.

---

## Learning Objectives

- By the end of this lesson, you will be able to:

  * Recognize situations where Intruder adds value.
  * Design focused automated experiments.
  * Select appropriate attack types.
  * Analyze response patterns.
  * Decide when to return to Repeater.

---

## Prerequisites

* Complete all previous Intruder lessons.
* Complete the Repeater module.

---

## Difficulty

🟠 Intermediate

---

## Estimated Time

| Activity  |          Time |
| --------- | ------------: |
| Reading   |        30 min |
| Practice  |        45 min |
| Exercises |        30 min |
| **Total** | **≈ 105 min** |

---

## Investigation Framework

- Every scenario follows the same workflow.

  ```text id="zgvqv7"
  Observation

  ↓

  Question

  ↓

  Hypothesis

  ↓

  Automation

  ↓

  Pattern Analysis

  ↓

  Manual Verification

  ↓

  Evidence
  ```

- Automation supports investigation—it does not replace it.

---

## Scenario 1 — Sequential Resource Identifiers

### Objective

- Understand how nearby resource identifiers behave.

### Observation

- A request contains:

  ```http id="7xz4t6"
  GET /resource?id=250 HTTP/1.1
  ```

### Question

- Do nearby identifiers produce consistent responses?

### Recommended Attack

* Attack Type: Sniper
* Payload Type: Numbers

### Review

- Sort by:

  * Status code
  * Response length

- Investigate any unusual responses manually.

---

## Scenario 2 — Input Validation

### Objective

- Understand how the application validates input values.

### Investigation Questions

* Are invalid values rejected consistently?
* Are different error messages returned?
* Does validation behave predictably?

### Payloads

- Use a small, focused set of representative values.

- Analyze:

  * Validation messages
  * Status codes
  * Response structure

---

## Scenario 3 — API Parameter Analysis

### Objective

- Observe how an API responds to different parameter values.

### Workflow

1. Capture a baseline.
2. Select one payload position.
3. Configure a small payload set.
4. Launch Intruder.
5. Compare JSON responses.
6. Return interesting responses to Repeater.

---

## Scenario 4 — Business Logic Investigation

### Objective

- Understand how changing client-controlled values influences application behavior.

- Examples include:

  * Quantity
  * Discount code
  * Shipping option
  * Language
  * Region

- Use Intruder to identify patterns.

- Use Repeater to explain them.

---

## Scenario 5 — Response Pattern Analysis

- Suppose an Intruder attack produces:

| Payload | Status | Length |
| ------: | :----: | -----: |
|     101 |   200  |   4210 |
|     102 |   200  |   4208 |
|     103 |   200  |   4211 |
|     104 |   302  |    612 |
|     105 |   200  |   4213 |

- Question:

  - Why is response `104` different?

- The answer comes from manual investigation—not assumptions.

---

## Scenario 6 — Session Behavior

- Objective:

  - Observe how different session-related values influence responses.

- Focus on:

  * Status codes
  * Redirects
  * Cookies
  * Response structure

- Document every observation before drawing conclusions.

---

## Decision Tree

```text id="a6qv5d"
Need One Experiment?

        │

      Yes ─────────► Repeater

        │

       No

        │

Need Pattern Recognition?

        │

      Yes ─────────► Intruder

        │

       No

        │

Return to Manual Investigation
```

- Choose the simplest tool that answers your question.

---

## Professional Workflow

```text id="u6mkc7"
Observe

↓

Question

↓

Baseline

↓

Small Payload Set

↓

Intruder

↓

Sort

↓

Filter

↓

Identify Outliers

↓

Repeater

↓

Evidence
```

---

## Performance & Safety

> [!IMPORTANT]
> Every automated experiment should have:

* A defined objective.
* A controlled payload set.
* A reasonable request count.
* Appropriate authorization.

- Stop attacks that no longer contribute useful information.

---

### Real Example

- Objective:

  - Understand how nearby invoice numbers behave.

- Baseline:

  ```text id="ibf5tr"
  Invoice 420
  ```

- Payloads:

  ```text id="g4r2jb"
  420–430
  ```

- Review:

  * Response lengths
  * Status codes
  * Response body differences

- Investigate only meaningful outliers.

---

## Field Notes

- Professionals automate repetition—not curiosity.

- Curiosity drives the investigation.

- Automation accelerates it.

---

## Common Mistakes

* Using Intruder before understanding the request.
* Large payload lists without a hypothesis.
* Ignoring baseline responses.
* Treating every unusual response as a finding.
* Forgetting manual verification.

---

## Practice Exercises

- For each request:

  1. Write an investigation question.
  2. Decide whether Intruder is appropriate.
  3. Choose an attack type.
  4. Select payload positions.
  5. Select payload types.
  6. Predict which responses would deserve manual investigation.

---

## Interview Questions

1. When is Intruder more appropriate than Repeater?
2. Why should automation begin with a hypothesis?
3. Why are outliers interesting?
4. Why should unusual responses be verified manually?
5. What makes a good automated experiment?

---

## Quick Revision

* Automate only after understanding.
* Build small, focused experiments.
* Analyze patterns.
* Investigate outliers.
* Return to Repeater.
* Evidence before conclusions.

---

## Mastery Checklist

* [ ] I know when Intruder is appropriate.
* [ ] I can design focused automated investigations.
* [ ] I analyze response patterns systematically.
* [ ] I verify interesting responses manually.
* [ ] I completed the practice exercises.
