# Performance & Safety

> **Module Progress**

```text id="t6g6cz"
█████████░░░░░░ 9 / 15 Lessons
```

- Sequencer itself does not exploit vulnerabilities.

- However, collecting large numbers of security-critical values can affect both the quality of your analysis and the target application.

- Professional testers collect evidence responsibly while minimizing unnecessary impact.

---

## Collect Responsibly

- Large sample sizes improve statistical confidence, but every request still reaches the application.

- Before collecting thousands of values, consider:

  * Scope authorization
  * Rate limits
  * Test environment availability
  * Application stability
  * Client requirements

- Never collect more data than necessary.

---

## Use Consistent Conditions

- Reliable analysis depends on consistent sample collection.

- Whenever possible, keep the following constant:

  * Same application workflow
  * Same endpoint
  * Same authentication state
  * Same token type
  * Same environment

- Changing conditions during collection may reduce confidence in the results.

---

## Avoid Mixed Datasets

- Do not combine different token types in a single analysis.

- For example, avoid mixing:

  * Session identifiers
  * Password reset tokens
  * CSRF tokens
  * API authentication tokens

- Each token type has a different purpose and generation process.

- Analyze them separately.

---

## Respect Rate Limits

- Some applications generate a new token for every request.

- Rapid automated collection may:

  * Trigger rate limiting
  * Activate monitoring systems
  * Cause temporary account restrictions
  * Distort normal application behavior

- Collect samples at a pace appropriate for the assessment.

---

## Monitor Application Behavior

- While collecting samples, watch for changes such as:

  * Authentication state changes
  * Error responses
  * Session expiration
  * CAPTCHA challenges
  * Security controls
  * Infrastructure issues

- Unexpected changes may affect the validity of your dataset.

---

## Validate Sample Quality

- Before trusting the statistical report, verify:

  * All samples represent the same token type.
  * Collection completed successfully.
  * No corrupted values were captured.
  * The application behaved consistently throughout the process.

- High-quality input improves confidence in the output.

---

## Investigation Workflow

```text id="zzn1gx"
Plan Collection

↓

Collect Consistently

↓

Monitor Application

↓

Validate Dataset

↓

Analyze

↓

Interpret Results
```

- Careful preparation produces more reliable evidence.

---

## Prediction Challenge

- You begin collecting session identifiers.

- After several hundred requests, the application introduces a CAPTCHA challenge and changes how sessions are created.

- Ask yourself:

  * Should the original and new tokens be analyzed together?
  * Would restarting the collection improve consistency?
  * How might this change affect the statistical results?

- Good investigators recognize when the dataset has changed.

---

## Safety Checklist

- Before Collection:

  * [ ] I have authorization to test.
  * [ ] I understand the application scope.
  * [ ] I selected the correct token.
  * [ ] I planned a consistent collection process.

- During Collection:

  * [ ] I monitored application behavior.
  * [ ] I respected rate limits.
  * [ ] I noted unexpected changes.
  * [ ] I avoided mixing datasets.

- After Collection:

  * [ ] I verified sample quality.
  * [ ] I documented collection conditions.
  * [ ] I recorded any limitations.
  * [ ] I interpreted results in context.

---

## Common Mistakes

- ❌ Collecting inconsistent samples.

- ❌ Mixing unrelated token types.

- ❌ Ignoring changes in application behavior.

- ❌ Assuming larger datasets automatically guarantee better conclusions.

- ❌ Forgetting to document collection conditions.

---

## Key Takeaways

* Responsible collection improves both safety and evidence quality.
* Consistent datasets produce more meaningful analysis.
* Monitor the application throughout collection.
* Validate the dataset before trusting statistical results.
* Document limitations and environmental changes.

