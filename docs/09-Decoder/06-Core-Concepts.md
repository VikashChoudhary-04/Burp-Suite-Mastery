# Core Concepts

## Overview

- Before using Burp Decoder effectively, you need to understand several core concepts about how applications represent and transform data.

- Many mistakes occur because people confuse:

  * Encoding
  * Decoding
  * Encryption
  * Decryption
  * Hashing
  * Compression
  * Escaping

- These concepts solve different problems.

- Understanding their purpose is more important than memorizing individual formats.

---

## Learning Objectives

- By the end of this lesson, you will be able to:

  * Distinguish between common data transformation concepts.
  * Understand why each technique exists.
  * Recognize which operations are reversible.
  * Decide which Burp Decoder operations are appropriate.
  * Avoid common misconceptions.

---

## Prerequisites

* Mental Models
* Behind the Scenes

---

## Difficulty

🟠 Intermediate

---

## Estimated Time

| Activity   |         Time |
| ---------- | -----------: |
| Reading    |       35 min |
| Practice   |       40 min |
| Reflection |       20 min |
| **Total**  | **≈ 95 min** |

---

## Mental Model

- Every transformation has a purpose.

```text
Need Safe Transport?

↓

Encoding

----------------

Need Confidentiality?

↓

Encryption

----------------

Need Integrity Check?

↓

Hashing

----------------

Need Smaller Size?

↓

Compression

----------------

Need Safe Representation?

↓

Escaping
```

- Never choose a transformation before understanding its purpose.

---

## Encoding

### Purpose

- Represent data in another format.

- Encoding is commonly used to:

  * Transport data safely.
  * Represent binary data as text.
  * Preserve special characters.

- Examples include:

  * URL encoding
  * Base64
  * Hexadecimal

- Encoding is generally reversible.

---

## Decoding

### Purpose

- Recover the original representation from encoded data.

- Decoder performs many decoding operations.

- Examples:

  ```text
  SGVsbG8=

  ↓

  Hello
  ```

- Decoding assumes you already know or have correctly identified the encoding format.

---

## Encryption

### Purpose

- Protect confidentiality.

- Encryption transforms data using a cryptographic algorithm and key.

- Unlike encoding, encryption is designed to prevent unauthorized access.

- Without the appropriate key, you should not expect to recover the original plaintext.

---

## Decryption

### Purpose

- Recover encrypted data using the appropriate key.

- Burp Decoder may help inspect encrypted-looking values, but it does not replace cryptographic tools or keys.

---

## Hashing

### Purpose

- Produce a fixed-length fingerprint of data.

- Hashes are commonly used for:

  * Integrity verification
  * Password storage (typically with additional protections)
  * File verification

- A hash is generally intended to be one-way.

- Decoder cannot "decode" a hash back into its original input.

---

## Compression

### Purpose

- Reduce data size.

- Compressed data often needs to be decompressed before it becomes readable.

- Compression is different from encryption.

- Compressed data may appear random even though it is not encrypted.

---

## Escaping

### Purpose

- Represent special characters safely in a particular context.

- Examples include:

  * HTML entities
  * JSON escaping
  * XML escaping

- Escaping helps preserve the intended meaning of data within structured formats.

---

## Comparison Table

| Concept     | Primary Purpose         | Typically Reversible?       |
| ----------- | ----------------------- | --------------------------- |
| Encoding    | Representation          | Usually                     |
| Decoding    | Recover representation  | Depends on correct encoding |
| Encryption  | Confidentiality         | Yes, with the correct key   |
| Decryption  | Recover protected data  | Yes, with the correct key   |
| Hashing     | Fingerprint / Integrity | No (designed to be one-way) |
| Compression | Reduce size             | Yes                         |
| Escaping    | Safe representation     | Usually                     |

---

## Decision Tree

```text
Need to safely represent data?

        │

        ▼

Encoding

----------------

Need to protect secrecy?

        │

        ▼

Encryption

----------------

Need to verify integrity?

        │

        ▼

Hashing

----------------

Need to reduce size?

        │

        ▼

Compression
```

- Choose the operation based on the problem you're solving.

---

## Professional Workflow

```text
Observe Data

↓

Identify Purpose

↓

Recognize Format

↓

Choose Appropriate Operation

↓

Verify Result

↓

Continue Investigation
```

- Purpose drives the operation.

---

## Recognition Clues

| Observation                     | Possible Interpretation              |
| ------------------------------- | ------------------------------------ |
| `%20`, `%2F`                    | URL encoding                         |
| Ends with `=`                   | Often Base64                         |
| Even-length hexadecimal         | Hexadecimal or binary representation |
| Three sections separated by `.` | JWT                                  |
| Fixed-length fingerprint        | Possibly a hash                      |

- These are clues—not guarantees.

---

## Recognition Challenge

- For each value:

  1. Identify the likely concept involved.
  2. Explain your reasoning.
  3. Decide whether decoding is appropriate.

```text
SGVsbG8=

%7B%22id%22%3A42%7D

e3b0c44298fc1c149afbf4c8996fb92427ae41e4649b934ca495991b7852b855

48656c6c6f20576f726c64

eyJhbGciOiJIUzI1NiJ9.eyJyb2xlIjoiYWRtaW4ifQ.signature
```

- Remember:

  - Recognition comes before transformation.

---

## Field Notes

- Experienced testers spend more time identifying what a value represents than clicking transformation buttons.

- Correct identification prevents wasted effort.

---

## Common Mistakes

* Confusing encoding with encryption.
* Trying to decode hashes.
* Assuming unreadable means encrypted.
* Ignoring the context of the value.
* Applying transformations without understanding their purpose.

---

## Interview Questions

1. What is the difference between encoding and encryption?
2. Why can't a hash normally be decoded?
3. What is escaping?
4. Why is compression different from encryption?
5. How do you decide which transformation to apply?

---

## Quick Revision

* Every transformation has a purpose.
* Encoding is not encryption.
* Hashes are generally one-way.
* Compression reduces size.
* Escaping preserves meaning.
* Recognition guides transformation.

---

## Mastery Checklist

* [ ] I understand the purpose of each core concept.
* [ ] I can distinguish encoding, encryption, hashing, compression, and escaping.
* [ ] I know which operations are generally reversible.
* [ ] I completed the Recognition Challenge.
