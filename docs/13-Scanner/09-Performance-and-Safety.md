# Performance & Safety

> **Module Progress**

```text id="d6m9wp"
█████████░░░░░░ 9 / 15 Lessons
```

- Automated scanning can significantly improve efficiency, but it also consumes resources and actively interacts with the target application.

- Professional testers consider both **performance** and **safety** before starting any scan.

---

## Understand the Impact

- Active scanning generates additional HTTP requests.

- Depending on the configuration, Scanner may:

  * Send numerous test payloads.
  * Repeat requests with different inputs.
  * Analyze application behavior.
  * Increase traffic to the target.

- More testing generally means more resource consumption.

---

## Respect Scope

- Never scan systems outside your authorized scope.

- Before starting a scan, verify:

  * Target hosts.
  * Included paths.
  * Excluded resources.
  * Authentication context.
  * Engagement permissions.

- Good scope management protects both the tester and the organization.

---

## Consider Application Stability

- Some applications are sensitive to heavy automated traffic.

- Potential effects include:

  * Increased server load.
  * Longer response times.
  * Temporary rate limiting.
  * Session expiration.
  * Unexpected application behavior.

- Understanding the environment helps you choose appropriate scan settings.

---

## Configure Responsibly

- Scanner provides configuration options that influence performance.

- Examples include:

  * Scan scope.
  * Active vs passive testing.
  * Authentication.
  * Performance limits.
  * Scan policies.

- Thoughtful configuration balances coverage with operational impact.

---

## Monitor While Scanning

- Do not launch a scan and ignore it.

- During execution, monitor:

  * Progress.
  * Errors.
  * Authentication state.
  * Unexpected responses.
  * Resource usage.

- Early detection allows adjustments before problems become significant.

---

## Validate Before Reporting

- Heavy automation does not guarantee accurate results.

- Always review:

  * Supporting evidence.
  * Requests and responses.
  * Reproducibility.
  * Business context.

- Scanner findings become professional conclusions only after validation.

---

## Safety Workflow

```
Confirm Authorization

↓

Define Scope

↓

Configure Scan

↓

Monitor Activity

↓

Review Findings

↓

Validate Manually

↓

Report Confirmed Issues
```

- Safety begins before the first request is sent.

---

## Scanner Challenge

- You are asked to scan a production application during business hours.

- Before starting, consider:

  * Is active scanning appropriate?
  * Could performance be affected?
  * Should testing be limited or scheduled differently?
  * How will you monitor the application's behavior?
  * What precautions reduce operational risk?

- Responsible testing protects both security and availability.

---

## Common Mistakes

* Scanning outside the authorized scope.
* Using aggressive settings without understanding their impact.
* Ignoring authentication failures.
* Leaving long-running scans unattended.
* Reporting findings without validation.

Avoiding these mistakes improves both technical quality and professional credibility.

---

## Key Takeaways

* Active scanning generates additional traffic.
* Scope should always be verified.
* Configuration affects both coverage and application impact.
* Monitor scans while they run.
* Validation remains essential before reporting.
