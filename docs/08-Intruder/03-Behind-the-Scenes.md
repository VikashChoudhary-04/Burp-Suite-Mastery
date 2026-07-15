# Behind the Scenes

## Overview

- Every Intruder attack follows a sequence of internal operations before the first response appears in the results table.

- Understanding this process helps you:

  * Design efficient attacks.
  * Predict how payloads will be combined.
  * Interpret unexpected results.
  * Troubleshoot configuration mistakes.
  * Understand why some attacks are slower than others.

- Intruder is not simply "sending lots of requests." It is executing a carefully defined experiment.

---

## Learning Objectives

- By the end of this lesson, you will be able to:

  * Explain what happens after starting an Intruder attack.
  * Understand the request lifecycle.
  * Recognize how payloads are applied.
  * Understand how responses are collected.
  * Explain why analysis is as important as execution.

---

## Prerequisites

* HTTP Fundamentals
* Proxy
* Target
* Repeater
* Intruder Introduction
* Intruder Interface

---

## Difficulty

🟡 Intermediate

---

## Estimated Time

| Activity   |         Time |
| ---------- | -----------: |
| Reading    |       20 min |
| Practice   |       30 min |
| Reflection |       20 min |
| **Total**  | **≈ 70 min** |

---

## Why This Exists

- Many users believe Intruder simply repeats a request.

- In reality, every request is:

  * Generated
  * Modified
  * Sent
  * Processed
  * Recorded
  * Compared

- Understanding each stage helps you build better experiments.

---

## Mental Model

- Think of Intruder as an automated laboratory.

```text
Baseline Request
        │
        ▼
Identify Payload Positions
        │
        ▼
Generate Payload Variations
        │
        ▼
Create Individual Requests
        │
        ▼
Send Requests
        │
        ▼
Receive Responses
        │
        ▼
Record Results
        │
        ▼
Identify Patterns
        │
        ▼
Manual Verification
```

---

## Complete Lifecycle

- Every Intruder attack follows this workflow.

```text
Original Request
      │
      ▼
Read Payload Positions
      │
      ▼
Insert Payload Values
      │
      ▼
Build New Request
      │
      ▼
Transmit Request
      │
      ▼
Server Processes Request
      │
      ▼
Receive Response
      │
      ▼
Store Response Metadata
      │
      ▼
Display Results
```

- Every row in the results table represents one completed experiment.

---

### Step 1 — Original Request

- Intruder starts with a single HTTP request.

- This request becomes the template for every generated request.

- Only the selected payload positions change.

- Everything else remains unchanged unless you intentionally configure it otherwise.

---

### Step 2 — Payload Generation

- Intruder selects payload values according to the configured attack type.

- Examples include:

  * Sequential numbers
  * Custom lists
  * Usernames
  * Email addresses
  * IDs

- The payload generator determines what values will be inserted.

---

### Step 3 — Request Generation

- For every payload value, Intruder creates a new HTTP request.

- Example:

  - Original:

    ```http
    GET /profile?id=15 HTTP/1.1
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

- Each request is treated as an independent experiment.

---

### Step 4 — Transmission

- Intruder sends each generated request to the server.

- Depending on your configuration, requests may be:

  * Sequential
  * Concurrent
  * Throttled

- Request scheduling influences both performance and server load.

---

## Performance & Safety

> [!IMPORTANT]
> Automation should always respect the rules of the authorized engagement.

- Before starting an attack:

  * Minimize unnecessary requests.
  * Avoid oversized payload lists.
  * Consider request rate limits.
  * Stop attacks that behave unexpectedly.

- Efficient testing is a professional skill.

---

### Step 5 — Server Processing

- Each generated request is processed independently by the application.

- The server evaluates:

  * Method
  * URL
  * Headers
  * Cookies
  * Parameters
  * Request body
  * Authentication
  * Authorization

- The resulting behavior may differ between payloads.

---

### Step 6 — Response Collection

- Intruder records information about every response.

- Common metadata includes:

  * Status code
  * Response length
  * Response time
  * Payload value
  * Error indicators

- The goal is not to read every response individually.

- The goal is to identify meaningful patterns.

---

### Step 7 — Pattern Recognition

- Imagine the following results:

| Payload | Status | Length |
| ------: | :----: | -----: |
|     101 |   200  |   4210 |
|     102 |   200  |   4208 |
|     103 |   404  |    612 |
|     104 |   200  |   4212 |
|     105 |   500  |   1987 |

- The outliers (`404` and `500`) deserve closer investigation.

- Return those requests to Repeater before drawing conclusions.

---

## Decision Point

```text
Interesting Result?
        │
      No ─────────► Continue Reviewing
        │
       Yes
        │
Return Request to Repeater
        │
        ▼
Manual Investigation
```

- Automation identifies candidates.

- Manual investigation confirms findings.

---

## Professional Workflow

```text
Question
    │
    ▼
Baseline
    │
    ▼
Configure Intruder
    │
    ▼
Generate Requests
    │
    ▼
Collect Responses
    │
    ▼
Identify Outliers
    │
    ▼
Manual Verification
    │
    ▼
Evidence-Based Conclusion
```

---

## Real Example

- Objective:

  - Understand how an endpoint behaves when the `id` parameter changes.

- Workflow:

  1. Capture the request.
  2. Verify the baseline in Repeater.
  3. Configure a small payload list.
  4. Launch Intruder.
  5. Sort results by status code.
  6. Inspect responses with unusual lengths.
  7. Send interesting requests back to Repeater.
  8. Investigate manually.

---

## Field Notes

- Professional testers rarely inspect every response.

- Instead, they search for:

  * Outliers
  * Inconsistencies
  * Unexpected behavior
  * Reproducible differences

- Those responses deserve attention.

---

## Common Mistakes

* Starting with an oversized payload list.
* Ignoring how payloads are generated.
* Looking only at HTTP 200 responses.
* Treating every outlier as a vulnerability.
* Failing to verify results manually.

---

## Practice Exercises

1. Select a request.
2. Identify one payload position.
3. Predict how many requests Intruder will generate.
4. Explain what information you expect in the results table.
5. Describe which responses you would investigate first.

---

## Interview Questions

1. What happens after you start an Intruder attack?
2. How are payloads applied to requests?
3. Why is every generated request considered an independent experiment?
4. Why should interesting responses be verified manually?
5. What information does Intruder collect from each response?

---

## Quick Revision

* Intruder starts with one request.
* Payloads generate many request variations.
* Every variation is an experiment.
* Results reveal patterns.
* Outliers deserve investigation.
* Repeater confirms findings.

---

## Mastery Checklist

* [ ] I understand the lifecycle of an Intruder attack.
* [ ] I know how payloads become requests.
* [ ] I understand how results are collected.
* [ ] I know why outliers matter.
* [ ] I completed the practice exercise.
