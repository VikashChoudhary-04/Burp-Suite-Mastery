# Intruder Interface

## Overview

- The Intruder interface is where you configure, execute, and analyze automated experiments.

- Every section of the interface supports a specific stage of the investigation workflow.

- Rather than memorizing tabs, learn how each one contributes to answering your investigation question.

---

## Learning Objectives

- By the end of this lesson, you will be able to:

  * Navigate the Intruder interface confidently.
  * Understand the purpose of each major section.
  * Configure an Intruder attack from start to finish.
  * Recognize which interface elements deserve the most attention.
  * Organize automated investigations efficiently.

---

## Prerequisites

- You should already understand:

  * HTTP fundamentals
  * Proxy
  * Target
  * Repeater
  * Intruder Introduction

---

## Difficulty

🟢 Beginner

---

## Estimated Time

| Activity          |         Time |
| ----------------- | -----------: |
| Reading           |       20 min |
| Hands-on Practice |       25 min |
| Exercises         |       20 min |
| **Total**         | **≈ 65 min** |

---

## Why Learn the Interface?

- A beginner sees tabs and buttons.

- A professional sees a workflow.

- Understanding how information flows through the interface helps you configure attacks efficiently and interpret results correctly.

---

## Mental Model

- Think of Intruder as a production line.

```text
Choose Request
      │
      ▼
Select Positions
      │
      ▼
Choose Attack Type
      │
      ▼
Configure Payloads
      │
      ▼
Run Attack
      │
      ▼
Analyze Results
      │
      ▼
Return Interesting Requests to Repeater
```

---

## Decision Point

```text
Need one careful experiment?
        │
      Yes ─────────► Repeater
        │
       No
        │
Need many controlled variations?
        │
      Yes ─────────► Intruder
```

- Do not use Intruder simply because it exists.

- Use it because automation helps answer your investigation question.

---

## Behind the Interface

- A typical Intruder workflow consists of five stages:

  1. Target request
  2. Payload positions
  3. Attack configuration
  4. Attack execution
  5. Results analysis

- Each stage builds on the previous one.

---

## Main Interface Areas

- A typical Intruder window contains:

  ```text
  +-------------------------------------------------------------+
  | Target Request                                              |
  +-------------------------------------------------------------+
  | Positions | Payloads | Resource Pool | Settings             |
  +-------------------------------------------------------------+
  | Attack Configuration                                        |
  +-------------------------------------------------------------+
  | Start Attack                                                |
  +-------------------------------------------------------------+
  | Results                                                     |
  +-------------------------------------------------------------+
  ```

> [!NOTE]
> The exact layout may vary slightly depending on your Burp Suite edition and version.

---

## Request Editor

- The Request Editor contains the HTTP request that will be used as the basis for the attack.

- Before configuring anything else:

  * Read the request.
  * Understand its purpose.
  * Identify the values you may want to vary.

- Never automate a request you do not understand.

---

## Positions Tab

- The Positions tab defines **where** Intruder will insert payloads.

- Examples include:

  * Query parameters
  * Form fields
  * JSON values
  * Cookies
  * Headers
  * URL path segments

- Good position selection is one of the most important skills in using Intruder effectively.

---

## Payloads Tab

- The Payloads tab defines **what values** will replace the selected positions.

- Examples:

  * Sequential numbers
  * Usernames
  * Email addresses
  * IDs
  * Custom wordlists

- A small, targeted payload list is often more valuable than a massive one.

---

## Resource Pool

- The Resource Pool controls how requests are sent.

- Examples include:

  * Concurrent requests
  * Request throttling
  * Connection limits

> [!IMPORTANT]
> Configure request rates responsibly. Respect the rules and scope of the authorized engagement.

---

## Settings

- Settings control how the attack behaves.

- Depending on the Burp version, this may include options related to:

  * Redirect handling
  * Request behavior
  * Attack execution

- Only change settings when you understand why they are needed.

---

## Results Table

- After starting the attack, Intruder displays a results table.

- Typical columns include:

  * Request number
  * Payload
  * Status code
  * Response length
  * Response time
  * Error indicators

- This table is where pattern recognition begins.

---

## What Professionals Look At First

- When reviewing results, experienced testers often check:

  1. Status code
  2. Response length
  3. Response time (when relevant)
  4. Error messages
  5. Interesting payload values

- The goal is to identify responses that differ from the majority.

---

## Professional Workflow

```text
Capture Request
        │
        ▼
Understand the Request
        │
        ▼
Choose Positions
        │
        ▼
Configure Payloads
        │
        ▼
Run Controlled Attack
        │
        ▼
Analyze Results
        │
        ▼
Return Interesting Responses to Repeater
```

- Intruder finds patterns.

- Repeater explains them.

---

## Performance & Safety

- Before launching an attack, ask:

  * Is this target authorized?
  * Is my payload list larger than necessary?
  * Will this request create unnecessary load?
  * Can I answer the same question with fewer requests?

- Efficient automation is part of professional testing.

---

## Real Example

- Suppose you want to understand how an application responds to different account IDs.

- Workflow:

  1. Send the request to Intruder.
  2. Select the account ID as the payload position.
  3. Configure a small range of IDs.
  4. Launch the attack.
  5. Sort the results by response length.
  6. Investigate unusual responses manually in Repeater.

---

## Field Notes

- Large attacks do not necessarily produce better findings.

- Well-designed attacks with focused payloads often reveal more than millions of requests.

- Quality beats quantity.

---

## Common Mistakes

* Selecting too many payload positions.
* Using unnecessarily large payload lists.
* Ignoring the baseline request.
* Looking only at successful responses.
* Forgetting to investigate interesting results manually.

---

## Practice Exercises

1. Choose one request.
2. Identify one suitable payload position.
3. Explain why you selected it.
4. Decide what payload values you would use.
5. Explain which result would be most interesting and why.

---

## Interview Questions

1. What is the purpose of the Positions tab?
2. What is the purpose of the Payloads tab?
3. Why should you understand the request before automating it?
4. Why are response length and status code useful when reviewing results?
5. Why should interesting responses be verified in Repeater?

---

## Quick Revision

* Request → Positions → Payloads → Attack → Results.
* Automate only after understanding the request.
* Choose payload positions carefully.
* Use focused payloads.
* Analyze patterns before drawing conclusions.
* Verify interesting results manually.

---

## Mastery Checklist

* [ ] I understand each major section of the Intruder interface.
* [ ] I know how requests move through the workflow.
* [ ] I can identify appropriate payload positions.
* [ ] I understand where to review attack results.
* [ ] I completed the practice exercise.
