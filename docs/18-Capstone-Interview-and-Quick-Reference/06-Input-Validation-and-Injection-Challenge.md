# Input Validation and Injection Challenge

> **Module 18 — Capstone, Interview & Quick Reference**

> **Progress**

```text
██████░░░░░░░░░ 6 / 15 Files
```

---

## Overview

* Web applications continuously accept data from users, browsers, APIs, integrations, files, and other systems.

* Security problems arise when attacker-controlled input reaches a sensitive interpreter or execution context without appropriate validation, encoding, parameterization, or isolation.

* Input validation weaknesses may contribute to vulnerabilities such as:

  * SQL Injection.
  * Cross-Site Scripting.
  * OS Command Injection.
  * Server-Side Template Injection.
  * Path Traversal.
  * Header Injection.
  * CRLF Injection.
  * XML-related injection.
  * NoSQL Injection.
  * LDAP Injection.
  * Unsafe expression evaluation.
  * Other context-specific injection flaws.

* The central testing model is:

```text
Source

↓

Transformation

↓

Sink

↓

Execution Context

↓

Security Impact
```

* Professional injection testing is not:

```text
Send Huge Payload List

↓

Look for Errors
```

* It is:

```text
Understand Input

↓

Identify Context

↓

Establish Baseline

↓

Form Hypothesis

↓

Apply Minimal Mutation

↓

Observe Differential

↓

Validate Safely

↓

Determine Impact
```

---

## Objectives

* By completing this challenge, you should be able to:

  * Build an input inventory.
  * Identify attacker-controlled input sources.
  * Identify likely execution contexts.
  * Distinguish validation from output encoding.
  * Establish clean baselines.
  * Use controlled mutations.
  * Perform differential response analysis.
  * Test SQL injection systematically.
  * Test reflected and stored XSS.
  * Analyze DOM-based client-side flows.
  * Test command injection safely.
  * Identify potential SSTI contexts.
  * Test path traversal.
  * Review header and CRLF injection.
  * Consider structured-data injection.
  * Test alternate encodings and content types.
  * Distinguish anomalies from confirmed vulnerabilities.
  * Validate findings with minimal-impact proofs.
  * Document source-to-sink reasoning.
  * Build a reusable injection-testing workflow.

---

# Challenge Scenario

* You have mapped an authorized application containing:

```text
Search

Login

Profile

Comments

File Download

Report Generation

API Endpoints

Administrative Functions
```

* The application accepts input through:

```text
Query Parameters

Path Parameters

Form Fields

JSON

Headers

Cookies

File Names

Uploaded Content
```

* Your task is to determine whether attacker-controlled input reaches sensitive contexts unsafely.

---

# Challenge Deliverables

* Produce:

```text
Input-Inventory.md

Context-Map.md

Injection-Test-Matrix.md

Validated-Findings/

Evidence/

Input-Validation-and-Injection-Summary.md
```

---

# Phase 1 — Build the Input Inventory

* Begin with every input discovered during application mapping.

---

## Input Sources

```text
URL Path

Query String

Form Body

JSON

XML

Headers

Cookies

Multipart Fields

File Names

File Contents

GraphQL Arguments

WebSocket Messages
```

---

## Input Inventory

| Input    | Location | Endpoint       | Purpose        | Reflected | Stored | Sensitive |
| -------- | -------- | -------------- | -------------- | --------- | ------ | --------- |
| `q`      | Query    | `/search`      | Search term    | Yes       | No     | Yes       |
| `name`   | JSON     | `/api/profile` | Display name   | Yes       | Yes    | Yes       |
| `file`   | Query    | `/download`    | File selection | No        | No     | Yes       |
| `format` | JSON     | `/report`      | Report format  | No        | No     | Maybe     |

---

# Phase 2 — Classify Input Trust

* Determine where each value originates.

---

## Sources

```text
Direct User Input

Browser-Controlled Input

Application-Generated Input

Third-Party Input

Stored Database Data

Uploaded Files

External API Data
```

---

## Important Principle

```text
Application Generated

≠

Automatically Trusted
```

* Data stored earlier may later enter a dangerous context.

---

# Phase 3 — Identify Potential Sinks

* A sink is where input becomes security-sensitive.

---

## Common Sinks

```text
SQL Query

HTML

HTML Attribute

JavaScript

URL

Operating-System Command

Template Engine

File-System Path

HTTP Header

XML Parser

LDAP Query

NoSQL Query

Expression Engine
```

---

# Source-to-Sink Model

```text
User Input

↓

Application Processing

↓

Sensitive Sink

↓

Interpreter
```

* Your task is to determine:

```text
Can Attacker-Controlled Data

Influence the Interpreter's Structure

Instead of Remaining Data?
```

---

# Phase 4 — Build the Context Map

| Input      | Source | Potential Sink  | Context        | Candidate Vulnerability |
| ---------- | ------ | --------------- | -------------- | ----------------------- |
| `q`        | Query  | HTML response   | HTML text      | XSS                     |
| `id`       | Query  | Database        | SQL            | SQLi                    |
| `host`     | JSON   | System utility  | Shell/argument | Command injection       |
| `template` | Form   | Template engine | Template       | SSTI                    |
| `file`     | Query  | File system     | Path           | Traversal               |

---

# Phase 5 — Establish Baselines

* Capture normal behavior before injecting unusual characters.

---

## Record

```text
Request

Response

Status Code

Response Length

Response Time

Visible Output

Application State

Server Errors
```

---

## Example

```text
GET /search?q=alpha
```

* Record the normal response before changing `q`.

---

# Phase 6 — Use Minimal Mutations

* Start with small controlled changes.

---

## Examples

```text
Normal Value

↓

Boundary Character

↓

Context-Specific Character

↓

Controlled Test Expression

↓

Validated Proof
```

---

## Why

* Minimal mutations help isolate cause and effect.

```text
One Variable Changed

↓

One Observable Difference

↓

Stronger Reasoning
```

---

# Phase 7 — Perform Differential Analysis

* Compare:

```text
Baseline

vs

Mutation
```

---

## Observe

* [ ] Status code.
* [ ] Response length.
* [ ] Response body.
* [ ] Error messages.
* [ ] Timing.
* [ ] Redirects.
* [ ] Data returned.
* [ ] State changes.
* [ ] Out-of-band interaction where authorized.

---

# Important Principle

```text
Different Response

≠

Confirmed Injection
```

* A difference creates a hypothesis.

* Validation determines whether the hypothesis is correct.

---

# Phase 8 — SQL Injection Methodology

* SQL injection occurs when attacker-controlled input changes the intended structure or semantics of a database query.

---

## Candidate Inputs

```text
Search

Filters

IDs

Sorting

Login Fields

Report Parameters

API Parameters
```

---

# SQLi Testing Workflow

```text
Identify Input

↓

Capture Baseline

↓

Test Syntax Sensitivity

↓

Observe Differential

↓

Form Database Hypothesis

↓

Use Controlled Confirmation

↓

Determine Impact
```

---

# Phase 9 — Error-Based SQLi Signals

* Unexpected database-related errors may indicate unsafe query construction.

---

## Look For

```text
SQL Syntax Errors

Database Driver Errors

Query Exceptions

Type Conversion Errors

Database-Specific Messages
```

---

## Important

```text
Database Error

≠

Automatically Exploitable SQL Injection
```

* Confirm that your input influences query structure.

---

# Phase 10 — Boolean Differential Testing

* Compare logically different conditions where appropriate.

---

## Concept

```text
Condition Expected True

vs

Condition Expected False

↓

Consistent Response Difference
```

---

## Validate

* Repeat multiple times.

* Confirm:

```text
Same Input Logic

↓

Same Application Behavior
```

* Avoid conclusions based on one anomalous response.

---

# Phase 11 — Time-Based Testing

* Timing can help when visible output does not change.

---

## Model

```text
Baseline Timing

↓

Controlled Delay Hypothesis

↓

Repeated Measurement

↓

Compare
```

---

## Important

* Network latency creates noise.

* Use:

  * Repeated baselines.
  * Repeated test requests.
  * Conservative conclusions.
  * Minimal delays.

---

# Phase 12 — SQLi Impact Analysis

* If SQL injection is validated, determine the minimum evidence needed to establish impact.

---

## Potential Impact

```text
Authentication Bypass

Unauthorized Data Access

Unauthorized Data Modification

Database Metadata Exposure

Privilege Escalation

Potential Backend Compromise
```

* Do not perform destructive database actions merely to increase severity.

---

# Phase 13 — Cross-Site Scripting Methodology

* XSS occurs when attacker-controlled data reaches a browser execution context without appropriate handling.

---

## Main Categories

```text
Reflected XSS

Stored XSS

DOM-Based XSS
```

---

# Phase 14 — Identify Reflection

* Submit a unique marker.

---

## Example Concept

```text
UNIQUE-MARKER-123
```

* Search the response for the exact marker.

---

## Determine Context

```text
HTML Text

HTML Attribute

JavaScript String

URL

CSS

DOM
```

---

# Critical Principle

```text
Reflection

≠

XSS
```

* Exploitability depends on context and browser interpretation.

---

# Phase 15 — Context-Aware XSS Testing

* Determine how the value is inserted.

---

## HTML Text

```text
<div>
USER_INPUT
</div>
```

---

## Attribute

```text
<input value="USER_INPUT">
```

---

## JavaScript

```text
var name = "USER_INPUT";
```

---

## URL

```text
<a href="USER_INPUT">
```

* Each context requires different reasoning.

---

# Phase 16 — Stored XSS Testing

* Stored input may appear in another page, role, or workflow.

---

## Example Flow

```text
User Input

↓

Stored in Database

↓

Viewed Later

↓

Rendered in Browser
```

---

## Test Locations

```text
Profile Fields

Comments

Messages

Tickets

Project Names

File Names

Administrative Views
```

---

## Use Controlled Accounts

* Prefer:

```text
User A

↓

Stores Controlled Marker
```

```text
User B / Admin Test Account

↓

Views Controlled Content
```

* Avoid exposing real users to test payloads.

---

# Phase 17 — DOM-Based Analysis

* Client-side JavaScript may read attacker-controlled data and pass it to dangerous browser sinks.

---

## Sources

```text
location

location.hash

location.search

document.URL

postMessage

localStorage
```

---

## Potential Sinks

```text
innerHTML

outerHTML

document.write

eval

setTimeout With String

Dynamic Script Creation
```

---

# DOM Flow

```text
Browser-Controlled Source

↓

JavaScript Processing

↓

Dangerous DOM Sink

↓

Possible Execution
```

---

# Phase 18 — Command Injection Methodology

* Command injection may occur when application input influences an operating-system command.

---

## Candidate Features

```text
Ping

DNS Lookup

File Conversion

Image Processing

Backup

Archive

Diagnostics

Report Generation

Administrative Utilities
```

---

## Testing Model

```text
Identify Command-Like Feature

↓

Establish Baseline

↓

Test Controlled Syntax Sensitivity

↓

Observe Output / Timing / OAST

↓

Validate Safely
```

---

# Safe Validation Principle

```text
Minimal Proof

>

Destructive Command
```

* Do not:

  * Delete files.
  * Modify system configuration.
  * Establish persistence.
  * Access unrelated sensitive data.

---

# Phase 19 — Distinguish Shell and Argument Injection

* User input may influence:

```text
Shell Command Structure
```

* Or:

```text
Command-Line Arguments
```

* These are related but different security problems.

---

## Ask

```text
Is a Shell Invoked?

Is Input Concatenated?

Is Input Passed as an Argument?

Can Options Be Injected?

Is Allowlisting Applied?
```

---

# Phase 20 — Server-Side Template Injection

* SSTI occurs when attacker-controlled input is interpreted as template syntax rather than treated only as data.

---

## Candidate Features

```text
Email Templates

Report Generation

Document Generation

Custom Notifications

Preview Features

CMS Content

Administrative Templates
```

---

# SSTI Workflow

```text
Identify Template-Like Feature

↓

Submit Plain Marker

↓

Test Harmless Expression

↓

Observe Evaluation

↓

Fingerprint Carefully if Necessary

↓

Validate Minimal Impact
```

---

# Important Principle

```text
Input Appears in Template

≠

Template Injection
```

* Confirm server-side evaluation.

---

# Phase 21 — Path Traversal Methodology

* Path traversal occurs when user-controlled input influences file-system paths without proper restriction.

---

## Candidate Inputs

```text
filename

file

path

document

template

download

image

attachment
```

---

## Candidate Endpoints

```text
/download

/file

/export

/template

/image

/attachment
```

---

# Path Testing Workflow

```text
Known Allowed File

↓

Identify File Reference

↓

Test Controlled Path Mutation

↓

Observe Normalization / Rejection

↓

Verify Boundary Behavior
```

---

## Test Safely

* Prefer application-owned test files and benign known files.

* Avoid unnecessary access to sensitive operating-system or user data.

---

# Phase 22 — Encoded Traversal Variants

* Applications may normalize input at different layers.

---

## Consider

```text
URL Encoding

Double Encoding

Alternate Separators

Path Normalization

Absolute vs Relative Paths
```

---

## Core Question

```text
Where Is Validation Performed

Relative to

Decoding and Normalization?
```

---

# Phase 23 — Header Injection

* User-controlled data may reach HTTP response headers.

---

## Candidate Inputs

```text
Redirect

Filename

Download Name

Language

Host-Related Values

Custom Header Values
```

---

## Observe

```text
Location

Content-Disposition

Set-Cookie

Custom Headers
```

---

# Phase 24 — CRLF Injection

* CRLF injection involves unsafe handling of line-break characters in contexts such as HTTP headers.

---

## Testing Goal

```text
Can User Input

Alter Header Structure

Instead of Remaining One Header Value?
```

---

## Validate

* Inspect the raw response.

* Determine whether:

```text
New Header Created?

Existing Header Altered?

Response Structure Changed?
```

---

# Phase 25 — Structured-Data Injection

* Applications may build queries or expressions for systems other than SQL databases.

---

## Examples

```text
NoSQL

LDAP

XPath

GraphQL Filters

Search Engines

Expression Languages
```

---

## Method

```text
Understand Backend Context

↓

Identify Operators / Structure

↓

Test Controlled Structural Mutation

↓

Observe Semantic Difference

↓

Validate
```

---

# Core Principle

```text
Do Not Treat Every Backend

As SQL
```

* Injection methodology should match the interpreter.

---

# Phase 26 — Test JSON Inputs

* JSON APIs frequently expose security-sensitive properties.

---

## Test

```text
String Values

Numeric Values

Boolean Values

Null

Arrays

Objects

Unexpected Types

Additional Properties
```

---

## Ask

```text
Does Type Change Behavior?

Are Unexpected Properties Accepted?

Does Validation Differ From the UI?

Does Backend Coercion Create Security Issues?
```

---

# Phase 27 — Test Content-Type Variations

* Backend parsing may change depending on `Content-Type`.

---

## Examples

```text
application/json

application/x-www-form-urlencoded

multipart/form-data

text/plain

application/xml
```

---

## Ask

```text
Does Alternate Parsing Reach Different Validation?

Are Security Controls Consistent?
```

---

# Phase 28 — Test Duplicate Parameters

* Different components may interpret duplicate parameters differently.

---

## Concept

```text
parameter=A

parameter=B
```

* Ask:

```text
Which Value Does the Proxy Use?

Which Value Does the Framework Use?

Which Value Does Validation Inspect?

Which Value Does Business Logic Use?
```

---

# Phase 29 — Test Parameter Location Changes

* Some applications accept the same value from multiple locations.

---

## Compare

```text
Query

Body

Header

Cookie
```

* Determine whether validation and authorization remain consistent.

---

# Phase 30 — Test Encoding and Canonicalization

* Validation may occur before or after decoding.

---

## Consider

```text
URL Encoding

Unicode

Double Encoding

Case Differences

Whitespace

Normalization

Escaping
```

---

# Canonicalization Problem

```text
Validator Sees A

↓

Decoder Converts to B

↓

Interpreter Processes B
```

* Secure validation should account for the final interpreted form.

---

# Phase 31 — Test Server-Side and Client-Side Validation Separately

* Browser validation improves usability.

* It is not a security boundary.

---

## Workflow

```text
UI Rejects Input

↓

Capture Request

↓

Modify in Burp Repeater

↓

Send Directly to Server

↓

Observe Server Validation
```

---

# Core Rule

```text
Client-Side Validation

≠

Server-Side Security Control
```

---

# Phase 32 — Analyze Error Handling

* Errors may reveal useful information.

---

## Look For

```text
Database Names

Query Fragments

File Paths

Framework Details

Template Engines

Command Errors

Stack Traces

Internal Hostnames
```

---

## Important

```text
Verbose Error

May Be

Information Disclosure

Without Being Injection
```

* Classify accurately.

---

# Phase 33 — Use Burp Repeater Systematically

* Organize requests by context.

---

## Example

```text
INJECTION/
├── SQL/
├── XSS/
├── COMMAND/
├── SSTI/
├── PATH/
└── HEADERS/
```

---

## Naming

```text
SQL-01-Baseline

SQL-02-Syntax-Test

SQL-03-Control

XSS-01-Reflection

XSS-02-Context

PATH-01-Baseline

PATH-02-Normalization
```

---

# Controlled Mutation Workflow

```text
Baseline

↓

Change One Input

↓

Send

↓

Compare

↓

Revert

↓

Repeat

↓

Validate
```

---

# Phase 34 — Use Burp Intruder Carefully

* Intruder can help test controlled input sets.

---

## Appropriate Uses

```text
Small Context-Specific Payload Sets

Encoding Comparisons

Boundary Testing

Parameter Variation

Response Comparison
```

---

## Avoid

```text
Blind Massive Payload Lists

Uncontrolled Request Volume

Out-of-Scope Targets

Destructive Payloads
```

---

# Phase 35 — Build the Injection Test Matrix

| Input  | Context       | Baseline      | Mutation          | Signal                  | Status      |
| ------ | ------------- | ------------- | ----------------- | ----------------------- | ----------- |
| `q`    | HTML          | Normal search | Marker            | Reflected               | Investigate |
| `id`   | SQL candidate | Object 10     | Controlled syntax | Error difference        | Investigate |
| `file` | Path          | `report.pdf`  | Path mutation     | Different file behavior | Investigate |

---

# Phase 36 — Distinguish Signal From Vulnerability

* A signal may include:

```text
Error

Timing Difference

Reflection

Status Change

Length Change

Unexpected Data
```

* A vulnerability requires:

```text
Controlled Input

↓

Security Boundary Failure

↓

Reproducible Effect

↓

Meaningful Impact
```

---

# Phase 37 — Validate With Controls

* Use:

```text
Positive Control

Negative Control

Repeated Test

Baseline Restoration
```

---

## Example

```text
Baseline

↓

Candidate Mutation

↓

Control Mutation

↓

Candidate Mutation Again
```

* If only the candidate produces the expected effect consistently, confidence increases.

---

# Phase 38 — Determine Impact

* Do not stop at:

```text
Payload Worked
```

* Ask:

```text
What Security Boundary Failed?

What Can an Attacker Actually Achieve?

What Data or Action Is Affected?

Which Users Are Impacted?

What Privileges Are Required?
```

---

# Phase 39 — Identify Root Cause

* Common root causes include:

```text
Dynamic Query Concatenation

Missing Parameterization

Context-Inappropriate Encoding

Unsafe Shell Invocation

Unsafe Template Evaluation

Weak Path Canonicalization

Header Construction From Untrusted Data

Inconsistent Server-Side Validation

Unsafe Type Coercion
```

---

# Phase 40 — Collect Evidence

* Preserve:

```text
Baseline Request

Baseline Response

Test Request

Test Response

Input Location

Execution Context

Reproduction Steps

Security Effect

Final Impact
```

---

## Evidence Should Show

```text
Source

↓

Sink

↓

Controlled Influence

↓

Security Consequence
```

---

# Capstone Challenge

## Stage 1 — Input Inventory

* Identify at least:

```text
20 Inputs
```

* Across:

  * Query parameters.
  * Paths.
  * Forms.
  * JSON.
  * Headers.
  * Cookies.
  * Files.

---

## Stage 2 — Context Mapping

* Classify each interesting input into potential contexts.

```text
SQL

HTML

JavaScript

Command

Template

Path

Header

Structured Query
```

---

## Stage 3 — Baselines

* Capture clean baseline requests for every high-priority input.

---

## Stage 4 — Controlled Testing

* For each candidate:

```text
Baseline

↓

Minimal Mutation

↓

Differential Analysis

↓

Context-Specific Test

↓

Validation
```

---

## Stage 5 — SQLi

* Test appropriate database-facing candidates using controlled differential techniques.

---

## Stage 6 — XSS

* Test:

```text
Reflection

Context

Stored Rendering

DOM Sources and Sinks
```

---

## Stage 7 — Server-Side Injection

* Review:

```text
Command-Like Features

Template Features

Report Generation

Conversion Features
```

---

## Stage 8 — Path and Header Testing

* Review:

```text
Downloads

File Selection

Templates

Redirects

Response Headers

File Names
```

---

## Stage 9 — Validation

* Every candidate must be classified as:

```text
Confirmed

Rejected

Needs More Evidence
```

---

# Challenge Success Criteria

* You should be able to answer:

```text
Where Does User Input Enter?

Where Does It Go?

Which Interpreter Processes It?

What Context Is Used?

What Validation Occurs?

What Encoding Occurs?

Can Input Alter Structure?

Is the Effect Reproducible?

What Security Boundary Fails?

What Is the Real Impact?
```

---

# Injection Testing Workflow

```text
Map Inputs

↓

Identify Contexts

↓

Prioritize Sinks

↓

Capture Baselines

↓

Apply Minimal Mutations

↓

Compare Responses

↓

Form Hypothesis

↓

Use Context-Specific Tests

↓

Validate With Controls

↓

Determine Impact

↓

Identify Root Cause

↓

Collect Evidence

↓

Report
```

---

# Common Mistakes

* Sending payloads before understanding the input context.
* Using enormous payload lists immediately.
* Testing every parameter identically.
* Treating every error as injection.
* Treating reflection as confirmed XSS.
* Ignoring stored inputs.
* Ignoring DOM-based flows.
* Ignoring headers and cookies as input sources.
* Ignoring JSON properties.
* Ignoring GraphQL arguments.
* Ignoring file names.
* Ignoring alternate content types.
* Ignoring duplicate parameters.
* Ignoring encoding and canonicalization.
* Trusting client-side validation.
* Assuming a WAF rejection proves a vulnerability.
* Relying on one timing result.
* Performing destructive database tests.
* Executing dangerous operating-system commands.
* Accessing unnecessary sensitive files during traversal testing.
* Using real users for stored XSS testing.
* Failing to restore modified test data.
* Failing to use positive and negative controls.
* Reporting a payload instead of explaining the failed security boundary.
* Failing to identify the actual sink.
* Failing to determine real-world impact.

---

# Professional Tips

* Build an input inventory before fuzzing.
* Think in terms of sources, transformations, sinks, and contexts.
* Prioritize inputs likely to reach interpreters.
* Capture a clean baseline first.
* Change one variable at a time.
* Use unique markers for reflection testing.
* Identify exact output context before XSS testing.
* Use controlled accounts for stored-input testing.
* Separate browser execution from simple reflection.
* Use repeated measurements for timing-based tests.
* Keep delay-based validation minimal.
* Distinguish SQL syntax errors from confirmed query manipulation.
* Test server-side validation directly with Repeater.
* Review APIs separately from UI restrictions.
* Test JSON types and additional properties.
* Check alternate parsers and content types.
* Consider decoding and normalization order.
* Use small, context-specific Intruder payload sets.
* Avoid destructive proofs.
* Validate suspicious behavior with controls.
* Verify persistent state where injection causes modifications.
* Explain source-to-sink flow in reports.
* Identify the failed defense: parameterization, encoding, validation, isolation, or canonicalization.
* Demonstrate only the minimum impact necessary to prove severity.

---

# Practice Tasks

1. Select an authorized practice application.

2. Build an inventory of at least 20 inputs.

3. Record each input location.

4. Identify reflected inputs.

5. Identify stored inputs.

6. Identify security-sensitive inputs.

7. Build a context map.

8. Identify possible database-facing parameters.

9. Identify HTML rendering contexts.

10. Identify JavaScript contexts.

11. Identify command-like functionality.

12. Identify template functionality.

13. Identify file-system path inputs.

14. Identify header-related inputs.

15. Capture clean baselines.

16. Apply one controlled mutation at a time.

17. Compare status codes.

18. Compare response lengths.

19. Compare response bodies.

20. Compare timing where relevant.

21. Test SQLi candidates systematically.

22. Use repeated controls for boolean or timing differences.

23. Test reflected XSS candidates with unique markers.

24. Determine exact reflection context.

25. Test stored rendering using controlled accounts.

26. Review JavaScript sources and sinks.

27. Test command-like features safely.

28. Test template-like features with harmless expressions.

29. Test file-path normalization.

30. Test encoded path variants where appropriate.

31. Inspect response headers for reflected values.

32. Test CRLF handling safely.

33. Review structured-data query inputs.

34. Test JSON type variations.

35. Test additional JSON properties.

36. Test alternate content types where supported.

37. Test duplicate parameter handling.

38. Test parameter-location changes where relevant.

39. Test encoding and canonicalization.

40. Bypass client-side validation using Repeater.

41. Record verbose errors separately.

42. Organize Repeater tabs by injection class.

43. Build an injection test matrix.

44. Classify every candidate as confirmed, rejected, or unresolved.

45. Validate confirmed findings with controls.

46. Determine actual security impact.

47. Identify root cause.

48. Collect minimal sufficient evidence.

49. Restore any modified test state.

50. Write the final injection-testing summary.

---

# Interview Questions

1. What is input validation?
2. What is injection?
3. What is the difference between validation and output encoding?
4. What is a source in injection analysis?
5. What is a sink?
6. Why is execution context important?
7. How do you build an input inventory?
8. Which HTTP locations can contain attacker-controlled input?
9. Why should you capture a baseline before testing?
10. What is differential analysis?
11. Why does a different response not automatically prove injection?
12. How do you test SQL injection systematically?
13. What does a database error tell you?
14. How do boolean-based SQL injection tests work conceptually?
15. How do you reduce false positives in time-based testing?
16. How do you determine SQL injection impact safely?
17. What is reflected XSS?
18. What is stored XSS?
19. What is DOM-based XSS?
20. Why does reflection not automatically mean XSS?
21. Why must XSS payloads be context aware?
22. How do you test stored XSS safely?
23. What are DOM sources and sinks?
24. How do you identify command-injection candidates?
25. What is the difference between shell injection and argument injection?
26. How do you validate command injection safely?
27. What is SSTI?
28. How do you distinguish template reflection from template evaluation?
29. What is path traversal?
30. Why does path normalization matter?
31. How can encoding affect traversal testing?
32. What is header injection?
33. What is CRLF injection?
34. How do you validate CRLF injection?
35. What other query languages may be vulnerable to injection besides SQL?
36. Why should JSON type variations be tested?
37. Why should alternate content types be tested?
38. What security problems can duplicate parameters cause?
39. What is canonicalization?
40. Why is client-side validation not a security boundary?
41. How do you use Burp Repeater for injection testing?
42. When should Burp Intruder be used?
43. Why are massive payload lists often inefficient?
44. How do you distinguish a signal from a vulnerability?
45. What controls should be used when validating injection?
46. What evidence should an injection report contain?
47. What are common injection root causes?
48. How do you minimize impact during validation?
49. How do you prioritize injection candidates?
50. Walk me through your complete input-validation and injection-testing methodology.

---

# Quick Revision

* Core model:

```text
Source

↓

Transformation

↓

Sink

↓

Interpreter

↓

Impact
```

* Testing:

```text
Baseline

↓

Minimal Mutation

↓

Differential

↓

Hypothesis

↓

Context-Specific Validation
```

* SQLi:

```text
Input

↓

Database Query

↓

Can Input Alter Query Semantics?
```

* XSS:

```text
Input

↓

Browser Context

↓

Can Input Become Executable?
```

* Command injection:

```text
Input

↓

OS Command / Argument

↓

Can Input Alter Execution?
```

* SSTI:

```text
Input

↓

Template Engine

↓

Is Input Evaluated?
```

* Path traversal:

```text
Input

↓

File Path

↓

Can Intended Directory Boundary Be Escaped?
```

* Validation rule:

```text
Interesting Response

≠

Confirmed Vulnerability
```

* Confirmation:

```text
Reproducible

+

Controlled

+

Context Explained

+

Security Impact
```

---

# Input Validation & Injection Quick Checklist

```text
INPUTS

[ ] Query
[ ] Path
[ ] Form
[ ] JSON
[ ] XML
[ ] Headers
[ ] Cookies
[ ] Multipart
[ ] Files
[ ] GraphQL
[ ] WebSockets


CONTEXTS

[ ] SQL
[ ] HTML
[ ] Attribute
[ ] JavaScript
[ ] URL
[ ] Command
[ ] Template
[ ] File path
[ ] Header
[ ] Structured query


METHOD

[ ] Baseline
[ ] One-variable mutation
[ ] Differential analysis
[ ] Context identification
[ ] Positive control
[ ] Negative control
[ ] Repetition


SQLI

[ ] Syntax sensitivity
[ ] Error behavior
[ ] Boolean differential
[ ] Timing if appropriate
[ ] Safe impact validation


XSS

[ ] Reflection
[ ] Exact context
[ ] Stored rendering
[ ] Controlled viewers
[ ] DOM sources
[ ] DOM sinks


SERVER-SIDE

[ ] Command-like functions
[ ] Argument handling
[ ] Template evaluation
[ ] Safe proof


FILES / HEADERS

[ ] Path parameters
[ ] Normalization
[ ] Encoding
[ ] Response headers
[ ] CRLF handling


PARSING

[ ] JSON types
[ ] Additional properties
[ ] Content types
[ ] Duplicate parameters
[ ] Parameter locations
[ ] Canonicalization


VALIDATION

[ ] Reproducible
[ ] Security boundary identified
[ ] Impact demonstrated
[ ] Root cause identified
[ ] Minimal evidence
[ ] Test state restored
```

---

# Key Takeaways

* Injection testing begins by understanding where attacker-controlled input enters the application and where it is ultimately interpreted.
* The core model is **source → transformation → sink → execution context → security impact**.
* Input inventories should include paths, queries, forms, JSON, XML, headers, cookies, multipart fields, files, GraphQL arguments, and WebSocket messages.
* Stored or application-generated data should not automatically be considered trusted.
* Potential sinks include databases, browsers, operating-system commands, template engines, file systems, HTTP headers, structured queries, and expression interpreters.
* A clean baseline is essential for meaningful differential analysis.
* Minimal one-variable mutations usually produce stronger evidence than immediate large-scale fuzzing.
* A changed response is a signal that deserves investigation, not proof of a vulnerability.
* SQL injection testing should establish whether attacker-controlled input can alter database-query semantics.
* Database errors may reveal useful information but do not automatically prove exploitable SQL injection.
* Boolean and timing tests require repeated controls to reduce false positives.
* XSS testing requires exact understanding of the browser rendering or execution context.
* Reflection alone does not prove XSS.
* Stored XSS should be tested with controlled identities so real users are not exposed to test payloads.
* DOM-based testing requires tracing browser-controlled sources into security-sensitive JavaScript sinks.
* Command injection testing should identify whether input influences shell syntax, command arguments, or both.
* Server-side injection should be validated with minimal-impact proofs rather than destructive commands.
* SSTI requires proof that input is evaluated by the server-side template engine rather than merely reflected.
* Path traversal testing should consider decoding, normalization, relative paths, absolute paths, and boundary enforcement.
* Header and CRLF testing should be verified using raw HTTP responses.
* Injection concepts extend beyond SQL to NoSQL, LDAP, XPath, search queries, GraphQL filters, and expression languages.
* JSON APIs should be tested for type handling, unexpected properties, and backend coercion.
* Alternate content types and duplicate parameters may expose inconsistent parsing or validation.
* Canonicalization problems occur when validation and interpretation operate on different representations of the same input.
* Client-side validation improves usability but cannot replace server-side enforcement.
* Burp Repeater is ideal for controlled, hypothesis-driven input testing.
* Burp Intruder is most useful with small, targeted, context-specific payload sets rather than uncontrolled fuzzing.
* Strong validation requires reproducibility, controls, context understanding, a demonstrated security boundary failure, and meaningful impact.
* Reports should explain the vulnerable source-to-sink path and root cause rather than merely listing a successful payload.
* The complete methodology is **map inputs → identify contexts and sinks → prioritize candidates → capture baselines → apply minimal mutations → analyze differentials → form hypotheses → perform context-specific testing → validate with controls → determine impact → identify root cause → preserve evidence → report**.
