# Modern Application Testing Capstone

> **Module 16 — Modern Application Testing**

> **Progress**

```text
█████████████ 13 / 13 Files
```

---

## Overview

* This capstone brings together the complete **Modern Application Testing** methodology covered throughout Module 16.

* Modern applications are rarely simple collections of HTML pages and form submissions.

* A single application may contain:

  ```text
  Web Interface

  +

  REST APIs

  +

  JavaScript-Driven Functionality

  +

  JWT Authentication

  +

  OAuth / OIDC Flows

  +

  File Uploads

  +

  WebSockets

  +

  GraphQL

  +

  Multi-Step Business Workflows
  ```

* Testing each technology independently is useful.

* Professional penetration testing requires something more:

  ```text
  Connecting the Technologies

  ↓

  Understanding Trust Boundaries

  ↓

  Mapping Application Workflows

  ↓

  Testing Security Invariants

  ↓

  Chaining Related Weaknesses

  ↓

  Demonstrating Real Impact
  ```

* The purpose of this capstone is to simulate how a professional tester investigates a modern application from discovery through evidence collection and reporting.

---

## Learning Objectives

* By completing this capstone, you will be able to:

  * Build a complete application attack-surface map.
  * Identify modern application architectures.
  * Map frontend and backend communication.
  * Analyze API endpoints systematically.
  * Map authentication and authorization boundaries.
  * Test REST APIs.
  * Investigate JWT-based authentication.
  * Analyze OAuth and OIDC flows.
  * Test object-level authorization.
  * Test function-level and field-level authorization.
  * Investigate mass assignment.
  * Test business logic and workflow enforcement.
  * Analyze race-condition candidates safely.
  * Test file upload and file handling.
  * Investigate WebSocket security.
  * Test GraphQL APIs.
  * Correlate behavior across multiple protocols.
  * Identify trust-boundary failures.
  * Distinguish anomalies from vulnerabilities.
  * Build evidence-driven findings.
  * Prioritize vulnerabilities by realistic impact.
  * Produce a professional assessment summary.

---

## Capstone Philosophy

* Do not approach the target as:

  ```text
  Run Tool

  ↓

  Find Payload

  ↓

  Report
  ```

* Approach it as:

  ```text
  Understand

  ↓

  Map

  ↓

  Form Hypothesis

  ↓

  Establish Baseline

  ↓

  Change One Variable

  ↓

  Compare

  ↓

  Verify Final State

  ↓

  Prove Impact

  ↓

  Document
  ```

---

## Core Principle

```text
Professional Testing

=

Understanding System Behavior

+

Testing Security Assumptions

+

Evidence
```

---

## Authorization Requirement

* Perform this capstone only against:

  ```text
  A Deliberately Vulnerable Lab

  A Local Application You Control

  A System You Own

  or

  A Target You Have Explicit Authorization to Test
  ```

* Do not perform destructive, disruptive, or high-volume testing unless explicitly permitted.

---

## Capstone Scenario

* Assume you are assessing a modern application called:

  ```text
  NovaDesk
  ```

* NovaDesk is a fictional multi-tenant collaboration platform.

* The application contains:

  ```text
  User Accounts

  Organizations

  Projects

  Documents

  File Attachments

  Notifications

  Real-Time Collaboration

  REST APIs

  GraphQL

  JWT-Based API Authentication

  OAuth Login

  WebSocket Events
  ```

* Example roles:

  ```text
  Member

  Manager

  Administrator
  ```

* Your objective is not simply to find isolated technical issues.

* Your objective is to answer:

  ```text
  Can One User Access Another User's Data?

  Can One Tenant Cross Into Another Tenant?

  Can Lower-Privilege Users Perform Restricted Actions?

  Can Client-Controlled Data Override Security Decisions?

  Can Workflows Be Manipulated?

  Can Real-Time Channels Leak Data?

  Can Files Be Accessed or Processed Unsafely?

  Can Authentication Boundaries Be Bypassed?

  Can Multiple Weaknesses Be Chained?
  ```

---

## Capstone Rules

* During the assessment:

  * Use controlled test accounts.
  * Use controlled objects.
  * Establish legitimate baselines first.
  * Change one variable at a time.
  * Keep concurrency tests minimal.
  * Avoid destructive payloads.
  * Avoid real malware.
  * Avoid resource-exhaustion testing.
  * Verify backend state after state-changing operations.
  * Record evidence as you work.
  * Restore modified test data where practical.

---

## Recommended Test Accounts

* Create or obtain authorized accounts representing:

  ```text
  User A

  Tenant A

  Role: Member
  ```

  ```text
  User B

  Tenant A

  Role: Member
  ```

  ```text
  User C

  Tenant B

  Role: Member
  ```

  ```text
  User D

  Tenant A

  Role: Manager or Administrator
  ```

* This gives you controlled identities for testing:

  ```text
  Horizontal Authorization

  Vertical Authorization

  Cross-Tenant Authorization
  ```

---

## Assessment Workspace

* Create an investigation structure:

  ```text
  Modern-App-Capstone/
  │
  ├── 01-Scope/
  ├── 02-Architecture/
  ├── 03-Endpoints/
  ├── 04-Authentication/
  ├── 05-Authorization/
  ├── 06-Business-Logic/
  ├── 07-Files/
  ├── 08-WebSockets/
  ├── 09-GraphQL/
  ├── 10-Evidence/
  └── 11-Report/
  ```

---

## Master Investigation Journal

```text
Application:

Assessment Date:

Scope:

Authorized Accounts:

Roles:

Tenants:

Primary Domains:

API Domains:

GraphQL Endpoint:

WebSocket Endpoint:

Authentication Methods:

Token Types:

OAuth / OIDC Providers:

File Upload Features:

Critical Workflows:

Sensitive Objects:

High-Risk Operations:

Validated Findings:

Potential Findings:

False Positives:

Evidence Locations:

Next Steps:
```

---

## Phase 1 — Define Scope

* Before testing, record:

  ```text
  In-Scope Hosts

  In-Scope APIs

  Allowed Accounts

  Allowed Test Techniques

  Rate Limits

  Excluded Actions

  Out-of-Scope Systems
  ```

---

## Scope Questions

```text
Can Authentication Be Tested?

Can Multiple Accounts Be Used?

Can File Uploads Be Tested?

Can Race Conditions Be Tested?

Can OAuth Flows Be Tested?

Are Third-Party Providers Out of Scope?

Can WebSocket Messages Be Modified?

Can GraphQL Complexity Be Tested?

What Data Must Not Be Accessed?
```

---

## Scope Invariant

```text
Technical Capability

≠

Authorization to Test
```

---

## Phase 2 — Map the Application Architecture

* Use the application normally before attempting unusual behavior.

* Identify:

  ```text
  Frontend

  REST API

  GraphQL

  WebSockets

  Authentication Provider

  File Storage

  CDN

  Third-Party Services
  ```

---

## Architecture Map

```text
Browser

│

├── Web Frontend
│
├── REST API
│
├── GraphQL API
│
├── WebSocket Service
│
├── Authentication / OAuth
│
└── File Storage / CDN
```

* Replace this conceptual map with the actual architecture you observe.

---

## Architecture Test Card

```text
Component:

Hostname:

Protocol:

Purpose:

Authentication:

Sensitive Data:

Trust Boundary:

Related Components:

Notes:
```

---

## Questions to Answer

```text
Where Is Authentication Performed?

Which Component Issues Tokens?

Which APIs Trust Those Tokens?

Where Are Authorization Decisions Made?

Where Are Files Stored?

Which Services Receive User-Controlled IDs?

Which Components Communicate Across Tenants?

Which Features Use WebSockets?

Which Features Use GraphQL?
```

---

## Phase 3 — Build an Endpoint Inventory

* Capture normal traffic through Burp Suite.

* Inventory:

  ```text
  Endpoint

  Method

  Purpose

  Authentication

  Parameters

  Object IDs

  Role Requirements

  Tenant Context

  State Change
  ```

---

## Endpoint Inventory Template

```text
Endpoint:

Method:

Protocol:

Purpose:

Authentication Required?:

Role:

Tenant:

Parameters:

Object IDs:

Sensitive Fields:

State-Changing?:

Notes:
```

---

## Categorize Endpoints

```text
Authentication

Account

Users

Organizations

Projects

Documents

Files

Billing

Administration

Search

Imports / Exports

Notifications

GraphQL

WebSockets
```

---

## High-Risk Endpoints

* Prioritize operations involving:

  ```text
  Authentication

  Password Changes

  Role Changes

  Invitations

  Ownership

  Payments

  File Access

  Administrative Actions

  Sensitive Data

  Cross-Tenant Objects
  ```

---

## Phase 4 — Map Authentication

* Identify every authentication mechanism.

* Examples:

  ```text
  Session Cookie

  JWT

  OAuth

  OIDC

  API Token

  Refresh Token
  ```

---

## Authentication Map

```text
Login

↓

Credential Validation / OAuth

↓

Session or Token Issued

↓

Client Stores Credential

↓

Credential Sent to API

↓

Authorization Decisions
```

---

## Authentication Test Card

```text
Login Endpoint:

Authentication Method:

Session Cookie:

Access Token:

Refresh Token:

Token Storage:

Logout Endpoint:

Password Change:

Reset Flow:

OAuth Provider:

Redirect URI:

State Parameter:

Nonce:

PKCE:

Notes:
```

---

## Authentication Tests

* Review:

  ```text
  Login

  Logout

  Session Expiration

  Token Expiration

  Refresh

  Password Change

  Account Disablement

  Multiple Sessions

  Role Changes
  ```

---

## Authentication Invariant

```text
Invalidated Identity

Must Not Retain

Unexpected Security-Sensitive Access
```

---

## Phase 5 — Analyze JWTs

* If JWTs are used, record:

  ```text
  Header

  Payload

  Signature Algorithm

  iss

  aud

  sub

  exp

  nbf

  iat

  roles / permissions
  ```

---

## JWT Questions

```text
Is the Signature Verified?

Is the Expected Algorithm Enforced?

Is Expiration Enforced?

Is Issuer Validated?

Is Audience Validated?

Are Tokens Accepted Across Services Unexpectedly?

Are Role Claims Trusted Safely?

Are Sensitive Secrets Stored in the Payload?
```

---

## JWT Test Matrix

```text
Valid Token

Expired Token

Malformed Token

Modified Payload

Wrong Audience

Wrong Issuer

Logged-Out Token

Token From Different Controlled User
```

* Do not attempt destructive token attacks outside scope.

---

## Important Principle

```text
JWT Payload

Is Encoded

Not Automatically Secret
```

---

## Phase 6 — Analyze OAuth / OIDC

* If OAuth or OIDC is used, map:

  ```text
  Authorization Request

  Redirect URI

  state

  nonce

  PKCE

  Authorization Code

  Token Exchange

  Account Linking
  ```

---

## OAuth Test Questions

```text
Is state Validated?

Is Redirect URI Strictly Validated?

Is PKCE Used Where Appropriate?

Are Authorization Codes Bound Correctly?

Can Accounts Be Linked Incorrectly?

Can Login CSRF Occur?

Are Tokens Exposed Through URLs or Logs?

Are Third-Party Identities Mapped Safely?
```

---

## Important Scope Rule

```text
Test the Target Application's OAuth Integration

Not the Third-Party Provider

Unless Explicitly Authorized
```

---

## Phase 7 — Build an Authorization Matrix

* Authorization is one of the highest-value areas of modern application testing.

* Create a matrix:

| Action                     | Member   | Manager        | Admin    | Cross-Tenant |
| -------------------------- | -------- | -------------- | -------- | ------------ |
| View Own Project           | Expected | Expected       | Expected | Depends      |
| View Other Private Project | Depends  | Depends        | Depends  | Denied       |
| Edit Member Role           | Denied   | Depends        | Expected | Denied       |
| Delete Organization        | Denied   | Denied/Depends | Expected | Denied       |

* Adapt the matrix to actual application rules.

---

## Test Three Authorization Dimensions

```text
Horizontal

↓

User A vs User B
```

```text
Vertical

↓

Member vs Administrator
```

```text
Tenant

↓

Tenant A vs Tenant B
```

---

## Phase 8 — Test Object-Level Authorization

* Identify client-controlled object references:

  ```text
  user_id

  project_id

  document_id

  organization_id

  file_id

  invoice_id
  ```

---

## Controlled Two-Account Workflow

```text
User A

↓

Access Object A

↓

Capture Request

↓

Change Only Object Identifier

↓

Request Controlled Object B

↓

Compare

↓

Verify Authorization
```

---

## Object Authorization Test Card

```text
Endpoint / Operation:

User:

Role:

Tenant:

Baseline Object:

Baseline Owner:

Modified Object:

Modified Owner:

Expected:

Actual:

Sensitive Data Returned?:

State Changed?:

Evidence:
```

---

## Critical Principle

```text
Object Identifier

≠

Authorization
```

---

## Phase 9 — Test Function-Level Authorization

* Look for actions such as:

  ```text
  Delete User

  Change Role

  Export Data

  Manage Billing

  Approve Request

  Disable Account

  Modify Organization
  ```

* Capture a legitimate privileged request using a controlled privileged account.

* Replay only against controlled objects using a lower-privilege account.

---

## Function-Level Workflow

```text
Privileged User

↓

Perform Authorized Action

↓

Capture Request

↓

Identify Required Parameters

↓

Use Lower-Privilege Controlled Account

↓

Replay Safely

↓

Verify Final State
```

---

## Critical Principle

```text
Hidden Button

≠

Authorization Control
```

---

## Phase 10 — Test Field-Level Authorization

* Modern APIs may return or accept fields the UI does not expose.

* Review:

  ```text
  role

  permissions

  tenant_id

  owner_id

  internal_notes

  billing_status

  account_status
  ```

---

## Read Test

```text
Authorized Object

↓

Request Additional Field

↓

Compare Returned Data

↓

Determine Whether Current Role Should Access It
```

---

## Write Test

```text
Normal Update

↓

Capture Request

↓

Add One Security-Sensitive Field

↓

Send

↓

Verify Stored State
```

---

## Phase 11 — Test Mass Assignment

* Example baseline:

```json
{
  "display_name": "Test User"
}
```

* Controlled test:

```json
{
  "display_name": "Test User",
  "role": "admin"
}
```

* Use only controlled accounts.

---

## Mass Assignment Questions

```text
Are Unexpected Fields Rejected?

Ignored?

Stored?

Applied?

Does Behavior Differ by Role?

Are Nested Fields Protected?
```

---

## Critical Principle

```text
Client Can Submit Field

≠

Client Is Authorized to Control Field
```

---

## Phase 12 — Test API Input Validation

* Test one input dimension at a time:

  ```text
  Missing Field

  Null

  Empty String

  Wrong Type

  Negative Number

  Boundary Value

  Unexpected Field

  Duplicate Value
  ```

---

## Validation Matrix

| Input                  | Expected Behavior          |
| ---------------------- | -------------------------- |
| Valid                  | Accepted                   |
| Missing Required Field | Rejected                   |
| Wrong Type             | Rejected                   |
| Unauthorized Field     | Rejected or ignored safely |
| Boundary Value         | Enforced correctly         |

---

## Phase 13 — Test Business Logic

* Identify critical workflows.

* Examples:

  ```text
  Registration

  Invitations

  Purchases

  Approval

  Subscription

  Credits

  Password Reset

  Role Changes

  File Approval

  Project Transfer
  ```

---

## Workflow Mapping

```text
State A

↓

Action

↓

State B

↓

Action

↓

State C
```

* Write down the expected rules between states.

---

## Business Invariants

* Examples:

  ```text
  A User Cannot Approve Their Own Restricted Request

  A Coupon Can Be Used Only Once

  A Member Cannot Promote Themselves

  A Tenant Cannot Access Another Tenant's Objects

  A Deleted Invitation Cannot Be Reused
  ```

---

## Invariant Test Card

```text
Invariant:

Baseline:

Manipulation:

Expected:

Actual:

Final State:

Impact:

Evidence:
```

---

## Phase 14 — Test Workflow Bypass

* Investigate:

  ```text
  Skipping Steps

  Reordering Steps

  Repeating Steps

  Calling Final Endpoint Directly

  Modifying State Parameters

  Reusing Old Tokens
  ```

---

## Example

```text
Expected:

Create

↓

Review

↓

Approve
```

* Controlled test:

  ```text
  Create

  ↓

  Call Approve Directly
  ```

* Determine whether server-side state enforcement exists.

---

## Phase 15 — Test Replay

* Identify one-time operations.

* Examples:

  ```text
  Reward Claim

  Invitation Acceptance

  Password Reset Token

  Coupon Redemption

  Approval Action
  ```

---

## Replay Workflow

```text
Perform Action Once

↓

Verify Success

↓

Replay Same Request

↓

Verify Final State

↓

Determine Whether Duplicate Effect Occurred
```

---

## Important Principle

```text
Replayable Request

≠

Automatically Vulnerable
```

* The action must violate an intended one-time or state-based rule.

---

## Phase 16 — Identify Race-Condition Candidates

* Look for:

  ```text
  Check-Then-Update

  Limited Inventory

  One-Time Claims

  Balance Changes

  Invitations

  Role Changes

  Duplicate Creation

  File Replacement
  ```

---

## Race Testing Principle

```text
Sequential Baseline First

↓

Minimal Concurrent Test

↓

Verify Final State
```

* Do not start with high-volume concurrency.

---

## Race Invariant

```text
Concurrent Requests

Must Not Violate

Business or Security Invariants
```

---

## Phase 17 — Test File Uploads

* Inventory:

  ```text
  Avatars

  Attachments

  Documents

  Imports

  Media

  Backups
  ```

---

## File Lifecycle

```text
Upload

↓

Validate

↓

Store

↓

Process

↓

Retrieve

↓

Replace / Delete
```

---

## File Test Matrix

```text
Extension

MIME

Signature

Actual Content

Filename

Size

Storage

Processing

Retrieval

Authorization
```

---

## Critical Principle

```text
Accepted

≠

Stored

≠

Processed

≠

Served

≠

Executed
```

---

## File Authorization

* Use controlled accounts:

  ```text
  User A Uploads Private File

  ↓

  User B Attempts Access

  ↓

  Compare With Intended Policy
  ```

---

## File Safety

* Do not use:

  ```text
  Real Malware

  Destructive Payloads

  Resource-Exhaustion Archives
  ```

* Use harmless proof files.

---

## Phase 18 — Test WebSockets

* Identify:

  ```text
  Handshake

  Authentication

  Origin

  Client Messages

  Server Messages

  Object IDs

  Channels

  Subscriptions
  ```

---

## WebSocket Security Layers

```text
Handshake Security

+

Message-Level Security
```

---

## WebSocket Authorization

* Test:

  ```text
  Object IDs

  Roles

  Tenants

  Channels

  Subscriptions

  Hidden Actions
  ```

---

## WebSocket Invariant

```text
Authenticated Connection

≠

Authorization for Every Message
```

---

## WebSocket Session Lifecycle

* Review:

  ```text
  Logout

  Token Expiration

  Password Change

  Role Change

  Account Disablement

  Reconnection
  ```

---

## Origin Validation

* If cookies authenticate the WebSocket, investigate:

  ```text
  Origin Validation

  Cross-Site Connection Behavior

  Additional Tokens

  Sensitive Read / Write Capability
  ```

* Do not report missing origin validation alone as exploitable CSWSH.

---

## Phase 19 — Test GraphQL

* Identify:

  ```text
  Endpoint

  Queries

  Mutations

  Subscriptions

  Variables

  Object IDs

  Input Objects
  ```

---

## GraphQL Security Layers

```text
Authentication

↓

Object Authorization

↓

Nested Authorization

↓

Field Authorization

↓

Mutation Authorization

↓

Role / Tenant Authorization
```

---

## GraphQL Object Test

```text
User A + Object A

↓

Baseline

User A + Controlled Object B

↓

Compare
```

---

## GraphQL Field Test

```text
Authorized Object

↓

Add Sensitive Field

↓

Determine Whether Field-Level Authorization Exists
```

---

## GraphQL Mutation Test

```text
Legitimate Mutation

↓

Capture

↓

Change One Authorization-Relevant Variable

↓

Send

↓

Verify Final State
```

---

## GraphQL Complexity

* Review safely:

  ```text
  Aliases

  Batching

  Pagination

  Depth

  Resolver Complexity
  ```

* Keep tests low volume.

---

## Important Principle

```text
Complexity Testing

≠

Stress Testing
```

---

## Phase 20 — Correlate Technologies

* This is where the capstone becomes a real modern-application assessment.

* Do not treat each technology as isolated.

* Ask how they interact.

---

## Cross-Technology Questions

```text
Does REST and GraphQL Enforce the Same Authorization?

Does WebSocket Authorization Match REST Authorization?

Does Logout Invalidate REST Tokens and WebSocket Sessions?

Can a File ID Obtained Through GraphQL Be Accessed Through REST?

Can OAuth-Created Accounts Access Unexpected API Roles?

Do Role Changes Propagate to JWTs and Existing WebSockets?

Does a GraphQL Mutation Bypass UI Workflow Controls?

Can REST Create an Object That WebSocket Exposes Cross-Tenant?
```

---

## Trust Boundary Map

```text
Browser

↓

Authentication Layer

↓

REST API ─────────┐
                  │
GraphQL API ──────┼──► Shared Data / Services
                  │
WebSocket ────────┘

↓

File Storage / External Services
```

* Security inconsistencies often appear where multiple components trust the same identity or object differently.

---

## Phase 21 — Test Authorization Consistency

* Suppose a document can be accessed through:

  ```text
  REST

  GraphQL

  WebSocket Notification

  File Download Endpoint
  ```

* Test the same controlled object through each path.

---

## Authorization Consistency Matrix

| Interface      | Own Object | Other User | Other Tenant |
| -------------- | ---------- | ---------- | ------------ |
| REST           | Test       | Test       | Test         |
| GraphQL        | Test       | Test       | Test         |
| WebSocket      | Test       | Test       | Test         |
| File Retrieval | Test       | Test       | Test         |

---

## Critical Principle

```text
Security Policy

Must Remain Consistent

Across Every Interface
```

---

## Phase 22 — Test Identity Consistency

* Determine whether identity is derived from:

  ```text
  Session

  JWT sub

  user_id Parameter

  GraphQL Variable

  WebSocket Message

  Tenant Header
  ```

* Security-sensitive identity should not be overridden by untrusted client input.

---

## Example Trust Failure

```text
JWT Says:

User 1001

Request Body Says:

user_id = 1002

↓

Which Identity Does the Server Trust?
```

---

## Identity Invariant

```text
Authenticated Identity

Must Not Be Replaced

By Client-Controlled Identity Fields
```

---

## Phase 23 — Test Tenant Consistency

* Tenant context may come from:

  ```text
  Token Claim

  Subdomain

  Header

  URL

  Object Relationship

  Request Body
  ```

* Compare how each component derives tenant membership.

---

## Tenant Invariant

```text
Client-Controlled Tenant Identifier

Must Not Override

Authorized Tenant Context
```

---

## Phase 24 — Look for Vulnerability Chains

* Individual weaknesses may combine into greater impact.

* Example conceptual chain:

  ```text
  Excessive API Data Exposure

  ↓

  Reveals Object Identifier

  ↓

  Missing File Authorization

  ↓

  Private Document Access
  ```

---

## Another Chain

```text
Role Change

↓

Old JWT Remains Privileged

↓

Existing WebSocket Connection Retains Privileges

↓

Restricted Action Still Possible
```

---

## Another Chain

```text
GraphQL Hidden Mutation

↓

Weak Function Authorization

↓

Unauthorized State Change

↓

REST Endpoint Exposes Result
```

---

## Chain Analysis Questions

```text
Does One Finding Provide Information Needed for Another?

Does One Weakness Increase Another's Impact?

Can Multiple Low-Severity Issues Produce High Impact?

Does the Chain Cross Protocol Boundaries?

Does the Chain Cross Authentication or Tenant Boundaries?
```

---

## Important Principle

```text
Do Not Artificially Chain Unrelated Findings
```

* A valid chain requires a realistic dependency between weaknesses.

---

## Phase 25 — Distinguish Anomalies From Vulnerabilities

* Examples:

  ```text
  Introspection Enabled

  ≠

  Automatically Vulnerable
  ```

  ```text
  Unexpected File Extension Accepted

  ≠

  Automatically Critical
  ```

  ```text
  WebSocket Origin Not Checked

  ≠

  Automatically Exploitable CSWSH
  ```

  ```text
  JWT Decodes Successfully

  ≠

  JWT Is Insecure
  ```

  ```text
  HTTP 200

  ≠

  Operation Succeeded
  ```

---

## Vulnerability Validation Model

```text
Unexpected Behavior

+

Security Boundary Violated

+

Reproducible Evidence

+

Meaningful Impact

=

Validated Finding
```

---

## Phase 26 — Verify Final State

* After every state-changing test, verify what actually happened.

* Check through:

  ```text
  UI

  API

  Second Account

  Fresh Session

  Database-Visible Application State

  Re-Login

  Retrieval Endpoint
  ```

---

## Critical Principle

```text
Response Says Success

≠

State Actually Changed
```

* And:

```text
Response Says Error

≠

State Definitely Did Not Change
```

---

## Phase 27 — Record Evidence

* For every potential finding, preserve:

  ```text
  Timestamp

  User

  Role

  Tenant

  Endpoint / Operation

  Baseline Request

  Baseline Response

  Modified Request

  Modified Response

  Changed Variable

  Final State

  Screenshots if Useful

  Security Impact
  ```

---

## Evidence Naming Convention

```text
FINDING-01-baseline-request.txt

FINDING-01-baseline-response.txt

FINDING-01-modified-request.txt

FINDING-01-modified-response.txt

FINDING-01-final-state.png
```

---

## Phase 28 — Reproduce Findings

* Before reporting:

  ```text
  Reset Controlled State

  ↓

  Repeat Baseline

  ↓

  Repeat Manipulation

  ↓

  Verify Same Result

  ↓

  Record Clean Evidence
  ```

---

## Reproduction Standard

```text
One Unexpected Result

=

Lead

Repeated Controlled Result

=

Evidence
```

---

## Phase 29 — Assess Impact

* Ask:

  ```text
  What Security Boundary Was Broken?

  Who Can Exploit It?

  What Prerequisites Exist?

  What Data Can Be Accessed?

  What State Can Be Changed?

  Is Cross-Tenant Impact Possible?

  Is Privilege Escalation Possible?

  Is User Interaction Required?

  Is the Impact Repeatable?
  ```

---

## Impact Categories

```text
Confidentiality

Integrity

Availability

Authorization

Authentication

Privacy

Financial

Business Process

Tenant Isolation
```

---

## Severity Is Contextual

* Do not assign severity based only on vulnerability names.

* Example:

  ```text
  IDOR

  Could Mean:

  Public Profile Access

  or

  Private Financial Document Access
  ```

* Impact determines significance.

---

## Phase 30 — Prioritize Findings

* A practical priority model:

  ```text
  Exploitability

  +

  Privilege Required

  +

  User Interaction

  +

  Data Sensitivity

  +

  Scope of Impact

  +

  Business Consequence
  ```

---

## Finding Priority Table

| Finding   | Boundary Broken      | Impact | Confidence | Priority |
| --------- | -------------------- | ------ | ---------- | -------- |
| Finding 1 | Authorization        | High   | Confirmed  | High     |
| Finding 2 | Information Exposure | Low    | Confirmed  | Low      |
| Finding 3 | Business Logic       | Medium | Confirmed  | Medium   |

---

## Phase 31 — Write Professional Findings

* Each validated finding should contain:

  ```text
  Title

  Severity

  Affected Component

  Description

  Preconditions

  Steps to Reproduce

  Expected Behavior

  Actual Behavior

  Security Impact

  Evidence

  Remediation

  Retest Guidance
  ```

---

## Finding Template

```text
Title:

Severity:

Affected Component:

Endpoint / Operation:

Description:

Preconditions:

Steps to Reproduce:

1.

2.

3.

Expected Behavior:

Actual Behavior:

Security Impact:

Evidence:

Remediation:

Retest Guidance:
```

---

## Writing Strong Titles

* Weak:

  ```text
  IDOR Found
  ```

* Better:

  ```text
  Missing Object-Level Authorization Allows Cross-User Access to Private Documents
  ```

* A strong title communicates:

  ```text
  Root Cause

  +

  Impact
  ```

---

## Writing Strong Impact

* Weak:

  ```text
  An attacker can change the ID.
  ```

* Better:

  ```text
  An authenticated member can modify the document identifier in the request and retrieve private documents belonging to another user when the document ID is known.
  ```

* Describe only validated impact.

---

## Root Cause vs Symptom

* Symptom:

  ```text
  Changing ID 1001 to 1002 Returns Data
  ```

* Root cause:

  ```text
  Missing Server-Side Object-Level Authorization
  ```

* Reports should explain both.

---

## Remediation Principles

* Recommendations should address the root cause.

* Example:

  ```text
  Enforce object-level authorization server-side for every document retrieval request based on the authenticated user's permissions and tenant membership.
  ```

* Avoid vague recommendations such as:

  ```text
  Improve security.
  ```

---

## Phase 32 — Build the Executive Summary

* Summarize:

  ```text
  What Was Tested

  Overall Security Posture

  Most Important Findings

  Business Impact

  Major Security Themes

  Recommended Priorities
  ```

---

## Executive Summary Template

```text
The assessment evaluated the application's modern web architecture, including authentication, authorization, REST APIs, GraphQL operations, WebSocket communication, file handling, and critical business workflows.

Testing focused on identifying weaknesses that could allow unauthorized data access, privilege escalation, cross-tenant access, workflow manipulation, or unsafe handling of user-controlled content.

The most significant findings involved:

1.

2.

3.

The primary remediation priority should be strengthening server-side authorization and ensuring security policies are enforced consistently across all application interfaces.
```

* Adapt the wording to actual results.

---

## Phase 33 — Build an Attack-Surface Summary

```text
Authentication:

REST API:

GraphQL:

WebSockets:

Files:

Business Logic:

Authorization:

Tenant Isolation:

Third-Party Integrations:

High-Risk Areas:

Validated Findings:
```

---

## Phase 34 — Create a Security Invariant Table

| Invariant                                        | Tested? | Result    | Evidence  |
| ------------------------------------------------ | ------- | --------- | --------- |
| User cannot access another user's private object | Yes/No  | Pass/Fail | Reference |
| Member cannot perform admin action               | Yes/No  | Pass/Fail | Reference |
| Tenant A cannot access Tenant B data             | Yes/No  | Pass/Fail | Reference |
| Logged-out session loses protected access        | Yes/No  | Pass/Fail | Reference |
| One-time action cannot be repeated               | Yes/No  | Pass/Fail | Reference |

* This converts testing into measurable security assertions.

---

## Phase 35 — Create a Coverage Matrix

| Area            | Mapped | Tested | Findings | Notes |
| --------------- | ------ | ------ | -------- | ----- |
| Authentication  |        |        |          |       |
| JWT             |        |        |          |       |
| OAuth/OIDC      |        |        |          |       |
| REST API        |        |        |          |       |
| Authorization   |        |        |          |       |
| Business Logic  |        |        |          |       |
| Race Conditions |        |        |          |       |
| File Handling   |        |        |          |       |
| WebSockets      |        |        |          |       |
| GraphQL         |        |        |          |       |

---

## Phase 36 — Review for Missed Attack Surface

* Before finishing, ask:

  ```text
  Did I Test Every Role?

  Did I Test Horizontal Authorization?

  Did I Test Vertical Authorization?

  Did I Test Tenant Isolation?

  Did I Review Hidden API Fields?

  Did I Review State-Changing Operations?

  Did I Test Logout and Token Invalidation?

  Did I Review Files?

  Did I Review WebSockets?

  Did I Review GraphQL?

  Did I Verify Final State?

  Did I Reproduce Findings?
  ```

---

## Capstone Master Workflow

```text
Define Scope

↓

Create Controlled Accounts

↓

Map Architecture

↓

Capture Application Traffic

↓

Inventory Endpoints and Operations

↓

Map Authentication

↓

Analyze JWT / OAuth if Present

↓

Build Authorization Matrix

↓

Test Object-Level Authorization

↓

Test Function-Level Authorization

↓

Test Field-Level Authorization

↓

Test Mass Assignment

↓

Test Input Validation

↓

Map Critical Business Workflows

↓

Test Workflow Bypass

↓

Test Replay

↓

Identify Race Candidates

↓

Test Files

↓

Test WebSockets

↓

Test GraphQL

↓

Compare Security Across Interfaces

↓

Analyze Trust Boundaries

↓

Look for Realistic Chains

↓

Verify Final State

↓

Reproduce Findings

↓

Assess Impact

↓

Prioritize

↓

Write Findings

↓

Create Executive Summary

↓

Review Coverage
```

---

## Capstone Challenge 1 — Architecture Mapping

### Objective

* Produce a complete architecture map.

### Required Deliverables

```text
Application Components

Domains / Hosts

REST Endpoints

GraphQL Endpoint

WebSocket Endpoint

Authentication Mechanisms

File Storage

Third-Party Integrations

Trust Boundaries
```

### Success Criteria

* You can explain how a user action travels from:

  ```text
  Browser

  ↓

  Authentication

  ↓

  Backend Service

  ↓

  Data / Storage

  ↓

  Response
  ```

---

## Capstone Challenge 2 — Authorization Matrix

### Objective

* Build and test a role/object/tenant matrix.

### Minimum Coverage

```text
2 Users in Same Tenant

1 User in Different Tenant

1 Higher-Privilege User
```

### Test

```text
Read

Create

Update

Delete

Restricted Function
```

### Deliverable

* A completed authorization matrix with evidence references.

---

## Capstone Challenge 3 — Authentication Lifecycle

### Objective

* Test how authentication state changes propagate.

### Test

```text
Login

↓

Use REST

↓

Use GraphQL

↓

Open WebSocket

↓

Logout

↓

Retest All Three Interfaces
```

### Question

```text
Does Logout Consistently Remove Protected Access?
```

---

## Capstone Challenge 4 — JWT or Session Analysis

### Objective

* Map how identity is represented.

### Record

```text
User Identity

Role

Tenant

Expiration

Issuer

Audience

Session Binding
```

### Test

* Verify that modified, expired, invalidated, or inappropriate credentials are rejected according to the application's intended model.

---

## Capstone Challenge 5 — Business Logic

### Objective

* Select one critical multi-step workflow.

### Map

```text
State 1

↓

Action

↓

State 2

↓

Action

↓

State 3
```

### Test

```text
Skip

Repeat

Reorder

Modify

Replay
```

### Deliverable

* At least three clearly defined invariants and their test results.

---

## Capstone Challenge 6 — File Handling

### Objective

* Analyze one upload feature end to end.

### Test

```text
Validation

Filename

MIME

Content

Storage

Processing

Retrieval

Authorization

Replacement

Deletion
```

### Deliverable

* A complete file lifecycle diagram and test matrix.

---

## Capstone Challenge 7 — WebSocket Security

### Objective

* Map one real-time feature.

### Test

```text
Handshake

Authentication

Origin

Messages

Object IDs

Subscriptions

Authorization

Logout

Reconnect
```

### Deliverable

* Message inventory plus authorization results.

---

## Capstone Challenge 8 — GraphQL Security

### Objective

* Analyze one GraphQL feature.

### Test

```text
Query

Variables

Object Authorization

Nested Objects

Fields

Mutation

Role

Tenant

Errors
```

### Deliverable

* Operation inventory and authorization matrix.

---

## Capstone Challenge 9 — Cross-Interface Consistency

### Objective

* Select one sensitive object available through multiple interfaces.

* Example:

  ```text
  Document
  ```

### Test Through

```text
REST

GraphQL

WebSocket Event

File Retrieval
```

### Question

```text
Does Every Interface Enforce the Same Security Policy?
```

---

## Capstone Challenge 10 — Final Report

### Required Sections

```text
Executive Summary

Scope

Methodology

Architecture Overview

Attack Surface

Findings

Risk Summary

Security Invariants

Coverage Matrix

Remediation Priorities

Conclusion
```

---

## Final Deliverables

* By the end of the capstone, you should have:

  1. Scope document.
  2. Architecture map.
  3. Endpoint inventory.
  4. Authentication map.
  5. Authorization matrix.
  6. Business-logic workflow map.
  7. Security invariant table.
  8. File-handling test matrix.
  9. WebSocket message inventory.
  10. GraphQL operation inventory.
  11. Evidence directory.
  12. Validated findings.
  13. False-positive notes.
  14. Coverage matrix.
  15. Final professional assessment report.

---

## Capstone Completion Checklist

### Scope

* [ ] Scope documented.
* [ ] Testing permissions understood.
* [ ] Out-of-scope systems identified.
* [ ] Controlled accounts prepared.

### Architecture

* [ ] Frontend mapped.
* [ ] REST APIs mapped.
* [ ] GraphQL identified.
* [ ] WebSockets identified.
* [ ] Authentication services mapped.
* [ ] File storage identified.
* [ ] Trust boundaries documented.

### Authentication

* [ ] Login tested.
* [ ] Logout tested.
* [ ] Session expiration reviewed.
* [ ] Token lifecycle reviewed.
* [ ] JWT analyzed if applicable.
* [ ] OAuth/OIDC reviewed if applicable.

### Authorization

* [ ] Horizontal authorization tested.
* [ ] Vertical authorization tested.
* [ ] Cross-tenant authorization tested.
* [ ] Object-level authorization tested.
* [ ] Function-level authorization tested.
* [ ] Field-level authorization tested.

### APIs

* [ ] Endpoint inventory created.
* [ ] Parameters mapped.
* [ ] Object IDs mapped.
* [ ] Input validation tested.
* [ ] Mass assignment tested.
* [ ] Hidden fields reviewed.

### Business Logic

* [ ] Critical workflows mapped.
* [ ] Security invariants defined.
* [ ] Step skipping tested.
* [ ] Replay tested.
* [ ] State transitions tested.
* [ ] Race candidates reviewed.

### Files

* [ ] Upload baseline established.
* [ ] Validation tested.
* [ ] Filename handling reviewed.
* [ ] Storage behavior reviewed.
* [ ] Processing reviewed.
* [ ] Retrieval authorization tested.
* [ ] Replacement/deletion reviewed.

### WebSockets

* [ ] Handshake captured.
* [ ] Authentication mapped.
* [ ] Messages inventoried.
* [ ] Object authorization tested.
* [ ] Subscription authorization tested.
* [ ] Origin handling reviewed.
* [ ] Logout behavior tested.
* [ ] Reconnection reviewed.

### GraphQL

* [ ] Endpoint identified.
* [ ] Queries mapped.
* [ ] Mutations mapped.
* [ ] Introspection reviewed.
* [ ] Object authorization tested.
* [ ] Nested authorization tested.
* [ ] Field authorization tested.
* [ ] Input objects reviewed.
* [ ] Complexity considered safely.

### Validation

* [ ] Findings reproduced.
* [ ] Final state verified.
* [ ] False positives eliminated.
* [ ] Impact demonstrated.
* [ ] Evidence organized.

### Reporting

* [ ] Findings written.
* [ ] Severity contextualized.
* [ ] Root causes identified.
* [ ] Remediation provided.
* [ ] Executive summary completed.
* [ ] Coverage matrix completed.

---

## Final Investigation Journal

```text
Application:

Assessment Date:

Tester:

Scope:

Accounts:

Roles:

Tenants:

Architecture Summary:

Authentication Summary:

REST API Summary:

JWT Summary:

OAuth/OIDC Summary:

Authorization Summary:

Business Logic Summary:

Race Condition Summary:

File Handling Summary:

WebSocket Summary:

GraphQL Summary:

Cross-Interface Findings:

Security Invariants Tested:

Validated Findings:

Finding 1:

Finding 2:

Finding 3:

False Positives Eliminated:

Highest-Risk Finding:

Most Important Root Cause:

Recommended Priority:

Evidence Directory:

Coverage Gaps:

Retest Requirements:

Final Conclusion:
```

---

## Professional Tips

* Understand the application before attempting unusual requests.
* Map architecture and trust boundaries early.
* Use multiple controlled accounts whenever authorization is important.
* Test horizontal, vertical, and cross-tenant authorization separately.
* Build an authorization matrix instead of testing random IDs.
* Change one variable at a time.
* Treat REST, GraphQL, WebSockets, and file endpoints as different interfaces to the same security policy.
* Compare authorization behavior across interfaces.
* Never assume the frontend represents the complete attack surface.
* Inspect JavaScript-generated API traffic.
* Treat client-controlled identity, role, tenant, price, ownership, and status fields as untrusted.
* Verify object-level authorization on every sensitive operation.
* Test field-level authorization independently from object access.
* Map business workflows as state machines.
* Define security invariants before attempting bypasses.
* Test sequential behavior before concurrency.
* Keep race-condition testing minimal and controlled.
* Analyze the complete file lifecycle rather than only extension validation.
* Treat WebSocket messages as untrusted API requests.
* Test GraphQL resolvers, fields, mutations, and nested relationships independently.
* Treat introspection as information, not automatically as a finding.
* Review authentication lifecycle across all protocols.
* Check whether logout, password changes, role changes, and account disablement propagate correctly.
* Verify final backend state after every important state-changing test.
* Record evidence while testing rather than reconstructing it later.
* Reproduce findings from a clean baseline.
* Distinguish unexpected behavior from actual security-boundary violations.
* Chain findings only when there is a realistic dependency.
* Prioritize root causes over superficial symptoms.
* Write impact in terms of what an attacker can actually access, modify, or influence.
* Keep every test within authorization and avoid unnecessary disruption.

---

## Common Mistakes

* Starting exploitation before understanding architecture.
* Treating every API endpoint independently without mapping workflows.
* Testing only one user account.
* Ignoring tenant boundaries.
* Assuming authentication guarantees authorization.
* Testing random object IDs without controlled ownership.
* Ignoring field-level authorization.
* Ignoring hidden API fields.
* Trusting client-controlled roles or identities.
* Testing only REST while ignoring GraphQL.
* Testing only HTTP while ignoring WebSockets.
* Testing file extensions without examining storage and retrieval.
* Treating JWT decoding as a vulnerability.
* Reporting enabled GraphQL introspection automatically.
* Reporting missing WebSocket origin validation without proving exploitability.
* Testing business logic without defining the expected invariant.
* Starting concurrency testing before establishing sequential behavior.
* Sending excessive parallel requests.
* Confusing response messages with actual backend state.
* Ignoring token behavior after logout.
* Ignoring existing WebSocket connections after session invalidation.
* Ignoring cross-interface authorization inconsistencies.
* Artificially chaining unrelated findings.
* Reporting anomalies without meaningful impact.
* Failing to reproduce findings.
* Failing to preserve evidence.
* Assigning severity based only on vulnerability names.
* Writing vague remediation.
* Finishing without reviewing coverage gaps.

---

## Interview Questions

1. How do you approach testing a modern web application from start to finish?
2. Why should architecture mapping happen before deep vulnerability testing?
3. What is a trust boundary?
4. How do you build an attack-surface inventory?
5. Why are multiple controlled accounts important?
6. What is the difference between horizontal, vertical, and cross-tenant authorization?
7. How do you build an authorization matrix?
8. Why should object identifiers never be treated as authorization controls?
9. What is function-level authorization?
10. What is field-level authorization?
11. How does mass assignment occur?
12. Why should security-sensitive identity fields be derived server-side?
13. How do you map a business workflow?
14. What is a security invariant?
15. How do you test workflow bypasses?
16. When does request replay become a vulnerability?
17. How do you identify race-condition candidates?
18. Why should sequential behavior be tested before concurrency?
19. How do you analyze a JWT securely?
20. Which JWT claims are important during security testing?
21. Why is decoding a JWT not itself a vulnerability?
22. What should you test in an OAuth/OIDC integration?
23. Why must third-party OAuth providers remain outside testing scope unless explicitly authorized?
24. How do you test object-level authorization in REST APIs?
25. How do you test nested authorization in GraphQL?
26. What is GraphQL field-level authorization?
27. Why is enabled GraphQL introspection not automatically a vulnerability?
28. Why can one GraphQL HTTP request represent many backend operations?
29. How do you test GraphQL complexity safely?
30. How do you test WebSocket message-level authorization?
31. Why does an authenticated WebSocket connection not authorize every message?
32. What conditions are generally required for Cross-Site WebSocket Hijacking?
33. How should logout affect existing WebSocket connections?
34. How do you test file-upload security beyond extension validation?
35. Why are upload acceptance and file execution different security states?
36. How do you test private file authorization?
37. Why should REST, GraphQL, WebSockets, and file retrieval enforce consistent authorization?
38. What is cross-interface authorization testing?
39. How can identity inconsistencies create vulnerabilities?
40. How can tenant context inconsistencies create vulnerabilities?
41. What is a realistic vulnerability chain?
42. Why should unrelated findings not be artificially chained?
43. What is the difference between an anomaly and a validated vulnerability?
44. Why must final application state be verified?
45. What evidence should be collected for a vulnerability?
46. Why should findings be reproduced before reporting?
47. How should vulnerability severity be determined?
48. What is the difference between a vulnerability symptom and its root cause?
49. What should a professional penetration-test finding contain?
50. Describe your complete modern application security testing methodology using Burp Suite.

---

## Quick Revision

* Modern application:

  ```text
  Frontend

  +

  REST

  +

  GraphQL

  +

  WebSockets

  +

  Authentication

  +

  Files

  +

  Business Logic
  ```

* Core methodology:

  ```text
  Understand

  ↓

  Map

  ↓

  Baseline

  ↓

  Hypothesis

  ↓

  Modify One Variable

  ↓

  Compare

  ↓

  Verify State

  ↓

  Prove Impact

  ↓

  Document
  ```

* Authorization:

  ```text
  Horizontal

  +

  Vertical

  +

  Tenant
  ```

* Security layers:

  ```text
  Authentication

  ↓

  Object Authorization

  ↓

  Function Authorization

  ↓

  Field Authorization

  ↓

  Business Rules
  ```

* Identity:

  ```text
  Authenticated Identity

  ≠

  Client-Controlled user_id
  ```

* Tenant:

  ```text
  Authorized Tenant Context

  ≠

  Client-Controlled tenant_id
  ```

* File lifecycle:

  ```text
  Upload

  ↓

  Validate

  ↓

  Store

  ↓

  Process

  ↓

  Retrieve
  ```

* WebSockets:

  ```text
  Handshake Security

  +

  Message Security
  ```

* GraphQL:

  ```text
  Query

  Mutation

  Subscription

  +

  Object / Field / Resolver Authorization
  ```

* Business logic:

  ```text
  Define Invariant

  ↓

  Establish Baseline

  ↓

  Skip / Repeat / Reorder / Modify

  ↓

  Verify Final State
  ```

* Validation:

  ```text
  Unexpected Behavior

  +

  Security Boundary Violation

  +

  Reproducibility

  +

  Impact

  =

  Validated Finding
  ```

* Cross-interface testing:

  ```text
  REST

  vs

  GraphQL

  vs

  WebSocket

  vs

  File Retrieval
  ```

* Professional finish:

  ```text
  Reproduce

  ↓

  Evidence

  ↓

  Assess Impact

  ↓

  Prioritize

  ↓

  Report Root Cause

  ↓

  Recommend Remediation
  ```

---

## Key Takeaways

* Modern application testing requires understanding the complete system rather than testing individual endpoints in isolation.
* Architecture mapping reveals components, protocols, trust boundaries, identity flows, and high-risk integration points.
* Controlled accounts representing different users, roles, and tenants are essential for reliable authorization testing.
* Authorization should be tested across horizontal, vertical, and cross-tenant boundaries.
* Object-level, function-level, and field-level authorization are distinct controls and must be tested independently.
* Client-controlled identifiers such as `user_id`, `tenant_id`, `role`, `owner_id`, and object IDs must never replace server-side authorization decisions.
* JWT, OAuth/OIDC, session, and token testing should focus on actual trust decisions, lifecycle behavior, validation, and authorization impact.
* Business-logic testing begins by mapping workflows and defining security invariants before attempting step skipping, replay, reordering, or concurrency.
* Race-condition testing should begin with a sequential baseline and use minimal concurrency to avoid unnecessary disruption.
* File-upload testing must cover validation, storage, processing, retrieval, authorization, replacement, and deletion.
* WebSocket testing requires separate analysis of handshake security and message-level authorization.
* GraphQL testing requires authorization analysis at the object, nested-object, field, mutation, role, tenant, and resolver levels.
* Security controls must remain consistent across REST APIs, GraphQL, WebSockets, file retrieval, and other interfaces exposing the same underlying data.
* Authentication-state changes such as logout, password changes, role changes, token expiration, and account disablement should propagate according to the intended security model across all application protocols.
* Cross-technology testing is especially valuable because different services may interpret identity, tenant context, ownership, or authorization differently.
* Vulnerability chains should represent realistic dependencies between weaknesses rather than artificial combinations of unrelated findings.
* Unexpected behavior becomes a validated vulnerability only when a security boundary is demonstrably violated with reproducible and meaningful impact.
* Final backend state must be verified after state-changing tests because protocol responses alone may not reflect what actually occurred.
* Evidence should be collected during testing and should clearly show the baseline, controlled modification, resulting behavior, final state, and impact.
* Findings should describe root causes rather than only symptoms and should provide remediation that addresses the underlying security failure.
* Severity should be based on realistic exploitability and business impact rather than vulnerability names alone.
* A professional assessment ends with reproducible findings, an executive summary, security-invariant results, coverage analysis, prioritized remediation, and clearly identified testing gaps.
* The complete modern application testing methodology is **define scope → map architecture and trust boundaries → inventory endpoints and protocols → map authentication and identity → build authorization matrices → test object/function/field/tenant controls → test API inputs and mass assignment → map business workflows and invariants → test replay, sequencing, and race candidates → analyze file handling → test WebSockets → test GraphQL → compare security across interfaces → investigate realistic vulnerability chains → verify final state → reproduce findings → assess impact → document evidence → prioritize root causes → report professionally**.

---

# Module 16 Complete

```text
█████████████ 13 / 13 Files

✅ MODERN APPLICATION TESTING — COMPLETE
```

* You have now completed the full **Modern Application Testing** module.

* The module covers the transition from using Burp Suite as a collection of individual tools to applying it as part of a structured methodology against modern application architectures.

* At this point, the repository covers the complete planned progression through:

  ```text
  Burp Suite Foundations

  ↓

  Core Burp Suite Tools

  ↓

  Professional Testing Workflows

  ↓

  Advanced Burp Usage

  ↓

  Modern Application Testing
  ```
