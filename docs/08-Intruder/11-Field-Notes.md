# Field Notes

## Overview

- This lesson collects practical observations and habits that experienced testers develop after using Burp Intruder during many authorized security assessments.

- These notes are not rules.

- They are patterns that repeatedly prove useful in real investigations.

---

## Learning Objectives

- By the end of this lesson, you will be able to:

  * Apply professional habits when using Intruder.
  * Design cleaner experiments.
  * Reduce unnecessary requests.
  * Improve result analysis.
  * Build repeatable investigation workflows.

---

## Field Note 1 — Start Small

- A focused payload list often produces better insights than a massive wordlist.

- Instead of testing 10,000 values immediately:

  1. Test 10.
  2. Analyze.
  3. Expand only if necessary.

---

## Field Note 2 — Repeater Comes First

- Intruder should rarely be the first tool you open.

- A good workflow is:

  ```text
  Proxy
      │
      ▼
  Repeater
      │
      ▼
  Understand
      │
      ▼
  Intruder
  ```

- Understanding should always come before automation.

---

## Field Note 3 — Every Payload Needs a Purpose

- Avoid adding payloads simply because they exist.

- Ask:

  > "What question will this value help answer?"

- If you cannot answer that question, remove the payload.

---

## Field Note 4 — Outliers Matter

- Most responses will look similar.

- Your attention should go to responses that differ from the baseline in meaningful ways.

- Examples include:

  * Different status codes
  * Different response lengths
  * Different redirects
  * Different error messages

---

## Field Note 5 — Smaller Experiments Are Easier to Explain

- A 20-request experiment is usually easier to:

  * Review
  * Reproduce
  * Document
  * Explain

than a 20,000-request experiment.

---

## Field Note 6 — Don't Chase Every Difference

- Not every unusual response indicates a security issue.

- Ask:

  * Is it reproducible?
  * Does it make sense?
  * Does it align with the application's expected behavior?
  * Do I have enough evidence?

---

## Field Note 7 — Document While Investigating

- Don't rely on memory.

- Record:

  * Objective
  * Baseline
  * Payloads
  * Observations
  * Conclusions

- Good notes save time later.

---

## Field Note 8 — Review Before Expanding

- Before increasing payload size, ask:

  * Did the first experiment answer my question?
  * What did I learn?
  * What should change next?

- Expansion should be driven by evidence, not habit.

---

## Field Note 9 — Quality Beats Quantity

- Professional investigations are measured by the quality of observations—not by the number of requests sent.

- Efficient experiments respect both your time and the authorized target.

---

## Field Note 10 — Intruder Finds Candidates

- Intruder identifies responses that deserve attention.

- Repeater helps explain those responses.

- Neither tool replaces the other.

---

## Daily Checklist

- Before launching an Intruder attack, confirm:

  * [ ] I understand the request.
  * [ ] I established a baseline.
  * [ ] I have a clear objective.
  * [ ] My payload list is focused.
  * [ ] I selected the correct attack type.
  * [ ] My request volume is reasonable.
  * [ ] The target is within my authorized scope.

---

## Reflection

- Think about your last Intruder experiment.

- Answer:

| Question                              | Your Notes |
| ------------------------------------- | ---------- |
| What was my objective?                |            |
| Did I start with a small payload set? |            |
| What surprised me?                    |            |
| What would I change next time?        |            |
| What evidence supports my conclusion? |            |

---

## Professional Mindset

- The purpose of Intruder is not to send more requests.

- The purpose is to answer better questions.

- The best testers automate carefully, observe patiently, and document thoroughly.
