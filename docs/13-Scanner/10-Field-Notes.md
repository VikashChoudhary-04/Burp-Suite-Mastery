# Field Notes

> **Module Progress**

```text id="k9v5mt"
██████████░░░░░ 10 / 15 Lessons
```

- The following observations come from common practices used by experienced penetration testers.

- They are practical habits that improve efficiency, strengthen validation, and reduce the likelihood of incorrect conclusions.

---

## Understand the Application First

- Scanner becomes much more valuable after you understand how the application works.

- Before scanning:

  * Explore important functionality.
  * Observe authentication flows.
  * Identify sensitive areas.
  * Map important endpoints.

- A well-informed scan produces more meaningful findings.

---

## Configure With Intention

- Avoid treating scan settings as "set and forget."

- Instead, ask:

  * What am I trying to test?
  * Which scan type fits this objective?
  * Is active testing appropriate?
  * Are there areas that should be excluded?

- Configuration should support the investigation.

---

## Review Evidence Before Severity

- Severity labels help prioritize work, but they should never replace evidence.

- For each important finding:

  * Read the supporting description.
  * Review requests and responses.
  * Look for reproducible behavior.
  * Consider the application's context.

- Evidence should drive conclusions.

---

## Expect Imperfection

- No scanner is perfect.

- You will encounter:

  * False positives
  * False negatives
  * Incomplete coverage  
  * Unexpected behavior

- Treat these as normal parts of automated testing rather than signs that the tool has failed.

---

## Validate Early

- If a finding appears significant, validate it as soon as practical.

- Early validation helps:

  * Confirm important issues quickly.  
  * Avoid spending time on incorrect assumptions.
  * Improve confidence in the final report.

- Waiting until the end of an assessment may make reproduction more difficult.

---

## Keep Notes

- Record information such as:

  * Scan objectives.
  * Configuration choices.
  * Significant findings.
  * Validation results.
  * Lessons learned.
  * Areas requiring additional manual testing.

- These notes become valuable references for future engagements.

---

## Investigation Workflow

```text id="q4n7rv"
Understand Target

↓

Configure Scanner

↓

Run Assessment

↓

Review Evidence

↓

Validate Early

↓

Document Findings
```

- Good documentation strengthens both technical work and reporting quality.

---

## Scanner Challenge

- During a long assessment, Scanner reports several medium-confidence issues.

- Instead of immediately continuing the scan, ask:

  * Should any findings be validated now?
  * Would early confirmation change my testing priorities?
  * Are multiple findings related?
  * Does the evidence suggest a broader security issue?

- Professional testers adapt their investigations based on evidence.

---

## Professional Habits

- Experienced testers commonly:

  * Understand the application before scanning.
  * Configure deliberately.
  * Read evidence carefully.
  * Validate significant findings early.
  * Keep detailed investigation notes.
  * Use automation to support—not replace—critical thinking.

---

## Key Takeaways

* Scanner is most effective when guided by a clear objective.
* Configuration should match the engagement.
* Evidence matters more than severity labels.
* Early validation improves confidence.
* Documentation strengthens future investigations.
