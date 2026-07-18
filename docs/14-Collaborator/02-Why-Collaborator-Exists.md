# Why Collaborator Exists

> **Module Progress**

```text id="r8v2mt"
██░░░░░░░░░░░░░ 2 / 15 Lessons
```

- Most web security testing is based on a simple assumption:

  > If the application is vulnerable, the response will reveal it.

- While this is true for many issues, modern web applications often perform work that is **not immediately visible** to the user.

- Burp Collaborator was created to help investigators observe this hidden behavior.

---

## The Limitation of Request–Response Testing

- Traditional testing observes:
  
  ```text id="g4p7ws"
  Request
     ↓
  Application
     ↓
  Response
  ```

- This works well when the application's behavior is reflected directly in the HTTP response.

- However, many server-side processes continue after the response has already been sent.

- Examples include:

  * Fetching remote resources.
  * Processing background jobs.
  * Sending notifications.
  * Communicating with internal or external services.
  * Performing asynchronous tasks.

- The browser may never display evidence that these actions occurred.

---

## Why Hidden Interactions Matter

- Some security issues only become apparent when the application makes an outbound interaction.

- Rather than looking only at what comes **back to the client**, testers may also need to observe what goes **out from the server**.

- This broader perspective helps identify behavior that would otherwise remain invisible during a normal assessment.

---

## The Role of Collaborator

- Collaborator provides a controlled endpoint that records outbound interactions initiated by the target application.

- Depending on the application's behavior, it may capture evidence such as:

  * DNS lookups
  * HTTP requests
  * SMTP interactions (where applicable)

- These records give testers another source of evidence when investigating application behavior.

---

## Evidence vs. Conclusions

- An outbound interaction is **evidence**, not proof of a specific vulnerability.

- For example, a DNS lookup may show that the application attempted to resolve a supplied hostname.

- It does **not**, by itself, establish:

  * The root cause.
  * The impact.
  * The exploitability.
  * The security severity.

- Professional testers combine Collaborator evidence with manual investigation before reaching conclusions.

---

## Typical Use Cases

- Collaborator is often useful when assessing applications for issues that may trigger external interactions, including:

  * Blind Server-Side Request Forgery (SSRF)
  * Blind XML External Entity (XXE) processing
  * Blind Command Injection
  * Certain Server-Side Template Injection (SSTI) scenarios
  * Other asynchronous or out-of-band behaviors

- The common characteristic is that **the browser alone may not reveal what happened**.

---

## Investigation Workflow

```text id="w6n3qp"
Submit Input
      ↓
Application Processes Data
      ↓
Possible External Interaction
      ↓
Collaborator Records Evidence
      ↓
Analyze Context
      ↓
Validate Findings
```

- The recorded interaction is a clue that guides further investigation.

---

## Collaborator Challenge

- You submit input to an application and receive a normal success message.

- A short time later, Collaborator records an HTTP request from the target environment.

- Consider:

  * What does this tell you about the application's behavior?
  * What questions remain unanswered?
  * What additional evidence would you seek before reporting a vulnerability?

- Approach every interaction with curiosity rather than assumptions.

---

## Key Takeaways

* Traditional request–response testing has limitations.
* Modern applications often perform hidden server-side actions.
* Collaborator helps observe outbound interactions.
* Recorded interactions provide investigative evidence.
* Manual validation is required before drawing conclusions.
