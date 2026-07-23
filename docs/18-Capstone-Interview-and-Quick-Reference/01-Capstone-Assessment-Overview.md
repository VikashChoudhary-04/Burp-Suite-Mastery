# Capstone Assessment Overview

> **Module 18 — Capstone, Interview & Quick Reference**

> **Progress**

```text
█░░░░░░░░░░░░░░ 1 / 15 Files
```

---

## Overview

* The capstone assessment is the final practical stage of the Burp Suite Mastery learning path.

* Previous modules developed individual capabilities such as:

  * Understanding HTTP.
  * Intercepting and modifying traffic.
  * Mapping applications.
  * Using Repeater for controlled testing.
  * Using Intruder for structured automation.
  * Encoding and decoding data.
  * Comparing responses.
  * Analyzing tokens.
  * Testing authentication and authorization.
  * Investigating APIs.
  * Identifying business-logic weaknesses.
  * Validating vulnerabilities.
  * Collecting evidence.
  * Writing professional reports.
  * Retesting remediation.

* The capstone combines these skills into one complete assessment.

```text
Scope

↓

Application Mapping

↓

Attack Surface Analysis

↓

Hypothesis Generation

↓

Manual Testing

↓

Vulnerability Validation

↓

Evidence Collection

↓

Professional Reporting

↓

Retesting

↓

Final Assessment Review
```

* The objective is not to find the largest possible number of vulnerabilities.

* The objective is to demonstrate that you can conduct a structured, safe, evidence-based security assessment from beginning to end.

---

## Objectives

* By completing the capstone, you should be able to:

  * Prepare a professional assessment workspace.
  * Define and follow testing scope.
  * Configure Burp Suite for an assessment.
  * Map an unfamiliar web application.
  * Build an endpoint inventory.
  * Identify roles, objects, and trust boundaries.
  * Prioritize high-value attack surfaces.
  * Generate security hypotheses.
  * Test hypotheses systematically.
  * Use Burp Suite tools appropriately.
  * Test authentication and sessions.
  * Test authorization across multiple users.
  * Investigate APIs.
  * Analyze business logic.
  * Test input-processing boundaries.
  * Investigate file and server-side functionality.
  * Validate vulnerabilities.
  * Eliminate false positives.
  * Demonstrate impact safely.
  * Preserve professional evidence.
  * Write complete vulnerability reports.
  * Perform remediation retesting.
  * Evaluate your testing coverage.
  * Explain your methodology during interviews.

---

# Capstone Philosophy

* The capstone should simulate the reasoning process of a real penetration test.

* Avoid:

```text
Open Application

↓

Run Scanner

↓

Find Alerts

↓

Submit Results
```

* Follow:

```text
Understand Application

↓

Build Security Model

↓

Identify Trust Boundaries

↓

Generate Hypotheses

↓

Perform Controlled Tests

↓

Validate Findings

↓

Document Evidence

↓

Report Professionally
```

---

# Recommended Capstone Environment

* Perform the assessment only against an application you are explicitly authorized to test.

* Suitable environments include:

  * OWASP Juice Shop.
  * PortSwigger Web Security Academy labs.
  * DVWA.
  * bWAPP.
  * WebGoat.
  * Purpose-built local vulnerable applications.
  * Your own intentionally vulnerable application.
  * Authorized bug bounty assets when program rules permit the planned testing.

* A local or dedicated practice environment is recommended when performing the complete capstone because it allows broader experimentation without affecting real users.

---

# Capstone Rules

## Rule 1 — Stay Within Scope

```text
In Scope

→

Test
```

```text
Out of Scope

→

Do Not Test
```

---

## Rule 2 — Use Controlled Accounts

* Where multiple users are required:

```text
User A

User B

Administrator
```

* Use accounts you control whenever possible.

---

## Rule 3 — Minimize Impact

```text
Minimum Evidence

>

Maximum Exploitation
```

* Stop once sufficient evidence exists.

---

## Rule 4 — Document as You Test

* Do not wait until the end of the assessment to reconstruct your work.

```text
Test

↓

Observe

↓

Record

↓

Preserve Evidence
```

---

## Rule 5 — Validate Before Reporting

```text
Scanner Alert

≠

Confirmed Vulnerability
```

```text
Interesting Response

≠

Confirmed Vulnerability
```

```text
Validated Security Boundary Failure

+

Demonstrated Impact

=

Reportable Finding
```

---

# Capstone Scenario

* Assume you have been assigned to assess a modern web application containing:

  ```text
  Public Pages

  User Registration

  Authentication

  User Profiles

  Multiple User Roles

  Projects or Other User-Owned Objects

  Search

  File Upload

  API Endpoints

  Administrative Functions

  Import / Export Features

  Invitations or Sharing

  Business Workflows
  ```

* Your task is to determine whether the application correctly protects:

  ```text
  Identity

  Sessions

  Permissions

  Sensitive Data

  User-Owned Objects

  Tenant Boundaries

  Business Rules

  Server-Side Operations
  ```

---

# Capstone Deliverables

* By the end of the assessment, produce:

```text
01-Scope-and-Rules.md

02-Application-Map.md

03-Endpoint-Inventory.md

04-Role-and-Permission-Matrix.md

05-Trust-Boundary-Map.md

06-Hypothesis-Tracker.md

07-Testing-Coverage.md

08-Validated-Findings/

09-Evidence/

10-Final-Report.md

11-Retest-Notes.md

12-Lessons-Learned.md
```

---

# Suggested Workspace

```text
capstone/
│
├── 01-scope/
│   └── scope-and-rules.md
│
├── 02-mapping/
│   ├── application-map.md
│   ├── endpoint-inventory.md
│   ├── role-matrix.md
│   └── trust-boundaries.md
│
├── 03-hypotheses/
│   └── hypothesis-tracker.md
│
├── 04-testing/
│   ├── authentication.md
│   ├── authorization.md
│   ├── api.md
│   ├── business-logic.md
│   └── input-testing.md
│
├── 05-findings/
│
├── 06-evidence/
│
├── 07-reports/
│   └── final-report.md
│
├── 08-retest/
│   └── retest-notes.md
│
└── 09-review/
    └── lessons-learned.md
```

---

# Phase 1 — Define Scope

* Record exactly what may be tested.

---

## Scope Template

```text
Assessment Name:

Application:

Environment:

Start Date:

End Date:

Authorized Assets:

Excluded Assets:

Test Accounts:

Allowed Techniques:

Restricted Techniques:

Automation Rules:

Rate Limits:

Data Handling Rules:

Notes:
```

---

## Scope Checklist

* [ ] Target confirmed.
* [ ] Environment confirmed.
* [ ] Domains recorded.
* [ ] APIs recorded.
* [ ] Out-of-scope assets recorded.
* [ ] Test accounts prepared.
* [ ] Restricted techniques understood.
* [ ] Automation limits understood.
* [ ] Data-handling rules understood.

---

# Phase 2 — Configure Burp Suite

* Prepare Burp Suite before application testing.

---

## Configure

* [ ] Create dedicated Burp project.
* [ ] Configure browser proxy.
* [ ] Confirm HTTPS interception.
* [ ] Configure target scope.
* [ ] Filter irrelevant traffic.
* [ ] Configure logging where appropriate.
* [ ] Prepare Repeater tabs.
* [ ] Configure Collaborator if required and authorized.
* [ ] Confirm Intruder resource usage is appropriate.
* [ ] Verify no unintended targets are included.

---

# Phase 3 — Browse Normally

* Use the application as a normal user before manipulating requests.

---

## Explore

```text
Registration

Login

Profile

Settings

Search

Objects

Files

Sharing

Invitations

Billing

Administration

APIs

Logout
```

---

## Goal

* Understand:

```text
What the Application Does

Who Uses It

What Data Exists

What Actions Matter

What Security Boundaries Exist
```

---

# Phase 4 — Build the Application Map

* Record major features.

---

## Example

```text
Authentication
├── Register
├── Login
├── Logout
└── Password Reset

Account
├── Profile
├── Settings
└── Security

Projects
├── Create
├── View
├── Edit
├── Delete
├── Share
└── Export

Administration
├── Users
├── Roles
└── Configuration
```

---

# Phase 5 — Build the Endpoint Inventory

* Use:

  * Proxy history.
  * Target site map.
  * JavaScript analysis.
  * Normal application browsing.
  * API documentation where available.
  * Content discovery where authorized.

---

## Endpoint Template

| Method | Endpoint             | Auth | Role   | Parameters  | Object  | Sensitive |
| ------ | -------------------- | ---- | ------ | ----------- | ------- | --------- |
| POST   | `/login`             | No   | Public | Credentials | User    | Yes       |
| GET    | `/api/projects/{id}` | Yes  | Member | ID          | Project | Yes       |
| PATCH  | `/api/projects/{id}` | Yes  | Owner  | JSON        | Project | Yes       |

---

# Phase 6 — Identify Roles

* Record every role discovered.

---

## Example

```text
Unauthenticated

Standard User

Premium User

Organization Member

Organization Administrator

Application Administrator
```

---

# Role Matrix

| Function            | Public | User  | Org Admin | App Admin |
| ------------------- | ------ | ----- | --------- | --------- |
| View public content | Allow  | Allow | Allow     | Allow     |
| View own profile    | Deny   | Allow | Allow     | Allow     |
| Manage organization | Deny   | Deny  | Allow     | Allow     |
| Manage all users    | Deny   | Deny  | Deny      | Allow     |

---

# Phase 7 — Identify Objects

* Track security-sensitive object types.

---

## Examples

```text
User

Profile

Organization

Project

Document

Invoice

Order

Message

Invitation

API Key

File
```

---

## Record

```text
Object Type:

Identifier:

Owner:

Tenant:

Readable By:

Writable By:

Deletable By:

Shareable By:

Exportable By:
```

---

# Phase 8 — Identify Trust Boundaries

* Look for transitions such as:

```text
Unauthenticated

→

Authenticated
```

```text
User

→

Administrator
```

```text
User A

→

User B
```

```text
Tenant A

→

Tenant B
```

```text
Browser

→

Server
```

```text
Application

→

Internal Network
```

---

# Phase 9 — Build the Attack Surface

* Organize testing areas.

```text
Authentication

Sessions

Authorization

APIs

Business Logic

Input Processing

Files

Server-Side Requests

Browser Security

Caching

Concurrency

Integrations
```

---

# Phase 10 — Prioritize

* Start with high-impact boundaries.

---

## High Priority

```text
Authentication

Account Recovery

Authorization

Cross-Tenant Access

Administrative Functions

Sensitive Data

Payments

API Keys

Server-Side Fetching
```

---

## Medium Priority

```text
File Processing

Business Workflows

Sharing

Invitations

Exports

Integrations
```

---

## Context Dependent

```text
Injection

Browser Security

Caching

Concurrency

Information Disclosure
```

---

# Phase 11 — Generate Hypotheses

* For every interesting feature, ask:

```text
What Does the Server Trust?

What Can the Client Modify?

What Security Rule Should Hold?

What Happens If That Rule Fails?
```

---

## Example

### Observation

```text
PATCH /api/projects/731
```

### Hypothesis

```text
The server may authorize project updates only
through client-side UI restrictions.
```

### Test

```text
User A

↓

Send PATCH Request for User B's Controlled Project

↓

Observe Authorization Decision
```

---

# Hypothesis Tracker

| ID   | Feature  | Hypothesis           | Priority | Status  |
| ---- | -------- | -------------------- | -------- | ------- |
| H001 | Projects | Cross-user IDOR      | High     | New     |
| H002 | Profile  | Role mass assignment | High     | Testing |
| H003 | Import   | SSRF                 | High     | New     |

---

# Phase 12 — Perform Baseline Testing

* Every security test should begin with expected behavior.

---

## Example

```text
User A

↓

Access User A Object

↓

200 OK
```

* Then test the boundary:

```text
User A

↓

Access User B Controlled Object

↓

Expected:

403 / 404 / Denial
```

---

# Phase 13 — Change One Variable

* Avoid changing multiple variables at once.

---

## Better

```text
Baseline Request

↓

Change Object ID Only

↓

Compare
```

---

## Poor

```text
Change ID

+

Change Role

+

Change Method

+

Change Headers

↓

Unclear Result
```

---

# Phase 14 — Use Burp Suite Strategically

## Proxy

* Observe application behavior.

## Target

* Build the attack surface.

## Repeater

* Perform controlled manual testing.

## Intruder

* Automate carefully selected variations.

## Decoder

* Inspect encoded values.

## Comparer

* Compare meaningful response differences.

## Sequencer

* Analyze token randomness where appropriate.

## Collaborator

* Validate authorized out-of-band behavior.

---

# Phase 15 — Test Authentication

* Assess:

```text
Registration

Login

Password Reset

MFA

Account Recovery

Alternative Authentication

Logout
```

---

## Questions

* [ ] Can authentication steps be skipped?
* [ ] Can accounts be enumerated?
* [ ] Are reset tokens secure?
* [ ] Can reset tokens be reused?
* [ ] Can MFA be bypassed?
* [ ] Are alternate authentication endpoints weaker?
* [ ] Are sessions created correctly?

---

# Phase 16 — Test Sessions

* Assess:

```text
Creation

Rotation

Expiration

Logout

Revocation

Privilege Changes

Password Changes
```

---

## Questions

* [ ] Does the session rotate after authentication?
* [ ] Does logout invalidate the session?
* [ ] Do password changes affect old sessions appropriately?
* [ ] Do role changes update active permissions?
* [ ] Are cookies protected appropriately?

---

# Phase 17 — Test Authorization

* Use multiple controlled accounts.

---

## Matrix

```text
Actor

×

Object

×

Action

×

Role

×

Tenant
```

---

## Test

* [ ] Own object.
* [ ] Other-user object.
* [ ] Same-tenant object.
* [ ] Cross-tenant object.
* [ ] Read.
* [ ] Update.
* [ ] Delete.
* [ ] Export.
* [ ] Share.
* [ ] Administrative actions.
* [ ] Sensitive properties.

---

# Phase 18 — Test APIs

* Review:

```text
Authentication

Authorization

Mass Assignment

Excessive Data Exposure

Rate Limiting

Pagination

Filtering

Batch Operations

Version Differences

Error Handling
```

---

## API Rule

```text
Every Identifier

May Represent

A Security Boundary
```

---

# Phase 19 — Test Business Logic

* Identify business rules.

---

## Examples

```text
Coupon Used Once

Invitation Used Once

Refund ≤ Payment

Balance ≥ 0

Only Owner Can Approve

Order Must Be Paid Before Fulfillment
```

---

## Manipulate

```text
Order

Sequence

Replay

Quantity

Price

State

Concurrency
```

---

# Phase 20 — Test Input Boundaries

* Identify:

```text
Input Source

↓

Transformation

↓

Sensitive Sink
```

---

## Investigate

* [ ] SQL injection.
* [ ] XSS.
* [ ] Command injection.
* [ ] SSTI.
* [ ] XXE.
* [ ] Path traversal.
* [ ] Header injection.
* [ ] CRLF injection.

---

# Phase 21 — Test File Features

* Review:

```text
Upload

Download

Import

Export

Processing

Storage

Retrieval
```

---

## Questions

* [ ] Is access authorized?
* [ ] Is file type validated?
* [ ] Is content validated?
* [ ] Are filenames handled safely?
* [ ] Can uploaded content execute?
* [ ] Can paths escape intended directories?
* [ ] Are exports scoped correctly?

---

# Phase 22 — Test Server-Side Requests

* Identify features that accept:

```text
URLs

Webhooks

Remote Images

Remote Files

Feeds

Callbacks
```

---

## Evaluate

* [ ] Destination control.
* [ ] Scheme restrictions.
* [ ] Redirect handling.
* [ ] Internal destination protections.
* [ ] External interaction behavior.
* [ ] Equivalent fetch functionality.

---

# Phase 23 — Test Browser Security

* Review:

```text
XSS

CORS

CSRF

CSP

Clickjacking

postMessage

DOM Behavior

Browser Storage
```

---

# Phase 24 — Test Concurrency

* Identify one-time or limited actions.

---

## Examples

```text
Coupon Redemption

Credit Use

Reward Claim

Withdrawal

Invitation Acceptance

Inventory Purchase
```

---

## Workflow

```text
Define Invariant

↓

Capture Baseline

↓

Reset State

↓

Send Concurrent Requests

↓

Verify Final State

↓

Repeat
```

---

# Phase 25 — Validate Suspicious Findings

* Before reporting:

```text
Reproduce

↓

Use Controls

↓

Verify Security Boundary

↓

Verify Final State

↓

Identify Root Cause

↓

Demonstrate Minimal Impact
```

---

## Validation Checklist

* [ ] Reproducible.
* [ ] Attacker identified.
* [ ] Target identified.
* [ ] Ownership proven.
* [ ] Expected behavior known.
* [ ] Actual behavior confirmed.
* [ ] Security boundary failed.
* [ ] Impact demonstrated.
* [ ] False positives considered.
* [ ] Root cause identified.

---

# Phase 26 — Collect Evidence

* Preserve:

```text
Baseline

Modified Request

Response

Identity

Ownership

Final State

Impact
```

---

## Evidence Folder

```text
findings/
│
├── F001/
│   ├── notes.md
│   ├── requests/
│   ├── responses/
│   ├── screenshots/
│   └── poc/
```

---

# Phase 27 — Write Findings

* Every confirmed finding should contain:

```text
Title

Summary

Affected Asset

Severity

Prerequisites

Description

Steps to Reproduce

Expected Behavior

Actual Behavior

Evidence

Impact

Root Cause

Remediation
```

---

# Phase 28 — Review Coverage

* Before ending the assessment, verify:

* [ ] Authentication tested.

* [ ] Sessions tested.

* [ ] Authorization tested.

* [ ] Multiple roles tested.

* [ ] Cross-user boundaries tested.

* [ ] Cross-tenant boundaries tested.

* [ ] APIs tested.

* [ ] Business logic tested.

* [ ] Input boundaries tested.

* [ ] Files tested.

* [ ] Server-side requests tested.

* [ ] Browser security reviewed.

* [ ] Concurrency considered.

* [ ] High-value integrations reviewed.

---

# Phase 29 — Retest

* If remediation is available:

```text
Original PoC

↓

Root Cause

↓

Equivalent Paths

↓

Bypass

↓

Regression

↓

Legitimate Functionality
```

---

# Phase 30 — Final Review

* Ask:

```text
What Did I Understand Well?

What Did I Miss Initially?

Which Hypotheses Were Strongest?

Which Tests Produced Noise?

Where Did I Rely Too Much on Tools?

Where Did Manual Reasoning Matter Most?

Were My Findings Fully Proven?

Were My Reports Reproducible?

What Would I Test Next With More Time?
```

---

# Capstone Scoring Model

## 1 — Scope and Safety

```text
10 Points
```

* Clear scope.
* Safe testing.
* Controlled accounts.
* Appropriate data handling.

---

## 2 — Application Mapping

```text
15 Points
```

* Feature map.
* Endpoint inventory.
* Role identification.
* Object identification.
* Trust boundaries.

---

## 3 — Testing Methodology

```text
20 Points
```

* Structured hypotheses.
* Baselines.
* Controlled mutations.
* Appropriate Burp usage.
* Coverage.

---

## 4 — Vulnerability Validation

```text
20 Points
```

* Reproduction.
* Controls.
* Root cause.
* Impact.
* False-positive elimination.

---

## 5 — Evidence

```text
10 Points
```

* Requests.
* Responses.
* Screenshots.
* Ownership.
* Final-state verification.

---

## 6 — Reporting

```text
15 Points
```

* Clear title.
* Reproduction.
* Impact.
* Root cause.
* Remediation.

---

## 7 — Retesting and Review

```text
10 Points
```

* Root-cause validation.
* Bypass checks.
* Regression checks.
* Lessons learned.

---

## Total

```text
100 Points
```

---

# Score Interpretation

```text
90–100

Professional Assessment Ready
```

```text
80–89

Strong Practical Capability
```

```text
70–79

Good Foundation — Needs More Repetition
```

```text
60–69

Methodology Developing
```

```text
Below 60

Repeat Key Modules Before Final Assessment
```

---

# Capstone Completion Criteria

* The capstone is complete when you have:

* [ ] Defined scope.

* [ ] Configured Burp Suite.

* [ ] Mapped the application.

* [ ] Created an endpoint inventory.

* [ ] Identified roles.

* [ ] Identified sensitive objects.

* [ ] Mapped trust boundaries.

* [ ] Generated hypotheses.

* [ ] Tested high-value surfaces.

* [ ] Used multiple controlled accounts.

* [ ] Tested authorization systematically.

* [ ] Investigated APIs.

* [ ] Investigated business logic.

* [ ] Validated suspicious findings.

* [ ] Eliminated false positives.

* [ ] Collected evidence.

* [ ] Written professional reports.

* [ ] Reviewed testing coverage.

* [ ] Performed retesting where possible.

* [ ] Written lessons learned.

---

# Capstone Submission Package

```text
Burp-Suite-Capstone/
│
├── Scope-and-Rules.md
├── Application-Map.md
├── Endpoint-Inventory.md
├── Role-Permission-Matrix.md
├── Trust-Boundary-Map.md
├── Hypothesis-Tracker.md
├── Testing-Coverage.md
├── Findings/
├── Evidence/
├── Final-Report.md
├── Retest-Notes.md
└── Lessons-Learned.md
```

---

# Portfolio Considerations

* If publishing a capstone publicly:

  * Use only authorized practice targets.
  * Remove credentials.
  * Remove reusable tokens.
  * Remove sensitive personal information.
  * Do not publish confidential client information.
  * Do not publish active vulnerabilities without authorization.
  * Clearly identify intentionally vulnerable lab environments.
  * Focus on methodology and learning.

---

# Interview Value

* A completed capstone gives you a concrete answer to:

```text
"Walk me through how you would test
a web application from start to finish."
```

* Instead of listing tools, explain:

```text
Scope

↓

Mapping

↓

Trust Boundaries

↓

Hypotheses

↓

Testing

↓

Validation

↓

Impact

↓

Reporting

↓

Retesting
```

---

# Professional Tips

* Treat the capstone like a real engagement.
* Create the workspace before testing.
* Define scope before opening Burp.
* Browse normally before manipulating traffic.
* Map features before testing vulnerabilities.
* Maintain an endpoint inventory throughout the assessment.
* Label every test account clearly.
* Keep sessions separated.
* Track object ownership.
* Identify tenant boundaries early.
* Build hypotheses from observed application behavior.
* Prioritize high-value security boundaries.
* Establish baselines before mutations.
* Change one meaningful variable at a time.
* Use Burp Repeater as the primary controlled testing workspace.
* Use automation only when it improves structured coverage.
* Test authorization with multiple controlled identities.
* Think beyond the visible UI.
* Investigate APIs independently.
* Define business invariants before testing logic abuse.
* Verify persistent state after state-changing tests.
* Use negative controls.
* Attempt to disprove suspicious findings.
* Stop exploitation once sufficient proof exists.
* Preserve evidence immediately.
* Write findings while context is fresh.
* Explain root cause, not only symptoms.
* Separate technical impact from business impact.
* Review coverage before ending the assessment.
* Document what you did not test.
* Retest the underlying security control rather than only the original payload.
* Finish with a lessons-learned review.

---

# Common Mistakes

* Treating the capstone as a vulnerability-count competition.
* Testing without defining scope.
* Using real targets without authorization.
* Starting with scanners before understanding the application.
* Failing to map functionality.
* Failing to create an endpoint inventory.
* Using only one account.
* Mixing sessions between test users.
* Losing track of object ownership.
* Ignoring tenant boundaries.
* Testing only visible UI functionality.
* Randomly inserting payloads without hypotheses.
* Changing too many variables simultaneously.
* Treating unusual responses as confirmed vulnerabilities.
* Reporting scanner alerts without validation.
* Failing to use positive and negative controls.
* Failing to verify final state.
* Over-exploiting findings.
* Accessing unnecessary real-user data.
* Overclaiming impact.
* Writing reports from memory after testing ends.
* Failing to explain root cause.
* Giving generic remediation.
* Ignoring testing coverage gaps.
* Treating a blocked original payload as proof of remediation.
* Publishing sensitive capstone evidence publicly.
* Focusing on tools instead of methodology.

---

# Practice Tasks

1. Choose an authorized capstone application.

2. Create the complete capstone workspace.

3. Write the scope document.

4. Configure a dedicated Burp Suite project.

5. Create at least two controlled accounts.

6. Browse every major feature normally.

7. Build an application map.

8. Build an endpoint inventory.

9. Identify every role you can observe.

10. Create a role-permission matrix.

11. Identify at least five sensitive object types.

12. Record ownership and tenant relationships.

13. Identify at least five trust boundaries.

14. Build an attack-surface map.

15. Rank the attack surfaces by priority.

16. Create at least ten security hypotheses.

17. Test authentication.

18. Test session management.

19. Test object-level authorization.

20. Test function-level authorization.

21. Test property-level authorization.

22. Test cross-user boundaries.

23. Test cross-tenant boundaries where available.

24. Review APIs independently of the UI.

25. Identify at least three business invariants.

26. Test business workflows safely.

27. Map input sources to sensitive sinks.

28. Review file functionality.

29. Review server-side request functionality.

30. Review browser-side trust boundaries.

31. Identify concurrency-sensitive actions.

32. Validate every suspicious result.

33. Use positive and negative controls.

34. Verify persistent state where relevant.

35. Attempt to falsify each candidate finding.

36. Create evidence folders for confirmed findings.

37. Write complete vulnerability reports.

38. Review severity independently.

39. Complete the coverage checklist.

40. Document testing limitations.

41. Simulate or perform authorized remediation retesting.

42. Write the final assessment report.

43. Score yourself using the 100-point model.

44. Write a lessons-learned document.

45. Prepare a five-minute interview explanation of your assessment methodology.

---

# Interview Questions

1. What is the purpose of a penetration-testing capstone?
2. How would you choose a safe environment for a capstone assessment?
3. What should you define before testing begins?
4. How do you structure an assessment workspace?
5. Why should you browse an application normally before security testing?
6. How do you build an application map?
7. How do you create an endpoint inventory?
8. Why are roles important during testing?
9. How do you identify security-sensitive objects?
10. What is a trust boundary?
11. How do you prioritize attack surfaces?
12. What is a security hypothesis?
13. Why should every test begin with a baseline?
14. Why should you change one variable at a time?
15. How do you use Burp Suite throughout an assessment?
16. How do you test authentication systematically?
17. How do you test session management?
18. How do you test authorization with multiple accounts?
19. How do you approach API testing?
20. How do you identify business-logic vulnerabilities?
21. What is a business invariant?
22. How do you approach input-processing vulnerabilities?
23. How do you test file functionality?
24. How do you identify SSRF attack surfaces?
25. What browser-side security boundaries do you review?
26. How do you identify race-condition candidates?
27. How do you validate a suspicious finding?
28. Why are controls important?
29. How do you eliminate false positives?
30. How do you demonstrate impact safely?
31. What evidence should be preserved?
32. What makes a vulnerability report professional?
33. How do you determine root cause?
34. How do you assess severity?
35. Why should testing coverage be tracked?
36. What should be included in a final assessment review?
37. How do you retest remediation?
38. What is the difference between testing the original PoC and testing the root cause?
39. What information should never be published in a public portfolio?
40. Walk me through your complete capstone assessment from start to finish.

---

# Quick Revision

* Capstone:

```text
Scope

↓

Map

↓

Model

↓

Hypothesize

↓

Test

↓

Validate

↓

Prove

↓

Report

↓

Retest

↓

Review
```

* Mapping:

```text
Features

+

Endpoints

+

Roles

+

Objects

+

Trust Boundaries
```

* Testing:

```text
Baseline

↓

One Controlled Change

↓

Observe

↓

Compare

↓

Verify
```

* Authorization:

```text
Actor

×

Object

×

Action

×

Role

×

Tenant
```

* Validation:

```text
Reproduce

+

Controls

+

Security Boundary Failure

+

Impact

+

Root Cause
```

* Evidence:

```text
Identity

+

Ownership

+

Request

+

Response

+

Final State
```

* Reporting:

```text
What?

Where?

How?

Why?

Impact?

Fix?
```

* Retesting:

```text
Original PoC

+

Root Cause

+

Equivalent Paths

+

Bypass

+

Regression
```

---

# Key Takeaways

* The capstone combines the complete Burp Suite Mastery learning path into one structured security assessment.
* The goal is not maximum vulnerability count but professional methodology, validation quality, evidence, and reporting.
* Capstone testing must be performed only against explicitly authorized targets.
* Local or dedicated vulnerable applications are ideal for broad capstone testing.
* A professional capstone begins with scope, rules, controlled accounts, and an organized workspace.
* Applications should be explored normally before security manipulation begins.
* Application maps, endpoint inventories, role matrices, object inventories, and trust-boundary maps form the foundation of structured testing.
* High-value attack surfaces should be prioritized according to application context rather than tested in arbitrary order.
* Security hypotheses convert observations into focused tests.
* Every meaningful test should begin with a known baseline.
* Changing one variable at a time makes results easier to interpret.
* Burp Suite tools should support methodology rather than replace reasoning.
* Authentication, sessions, authorization, APIs, business logic, input boundaries, files, server-side requests, browser security, and concurrency should all be considered according to relevance.
* Authorization should be tested across actors, objects, actions, roles, and tenants.
* Business-logic testing begins with identifying the intended invariant.
* Suspicious observations must be reproduced, controlled, verified, and tied to a failed security boundary before reporting.
* False-positive elimination is part of professional vulnerability validation.
* Evidence should prove identity, ownership, request manipulation, response behavior, final state, and impact with minimum unnecessary exposure.
* Professional reports explain the vulnerability, reproduction, evidence, impact, root cause, and remediation.
* Testing coverage should be reviewed explicitly before ending an assessment.
* Coverage limitations should be documented rather than silently ignored.
* Remediation retesting should validate root cause, equivalent attack paths, bypass resistance, regression behavior, and legitimate functionality.
* A completed capstone can become a strong portfolio artifact when performed on an authorized practice environment and sanitized appropriately.
* The capstone also provides a concrete framework for answering methodology questions during penetration-testing interviews.
* The complete capstone workflow is **define scope → configure environment → understand application → map features and endpoints → identify roles, objects, and trust boundaries → prioritize attack surfaces → generate hypotheses → establish baselines → perform controlled testing → validate findings → eliminate false positives → demonstrate minimal impact → collect evidence → write professional reports → review coverage → retest remediation → document lessons learned**.
