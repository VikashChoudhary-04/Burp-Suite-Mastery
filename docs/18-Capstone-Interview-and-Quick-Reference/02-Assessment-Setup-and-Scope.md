# Assessment Setup and Scope

> **Module 18 — Capstone, Interview & Quick Reference**

> **Progress**

```text
██░░░░░░░░░░░░░ 2 / 15 Files
```

---

## Overview

* A professional web application security assessment begins before the first security payload is sent.

* The quality of an assessment depends heavily on preparation.

* Before testing begins, you should establish:

  * Authorization.
  * Scope.
  * Rules of engagement.
  * Testing environment.
  * Test accounts.
  * Data-handling requirements.
  * Evidence organization.
  * Burp Suite configuration.
  * Communication procedures.
  * Testing limitations.

* Poor preparation can cause:

  * Accidental testing of out-of-scope systems.
  * Mixing traffic from unrelated applications.
  * Loss of important evidence.
  * Confusion between test accounts.
  * Unnecessary impact on production systems.
  * Violations of program rules.
  * Incomplete assessment coverage.

* The professional workflow begins with:

```text
Authorization

↓

Scope

↓

Rules of Engagement

↓

Workspace Preparation

↓

Burp Suite Configuration

↓

Account Preparation

↓

Baseline Verification

↓

Testing
```

---

## Objectives

* By the end of this lesson, you will be able to:

  * Confirm authorization before testing.
  * Define assessment scope precisely.
  * Distinguish in-scope and out-of-scope assets.
  * Understand rules of engagement.
  * Identify prohibited testing techniques.
  * Record assessment constraints.
  * Prepare a structured testing workspace.
  * Configure Burp Suite for a dedicated assessment.
  * Configure target scope.
  * Reduce irrelevant traffic.
  * Prepare multiple controlled test accounts.
  * Separate user sessions safely.
  * Establish evidence-handling procedures.
  * Create assessment notes and templates.
  * Establish baseline application behavior.
  * Perform a professional pre-assessment checklist.

---

# Phase 1 — Confirm Authorization

* Never assume that an accessible system is authorized for security testing.

* Authorization should exist before active testing begins.

---

## Authorization Sources

* Depending on the engagement, authorization may come from:

  * A penetration-testing contract.
  * A statement of work.
  * Written client authorization.
  * A bug bounty program policy.
  * A vulnerability disclosure program.
  * A controlled training environment.
  * A system you personally own.

---

## Core Rule

```text
Accessible

≠

Authorized
```

---

## Before Testing Ask

```text
Who Owns the System?

Who Authorized the Assessment?

Which Assets Are Included?

Which Techniques Are Allowed?

Which Techniques Are Prohibited?

When May Testing Occur?

What Data May Be Accessed?

Who Should Be Contacted if Something Goes Wrong?
```

---

# Phase 2 — Define Scope

* Scope defines the technical boundaries of the assessment.

---

## Scope May Include

```text
Domains

Subdomains

IP Addresses

Web Applications

APIs

Mobile Backends

Specific Features

Specific Accounts

Specific Environments
```

---

## Example

```text
In Scope

app.example.com

api.example.com

*.staging.example.com
```

```text
Out of Scope

support.example.com

status.example.com

third-party.example.net
```

---

# Scope Record

```text
Assessment Name:

Client / Program:

Assessment Type:

Start Date:

End Date:

Primary Application:

In-Scope Domains:

In-Scope Subdomains:

In-Scope APIs:

In-Scope IP Addresses:

In-Scope Features:

Out-of-Scope Assets:

Excluded Third Parties:

Testing Accounts:

Notes:
```

---

# Scope Classification

## Explicitly In Scope

* Assets clearly listed as authorized.

```text
app.example.com
```

---

## Explicitly Out of Scope

* Assets clearly excluded.

```text
support.example.com
```

---

## Ambiguous

* Assets discovered during testing but not clearly covered.

```text
internal-api.example.com
```

* Do not automatically test ambiguous assets.

```text
Discover

↓

Check Scope

↓

Clarify if Necessary

↓

Test Only if Authorized
```

---

# Wildcard Scope

* A wildcard may authorize multiple subdomains.

```text
*.example.com
```

* However, wildcard scope does not automatically mean every discovered system should be aggressively tested.

* Review:

  * Program exclusions.
  * Third-party hosting.
  * Acquired companies.
  * Shared infrastructure.
  * Production restrictions.
  * Explicit exceptions.

---

# Third-Party Services

* Applications frequently depend on external providers.

```text
Application

↓

Payment Provider

CDN

Analytics

Support Platform

Cloud Storage

Authentication Provider
```

* Third-party services may not be covered by the target's authorization.

---

## Rule

```text
Application Uses Service

≠

Permission to Test Service
```

---

# Phase 3 — Review Rules of Engagement

* Scope tells you **where** you may test.

* Rules of engagement tell you **how** you may test.

---

## Review

* [ ] Allowed testing hours.
* [ ] Rate limits.
* [ ] Automation restrictions.
* [ ] Scanner restrictions.
* [ ] Brute-force restrictions.
* [ ] Credential-testing restrictions.
* [ ] Social-engineering restrictions.
* [ ] Denial-of-service restrictions.
* [ ] File-upload restrictions.
* [ ] Data-access restrictions.
* [ ] Account restrictions.
* [ ] Reporting requirements.
* [ ] Disclosure requirements.
* [ ] Emergency procedures.

---

# Common Prohibited Techniques

* Depending on the engagement, prohibited techniques may include:

```text
Denial of Service

Destructive Testing

Social Engineering

Physical Attacks

Large-Scale Brute Force

Unrestricted Automated Scanning

Spam

Testing Third Parties

Persistent Malware

Destructive Data Modification
```

* Always follow the specific rules of the engagement.

---

# Phase 4 — Understand Testing Constraints

* Record operational limitations before testing.

---

## Constraints Template

```text
Testing Window:

Maximum Request Rate:

Automation Allowed:

Scanner Allowed:

Credential Attacks Allowed:

File Upload Testing Allowed:

Out-of-Band Testing Allowed:

Race Testing Allowed:

Production Data Restrictions:

Maximum Proof Allowed:

Emergency Contact:

Special Restrictions:
```

---

# Phase 5 — Define Stop Conditions

* Professional testers should know when to stop a test.

---

## Stop When

```text
Scope Becomes Unclear

Authorization Becomes Unclear

Production Stability Is Affected

Unexpected Sensitive Data Is Exposed

Real Financial Transactions May Occur

Destructive Actions Would Be Required

Rate Limits Are Being Exceeded

Another User Could Be Harmed

Program Rules Prohibit Further Testing
```

---

## Decision Model

```text
Is It In Scope?
        │
        ├── No → Stop
        │
        ▼
       Yes
        │
        ▼
Is the Technique Allowed?
        │
        ├── No → Stop
        │
        ▼
       Yes
        │
        ▼
Can It Be Tested Safely?
        │
        ├── No → Find Safer Alternative
        │
        ▼
       Yes
        │
        ▼
Proceed With Controlled Testing
```

---

# Phase 6 — Create the Assessment Workspace

* Create a dedicated workspace for the assessment.

---

## Example

```text
assessment/
│
├── 01-scope/
├── 02-recon/
├── 03-mapping/
├── 04-requests/
├── 05-hypotheses/
├── 06-findings/
├── 07-evidence/
├── 08-reports/
├── 09-retest/
└── 10-notes/
```

---

## Purpose

### `01-scope/`

* Authorization.
* Scope.
* Rules of engagement.

### `02-recon/`

* Passive reconnaissance.
* Asset discovery.
* Technology information.

### `03-mapping/`

* Application map.
* Endpoint inventory.
* Roles.
* Objects.
* Trust boundaries.

### `04-requests/`

* Important raw HTTP requests and responses.

### `05-hypotheses/`

* Security hypotheses.
* Test status.
* Investigation notes.

### `06-findings/`

* Validated vulnerabilities.

### `07-evidence/`

* Screenshots.
* Videos.
* Request/response evidence.
* OAST evidence.

### `08-reports/`

* Vulnerability reports.
* Final report.

### `09-retest/`

* Remediation verification.

### `10-notes/`

* Daily notes.
* Questions.
* Observations.

---

# Phase 7 — Create a Naming Convention

* Consistent naming makes evidence easier to organize.

---

## Finding IDs

```text
F001

F002

F003
```

---

## Hypothesis IDs

```text
H001

H002

H003
```

---

## Request Naming

```text
H001-01-Baseline

H001-02-Modified

H001-03-Negative-Control

H001-04-Verification
```

---

## Screenshot Naming

```text
F001-01-User-A-Object.png

F001-02-User-B-Access.png

F001-03-Final-State.png
```

---

# Phase 8 — Prepare Notes

* Do not rely on memory during an assessment.

---

## Daily Notes Template

```text
Date:

Target:

Testing Period:

Accounts Used:

Features Tested:

Endpoints Discovered:

Hypotheses Created:

Hypotheses Validated:

Hypotheses Rejected:

Interesting Observations:

Evidence Collected:

Unresolved Questions:

Next Priorities:
```

---

# Observation Template

```text
Observation ID:

Date:

Feature:

Endpoint:

Account:

Role:

Tenant:

Observation:

Why It Matters:

Potential Hypothesis:

Next Test:
```

---

# Phase 9 — Prepare Finding Templates

```text
Finding ID:

Title:

Severity:

Affected Asset:

Endpoint:

Method:

Parameter:

Attacker:

Required Role:

Target:

Prerequisites:

Summary:

Description:

Steps to Reproduce:

Expected Behavior:

Actual Behavior:

Evidence:

Technical Impact:

Business Impact:

Root Cause:

Remediation:

References:

Retest Status:
```

---

# Phase 10 — Create a Dedicated Burp Project

* Use a separate Burp Suite project for the assessment.

---

## Benefits

```text
Cleaner HTTP History

Dedicated Site Map

Persistent Repeater Tabs

Assessment-Specific Configuration

Easier Evidence Preservation

Reduced Cross-Target Confusion
```

---

## Recommended

```text
One Assessment

↓

One Burp Project
```

---

# Phase 11 — Configure the Browser

* Use a dedicated testing browser or browser profile.

---

## Configure

* [ ] Proxy traffic through Burp.
* [ ] Install Burp CA certificate where required.
* [ ] Verify HTTPS interception.
* [ ] Disable unrelated extensions if necessary.
* [ ] Separate personal browsing.
* [ ] Avoid personal accounts.
* [ ] Confirm target requests appear in Proxy history.

---

## Professional Rule

```text
Personal Browsing

≠

Testing Browser
```

---

# Phase 12 — Configure Burp Target Scope

* Add authorized assets to Burp's target scope.

---

## Example

```text
https://app.example.com/*

https://api.example.com/*
```

---

## Benefits

* Scope configuration helps:

  * Reduce noise.
  * Focus Proxy history.
  * Focus Site map.
  * Reduce accidental interaction with unrelated systems.
  * Improve assessment organization.

---

# Scope Verification Test

* Browse:

```text
https://app.example.com
```

* Confirm:

```text
Request Appears in Burp

↓

Target Is Marked In Scope

↓

Application Loads Normally
```

---

# Phase 13 — Reduce Noise

* Modern browsers generate large amounts of unrelated traffic.

---

## Common Noise

```text
Browser Updates

Analytics

Search Suggestions

Extension Traffic

Telemetry

Third-Party CDNs

Background Services
```

---

## Goal

```text
HTTP History

↓

Mostly Relevant Assessment Traffic
```

---

# Phase 14 — Configure Interception Workflow

* Constant interception is not always efficient.

---

## Recommended Workflow

```text
Normal Browsing

↓

Intercept OFF

↓

Observe Proxy History

↓

Identify Interesting Request

↓

Send to Repeater

↓

Controlled Testing
```

---

## Use Intercept ON When

* You need to:

  * Modify a request before it reaches the server.
  * Test a one-time workflow.
  * Manipulate a client-side restricted field.
  * Observe a specific state transition.
  * Prevent a request temporarily.

---

# Phase 15 — Prepare Test Accounts

* Multiple controlled accounts are essential for authorization testing.

---

## Recommended Identities

```text
User A

User B

Privileged User / Admin
```

* If the application supports organizations:

```text
Tenant A
├── User A1
└── Admin A

Tenant B
├── User B1
└── Admin B
```

---

# Account Inventory

| Label   | Username   | Role  | Tenant   | Purpose              |
| ------- | ---------- | ----- | -------- | -------------------- |
| User A  | Controlled | User  | Tenant A | Baseline             |
| User B  | Controlled | User  | Tenant A | Horizontal testing   |
| User C  | Controlled | User  | Tenant B | Cross-tenant testing |
| Admin A | Controlled | Admin | Tenant A | Vertical testing     |

---

# Phase 16 — Separate Sessions

* Mixing authentication sessions can invalidate authorization testing.

---

## Options

```text
Separate Browser Profiles

Separate Browsers

Burp Repeater Tabs With Explicit Cookies

Burp Session Handling

Incognito Profiles Where Appropriate
```

---

## Label Clearly

```text
USER-A

USER-B

TENANT-B

ADMIN
```

---

## Core Rule

```text
Unknown Session Context

=

Unreliable Authorization Test
```

---

# Phase 17 — Create Controlled Objects

* Create objects whose ownership is known.

---

## Example

```text
User A

Creates

Project A-101
```

```text
User B

Creates

Project B-202
```

* Now ownership is controlled and verifiable.

---

## Object Inventory

| Object    | ID  | Owner  | Tenant   | Purpose           |
| --------- | --- | ------ | -------- | ----------------- |
| Project A | 101 | User A | Tenant A | Baseline          |
| Project B | 202 | User B | Tenant A | Horizontal auth   |
| Project C | 303 | User C | Tenant B | Cross-tenant auth |

---

# Phase 18 — Establish Baseline Behavior

* Before manipulating requests, understand normal application behavior.

---

## Record

```text
Normal Login

Normal Logout

Normal Object Creation

Normal Object Read

Normal Object Update

Normal Object Delete

Normal Sharing

Normal Export

Normal Error Behavior
```

---

# Baseline Principle

```text
You Cannot Reliably Identify Abnormal Behavior

Without Understanding Normal Behavior
```

---

# Phase 19 — Verify Account Permissions

* Before authorization testing, confirm what each account is legitimately allowed to do.

---

## Example

| Action                      | User A        | User B        | Admin   |
| --------------------------- | ------------- | ------------- | ------- |
| View own project            | Allow         | Allow         | Allow   |
| Edit own project            | Allow         | Allow         | Allow   |
| View another user's project | Expected Deny | Expected Deny | Depends |
| Manage users                | Deny          | Deny          | Allow   |

---

# Phase 20 — Prepare Evidence Capture

* Decide how evidence will be preserved before findings appear.

---

## Evidence Types

```text
Raw Request

Raw Response

Screenshot

Video

Timestamp

Object Ownership

Account Context

Final State

Out-of-Band Interaction
```

---

## Evidence Structure

```text
evidence/
│
├── F001/
│   ├── 01-baseline-request.txt
│   ├── 02-baseline-response.txt
│   ├── 03-test-request.txt
│   ├── 04-test-response.txt
│   ├── 05-verification.png
│   └── notes.md
```

---

# Phase 21 — Protect Sensitive Data

* Assessment evidence may contain:

```text
Session Cookies

Authorization Tokens

Passwords

API Keys

Personal Data

Internal URLs

Private Files
```

---

## Handle Carefully

* [ ] Store evidence securely.
* [ ] Avoid unnecessary copying.
* [ ] Redact secrets from reports.
* [ ] Do not commit secrets to Git.
* [ ] Remove reusable tokens.
* [ ] Avoid publishing client data.
* [ ] Follow required retention policies.

---

# Git Safety

* Before committing assessment material:

```text
Check

↓

Credentials?

Tokens?

Cookies?

API Keys?

Personal Data?

Client Secrets?
```

* If yes:

```text
Remove

Redact

or

Do Not Commit
```

---

# Phase 22 — Prepare Burp Repeater Organization

* Repeater often becomes the main manual testing workspace.

---

## Suggested Groups

```text
Authentication

Sessions

Authorization

API

Business Logic

Injection

Files

SSRF

Validation
```

---

## Naming

```text
AUTH-01-Login-Baseline

AUTHZ-01-Project-Own

AUTHZ-02-Project-Cross-User

API-01-User-Endpoint

BL-01-Coupon-Replay
```

---

# Phase 23 — Prepare Hypothesis Tracking

* Do not test randomly.

---

## Template

```text
Hypothesis ID:

Feature:

Observation:

Security Assumption:

Potential Failure:

Attacker:

Target:

Expected Secure Behavior:

Test Plan:

Potential Impact:

Status:
```

---

## Status

```text
New

Testing

Validated

Rejected

Needs Follow-Up
```

---

# Phase 24 — Define Testing Priorities

* Prioritize high-value areas first.

---

## Priority 1

```text
Authentication

Authorization

Cross-Tenant Boundaries

Administrative Functions

Sensitive Data
```

---

## Priority 2

```text
Business Logic

Payments

Files

APIs

Server-Side Requests
```

---

## Priority 3

```text
Input Processing

Browser Security

Caching

Concurrency

Information Disclosure
```

* Priorities should change according to the application.

---

# Phase 25 — Establish Communication Procedures

* Formal engagements may require communication during testing.

---

## Record

```text
Primary Contact:

Backup Contact:

Emergency Contact:

Reporting Channel:

Critical Finding Procedure:

Incident Procedure:

Testing Pause Procedure:
```

---

# Critical Finding Workflow

```text
Potential Critical Finding

↓

Validate Safely

↓

Minimize Further Exploitation

↓

Preserve Evidence

↓

Follow Required Escalation Procedure
```

---

# Phase 26 — Perform Pre-Assessment Verification

* Before active testing begins:

* [ ] Authorization confirmed.

* [ ] Scope recorded.

* [ ] Out-of-scope assets recorded.

* [ ] Rules reviewed.

* [ ] Restrictions reviewed.

* [ ] Stop conditions defined.

* [ ] Workspace created.

* [ ] Burp project created.

* [ ] Browser configured.

* [ ] HTTPS interception verified.

* [ ] Target scope configured.

* [ ] Noise reduced.

* [ ] Test accounts prepared.

* [ ] Sessions separated.

* [ ] Controlled objects created.

* [ ] Account permissions verified.

* [ ] Notes prepared.

* [ ] Finding templates prepared.

* [ ] Evidence structure prepared.

* [ ] Sensitive-data handling understood.

* [ ] Communication contacts recorded.

---

# Assessment Setup Workflow

```text
Authorization

↓

Read Scope

↓

Read Rules

↓

Record Restrictions

↓

Define Stop Conditions

↓

Create Workspace

↓

Create Burp Project

↓

Configure Browser

↓

Configure Target Scope

↓

Reduce Noise

↓

Prepare Accounts

↓

Separate Sessions

↓

Create Controlled Objects

↓

Establish Baselines

↓

Prepare Evidence

↓

Prepare Notes

↓

Begin Mapping
```

---

# Example Assessment Setup

## Target

```text
https://lab.example
```

## Accounts

```text
User A

user-a@example.test
```

```text
User B

user-b@example.test
```

```text
Admin

admin@example.test
```

## Controlled Objects

```text
User A

Project ID 101
```

```text
User B

Project ID 202
```

## Burp Repeater

```text
AUTH/

AUTHZ/

API/

BUSINESS-LOGIC/

VALIDATION/
```

## Evidence

```text
evidence/

F001/

F002/
```

* The environment is now ready for structured testing.

---

# Bug Bounty Setup Differences

* Bug bounty testing may differ from formal penetration testing.

---

## Bug Bounty

```text
Public Program Policy

↓

Program Scope

↓

Program Rules

↓

Researcher-Controlled Workspace

↓

Safe Testing

↓

Report Individual Findings
```

---

## Formal Pentest

```text
Contract

↓

Statement of Work

↓

Rules of Engagement

↓

Defined Assessment Window

↓

Structured Coverage

↓

Final Assessment Report
```

---

# Shared Principle

```text
Different Engagement Model

Same Requirement:

Authorization + Scope + Safety
```

---

# Common Mistakes

* Testing before confirming authorization.
* Assuming a discovered subdomain is automatically in scope.
* Ignoring explicit exclusions.
* Testing third-party infrastructure without authorization.
* Failing to read bug bounty program rules.
* Ignoring rate limits.
* Running scanners without checking automation restrictions.
* Failing to define stop conditions.
* Using the same Burp project for unrelated targets.
* Mixing personal browsing with assessment traffic.
* Failing to configure target scope.
* Allowing Proxy history to fill with irrelevant traffic.
* Using only one test account.
* Mixing User A and User B sessions.
* Failing to track object ownership.
* Testing authorization against unknown real-user objects.
* Failing to establish normal baseline behavior.
* Writing notes only after testing ends.
* Saving screenshots without meaningful names.
* Losing raw requests and responses.
* Storing reusable tokens in Git repositories.
* Publishing sensitive evidence.
* Failing to prepare a reporting template.
* Beginning random vulnerability testing before setup is complete.

---

# Professional Tips

* Read scope twice before testing.
* Keep a local scope reference available during the assessment.
* Record exclusions explicitly.
* Treat ambiguous assets as unauthorized until clarified.
* Check ownership of third-party services.
* Record testing restrictions before opening scanners.
* Define stop conditions before risky tests.
* Use one Burp project per assessment.
* Use a dedicated testing browser.
* Configure Burp target scope immediately.
* Filter irrelevant traffic early.
* Create multiple controlled identities.
* Label sessions clearly.
* Create controlled objects for authorization testing.
* Record object ownership before manipulating IDs.
* Establish baselines before security mutations.
* Organize Repeater tabs by testing category.
* Use consistent hypothesis and finding IDs.
* Preserve raw HTTP evidence.
* Name screenshots according to findings.
* Keep evidence minimal but sufficient.
* Redact secrets before sharing reports.
* Never commit live credentials or session tokens.
* Maintain daily notes.
* Record unresolved questions.
* Review setup before beginning active testing.

---

# Practice Tasks

1. Choose an authorized practice target.

2. Create:

```text
assessment/
```

3. Add:

```text
01-scope/

02-recon/

03-mapping/

04-requests/

05-hypotheses/

06-findings/

07-evidence/

08-reports/

09-retest/

10-notes/
```

4. Create a scope document.

5. Record in-scope assets.

6. Record out-of-scope assets.

7. Record testing restrictions.

8. Define at least five stop conditions.

9. Create a dedicated Burp project.

10. Configure a dedicated browser.

11. Verify HTTP and HTTPS interception.

12. Configure Burp target scope.

13. Filter unrelated traffic.

14. Create User A.

15. Create User B.

16. Create an administrative account if the lab supports one.

17. Separate all sessions.

18. Create one controlled object for User A.

19. Create one controlled object for User B.

20. Record object IDs and ownership.

21. Verify legitimate permissions for each account.

22. Capture baseline requests for:

    * Login.
    * Object creation.
    * Object viewing.
    * Object editing.
    * Logout.

23. Create a hypothesis tracker.

24. Create a finding template.

25. Create an evidence folder.

26. Create a screenshot naming convention.

27. Create Repeater groups.

28. Check your workspace for exposed credentials or tokens.

29. Complete the pre-assessment checklist.

30. Do not begin vulnerability testing until all preparation steps are complete.

---

# Interview Questions

1. What should you do before beginning a penetration test?
2. Why is authorization important?
3. What is assessment scope?
4. What is the difference between scope and rules of engagement?
5. How do you handle an asset whose scope is ambiguous?
6. Does wildcard scope always mean every subdomain can be tested aggressively?
7. How do third-party services affect scope?
8. What are common prohibited testing techniques?
9. What are stop conditions?
10. When should a penetration tester stop testing?
11. How do you organize an assessment workspace?
12. Why should you use a dedicated Burp project?
13. Why should you use a dedicated testing browser?
14. How do you configure Burp Suite for an assessment?
15. Why is target scope important in Burp?
16. How do you reduce irrelevant Proxy traffic?
17. When should Intercept be enabled?
18. Why are multiple test accounts important?
19. How do you separate authentication sessions?
20. Why is session labeling important?
21. Why should you create controlled objects?
22. How do you track object ownership?
23. Why should normal application behavior be established before testing?
24. What is a baseline request?
25. How do you prepare evidence collection?
26. What sensitive information may appear in assessment evidence?
27. Why should session tokens not be committed to Git?
28. How do you organize Repeater during an assessment?
29. What is a hypothesis tracker?
30. How do you prioritize testing areas?
31. How does bug bounty setup differ from a formal penetration test?
32. What information should be recorded for emergency communication?
33. What should be included in a pre-assessment checklist?
34. Walk me through how you prepare Burp Suite before testing a new application.
35. Walk me through your complete assessment setup process before sending your first security test.

---

# Quick Revision

* Before testing:

```text
Authorization

↓

Scope

↓

Rules

↓

Restrictions

↓

Stop Conditions
```

* Environment:

```text
Workspace

↓

Burp Project

↓

Testing Browser

↓

Target Scope

↓

Noise Reduction
```

* Identity:

```text
User A

+

User B

+

Admin

+

Separate Sessions
```

* Controlled testing:

```text
Known Account

+

Known Object

+

Known Ownership

+

Known Baseline
```

* Evidence:

```text
Request

+

Response

+

Identity

+

Ownership

+

Final State
```

* Core rule:

```text
Unknown Scope

→

Do Not Test
```

* Professional preparation:

```text
Prepare First

Test Second
```

---

# Pre-Assessment Quick Checklist

```text
AUTHORIZATION

[ ] Written or program authorization
[ ] Assessment owner identified


SCOPE

[ ] In-scope assets
[ ] Out-of-scope assets
[ ] APIs
[ ] Third parties
[ ] Ambiguous assets reviewed


RULES

[ ] Automation
[ ] Rate limits
[ ] Brute force
[ ] DoS
[ ] Data access
[ ] File testing
[ ] OAST
[ ] Race testing


ENVIRONMENT

[ ] Dedicated workspace
[ ] Dedicated Burp project
[ ] Dedicated browser
[ ] HTTPS interception
[ ] Target scope
[ ] Traffic filters


IDENTITIES

[ ] User A
[ ] User B
[ ] Admin if available
[ ] Sessions separated
[ ] Roles recorded
[ ] Tenants recorded


OBJECTS

[ ] Controlled objects
[ ] IDs recorded
[ ] Ownership recorded
[ ] Baselines established


DOCUMENTATION

[ ] Notes template
[ ] Hypothesis tracker
[ ] Finding template
[ ] Evidence structure
[ ] Naming convention


SAFETY

[ ] Stop conditions
[ ] Sensitive-data handling
[ ] No credentials in Git
[ ] Emergency contact
```

---

# Key Takeaways

* Professional security assessments begin with preparation rather than payloads.
* Authorization must exist before active security testing begins.
* An accessible system is not automatically an authorized target.
* Scope defines where testing may occur, while rules of engagement define how testing may be performed.
* Explicitly out-of-scope assets must not be tested.
* Ambiguous assets should be treated conservatively until authorization is clarified.
* Third-party services used by an application may not be authorized for testing.
* Testing restrictions may cover automation, brute force, denial of service, file uploads, race conditions, out-of-band testing, and data access.
* Stop conditions should be defined before testing begins.
* A dedicated assessment workspace improves organization, evidence preservation, and reporting.
* Consistent IDs and naming conventions make hypotheses, findings, requests, responses, and screenshots easier to correlate.
* A dedicated Burp project should normally be used for each assessment.
* A dedicated testing browser helps separate assessment traffic from unrelated personal traffic.
* Burp target scope reduces noise and helps prevent accidental interaction with unrelated systems.
* Intercept does not need to remain enabled continuously; normal browsing with Proxy history followed by controlled Repeater testing is often more efficient.
* Multiple controlled accounts are essential for reliable authorization testing.
* Sessions must be clearly separated so the tester always knows which identity generated a request.
* Controlled objects provide known ownership for horizontal and cross-tenant authorization tests.
* Baseline behavior should be established before abnormal behavior can be evaluated reliably.
* Evidence handling should be prepared before vulnerabilities are discovered.
* Raw requests, responses, account context, ownership, screenshots, timestamps, and final-state verification may all contribute to strong evidence.
* Credentials, cookies, tokens, API keys, personal data, and confidential client information must be handled carefully.
* Live secrets should never be committed to public repositories.
* Hypothesis trackers help replace random testing with structured investigation.
* Testing priorities should reflect the application's most important trust boundaries and business risks.
* Formal penetration tests and bug bounty programs differ operationally, but both require authorization, scope awareness, safe testing, validation, and professional documentation.
* The complete setup workflow is **confirm authorization → define scope → review rules → record restrictions → define stop conditions → create workspace → configure Burp → configure browser → set target scope → prepare accounts → separate sessions → create controlled objects → establish baselines → prepare evidence and notes → begin application mapping**.
