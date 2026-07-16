# Quick Revision

> **Estimated Reading Time:** 2–3 Minutes

- Use this page before:

  * Technical interviews
  * Bug bounty hunting
  * Penetration tests
  * Labs
  * CTFs
  * Practice sessions

---

## What is Decoder?

- Burp Decoder is a local data transformation and inspection tool.

- It helps you:

  * Understand transformed data.
  * Inspect common encodings.
  * Recognize data formats.
  * Support investigations.

- Decoder **does not communicate** with the target application.

---

## Investigation Workflow

```text
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

- Recognition always comes before transformation.

---

## Core Concepts

| Concept     | Purpose                     |
| ----------- | --------------------------- |
| Encoding    | Change representation       |
| Decoding    | Recover representation      |
| Encryption  | Protect confidentiality     |
| Decryption  | Recover protected data      |
| Hashing     | Produce a fingerprint       |
| Compression | Reduce size                 |
| Escaping    | Preserve meaning in context |

- Know **why** each concept exists.

---

## Recognition Clues

### URL Encoding

- Look for:

  ```text
  %20
  %2F
  %3D
  ```

---

### Base64

- Often:

  * Ends with `=`
  * Uses Base64 character set

---

### Hexadecimal

- Look for:

  * `0–9`
  * `A–F`
  * `a–f`

- Often even length.

---

### JWT

- Usually:

  ```text
  xxxxx.yyyyy.zzzzz
  ```

- Three sections separated by periods.

---

### Hash Candidate

- Often:

  * Fixed length
  * Hexadecimal

- Do **not** assume it can be decoded.

---

## Professional Workflow

```text
Proxy

↓

Repeater

↓

Unknown Value

↓

Decoder

↓

Understand Data

↓

Continue Investigation
```

- Decoder supports the investigation.

- It does not replace other Burp tools.

---

## Decoder Golden Rules

### Rule 1

- Observe before acting.

---

### Rule 2

- Context matters.

---

### Rule 3

- Recognition before transformation.

---

### Rule 4

- Apply one justified transformation.

---

### Rule 5

- Verify every output.

---

### Rule 6

- Evidence before conclusions.

---

## Common Mistakes

- ❌ Assuming everything unreadable is encrypted.

- ❌ Confusing encoding with encryption.

- ❌ Trying to decode hashes.

- ❌ Ignoring context.

- ❌ Random transformations.

- ❌ Assuming readable means correct.

---

## Interview Revision

- Can you confidently explain:

  * What Decoder is?
  * Why it exists?
  * Encoding vs. encryption?
  * Decoding vs. decryption?
  * Common recognition clues?
  * Why context matters?
  * Why Decoder works locally?
  * How Decoder fits into a professional workflow?

- If not, revisit the relevant lesson.

---

## Recognition Checklist

- Before opening Decoder:

  * [ ] Observe the value.
  * [ ] Examine the surrounding context.
  * [ ] Identify recognition clues.
  * [ ] Form a hypothesis.
  * [ ] Apply one justified transformation.
  * [ ] Verify the output.

---

## Module Summary

- Congratulations!

- You have completed the **Decoder** module.

- You now understand:

  * Why Decoder exists.
  * How Decoder works.
  * How to recognize common data formats.
  * The difference between encoding, encryption, hashing, compression, and escaping.
  * Professional investigation workflows.
  * Recognition heuristics.
  * Common mistakes.
  * Evidence-based data interpretation.

- Decoder is more than a transformation tool.

- It is a way to understand data before making security decisions.
