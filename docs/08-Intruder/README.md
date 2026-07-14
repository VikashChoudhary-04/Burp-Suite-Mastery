# Intruder

> *"The Burp Suite tool for automating controlled experiments against web applications."*

---

## What is Intruder?

- Burp Intruder is an automation tool that sends multiple variations of an HTTP request by replacing selected parts of the request with different values.

- Unlike Repeater, which focuses on one request at a time, Intruder allows you to perform large numbers of structured experiments while keeping full control over what changes between requests.

- Intruder is best viewed as an **experiment automation engine**, not simply a brute-force tool.

---

## Why Learn Intruder?

- Imagine you want to answer questions such as:

  * How does the application respond to different user IDs?
  * Which API parameters affect the response?
  * Does input validation behave consistently?
  * Which responses deserve deeper investigation?

- Manually repeating hundreds of requests would be slow and error-prone.

- Intruder automates the repetitive part while allowing you to focus on analyzing the results.

---

## Learning Objectives

- By the end of this module, you will be able to:

  * Understand when Intruder should be used.
  * Choose between Repeater and Intruder confidently.
  * Configure Intruder attacks correctly.
  * Understand attack types and payloads.
  * Analyze large result sets.
  * Identify interesting responses efficiently.
  * Use Intruder during professional penetration tests and authorized security assessments.

---

## Repeater vs Intruder

| Repeater              | Intruder           |
| --------------------- | ------------------ |
| Manual testing        | Automated testing  |
| One request at a time | Many requests      |
| Deep investigation    | Pattern discovery  |
| Hypothesis validation | Hypothesis scaling |
| Interactive           | Batch-oriented     |

- A common professional workflow is:

  ```text
  Proxy
     │
     ▼
  Repeater
     │
     ▼
  Understand the application
     │
     ▼  
  Intruder
     │
     ▼
  Scale the investigation
     │
     ▼
  Review results
     │
     ▼
  Return to Repeater for manual verification  
  ```

- Repeater and Intruder complement each other—they are not competitors.

---

## Mastery Path

### 🟢 Beginner

* Understand what Intruder does.
* Learn the interface.
* Configure a simple attack.

---

### 🟡 Intermediate

* Select payload positions.
* Use different attack types.
* Interpret response patterns.
* Filter interesting results.

---

### 🟠 Advanced

* Build efficient payload sets.
* Use payload processing.
* Analyze APIs.
* Reduce false positives.

---

### 🔴 Expert

- Use Intruder as part of a complete investigation workflow by combining automation with careful manual verification in Repeater.

---

## Module Structure

| Lesson | Description             |
| ------ | ----------------------- |
| 01     | Introduction            |
| 02     | Interface               |
| 03     | Behind the Scenes       |
| 04     | Attack Types            |
| 05     | Payload Positions       |
| 06     | Payload Types           |
| 07     | Payload Processing      |
| 08     | Grep & Results Analysis |
| 09     | Professional Workflow   |
| 10     | Attack Scenarios        |
| 11     | Field Notes             |
| 12     | Common Mistakes         |
| 13     | Interview Questions     |
| 14     | Exercises               |
| 15     | Quick Revision          |

---

## Recommended Learning Order

- For each lesson:

  1. Read the concept.
  2. Practice in Burp Suite.
  3. Complete the exercises.
  4. Record observations.
  5. Review the Quick Revision page.

- Avoid skipping directly to attack types before understanding how Intruder works.

---

## Prerequisites

- Before beginning this module, you should already understand:

  * HTTP fundamentals
  * Proxy
  * Target
  * Repeater
  * Response analysis
  * Investigation methodology

---

## Professional Mindset

- The goal of Intruder is **not** to send as many requests as possible.

- The goal is to answer a well-defined question efficiently.

- Always begin with:

  * A clear objective.
  * A baseline request.
  * A hypothesis.
  * A controlled payload set.

- Automation should support your investigation—not replace your reasoning.

---

## Expected Outcome

- After completing this module, you should be able to:

  * Decide when Intruder is appropriate.
  * Design efficient automated experiments.
  * Analyze results systematically.
  * Identify requests that require manual follow-up.
  * Integrate Intruder into a professional testing workflow.
