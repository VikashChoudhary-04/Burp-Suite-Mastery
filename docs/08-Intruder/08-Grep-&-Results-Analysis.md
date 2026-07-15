# Grep & Results Analysis

## Overview

- Running an Intruder attack is only the beginning.

- The real value of Intruder comes from analyzing the responses and identifying meaningful patterns.

- Professional penetration testers spend far more time reviewing results than launching attacks.

- This lesson teaches you how to separate useful signals from thousands of responses.

---

## Learning Objectives

- By the end of this lesson, you will be able to:

  * Analyze Intruder results efficiently.
  * Recognize meaningful response patterns.
  * Understand common result metrics.
  * Use Grep features effectively.
  * Decide which responses deserve manual investigation.

---

## Prerequisites

* Payload Types
* Payload Processing
* Response Analysis (Repeater Module)

---

## Difficulty

🔴 Advanced

---

## Estimated Time

| Activity          |          Time |
| ----------------- | ------------: |
| Reading           |        35 min |
| Hands-on Practice |        50 min |
| Exercises         |        30 min |
| **Total**         | **≈ 115 min** |

---

## Why Results Analysis Matters

- Imagine an Intruder attack sends:

  ```text id="n9eq7v"
  10,000 Requests
  ```

- Do you read all 10,000 responses?

  - No.

- You look for the responses that differ from the rest.

- Intruder helps automate requests.

- Your job is to identify the responses that deserve attention.

---

## Mental Model

- Think like a data analyst.

```text id="ylujqf"
Run Attack

↓

Collect Results

↓

Sort

↓

Filter

↓

Identify Outliers

↓

Manual Verification

↓

Conclusion
```

- Analysis is a process, not a single step.

---

## What Should You Look For?

- Common indicators include:

  * Status code
  * Response length
  * Response time
  * Redirects
  * Error messages
  * Reflected values
  * Custom Grep matches

- Each metric tells part of the story.

---

## Status Codes

- Suppose you observe:

| Payload | Status |
| ------: | :----: |
|     101 |   200  |
|     102 |   200  |
|     103 |   403  |
|     104 |   200  |
|     105 |   500  |

- Questions:

  * Why did `103` return `403`?
  * Why did `105` return `500`?
  * Are the responses reproducible?

- Outliers deserve investigation—not assumptions.

---

## Response Length

- Imagine:

| Payload | Length |
| ------: | -----: |
|     100 |   4210 |
|     101 |   4212 |
|     102 |   4211 |
|     103 |    879 |
|     104 |   4213 |

- The shorter response may indicate:

  * Error page
  * Redirect
  * Different application logic

- Length is an indicator, not proof.

---

## Response Time

- Sometimes response time varies.

- Possible explanations include:

  * Backend processing differences
  * Validation logic
  * Resource availability
  * Network conditions

- Always verify timing observations before drawing conclusions.

---

## Grep Features

- Grep allows Intruder to search responses for specific patterns.

- Examples include:

  * Success messages
  * Error strings
  * Usernames
  * JSON fields
  * Application-specific text

- Rather than reading every response, you can quickly identify those containing information relevant to your investigation.

---

## Decision Point

```text id="65kn9z"
Interesting Response?

        │

      No ─────────► Continue Reviewing

        │

       Yes

        │

Reproduce?

        │

      No ─────────► Investigate Further

        │

       Yes

        │

Send to Repeater

        │

        ▼

Manual Verification
```

- Automation finds candidates.

- Manual testing confirms findings.

---

## Sorting Results

- Professional reviewers often sort by:

  1. Status code
  2. Response length
  3. Response time
  4. Payload value

- Sorting helps unusual responses stand out immediately.

---

## Filtering Results

- Useful filtering strategies include:

  * Show only errors.
  * Show only redirects.
  * Show responses containing specific text.
  * Show responses above or below a chosen length.

- Filtering reduces noise and speeds up analysis.

---

## Professional Workflow

```text id="sjb62k"
Run Attack

↓

Sort Results

↓

Filter Responses

↓

Identify Outliers

↓

Compare with Baseline

↓

Send Interesting Requests to Repeater

↓

Collect Evidence

↓

Document Findings
```

- The workflow should always end with evidence—not assumptions.

---

## Performance & Safety

> [!IMPORTANT]
> If an attack produces more results than you can reasonably review, reconsider your payload design.

- Smaller, focused attacks often produce clearer insights than very large ones.

- Quality of analysis matters more than quantity of requests.

---

## Real Example

- Objective:

  - Understand how different invoice IDs affect responses.

- Attack:

  - Payloads:

    ```text id="ykpjr2"
    200–250
    ```

  - Results:

    - Most responses:

      * 200 OK
      * Length ≈ 4100 bytes

    - One response:

      * 404 Not Found
      * Length 720 bytes

- Next step:

  - Return that request to Repeater and determine why it differs.

---

## Field Notes

- Professionals rarely inspect every response.

- Instead, they search for:

  * Inconsistencies
  * Outliers
  * Unexpected behavior
  * Reproducible differences

- These responses deserve manual investigation.

---

## Common Mistakes

* Reading responses in order instead of sorting them.
* Ignoring response length.
* Treating every error as a vulnerability.
* Forgetting to compare against the baseline.
* Reporting findings without manual verification.

---

## Practice Exercises

- Choose an Intruder attack.

- After it completes:

  1. Sort by status code.
  2. Sort by response length.
  3. Identify three unusual responses.
  4. Explain why they stand out.
  5. Verify each one manually in Repeater.

---

## Interview Questions

1. Why is results analysis more important than generating requests?
2. What metrics should you review first?
3. Why is response length useful?
4. What is the purpose of Grep?
5. Why should interesting responses be verified manually?

---

## Quick Revision

* Intruder generates data.
* Analysis creates understanding.
* Sort before reading.
* Filter before concluding.
* Outliers deserve investigation.
* Verify findings manually.

---

## Mastery Checklist

* [ ] I understand how to analyze Intruder results.
* [ ] I know how to identify outliers.
* [ ] I understand the purpose of Grep.
* [ ] I verify interesting responses manually.
* [ ] I completed the practice exercise.
