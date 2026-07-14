# Response Analysis

## Overview

- Sending a request is only half of the investigation.

- The real work begins when the server responds.

- Professional penetration testers spend far more time analyzing responses than sending requests.

- This lesson teaches you how to examine responses systematically, identify meaningful changes, and avoid incorrect conclusions.

---

## Learning Objectives

- By the end of this lesson, you will be able to:

  * Analyze HTTP responses methodically.
  * Identify meaningful differences between responses.
  * Recognize expected and unexpected application behavior.
  * Distinguish functional changes from security-relevant changes.
  * Build evidence before concluding that a vulnerability exists.

---

## Prerequisites

* HTTP for Burp Users
* Proxy
* Target
* Repeater Introduction
* Repeater Interface
* Behind the Scenes
* Request Editing

---

## Difficulty

🟠 Intermediate

---

## Estimated Time

| Activity  |          Time |
| --------- | ------------: |
| Reading   |        25 min |
| Practice  |        40 min |
| Exercises |        20 min |
| Challenge |        30 min |
| **Total** | **≈ 115 min** |

---

## Why This Exists

- A changed response does **not** automatically mean a vulnerability exists.

- Likewise, an unchanged page does **not** mean your modification had no effect.

- The goal is to understand what the application is telling you.

---

## Mental Model

- Treat every response as evidence.

```text
Original Request
        │
        ▼
Baseline Response
        │
        ▼
Modify One Value
        │
        ▼
New Response
        │
        ▼
Compare Evidence
        │
        ▼
Form Conclusion
```

- Never jump directly from "difference" to "vulnerability."

---

## Behind the Scenes

- When the server receives your request, it performs validation, business logic, authentication, authorization, and data processing before generating a response.

- Even small request changes can alter:

  * Status codes
  * Headers
  * Cookies
  * Response body
  * Response length
  * Processing time

- Your job is to determine **why** those changes occurred.

---

## What Should You Analyze?

- For every response, inspect:

  * Status code
  * Response headers
  * Cookies
  * Response body
  * Response length
  * Redirect behavior
  * Error messages
  * Reflected input
  * Timing (when relevant)

- Do not focus on only one part of the response.

---

## Status Code

- The status code is often the first clue.

- Examples:

| Status | Meaning                 |
| ------ | ----------------------- |
| 200    | Request succeeded       |
| 201    | Resource created        |
| 204    | Success with no content |
| 301    | Permanent redirect      |
| 302    | Temporary redirect      |
| 400    | Bad request             |
| 401    | Authentication required |
| 403    | Access forbidden        |
| 404    | Resource not found      |
| 405    | Method not allowed      |
| 500    | Internal server error   |

- A status code alone rarely proves a vulnerability, but it helps explain application behavior.

---

## Response Headers

- Headers can reveal valuable information.

- Examples include:

  * Content-Type
  * Set-Cookie
  * Location
  * Cache-Control
  * Content-Length

- Questions to ask:

  * Did a new cookie appear?
  * Was the session changed?
  * Did the server redirect?
  * Did the content type change?

---

## Response Body

- Read the body carefully.

- Look for:

  * Returned data
  * Validation messages
  * User information
  * Stack traces
  * Debug messages
  * JSON fields
  * Reflected input

- Many findings are hidden in the response body rather than the status code.

---

## Response Length

- A change in response size may indicate:

  * Additional data
  * Missing data
  * Different application logic
  * Error pages
  * Authorization failures

- Length alone is not proof, but it is a useful indicator that deserves further investigation.

---

## Redirect Behavior

- Observe whether the application redirects.

- Examples:

  * Login page
  * Dashboard
  * Error page
  * Different endpoint

- Unexpected redirects often reveal authentication or session-related behavior.

---

## Error Messages

- Error messages can provide useful clues.

- For example:

  * Validation failures
  * Missing parameters
  * Unsupported methods
  * Parsing errors

- Do not assume an error message indicates a vulnerability.

- Instead, ask why the application generated that response.

---

## Comparing Responses

- Always compare against your baseline.

- Consider:

  * Did the status code change?
  * Did the headers change?
  * Did the body change?
  * Did the response length change?
  * Did the server behave consistently?

- Record every observation.

---

## Professional Workflow

```text
Capture Baseline
        │
        ▼
Modify One Input
        │
        ▼
Send Request
        │
        ▼
Compare Responses
        │
        ▼
Identify Differences
        │
        ▼
Explain Differences
        │
        ▼
Design Next Test
```

- Professional testing is iterative.

- Each response informs the next experiment.

---

## Attack Scenario

- You capture:

  ```http
  GET /api/orders/150 HTTP/1.1
  Host: example.com
  Cookie: session=abc123
  ```

- Baseline:

  * Status: 200
  * Length: 3150 bytes

- Now change:

  ```text
  150
  ```

- to

  ```text
  151
  ```

- New response:

  * Status: 200
  * Length: 3285 bytes

- Questions:

  * Is this another user's order?
  * Is the difference expected?
  * Does the application enforce authorization?
  * What additional evidence is needed before reporting a finding?

- Do not conclude anything until you verify the behavior.

---

## Field Notes

- Experienced testers avoid phrases such as:

  > "It looks vulnerable."

- Instead, they ask:

  * Can I reproduce this?
  * Is it consistent?
  * Does it violate the application's intended security model?
  * What evidence supports my conclusion?

- Evidence comes before conclusions.

---

## Common Mistakes

* Looking only at the status code.
* Ignoring response headers.
* Assuming different lengths mean a vulnerability.
* Forgetting the baseline response.
* Reporting observations without verification.

---

## Practice Exercises

- Using a practice application:

  1. Capture a baseline request.
  2. Record the complete response.
  3. Change one parameter.
  4. Compare:

     * Status code
     * Headers
     * Cookies
     * Body
     * Length
  5. Explain every difference you observe.

---

## Capstone Challenge

- Choose an authenticated request.

- Perform five controlled modifications.

- For each one, document:

  * Baseline response
  * Modified response
  * Differences
  * Possible explanations
  * Additional tests required
  * Final conclusion

- Your conclusion must be supported by evidence.

---

## Investigation Journal

| Step               | Your Notes |
| ------------------ | ---------- |
| Observation        |            |
| Hypothesis         |            |
| Test Performed     |            |
| Evidence Collected |            |
| Conclusion         |            |

---

## Interview Questions

1. Why is a baseline response important?
2. What should you inspect first in an HTTP response?
3. Why should response headers be analyzed?
4. Does a different response length prove a vulnerability?
5. Why is evidence important before reporting a finding?
6. What additional tests might be required before confirming a vulnerability?

---

## Quick Revision

* Responses are evidence.
* Compare against a baseline.
* Inspect the entire response.
* Explain every observed difference.
* Verify before concluding.
* Evidence supports findings.

---

## Mastery Checklist

* [ ] I know how to analyze HTTP responses.
* [ ] I compare responses methodically.
* [ ] I understand the importance of a baseline.
* [ ] I avoid unsupported conclusions.
* [ ] I completed the exercises.
* [ ] I completed the capstone challenge.
