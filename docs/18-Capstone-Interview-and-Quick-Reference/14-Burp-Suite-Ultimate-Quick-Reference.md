# Burp Suite Ultimate Quick Reference

> **Module 18 — Capstone, Interview & Quick Reference**

> **Progress**

```text
██████████████░ 14 / 15 Files
```

---

## Overview

* This file is the rapid-reference companion for the entire Burp Suite Mastery repository.

* Use it when you need to quickly remember:

  * Which Burp tool to use.
  * What to inspect in a request.
  * How to approach a vulnerability class.
  * How to validate suspicious behavior.
  * What evidence to preserve.
  * What common mistakes to avoid.
  * How to move from reconnaissance to reporting.

* This is not a replacement for the detailed modules.

* The core methodology is:

```text
Scope

↓

Map

↓

Understand

↓

Hypothesize

↓

Baseline

↓

Mutate

↓

Compare

↓

Validate

↓

Assess Impact

↓

Collect Evidence

↓

Report
```

---

# 1 — Burp Suite Core Tools

| Tool         | Primary Purpose                       |
| ------------ | ------------------------------------- |
| Proxy        | Intercept and inspect traffic         |
| HTTP History | Review captured traffic               |
| Target       | Organize attack surface               |
| Repeater     | Manually modify and replay requests   |
| Intruder     | Automate controlled payload variation |
| Decoder      | Encode and decode data                |
| Comparer     | Compare responses or data             |
| Sequencer    | Analyze token randomness              |
| Scanner      | Identify candidate vulnerabilities    |
| Collaborator | Detect out-of-band interactions       |
| Logger       | Review detailed Burp traffic/events   |
| Organizer    | Organize interesting requests         |

---

# 2 — Tool Selection

```text
Need to inspect live traffic?

→ Proxy
```

```text
Need to review previous traffic?

→ HTTP History
```

```text
Need to understand application structure?

→ Target / Site Map
```

```text
Need to modify one request repeatedly?

→ Repeater
```

```text
Need controlled automated variation?

→ Intruder
```

```text
Need to decode data?

→ Decoder
```

```text
Need to compare subtle differences?

→ Comparer
```

```text
Need to analyze token randomness?

→ Sequencer
```

```text
Need automated candidate discovery?

→ Scanner
```

```text
Need to detect blind external interaction?

→ Collaborator
```

---

# 3 — Professional Setup Checklist

```text
[ ] Confirm written authorization

[ ] Read scope carefully

[ ] Read prohibited testing rules

[ ] Configure Burp proxy

[ ] Install Burp CA in testing browser

[ ] Verify HTTPS interception

[ ] Define Burp target scope

[ ] Filter out-of-scope traffic

[ ] Configure project storage

[ ] Prepare controlled test accounts

[ ] Prepare note-taking structure

[ ] Prepare evidence directories

[ ] Confirm testing environment
```

---

# 4 — HTTP Request Anatomy

```http
POST /api/orders/123 HTTP/1.1
Host: app.example.test
Authorization: Bearer [TOKEN]
Cookie: session=[SESSION]
Content-Type: application/json

{
  "quantity": 1
}
```

---

## Inspect

```text
Method

Path

Query String

Headers

Cookies

Authentication

Content Type

Body

Object IDs

State Values

Business Values
```

---

# 5 — HTTP Methods

| Method  | Typical Purpose               |
| ------- | ----------------------------- |
| GET     | Retrieve                      |
| POST    | Create / action               |
| PUT     | Replace                       |
| PATCH   | Modify                        |
| DELETE  | Delete                        |
| OPTIONS | Capabilities / CORS preflight |
| HEAD    | Headers without body          |

* Never assume authorization is identical across methods.

---

# 6 — Important Status Codes

```text
200 OK

201 Created

204 No Content

301 / 302 Redirect

304 Not Modified

400 Bad Request

401 Unauthenticated / authentication required

403 Forbidden

404 Not Found

405 Method Not Allowed

409 Conflict

429 Too Many Requests

500 Internal Server Error

502 Bad Gateway

503 Service Unavailable
```

---

## Remember

```text
HTTP Status

≠

Security Result
```

* Always inspect content and persistent application state.

---

# 7 — First-Pass Request Checklist

For every interesting request, ask:

```text
Who sent it?

What role?

What object?

Who owns the object?

What action occurs?

Which parameters are controllable?

Which values are security sensitive?

What server state changes?

Can the request be replayed?

Can another role perform it?

Can the sequence be changed?
```

---

# 8 — Application Mapping

```text
Browse Normally

↓

Exercise Features

↓

Capture Traffic

↓

Review Site Map

↓

Identify Endpoints

↓

Identify Parameters

↓

Identify Roles

↓

Identify Objects

↓

Identify Workflows

↓

Identify Trust Boundaries
```

---

# 9 — Attack Surface Inventory

Record:

```text
Hosts

Subdomains

Paths

API Endpoints

Methods

Parameters

Headers

Cookies

Authentication Mechanisms

Roles

Object Types

Upload Functions

URL-Fetching Features

Search Functions

Admin Functions

Integrations

WebSockets

GraphQL

OAuth / SSO

Business Workflows
```

---

# 10 — Repeater Workflow

```text
Known-Good Request

↓

Duplicate

↓

Preserve Baseline

↓

Change One Variable

↓

Send

↓

Compare

↓

Repeat

↓

Verify Final State
```

---

## Repeater Rule

```text
One Hypothesis

↓

One Controlled Change
```

---

# 11 — What to Compare

```text
Status

Length

Words

Lines

Headers

Body

Redirect

Cookies

Timing

Error Message

Returned Object

Persistent State
```

---

# 12 — Intruder Attack Types

## Sniper

```text
One payload set

One position at a time
```

Use for:

```text
Parameter testing

Input variation

Targeted fuzzing
```

---

## Battering Ram

```text
Same payload

Multiple positions
```

Use when the same value must appear in several places.

---

## Pitchfork

```text
Payload A → Position A

Payload B → Position B
```

Use for paired data.

---

## Cluster Bomb

```text
Every Combination
```

Use carefully because request counts can grow rapidly.

---

# 13 — Intruder Result Analysis

Compare:

```text
Status

Length

Words

Lines

Timing

Redirects

Errors

Extracted Values
```

---

## Rule

```text
Anomaly

→ Investigate

Not

→ Immediately Report
```

---

# 14 — Decoder Reference

Common transformations:

```text
URL Encode / Decode

Base64 Encode / Decode

Hex Encode / Decode

HTML Encode / Decode
```

---

## Remember

```text
Encoding

≠

Encryption

≠

Hashing
```

---

# 15 — Comparer Reference

Use when:

```text
Responses Look Similar

↓

Subtle Difference Matters
```

Common cases:

```text
Login responses

Authorization responses

Token changes

Error messages

Dynamic fields
```

---

# 16 — Sequencer Reference

Candidate tokens:

```text
Session IDs

Reset Tokens

CSRF Tokens

One-Time Tokens
```

Evaluate:

```text
Randomness

Predictability

Sample Size

Lifetime

Practical Exploitability
```

---

# 17 — Authentication Testing

```text
Registration

↓

Verification

↓

Login

↓

MFA

↓

Session Creation

↓

Authenticated Actions

↓

Password Change

↓

Logout

↓

Recovery
```

---

## Test

```text
[ ] User enumeration

[ ] Rate limiting

[ ] Lockout

[ ] Session creation

[ ] Session rotation

[ ] Logout invalidation

[ ] Password reset

[ ] MFA enforcement

[ ] Remember-me

[ ] Account recovery

[ ] Session expiration
```

---

# 18 — Session Testing

Check:

```text
Session Created When?

Session Rotated When?

Session Expires When?

Logout Invalidates Server-Side?

Password Change Invalidates?

Privilege Change Rotates?

Old Sessions Remain Valid?

Concurrent Sessions Allowed?
```

---

# 19 — Cookie Checklist

```text
[ ] Secure

[ ] HttpOnly

[ ] SameSite

[ ] Domain

[ ] Path

[ ] Expiration
```

* Interpret attributes in context.

---

# 20 — Authorization Testing

Three major boundaries:

```text
Object-Level

Function-Level

Property-Level
```

---

# 21 — IDOR / BOLA Workflow

```text
User A Creates Object A

↓

User B Creates Object B

↓

Capture Valid Request

↓

Switch Authentication Context

↓

Request Object A as User B

↓

Verify Authorization
```

---

## Test Actions

```text
Read

Update

Delete

Download

Export

Share

Transfer
```

---

## Rule

```text
UUID

≠

Authorization
```

---

# 22 — Vertical Authorization

```text
Admin Performs Action

↓

Capture Request

↓

Replay as Lower Role

↓

Verify Server-Side Authorization
```

Test:

```text
Hidden Admin Endpoints

Role Management

User Management

Configuration

Exports

Approvals

Privileged Actions
```

---

# 23 — Property-Level Authorization

Look for fields such as:

```text
role

is_admin

owner_id

status

verified

approved

credit

balance

permissions
```

Workflow:

```text
Valid Update

↓

Add / Modify Sensitive Property

↓

Send

↓

Retrieve Fresh State

↓

Verify Persistence and Authorization
```

---

# 24 — SQL Injection Workflow

```text
Baseline

↓

Identify Candidate Input

↓

Controlled Mutation

↓

Observe Difference

↓

Boolean Testing

↓

Timing if Appropriate

↓

Minimal Confirmation

↓

Impact
```

---

## Indicators

```text
Database Errors

Boolean Differences

Timing Differences

Unexpected Query Behavior
```

---

## Rule

```text
Error

≠

SQL Injection
```

* Confirm causality.

---

# 25 — XSS Workflow

```text
Input

↓

Reflection / Storage

↓

Output Context

↓

Encoding

↓

Browser Interpretation

↓

Execution?

↓

Victim Context

↓

Impact
```

---

## Contexts

```text
HTML Text

HTML Attribute

JavaScript

URL

DOM
```

---

## Rule

```text
Reflection

≠

XSS
```

---

# 26 — CSRF Workflow

```text
Sensitive Action

↓

Authentication Mechanism

↓

Cookie Behavior

↓

CSRF Token

↓

Origin / Referer

↓

Request Format

↓

Cross-Site Feasibility

↓

Impact
```

---

## Rule

```text
Missing CSRF Token

≠

CSRF Confirmed
```

---

# 27 — File Upload Workflow

```text
Valid Upload

↓

Extension

↓

Filename

↓

MIME Type

↓

Content

↓

Storage

↓

Retrieval

↓

Rendering / Execution

↓

Authorization
```

---

## Test

```text
Extension validation

MIME validation

Magic bytes

Filename handling

Path handling

Storage location

Public retrieval

Authorization

Content-Disposition

Active content rendering
```

---

# 28 — Path Traversal

Candidate input:

```text
file=

path=

filename=

template=

download=
```

Method:

```text
Baseline Valid Resource

↓

Modify Path

↓

Observe Canonicalization

↓

Verify Access Boundary
```

* Use harmless authorized test files.

---

# 29 — Command Injection

Look for features that may invoke system utilities:

```text
Diagnostics

File Conversion

Image Processing

Archive Handling

Network Utilities

Administrative Tools
```

Method:

```text
Baseline

↓

Controlled Input Variation

↓

Observe Behavioral Difference

↓

Use Minimal Safe Confirmation
```

* Avoid destructive operating-system commands.

---

# 30 — SSTI

Look for:

```text
Email Templates

Document Generation

Custom Messages

Preview Functions

Notification Templates
```

Method:

```text
Controlled Marker

↓

Template-Like Expression

↓

Observe Evaluation vs Literal Output

↓

Identify Context

↓

Minimal Confirmation
```

---

# 31 — XXE

Candidate surfaces:

```text
XML APIs

SOAP

Document Processing

XML Uploads

SVG Processing
```

Check:

```text
Does parser accept DTD?

Are external entities enabled?

Can external interaction occur?

What data or network access is possible?
```

* Use safe controlled resources.

---

# 32 — SSRF Workflow

```text
Find Server-Side URL Feature

↓

Controlled External Destination

↓

Confirm Server Request

↓

Analyze URL Validation

↓

Redirect Handling

↓

Protocol Restrictions

↓

Destination Restrictions

↓

Impact
```

---

## Candidate Features

```text
Webhooks

URL Preview

Image Fetch

PDF Generation

Import by URL

Callback Validation

Integrations
```

---

## Safety

```text
Do Not Probe Internal Systems

Unless Explicitly Authorized
```

---

# 33 — Open Redirect

Look for:

```text
next=

url=

redirect=

return=

continue=
```

Verify:

```text
Attacker-Controlled Destination?

↓

External Navigation?

↓

Security-Relevant Workflow?
```

---

# 34 — CORS Workflow

```text
Sensitive Endpoint

↓

Controlled Origin

↓

ACAO

↓

ACAC

↓

Credential Behavior

↓

Browser Readability

↓

Impact
```

---

## Important Headers

```text
Access-Control-Allow-Origin

Access-Control-Allow-Credentials
```

---

## Rule

```text
Permissive Header

≠

Exploitable CORS
```

---

# 35 — Host Header Testing

Inspect use of:

```text
Host

X-Forwarded-Host

Forwarded
```

Candidate impact:

```text
Password Reset Links

Absolute URL Generation

Cache Behavior

Routing
```

* Validate actual downstream trust.

---

# 36 — HTTP Request Smuggling

Concept:

```text
Front End

and

Back End

Disagree About Request Boundaries
```

Possible parsing patterns include:

```text
CL.TE

TE.CL

TE.TE
```

* This is high-risk testing.

* Prefer isolated environments and explicit authorization.

---

# 37 — Web Cache Poisoning

```text
Unkeyed Input

↓

Changes Response

↓

Response Cached

↓

Other Requests Receive Modified Response
```

Test carefully with:

```text
Harmless Marker

Unique Cache Key

Controlled Endpoint

Minimal Requests
```

---

# 38 — Web Cache Deception

```text
Sensitive Dynamic Response

↓

Cacheable-Looking Path

↓

Shared Cache Stores Response?

↓

Unauthorized Retrieval?
```

* Use only controlled accounts and non-sensitive test data.

---

# 39 — WebSockets

Test:

```text
Handshake Authentication

Origin

Session Binding

Message Authorization

Object Authorization

Input Validation

Replay

State Changes
```

---

## Rule

```text
Authenticated Handshake

≠

Every Message Authorized
```

---

# 40 — REST API Checklist

```text
[ ] Endpoint inventory

[ ] Methods

[ ] Authentication

[ ] Object authorization

[ ] Function authorization

[ ] Property authorization

[ ] Input validation

[ ] Rate limits

[ ] Pagination

[ ] Filtering

[ ] Error handling

[ ] Versioning

[ ] Business logic
```

---

# 41 — GraphQL Checklist

Inspect:

```text
Endpoint

Queries

Mutations

Variables

Object IDs

Nested Fields

Authorization

Introspection

Batching

Aliases

Depth / Complexity
```

---

## Core Question

```text
Is Authorization Enforced

at Every Resolver / Object Boundary?
```

---

# 42 — Mass Assignment

```text
Valid Object Update

↓

Identify Expected Fields

↓

Add Candidate Sensitive Field

↓

Send

↓

Retrieve Fresh Object

↓

Verify Unauthorized Persistence
```

---

# 43 — JWT Quick Reference

Structure:

```text
Header.Payload.Signature
```

Review:

```text
Algorithm

Signature Validation

Expiration

Issuer

Audience

Subject

Claims

Key Selection

Token Type
```

---

## Rule

```text
JWT Decodable

≠

JWT Insecure
```

---

# 44 — OAuth / SSO Checklist

Map:

```text
Client

Authorization Server

Resource Server

Browser

Redirect URI

Authorization Code

Tokens

Session
```

Test:

```text
redirect_uri

state

PKCE

Account Linking

Identity Binding

Session Binding

Token Handling

Logout
```

---

# 45 — Business Logic Workflow

```text
Understand Business Rule

↓

Define Invariant

↓

Map Normal Workflow

↓

Capture Baseline

↓

Mutate Sequence / Value / State

↓

Verify Final State
```

---

## Test

```text
Step Skipping

Reordering

Replay

Duplicate Submission

Stale Requests

Boundary Values

Coupons

Credits

Refunds

Approvals

Cross-Feature Interactions
```

---

# 46 — Race Condition Workflow

```text
Define Invariant

↓

Find Shared State

↓

Sequential Baseline

↓

Prepare Equivalent Requests

↓

Minimal Synchronized Dispatch

↓

Verify Persistent Final State

↓

Compare Sequential vs Concurrent
```

---

## Rule

```text
Two 200 Responses

≠

Race Condition
```

---

# 47 — Race Candidates

```text
Coupons

Credits

Balances

Rewards

Inventory

Limits

Invitations

Refunds

Transfers

One-Time Actions

Unique Resource Creation
```

---

# 48 — Idempotency

Check:

```text
Can Request Be Safely Retried?

Is There an Idempotency Key?

Is It Bound to User?

Is It Bound to Request?

Can It Be Reused?

Does Retry Duplicate State?
```

---

# 49 — Rate Limiting

Test safely:

```text
Baseline Request

↓

Small Controlled Sequence

↓

Observe Threshold

↓

Observe Response

↓

Wait / Reset

↓

Verify Enforcement
```

Inspect:

```text
Per Account

Per Session

Per Endpoint

Per Action

Time Window
```

* Do not attempt high-volume denial-of-service testing.

---

# 50 — Password Reset

Map:

```text
Request Reset

↓

Token Generated

↓

Token Delivered

↓

Token Validated

↓

Password Changed

↓

Token Invalidated

↓

Existing Sessions Handled
```

Test:

```text
Token reuse

Expiration

Account binding

Single use

Session invalidation

Host / URL generation

User enumeration
```

---

# 51 — MFA

Test:

```text
Enrollment

Challenge

Verification

Recovery

Disable Flow

Trusted Device

Session Upgrade
```

Ask:

```text
Can MFA Step Be Skipped?

Can Old Session Bypass MFA?

Are Recovery Codes Single Use?

Can MFA Be Disabled Without Reauthentication?
```

---

# 52 — Sensitive Workflow Matrix

| Workflow       | Key Security Question                |
| -------------- | ------------------------------------ |
| Login          | Can identity controls be bypassed?   |
| Password reset | Can account ownership be bypassed?   |
| MFA            | Can second factor be skipped?        |
| Profile update | Can sensitive properties be changed? |
| Payment        | Can value or state be manipulated?   |
| Refund         | Can value be duplicated or exceeded? |
| Admin          | Can lower roles invoke functions?    |
| File access    | Is ownership enforced?               |
| Invitation     | Can membership rules be bypassed?    |
| OAuth          | Is identity securely bound?          |

---

# 53 — Burp Scanner Workflow

```text
Candidate

↓

Review Evidence

↓

Send to Repeater

↓

Baseline

↓

Manual Reproduction

↓

Validate Boundary Failure

↓

Determine Impact

↓

Report or Reject
```

---

# 54 — Collaborator Workflow

```text
Generate Unique Collaborator Payload

↓

Place in Candidate Input

↓

Send Request

↓

Poll Interactions

↓

Correlate Unique Identifier

↓

Determine Interaction Type

↓

Validate Vulnerability Context
```

---

## Possible Interactions

```text
DNS

HTTP

SMTP
```

* An interaction proves external communication occurred, not automatically the full impact of a vulnerability.

---

# 55 — JavaScript Analysis

Look for:

```text
API Routes

Hidden Endpoints

Feature Flags

Object Names

Parameter Names

WebSocket URLs

GraphQL Endpoints

Authentication Logic

Client-Side Roles

Source Maps

Hardcoded Secrets
```

---

## Rule

```text
Secret-Looking String

≠

Valid Sensitive Secret
```

* Validate type, scope, exposure, and actual privilege safely.

---

# 56 — Security Headers

Common headers:

```text
Content-Security-Policy

Strict-Transport-Security

X-Content-Type-Options

Referrer-Policy

Permissions-Policy

Cross-Origin-Opener-Policy
```

* Missing headers should be evaluated in context rather than automatically treated as high-severity vulnerabilities.

---

# 57 — Input Classification

For each input, classify:

```text
Identifier

Search

Filename

URL

HTML

JSON

XML

Numeric Value

Boolean

Role

State

Token

Redirect

Header
```

* Testing should follow the input's context.

---

# 58 — Parameter Discovery

Check:

```text
Query Parameters

Form Fields

JSON Keys

XML Elements

Headers

Cookies

Path Segments

GraphQL Variables

WebSocket Messages
```

---

# 59 — Hidden Parameters

Sources:

```text
JavaScript

Old Requests

API Documentation

Mobile Clients

HTML Forms

Source Maps

Alternate API Versions
```

* Test only within authorized scope.

---

# 60 — Response Analysis

Look for:

```text
Sensitive Data

Object IDs

Internal Paths

Stack Traces

Version Information

Tokens

Debug Data

Hidden Fields

Role Information

Internal Hostnames
```

---

# 61 — Error Analysis

Compare:

```text
Valid Input

Invalid Input

Unauthorized Input

Malformed Input

Boundary Input
```

* Errors may reveal implementation details but must be evaluated for actual security impact.

---

# 62 — Role Matrix

Example:

| Function          | Guest | User | Manager | Admin |
| ----------------- | :---: | :--: | :-----: | :---: |
| View own profile  |   ❌   |   ✅  |    ✅    |   ✅   |
| View another user |   ❌   |   ?  |    ?    |   ✅   |
| Approve request   |   ❌   |   ❌  |    ?    |   ✅   |
| Manage roles      |   ❌   |   ❌  |    ❌    |   ✅   |

* Replace `?` through controlled testing.

---

# 63 — Object Matrix

| Object  | Owner  | Read | Update | Delete | Share |
| ------- | ------ | ---- | ------ | ------ | ----- |
| Profile | User A | Test | Test   | —      | —     |
| Invoice | User A | Test | —      | —      | Test  |
| File    | User A | Test | Test   | Test   | Test  |

---

# 64 — State Transition Matrix

Example:

```text
Draft

↓

Pending

↓

Approved

↓

Completed
```

Test:

```text
Draft → Approved?

Draft → Completed?

Rejected → Approved?

Completed → Pending?

Cancelled → Completed?
```

---

# 65 — Baseline Discipline

Before every mutation:

```text
Know:

Who You Are

What Object You Own

What State Exists

What Response Is Expected
```

---

## Golden Rule

```text
No Baseline

=

Weak Conclusion
```

---

# 66 — Validation Framework

```text
Observation

↓

Hypothesis

↓

Control Request

↓

Single Mutation

↓

Repeat

↓

Independent Verification

↓

Security Boundary Failure?

↓

Impact?
```

---

# 67 — False Positive Reduction

Ask:

```text
Is It Reproducible?

Does It Require the Mutation?

Does a Control Behave Differently?

Did Persistent State Change?

Is the Behavior Intended?

Is the Security Boundary Clear?

Is There Demonstrated Impact?
```

---

# 68 — Evidence Checklist

For confirmed findings:

```text
[ ] Finding ID

[ ] Baseline request

[ ] Baseline response

[ ] Test request

[ ] Test response

[ ] Authentication context

[ ] Object ownership

[ ] Modified value highlighted

[ ] Persistent final state

[ ] Screenshot if useful

[ ] Impact evidence

[ ] Sensitive data redacted
```

---

# 69 — Evidence Chain

```text
Expected Rule

↓

Baseline

↓

Mutation

↓

Observed Difference

↓

Persistent Result

↓

Security Impact
```

---

# 70 — Vulnerability Report Template

```text
# F001 — Finding Title

Severity:

Affected Component:

## Description

## Prerequisites

## Steps to Reproduce

## Observed Result

## Expected Result

## Impact

## Evidence

## Root Cause

## Remediation

## References

## Retest Status
```

---

# 71 — Finding Title Formula

```text
Vulnerability Class

+

Affected Function

+

Impact
```

Example:

```text
Broken Object-Level Authorization Allows Cross-User Invoice Access
```

---

# 72 — Impact Rule

```text
Report:

What You Demonstrated
```

Not:

```text
The Worst Thing You Can Imagine
```

---

# 73 — Severity Questions

Ask:

```text
Who Can Exploit It?

What Access Is Required?

Is User Interaction Required?

What Data Is Exposed?

What Can Be Modified?

How Broad Is the Scope?

Is Exploitation Reliable?

What Is the Business Context?
```

---

# 74 — Root Cause

Good root causes describe failed controls:

```text
Missing Object-Level Authorization

Missing Server-Side Validation

Unsafe Output Encoding

Weak Session Invalidation

Unsafe Object Binding

Missing Transaction Atomicity

Insecure URL Validation

Inconsistent Authorization Across Versions
```

---

# 75 — Remediation

```text
Root Cause

↓

Required Security Control

↓

Server-Side Enforcement Point

↓

Defense in Depth
```

---

# 76 — Retesting

```text
Original Finding

↓

Original Reproduction

↓

Verify Fix

↓

Test Nearby Variants

↓

Verify Root Cause

↓

Check Regression

↓

Record Status
```

Statuses:

```text
Fixed

Partially Fixed

Not Fixed

Unable to Retest
```

---

# 77 — Testing Priority

When time is limited:

```text
Authentication

↓

Authorization

↓

High-Value APIs

↓

Sensitive Data

↓

Business-Critical Workflows

↓

High-Risk Inputs

↓

Advanced Surfaces

↓

Hardening
```

* Adjust according to the actual application and threat model.

---

# 78 — High-Value Targets

Look closely at:

```text
Admin Functions

Account Settings

Password Reset

MFA

Payments

Refunds

Credits

File Access

Exports

Invitations

Role Management

API Keys

Integrations

Webhooks

OAuth

Sensitive Search

Bulk Operations
```

---

# 79 — Common Trust Boundaries

```text
Guest → User

User A → User B

User → Admin

Browser → Server

API Client → API

Tenant A → Tenant B

Application → Internal Service

Application → Third Party

Cache → Origin

Front End → Back End
```

---

# 80 — Testing Questions

For every feature:

```text
Can I Access It?

Can Another User Access It?

Can a Lower Role Access It?

Can I Change the Object?

Can I Change Hidden Fields?

Can I Replay It?

Can I Skip a Step?

Can I Change the Order?

Can I Duplicate It?

Can I Send an Unexpected Value?

Can I Trigger It Cross-Site?

Can I Make the Server Contact Something?

Can Timing Change the Result?
```

---

# 81 — Common Mistakes

```text
Scanning Before Understanding

Testing Without Scope

No Baseline

Changing Too Many Variables

Trusting HTTP 200

Trusting Scanner Output

Treating Reflection as XSS

Treating UUID as Authorization

Treating Missing CSRF Token as CSRF

Treating Two 200s as Race Condition

Ignoring Persistent State

Ignoring Roles

Ignoring API Versions

Ignoring Business Logic

Overtesting Production

Collecting Weak Evidence

Overstating Impact
```

---

# 82 — Professional Testing Rules

```text
Authorization Before Testing

Scope Before Requests

Understand Before Attacking

Baseline Before Mutation

One Variable at a Time

Manual Validation Before Reporting

Minimum Necessary Impact

Evidence Before Conclusions

Root Cause Before Remediation

Safety Before Curiosity
```

---

# 83 — Burp Workflow by Phase

## Phase A — Setup

```text
Scope

Proxy

CA

Project

Accounts
```

## Phase B — Map

```text
Browse

HTTP History

Site Map

Endpoints

Roles

Objects

Workflows
```

## Phase C — Analyze

```text
Authentication

Sessions

Authorization

Inputs

APIs

Business Logic
```

## Phase D — Test

```text
Repeater

Intruder

Scanner

Collaborator

Extensions
```

## Phase E — Validate

```text
Baseline

Control

Mutation

Repeat

Persistent State
```

## Phase F — Report

```text
Evidence

Impact

Severity

Root Cause

Remediation
```

---

# 84 — Full Web Pentest Checklist

```text
SCOPE

[ ] Authorization
[ ] Domains
[ ] APIs
[ ] Roles
[ ] Exclusions
[ ] Prohibited tests


MAPPING

[ ] Browse application
[ ] Site Map
[ ] Endpoints
[ ] Parameters
[ ] JavaScript
[ ] APIs
[ ] Roles
[ ] Objects
[ ] Workflows


AUTHENTICATION

[ ] Registration
[ ] Login
[ ] Enumeration
[ ] Rate limits
[ ] MFA
[ ] Password reset
[ ] Recovery


SESSIONS

[ ] Creation
[ ] Rotation
[ ] Cookies
[ ] Expiration
[ ] Logout
[ ] Password change
[ ] Privilege change


AUTHORIZATION

[ ] Horizontal
[ ] Vertical
[ ] Object-level
[ ] Function-level
[ ] Property-level
[ ] Multi-tenant


INPUT

[ ] SQLi
[ ] XSS
[ ] Command injection
[ ] SSTI
[ ] XXE
[ ] Path traversal
[ ] File upload


SERVER-SIDE

[ ] SSRF
[ ] URL validation
[ ] Redirect handling
[ ] Host header


BROWSER

[ ] CSRF
[ ] CORS
[ ] Open redirect
[ ] Clickjacking context
[ ] Security headers


API

[ ] REST
[ ] GraphQL
[ ] BOLA
[ ] BFLA
[ ] Property authorization
[ ] Mass assignment
[ ] Rate limits
[ ] Versioning


MODERN

[ ] WebSockets
[ ] JWT
[ ] OAuth
[ ] SSO


LOGIC

[ ] Step skipping
[ ] Replay
[ ] Stale requests
[ ] Boundaries
[ ] Coupons
[ ] Credits
[ ] Refunds
[ ] Approvals
[ ] Race conditions
[ ] Idempotency


ADVANCED

[ ] Request smuggling where authorized
[ ] Cache behavior
[ ] OOB testing
[ ] Advanced parser differences


REPORTING

[ ] Validate
[ ] Evidence
[ ] Impact
[ ] Severity
[ ] Root cause
[ ] Remediation
[ ] QA
```

---

# 85 — Interview Rapid Recall

## Proxy

```text
Intercept and inspect traffic
```

## Target

```text
Organize attack surface
```

## Repeater

```text
Manual controlled request testing
```

## Intruder

```text
Automated payload variation
```

## Decoder

```text
Transform encoded data
```

## Comparer

```text
Find subtle differences
```

## Sequencer

```text
Analyze token randomness
```

## Scanner

```text
Generate vulnerability hypotheses
```

## Collaborator

```text
Detect out-of-band interactions
```

---

# 86 — Interview Answer Formula

```text
Concept

↓

Methodology

↓

Burp Workflow

↓

Validation

↓

Impact

↓

Safety
```

---

# 87 — Scenario Formula

If asked:

> How would you test X?

Answer:

```text
Understand X

↓

Capture Valid Baseline

↓

Identify Security Boundary

↓

Change One Relevant Variable

↓

Compare Behavior

↓

Verify Persistent State

↓

Demonstrate Minimal Impact

↓

Preserve Evidence
```

---

# 88 — Rapid Security Rules

```text
Authentication

≠

Authorization
```

```text
Encoding

≠

Encryption
```

```text
UUID

≠

Access Control
```

```text
HTTP 200

≠

Successful Exploitation
```

```text
Reflection

≠

XSS
```

```text
Error

≠

SQL Injection
```

```text
Missing CSRF Token

≠

CSRF
```

```text
Permissive CORS Header

≠

Exploitable CORS
```

```text
Scanner Finding

≠

Confirmed Vulnerability
```

```text
Two Successful Responses

≠

Race Condition
```

```text
External Interaction

≠

Maximum SSRF Impact
```

---

# 89 — Five Questions Before Reporting

```text
1. Can I reproduce it?

2. What exact security rule failed?

3. What did I actually demonstrate?

4. What evidence proves it?

5. What root cause should be fixed?
```

---

# 90 — Five Questions Before High-Risk Testing

```text
1. Is this explicitly authorized?

2. Could this affect other users?

3. Can I prove it with less impact?

4. Is an isolated environment available?

5. What is my stop condition?
```

---

# 91 — Note-Taking Template

```text
Endpoint:

Method:

Role:

Object:

Owner:

Baseline:

Hypothesis:

Mutation:

Observed:

Expected:

Impact:

Evidence:

Status:
```

---

# 92 — Investigation Status

Use:

```text
TODO

TESTING

CANDIDATE

CONFIRMED

REJECTED

NEEDS EVIDENCE

REPORTED

RETESTED
```

---

# 93 — Evidence Naming

```text
F001/
│
├── 01-context.md
├── 02-baseline-request.txt
├── 03-baseline-response.txt
├── 04-test-request.txt
├── 05-test-response.txt
├── 06-impact.png
└── notes.md
```

---

# 94 — Daily Assessment Rhythm

```text
Start

↓

Review Scope

↓

Review Previous Notes

↓

Map New Surface

↓

Test High-Priority Hypotheses

↓

Validate Candidates

↓

Organize Evidence

↓

Update Finding Register

↓

Plan Next Session
```

---

# 95 — If You Get Stuck

```text
Return to Baseline

↓

Re-read Request

↓

Identify Trust Boundary

↓

Compare Roles

↓

Compare Objects

↓

Compare States

↓

Check JavaScript

↓

Check Related Endpoints

↓

Check Alternate Methods

↓

Check API Versions

↓

Map the Workflow Again
```

---

# 96 — If Nothing Is Found

Do not randomly increase payload volume.

Instead:

```text
Improve Mapping

↓

Understand Business Logic

↓

Create Second Identity

↓

Compare Roles

↓

Trace Object Lifecycles

↓

Review JavaScript

↓

Inspect APIs

↓

Test State Transitions

↓

Analyze Feature Interactions
```

---

# 97 — Minimal Professional Methodology

If you remember only one workflow:

```text
Scope

↓

Map the Application

↓

Understand Roles and Objects

↓

Test Authentication

↓

Test Sessions

↓

Test Authorization

↓

Test Inputs

↓

Test APIs

↓

Test Business Logic

↓

Investigate Advanced Surfaces

↓

Validate Manually

↓

Collect Evidence

↓

Report Root Cause and Impact
```

---

# 98 — Core Burp Mindset

```text
Burp Suite

≠

A Button That Finds Vulnerabilities
```

```text
Burp Suite

=

A Workbench for Security Investigation
```

* The tester supplies:

```text
Context

Hypotheses

Judgment

Validation

Impact Analysis
```

---

# 99 — Final Master Checklist

Before considering an assessment complete:

```text
[ ] Scope was respected

[ ] Application was fully mapped

[ ] Important roles were tested

[ ] Important objects were tested

[ ] Authentication lifecycle was reviewed

[ ] Sessions were reviewed

[ ] Horizontal authorization was tested

[ ] Vertical authorization was tested

[ ] Property authorization was tested

[ ] High-value inputs were tested

[ ] APIs were mapped and tested

[ ] Business workflows were tested

[ ] State transitions were tested

[ ] Replay was tested

[ ] Relevant race candidates were considered

[ ] Advanced surfaces were evaluated where applicable

[ ] Scanner candidates were manually validated

[ ] False positives were rejected

[ ] Findings have reproducible evidence

[ ] Impact is demonstrated

[ ] Severity is justified

[ ] Root causes are identified

[ ] Remediation is actionable

[ ] Sensitive evidence is redacted

[ ] Report QA is complete
```

---

# 100 — The Burp Suite Master Formula

```text
Professional Burp Testing

=

HTTP Understanding

+

Application Mapping

+

Controlled Request Manipulation

+

Security Boundary Analysis

+

Manual Validation

+

Business Context

+

Evidence

+

Professional Judgment
```

---

# One-Minute Revision

```text
Proxy

→ Capture
```

```text
Target

→ Map
```

```text
Repeater

→ Investigate
```

```text
Intruder

→ Automate targeted variation
```

```text
Decoder

→ Transform
```

```text
Comparer

→ Compare
```

```text
Sequencer

→ Analyze randomness
```

```text
Scanner

→ Generate hypotheses
```

```text
Collaborator

→ Detect OOB interactions
```

```text
Tester

→ Understand + Validate + Judge
```

---

# Final Principles

1. **Know your scope before sending requests.**

2. **Understand the application before aggressively testing it.**

3. **Build a known-good baseline before modifying anything.**

4. **Change one meaningful variable at a time.**

5. **Use multiple controlled identities for authorization testing.**

6. **Treat automated findings as hypotheses until manually validated.**

7. **Never confuse response differences with proven security impact.**

8. **Verify persistent server state whenever the vulnerability changes data or workflow state.**

9. **Test the security boundary, not merely the parameter.**

10. **Prefer minimal, safe proof over unnecessary exploitation.**

11. **Use Burp tools according to a hypothesis rather than randomly.**

12. **Think in terms of users, roles, objects, states, workflows, and trust boundaries.**

13. **Investigate root causes rather than collecting isolated symptoms.**

14. **Preserve evidence as you test rather than reconstructing it later.**

15. **Report demonstrated impact accurately without exaggeration.**

16. **A professional pentester is defined by methodology and judgment—not by the number of payloads sent.**

---

# Key Takeaways

* Burp Suite is most effective when used as an integrated security investigation workbench.
* Proxy captures and controls traffic, Target organizes attack surface, Repeater supports manual investigation, and Intruder provides targeted automation.
* Decoder, Comparer, and Sequencer support specialized analysis rather than replacing security reasoning.
* Scanner findings should be treated as hypotheses requiring manual validation.
* Collaborator helps identify blind external interactions, but interaction alone does not establish maximum vulnerability impact.
* Professional testing begins with scope, application mapping, roles, objects, workflows, and trust boundaries.
* A clean baseline is essential before meaningful request manipulation.
* Authentication, authorization, and session management must be tested as separate security concerns.
* Object-level, function-level, and property-level authorization should all be evaluated.
* Input testing must follow context rather than rely on generic payload spraying.
* Modern applications require attention to REST APIs, GraphQL, WebSockets, JWTs, OAuth, and client-side JavaScript.
* Business logic testing focuses on violating intended rules through legitimate-looking requests and unexpected workflow sequences.
* Race conditions require concurrency-dependent violations of persistent business invariants.
* High-risk techniques require explicit authorization, minimal-impact validation, and clear stop conditions.
* HTTP status codes, reflection, scanner output, permissive headers, or anomalous responses are signals—not vulnerabilities by themselves.
* Strong validation combines a known baseline, controlled mutation, repeatability, independent verification, and demonstrated security impact.
* Strong evidence connects the expected security rule to the baseline, mutation, observed behavior, persistent result, and impact.
* Professional reports explain the failed security boundary, reproducible steps, demonstrated impact, root cause, and actionable remediation.
* When testing stalls, deeper application understanding usually provides more value than simply increasing payload volume.
* The master methodology is **scope → map → understand → hypothesize → baseline → mutate → compare → validate → assess impact → preserve evidence → report**.
