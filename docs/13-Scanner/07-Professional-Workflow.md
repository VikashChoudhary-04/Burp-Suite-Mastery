# Professional Workflow

> **Module Progress**

```text id="t5n8wr"
███████░░░░░░░░ 7 / 15 Lessons
```

- Professional penetration testers use Burp Scanner as part of a larger investigation process.

- Rather than starting with automated scanning, they first understand the application, define scope, and identify testing objectives.

- Scanner then helps improve coverage and reduce repetitive work.

---

## Step 1 — Define Scope

- Before launching a scan, clearly identify:

  * Authorized targets
  * Included applications
  * Excluded systems
  * Authentication requirements
  * Testing objectives

- A well-defined scope improves both safety and result quality.

---

## Step 2 — Explore the Application

- Use Burp's built-in tools to understand the application before scanning.

- Examples include:

  * Browsing the application
  * Mapping endpoints
  * Observing authentication flows
  * Identifying important functionality
  * Discovering input locations

- Better understanding leads to more meaningful scans.

---

## Step 3 — Configure the Scan

- Choose settings appropriate for the engagement.

- Review:

  * Scan scope
  * Passive vs active testing
  * Authentication
  * Performance limits
  * Scan policies

- Avoid using default settings without understanding their impact.

---

## Step 4 — Monitor Progress

- While Scanner is running, monitor:

  * Coverage
  * Progress
  * Errors
  * Authentication status
  * Resource usage

- Early monitoring helps detect problems before they affect the final results.

---

## Step 5 — Review Findings

- After the scan completes:

  * Read issue descriptions.
  * Examine supporting evidence.
  * Review requests and responses.
  * Consider severity and confidence.
  * Prioritize findings for validation.

- Do not report findings without investigation.

---

## Step 6 — Validate Manually

- For each significant finding:
  
  * Reproduce the behavior.
  * Confirm exploitability where appropriate.
  * Assess business impact.
  * Gather additional evidence.
  * Record observations.

- Manual validation transforms scanner output into defensible conclusions.

---

## Step 7 — Report Confirmed Issues

- Only confirmed findings should appear in the final report.

- A professional report should include:

  * Clear evidence
  * Reproduction steps
  * Risk explanation
  * Practical remediation guidance

- Scanner output supports the report but should not replace thoughtful analysis.

---

## Professional Workflow

```text id="x4j7pk"
Define Scope

↓

Explore Application

↓

Configure Scan

↓

Run Scanner

↓

Review Findings

↓

Validate Manually

↓

Report Confirmed Issues
```

- The workflow begins with understanding and ends with verified evidence.

---

## Scanner Challenge

- A scan identifies several medium-confidence findings after a long assessment.

- Before reporting them, ask:

  * Have I reproduced the behavior?
  * Does the evidence support the conclusion?
  * Is the issue practically exploitable?
  * Does the application's context change the risk?

- Professional reports emphasize verified facts over automated assumptions.

---

## Professional Habits

- Experienced testers:

  * Understand the application first.
  * Define scope carefully.
  * Configure scans intentionally.
  * Monitor long-running scans.
  * Validate all significant findings.
  * Report confirmed vulnerabilities only.

- These habits improve both technical accuracy and client confidence.

---

## Key Takeaways

* Scanner fits into a structured penetration testing workflow.
* Understanding the application improves scan quality.
* Monitoring helps detect problems early.
* Manual validation is essential.
* Reports should be based on confirmed evidence.
