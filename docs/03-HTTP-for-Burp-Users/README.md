# HTTP for Burp Users

## Objectives

- By the end of this chapter, you will understand:

  * What HTTP is
  * How browsers communicate with servers
  * The anatomy of an HTTP request
  * The anatomy of an HTTP response
  * Why Burp Suite revolves around HTTP messages
  * How understanding HTTP improves vulnerability discovery

---

## Why Learn HTTP Before Burp?

- Burp Suite is not a vulnerability scanner.

- Burp Suite is an HTTP message manipulation platform.

- Every action you perform in Burp—intercepting traffic, modifying requests, replaying requests, fuzzing parameters, testing authentication, or analyzing APIs—is based on understanding HTTP.

- If you understand HTTP well, Burp becomes intuitive.

---

## What is HTTP?

- HTTP (Hypertext Transfer Protocol) is the protocol that allows a client (such as a web browser) to communicate with a web server.

- Example:

  ```text
  Browser  ----------HTTP Request---------->  Server
  Browser  <---------HTTP Response---------- Server
  ```

- Every interaction with a website consists of one or more HTTP requests and responses.

---

## Client and Server

- The browser is the **client**.

- The website is the **server**.

- The client sends requests.

- The server sends responses.

- Example:

  ```text
  Chrome
     │
  HTTP Request
     ▼
  example.com

  example.com
     │  
  HTTP Response
     ▼
  Chrome
  ```

---

## Structure of an HTTP Request

- An HTTP request consists of:

  * Request Line
  * Headers
  * Blank Line
  * Message Body (optional)

- Example:

  ```http
  GET /login HTTP/1.1
  Host: example.com
  User-Agent: Mozilla/5.0
  Accept: text/html
  Cookie: session=abc123

  ```

---

## Request Components

### HTTP Method

- Common methods:

  * GET
  * POST
  * PUT
  * PATCH
  * DELETE
  * OPTIONS
  * HEAD

- Each method has a different purpose.

---

### URL

- Example:

  ```text
  https://example.com/login
  ```

- The URL tells the server which resource is being requested.

---

### Headers

- Headers provide additional information.

- Examples:

  ```text
  Host
  User-Agent
  Cookie
  Authorization
  Accept
  Content-Type
  Origin
  Referer
  ```

- Headers are one of the most frequently modified parts of a request during penetration testing.

---

### Body

- Some requests contain data.

- Example:

  ```text
  username=victim
  password=Password123
  ```

- POST requests commonly contain request bodies.

---

## Structure of an HTTP Response

- Responses contain:

  * Status Line
  * Headers
  * Blank Line
  * Response Body

- Example:

  ```http
  HTTP/1.1 200 OK
  Content-Type: text/html
  Content-Length: 1524

  <html>
  ...
  </html>
  ```

---

## HTTP Status Codes

- Common categories:

| Code | Meaning       |
| ---- | ------------- |
| 1xx  | Informational |
| 2xx  | Success       |
| 3xx  | Redirection   |
| 4xx  | Client Error  |
| 5xx  | Server Error  |

- Common examples:

  * 200 OK
  * 201 Created
  * 301 Moved Permanently
  * 302 Found
  * 400 Bad Request
  * 401 Unauthorized
  * 403 Forbidden
  * 404 Not Found
  * 500 Internal Server Error

- Status codes often provide valuable clues during security testing.

---

## Cookies

- Cookies allow servers to identify users.

- Example:

  ```http
  Cookie: session=abc123xyz
  ```

- Burp Suite lets you inspect, modify, replace, and replay cookies.

- Many authentication and authorization vulnerabilities involve cookies.

---

## Parameters

- Parameters are user-supplied inputs.

- Examples:

### URL Parameters

```text
?id=15
```

### Body Parameters

```text
username=admin
```

### JSON Parameters

```json
{
  "username":"admin",
  "role":"user"
}
```

- Parameters are primary targets during vulnerability testing.

---

## Why This Matters in Burp

- When using Burp Suite, you will frequently:

  * Modify headers
  * Modify cookies
  * Change parameters
  * Replay requests
  * Compare responses
  * Observe status codes
  * Analyze server behavior

- Understanding HTTP allows you to predict how applications will respond to your modifications.

---

## Professional Tips

* Read requests from top to bottom before modifying them.
* Understand what each header does before deleting or changing it.
* Focus on parameters that control identity, authorization, pricing, and business logic.
* Pay attention to unexpected status code changes.

---

## Common Mistakes

* Ignoring headers.
* Looking only at the response body.
* Changing multiple values at once, making it difficult to identify the cause of changes.
* Assuming every parameter is user-controllable.

---

## Practice Tasks

1. Visit a website through Burp Suite.
2. Locate the request in HTTP history.
3. Identify:

   * Method
   * URL
   * Headers
   * Cookies
   * Parameters
4. Identify the response status code.
5. Determine whether the request contains a body.

---

## Interview Questions

1. What is HTTP?
2. Explain the structure of an HTTP request.
3. What is the difference between headers and the request body?
4. What are cookies used for?
5. What information can be learned from HTTP status codes?
6. Why is HTTP knowledge essential for Burp Suite?

---

## Summary

- HTTP is the language spoken between browsers and web servers. Burp Suite gives you complete visibility into these conversations, allowing you to inspect, modify, replay, and analyze HTTP messages. A solid understanding of HTTP is the foundation of effective web application security testing.
