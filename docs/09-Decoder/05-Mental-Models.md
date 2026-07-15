# Mental Models

## Overview

- Professional security testers rarely identify data formats by memorizing long lists of encodings.

- Instead, they develop mental models—simple patterns that help them recognize likely formats quickly.

- These models are not rules.

- They are heuristics that guide your investigation.

- Always verify your assumptions using Decoder.

---

## Learning Objectives

- By the end of this lesson, you will be able to:

  * Recognize common data formats quickly.
  * Apply simple heuristics during investigations.
  * Distinguish observations from conclusions.
  * Decide which transformation to try first.
  * Build confidence when encountering unfamiliar data.

---

## Prerequisites

* Decoder Introduction
* Why Decoder Exists
* Decoder Interface
* Behind the Scenes

---

## Difficulty

🟡 Intermediate

---

## Estimated Time

| Activity             |         Time |
| -------------------- | -----------: |
| Reading              |       30 min |
| Recognition Practice |       35 min |
| Reflection           |       20 min |
| **Total**            | **≈ 85 min** |

---

## Mental Model 1 — Look Before You Decode

- Your first question should never be:

  > "How do I decode this?"

- Instead ask:

  > "What does this look like?"

- Observation always comes before transformation.

---

## Mental Model 2 — Context Matters

- The same value may have different meanings depending on where it appears.

- Examples:

  * Query parameter
  * Cookie
  * JWT
  * JSON field
  * Header
  * API response

- Always inspect the surrounding request or response before deciding on a transformation.

---

## Mental Model 3 — Recognition Before Action

- Follow this sequence:

  ```text id="rmz8l2"
  Observe

  ↓

  Recognize

  ↓

  Hypothesize
  
  ↓

  Transform

  ↓

  Verify

  ↓

  Continue Investigation
  ```

- Skipping the recognition step often leads to wasted effort.

---

## Recognition Clues

- These clues are **heuristics**, not guarantees.

### Base64

- Often:

  * Ends with `=` or `==`
  * Uses:

    * A–Z
    * a–z
    * 0–9
    * `+`
    * `/`

- May also appear in URL-safe form using `-` and `_` instead of `+` and `/`.

---

### Hexadecimal

- Usually:

  * Uses only:

    ```text id="j9fw3d"
    0–9
    A–F
    a–f
    ```

- Often has an even number of characters because two hexadecimal digits represent one byte.

---

### URL Encoding

- Usually contains:

  ```text id="4k2vwd"
  %20

  %2F

  %3A

  %3D
  ```  

- Look for `%` followed by two hexadecimal digits.

---

### JWT

- Usually appears as:

  ```text id="cb6x1u"
  xxxxx.yyyyy.zzzzz
  ```

- Three sections separated by periods.

- The first two sections are typically Base64URL-encoded JSON.

---

### Hashes

- May appear as long strings of hexadecimal characters.

- Unlike encodings, hashes are generally designed to be one-way.

- Do not assume that every hexadecimal string is a hash, or that every hash uses hexadecimal.

- Context is important.

---

## Mental Model 4 — One Step at a Time

- If a value is still unreadable after one transformation:

- Ask:

  * Was the transformation appropriate?
  * Could another layer exist?
  * Does the context support another step?

- Avoid repeatedly applying random transformations.

---

## Decision Tree

```text id="z4q8cn"
Unknown Value?

        │

        ▼

Observe Context

        │

        ▼

Recognize Likely Format

        │

        ▼

Choose One Transformation

        │

        ▼

Review Output

        │

        ▼

Makes Sense?

   │            │
  Yes          No
   │            │
Continue    Reassess Assumptions
```

---

## Professional Workflow

```text id="h3kp7n"
Capture Value

↓

Observe

↓

Recognize

↓

Transform

↓

Verify

↓

Document

↓

Continue Investigation
```

- Recognition is a skill that improves through repetition.

---

## Recognition Challenge

- For each value:

  1. Identify the most likely format.
  2. Explain your reasoning.
  3. Decide which Decoder transformation you would try first.

```text id="z7v2rn"
QnVycCBTdWl0ZQ==

%2Fapi%2Fv1%2Fusers%3Fid%3D42

48656c6c6f20576f726c64

eyJhbGciOiJIUzI1NiJ9.eyJ1c2VyIjoiYWRtaW4ifQ.signature

098f6bcd4621d373cade4e832627b4f6
```

- Do not use Decoder until you have made your predictions.

---

## Field Notes

- Experienced testers are comfortable saying:

  > "I don't know what this is yet."

- They gather evidence before acting.

- Confidence comes from methodical investigation, not guesswork.

---

## Common Mistakes

* Guessing instead of observing.
* Applying multiple transformations without a reason.
* Ignoring context.
* Assuming every unfamiliar value must be decoded.
* Treating heuristics as absolute rules.

---

## Interview Questions

1. Why are mental models useful?
2. What is the difference between a heuristic and a guarantee?
3. Why should context be considered before decoding?
4. What clues suggest a value is URL-encoded?
5. How would you recognize a JWT?

---

## Quick Revision

* Observe before acting.
* Context matters.
* Recognition comes before transformation.
* Apply one transformation at a time.
* Verify your assumptions.

---

## Mastery Checklist

* [ ] I understand the mental models.
* [ ] I recognize common format clues.
* [ ] I avoid random transformations.
* [ ] I completed the Recognition Challenge.
* [ ] I can explain why heuristics are not guarantees.
