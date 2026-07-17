# Interface

> **Module Progress**

```text
███░░░░░░░░░░░░ 3 / 15 Lessons
```

- The Comparer interface is intentionally simple.

- Unlike tools such as Intruder or Repeater, Comparer is designed around a single task:

  > **Compare two pieces of data and highlight their differences.**

- Its minimal interface allows you to focus on analysis rather than configuration.

---

## Opening Comparer

- Comparer can receive data from several Burp Suite tools.

- Common sources include:

  * Proxy
  * Repeater
  * Intruder
  * Decoder
  * HTTP History
  * Site Map
  * Manually pasted content

- This flexibility makes Comparer useful throughout an assessment.

---

## Main Interface Layout

- The Comparer window typically consists of:

  1. Data Panel A
  2. Data Panel B
  3. Comparison View
  4. Navigation Controls
  5. Difference Indicators

- Each component supports a different stage of the comparison process.

---

## Data Panels

- The left and right panels contain the two items being compared.

- Examples include:

  * Two HTTP requests
  * Two HTTP responses
  * Two JWTs
  * Two JSON objects
  * Two headers
  * Two cookies

- Think of these panels as your **evidence sources**.

- Comparer never changes the original data—it only analyzes it.

---

## Comparison View

- The comparison view highlights where the two items differ.

- Rather than forcing you to read everything line by line, Comparer directs your attention to the sections that changed.

- This helps reduce cognitive load during investigations.

---

## Navigation Controls

- Large requests and responses may contain many differences.

- Navigation controls allow you to move efficiently between highlighted changes.

- Instead of scrolling through hundreds of lines, you can jump directly from one difference to the next.

---

## Difference Indicators

- Comparer visually marks additions, removals, and modifications.

- These indicators answer questions such as:

  * Which value changed?
  * Was a header added?
  * Was a parameter removed?
  * Did the response body change?
  * Did only one character change?

- Remember:

  - A highlighted difference is **evidence**, not proof of a vulnerability.

---

## Typical Workflow

- A common investigation looks like this:

  ```text
  Proxy / Repeater / Intruder

          │

          ▼

  Select Two Items

          │

          ▼
  
  Send to Comparer

          │

          ▼

  Review Differences

          │

          ▼

  Interpret Security Impact

          │

          ▼

  Continue Testing
  ```

- Notice that the final step is **interpretation**.

- Comparer identifies changes; you determine whether they matter.

---

## Practical Example

- Suppose you have:

  - **Request A**

  ```http
  GET /api/profile HTTP/1.1
  Authorization: Bearer user_token
  ```

  - **Request B**

  ```http
  GET /api/profile HTTP/1.1
  Authorization: Bearer admin_token
  ```

- Comparer immediately highlights the changed authorization token.

- Your task is then to investigate whether that difference results in different application behavior.

---

## Interface Best Practices

* Compare related data only.
* Label your comparisons mentally (for example, *User vs Admin*).
* Keep the comparison goal clear before loading data.
* Review every highlighted difference before reaching conclusions.
* Ignore expected variations such as timestamps unless they are relevant.

---

## Difference Challenge

- You compare two responses.

- Comparer highlights:

  * A changed status code.
  * A modified cookie.
  * An additional JSON field.
  * A different response length.

- Ask yourself:

  1. Which change is most likely to affect application behavior?
  2. Which change might simply be informational?
  3. What would you investigate next?

- Practice identifying the differences that deserve the most attention.

---

## Key Takeaways

* Comparer has a simple interface focused on analysis.
* Data panels contain the items being compared.
* Difference indicators guide your attention.
* Navigation controls help with large comparisons.
* The interface supports investigation—it does not replace reasoning.
