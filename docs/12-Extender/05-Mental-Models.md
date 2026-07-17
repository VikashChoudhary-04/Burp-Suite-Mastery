# Mental Models

> **Module Progress**

```text id="n7mr6y"
█████░░░░░░░░░░ 5 / 15 Lessons
```

- Extensions are powerful, but power without purpose creates unnecessary complexity.

- Professional testers choose extensions intentionally, understand their limitations, and regularly review whether each extension still adds value.

- These mental models will help you build a maintainable and effective Burp environment.

---

## Mental Model 1 — Solve Problems, Not Collections

- Do not ask:

  > "Which extensions should I install?"

- Instead ask:

  > **"What problem am I trying to solve?"**

- An extension should exist because it improves a workflow—not because it is popular.

---

## Mental Model 2 — Fewer, Better Tools

- Ten well-understood extensions are usually more valuable than fifty rarely used ones.

- A focused toolkit is:

  * Easier to learn.
  * Easier to troubleshoot.
  * Easier to maintain.
  * Less likely to introduce conflicts.

- Quality beats quantity.

---

## Mental Model 3 — Understand Before You Automate

- Automation saves time only when you understand the underlying task.

- Before relying on an extension, make sure you understand:

  * What it does.
  * How it works.
  * What assumptions it makes.
  * What it cannot do.

- Use automation to support knowledge—not replace it.

---

## Mental Model 4 — Trust, But Verify

- Extensions can:

  * Miss findings.
  * Produce false positives.  
  * Produce false negatives.
  * Behave unexpectedly.

- Treat extension output as evidence to investigate, not unquestionable truth.

- Validation remains your responsibility.

---

## Mental Model 5 — Every Extension Has a Cost

- Installing an extension may increase:

  * Memory usage
  * CPU usage
  * Startup time
  * Maintenance effort
  * Interface complexity

- Before installing one, ask:

  > **"Is the benefit worth the added complexity?"**

---

## Mental Model 6 — Build Around Your Workflow

- Different roles benefit from different extensions.

- Examples:

  * Bug bounty hunter
  * Penetration tester
  * API security tester
  * Application security engineer
  * Security researcher

- Choose extensions that support *your* workflow rather than copying someone else's setup.

---

## Mental Model 7 — Review Regularly

- Your Burp environment should evolve.

- Periodically ask:

  * Do I still use this extension?
  * Does Burp now provide this feature natively?
  * Is the extension still maintained?
  * Is there a better alternative?

- Regular reviews keep your toolkit efficient and relevant.

---

## Extension Selection Challenge

- You discover five extensions that all claim to improve API testing.

- Before installing them, consider:

  * Which one best fits your current workflow?
  * Are multiple extensions solving the same problem?
  * Could one well-chosen extension replace several others?
  * Would installing all five make Burp easier—or harder—to use?

- Professional environments prioritize clarity over excess.

---

## Professional Habits

- Experienced testers:

  * Install with purpose.
  * Learn before automating.
  * Validate extension output.
  * Keep toolsets lean.
  * Remove unused extensions.
  * Review their environment regularly.

---

## Key Takeaways

* Install extensions to solve specific problems.
* A smaller, well-understood toolkit is often more effective.
* Understand a task before automating it.
* Validate extension output before relying on it.
* Regular maintenance keeps Burp efficient and manageable.
