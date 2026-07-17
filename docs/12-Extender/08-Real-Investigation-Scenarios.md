# Real Investigation Scenarios

> **Module Progress**

```text id="v5m8kr"
████████░░░░░░░ 8 / 15 Lessons
```

- Professional testers introduce extensions only when they improve an investigation.

- The following scenarios demonstrate how experienced practitioners decide whether an extension is appropriate and how it fits into a broader testing workflow.

---

## Scenario 1 — JWT Analysis

- You are assessing an application that relies heavily on JSON Web Tokens for authentication and authorization.

- Your objectives are to:

  * Inspect token structure.
  * Decode claims.
  * Review signature information.
  * Test security assumptions.

**Question:**

- Would manually decoding tokens remain practical throughout a long assessment, or would a dedicated extension improve consistency and efficiency?

- The extension should support your analysis—not replace your understanding of JWTs.

---

## Scenario 2 — Hidden Parameter Discovery

- During testing, you suspect the application accepts undocumented HTTP parameters.

- Your objectives are to:

  * Discover hidden inputs.
  * Expand the attack surface.
  * Identify unexpected functionality.

**Question:**

- Would manually testing thousands of parameter names be practical, or would a parameter discovery extension make the process more efficient?

- Automation helps scale repetitive tasks.

---

## Scenario 3 — Authorization Testing

- You need to compare responses from users with different privilege levels.

- Your objectives are to:

  * Detect inconsistent access controls.
  * Compare responses.
  * Identify potential authorization weaknesses.

**Question:**

- Would an authorization-focused extension simplify repetitive comparisons while allowing you to validate important findings manually?

---

## Scenario 4 — Request Logging

- A long assessment generates thousands of HTTP requests.

- Your objectives are to:

  * Organize traffic.
  * Review historical requests.
  * Investigate unexpected behavior.
  * Preserve evidence.

**Question:**

- Would enhanced logging improve your investigation, or would Burp's built-in history already meet your needs?

- Always choose the simplest solution that satisfies the requirement.

---

## Scenario 5 — Workflow Automation

- You repeatedly perform the same sequence of actions throughout an engagement.

- Examples include:

  * Data transformation.
  * Request modification.
  * Repetitive validation.
  * Custom analysis.

**Question:**

- Would carefully designed automation reduce repetitive work without hiding important details from the tester?

- Automation should save time—not reduce understanding.

---

## Investigation Workflow

```text id="r2h6py"
Security Objective

↓

Manual Approach

↓

Repeated Often?

     │
 No  │ Yes
     ▼
Continue     Evaluate Extension

             ↓

      Install if Beneficial

             ↓

     Validate the Results

             ↓

      Continue Assessment
```

- The decision to automate should always be based on evidence that it improves the investigation.

---

## Extension Selection Challenge

- You discover two extensions that both claim to improve API testing.

- One provides extensive automation with many configuration options.

- The other performs only one focused task exceptionally well.

- Consider:

  * Which extension best matches your current objective?
  * Which introduces less unnecessary complexity?
  * Which will be easier to understand, maintain, and troubleshoot?

- Professionals often prefer focused tools over feature-heavy alternatives.

---

## Investigation Journal

- After each assessment, consider recording:

  * The problem encountered.
  * Whether an extension was used.
  * Why it was selected.
  * How it improved the workflow.
  * Any limitations observed.
  * Whether you would use it again.

- Over time, this journal becomes a valuable reference for future engagements.

---

## Key Takeaways

* Extensions should address real investigation needs.
* Automation is most valuable for repetitive tasks.
* Validate extension output before relying on it.
* Simpler solutions are often easier to maintain.
* Good extension choices improve both productivity and investigation quality.
