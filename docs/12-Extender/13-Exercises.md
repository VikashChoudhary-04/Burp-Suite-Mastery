# Exercises

> **Module Progress**

```text id="y5h8nd"
█████████████░░ 13 / 15 Lessons
```

- These exercises focus on making informed decisions about when and how to use Burp extensions.

- The goal is not to install extensions, but to develop the reasoning process that experienced testers use during real assessments.

---

## Learning Objectives

- By completing these exercises, you should be able to:

  * Decide when an extension is appropriate.
  * Evaluate extensions before installation.
  * Balance automation with manual testing.
  * Maintain a clean and reliable Burp environment.
  * Justify your decisions using evidence and workflow requirements.

---

## Rules

- For every exercise:

  * Begin with the security objective.
  * Consider whether Burp's built-in tools are sufficient.
  * Introduce an extension only if it provides clear value.
  * Explain your reasoning.  
  * Describe how you would validate the results.

- The explanation is more important than the final answer.

---

## Level 1 — Identify the Need

- For each scenario, answer:

  1. Would you use only built-in Burp features?
  2. Would you evaluate an extension?
  3. Why?

- Scenarios:

  * Reviewing a small number of JWTs.
  * Discovering undocumented HTTP parameters.
  * Comparing authorization responses.
  * Organizing thousands of HTTP requests.
  * Repeating a manual transformation hundreds of times.

- Focus on the decision-making process.

---

## Level 2 — Extension Evaluation

- Imagine you have found an extension that appears useful.

- Evaluate it using the following checklist:

  * What problem does it solve?
  * Is it compatible with your Burp version?
  * Is it actively maintained?
  * Does it require additional configuration?
  * What are its limitations?
  * How would you validate its output?

- A structured evaluation often reveals whether an extension truly belongs in your toolkit.

---

## Level 3 — Troubleshooting

- Consider the following situations:

  * An extension fails to load.
  * Burp becomes noticeably slower after installing several extensions.
  * An extension produces unexpected results.
  * A previously reliable extension stops working after an update.

- For each case:

  * Identify possible causes.
  * Describe the order in which you would investigate them.
  * Explain why you chose that approach.

- Practice troubleshooting methodically rather than randomly.

---

## Level 4 — Toolkit Review

- Imagine your Burp environment contains twenty installed extensions.

- Review the toolkit and decide:

  * Which extensions still provide value?
  * Which appear redundant?
  * Which would you remove?
  * Which would you investigate further before making a decision?

- Your goal is not to maximize the number of extensions but to optimize your workflow.

---

## Extension Selection Challenge

- A teammate recommends installing three extensions before every engagement because they are "industry standard."

- Without knowing anything else, explain:

  * What questions would you ask first?
  * What information would influence your decision?
  * How would you determine whether each extension is genuinely useful for the engagement?

- Good testers evaluate recommendations rather than accepting them automatically.

---

## Reflection

- After completing these exercises, ask yourself:

  * Do I introduce extensions because I need them or because they are available?  
  * Can I explain why each extension is installed?
  * Do I understand the manual process behind each automated task?
  * Would another tester understand my toolkit if they inherited it?
  
---

## Self-Assessment

- You are ready to continue if you can confidently:

  * Choose between built-in tools and extensions.
  * Evaluate extensions critically.
  * Troubleshoot extension issues methodically.
  * Maintain a focused Burp environment.
  * Justify every installation decision.

---

## Professional Checklist

- Before installing any extension, confirm:

  * □ I understand the problem.
  * □ Built-in features are insufficient.
  * □ The extension provides clear value.
  * □ Compatibility has been considered.
  * □ I know how to validate the output.
  * □ I am prepared to maintain or remove it later.

---

## Mastery Challenge

- Design your own extension evaluation framework.

- It should answer:

  1. When is an extension justified?
  2. What criteria must it satisfy?
  3. How will it be validated?
  4. When should it be removed?

- If another penetration tester can use your framework to make consistent decisions, you've understood the purpose of this module.
