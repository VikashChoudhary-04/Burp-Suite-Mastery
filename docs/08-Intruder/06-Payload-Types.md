# Payload Types

## Overview

- A payload type determines **how Burp Intruder generates the values** that replace the selected payload positions.

- Choosing the right payload type is just as important as choosing the right payload position.

- The goal is not to send the largest possible list of values.

- The goal is to generate the **smallest set of values that answers your investigation question**.

---

## Learning Objectives

- By the end of this lesson, you will be able to:

  * Explain what a payload type is.
  * Select an appropriate payload type for different investigations.
  * Understand when to generate values and when to use predefined lists.
  * Build efficient payload sets.
  * Avoid unnecessary requests.

---

## Prerequisites

* Intruder Introduction
* Intruder Interface
* Attack Types
* Payload Positions

---

## Difficulty

🟡 Intermediate

---

## Estimated Time

| Activity          |         Time |
| ----------------- | -----------: |
| Reading           |       25 min |
| Hands-on Practice |       35 min |
| Exercises         |       25 min |
| **Total**         | **≈ 85 min** |

---

## Why Payload Types Matter

- Imagine you want to investigate product IDs.

- Would you use:

  * A list of common usernames?
  * Random strings?
  * Sequential numbers?

- Probably not.

- The payload should match the investigation objective.

- Good payload selection reduces noise and improves analysis.

---

## Mental Model

- Think of payloads as the data that powers your experiment.

```text
Investigation Question
        │
        ▼
Choose Payload Type
        │
        ▼
Generate Payload Values
        │
        ▼
Run Intruder
        │
        ▼
Analyze Results
```

---

## Decision Point

```text
Do I already know the values?
        │
      Yes ─────────► Use a predefined list
        │
       No
        │
Can the values be generated?
        │
      Yes ─────────► Use a generated sequence
        │
       No
        │
Create a focused custom list
```

---

## Common Payload Types

### Simple List

- Uses a predefined list of values.

- Examples:

  ```text
  100
  101
  102
  103
  104
  ```

- Best for:

  * IDs
  * Usernames
  * Email addresses
  * API keys (authorized testing)
  * Custom wordlists

---

### Numbers

- Generate numeric sequences automatically.

- Example:

  ```text  
  1  
  2
  3  
  4
  5
  ...
  100
  ```

- Best for:

  * Sequential identifiers
  * Pagination
  * Resource numbers

---

### Dates

- Generate date values.

- Useful when testing applications that process:

  * Booking dates
  * Expiration dates
  * Schedules
  * Time-based filters

---

### Character Blocks

- Generate combinations from defined character sets.

- Useful for:

  * Short tokens
  * Controlled pattern testing

- Be careful—large character sets can rapidly increase the number of requests.

---

### Custom Lists

- Sometimes the best payload is one you create yourself.

- Examples:

  ```text
  guest
  admin
  manager
  support
  developer
  ```

- Focused lists often outperform massive generic wordlists.

---

## Choosing the Right Payload Type

- Ask yourself:

### Are the values already known?

- Use a predefined list.

---

### Are the values sequential?

- Generate numbers.

---

### Are the values related to the application's business logic?

- Create a custom list.

---

### Will thousands of values improve the investigation?

- Often, no.

- Start small.

---

## Performance & Safety

> [!IMPORTANT]
> Larger payload sets increase execution time and request volume.

- Before launching an attack:

  * Remove duplicate values.
  * Remove irrelevant entries.
  * Start with a small sample.
  * Expand only if the results justify it.

- Efficient payload design is part of responsible testing.

---

## Professional Workflow

```text
Define Objective
        │
        ▼
Choose Payload Position
        │
        ▼
Choose Payload Type
        │
        ▼
Create Small Payload Set
        │
        ▼
Run Test
        │
        ▼
Review Results
        │
        ▼
Expand If Needed
```

- Professionals rarely begin with their largest wordlist.

---

## Real Example

- Objective:

  - Understand how product identifiers behave.

- Request:

  ```http
  GET /product?id=250 HTTP/1.1
  ```

- Payload type:

  - **Numbers**

- Payload values:

  ```text
  245
  246
  247
  248
  249
  250
  251
  252
  253
  254
  255
  ```

- Reason:

  - The values are sequential and closely related to the baseline request.

---

## Field Notes

- The best payload list is not the biggest one.

- It is the one that answers your investigation with the fewest requests.

---

## Common Mistakes

* Using huge payload lists immediately.
* Choosing a payload type unrelated to the objective.
* Forgetting to remove duplicate values.
* Expanding the payload set before reviewing initial results.
* Assuming more requests always produce better findings.

---

## Practice Exercises

- For each objective, choose the best payload type.

  1. Test invoice numbers from 500–550.
  2. Test a list of employee usernames.
  3. Test common language codes.
  4. Test sequential order IDs.

- Explain why your chosen payload type is appropriate.

---

## Interview Questions

1. What is a payload type?
2. How does a payload type differ from a payload position?
3. Why should payloads match the investigation objective?
4. Why should you begin with a small payload set?
5. When would you use a custom payload list?

---

## Quick Revision

* Payload positions define **where** values go.
* Payload types define **what** values are used.
* Start with focused payloads.
* Expand only when necessary.
* Match the payload type to the investigation.

---

## Mastery Checklist

* [ ] I understand payload types.
* [ ] I can select the appropriate payload type.
* [ ] I build focused payload lists.
* [ ] I understand why smaller payload sets are often better.
* [ ] I completed the practice exercises.
