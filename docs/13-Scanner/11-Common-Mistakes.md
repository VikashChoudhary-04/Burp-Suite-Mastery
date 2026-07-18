# Common Mistakes

> **Module Progress**

```text id="n8w2kt"
███████████░░░░ 11 / 15 Lessons
```

- Burp Scanner is a powerful investigative tool, but incorrect assumptions and poor workflow can reduce its effectiveness.

- The mistakes below are among the most common errors made by new users.

- Learning to avoid them will improve both your assessments and your reports.

---

## Mistake 1 — Trusting Every Finding

- Scanner reports **potential** security issues based on observed evidence.

- Not every finding represents a confirmed vulnerability.

- **Better approach:**

  * Review the evidence.
  * Reproduce the behavior.
  * Confirm exploitability where appropriate.
  * Report only verified findings.

---

## Mistake 2 — Ignoring False Negatives

- A clean scan does **not** prove an application is secure.

- Many vulnerabilities require:

  * Business context
  * Multi-step workflows
  * Authorization testing
  * Creative reasoning

- **Better approach:**

  - Continue manual testing even when Scanner reports no issues.

---

## Mistake 3 — Scanning Without Understanding the Application

- Launching a scan before understanding the target often produces less useful results.

- **Better approach:**

  - Explore the application first.

- Identify:
  
  * Important functionality
  * Authentication flows
  * Sensitive endpoints
  * Critical business processes

- Knowledge improves scan quality.

---

## Mistake 4 — Poor Scope Definition

- Scanning the wrong targets wastes time and may create unnecessary risk.

- **Better approach:**

  - Verify:

    * Included hosts
    * Excluded hosts
    * Authorized scope
    * Authentication context

- Always confirm scope before scanning.

---

## Mistake 5 — Ignoring Evidence

- Some users focus only on severity labels.

- Severity helps prioritize work.

- Evidence supports conclusions.

- **Better approach:**

  - Review:

    * Requests
    * Responses
    * Supporting details
    * Reproducibility

- Evidence should always guide reporting.

---

## Mistake 6 — Using Default Configuration Automatically

- Default settings are convenient but may not suit every engagement.

- **Better approach:**

  - Adjust configuration based on:

    * Objectives
    * Application type
    * Performance requirements
    * Testing permissions

- Thoughtful configuration improves results.

---

## Mistake 7 — Leaving Long Scans Unattended

- Long-running scans may encounter:

  * Authentication failures
  * Network interruptions
  * Application changes
  * Unexpected behavior

- **Better approach:**

  - Monitor progress periodically and investigate significant issues as they appear.

---

## Decision Workflow

```text id="r6p9vn"
Understand Application

↓

Define Scope

↓

Configure Scanner

↓

Run Scan

↓

Review Evidence

↓

Validate

↓

Report
```

- Following a structured process reduces mistakes and improves consistency.

---

## Scanner Challenge

- A colleague sends you a Scanner report containing twenty findings and asks you to submit it directly to the client.

- Before doing so, ask:

  * Have the findings been validated?
  * Does the evidence support each conclusion?
  * Are there duplicate or related issues?
  * Has business context been considered?

- Professional reports require professional verification.

---

## Professional Habits

- Experienced testers:

  * Validate findings manually.
  * Continue manual testing after scanning.
  * Define scope carefully.
  * Configure scans intentionally.
  * Review evidence before severity.
  * Monitor long-running scans.

- These habits improve both technical accuracy and client trust.

---

## Key Takeaways

* Scanner findings require validation.
* A clean scan is not proof of security.
* Understand the application before scanning.
* Scope and configuration matter.
* Evidence is more valuable than severity labels.
