# Common Mistakes

> **Module Progress**

```text
███████████░░░░ 11 / 15 Lessons
```

- Comparer is simple to use, but effective comparison requires disciplined thinking.

- Most mistakes occur because investigators jump to conclusions instead of following a structured process.

- This lesson highlights common pitfalls and how to avoid them.

---

## Mistake 1 — Comparing Without a Goal

- Opening Comparer without knowing what you're trying to learn often leads to confusion.

- Instead, begin with a clear question.

- Examples:

  * Did authentication change the response?
  * Did parameter tampering alter authorization?
  * Did the application expose additional data?

- A defined objective gives meaning to the comparison.

---

## Mistake 2 — Treating Every Difference as Important

- Comparer highlights every detected change.

- Not every change deserves investigation.

- Expected differences include:

  * Timestamps
  * Request IDs
  * Session identifiers
  * CSRF tokens
  * Random values

- Focus first on differences that could influence application behavior.

---

## Mistake 3 — Ignoring Context

- The same difference can have different meanings depending on where and when it appears.

- Example:

  - A new session cookie immediately after login is expected.

- A new session cookie during an unrelated API request may deserve further investigation.

- Always interpret differences within the application's workflow.

---

## Mistake 4 — Comparing Unrelated Data

- Poor comparisons often produce noisy results.

- Avoid comparing:

  * Different endpoints
  * Different features
  * Different workflows
  * Unrelated API calls

- Instead, compare equivalent scenarios whenever possible.

---

## Mistake 5 — Assuming a Difference Is a Vulnerability

- Comparer shows evidence.

- It does not confirm vulnerabilities.

- For example:

  ```http
  HTTP/1.1 403 Forbidden
  ```

becoming

  ```http
  HTTP/1.1 200 OK
  ```

is worth investigating.

- It is **not**, by itself, proof of broken access control.

- Always validate manually.

---

## Mistake 6 — Ignoring Small Changes

- Some investigators focus only on large differences.

- Many serious findings begin with a single value changing:

  * Role  
  * User ID
  * Permission
  * Boolean flag
  * Response code

- Small changes can produce major behavioral differences.

---

## Mistake 7 — Failing to Reproduce Findings

- If you cannot reproduce a difference consistently, treat it as an observation—not a confirmed finding.

- Reproduce important differences using:

  * Repeater
  * Additional requests
  * Alternative accounts
  * Controlled testing

- Consistency builds confidence.

---

## Mistake 8 — Poor Documentation

- After identifying an important difference, document:

  * What changed
  * Why it matters
  * How it was reproduced
  * What evidence supports your conclusion

- Good notes reduce mistakes during report writing.

---

## Mistake 9 — Chasing Cosmetic Differences

- Whitespace, formatting, and other presentation changes often distract beginners.

- Before investigating, ask:

  > **Does this difference change application behavior?**

- If not, it may simply be cosmetic.

---

## Mistake 10 — Stopping at the First Interesting Difference

- Finding one important change does not necessarily end the investigation.

- Continue asking:

  * What caused this difference?
  * Can another user reproduce it?
  * Does it affect other endpoints?
  * Does it expose additional attack paths?

- Comparer helps you discover leads—not necessarily final answers.

---

## Avoiding These Mistakes

- Professional testers generally follow this process:

  ```text
  Define Question

          │

          ▼

  Compare Related Evidence

          │

          ▼

  Classify Differences

          │
  
          ▼
  
  Validate Important Changes

          │
  
          ▼

  Document Verified Findings
  ```

- A repeatable process reduces errors and improves investigation quality.

---

## Difference Challenge

- Comparer highlights:

  * Updated timestamp
  * New request ID
  * `403` → `200`
  * `"role": "user"` → `"role": "admin"`
  * Added `"permissions"` field

- Before investigating:

  1. Which differences are likely expected?
  2. Which deserve immediate attention?
  3. Which require manual validation?
  4. Which should appear in a security report only after confirmation?

---

## Key Takeaways

* Compare with a clear objective.
* Prioritize behavioral changes over cosmetic ones.
* Interpret differences in context.
* Validate findings before reporting them.
* Treat Comparer as an investigation aid, not an automated decision-maker.
