# Interface

> **Module Progress**

```text id="w3m7kt"
███░░░░░░░░░░░░ 3 / 15 Lessons
```

- The Scanner interface provides a centralized workspace for configuring scans, monitoring progress, reviewing findings, and managing automated testing activities.

- Rather than viewing Scanner as a single feature, think of it as a complete investigation environment.

- The interface is designed to help you answer:

  > **"What has been tested, what was discovered, and what requires manual validation?"**

---

## Main Components

- The Scanner workspace generally includes:

  * Scan configurations
  * Running and completed scans
  * Scan progress
  * Discovered issues
  * Issue details
  * Scan logs and status information

- Each component supports a different stage of the automated testing process.

---

## Scan Configurations

- Before a scan begins, you define how Scanner should operate.

- Configuration may include:

  * Scope
  * Scan type  
  * Testing options
  * Authentication settings
  * Performance limits

- Good configuration improves both efficiency and result quality.

---

## Running Scans

- The interface allows you to monitor scans as they execute.

- During this stage, you can observe:

  * Current activity  
  * Overall progress
  * Target coverage
  * Scan status

- Monitoring helps you identify unexpected behavior before a scan completes.

---

## Discovered Issues

- As Scanner identifies potential vulnerabilities, findings are organized for review.

- Each issue typically includes:

  * Issue type
  * Severity
  * Confidence level
  * Evidence
  * Affected location
  * Supporting details

- These findings should guide investigation—not replace it.

---

## Issue Details

- Selecting a finding provides additional context, such as:

  * Description
  * Evidence
  * Potential impact
  * Suggested remediation
  * Relevant requests and responses

- Reviewing these details carefully is an essential part of manual validation.

---

## Logs and Status

- Scanner records operational information while it runs.

- These logs may help you:

  * Confirm scan completion
  * Identify interruptions
  * Troubleshoot unexpected behavior
  * Understand why certain tests did or did not execute

- Logs often provide valuable context during investigations.

---

## Investigation Workflow

```text id="m5p9rv"
Configure Scan

↓

Start Scan

↓

Monitor Progress

↓

Review Findings

↓

Validate Manually

↓

Report Confirmed Issues
```

- Automation supports the investigation, but the final conclusions come from careful analysis.

---

## Scanner Challenge

- A completed scan reports several medium-severity findings with high confidence.

- Before documenting them, ask:

  * Have I reviewed the supporting evidence?
  * Can I reproduce the behavior manually?
  * Are the findings relevant to the application's context?
  * Do the requests and responses support the conclusion?

- Professional testers verify findings before reporting them.

---

## Practical Tips

* Define scope carefully before scanning.
* Monitor long-running scans periodically.
* Review evidence rather than relying only on severity labels.
* Use issue details to guide manual validation.
* Treat logs as an important troubleshooting resource.

---

## Key Takeaways

* The Scanner interface supports the full scan lifecycle.
* Configuration determines how scans are performed.
* Findings require manual review and validation.
* Logs provide valuable operational context.
* The interface is designed to support investigation—not replace it.
