# Professional Workflow

## Overview

- Knowing how to configure Burp Intruder is only part of the skill.

- Professional testers use Intruder as one stage in a larger investigation process. They do not begin with automation—they begin with understanding.

- This lesson presents a structured workflow that integrates Repeater and Intruder into a repeatable methodology for authorized web application security assessments.

---

## Learning Objectives

- By the end of this lesson, you will be able to:

  * Integrate Intruder into a professional testing workflow.
  * Decide when automation is appropriate.
  * Design efficient experiments.
  * Analyze results systematically.
  * Return to manual verification before drawing conclusions.

---

## Prerequisites

* Complete all previous Intruder lessons.
* Complete the Repeater module.

---

## Difficulty

🟠 Intermediate

---

## Estimated Time

| Activity   |         Time |
| ---------- | -----------: |
| Reading    |       25 min |
| Practice   |       40 min |
| Reflection |       20 min |
| **Total**  | **≈ 85 min** |

---

## Why This Workflow Exists

- A common beginner workflow looks like this:

  ```text
  Open Intruder

  ↓

  Load Large Wordlist

  ↓

  Start Attack

  ↓

  Hope Something Interesting Happens
  ```

- This approach often creates unnecessary traffic and difficult-to-analyze results.

- Professional testers work differently.

---

## The Professional Workflow

```text
Capture Request
        │
        ▼
Understand the Request
        │
        ▼
Establish a Baseline (Repeater)
        │
        ▼
Define an Objective
        │
        ▼
Write a Hypothesis
        │
        ▼
Design a Small Payload Set
        │
        ▼
Choose Attack Type
        │
        ▼
Run Intruder
        │
        ▼
Analyze Results
        │
        ▼
Return Interesting Requests to Repeater
        │
        ▼
Collect Evidence
        │
        ▼
Draw Conclusions
```

- Automation supports investigation—it does not replace it.

---

### Step 1 — Understand the Request

- Before opening Intruder, ask:

  * What does this request do?
  * Which values control its behavior?
  * Which values can the client influence?
  * What question am I trying to answer?

- If you cannot answer these questions, continue investigating in Repeater first.

---

### Step 2 — Establish a Baseline

- Use Repeater to send the original request.

- Record:

  * Status code
  * Response length
  * Headers
  * Body
  * Timing (if relevant)

- Every Intruder result should be compared with this baseline.

---

### Step 3 — Define the Objective

- Examples:

  * Understand how sequential IDs behave.
  * Observe validation for different input values.
  * Identify unusual responses across a controlled set of requests.

- A clear objective guides every later decision.

---

### Step 4 — Write a Hypothesis

- Examples:

  * "Nearby resource IDs should produce similar responses."
  * "Changing this parameter should not affect authorization."
  * "Invalid values should trigger consistent validation."

- A hypothesis gives your experiment purpose.

---

### Step 5 — Build a Focused Payload Set

- Start with the smallest payload list that can answer your question.

- Avoid loading large generic lists without a reason.

- Good payload design improves both efficiency and analysis.

---

### Step 6 — Choose the Right Attack Type

- Ask:

  * One changing value? → Sniper
  * Same value in multiple places? → Battering Ram
  * Related lists? → Pitchfork
  * Every combination? → Cluster Bomb

- Choose the simplest attack that answers your question.

---

### Step 7 — Run the Attack

- Before clicking **Start Attack**, verify:

  * Payload positions
  * Payload values
  * Estimated request count
  * Target authorization
  * Request rate

- Careful preparation prevents unnecessary reruns.

---

### Step 8 — Analyze Results

- Review:

  * Status codes
  * Response lengths
  * Response times (when relevant)
  * Grep matches
  * Unexpected responses

- Sort and filter before reading individual responses.

---

### Step 9 — Return to Repeater

- Interesting Intruder results should be sent back to Repeater.

- Why?

  - Because manual investigation provides:

    * Better understanding
    * Easier experimentation
    * Stronger evidence
    * Reproducibility

- Intruder discovers patterns.

- Repeater explains them.

---

## Performance & Safety

> [!IMPORTANT]
> Professional testing minimizes unnecessary requests.

- Before increasing payload size:

  * Review current results.
  * Confirm your hypothesis still requires more data.
  * Respect the limits of the authorized engagement.

- Quality of investigation is more important than request count.

---

## Real Example

- Objective:

  - Understand how nearby product IDs behave.

- Workflow:

  1. Verify baseline in Repeater.
  2. Configure a Sniper attack.
  3. Test IDs 240–260.
  4. Sort by response length.
  5. Investigate unusual responses in Repeater.
  6. Record evidence.

---

## Field Notes

- Experienced testers rarely jump directly to large-scale automation.

- They begin with small experiments and expand only when the evidence justifies it.

---

## Common Mistakes

* Skipping the baseline.
* Using oversized payload lists.
* Choosing the wrong attack type.
* Ignoring response analysis.
* Reporting results without manual verification.

---

## Practice Exercises

- Choose one request.

- Complete the entire workflow:

  1. Baseline
  2. Objective
  3. Hypothesis
  4. Payload design
  5. Intruder attack
  6. Results analysis
  7. Manual verification
  8. Final conclusion

---

## Interview Questions

1. Why should Repeater be used before Intruder?
2. Why is a baseline important?
3. How do you decide when automation is appropriate?
4. Why should interesting responses be verified manually?
5. Why should payload lists start small?

---

## Quick Revision

* Understand before automating.
* Establish a baseline.
* Define an objective.
* Build a hypothesis.
* Choose the simplest attack type.
* Analyze before concluding.
* Verify manually.

---

## Mastery Checklist

* [ ] I can integrate Intruder into a professional workflow.
* [ ] I understand when to automate.
* [ ] I design focused payload sets.
* [ ] I verify findings manually.
* [ ] I completed the practice exercise.
