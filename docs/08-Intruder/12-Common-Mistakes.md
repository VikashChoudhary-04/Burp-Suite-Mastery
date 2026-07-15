# Common Mistakes

## Overview

- Burp Intruder is a powerful automation tool, but its effectiveness depends on how thoughtfully it is used.

- Many unproductive investigations are caused not by the tool itself, but by avoidable mistakes in planning, execution, or analysis.

- This lesson highlights common pitfalls and explains how to avoid them.

---

## Learning Objectives

- By the end of this lesson, you will be able to:

  * Recognize common Intruder mistakes.
  * Understand why they occur.
  * Apply better investigation habits.
  * Improve the quality of automated experiments.
  * Build repeatable professional workflows.

---

## Mistake 1 — Starting with Intruder

### Problem

- Opening Intruder before understanding the request.

### Why It Happens

- Automation feels faster than manual investigation.

### Better Approach

- Use Repeater first.

  * Understand the request.
  * Build a baseline.
  * Form a hypothesis.
  * Then automate.

---

## Mistake 2 — No Investigation Objective

### Problem

- Launching an attack without knowing what question you're trying to answer.

### Better Approach

- Write one clear objective before configuring Intruder.

- Example:

  > "I want to observe how nearby product IDs affect the response."

---

## Mistake 3 — Oversized Payload Lists

### Problem

- Loading very large payload lists immediately.

### Risks

* Longer execution time
* More difficult analysis
* Unnecessary request volume

### Better Approach

- Start with a small, focused payload set.

- Expand only when the evidence justifies it.

---

## Mistake 4 — Choosing the Wrong Attack Type

### Problem

- Selecting Cluster Bomb when Sniper would answer the same question.

### Better Approach

- Choose the simplest attack type that satisfies your investigation objective.

---

## Mistake 5 — Too Many Payload Positions

### Problem

- Marking every editable value as a payload position.

### Result

- Large request counts and confusing analysis.

### Better Approach

- Select only the positions directly related to your investigation.

---

## Mistake 6 — Ignoring the Baseline

### Problem

- Reviewing Intruder results without comparing them to the original request.

### Better Approach

- Record:

  * Status code
  * Response length
  * Headers
  * Body

before launching the attack.

---

## Mistake 7 — Reading Responses One by One

### Problem

- Reviewing results sequentially instead of sorting them.

### Better Approach

- Sort by:

  * Status code
  * Response length
  * Response time (when relevant)

- Identify outliers first.

---

## Mistake 8 — Assuming Every Difference Is Significant

### Problem

- Treating every unusual response as evidence of a security issue.

### Better Approach

- Ask:

  * Is it reproducible?
  * Does it differ meaningfully from the baseline?
  * Can I explain it?
  * Do I have enough evidence?

---

## Mistake 9 — Skipping Manual Verification

### Problem

- Reporting conclusions based only on Intruder results.

### Better Approach

- Return interesting requests to Repeater.

- Reproduce the behavior.

- Collect supporting evidence.

---

## Mistake 10 — Poor Documentation

### Problem

- Finishing an investigation without recording:

  * Objective
  * Payloads
  * Observations
  * Conclusions

### Better Approach

- Document while you investigate—not afterward.

---

## Decision Tree

```text id="9n3fwv"
Need Automation?

        │

      No ─────────► Stay in Repeater

        │

       Yes

        │

Have a Baseline?

        │

      No ─────────► Build One

        │

       Yes

        │

Have a Hypothesis?

        │

      No ─────────► Define One

        │

       Yes

        │

Run Intruder
```

---

## Performance & Safety

> [!IMPORTANT]
> Responsible testing means sending only the requests needed to answer your investigation question.

- Before increasing payload size, ask:

  * What have I already learned?
  * What new information do I expect?  
  * Can a smaller experiment answer the same question?

---

## Professional Habits

- Experienced testers:

  * Start small.
  * Build hypotheses.
  * Keep payloads focused.
  * Analyze patterns.
  * Verify manually.
  * Document continuously.

---

## Self-Assessment

- For each statement, answer **Yes** or **No**.

  * I establish a baseline before using Intruder.
  * I choose attack types intentionally.
  * I keep payload lists focused.
  * I review outliers before reading every response.
  * I verify interesting findings in Repeater.
  * I document every investigation.

- Any **No** answer identifies an area to improve.

---

## Interview Questions

1. Why shouldn't Intruder be the first tool you use?
2. Why should payload lists start small?
3. Why is a baseline important?
4. Why should interesting responses be verified manually?
5. What is the biggest mistake beginners make with Intruder?

---

## Quick Revision

* Understand before automating.
* Build a baseline.
* Choose focused payloads.
* Select the simplest attack type.
* Analyze patterns.
* Verify manually.
* Document everything.

---

## Mastery Checklist

* [ ] I recognize common Intruder mistakes.
* [ ] I know how to avoid them.
* [ ] I understand why planning matters.
* [ ] I completed the self-assessment.
