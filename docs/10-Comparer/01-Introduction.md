# Introduction

> **Module Progress**

```text
█░░░░░░░░░░░░░░ 1 / 15 Lessons
```

- Comparer is one of Burp Suite's most overlooked tools.

- Many beginners assume it is simply a text comparison utility. In reality, it is an investigation aid that helps security testers identify meaningful differences between pieces of data that may influence an application's behavior.

- During a penetration test, you'll constantly compare information such as:

  * Two HTTP requests
  * Two HTTP responses
  * Cookies before and after login
  * JWTs from different users
  * JSON API responses
  * HTTP headers
  * CSRF tokens
  * Session identifiers

- Some differences are obvious.

- Others may consist of a single character that changes the outcome of an entire request.

- Finding those differences manually is slow and error-prone.

- Comparer helps you detect them quickly.

---

## Why Difference Analysis Matters

- Many web application vulnerabilities reveal themselves through small changes.

- Examples include:

  * An HTTP status code changing from **403** to **200**
  * A response body exposing additional data
  * A missing authorization header
  * A modified cookie
  * A changed user identifier
  * An additional JSON field
  * A different response length

- These changes often provide the first indication of:

  * Broken access control
  * IDOR
  * Authentication flaws
  * Business logic issues  
  * Information disclosure
  * API inconsistencies

- Professional testers learn to ask one question repeatedly:

  > **"What changed, and why?"**

- Comparer helps answer that question.

---

## Difference Analysis vs. Reading

- Reading a request tells you what it contains.

- Comparing two requests tells you what changed.

- That distinction is important.

- Reading focuses on understanding a single piece of evidence.

- Difference analysis focuses on identifying relationships between multiple pieces of evidence.

---

## Where You'll Use Comparer

- Throughout a security assessment, Comparer can assist with:

  * Authorization testing
  * Authentication testing
  * API testing
  * Session analysis
  * Cookie analysis
  * JWT analysis
  * Response comparison
  * Header comparison
  * Manual verification after Intruder attacks
  * Bug bounty investigations

- Rather than replacing your judgment, Comparer helps you focus it.

---

## Investigation Mindset

- Before opening Comparer, ask yourself:

  1. What am I comparing?
  2. Why am I comparing it?
  3. Which differences are likely to matter?
  4. Which differences are probably cosmetic?

- Comparer highlights differences.

- You determine their significance.

---

## Difference Challenge

- Consider these two HTTP status lines:

  ```http
  HTTP/1.1 200 OK
  ```

  ```http
  HTTP/1.1 403 Forbidden
  ```

- Ask yourself:

  * What changed?
  * Why is that change important?
  * What might it suggest about authentication or authorization?

- This is the type of thinking you'll practice throughout this module.

---

## Key Takeaways

* Comparer identifies differences between two pieces of data.
* Small changes can have major security implications.
* Difference analysis is a core penetration testing skill.
* Comparer supports investigations; it does not replace analysis.
* Always ask **what changed and why it matters**.
