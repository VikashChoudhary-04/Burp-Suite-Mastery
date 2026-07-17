# Common Mistakes

> **Module Progress**

```text id="m6p8wr"
███████████░░░░ 11 / 15 Lessons
```

- Extensions can significantly improve your workflow, but only when they are selected and used thoughtfully.

- The mistakes below are common among new Burp users and are entirely avoidable with good habits.

---

## Mistake 1 — Installing Everything

- New users often install every extension they find interesting.

- This leads to:

  * Unnecessary complexity
  * Higher memory usage
  * Slower startup
  * More difficult troubleshooting
  * Overlapping functionality

- **Better approach:** Install extensions only when they solve a specific problem.

---

## Mistake 2 — Trusting Automation Blindly

- An extension's output is not automatically correct.

- Extensions can:

  * Miss vulnerabilities
  * Produce false positives
  * Misinterpret application behavior
  * Make incorrect assumptions

- **Better approach:** Validate important findings using manual investigation and Burp's built-in tools.

---

## Mistake 3 — Skipping Documentation

- Installing an extension without understanding it often leads to misuse.

- Before relying on an extension, understand:

  * Its purpose
  * Its limitations
  * Required configuration
  * Expected output

- **Better approach:** Read the documentation before using it in an assessment.

---

## Mistake 4 — Ignoring Compatibility

- An extension that worked previously may not behave correctly after Burp or the extension itself is updated.

- Ignoring compatibility can result in:

  * Loading failures
  * Unexpected errors
  * Missing functionality

- **Better approach:** Verify compatibility whenever something behaves unexpectedly.

---

## Mistake 5 — Never Reviewing Your Toolkit

- Many users leave unused extensions installed indefinitely.

- Over time this creates unnecessary clutter.

- **Better approach:** Periodically remove extensions that no longer improve your workflow.

---

## Mistake 6 — Automating Before Understanding

- Automation should improve efficiency—not replace knowledge.

- If you cannot explain the manual process, you are unlikely to recognize when automation fails.

- **Better approach:** Learn the manual workflow first, then automate repetitive tasks.

---

## Mistake 7 — Random Troubleshooting

- When Burp behaves unexpectedly, beginners often change multiple settings at once.

- This makes the real cause difficult to identify.

- **Better approach:** Troubleshoot methodically:

  1. Review logs.  
  2. Check compatibility.
  3. Disable recent extensions.
  4. Test one change at a time.

---

## Decision Workflow

```text id="q1z7ht"
Need Extension?

↓

Evaluate

↓

Install

↓

Configure

↓

Validate

↓

Keep?

     │
 No  │ Yes
     ▼
Remove     Maintain
```

- Every extension should continue earning its place in your toolkit.

---

## Extension Selection Challenge

- A colleague recommends an extension because "everyone uses it."

- Before installing it, ask:

  * What specific problem does it solve?
  * Do I actually encounter that problem?
  * Will it improve my workflow?
  * Can Burp already handle this task?

- Popularity should never replace thoughtful evaluation.

---

## Professional Habits

- Experienced testers:

  * Install with purpose.
  * Read documentation.
  * Validate automation.
  * Review compatibility.
  * Remove unused tools.
  * Troubleshoot systematically.

- These habits create a reliable and maintainable Burp environment.

---

## Key Takeaways

* Avoid unnecessary extensions.
* Never trust automation without verification.
* Understand an extension before relying on it.
* Keep your toolkit organized and current.
* Solve problems systematically.
