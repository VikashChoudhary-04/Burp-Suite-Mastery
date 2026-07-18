# Behind the Scenes

> **Module Progress**

```text
████░░░░░░░░░░░ 4 / 15 Lessons
```

- Burp Collaborator works by giving the tester a unique address that can be observed for external interactions.

- When an application processes that address, activity may occur outside the normal request-response path.

- Understanding this flow helps you interpret Collaborator evidence correctly.

---

## Step 1 — Generate a Unique Payload

- The tester begins with a unique Collaborator payload.

- Conceptually, it acts as an identifiable destination associated with a particular test.

```text
Unique Payload
      ↓
Associated With Test
```

- Using unique identifiers helps distinguish one investigation from another.

---

## Step 2 — Supply the Payload

- The payload is introduced into an authorized test case where the application may process externally referenced data.

- At this point, two broad outcomes are possible:

```text
Application Processes Input
          ↓
     ┌────┴────┐
     │         │
No External   External
Interaction   Interaction
     │         │
     ▼         ▼
No OAST       Evidence
Evidence      Recorded
```

- The absence of an interaction does not automatically prove that no vulnerability exists.

---

## Step 3 — The Application Processes the Input

- The target may process supplied data:

  * Immediately
  * Asynchronously
  * Through a background worker
  * Through another internal component

- This explains why Collaborator interactions may not appear at the same time as the original HTTP response.

---

## Step 4 — DNS Resolution May Occur

- Before connecting to a hostname, a system commonly needs to resolve it.

- Conceptually:

  ```text
  Target Application
          ↓
  Needs Hostname Resolution
          ↓
  DNS Query
          ↓
  Collaborator Infrastructure
  ```

- A recorded DNS interaction can provide evidence that some component attempted to resolve the supplied hostname.

- It does not necessarily prove that a full network connection followed.

---

## Step 5 — Additional Interaction May Follow

- Depending on the application's behavior, further communication may occur after resolution.

- For example:

  ```text
  DNS Resolution
        ↓
  Connection Attempt
        ↓
  HTTP Interaction
  ```

- Different interaction types provide different pieces of evidence.

- A DNS lookup and an HTTP request should therefore not be treated as identical observations.

---

## Step 6 — Collaborator Records the Interaction

- The Collaborator infrastructure associates observed activity with the unique payload.

- Relevant metadata may then become available for investigation.

- This allows the tester to correlate:

  ```text
  Original Test
        ↕
  Unique Payload
        ↕
  Observed Interaction
  ```

- Correlation is one of the most important principles in OAST testing.

---

## Step 7 — Burp Retrieves the Evidence

- The Collaborator client polls for recorded interactions.

- Once retrieved, the tester can review the available evidence and compare it with:

  * The original request
  * The tested input
  * Timing
  * Application behavior
  * Other observed interactions

- The purpose is to reconstruct what most likely happened.

---

## DNS vs HTTP Evidence

- Consider these two observations.

### Observation A

- Only a DNS interaction appears.

- This may indicate that a system attempted to resolve the Collaborator hostname.

### Observation B

- DNS activity is followed by an HTTP interaction.

- This provides additional evidence that network communication progressed beyond hostname resolution.

- Neither observation should be interpreted without context.

---

## Correlation Matters

- Imagine using the same Collaborator payload across twenty unrelated tests.

- Later, an interaction appears.

- Which test caused it?

- You may not know.

- Using unique payloads and maintaining good notes creates stronger correlation between cause and observed behavior.

```text
Test A → Payload A → Interaction A

Test B → Payload B → No Interaction

Test C → Payload C → Interaction C
```

- Clear correlation produces stronger evidence.

---

## Important Limitation — Source Attribution

- The system generating an interaction may not always be the same system that processed the original request.

- Interactions could involve:

  * Application servers
  * Background workers
  * Proxies
  * Security infrastructure
  * Other supporting components

- Avoid making unsupported assumptions about the exact source based solely on an interaction.

---

## Collaborator Challenge

- You submit a unique Collaborator payload during an authorized test.

- Ten seconds later:

  1. A DNS interaction appears.
  2. Shortly afterward, an HTTP interaction appears.

- Ask yourself:

  * What does the DNS interaction demonstrate?
  * What additional evidence does the HTTP interaction provide?
  * Can you confidently correlate both with your original test?
  * What conclusions would still require further validation?

- Separate **observation**, **inference**, and **conclusion**.

---

## Key Takeaways

* Collaborator uses unique payloads to correlate external interactions with tests.
* Server-side processing may occur asynchronously.
* DNS and HTTP interactions provide different levels of evidence.
* An interaction does not automatically establish root cause or impact.
* Strong correlation is essential for reliable OAST investigations.
* Manual interpretation turns recorded interactions into meaningful security evidence.
