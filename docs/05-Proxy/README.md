# Proxy Tool

## Objectives

- By the end of this chapter, you will be able to:

  * Understand the purpose of the Proxy tool
  * Configure and use request interception
  * Analyze HTTP history
  * Modify requests before they reach the server
  * Inspect and modify responses
  * Use Proxy effectively during penetration tests and bug bounty engagements

---

## What is the Proxy Tool?

- The Proxy tool is the core component of Burp Suite. It acts as an intercepting proxy between your browser and the target web application.

- Every HTTP and HTTPS request from your browser passes through the Proxy before reaching the server.

- Likewise, every response from the server passes back through the Proxy before being displayed in your browser.

- Without the Proxy tool, Burp Suite would not be able to inspect or manipulate web traffic.

---

## Why Does the Proxy Tool Exist?

- Browsers send requests automatically.

- Normally, you cannot pause or modify those requests before they reach the server.

- The Proxy tool solves this problem by placing Burp Suite between the browser and the web server, giving you complete control over the communication.

- This enables you to:

  * Inspect requests
  * Modify requests
  * Drop requests
  * Replay requests
  * Analyze responses
  * Discover vulnerabilities

---

## Where Does the Proxy Fit?

```text
Browser
    │
    ▼
Burp Proxy
    │
    ▼
Target Server
```

- Every request flows through the Proxy.

---

## Components of the Proxy Tool

- The Proxy tool contains several important sections:

  * Intercept
  * HTTP History
  * WebSockets History
  * Options (or Proxy Settings, depending on the Burp version)

- Each section serves a different purpose.

---

## Intercept

- The Intercept tab allows you to pause requests before they are sent to the server.

- When **Intercept is ON**:

  * Requests stop inside Burp.
  * You can inspect or modify them.
  * You choose when they are forwarded.

- When **Intercept is OFF**:

  * Requests pass through automatically.
  * Burp still records them in HTTP History.

- **Professional Tip:** During general browsing, keep Intercept OFF. Turn it ON only when you want to inspect or modify a specific request.

---

## HTTP History

- HTTP History records every request and response that passes through the Proxy.

- For each request, you can view information such as:

  * Host
  * Method
  * URL
  * Status code
  * MIME type
  * Response length
  * Timestamp

- HTTP History is often the starting point for manual testing because it provides a complete record of application traffic.

---

## WebSockets History

- Some modern applications use WebSockets instead of traditional HTTP requests.

- The WebSockets History tab records these messages, allowing you to inspect real-time communication between the browser and the server.

---

## Proxy Settings

- The Proxy settings allow you to configure how Burp listens for browser traffic.

- Common settings include:

  * Proxy listeners
  * Listening address
  * Listening port
  * Request interception rules
  * Response interception rules

- The default listener is:

  * Address: `127.0.0.1`
  * Port: `8080`

---

## Typical Workflow

- A common manual testing workflow is:

  ```text
  Browse the application
          │
          ▼
  Requests appear in HTTP History
          │
          ▼
  Identify an interesting request
          │
          ▼
  Send it to Repeater
          │
          ▼
  Modify parameters
          │
          ▼
  Analyze responses
  ```

- This simple workflow is used repeatedly throughout web application assessments.

---

## Real-World Example

- Imagine you submit a login form.

- The browser sends:

  ```http
  POST /login HTTP/1.1
  Host: example.com
  Content-Type: application/x-www-form-urlencoded
  
  username=alice&password=Password123
  ```

- With Intercept ON, Burp pauses this request before it reaches the server.

- You can:

  * View the request.
  * Modify the username or password.
  * Add or remove headers.
  * Change cookies.
  * Forward or drop the request.

---

## Professional Tips

* Keep Intercept OFF during normal browsing.
* Use HTTP History as your primary source of requests.
* Read the entire request before making changes.
* Save interesting requests for later analysis.

---

## Common Mistakes

* Leaving Intercept ON and wondering why pages stop loading.
* Editing multiple parameters at once.
* Ignoring HTTP History.
* Forgetting to verify the scope before testing.

---

## Practice Tasks

1. Enable Intercept.
2. Visit `https://example.com`.
3. Observe the intercepted request.
4. Forward the request.
5. Disable Intercept.
6. Browse a few pages.
7. Open HTTP History and review the recorded requests.

---

## Interview Questions

1. What is the purpose of the Proxy tool?
2. What happens when Intercept is ON?
3. What happens when Intercept is OFF?
4. Why is HTTP History important?
5. How does the Proxy tool assist in vulnerability testing?

---

## Summary

- The Proxy tool is the foundation of Burp Suite. It enables you to intercept, inspect, modify, and analyze web traffic, making it the starting point for nearly every manual web application security assessment.
