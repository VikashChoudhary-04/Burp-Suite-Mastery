# Request Editing

## Overview

- Request editing is the process of modifying an HTTP request before sending it to the server.

- This is the core capability of Burp Repeater and one of the most valuable skills in manual web application security testing.

- The objective is not to send random requests, but to make deliberate, controlled changes that help you understand how an application behaves.

---

## Learning Objectives

- By the end of this lesson, you will be able to:

  * Identify every editable part of an HTTP request.
  * Understand why each component matters.
  * Modify requests methodically.
  * Predict how changes might affect server behavior.
  * Build repeatable testing workflows.

---

## Prerequisites

* HTTP for Burp Users
* Proxy
* Target
* Repeater Introduction
* Repeater Interface
* Behind the Scenes

---

## Difficulty

🟡 Intermediate

---

## Estimated Time

| Activity  | Time          |
| --------- | ------------- |
| Reading   | 20 min        |
| Practice  | 35 min        |
| Exercises | 20 min        |
| Challenge | 25 min        |
| **Total** | **≈ 100 min** |

---

## Why This Exists

- Every vulnerability investigation starts by changing something.

- You are asking questions such as:

  * What happens if this value changes?
  * Does the server validate it?
  * Is this field trusted?
  * Does authorization still work?
  * Does the application behave differently?

- Request editing lets you answer these questions.

---

## Mental Model

- Treat every request as an experiment.

  ```text
  Original Request
          │
          ▼
  Select One Component
          │
          ▼
  Modify It
          │
          ▼
  Send Request
          │
          ▼
  Compare Response
          │
          ▼
  Document Findings  
  ```

- The quality of your investigation depends on how carefully you perform each experiment.

---

## What Can Be Edited?

- Almost every part of an HTTP request.

- Common examples include:

  * HTTP Method
  * URL
  * Path
  * Query Parameters
  * Headers
  * Cookies
  * Body Parameters
  * JSON Fields
  * XML Data
  * Multipart Form Data

- Understanding what each part controls is essential for effective testing.

---

## HTTP Method

- Examples:

  ```http
  GET
  POST
  PUT
  PATCH
  DELETE
  OPTIONS
  HEAD
  ```

- Changing the method may reveal:

  * Hidden functionality
  * Incorrect method restrictions
  * Misconfigured APIs

- Always note the original method before experimenting.

---

## URL Path

- Example:

  ```text
  /profile
  ```

  ↓

  ```text
  /admin
  ```

- Changing the path helps explore different application functionality.

- Do not assume similar paths behave the same way.

---

## Query Parameters

- Example:

  ```text
  ?id=15
  ```

  ↓

  ```text
  ?id=16  
  ```

- Questions to ask:

  * Does the response change?
  * Is another user's data returned?
  * Is access denied?
  * Is validation performed?

---

## Headers

- Common headers to inspect:

  * Authorization
  * Cookie
  * User-Agent
  * Referer
  * Origin
  * Accept
  * Content-Type

- Headers often influence authentication, content negotiation, and application logic.

---

## Cookies

- Example:

  ```http
  Cookie: session=abc123
  ```

- Possible experiments:

  * Remove the cookie.
  * Replace the cookie.
  * Modify the cookie value.
  * Use an expired cookie.
  * Replay an old cookie.

- Observe how the application responds.

---

## Request Body

- Typical formats include:

### Form Data

```text
username=alice
password=password123
```

### JSON

```json
{
  "username":"alice",
  "role":"user"
}
```

### XML

```xml
<user>
    <id>15</id>
</user>
```

- Different applications process each format differently.

---

## Professional Workflow

- A disciplined workflow is essential.

```text
Capture Request
        │
        ▼
Record Baseline
        │
        ▼
Choose One Component
        │
        ▼
Modify It
        │
        ▼
Send
        │
        ▼
Compare
        │
        ▼
Document
        │
        ▼
Repeat
```

- Avoid changing multiple components simultaneously unless your objective specifically requires it.

---

## Attack Scenario

- Suppose you capture:

  ```http
  POST /api/profile HTTP/1.1
  Host: example.com
  Cookie: session=abc123

  {
      "userId":15  
  }
  ```

- Possible experiments:

  1. Change `userId`.
  2. Remove the session cookie.
  3. Replace the session cookie.
  4. Change the HTTP method.
  5. Modify the endpoint.

- Each experiment should have a clear hypothesis before you click **Send**.

---

## Field Notes

- Professional testers rarely ask:

  > "How can I hack this?"

- Instead, they ask:

  * Why does this parameter exist?
  * Who controls this value?
  * Can the client modify it?
  * Does the server validate it?
  * What assumptions is the application making?

- Good questions lead to good discoveries.

---

## Common Mistakes

* Editing multiple values at once.
* Forgetting the original request.
* Ignoring response headers.
* Failing to record observations.
* Guessing instead of testing.

---

## Practice Exercises

- Using a practice application:

  1. Modify one query parameter.
  2. Modify one header.
  3. Modify one cookie.
  4. Modify one body parameter.
  5. Record every difference.

- Do not proceed until you understand why each response changed.

---

## Capstone Challenge

- Choose one authenticated request.

- Perform five independent experiments.

- For each experiment, record:

  * Original value
  * Modified value
  * Reason for modification
  * Expected outcome
  * Actual outcome
  * Conclusion

- Only one component may change during each experiment.

---

## Investigation Journal

| Step         | Your Notes |
| ------------ | ---------- |
| Observation  |            |
| Hypothesis   |            |
| Modification |            |
| Result       |            |
| Conclusion   |            |

---

## Interview Questions

1. What parts of an HTTP request can be modified in Repeater?
2. Why should you modify only one component at a time?
3. What is a baseline request?
4. Why are headers important?
5. How can modifying cookies affect server behavior?
6. Why should every modification have a hypothesis?

---

## Quick Revision

* Every request component can influence server behavior.
* Change one component at a time.
* Always establish a baseline.
* Observe before concluding.
* Document every experiment.

---

## Mastery Checklist

* [ ] I can identify every editable request component.
* [ ] I understand why each component matters.
* [ ] I can perform controlled experiments.
* [ ] I document observations systematically.
* [ ] I completed the exercises.
* [ ] I completed the capstone challenge.
