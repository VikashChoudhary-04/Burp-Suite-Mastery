# Introduction

> **Module Progress**

```text id="b7q5wr"
█░░░░░░░░░░░░░░ 1 / 15 Lessons
```

- Most Burp Suite tools analyze what happens **between your browser and the target application**.

- Burp Collaborator expands this perspective by allowing you to observe interactions that occur **outside the normal HTTP request-response cycle**.

- These external interactions often provide the only evidence that certain vulnerabilities exist.

---

## The Traditional Model

- Most web testing follows a simple pattern:

  ```text
  Client
     ↓
  HTTP Request
     ↓
  Web Application
     ↓
  HTTP Response
     ↓
  Client
  ```

- The tester immediately observes the server's response and determines whether the application behaved securely.

- For many vulnerabilities, this approach is sufficient.

---

## The Hidden Problem

- Not every vulnerability produces an immediate response.

- Sometimes the application:

  * Makes a DNS lookup.
  * Sends an HTTP request to another server.
  * Connects to an external service.
  * Sends an email.
  * Processes input asynchronously.

- These actions may occur:

  * After the response is returned.
  * On another server.
  * In a background process.
  * Without any visible indication to the user.

- From the tester's perspective, nothing appears to happen.

---

## Out-of-Band Testing

- Out-of-Band Application Security Testing (OAST) is designed for situations where evidence exists **outside the application's immediate response**.

- Instead of relying solely on what the browser receives, the tester observes whether the application initiates an external interaction.

- If such an interaction occurs, it can provide valuable evidence during an investigation.

---

## What Collaborator Does

- Burp Collaborator provides a controlled endpoint that can receive external interactions from the target application.

- Depending on the application's behavior, Collaborator may observe:

  * DNS lookups
  * HTTP requests
  * SMTP interactions (when applicable)

- These interactions become additional sources of evidence during testing.

---

## A Simple Example

- Imagine an application accepts a URL as input.

- After you submit the request, the application responds:

  > "Your request has been received."

- Nothing else is visible.

- However, if the server later attempts to fetch the supplied URL, Collaborator records that outbound request.

- Even though the browser never displayed evidence, the interaction confirms that the application reached out to the supplied address.

---

## Investigation Workflow

```text id="n2v8qt"
Submit Input
      ↓
Application Processes Request
      ↓
External Interaction Occurs
      ↓
Collaborator Records Interaction
      ↓
Tester Reviews Evidence
      ↓
Manual Investigation Continues
```

- The interaction is the beginning of the investigation—not the end.

---

## Key Principles

- Remember these ideas throughout the module:

  * Not every vulnerability is visible in an HTTP response.
  * External interactions may provide critical evidence.
  * An interaction alone does not confirm a vulnerability.
  * Context and manual analysis remain essential.
  * Collaborator supports investigation rather than replacing it.

---

## Collaborator Challenge

- An application allows users to submit a URL.

- After submitting a unique Collaborator address, the web page simply displays:

  > "Operation completed successfully."

- Several seconds later, Collaborator records a DNS lookup.

- Ask yourself:
  
  * What does this interaction prove?
  * What does it *not* prove?
  * What additional investigation would you perform?

- Thinking critically about evidence is a key skill in out-of-band testing.

---

## Key Takeaways

* Traditional testing relies on immediate responses.
* Some vulnerabilities produce evidence only through external interactions.
* Collaborator enables observation of these interactions.
* OAST extends testing beyond the browser.
* Evidence must always be interpreted within context.
