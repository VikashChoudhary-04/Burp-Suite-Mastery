# Professional Workflow

> **Module Progress**

```text id="u9j3wa"
███████░░░░░░░░ 7 / 15 Lessons
```

- Professional penetration testers do not begin an assessment by installing extensions.

- They begin by understanding the objective.

- Only after identifying a genuine need do they evaluate whether an extension can improve efficiency, visibility, or accuracy.

- This workflow helps keep Burp environments focused, maintainable, and effective.

---

## Step 1 — Define the Objective

- Start with the security question you want to answer.

- Examples include:

  * Identify hidden HTTP parameters.
  * Analyze JSON Web Tokens.
  * Test authorization controls.
  * Improve request logging.
  * Automate repetitive analysis.

- A clearly defined objective prevents unnecessary tool usage.

---

## Step 2 — Check Built-in Capabilities

- Before adding an extension, determine whether Burp Suite already provides the required functionality.

- Ask yourself:

  * Can I perform this manually?
  * Is there already a built-in Burp tool for this task?
  * Would an extension provide a meaningful advantage?

- If the built-in tools meet your needs, additional complexity may not be justified.

---

## Step 3 — Evaluate Candidate Extensions

- When an extension is appropriate, evaluate it carefully.

- Consider:

  * Purpose
  * Compatibility
  * Maintenance status
  * Configuration requirements
  * Resource usage
  * Community reputation

- Do not select an extension based solely on popularity.

---

## Step 4 — Install and Configure

- After selecting an extension:

  * Install it.
  * Review its documentation.
  * Configure required settings.
  * Verify that it loads correctly.
  * Confirm that it behaves as expected.

- A poorly configured extension can produce misleading results.

---

## Step 5 — Validate the Output

- Treat extension output as evidence—not as unquestionable truth.

- Validate important findings by:

  * Reviewing the generated results.
  * Repeating key observations manually.
  * Comparing results with Burp's built-in tools.
  * Confirming that conclusions are supported by evidence.

- Professional testers verify before reporting.

---

## Step 6 — Maintain the Environment

- Regularly review your extension list.

- Ask:

  * Do I still use this extension?
  * Is it still maintained?
  * Does Burp now provide this feature natively?
  * Should it be updated or removed?

- A clean environment reduces maintenance and troubleshooting effort.

---

## Professional Workflow

```text id="y6pr1n"
Define Objective

↓

Use Built-in Tools?

     │
 Yes │ No
     ▼
Continue     Evaluate Extension

             ↓

     Install & Configure

             ↓

      Validate Results

             ↓

     Maintain Environment
```

- The workflow begins with the problem and ends with a reliable, well-maintained toolkit.

---

## Extension Selection Challenge

- During an API assessment, you realize that manually identifying hidden parameters is taking too long.

- Before installing an extension, consider:

  * Is the delay significant enough to justify automation?
  * Which extension best addresses this specific problem?
  * How will you verify its findings?
  * Will you continue using it after this engagement?

- Good decisions improve both productivity and reliability.

---

## Professional Habits

- Experienced testers:

  * Start with the objective.
  * Prefer simplicity when possible.
  * Evaluate before installing.
  * Validate all important output.
  * Remove tools that no longer provide value.

- These habits keep Burp environments efficient and trustworthy.

---

## Key Takeaways

* Define the security objective before choosing tools.
* Use built-in functionality whenever it is sufficient.
* Evaluate extensions carefully before installation.
* Validate extension output before relying on it.
* Maintain a focused, up-to-date extension environment.
