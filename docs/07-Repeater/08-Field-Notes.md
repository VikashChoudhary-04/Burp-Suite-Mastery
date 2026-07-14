# Field Notes

## Overview

- This lesson is a collection of practical habits, observations, and workflows that experienced penetration testers commonly develop over time.

- These notes are not rules.

- They are principles that help you work more efficiently, investigate more carefully, and reduce mistakes during authorized security assessments.

---

## Learning Objectives

- By the end of this lesson, you will be able to:

  * Develop better testing habits.
  * Improve investigation quality.
  * Avoid common workflow mistakes.
  * Organize testing sessions effectively.
  * Think more systematically during manual testing.

---

## Prerequisites

* Complete all previous Repeater lessons.

---

## Difficulty

🟠 Intermediate

---

## Estimated Time

| Activity   |         Time |
| ---------- | -----------: |
| Reading    |       20 min |
| Reflection |       20 min |
| Practice   |       30 min |
| **Total**  | **≈ 70 min** |

---

## Field Note #1 — Read Before You Edit

- Many beginners immediately start modifying requests.

- Experienced testers read the request first.

- Before changing anything, ask yourself:

  * What is this request doing?
  * Which values are supplied by the client?
  * Which values are likely validated by the server?
  * What is the purpose of each parameter?

- Understanding the request often reveals better test ideas than random modifications.

> [!TIP]
> If you cannot explain what a request does, spend more time understanding it before editing it.

---

## Field Note #2 — One Change, One Lesson

- Modify one variable at a time.

- If you change:

  * a parameter,
  * a cookie,
  * and a header,

then the response changes, you cannot confidently determine which modification caused the difference.

- Controlled experiments produce reliable conclusions.

---

## Field Note #3 — Always Keep a Baseline

- Never overwrite or forget the original request.

- Your baseline is your reference point.

- Every experiment should be compared against it.

- Without a baseline, response analysis becomes guesswork.

---

## Field Note #4 — Keep Notes While Testing

- Do not trust your memory.

- Record:

  * The objective.
  * The request.
  * The modification.
  * The response.
  * Your observations.
  * The next idea to investigate.

- Good notes make report writing much easier.

---

## Field Note #5 — Ask Better Questions

- Instead of asking:

  > "How do I exploit this?"

- Ask:

  * Why does this parameter exist?
  * What assumptions is the server making?
  * What happens if this value changes?
  * Is this behavior expected?
  * What evidence supports my conclusion?

- Better questions lead to better investigations.

---

## Field Note #6 — Slow Down

- Manual testing is not a race.

- Experienced testers often spend more time thinking than sending requests.

- Taking a few extra minutes to understand an application can prevent hours of unproductive testing.

---

## Field Note #7 — Organize Your Workspace

- As investigations grow, Repeater can fill with many tabs.

- Adopt simple habits:

  * Close tabs you no longer need.
  * Rename important requests.
  * Group related investigations.
  * Keep your workspace manageable.

- A tidy workspace reduces cognitive load.

---

## Field Note #8 — Unexpected Does Not Mean Vulnerable

- Unexpected behavior deserves investigation.

- It does **not** automatically indicate a vulnerability.

- Before reaching a conclusion:

  * Reproduce the behavior.
  * Compare with the baseline.
  * Consider legitimate explanations.
  * Gather additional evidence.

- Evidence should guide your conclusions.

---

## Field Note #9 — Think Like an Investigator

- An investigator does not begin with an answer.

- They begin with a question.

- Every request should have a purpose.

- Every response should teach you something.

- Every experiment should inform the next one.

---

## Field Note #10 — Learn the Application First

- Many findings come from understanding the application's intended behavior.

- Spend time exploring:

  * User workflows.
  * Authentication.
  * Authorization.
  * APIs.
  * Business processes.

- A deep understanding of the application often reveals opportunities for meaningful testing.

---

## Professional Habits Checklist

- Develop these habits:

  * Read before editing.
  * Keep a baseline.
  * Change one variable at a time.
  * Record observations.
  * Build hypotheses.
  * Collect evidence.
  * Reproduce interesting behavior.
  * Stay organized.
  * Review your notes regularly.

---

## Reflection Exercise

- Think about a recent lab or practice target.

- Answer:

  1. Did you establish a baseline?
  2. Did you modify only one variable?
  3. Did you keep investigation notes?
  4. Did every request have a clear objective?
  5. What would you do differently next time?

---

## Interview Questions

1. Why should testers keep a baseline request?
2. Why is documentation important during manual testing?
3. Why should only one variable change at a time?
4. What habits improve manual testing quality?
5. Why is understanding the application important before testing?

---

## Quick Revision

* Read first.
* Think before modifying.
* Keep a baseline.
* Change one variable.
* Take notes.
* Build hypotheses.
* Collect evidence.
* Stay organized.
* Verify before concluding.

---

## Mastery Checklist

* [ ] I understand the importance of disciplined testing.
* [ ] I know how to organize investigations.
* [ ] I keep useful notes.
* [ ] I collect evidence before concluding.
* [ ] I completed the reflection exercise.
