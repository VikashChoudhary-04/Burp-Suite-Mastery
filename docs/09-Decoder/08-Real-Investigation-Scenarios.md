# Real Investigation Scenarios

## Overview

- Decoder becomes most valuable when it helps answer real investigation questions.

- This lesson presents realistic situations where transformed data appears during authorized web application security assessments.

- The goal is not to memorize transformations.

- The goal is to recognize patterns, choose appropriate actions, and verify conclusions.

---

## Learning Objectives

- By the end of this lesson, you will be able to:

  * Recognize common situations where Decoder is useful.
  * Apply the recognition workflow.
  * Choose appropriate transformations.
  * Verify transformed output before drawing conclusions.
  * Integrate Decoder naturally into investigations.

---

## Prerequisites

* Complete all previous Decoder lessons.

---

## Difficulty

🟠 Intermediate

---

## Estimated Time

| Activity   |          Time |
| ---------- | ------------: |
| Reading    |        35 min |
| Practice   |        45 min |
| Reflection |        25 min |
| **Total**  | **≈ 105 min** |

---

## Investigation Framework

- Every scenario follows the same process.

```text id="f6tv0m"
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

---

## Scenario 1 — URL Parameter

### Observation

- You intercept:

  ```text id="r9zk1a"
  redirect=%2Fdashboard%3Ftab%3Dprofile
  ```

### Recognition Clues

* Contains `%`
* Followed by hexadecimal digits
* Appears in a URL parameter

### Likely Format

- URL encoding.

### First Transformation

- URL Decode.

### Verification

- Does the decoded value make sense within the request?

---

## Scenario 2 — Base64 Value

### Observation

```text id="c8lm3u"
QWRtaW46UGFzc3dvcmQ=
```

### Recognition Clues

* Ends with `=`
* Uses common Base64 character set

### Likely Format

- Base64.

### First Transformation

- Base64 Decode.

### Verification

- Does the output become meaningful text?

---

## Scenario 3 — JWT

### Observation

```text id="x7yr4p"
eyJhbGciOiJIUzI1NiJ9.eyJyb2xlIjoiYWRtaW4ifQ.signature
```

### Recognition Clues

* Three sections separated by periods.
* First two sections resemble Base64URL.

### Likely Format

- JWT.

### First Transformation

- Inspect or decode the header and payload (without assuming the signature is readable).

### Verification

- Does the decoded JSON explain the token's contents?

---

## Scenario 4 — Hexadecimal Data

### Observation

```text id="d2wc5h"
48656c6c6f20576f726c64
```

### Recognition Clues

* Only hexadecimal characters.
* Even number of characters.

### Likely Format

- Hexadecimal.

### First Transformation

- Hex Decode.

### Verification

- Does the output become readable text?

---

## Scenario 5 — Hash Candidate

### Observation

```text id="n4qv8s"
098f6bcd4621d373cade4e832627b4f6
```

### Recognition Clues

* Fixed-length hexadecimal string.
* No obvious encoding markers.

### Likely Interpretation

- Possibly a hash.

### Action

- Do **not** attempt to decode it simply because it is unreadable.

- Gather additional context before deciding what it represents.

---

## Scenario 6 — Multiple Layers

### Observation

```text id="k7dw1n"
SGVsbG8lMjBXb3JsZA==
```

### Recognition Clues

* Ends with `==`
* Output after Base64 decoding may still contain encoded characters.

### Possible Workflow

```text id="b9mx4j"
Base64 Decode

↓

Review Output

↓

If Evidence Supports It

↓

URL Decode

↓

Review Again
```

- Only perform the second transformation if the first output provides evidence that another layer exists.

---

## Recognition Clues Summary

| Observation                     | Possible Interpretation |
| ------------------------------- | ----------------------- |
| `%2F`, `%20`, `%3D`             | URL encoding            |
| Ends with `=`                   | Often Base64            |
| Three sections separated by `.` | JWT                     |
| Even-length hexadecimal         | Hexadecimal             |
| Long fixed-length hexadecimal   | Possibly a hash         |

These are investigation clues—not guarantees.

---

## Decision Tree

```text id="y4pl8e"
Unknown Value?

        │

        ▼

Observe Context

        │

        ▼

Recognize Pattern

        │

        ▼

Choose One Transformation

        │

        ▼

Meaningful Output?

   │            │
  Yes          No
   │            │
Continue    Reassess
```

---

## Professional Workflow

```text id="e8hr2v"
Capture Traffic

↓

Notice Unfamiliar Data

↓

Recognize Format

↓

Use Decoder

↓

Verify Result

↓

Return to Investigation
```

---

## Recognition Challenge

- For each value:

  1. Identify the likely format.
  2. Explain your reasoning.
  3. Select the first Decoder operation.
  4. Decide whether another transformation may be required.

```text id="j5pn7c"
%2Fapi%2Fv2%2Fusers%3Fpage%3D2

U2VjdXJpdHk=

48656c6c6f20576f726c64

eyJhbGciOiJIUzI1NiJ9.eyJ1c2VyIjoidmlrYXNoIn0.signature

e99a18c428cb38d5f260853678922e03
```

- Write your predictions before using Decoder.

---

## Field Notes

- Experienced testers spend more time thinking than transforming.

- Good recognition leads to fewer mistakes and faster investigations.

---

## Common Mistakes

* Assuming every value needs decoding.
* Ignoring the surrounding context.
* Applying multiple transformations without evidence.
* Treating heuristics as certainty.

---

## Interview Questions

1. How would you recognize URL-encoded data?
2. What clues suggest Base64?
3. How would you recognize a JWT?
4. Why should context be considered before transforming data?
5. Why shouldn't every unreadable value be decoded?

---

## Quick Revision

* Observe first.
* Recognize patterns.
* Form a hypothesis.
* Apply one transformation.
* Verify the output.
* Continue the investigation.

---

## Mastery Checklist

* [ ] I can recognize common data formats.
* [ ] I understand realistic Decoder workflows.
* [ ] I verify transformed output before drawing conclusions.
* [ ] I completed the Recognition Challenge.
