# Performance & Safety

> **Module Progress**

```text
█████████░░░░░░ 9 / 15 Lessons
```

- Comparer is designed to be simple and efficient.

- In most situations, performance is not limited by the tool itself but by the size and complexity of the data being compared.

- Good investigation practices help you work more efficiently and reduce the risk of overlooking important differences.

---

## Performance Principles

- Comparer works best when you compare:

  * Related requests
  * Related responses
  * Similar JSON objects
  * Equivalent authentication states
  * Comparable API results

- The more focused the comparison, the easier it becomes to interpret the output.

---

## Compare Small Before Large

- Instead of comparing hundreds of unrelated responses:

- Compare:

  * One representative success
  * One representative failure

- Then expand your investigation only if necessary.

- Smaller comparisons are usually easier to understand and validate.

---

## Avoid Comparison Overload

- Large responses may contain hundreds of highlighted differences.

- Before comparing, ask:

  * What question am I trying to answer?
  * Which two items best answer that question?

- Reducing unnecessary data improves analysis.

---

## Handle Dynamic Data Carefully

- Many applications generate values that naturally change.

- Examples include:

  * Session identifiers
  * CSRF tokens
  * Request IDs
  * Timestamps
  * Random values
  * Nonces

- These differences are expected.

- Do not mistake normal application behavior for a security issue.

---

## Validate Before Reporting

- A highlighted difference is only the beginning.

- Always verify:

  * Can the result be reproduced?
  * Does it consistently affect application behavior?
  * Is the difference caused by your test or by expected application logic?

- Only confirmed findings belong in a report.

---

## Protect Sensitive Data

- Comparer is often used with:

  * Session cookies
  * Authorization headers
  * JWTs
  * Personal information
  * API responses

- Treat this data responsibly.

- Good practices include:

  * Avoid sharing sensitive captures publicly.
  * Remove secrets before creating documentation.
  * Use sanitized examples in write-ups and presentations.
  * Store project files securely.

---

## Organize Your Comparisons

- Keep comparisons meaningful.

- Examples:

  - ✅ User vs. Admin

  - ✅ Before vs. After Login

  - ✅ Authenticated vs. Anonymous

  - ✅ Original vs. Modified Request

- Avoid vague comparisons such as:

- ❌ Response 17 vs. Response 42

- Without context, interpretation becomes difficult.

---

## Work Methodically

- Professional investigations follow a repeatable process:

  ```text
  Define Question

          │

          ▼

  Choose Two Related Items

          │

          ▼

        Compare
  
          │

          ▼

  Classify Differences

          │

          ▼

      Validate

          │

          ▼

      Document
  ```

- Consistency improves both speed and accuracy.

---

## Common Performance Pitfalls

- Avoid:

  * Comparing unrelated responses.
  * Investigating every highlighted difference equally.
  * Ignoring expected variation.
  * Drawing conclusions without validation.
  * Skipping documentation after confirming important behavior.

---

## Safety Checklist

- Before comparing:

  * [ ] I know why these two items are being compared.
  * [ ] The comparison supports a specific investigation.
  * [ ] Sensitive information is handled responsibly.

- After comparing:

  * [ ] Important differences were validated.
  * [ ] Expected variation was ignored.
  * [ ] Observations were documented accurately.
  * [ ] Any shared examples were sanitized.

---

## Difference Challenge

- You compare two API responses.

- Comparer highlights:

  * Different timestamp
  * Different request ID
  * Additional `"permissions"` field
  * Status changed from `403` to `200`
  * New session cookie

- Questions:

  1. Which differences are expected?
  2. Which require validation?
  3. Which could affect security?
  4. Which would you document first?

---

## Key Takeaways

* Focused comparisons are easier to interpret than broad comparisons.
* Dynamic values should not automatically be treated as findings.
* Validate meaningful differences before reporting them.
* Protect sensitive information during analysis and documentation.
* A structured workflow improves both performance and reliability.
