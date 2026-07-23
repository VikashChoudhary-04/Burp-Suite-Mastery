# Proxy Tool

> **Module 05 of the Burp Suite Mastery Guide**

---

## Overview

- The Proxy tool is the foundation of Burp Suite's traffic-interception workflow.

- It operates between the client and the target application, allowing HTTP, HTTPS, and WebSocket traffic to be observed, intercepted, modified, forwarded, dropped, and analyzed.

- This module develops the practical skills required to use Burp Proxy as part of a structured web application security assessment.

---

## Learning Objectives

- By the end of this module, you will be able to:

  * Understand Burp Proxy's role in the request-response lifecycle.
  * Understand the Proxy interface and interception architecture.
  * Intercept, inspect, modify, forward, and drop requests.
  * Understand response interception and modification.
  * Analyze application traffic using HTTP history.
  * Inspect WebSocket communication using WebSockets history.
  * Configure proxy listeners and relevant Proxy settings.
  * Use interception rules and Match and Replace appropriately.
  * Build efficient Proxy-based investigation workflows.
  * Troubleshoot common interception and connectivity problems.
  * Apply Proxy safely during authorized penetration tests and bug bounty assessments.

---

## Prerequisites

- Before beginning this module, you should understand:

  * Basic HTTP requests and responses.
  * HTTP methods, headers, parameters, cookies, and status codes.
  * HTTPS and TLS at a conceptual level.
  * How Burp Suite acts as an intercepting proxy.
  * Browser proxy configuration.
  * Burp's CA certificate and HTTPS interception.

- Modules **01–04** should ideally be completed first.

---

## Module Structure

```text
05-Proxy/
│
├── README.md
├── 01-Introduction.md
├── 02-Interface-and-Proxy-Architecture.md
├── 03-Intercepting-Requests.md
├── 04-Modifying-and-Forwarding-Requests.md
├── 05-HTTP-History-and-Traffic-Analysis.md
├── 06-Response-Interception-and-Modification.md
├── 07-Match-and-Replace.md
├── 08-Proxy-Listeners-and-Settings.md
├── 09-Request-and-Response-Interception-Rules.md
├── 10-Professional-Proxy-Workflow.md
├── 11-Common-Mistakes.md
├── 12-Practical-Exercises.md
├── 13-Interview-Questions.md
└── 14-Quick-Revision.md
```

---

## Learning Path

```text
Introduction

↓

Interface & Proxy Architecture

↓

Intercepting Requests

↓

Modifying & Forwarding Requests

↓

Intercepting & Modifying Responses

↓

HTTP History & Traffic Analysis

↓

WebSocket History

↓

Proxy Listeners & Settings

↓

Interception Rules & Match and Replace

↓

Professional Proxy Workflow

↓

Common Mistakes & Troubleshooting

↓

Practical Exercises

↓

Interview Questions

↓

Quick Revision
```

---

## Core Mental Model

```text
Client / Browser
      │
      ▼
  Burp Proxy
      │
      ▼
Target Application
```

- Burp Proxy sits in the communication path between the client and server.

- This position allows the tester to observe and control traffic before continuing the normal request-response flow.

```text
Browser
   │
   │ Request
   ▼
Burp Proxy
   │
   │ Inspect / Modify / Forward / Drop
   ▼
Server
   │
   │ Response
   ▼
Burp Proxy
   │
   │ Inspect / Analyze / Modify
   ▼
Browser
```

---

## Core Proxy Capabilities

- The Proxy workflow includes several closely related capabilities:

  * Request interception.
  * Request inspection and modification.
  * Request forwarding and dropping.
  * Response interception and modification.
  * HTTP history analysis.
  * WebSocket message inspection.
  * Proxy listener configuration.
  * Interception rules.
  * Match and Replace.
  * Integration with other Burp Suite tools.

---

## Intercept

- Intercept allows selected traffic to pause inside Burp before continuing.

- When interception is enabled, a tester can:

  * Inspect the complete request.
  * Modify parameters.
  * Modify headers.
  * Modify cookies.
  * Remove or add request data.
  * Forward the request.
  * Drop the request.
  * Send the request to other Burp tools.

- Interception should be used deliberately.

- During general application browsing, HTTP history is often more efficient than stopping every request.

---

## HTTP History

- HTTP history provides a record of proxied HTTP traffic.

- It allows testers to review application behavior without manually intercepting every request.

- Useful information may include:

  * Host.
  * HTTP method.
  * URL and path.
  * Parameters.
  * Status code.
  * MIME type.
  * Response length.
  * Request and response contents.

- During professional testing, HTTP history frequently becomes the starting point for identifying requests worth deeper investigation.

---

## WebSocket History

- Modern applications may use WebSockets for persistent bidirectional communication.

- WebSockets history helps testers inspect messages exchanged after the initial WebSocket handshake.

- WebSocket traffic may expose:

  * Authentication behavior.
  * Authorization decisions.
  * Real-time application actions.
  * Object identifiers.
  * Subscription mechanisms.
  * Client-controlled message fields.

- WebSocket testing is explored further in later modules.

---

## Proxy Configuration

- Proxy behavior depends on correctly configured listeners and client routing.

- A common local configuration is:

  ```text
  Address: 127.0.0.1
  Port:    8080
  ```

- The exact configuration may vary depending on:

  * Testing environment.
  * Browser configuration.
  * Mobile-device testing.
  * Virtual machines.
  * Containers.
  * Remote clients.
  * Network architecture.

- Listener exposure should always be configured deliberately and safely.

---

## Professional Proxy Workflow

```text
Confirm Authorization and Scope

↓

Configure Browser and Proxy

↓

Browse the Application Normally

↓

Observe HTTP History

↓

Identify Security-Relevant Requests

↓

Establish a Baseline

↓

Send Interesting Requests to Appropriate Burp Tools

↓

Modify One Relevant Variable at a Time

↓

Compare Behavior

↓

Verify Application State

↓

Preserve Evidence
```

- Proxy is primarily the observation and traffic-control layer.

- Deeper manual experimentation is often performed using tools such as Repeater, Intruder, Comparer, Scanner, or specialized extensions.

---

## Example Investigation Flow

```text
User Performs an Application Action
        │
        ▼
Request Appears in Proxy
        │
        ▼
Inspect Request Structure
        │
        ▼
Identify Security-Relevant Inputs
        │
        ▼
Review Authentication and Session Context
        │
        ▼
Send Request to Repeater
        │
        ▼
Establish Baseline
        │
        ▼
Perform Controlled Modification
        │
        ▼
Compare Result
        │
        ▼
Verify Final Application State
        │
        ▼
Document Evidence
```

---

## Professional Principles

- Use Proxy to understand application behavior before attempting security tests.

- Prefer controlled testing over random modification.

- Keep the authorized scope visible and verified.

- Avoid changing multiple variables simultaneously when investigating behavior.

- Treat HTTP history as evidence, but verify important findings independently.

- Distinguish unusual behavior from an actual security boundary violation.

- Preserve reproducible requests and responses for validated findings.

- Never test systems without explicit authorization.

---

## Common Mistakes

- Common mistakes include:

  * Leaving Intercept enabled during normal browsing and assuming the application is broken.
  * Testing before verifying scope.
  * Ignoring HTTP history.
  * Modifying several parameters simultaneously.
  * Failing to establish a legitimate baseline.
  * Assuming a changed response automatically proves a vulnerability.
  * Ignoring redirects or asynchronous requests.
  * Overlooking WebSocket traffic.
  * Misconfiguring proxy listeners.
  * Confusing browser, TLS, DNS, and proxy problems.
  * Failing to verify final application state after a state-changing request.

---

## Module Goal

- The goal of this module is not simply to teach how to turn interception on and off.

- The goal is to develop a disciplined traffic-analysis workflow:

  ```text
  Observe

  ↓

  Understand

  ↓

  Select

  ↓

  Intercept When Necessary

  ↓

  Modify Carefully

  ↓

  Compare

  ↓

  Validate

  ↓

  Document
  ```

- By the end of the module, Proxy should function as the foundation of a repeatable Burp Suite investigation workflow rather than merely as a request-interception feature.
