# Attack Types

## Overview

- Attack types determine **how Intruder combines payloads** and generates requests.

- Choosing the correct attack type is one of the most important decisions when configuring an Intruder attack.

- Instead of memorizing four names, think of each attack type as a different way to design an experiment.

- The question is never:

  > "Which attack type do I remember?"

- The question is:

  > "What experiment am I trying to perform?"

---

## Learning Objectives

- By the end of this lesson, you will be able to:

  * Explain every Intruder attack type.
  * Select the correct attack type for a given objective.
  * Predict how requests will be generated.
  * Avoid unnecessary requests.
  * Build efficient automation workflows.

---

## Prerequisites

* Intruder Introduction
* Intruder Interface
* Behind the Scenes

---

## Difficulty

🟠 Intermediate

---

## Estimated Time

| Activity          |          Time |
| ----------------- | ------------: |
| Reading           |        30 min |
| Hands-on Practice |        45 min |
| Exercises         |        30 min |
| **Total**         | **≈ 105 min** |

---

## Mental Model

- Every attack type answers a different question.

```text
One Position?
        │
      Yes ─────► Sniper
        │
       No
        │
Same Payload Everywhere?
        │
      Yes ─────► Battering Ram
        │
       No
        │
One-to-One Mapping?
        │
      Yes ─────► Pitchfork
        │
       No
        │
Every Combination?
        │
      Yes ─────► Cluster Bomb
```

- Think about the experiment first.

---

## Decision Tree

```text
Need one variable?

↓

Sniper

----------------

Need one payload copied to multiple places?

↓

Battering Ram

----------------

Need two or more payload lists that advance together?

↓

Pitchfork

----------------

Need every possible combination?

↓

Cluster Bomb
```

---

## Sniper

### Purpose

- Modify **one payload position at a time**.

- This is the simplest and most commonly used attack type.

- Example request:

  ```http  
  GET /profile?id=15
  ```

- Payload list:

  ```text
  15
  16
  17
  18
  ```

- Generated requests:

  ```text
  id=15

  ↓

  id=16

  ↓

  id=17

  ↓

  id=18
  ```

### Best For

* IDs
* Usernames
* Single parameters
* API values
* Focused experiments

---

### Real Example

- Objective:

  - Determine how different account IDs affect the response.

- Only the account ID changes.

- Everything else remains identical.

- This isolates the effect of one variable.

---

## Battering Ram

### Purpose

- Use **the same payload value** in multiple positions within the same request.

- Example:

  ```http
  username=test
  email=test@example.com
  ```

- Payload:

  ```text
  alice
  ```

- Generated request:

  ```text
  username=alice

  email=alice
  ```

- Then:

  ```text
  bob

  ↓

  username=bob

  email=bob
  ```

- Every marked position receives the same payload.

---

### Best For

* Matching values
* Repeated identifiers
* Tokens appearing in multiple places
* Consistency testing

---

## Pitchfork

### Purpose

- Advance multiple payload lists **together**.

- Example:

  - List A

    ```text
    alice
    bob
    charlie
    ```

  - List B

    ```text
    alice@example.com
    bob@example.com
    charlie@example.com
    ```

- Generated requests:

  ```text
  alice + alice@example.com

  ↓

  bob + bob@example.com

  ↓

  charlie + charlie@example.com
  ```

- The first item from each list is paired together, then the second, and so on.

---

### Best For

* Related values
* Username/password pairs (only in authorized testing)
* User/email mappings
* Data that naturally belongs together

---

## Cluster Bomb

### Purpose

- Generate **every possible combination** of multiple payload lists.

- Example:

  - List A

    ```text
    admin
    guest
    ```

- List B

    ```text
    123  
    456
    ```

- Generated requests:

  ```text
  admin + 123

  admin + 456

  guest + 123

  guest + 456  
  ```

- This creates the Cartesian product of the payload lists.

---

### Best For

* Exhaustive testing
* Combination analysis
* Small, carefully chosen payload sets

---

## Performance & Safety

> [!IMPORTANT]
> Cluster Bomb can generate very large numbers of requests.

- Before launching an attack:

  * Estimate the total request count.
  * Reduce payload lists where possible.
  * Confirm the request volume is appropriate for the authorized engagement.

- Large request counts increase execution time and may place unnecessary load on the target.

---

## Comparison Table

| Attack Type   | Positions | Payload Lists | Typical Use                     |
| ------------- | --------: | ------------: | ------------------------------- |
| Sniper        |       One |           One | Single-variable testing         |
| Battering Ram |  Multiple |           One | Same value in several positions |
| Pitchfork     |  Multiple |      Multiple | One-to-one value mapping        |
| Cluster Bomb  |  Multiple |      Multiple | Every possible combination      |

---

## Which Attack Type Should I Choose?

- Ask yourself:

### Am I changing only one value?

→ Sniper

---

### Do multiple positions need the same value?

→ Battering Ram

---

### Do payload lists belong together?

→ Pitchfork

---

### Do I need every possible combination?

→ Cluster Bomb

---

## Professional Workflow

```text
Define Objective

↓

Choose Payload Positions

↓

Estimate Request Count

↓

Select Attack Type

↓

Run Small Test

↓

Review Results

↓

Scale If Needed

↓

Manual Verification
```

- Professionals start with small experiments before increasing scope.

---

## Field Notes

- If you're unsure which attack type to use:

  - Start with **Sniper**.

    - It is simpler, produces fewer requests, and makes result analysis easier.

  - Only move to more complex attack types when your investigation requires them.

---

## Common Mistakes

* Choosing Cluster Bomb unnecessarily.
* Using very large payload lists.
* Selecting the wrong attack type.
* Ignoring estimated request volume.
* Forgetting to verify interesting responses manually.

---

## Practice Exercises

- For each objective, choose the best attack type and explain why.

1. Test account IDs one at a time.
2. Insert the same session value into two headers.
3. Pair usernames with matching email addresses.
4. Test every combination of two small value lists.

---

## Interview Questions

1. What is the difference between Sniper and Cluster Bomb?
2. When would you choose Pitchfork?
3. Why is Battering Ram useful?
4. Why should Cluster Bomb be used carefully?
5. Which attack type do you choose when only one parameter changes?

---

## Quick Revision

* Sniper → One variable.
* Battering Ram → Same value everywhere.
* Pitchfork → Lists move together.
* Cluster Bomb → Every combination.
* Choose the attack type based on the experiment.

---

## Mastery Checklist

* [ ] I understand all four attack types.
* [ ] I can choose the correct attack type for an objective.
* [ ] I can estimate request volume.
* [ ] I understand the trade-offs between attack types.
* [ ] I completed the practice exercises.
