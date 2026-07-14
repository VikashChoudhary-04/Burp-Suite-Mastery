# How Burp Suite Works

## Objectives

- By the end of this chapter, you will understand:

  * Where Burp Suite sits in the communication process
  * How requests travel between the browser and the server
  * What an intercepting proxy is
  * Why Burp can modify traffic
  * The complete request lifecycle inside Burp Suite

---

## The Big Picture

- Normally, a browser communicates directly with a web server.

  ```text
  Browser  --------------------->  Web Server
            HTTP / HTTPS
  ```

- When Burp Suite is configured as a proxy, the communication changes.

```text
Browser
    │
    ▼
Burp Suite
    │
    ▼
Web Server
```

- Burp now sits in the middle of every request and every response.

---

## What is an Intercepting Proxy?

- An intercepting proxy is software that receives traffic from a client before forwarding it to the destination server.

- Instead of:

  ```text
  Browser ─────► Server
  ```

the communication becomes:

  ```text
  Browser ─────► Burp ─────► Server
  ```

- Because every request passes through Burp, it can:

  * View requests
  * Stop requests
  * Modify requests
  * Drop requests
  * Replay requests
  * Forward requests

- The same applies to responses coming back from the server.

---

## Request Lifecycle

- A single request follows this path:

  ```text
  1. User clicks a button

          │

          ▼

  2. Browser creates HTTP request

          │

          ▼
  
  3. Request reaches Burp Proxy

          │

          ├── Intercept OFF
          │         │
          │         ▼
          │    Forward automatically
          │
          └── Intercept ON
                    │
                    ▼
            Wait for user action

                    │
          ┌─────────┼─────────┐
          │         │         │
          ▼         ▼         ▼
       Forward    Modify     Drop

                    │
                    ▼

  4. Request reaches server

                    │
                    ▼

  5. Server processes request

                    │
                    ▼

  6. Response returns to Burp

                    │
                    ▼

  7. Response forwarded to browser

                    │
                    ▼

  8. Browser displays the page
  ```

- Understanding this lifecycle explains nearly every feature in Burp Suite.

---

## Why Can Burp Modify Requests?

- Because Burp receives the request before the server does.

- The server has not processed the request yet.

- This allows you to:

  * Change parameters
  * Change headers
  * Edit cookies
  * Remove fields
  * Add new values
  * Replay requests multiple times

without changing the application's source code.

---

## Request vs Response

- Burp can inspect both directions.

### Request

```text
Browser
     │
     ▼
Burp
     │
     ▼
Server
```

### Response

```text
Server
     │
     ▼
Burp
     │
     ▼
Browser
```

- Many vulnerabilities are discovered by comparing how the server responds to small changes in requests.

---

## Why This Matters for Security Testing

- Most web vulnerabilities are triggered by manipulating client-controlled data.

- Examples include:

  * Changing an account ID (IDOR)
  * Modifying a price value
  * Removing authentication headers
  * Changing cookies
  * Altering JSON data
  * Replaying requests
  * Modifying API calls

- Burp Suite makes these tests possible.

---

## Mental Model

- Think of Burp Suite as a security checkpoint.

```text
Browser
    │
    ▼

Security Checkpoint (Burp Suite)

    │
    ▼

Web Server
```

- Every request must pass through the checkpoint before reaching the destination.

- At the checkpoint, you can inspect, modify, allow, or reject the request.

---

## Professional Tips

* Never rush to modify requests. Read them first.
* Change one value at a time so you know what caused a different response.
* Save interesting requests for later analysis.
* Compare original and modified responses carefully.

---

## Common Mistakes

* Modifying several fields simultaneously.
* Forgetting whether Intercept is ON or OFF.
* Assuming every value is trusted by the server.
* Ignoring response differences after small request changes.

---

## Practice Tasks

1. Enable Intercept.
2. Visit `https://example.com`.
3. Observe the intercepted request.
4. Forward it without changes.
5. Refresh the page and modify one harmless header (such as `User-Agent`).
6. Forward the modified request.
7. Compare the responses.

---

## Interview Questions

1. What is an intercepting proxy?
2. Why can Burp Suite modify HTTP requests?
3. Where does Burp Suite sit in the request lifecycle?
4. What happens when Intercept is OFF?
5. What happens when Intercept is ON?
6. Why is changing one parameter at a time considered a best practice?

---

## Summary

- Burp Suite works by acting as an intercepting proxy between your browser and the target web application. Every request and response passes through Burp, allowing you to inspect, modify, replay, and analyze traffic before it reaches its destination. Understanding this request lifecycle is essential for effective web application security testing.
