# Real Investigation Scenarios

> **Module Progress**

```text id="g3w9pt"
████████░░░░░░░ 8 / 15 Lessons
```

- Scanner is most valuable when it supports an investigation—not when it replaces one.

- The following scenarios illustrate how professional testers decide when automated scanning is appropriate and how they validate the resulting findings.

---

## Scenario 1 — Large Web Application

- You are assessing an application with hundreds of pages and numerous input fields.

- Your objectives are to:

  * Increase testing coverage.
  * Identify common vulnerability patterns.
  * Prioritize manual investigation.

**Question:**

- Would manually checking every endpoint be practical, or would Scanner improve efficiency without replacing manual testing?

---

## Scenario 2 — Authentication Review

- Scanner reports several findings related to cookies and authentication.

- Your objectives are to:

  * Review session management.
  * Validate cookie attributes.
  * Confirm authentication behavior.

**Question:**

- Which observations can be confirmed directly from requests and responses, and which require additional testing?

- Automation identifies evidence.

- Manual investigation establishes confidence.

---

## Scenario 3 — Reflected Input

- Scanner reports possible reflected cross-site scripting.

- Your objectives are to:

  * Review the reflected content.
  * Assess input handling.
  * Determine exploitability.

**Question:**

- Does reflected input automatically mean a vulnerability exists?

- What additional evidence would you seek before reporting it?

---

## Scenario 4 — API Assessment

- You are assessing a REST API with many endpoints.

- Your objectives are to:

  * Identify common security weaknesses.
  * Increase coverage.
  * Prioritize testing.

**Question:**

- How can Scanner improve efficiency while ensuring that authorization and business logic are still reviewed manually?

---

## Scenario 5 — Prioritizing Findings

- A completed scan reports:

  * Several High severity findings.
  * Numerous Medium severity findings.
  * Many Informational observations.

- Your objectives are to:

  * Prioritize investigation.
  * Validate important findings.
  * Produce an accurate report.

**Question:**

- Should severity alone determine your investigation order?

- Consider:

  * Confidence
  * Business impact
  * Ease of reproduction
  * Available evidence

- Professional prioritization considers multiple factors.

---

## Investigation Workflow

```text id="m8q4rv"
Understand Application

↓

Run Scanner

↓

Review Evidence

↓

Prioritize Findings

↓

Manual Validation

↓

Confirmed Report
```

- Automation provides direction.

- Validation provides certainty.

---

## Scanner Challenge

- Scanner identifies a possible SQL injection with medium confidence.

- Before reporting it, decide:

  * What evidence has Scanner provided?
  * How would you attempt to reproduce the behavior?
  * What alternative explanations exist?
  * How would successful manual validation change your confidence?

- Treat every finding as an investigation rather than a conclusion.

---

## Investigation Journal

- After each assessment, consider recording:

  * Scan objective.
  * Configuration used.
  * Significant findings.
  * Manual validation results.
  * False positives encountered.
  * Lessons learned for future scans.

- Over time, these notes improve both efficiency and judgment.

---

## Key Takeaways

* Scanner is most valuable when integrated into a larger investigation.
* Severity should not be the only prioritization factor.
* Evidence must be reviewed carefully.
* Manual validation confirms scanner findings.
* Good documentation improves future assessments.
