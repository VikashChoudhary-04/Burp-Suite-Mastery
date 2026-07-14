# Behind the Scenes

## Overview

- Every time you click **Send** in Burp Repeater, dozens of things happen before a response appears on your screen.

- Understanding this process helps you troubleshoot problems, interpret unexpected responses, and become a better penetration tester.

- This lesson follows a request from the moment you press **Send** until the server's response is displayed.

---

## Learning Objectives

- By the end of this lesson, you will be able to:

  * Explain what happens after clicking **Send**.
  * Understand the lifecycle of an HTTP request.
  * Recognize where problems can occur.
  * Understand why responses sometimes differ.
  * Build a deeper mental model of Repeater.

---

## Prerequisites

- You should already understand:

  * HTTP Requests
  * HTTP Responses
  * Proxy
  * Target
  * Repeater Introduction
  * Repeater Interface

---

## Difficulty

🟡 Intermediate

---

## Estimated Time

| Activity          |         Time |
| ----------------- | -----------: |
| Reading           |       20 min |
| Hands-on Practice |       25 min |
| Exercises         |       20 min |
| Challenge         |       20 min |
| **Total**         | **≈ 85 min** |

---

## Why This Exists

- Many people use Repeater without understanding how requests travel across the network.

- Knowing the request lifecycle helps you answer questions like:

  * Why did the server return a timeout?
  * Why did authentication fail?
  * Why did the response suddenly change?
  * Why does the request succeed in one environment but fail in another?

- Professionals investigate these questions by understanding the underlying process.

---

## Mental Model

- Think of Repeater as sending a letter.

  ```text  
  You
   │
   ▼
  Write Letter
   │
   ▼
  Mail Service
   │
   ▼  
  Destination
   │
   ▼
  Reply
   │
   ▼
  You Read Reply  
  ```

- Repeater performs the same cycle digitally.

---

## Request Lifecycle

- Every request follows this journey.

```text
Edit Request
      │
      ▼
Click Send
      │
      ▼
Burp Processes Request
      │
      ▼
Network Connection
      │
      ▼
Target Server
      │
      ▼
Server Processes Request
      │
      ▼
Response Returns
      │
      ▼
Burp Displays Response
```

---

### Step 1 — You Modify the Request

- Before anything is transmitted, Burp uses the exact contents currently visible in the Request Editor.

- If you changed:

  * URL
  * Headers
  * Cookies
  * Parameters
  * JSON
  * HTTP Method

those changes become part of the request.

- Nothing is sent until you click **Send**.

---

### Step 2 — Burp Builds the Request

- Burp prepares the HTTP message exactly as shown in the editor.

- Unlike a browser, Repeater does not automatically "fix" mistakes you intentionally introduce.

- If you remove a header, change a parameter, or alter the body, Burp sends it exactly as written.

- This precision is one reason Repeater is so valuable during manual testing.

> [!IMPORTANT]
> Repeater is designed to give you control. It assumes you know what you are sending.

---

### Step 3 — The Request Leaves Burp

- Burp transmits the request to the target server.

- Depending on the protocol, the traffic may be:

  * HTTP
  * HTTPS

- If HTTPS is used, the communication is encrypted while traveling across the network.

---

### Step 4 — The Server Processes the Request

- The server receives the request and evaluates:

  * HTTP method
  * URL
  * Headers
  * Cookies
  * Parameters
  * Request body
  * Authentication
  * Authorization

- The application then decides how to respond.

- Examples:

  * Return a page
  * Return JSON
  * Redirect
  * Reject the request
  * Produce an error

---

### Step 5 — The Response Returns

- The server sends an HTTP response.

- Typical parts include:

  * Status Code
  * Headers
  * Cookies
  * Response Body

- Burp receives the response exactly as it arrives from the server.

---

### Step 6 — Burp Displays the Response

- The Response Viewer updates immediately.

- Now you can inspect:

  * Status code
  * Headers
  * Cookies
  * HTML
  * JSON
  * XML
  * Response length
  * Timing

- This is where analysis begins.

- Receiving a response is not the end of the workflow—it is the beginning of your investigation.

---

## Why Responses Change

- Changing a single value can produce dramatically different responses.

- For example:

| Change               | Possible Result        |
| -------------------- | ---------------------- |
| Cookie               | Logged out             |
| Authorization header | 401 Unauthorized       |
| User ID              | Different user's data  |
| Product ID           | Different product      |
| HTTP Method          | 405 Method Not Allowed |
| Content-Type         | Parsing error          |
| JSON field           | Validation error       |

- Understanding **why** the response changed is one of the most important skills in web security testing.

---

## Professional Workflow

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
Send
        │
        ▼
Compare
        │
        ▼
Record Observation
        │
        ▼
Repeat
```

- Notice that every test starts with a baseline.

- Without a baseline, meaningful comparison is difficult.

---

## Attack Scenario

- You intercept:

  ```http
  GET /api/profile?id=25 HTTP/1.1
  Host: example.com
  Cookie: session=abc123
  ```

- You create three tests:

  * Change only the `id`.
  * Change only the session cookie.
  * Remove the cookie entirely.

- Questions to answer:

  * Does the status code change?
  * Does the response body change?
  * Does authentication fail?
  * Is another user's data returned?
  * Does the server behave consistently?

- Your goal is to understand the application's behavior before drawing conclusions.

---

## Field Notes

- Professional testers avoid guessing.

- Every modification should have a purpose.

- Ask yourself before clicking **Send**:

  * What am I testing?
  * What do I expect to happen?
  * What evidence would support or refute my hypothesis?

---

## Common Mistakes

* Sending requests without a hypothesis.
* Ignoring the baseline response.
* Changing multiple values at once.
* Assuming an error message indicates a vulnerability.
* Failing to document observations.

---

## Practice Exercises

1. Capture a request.
2. Send it to Repeater.
3. Record the original response.
4. Modify one header.
5. Send again.
6. Record the differences.
7. Repeat using a parameter instead of a header.

---

## Capstone Challenge

- Choose a request that contains:

  * URL parameters
  * Cookies
  * JSON body (if available)

- Perform three independent experiments.

- For each experiment, document:

  * Original request
  * Modification
  * Expected result
  * Actual result
  * Conclusion

- Do not move to the next experiment until the previous one is fully documented.

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

1. What happens after clicking **Send** in Repeater?
2. Does Repeater modify requests automatically?
3. Why is a baseline response important?
4. Why should you change only one variable at a time?
5. What information should you compare between two responses?
6. Why is forming a hypothesis important before testing?

---

## Quick Revision

* Repeater sends exactly what you write.
* Every request should begin with a baseline.
* Change one variable at a time.
* Compare responses carefully.
* Investigation is driven by evidence, not assumptions.

---

## Mastery Checklist

* [ ] I understand the request lifecycle.
* [ ] I know what happens after clicking **Send**.
* [ ] I can explain why responses change.
* [ ] I understand the importance of a baseline response.
* [ ] I completed the practice exercises.
* [ ] I completed the capstone challenge.
