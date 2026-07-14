# Repeater Interface

## Overview

- The Repeater interface is your manual testing workspace. Every section is designed to help you inspect, modify, send, and analyze HTTP requests efficiently.

- This lesson explains not only **what each part of the interface does**, but also **how experienced penetration testers use it during real assessments**.

---

## Learning Objectives

- By the end of this lesson, you will be able to:

  * Navigate the Repeater interface confidently.
  * Identify the purpose of each major panel.
  * Understand which interface elements matter most during testing.
  * Organize multiple requests efficiently.
  * Read responses like a penetration tester.

---

## Prerequisites

- Before starting this lesson, you should understand:

  * HTTP Requests
  * HTTP Responses
  * Proxy Tool
  * Target Tool
  * Repeater Introduction

---

## Difficulty

🟢 Beginner

---

## Estimated Time

| Activity           |         Time |
| ------------------ | -----------: |
| Reading            |       15 min |
| Hands-on Practice  |       20 min |
| Exercises          |       15 min |
| Capstone Challenge |       20 min |
| **Total**          | **≈ 70 min** |

---

## Why Learn the Interface?

- A beginner sees buttons.

- A professional sees a workflow.

- Understanding where information appears and how to move through the interface quickly saves significant time during an assessment.

---

## Mental Model

- Think of Repeater as a laboratory.

  ```text
                Repeater

   Request  ─────► Experiment ─────► Response
                     │
                     ▼
               Modify & Repeat
  ```

- Every experiment starts with a request.

- Every conclusion comes from the response.

---

## Behind the Scenes

- When you send a request from Proxy to Repeater:

  1. Burp creates a copy of the request.
  2. The copy becomes editable.
  3. The original request remains unchanged.
  4. Every time you click **Send**, Burp establishes a connection to the target server and transmits the current version of the request.
  5. The server processes the request and returns a fresh response.

- Because Repeater works with copies, you can safely experiment without affecting your browsing workflow.

---

## Interface Walkthrough

- A typical Repeater window contains:

  ```text
  +-------------------------------------------------------------+
  | Request Tab | Response Tab | Inspector | Settings           |
  +-------------------------------------------------------------+

  Request Editor
  ---------------------------------------------------------------
  GET /profile?id=15 HTTP/1.1
  Host: example.com
  Cookie: session=abc123

  ---------------------------------------------------------------

  [ Send ]

  ---------------------------------------------------------------

  Response Viewer
  ---------------------------------------------------------------
  HTTP/1.1 200 OK
  Content-Type: text/html

  <html>...</html>

  ---------------------------------------------------------------
  ```

---

## Request Editor

- The Request Editor is where you modify outgoing requests.

- Typical changes include:

  * Parameters
  * Headers
  * Cookies
  * JSON bodies
  * XML bodies
  * HTTP methods
  * API endpoints

- This is the area you'll spend the most time using.

> [!TIP]
> Change only **one value at a time** whenever possible. It makes response analysis much easier.

---

## Send Button

- The **Send** button transmits the current request exactly as it appears in the editor.

- Every click represents a new request.

- If you modify the request, Burp sends the modified version—not the original.

---

## Response Viewer

- The Response Viewer displays the server's reply.

- Key things to examine:

  * Status Code
  * Headers
  * Response Body
  * Cookies
  * Redirects
  * Error Messages

- Professionals rarely look only at the page rendered in a browser. They inspect the raw response first.

---

## Inspector Panel

- The Inspector provides a structured view of the request and response.

- Instead of manually searching through raw HTTP messages, it organizes information into logical sections such as:

  * URL
  * Parameters
  * Headers
  * Cookies
  * Body
  * Authentication

- This helps you locate important data more quickly.

> [!NOTE]
> The exact layout of the Inspector may vary slightly between Burp Suite versions.

---

## Request Tabs

- You may work with multiple requests simultaneously.

- Examples:

  ```text
  Login

  Profile

  Orders

  Admin

  API

  JWT
  ```

- Each request remains available until you close it, making it easy to switch between different investigations.

---

## What Professionals Look At First

- When a response arrives, experienced testers usually inspect it in roughly this order:

  1. Status Code
  2. Response Length
  3. Important Headers
  4. Response Body
  5. Error Messages
  6. Reflection of Input
  7. Timing (when relevant)

- This habit helps identify unusual behavior quickly.

---

## Professional Workflow

```text
HTTP History
      │
      ▼
Send to Repeater
      │
      ▼
Read Original Request
      │
      ▼
Modify One Value
      │
      ▼
Send
      │
      ▼
Compare Response
      │
      ▼
Repeat Until Behavior Is Understood
```

- Notice that the goal is understanding the application's behavior—not simply generating requests.

---

## Attack Scenario

- You intercept:

  ```http  
  GET /api/user/100 HTTP/1.1
  Host: example.com
  Cookie: session=abc123
  ```

- Inside Repeater:

  * Change `100` to `101`.
  * Send the request.
  * Observe the status code.
  * Compare the response body.
  * Note any differences in headers, response length, or returned data.

- These observations help determine whether further investigation is warranted.

---

## Field Notes

- During a real assessment, you may have dozens of Repeater tabs open.

- Use clear names for important requests and close tabs that are no longer useful.

- A well-organized workspace makes investigations much easier.

---

## Common Mistakes

* Ignoring response headers.
* Reading only the response body.
* Keeping dozens of unnamed tabs open.
* Sending modified requests without recording observations.
* Changing multiple values at once.

---

## Practice Exercises

1. Send a request from Proxy to Repeater.
2. Identify each section of the interface.
3. Locate the Request Editor.
4. Locate the Response Viewer.
5. Send the request.
6. Modify one parameter.
7. Send the request again.
8. Compare both responses.

---

## Capstone Challenge

- Choose a request containing:

  * URL parameters
  * Cookies
  * Multiple headers

- Perform the following:

  1. Record the original response.
  2. Modify one header.
  3. Send the request.
  4. Modify one parameter.
  5. Send again.
  6. Compare all responses.
  7. Write down every observed difference.

---

## Investigation Journal

| Step           | Your Notes |
| -------------- | ---------- |
| Observation    |            |
| Hypothesis     |            |
| Test Performed |            |
| Result         |            |
| Conclusion     |            |

---

## Interview Questions

1. What is the purpose of the Request Editor?
2. Why does Repeater work on copies of requests?
3. What information should you inspect first in a response?
4. What is the Inspector used for?
5. Why should only one value be modified at a time?
6. Why is Repeater preferred over repeatedly using a browser?

---

## Quick Revision

* Repeater is your manual testing workspace.
* The Request Editor modifies outgoing traffic.
* The Response Viewer displays server replies.
* The Inspector organizes request and response data.
* Read responses methodically.
* One change at a time leads to better analysis.

---

## Mastery Checklist

* [ ] I can identify every major area of the Repeater interface.
* [ ] I know where to edit requests.
* [ ] I know where to inspect responses.
* [ ] I understand the role of the Inspector.
* [ ] I can use multiple request tabs effectively.
* [ ] I completed the practice exercises.
* [ ] I completed the capstone challenge.
