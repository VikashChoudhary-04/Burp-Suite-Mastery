# Decoder

> *"The Burp Suite tool for inspecting, transforming, encoding, and decoding data during web application security assessments."*

---

## What is Decoder?

- Burp Decoder is a utility tool that helps security testers inspect and transform data into different formats.

- Unlike Repeater or Intruder, Decoder does not send requests to a server.

- Instead, it helps you understand the data that appears inside requests and responses.

- Whether you are examining URL-encoded parameters, Base64 strings, JWT components, hexadecimal values, hashes, or compressed data, Decoder provides a fast and reliable way to inspect and transform that information.

---

## Why Learn Decoder?

- Modern web applications exchange data in many different formats.

- Examples include:

  * URL-encoded parameters
  * Base64 values
  * JSON Web Tokens (JWTs)
  * Hexadecimal data
  * HTML entities
  * Cookies
  * API payloads

- Understanding these formats is essential for analyzing application behavior accurately.

- Decoder allows you to inspect these values without modifying the original request manually.

---

## Learning Objectives

- By the end of this module, you will be able to:

  * Understand the purpose of Burp Decoder.
  * Recognize common encoding formats.
  * Distinguish encoding, decoding, hashing, and encryption.
  * Transform data safely.
  * Inspect JWTs and API data.
  * Integrate Decoder into a professional Burp Suite workflow.

---

## Professional Mindset

- Decoder is not simply an encoding tool.

- It is an investigation tool.

- Its purpose is to help you understand what data actually represents before making assumptions.

- Professional testers use Decoder whenever they encounter unfamiliar or transformed values.

---

## Learning Path

- This module follows the improved Version 2 structure.

| Lesson | Topic                        |
| ------ | ---------------------------- |
| README | Module Overview              |
| 01     | Introduction                 |
| 02     | Why Decoder Exists           |
| 03     | Interface                    |
| 04     | Behind the Scenes            |
| 05     | Mental Models                |
| 06     | Core Concepts                |
| 07     | Professional Workflow        |
| 08     | Real Investigation Scenarios |
| 09     | Performance & Safety         |
| 10     | Field Notes                  |
| 11     | Common Mistakes              |
| 12     | Interview Questions          |
| 13     | Exercises                    |
| 14     | Cheat Sheet                  |
| 15     | Quick Revision               |

---

## Prerequisites

- Before starting this module, you should already understand:

  * HTTP Fundamentals
  * Proxy
  * Target
  * Repeater
  * Intruder

---

## Expected Outcome

- After completing this module, you should be able to:

  * Quickly identify common encodings.
  * Convert data between formats confidently.
  * Analyze encoded application data.
  * Explain the difference between encoding, encryption, and hashing.
  * Use Decoder naturally during penetration testing and bug bounty investigations.

---

## Recommended Learning Strategy

- For each lesson:

  1. Understand the concept.
  2. Practice with Burp Decoder.
  3. Complete the exercises.
  4. Record observations.
  5. Review the Cheat Sheet.
  6. Finish with the Quick Revision page.

- The objective is not to memorize transformations.

- The objective is to recognize data formats quickly and know when Decoder can help.
