# Interview Questions

## Overview

- This lesson prepares you for technical interviews involving Burp Intruder.

- The objective is not to memorize answers.

- Instead, focus on understanding the reasoning behind each response so you can adapt your explanation to different interview situations.

---

## Learning Objectives

- By the end of this lesson, you will be able to:

  * Explain Burp Intruder clearly.
  * Compare Intruder with other Burp Suite tools.
  * Describe professional testing workflows.
  * Answer common interview questions confidently.
  * Demonstrate practical understanding instead of memorization.

---

## Beginner Questions

### 1. What is Burp Intruder?

**Expected Answer**

- Burp Intruder is Burp Suite's automation tool for sending controlled variations of an HTTP request. It helps automate repetitive experiments while allowing the tester to analyze patterns across multiple responses.

---

### 2. Why would you use Intruder?

**Expected Answer**

- To automate repetitive testing when manually sending each request in Repeater would be inefficient.

- Automation supports investigation by helping identify interesting responses for manual follow-up.

---

### 3. What is the difference between Repeater and Intruder?

**Expected Answer**

- Repeater is used for manual investigation of individual requests.

- Intruder automates many variations of a request so that response patterns can be analyzed efficiently.

---

### 4. What is a payload position?

**Expected Answer**

- A payload position identifies the part of the request that Intruder will replace with payload values during an attack.

---

### 5. What is a payload type?

**Expected Answer**

- A payload type defines how Intruder generates or supplies the values that are inserted into the selected payload positions.

---

## Intermediate Questions

### 6. What are the four Intruder attack types?

**Expected Answer**

* Sniper
* Battering Ram
* Pitchfork
* Cluster Bomb

- The correct choice depends on the investigation objective rather than personal preference.

---

### 7. When would you choose Sniper?

**Expected Answer**

- When only one value changes at a time and the goal is to isolate the effect of a single variable.

---

### 8. Why is Cluster Bomb more resource-intensive?

**Expected Answer**

- Because it generates every possible combination of the configured payload lists, which can rapidly increase the total number of requests.

---

### 9. Why is a baseline important before using Intruder?

**Expected Answer**

- The baseline provides a reference for comparing automated responses, making it easier to identify meaningful differences.

---

### 10. Why should payload lists start small?

**Expected Answer**

- Small payload sets are easier to analyze, generate fewer requests, and often answer the investigation question more efficiently.

---

## Advanced Questions

### 11. What metrics do you review first after an Intruder attack?

**Expected Answer**

- Typically:

  * Status code
  * Response length
  * Response time (when relevant)
  * Error messages
  * Grep matches

- The exact order depends on the investigation objective.

---

### 12. Why should interesting Intruder results be verified in Repeater?

**Expected Answer**

- Repeater allows detailed manual investigation, confirmation of reproducibility, and collection of evidence before drawing conclusions.

---

### 13. What is payload processing?

**Expected Answer**

- Payload processing transforms payload values before they are inserted into requests, allowing reusable payload lists and more flexible experiments.

---

### 14. What makes an Intruder attack "professional"?

**Expected Answer**

- A professional attack has:

  * A clear objective.
  * A baseline request.
  * A focused payload set.
  * An appropriate attack type.
  * Systematic result analysis.
  * Manual verification of interesting responses.

---

### 15. Why shouldn't automation replace manual testing?

**Expected Answer**

- Automation efficiently generates data, but understanding application behavior, validating observations, and drawing conclusions still require manual investigation.

---

## Scenario-Based Questions

### Scenario 1

- You need to observe how sequential resource identifiers behave.

- Question:

  - Which attack type would you choose?

**Expected Answer**

- Sniper, because only one value changes at a time.

---

### Scenario 2

- You notice one response has a much shorter length than the others.

- Question:

  - What should you do next?

**Expected Answer**

- Investigate that response manually in Repeater before making any conclusions.

---

### Scenario 3

- You accidentally configured a very large Cluster Bomb attack.

- Question:

  - What should you do?

**Expected Answer**

- Stop the attack, review the configuration, estimate the request count, and redesign the experiment with a smaller, more focused payload set if appropriate.

---

### Scenario 4

- You don't understand what a request does.

- Question:

  - Should you use Intruder?

**Expected Answer**

- No.

- Understand the request first using Repeater before deciding whether automation is appropriate.

---

## Self-Evaluation

- Can you confidently explain:

  * What Intruder is?
  * Why it exists?
  * When to use it?
  * Which attack type to choose?
  * Why payload design matters?
  * Why Repeater and Intruder work together?

- Any hesitation indicates a topic worth reviewing.

---

## Quick Revision

- Remember these principles:

  * Understand first.
  * Automate second.
  * Analyze third.
  * Verify manually.
  * Document everything.

---

## Mastery Checklist

* [ ] I can explain Intruder confidently.
* [ ] I understand all attack types.
* [ ] I know when automation is appropriate.
* [ ] I can answer scenario-based questions.
* [ ] I am comfortable explaining the professional workflow.
