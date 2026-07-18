# Scanner Cheat Sheet

> **Module Progress**

```text id="c8m4yt"
██████████████░ 14 / 15 Lessons
```

- This cheat sheet summarizes the essential concepts from the Scanner module for quick revision before assessments, interviews, or penetration testing engagements.

---

## Scanner Workflow

```text
Understand Application
        ↓
Define Scope
        ↓
Configure Scanner
        ↓
Passive Scan
        ↓
Active Scan
        ↓
Review Evidence
        ↓
Manual Validation
        ↓
Report Confirmed Findings
```

---

## Passive vs Active Scanning

| Passive Scanning            | Active Scanning              |
| --------------------------- | ---------------------------- |
| Observes traffic            | Sends test requests          |
| Lower impact                | Higher impact                |
| No modification of requests | Generates additional traffic |
| Identifies potential issues | Tests for vulnerabilities    |
| Safe during exploration     | Requires authorization       |

---

## Core Concepts

* Passive Scan
* Active Scan
* Scan Scope
* Scan Configuration
* Insertion Points
* Severity
* Confidence
* Evidence
* Manual Validation

- Remember:

  - **Severity ≠ Confidence**

---

## Professional Workflow

1. Understand the application.
2. Verify authorization.
3. Define scan scope.
4. Configure Scanner.
5. Run the scan.
6. Review findings.
7. Validate manually.
8. Report confirmed issues.

---

## Validation Checklist

- Before reporting a finding, ask:

  * Have I reviewed the request?
  * Have I reviewed the response?
  * Can I reproduce the behavior?
  * Does the evidence support the conclusion?
  * Have I considered business context?

---

## Common Mistakes

* Trusting every finding.
* Ignoring false negatives.
* Scanning without understanding the application.
* Poor scope definition.
* Using default settings without review.
* Reporting without validation.
* Leaving long scans unattended.

---

## Investigation Priorities

- Prioritize findings using:

  * Confidence
  * Evidence
  * Business impact
  * Reproducibility
  * Severity

- Do **not** prioritize by severity alone.

---

## Golden Rules

* Understand before scanning.
* Scan only authorized targets.
* Configure intentionally.
* Review evidence carefully.
* Validate manually.
* Report only confirmed findings.

---

## Interview Revision

- Be ready to explain:
  
  * Passive vs active scanning.
  * Severity vs confidence.
  * Why validation is essential.
  * Scanner limitations.
  * Scanner's role in penetration testing.
  * How you prioritize findings.

---

## Module Summary

- Scanner is an investigative assistant—not a replacement for professional judgment.

- Automation increases coverage.

- Evidence supports findings.

- Manual validation confirms vulnerabilities.

- Professional reasoning produces reliable reports.
