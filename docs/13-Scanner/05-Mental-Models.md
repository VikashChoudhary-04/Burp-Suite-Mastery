# Mental Models

> **Module Progress**

```text id="r5x2mt"
█████░░░░░░░░░░ 5 / 15 Lessons
```

- Burp Scanner is one of the most powerful automation features in Burp Suite.

- Its real value comes not from the number of findings it produces, but from how effectively those findings are interpreted and validated.

- These mental models will help you use Scanner like an experienced penetration tester.

---

## Mental Model 1 — Findings Are Starting Points

- Do not ask:

  > "Scanner found a vulnerability."

- Instead ask:

  > **"Scanner found evidence that deserves investigation."**

- Every finding begins an investigation.

- None should automatically end one.

---

## Mental Model 2 — Evidence Over Labels

- Severity labels attract attention.

- Evidence supports conclusions.

- Before accepting any finding, review:

  * Requests
  * Responses
  * Observed behavior
  * Supporting details
  * Reproducibility

- Always prioritize evidence over severity.

---

## Mental Model 3 — Automation Improves Coverage

- Scanner excels at:

  * Repetition
  * Consistency
  * Speed
  * Large-scale testing

- Humans excel at:

  * Context
  * Creativity
  * Business logic
  * Judgment

- Professional testing combines both strengths.

---

## Mental Model 4 — False Positives Are Expected

- No automated scanner is perfect.

- Some findings will require dismissal after manual investigation.

- Treat false positives as part of the normal validation process—not as Scanner failures.

---

## Mental Model 5 — False Negatives Also Exist

- The absence of a finding does **not** prove the absence of a vulnerability.

- Some issues are beyond the scope of automated analysis.

- Continue investigating even when Scanner reports no issues.

---

## Mental Model 6 — Scope Matters

- A scan is only as useful as its scope.

- Poorly defined scope can result in:

  * Missed endpoints
  * Irrelevant findings
  * Excessive traffic
  * Wasted analysis time

- Careful scope definition improves both efficiency and result quality.

---

## Mental Model 7 — Validate Before Reporting

- Professional reports contain **confirmed vulnerabilities**, not raw scanner output.

- Validation should answer:

  * Can the issue be reproduced?
  * Is it actually exploitable?
  * What is the real impact?
  * What evidence supports the conclusion?

- Verification transforms observations into defensible findings.

---

## Scanner Challenge

- A scan returns:

  * 3 High severity findings
  * 12 Medium severity findings  
  * 40 Low severity findings

- Instead of starting with the severity labels, decide:

  * Which findings have the strongest supporting evidence?
  * Which are easiest to reproduce?
  * Which could have the greatest business impact if confirmed?
  * Which require immediate manual validation?

- Professional prioritization considers both technical evidence and practical impact.

---

## Professional Habits

- Experienced testers:

  * Review evidence first.
  * Validate manually.
  * Expect both false positives and false negatives.
  * Define scope carefully.
  * Treat Scanner as an investigative assistant.

- These habits produce more accurate assessments and stronger reports.

---

## Key Takeaways

* Scanner findings are hypotheses supported by evidence.
* Evidence matters more than severity labels.
* Automation increases coverage, not certainty.
* Both false positives and false negatives are normal.
* Manual validation is essential before reporting.
