# Repeater Introduction

## Learning Objectives

- By the end of this lesson, you will be able to:

  * Explain what Burp Repeater is.
  * Understand why Repeater is one of the most important Burp Suite tools.
  * Describe how Repeater differs from normal browser behavior.
  * Recognize situations where Repeater should be used.
  * Develop the mindset of manually investigating web applications.

---

## Introduction

- Burp Repeater is a tool that allows you to manually send HTTP requests to a web server, modify them, resend them, and study the responses.

- Unlike a browser, which automatically builds and sends requests, Repeater gives you complete control over every part of the communication.

- Think of Repeater as a laboratory where you can safely experiment with requests without changing the application's source code.

- For many professional penetration testers, Repeater is the most frequently used feature in Burp Suite.

---

## Why Does Repeater Exist?

- When browsing a website normally, every click produces a request that is immediately sent to the server.

- If you want to test a single parameter ten different ways, a browser becomes slow and inconvenient.

- Repeater solves this problem.

- It allows you to:

  * Send the same request repeatedly.
  * Modify one value at a time.
  * Observe changes in server behavior.
  * Compare responses.
  * Build hypotheses and test them quickly.

- This makes Repeater ideal for manual security testing.

---

## Mental Model

- Imagine you're a scientist performing an experiment.

```text
Original Request
       │
       ▼
Change One Value
       │
       ▼
Send Request
       │
       ▼
Observe Response
       │
       ▼
Record Findings
       │
       ▼
Repeat
```

- Repeater follows the same scientific method:

  1. Observe.
  2. Form a hypothesis.
  3. Modify one variable.
  4. Test.
  5. Analyze.
  6. Draw a conclusion.

---

## Behind the Scenes

- When you send a request from Proxy to Repeater, Burp creates a copy of that request.

- The original request remains unchanged.

- Inside Repeater you can:

  * Edit the copied request.
  * Send it any number of times.
  * Compare server responses.
  * Continue refining the request until you understand the application's behavior.

- Because the request is independent of your browser session, you are free to experiment without repeatedly navigating through the application.

---

## Practical Demonstration

- Suppose you intercept the following request:

  ```http
  GET /profile?id=15 HTTP/1.1
  Host: example.com
  Cookie: session=abc123
  ```

- You send it to Repeater.

- Now you change:

  ```text
  id=15
  ```

to

```text
id=16
```

and send it again.

- Questions to ask:

  * Did the response change?
  * Was different data returned?
  * Was access denied?
  * Did the status code change?
  * Did the response size change?

- These observations often lead to vulnerability discovery.

---

## Professional Workflow

- A common workflow looks like this:

  ```text
  Browse Application
          │
          ▼
  Intercept Request
          │
          ▼
  Send to Repeater
          │
          ▼
  Modify One Value
          │
          ▼
  Send Request
          │
          ▼
  Analyze Response
          │
          ▼
  Repeat Until You Understand the Behavior
  ```

- Notice that the goal is **understanding**, not simply finding a vulnerability.

- Professionals first understand how the application works, then look for ways it can fail.

---

## Attack Scenario

- You are testing an online shopping application.

- Original request:

  ```text
  GET /order?id=482
  ```

- Questions to investigate:

  * What happens if the order ID changes?
  * Can another user's order be accessed?
  * Does the server return an error?
  * Is authorization enforced?
  * Does the response length change?

- You are not trying to "hack" the application.

- You are trying to understand how it behaves when controlled inputs change.

---

## Field Notes

- One of the biggest differences between beginners and experienced testers is patience.

- Beginners often change many values at once.

- Professionals usually change **one variable at a time**.

- When only one value changes, it becomes much easier to determine why the application's behavior changed.

---

## Common Mistakes

* Modifying several parameters simultaneously.
* Ignoring response headers.
* Looking only at the page displayed in the browser instead of the raw response.
* Assuming different responses always indicate a vulnerability.
* Forgetting to compare the original request with the modified request.

---

## Practice Exercises

1. Open Burp Suite.
2. Browse to any practice application.
3. Send one request to Repeater.
4. Change a single parameter.
5. Send the request.
6. Record every difference in the response.
7. Repeat with a different parameter.

---

## Capstone Challenge

- Intercept a request that contains at least:

  * one parameter
  * one cookie
  * several headers

- Perform three separate tests:

  1. Modify only the parameter.
  2. Modify only the cookie.
  3. Modify only one header.

- For each test, answer:

  * What changed?
  * What stayed the same?
  * Did the server react differently?

---

## Investigation Journal

- Complete the following after finishing the challenge.

| Step           | Your Notes |
| -------------- | ---------- |
| Observation    |            |
| Hypothesis     |            |
| Test Performed |            |
| Result         |            |
| Conclusion     |            |

---

## Interview Questions

1. What is Burp Repeater?
2. Why is Repeater preferred for manual testing?
3. Why should you modify only one value at a time?
4. What is the difference between Proxy and Repeater?
5. Can Repeater send the same request multiple times?
6. Why is response analysis important?

---

## Quick Revision

* Repeater is designed for manual request manipulation.
* It works on copies of requests.
* Change one value at a time.
* Observe before concluding.
* Compare responses carefully.
* Think like an investigator, not just a tool user.

---

## Mastery Checklist

- After completing this lesson, you should be able to answer **Yes** to all of the following:

  * [ ] I can explain what Repeater is.
  * [ ] I understand why professionals rely on Repeater.
  * [ ] I know when to use Repeater.
  * [ ] I know how Repeater differs from Proxy.
  * [ ] I understand the importance of changing one variable at a time.
  * [ ] I completed the practice exercises.
  * [ ] I completed the capstone challenge.
