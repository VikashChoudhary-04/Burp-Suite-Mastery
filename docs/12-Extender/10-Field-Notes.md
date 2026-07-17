# Field Notes

> **Module Progress**

```text id="t9w3ln"
██████████░░░░░ 10 / 15 Lessons
```

- The following observations come from common practices used by experienced penetration testers.

- They are not strict rules, but practical habits that lead to cleaner workflows, better decisions, and more reliable assessments.

---

## Start With the Problem

- When a new challenge appears, resist the urge to immediately search for an extension.

- Instead ask:

  * What is the actual problem?
  * Can Burp already solve it?
  * Would automation provide meaningful value?

- Understanding the problem should always come before selecting a tool.

---

## Learn the Manual Process First

- If you cannot explain how a task works manually, an extension may hide important details.

- Learning the manual workflow helps you:

  * Validate automated output.
  * Troubleshoot unexpected behavior.
  * Understand the limitations of automation.

- Automation is most valuable when it builds on understanding.

---

## Introduce One Extension at a Time

- Adding several new extensions simultaneously makes troubleshooting difficult.

- A better approach is to:

  1. Install one extension.
  2. Configure it.
  3. Verify that it behaves as expected.
  4. Decide whether it genuinely improves your workflow.

- This incremental approach makes problems much easier to isolate.

---

## Keep Notes

- Maintain a record of:

  * Which extensions you use.
  * Why you chose them.
  * Typical use cases.
  * Configuration changes.
  * Known limitations.
  * Alternatives you considered.

- These notes become increasingly valuable as your toolkit grows.

---

## Review Your Toolkit Regularly

- Every few months, ask:

  * Do I still use this extension?
  * Has Burp added this capability natively?
  * Is the extension still maintained?
  * Does another solution fit my workflow better?

- Removing unnecessary tools keeps your environment clean and predictable.

---

## Validate Important Findings

- Even well-designed extensions can:

  * Miss issues.
  * Misinterpret data.
  * Produce false positives.
  * Produce false negatives.

- For important findings:

  * Review the raw requests and responses.
  * Repeat key observations manually.
  * Confirm that the evidence supports your conclusion.

- Evidence—not automation—supports a professional report.

---

## Investigation Workflow

```text id="r4x8qp"
Encounter Problem

↓

Understand Manually

↓

Need Automation?

     │
 No  │ Yes
     ▼
Continue     Evaluate Extension

             ↓

      Test Carefully

             ↓

      Validate Output

             ↓

      Record Lessons Learned
```

- The workflow encourages deliberate decisions rather than reactive tool installation.

---

## Extension Selection Challenge

- You install an extension that appears useful, but after several engagements you realize you rarely use it.

- Ask yourself:

  * Is it still adding value?
  * Does it complicate your environment?
  * Could removing it simplify your workflow without affecting productivity?

- Professionals are comfortable removing tools that no longer serve a purpose.

---

## Professional Habits

- Experienced testers often:

  * Learn manually before automating.
  * Add extensions gradually.
  * Keep written notes.
  * Review toolsets periodically.
  * Validate significant findings.
  * Prioritize clarity over convenience.

- These habits lead to more reliable and maintainable workflows.

---

## Key Takeaways

* Understand the task before automating it.
* Introduce extensions gradually.
* Keep records of your toolkit and configurations.
* Review your environment regularly.
* Validate critical findings manually.
