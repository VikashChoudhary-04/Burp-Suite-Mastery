# Field Notes

> **Module Progress**

```text
██████████░░░░░ 10 / 15 Lessons
```

- Field notes are practical observations gathered through repeated investigation.

- They are not strict rules.

- Instead, they are habits and heuristics that help you work more effectively when using Comparer.

---

## Field Note 1 — Compare With a Question

- Before opening Comparer, write down the question you're trying to answer.

- Examples:

  * Did authentication change the response?
  * Did parameter tampering expose more data?
  * Did the user's role affect permissions?

- Without a question, comparisons become unfocused.

---

## Field Note 2 — Name Your Comparisons

- Mentally label the two items you're comparing.

- Examples:

  * User vs. Admin
  * Before Login vs. After Login
  * Original vs. Modified
  * Success vs. Failure

- Clear labels reduce confusion during longer investigations.

---

## Field Note 3 — Ignore the Obvious Noise

- Many differences are expected.

- Common examples include:

  * Timestamps
  * Request IDs
  * Session identifiers
  * CSRF tokens
  * Random values

- Recognize them quickly so you can focus on behavior-changing differences.

---

## Field Note 4 — One Difference Can Explain Everything

- Do not judge importance by quantity.

- A single change such as:

  ```http
  HTTP/1.1 403 Forbidden
  ```

becoming

  ```http
  HTTP/1.1 200 OK
  ```

may be more important than dozens of cosmetic differences.

- Always ask:

  > **Which change best explains the application's behavior?**

---

## Field Note 5 — Compare Equivalent Scenarios

- Comparer works best when both inputs answer the same question.

- Good comparisons:

  * Same endpoint, different users
  * Same request, different parameter
  * Same API call, different authentication state

- Poor comparisons:

  * Different endpoints
  * Different workflows
  * Unrelated responses

- Equivalent scenarios produce clearer evidence.

---

## Field Note 6 — Validate Outside Comparer

- After identifying a meaningful difference:

  * Reproduce it manually.
  * Confirm the behavior.
  * Verify consistency.

- Comparer supports investigation.

- Validation confirms findings.

---

## Field Note 7 — Record Observations Immediately

- Don't rely on memory.

- Capture:

  * What changed
  * Why it matters
  * How you verified it
  * What to test next

- Small details are easy to forget during long assessments.

---

## Field Note 8 — Think Beyond the Difference

- Every difference should generate another question.

- Example:

  - Difference:

    ```json
    "role": "admin"
    ```

- Follow-up questions:

  * Who controls this value?
  * Can another user trigger it?
  * Does the server validate it?
  * Does behavior actually change?

- Differences are starting points, not conclusions.

---

## Field Note 9 — Compare Representative Samples

- If you have hundreds of responses:

  - Do not compare them all.

- Start with:

  * One successful response
  * One failed response

- Expand only if those comparisons reveal meaningful behavior.

---

## Field Note 10 — Context Wins

- The same difference can be harmless in one investigation and critical in another.

- For example:

  - A changed cookie value may be expected after login.

- The same cookie changing unexpectedly during a privileged action could justify deeper investigation.

- Never interpret a difference without considering the surrounding context.

---

## Investigation Habits

- Experienced testers typically:

  * Define a hypothesis first.
  * Compare equivalent evidence.
  * Ignore expected variation.
  * Prioritize behavioral changes.
  * Validate important findings.
  * Keep concise investigation notes.

- These habits improve both speed and accuracy.

---

## Difference Challenge

- You compare two responses.

- Comparer highlights:

  * New timestamp
  * New request ID
  * Added `"permissions"` array
  * Status changed from `403` to `200`
  * Different session cookie

- Before investigating, answer:

  1. Which changes are probably expected?
  2. Which change would you validate first?
  3. Which difference is most likely to explain the behavioral change?
  4. What evidence would convince you that a vulnerability exists?

---

## Key Takeaways

* Start every comparison with a clear investigative question.
* Compare equivalent scenarios whenever possible.
* Separate expected variation from meaningful changes.
* Validate important observations outside Comparer.
* Let context—not assumptions—guide your conclusions.
