# Decoder Cheat Sheet

> **Purpose:** A quick operational reference for recognizing and transforming common data formats during investigations.

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

- Never skip the **Recognize** step.

---

## Recognition Clues

| Observation                     | Likely Format | First Action             |
| ------------------------------- | ------------- | ------------------------ |
| `%20`, `%2F`, `%3D`             | URL Encoding  | URL Decode               |
| Ends with `=` or `==`           | Often Base64  | Base64 Decode            |
| Three sections separated by `.` | JWT           | Inspect header & payload |
| Even-length hexadecimal         | Hexadecimal   | Hex Decode               |
| Long fixed-length fingerprint   | Possibly Hash | Gather context first     |

- Remember:

  - These are **heuristics**, not guarantees.

---

## Common Formats

### URL Encoding

- Example

```text
%2Fdashboard%3Ftab%3Dprofile
```

- Recognition:

  * `%`
  * Two hexadecimal digits after `%`

- First operation:

  → URL Decode

---

### Base64

- Example

```text
SGVsbG8=
```

- Recognition:

  * Often ends with `=`
  * Base64 character set
  * May be padded

- First operation:

  → Base64 Decode

---

### Hexadecimal

- Example

```text
48656c6c6f20576f726c64
```

- Recognition:

  * Characters only from:

    ```text
    0-9
    A-F
    a-f
    ```

  * Often even length

- First operation:

  → Hex Decode

---

### JWT

- Example

  ```text
  xxxxx.yyyyy.zzzzz
  ```

- Recognition:

  * Three sections
  * Period separators
  * First two sections usually decode into JSON

- First operation:

  → Decode header and payload

---

### Hash Candidate

- Example

```text
5d41402abc4b2a76b9719d911017c592
```

- Recognition:

  * Fixed-length fingerprint
  * Often hexadecimal

- First operation:

  → Do **not** decode immediately.

- Gather context first.

---

## Decoder Decision Tree

```text
Unknown Value?

        │

        ▼

Recognize Pattern

        │

        ▼

Evidence Supports Transformation?

   │            │
  Yes          No
   │            │
Transform    Gather Context
        │
        ▼
Verify Output
```

---

## One Transformation Rule

- Always ask:

  1. Does the format match my hypothesis?
  2. Does the output make sense?
  3. Does another transformation have evidence?

- Never apply random transformations.

---

## Recognition Checklist

- Before using Decoder:

  * [ ] I examined the surrounding context.
  * [ ] I identified likely recognition clues.
  * [ ] I formed a hypothesis.
  * [ ] I selected one justified transformation.
  * [ ] I verified the result.

---

## Common Mistakes

- ❌ Assuming everything unreadable is encrypted.

- ❌ Confusing Base64 with encryption.

- ❌ Trying to decode hashes.

- ❌ Ignoring context.

- ❌ Applying multiple transformations without evidence.

- ❌ Assuming readable output is automatically correct.

---

## Decoder vs Other Burp Tools

| Tool     | Primary Purpose             |
| -------- | --------------------------- |
| Proxy    | Capture traffic             |
| Repeater | Manual investigation        |
| Intruder | Automated experiments       |
| Decoder  | Understand transformed data |

---

## Professional Mindset

- Ask yourself:

  > **"What evidence suggests this transformation?"**

- Not:

  > **"Which button should I click?"**

- Evidence should guide every decision.

---

## Recognition Mini Quiz

- Without using Decoder:

  ```text
  %2Flogin

  U2VjdXJpdHk=

  48656c6c6f

  eyJhbGciOiJIUzI1NiJ9.payload.signature

  098f6bcd4621d373cade4e832627b4f6
  ```

- Can you identify:

  * The likely format?
  * The first transformation?
  * Whether transformation is appropriate?

- If yes, you're thinking like an investigator.

---

## Decoder Golden Rules

1. Observe before acting.
2. Recognition before transformation.
3. One justified transformation at a time.
4. Verify every output.
5. Let evidence—not assumptions—drive the investigation.
