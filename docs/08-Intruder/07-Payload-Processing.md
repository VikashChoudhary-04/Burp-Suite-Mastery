# Payload Processing

## Overview

- Payload Processing allows Burp Intruder to transform payloads before inserting them into requests.

- Instead of sending payloads exactly as they appear in your payload list, Intruder can modify, encode, format, or otherwise transform them.

- This makes your attacks more flexible while reducing the need to create multiple payload lists.

- The goal is not to apply every available transformation.

- The goal is to apply only the processing needed to answer your investigation question.

---

## Learning Objectives

- By the end of this lesson, you will be able to:

  * Explain what payload processing is.
  * Understand why payload processing exists.
  * Recognize common payload transformations.
  * Decide when processing is appropriate.
  * Build cleaner and more maintainable Intruder attacks.

---

## Prerequisites

* Payload Types
* Payload Positions
* Attack Types

---

## Difficulty

🟠 Intermediate

---

## Estimated Time

| Activity  |         Time |
| --------- | -----------: |
| Reading   |       30 min |
| Practice  |       40 min |
| Exercises |       25 min |
| **Total** | **≈ 95 min** |

---

## Why Payload Processing Exists

- Suppose your payload list contains:

  ```text
  alice
  bob
  charlie
  ```

- But the application expects:

  ```text
  ALICE
  BOB
  CHARLIE
  ```

- Instead of creating a second payload list, Burp can transform each payload automatically before sending the request.

- This keeps your payload lists reusable and your attack configuration easier to maintain.

---

## Mental Model

- Think of payload processing as a pipeline.

  ```text
  Payload

  ↓

  Processing Rule 1

  ↓

  Processing Rule 2

  ↓

  Final Payload

  ↓

  HTTP Request
  ```

- Each processing rule receives the output of the previous rule.

---

## Decision Point

```text
Will the original payload work?

        │

      Yes ─────────► Don't process it

        │

       No

        │

Can Burp transform it automatically?

        │

      Yes ─────────► Add only the required processing

        │

       No

        │

Create a new payload list
```

- Keep processing as simple as possible.

---

## Common Processing Operations

- Examples include:

  * Convert to uppercase
  * Convert to lowercase
  * URL encode
  * Base64 encode
  * Add prefixes
  * Add suffixes
  * Replace text
  * Trim characters

- The available operations depend on your Burp Suite version.

---

### Example 1 — Prefix

- Payload list:

  ```text
  100
  101
  102
  ```

- Processing rule:

  ```text
  Prefix: user-
  ```

- Generated values:

  ```text
  user-100
  user-101
  user-102
  ```

---

### Example 2 — Suffix

- Payload:

  ```text
  alice
  ```

- Processing:

  ```text
  Suffix: @example.com
  ```

- Result:

  ```text
  alice@example.com
  ```

---

### Example 3 — URL Encoding

- Original:

  ```text
  admin test
  ```

- Processed:

  ```text
  admin%20test
  ```

- Useful when values must be transmitted safely within URLs or parameters.

---

### Example 4 — Uppercase

- Original:

  ```text
  admin
  ```

- Processed:

  ```text
  ADMIN
  ```

---

## Chaining Operations

- Multiple processing rules can be combined.

- Example:

  - Original:

    ```text
    alice
    ```

- Pipeline:
  
  ```text
  Uppercase

  ↓
  
  Prefix: USER-

  ↓

  Suffix: -TEST
  ```

- Final value:

  ```text  
  USER-ALICE-TEST
  ```

- Every step builds on the previous one.

---

## Performance & Safety

> [!IMPORTANT]
> Complex processing chains make attacks harder to understand.

- Before adding another rule, ask:

  * Does this help answer my investigation?
  * Could I simplify the payload list instead?
  * Will someone else understand this configuration later?

- Readable configurations are easier to review and reproduce.

---

## Professional Workflow

```text
Choose Payload

↓

Decide If Processing Is Needed

↓

Apply Minimal Transformations

↓

Generate Test Requests

↓

Verify Payload Values

↓

Run Attack

↓

Review Results
```

- Always verify the generated payloads before launching a large attack.

---

### Real Example

- Objective:

  - Test employee email addresses.

- Payload list:

  ```text
  alice
  bob
  charlie
  ```

- Processing:

  ```text
  Suffix: @company.local
  ```

- Generated values:

  ```text
  alice@company.local
  bob@company.local  
  charlie@company.local
  ```

- One reusable payload list supports many different investigations.

---

## Field Notes

- Beginners often create many nearly identical payload lists.

- Experienced testers maintain one clean payload list and transform it only when necessary.

- Simple configurations are easier to troubleshoot.

---

## Common Mistakes

* Applying unnecessary processing.
* Forgetting the order of processing rules.
* Creating long transformation chains.
* Not verifying processed payloads before running an attack.
* Using processing when a simpler payload list would suffice.

---

## Practice Exercises

- For each scenario, decide whether payload processing is appropriate.

1. Add `@example.com` to usernames.
2. Convert product codes to uppercase.
3. Prefix numeric IDs with `user-`.
4. Send URL-safe search terms.

- Explain your reasoning.

---

## Interview Questions

1. What is payload processing?
2. Why is payload processing useful?
3. What happens when multiple processing rules are chained?
4. When should you avoid payload processing?
5. Why should processed payloads be verified before starting an attack?

---

## Quick Revision

* Payload processing transforms values before sending requests.
* Apply only the processing your investigation requires.
* Processing rules are executed in order.
* Simpler configurations are easier to understand.
* Verify processed payloads before launching attacks.

---

## Mastery Checklist

* [ ] I understand payload processing.
* [ ] I know when processing is appropriate.
* [ ] I can explain common processing operations.
* [ ] I understand processing order.
* [ ] I completed the practice exercises.
