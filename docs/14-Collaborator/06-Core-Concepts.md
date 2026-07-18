# Core Concepts

> **Module Progress**

```text id="3qk8mr"
██████░░░░░░░░░ 6 / 15 Lessons
```

- Burp Collaborator introduces several concepts that differ from traditional request-response testing.

- Understanding this vocabulary helps you interpret out-of-band evidence accurately and explain your reasoning clearly during assessments and interviews.

---

## OAST

- **OAST** stands for:

  > **Out-of-Band Application Security Testing**

- OAST observes application behavior that occurs outside the immediate request-response channel.

- Traditional testing:

  ```text id="w8n2qp"  
  Tester
     ↓
  Request
     ↓
  Application
     ↓
  Response
     ↓
  Tester  
  ```

- OAST adds another observable path:

  ```text id="m4v7rx"
  Tester
     ↓
  Application
     ↓
  External Interaction
     ↓
  Collaborator
  ```

- This makes otherwise invisible server-side behavior observable.

---

## Collaborator Payload

- A Collaborator payload is a unique identifier or address associated with an OAST test.

- Its purpose is to help correlate an observed external interaction with the input that may have triggered it.

- Conceptually:

  ```text id="j6p3wt"
  Test Input

  ↓

  Unique Collaborator Payload

  ↓

  Possible Interaction

  ↓

  Correlation
  ```

- Unique payloads improve investigative confidence.

---

## Interaction

- An **interaction** is observable activity associated with a Collaborator payload.

- Depending on the situation, this may include:

  * DNS activity
  * HTTP activity
  * SMTP activity where applicable

- An interaction tells you that something happened.

- It does not automatically tell you why.

---

## DNS Interaction

- A DNS interaction generally indicates that a system attempted to resolve a Collaborator hostname.

- Conceptually:

  ```text id="r2m8qy"
  Application or Related Component
  
  ↓

  Hostname Resolution
  
  ↓

  DNS Infrastructure
  
  ↓

  Recorded Interaction
  ```

- This can provide evidence that supplied data reached a component capable of initiating name resolution.

- It does not necessarily prove that a full connection to the destination occurred.

---

## HTTP Interaction

- An HTTP interaction provides evidence that an HTTP request reached the Collaborator infrastructure.

- Conceptually:

  ```text id="v7k4pn"
  Application or Related Component

  ↓

  Network Request

  ↓

  Collaborator

  ↓

  HTTP Interaction Recorded
  ```

- This may provide stronger evidence of outbound communication than DNS resolution alone.

- The exact security meaning still depends on context.

---

## SMTP Interaction

- In some OAST scenarios, email-related behavior may produce SMTP interactions.

- These can help investigate application functionality involving:

  * Email delivery
  * Notifications
  * Message processing

- As with other protocols, the interaction itself must be interpreted within the tested workflow.

---

## Polling

- **Polling** is the process of checking the Collaborator service for new interactions associated with generated payloads.

- Polling matters because interactions may be delayed by:

  * Background processing
  * Queues
  * Scheduled jobs
  * Asynchronous services

- Do not assume all interactions will appear immediately.

---

## Correlation

- Correlation connects an observed interaction to the test that likely caused it.

- Strong correlation may involve:

  * A unique payload.
  * A recorded original request.
  * Matching timestamps.
  * Reproducible behavior.
  * Clear investigation notes.

- Conceptually:

  ```text id="x5q9mv"
  Original Request

  ↓

  Unique Payload

  ↓

  Observed Interaction

  ↓

  Matching Context

  ↓

  Strong Correlation
  ```

- Correlation is essential for defensible findings.

---

## Asynchronous Behavior

- Not all application processing happens immediately.

 - A request may trigger:

  ```text id="f3n8rk"
  User Request

  ↓

  Application Accepts Task

  ↓

  Immediate Response

  ↓

  Background Processing

  ↓

  External Interaction
  ```

- The HTTP response may therefore finish long before Collaborator receives evidence.

- This is one reason OAST investigations require patience.

---

## Interaction Attribution

- Attribution means determining which system or component likely caused an interaction.

- This can be difficult.

- Observed traffic may involve:

  * Application servers
  * Background workers
  * Proxies
  * DNS resolvers
  * Security infrastructure
  * Supporting services

- Avoid claiming more than the evidence supports.

---

## Unique Payload Strategy

- Consider two approaches.

### Approach A

- Use one Collaborator payload across twenty tests.

- If an interaction appears, identifying the trigger may be difficult.

### Approach B

- Use distinct payloads for separate tests.

  ```text id="p8w2qt"
  Test A → Payload A

  Test B → Payload B
  
  Test C → Payload C
  ```

- When Payload B receives an interaction, correlation becomes much easier.

- Good evidence begins with good test design.

---

## Interaction ≠ Finding

- Keep this distinction clear:

  ```text id="k4m7vx"
  Interaction

  ↓

  Evidence

  ↓

  Investigation

  ↓

  Validation

  ↓

  Confirmed Finding
  ```

- A Collaborator interaction is not automatically a reportable vulnerability.

- It becomes meaningful only after context and impact are established.

---

## Quick Reference

| Concept               | Meaning                                                         |
| --------------------- | --------------------------------------------------------------- |
| OAST                  | Testing behavior outside the immediate request-response channel |
| Collaborator Payload  | Unique address used to correlate external interactions          |
| Interaction           | Recorded external activity                                      |
| DNS Interaction       | Evidence of hostname resolution activity                        |
| HTTP Interaction      | Evidence of an HTTP request reaching Collaborator               |
| SMTP Interaction      | Evidence of email-related communication where applicable        |
| Polling               | Checking for newly recorded interactions                        |
| Correlation           | Linking an interaction to a specific test                       |
| Asynchronous Behavior | Processing that occurs separately or later                      |
| Attribution           | Determining what component likely caused an interaction         |

---

## Collaborator Challenge

- You perform four authorized tests:

  ```text id="b6r3ny"
  Test A → Payload A → No interaction

  Test B → Payload B → DNS

  Test C → Payload C → DNS + HTTP

  Test D → Payload D → Delayed HTTP
  ```

- Ask yourself:

  * What does each result directly demonstrate?
  * Which test provides the strongest evidence of outbound communication?
  * Why is Test D important when thinking about asynchronous processing?
  * Which results require further investigation before becoming reportable findings?

- Do not confuse interaction strength with vulnerability severity.

---

## Key Takeaways

* OAST observes behavior outside the normal HTTP response path.
* Collaborator payloads enable interaction correlation.
* DNS, HTTP, and SMTP interactions provide different evidence.
* Polling is important because processing may be delayed.
* Strong correlation improves confidence.
* Attribution requires caution.
* An interaction must be investigated before becoming a confirmed finding.
