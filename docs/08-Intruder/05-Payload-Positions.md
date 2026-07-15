# Payload Positions

## Overview

- A payload position is the part of an HTTP request that Burp Intruder replaces with different values during an attack.

- Choosing the correct payload position is often more important than choosing the payload itself.

- A well-chosen position produces meaningful results with relatively few requests.

- A poorly chosen position produces unnecessary traffic and confusing results.

---

## Learning Objectives

- By the end of this lesson, you will be able to:

  * Identify suitable payload positions.
  * Understand which request components are worth testing.
  * Recognize poor payload position choices.
  * Minimize unnecessary requests.
  * Design more efficient Intruder attacks.

---

## Prerequisites

* Intruder Introduction
* Intruder Interface
* Attack Types

---

## Difficulty

🟢 Beginner

---

## Estimated Time

| Activity          |         Time |
| ----------------- | -----------: |
| Reading           |       25 min |
| Hands-on Practice |       30 min |
| Exercises         |       25 min |
| **Total**         | **≈ 80 min** |

---

## Why Payload Positions Matter

- Every payload position represents a question.

- For example:

  > "What happens if this account ID changes?"

  or

  > "Does the application treat this cookie differently?"

- Selecting positions without understanding their purpose usually leads to poor experiments.

---

## Mental Model

- Think of a request as a document with editable fields.

```text
HTTP Request
      │
      ├── URL
      ├── Path
      ├── Parameters
      ├── Headers
      ├── Cookies
      └── Body
```

- Your job is to identify which field is most likely to answer your investigation question.

---

## Decision Point

```text
Does changing this value help answer my question?
        │
      No ─────────► Leave it unchanged
        │
       Yes
        │
Can the client control this value?
        │
      No ─────────► Probably not a useful position
        │
       Yes
        │
Use as a payload position
```

---

## Common Payload Positions

### Query Parameters

- Example:

  ```http
  GET /profile?id=15 HTTP/1.1
  ```

- Possible payload position:

  ```text
  15
  ```

- Typical investigations:

  * Resource identifiers
  * Pagination
  * Filters
  * Search terms

---

### URL Path

- Example:

  ```http
  GET /users/15/profile HTTP/1.1
  ```

- Payload position:

  ```text
  15
  ```

- Useful when identifiers are embedded in the path.

---

### Form Parameters

- Example:

  ```text
  username=alice
  ```

- Possible positions include:

  * Username
  * Email
  * Search field
  * Quantity
  * Coupon code

---

### JSON Fields

- Example:

  ```json  
  {
    "userId": 15,
    "role": "user"
  }
  ```

- Possible positions:

  * `userId`
  * `role`

- JSON APIs frequently expose useful investigation points.

---

### Cookies

- Example:

  ```http
  Cookie: language=en
  ```

- Possible position:

  ```text
  en
  ```

- Cookies may influence:

  * Preferences
  * Sessions
  * Localization
  * Feature flags

- Always understand the cookie's purpose before modifying it.

---

### Headers

- Examples:

  * Referer
  * Origin
  * User-Agent
  * X-Forwarded-For
  * Custom application headers

- Some headers influence application behavior.

- Others exist only for informational purposes.

---

## Positions That Usually Don't Need Payloads

- Not every editable value deserves automation.

- Examples include:

  * Host
  * Content-Length (normally handled automatically)
  * Standard browser headers that are unrelated to your objective

- Ask yourself:

  > "Will changing this help answer my investigation question?"

- If not, leave it unchanged.

---

## Good vs Poor Position Selection

### Good

- Objective:

  - Understand how an application handles account identifiers.

- Payload position:

  ```text
  id=15
  ```

- Reason:

  - The position is directly related to the investigation.

---

### Poor

- Objective:

  - Understand account identifiers.

- Payload position:

  ```text
  Accept-Language
  ```

- Reason:

  - This header is unlikely to answer the question.

---

## Performance & Safety

> [!IMPORTANT]
> Every additional payload position can significantly increase the number of generated requests, depending on the chosen attack type.

- Before selecting multiple positions:

  * Confirm each one supports your investigation.
  * Estimate the resulting request count.
  * Remove unnecessary positions.

---

## Professional Workflow

```text
Understand Request
        │
        ▼
Define Objective
        │
        ▼
Identify Candidate Positions
        │
        ▼
Select Only Relevant Positions
        │
        ▼
Choose Attack Type
        │
        ▼
Configure Payloads
        │
        ▼
Run Small Test
```

- Good position selection makes later analysis much easier.

---

## Real Example

- Objective:

  - Understand how the application behaves when different product IDs are requested.

- Request:

  ```http
  GET /product?id=125 HTTP/1.1
  ```

- Chosen payload position:

  ```text
  125
  ```

- Reason:

  - The product ID directly controls which resource the application returns.

---

## Field Notes

- Beginners often mark every editable value.

- Professionals usually mark only one or two carefully chosen positions.

- A focused attack is easier to understand, faster to run, and produces cleaner results.

---

## Common Mistakes

* Selecting too many positions.
* Choosing positions unrelated to the objective.
* Ignoring the purpose of the request.
* Forgetting how the attack type affects request count.
* Assuming every editable value is worth testing.

---

## Practice Exercises

- For each request:

  1. Identify all editable values.
  2. Choose the best payload position.
  3. Explain why you selected it.
  4. Identify one value you would deliberately leave unchanged.

- Repeat with:

  * Query parameters
  * JSON requests
  * Form submissions
  * Cookie-based requests

---

## Interview Questions

1. What is a payload position?
2. Why is position selection important?
3. Which request components commonly become payload positions?
4. Why shouldn't every editable value be selected?
5. How do payload positions affect the total number of requests?

---

## Quick Revision

* A payload position is where Intruder inserts payloads.
* Choose positions based on your objective.
* Not every editable value should be automated.
* Fewer, well-chosen positions usually produce better investigations.
* Good position selection improves analysis.

---

## Mastery Checklist

* [ ] I can identify suitable payload positions.
* [ ] I understand why position selection matters.
* [ ] I avoid unnecessary payload positions.
* [ ] I completed the practice exercises.
