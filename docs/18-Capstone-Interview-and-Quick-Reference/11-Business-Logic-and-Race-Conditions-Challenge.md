# Business Logic and Race Conditions Challenge

> **Module 18 — Capstone, Interview & Quick Reference**

> **Progress**

```text
███████████░░░░ 11 / 15 Files
```

---

## Overview

* Business logic vulnerabilities occur when an application correctly processes technically valid requests but allows users to violate the intended business rules.

* These flaws are often difficult for automated scanners to detect because understanding them requires knowledge of:

  * Application workflows.
  * User roles.
  * State transitions.
  * Pricing rules.
  * Quantity restrictions.
  * Approval processes.
  * Account lifecycle.
  * Credits and balances.
  * Coupons and promotions.
  * Resource ownership.
  * Time-sensitive operations.

* Race conditions are closely related because applications may enforce a rule correctly for a single request but fail when multiple operations occur concurrently.

* The core testing model is:

```text
Understand the Business Rule

↓

Map the Workflow

↓

Identify Invariants

↓

Capture a Valid Baseline

↓

Change Sequence / State / Value / Timing

↓

Observe Final Server State

↓

Verify Whether the Invariant Was Broken
```

---

## Objectives

* By completing this challenge, you should be able to:

  * Map application workflows.
  * Identify business rules and invariants.
  * Build state-transition diagrams.
  * Test workflow sequencing.
  * Test step skipping.
  * Test request replay.
  * Test duplicate submission.
  * Test stale-state behavior.
  * Test quantity and value boundaries.
  * Analyze coupon and promotion logic.
  * Test credit and balance workflows.
  * Test approval processes.
  * Analyze role-dependent workflows.
  * Identify race-condition candidates.
  * Establish race-condition baselines.
  * Perform controlled concurrency testing.
  * Distinguish concurrency noise from real impact.
  * Verify persistent final state.
  * Validate business impact safely.
  * Document complex logic vulnerabilities professionally.

---

# Challenge Scenario

* You are assessing an authorized application containing:

```text
Registration

↓

Account Verification

↓

Subscription / Purchase

↓

Coupon / Credit Application

↓

Order Creation

↓

Payment

↓

Approval / Fulfillment

↓

Cancellation / Refund
```

* The application also contains:

```text
Invitations

Rewards

Credits

Limits

Role Changes

Resource Transfers
```

* Your objective is to determine whether users can violate intended business rules through request manipulation, workflow abuse, or concurrent execution.

---

# Challenge Deliverables

* Produce:

```text
Business-Rule-Inventory.md

Workflow-Map.md

State-Transition-Matrix.md

Business-Invariant-Matrix.md

Race-Candidate-Matrix.md

Concurrency-Test-Results.md

Validated-Findings/

Evidence/

Business-Logic-Summary.md
```

---

# Phase 1 — Understand the Business Model

* Before testing, determine what the application actually does.

---

## Identify

```text
Users

Roles

Resources

Products

Plans

Payments

Credits

Rewards

Approvals

Limits

State Changes
```

---

## Ask

```text
What Can a User Create?

What Can a User Buy?

What Can a User Change?

What Requires Payment?

What Requires Approval?

What Has a Limit?

What Can Happen Only Once?

What Depends on Previous State?
```

---

# Phase 2 — Build the Business Rule Inventory

| Feature    | Rule                               | Security Importance |
| ---------- | ---------------------------------- | ------------------- |
| Coupon     | One use per account                | Financial           |
| Trial      | One active trial                   | Financial           |
| Invitation | Only authorized members can invite | Access control      |
| Refund     | Cannot exceed payment              | Financial           |
| Approval   | Must precede fulfillment           | Workflow integrity  |

---

# Phase 3 — Identify Business Invariants

* An invariant is a condition that should always remain true.

---

## Examples

```text
Account Balance Cannot Become Invalid

Refund Cannot Exceed Original Payment

Single-Use Coupon Cannot Be Used Twice

User Cannot Approve Their Own Restricted Request

Resource Cannot Belong to Two Exclusive Owners

Inventory Cannot Be Oversold

One-Time Reward Cannot Be Claimed Repeatedly
```

---

# Core Principle

```text
Business Logic Testing

=

Find a Rule That Must Always Hold

↓

Try to Make the Application Break It
```

---

# Phase 4 — Map Workflows

* Document important workflows step by step.

---

## Example

```text
Add Item

↓

Apply Coupon

↓

Review Price

↓

Create Order

↓

Pay

↓

Fulfill
```

---

## Record

```text
Step

Endpoint

Method

Required State

Input

Output

Next State

Role
```

---

# Phase 5 — Build State-Transition Diagrams

* Example:

```text
Draft

↓

Pending

↓

Approved

↓

Completed
```

* Alternative paths:

```text
Pending

↓

Rejected
```

```text
Approved

↓

Cancelled
```

---

## Ask

```text
Which Transitions Are Allowed?

Which Are Forbidden?

Who Can Trigger Each Transition?

Can States Be Skipped?

Can Completed States Be Reversed?
```

---

# Phase 6 — Establish a Valid Baseline

* Complete the workflow normally.

* Capture every request in Burp.

---

## Record

```text
Request Sequence

Identifiers

Tokens

Prices

Quantities

State Values

Server Responses

Final State
```

---

# Phase 7 — Test Step Skipping

* Attempt controlled workflow variations.

---

## Example

```text
Expected:

Create

↓

Verify

↓

Approve

↓

Complete
```

* Test:

```text
Create

↓

Complete
```

---

## Determine

```text
Does the Server Verify Prerequisites

or

Trust That Earlier UI Steps Occurred?
```

---

# Phase 8 — Test Out-of-Order Execution

* Replay valid workflow requests in a different order.

---

## Example

```text
Normal:

A → B → C
```

* Test:

```text
A → C → B
```

* Then verify the final state.

---

# Phase 9 — Test Repeated Actions

* Identify actions intended to happen:

```text
Once Per Account

Once Per Order

Once Per Invitation

Once Per Transaction

Once Per Day

Once Per Resource
```

---

## Workflow

```text
Perform Valid Action

↓

Confirm Success

↓

Replay Same Request

↓

Retrieve Final State

↓

Check Whether Effect Occurred Again
```

---

# Core Rule

```text
Duplicate HTTP Success

≠

Duplicate Business Effect
```

* Always inspect persistent state.

---

# Phase 10 — Test Stale Requests

* Capture a valid request.

* Change the application state.

* Replay the old request.

---

## Example

```text
Capture Discounted Checkout

↓

Remove Discount / Change Cart

↓

Replay Old Checkout Request
```

---

## Ask

```text
Does the Server Recalculate Current State?

or

Trust Stale Client-Supplied State?
```

---

# Phase 11 — Test Client-Supplied Business Values

* Identify values such as:

```text
price

total

discount

quantity

credit

balance

role

status

fee

shipping

tax
```

---

## Ask

```text
Is This Value Calculated by the Server?

or

Trusted From the Client?
```

---

# Phase 12 — Test Numeric Boundaries

* Use safe controlled values around expected limits.

---

## Examples

```text
0

1

Maximum

Maximum + 1

Negative Values

Decimal Boundaries

Large but Safe Values
```

---

## Candidate Fields

```text
Quantity

Price

Credit

Discount

Reward Points

Transfer Amount

Refund Amount
```

---

# Phase 13 — Test Quantity Logic

* Determine whether the application enforces:

```text
Minimum Quantity

Maximum Quantity

Inventory

Per-User Limit

Per-Order Limit
```

---

## Verify

```text
UI Restriction

vs

Server Enforcement
```

---

# Phase 14 — Test Price Integrity

* Map where price values appear.

---

## Compare

```text
Product Price

Cart Price

Checkout Price

Payment Amount

Order Record
```

---

## Core Question

```text
Which System Is the Source of Truth?
```

* Sensitive totals should normally be derived from trusted server-side state.

---

# Phase 15 — Test Coupon Logic

* Map coupon rules:

```text
Eligible Products

Minimum Spend

Expiration

User Eligibility

Usage Count

Combination Rules

Maximum Discount
```

---

## Test

```text
Reuse

Multiple Application

Remove and Reapply

Different Account

Different Cart

Changed Quantity

Changed Product

Stale Checkout
```

* Use only test coupons and controlled accounts.

---

# Phase 16 — Test Promotion Stacking

* Determine whether multiple promotions are intended to combine.

---

## Example

```text
Coupon A

+

Coupon B

+

Account Credit

+

Referral Reward
```

* Verify server-side rules governing combinations.

---

# Phase 17 — Test Credits and Balances

* Map:

```text
Credit Creation

↓

Credit Application

↓

Balance Update

↓

Refund / Reversal
```

---

## Test Invariants

```text
Credit Cannot Be Spent Twice

Balance Cannot Go Below Allowed Limit

Refund Cannot Create Extra Credit

Reversal Cannot Duplicate Value
```

---

# Phase 18 — Test Refund Logic

* Use controlled transactions.

---

## Test

```text
Full Refund

Partial Refund

Repeated Refund

Refund After Cancellation

Refund After Credit

Refund Boundary
```

---

## Invariant

```text
Total Refunded

≤

Eligible Original Amount
```

---

# Phase 19 — Test Account Lifecycle Logic

* Map:

```text
Registration

Verification

Activation

Suspension

Deletion

Recovery
```

---

## Ask

```text
Can Verification Be Skipped?

Can Deleted Accounts Still Act?

Can Suspended Sessions Continue?

Can Old Tokens Restore Access?

Can Restricted States Trigger Normal Actions?
```

---

# Phase 20 — Test Invitation Workflows

* Map:

```text
Invite Created

↓

Invite Sent

↓

Invite Accepted

↓

Membership Created
```

---

## Test

```text
Replay

Expired Invite

Revoked Invite

Different Controlled User

Role Modification

Cross-Tenant Use
```

---

# Phase 21 — Test Approval Workflows

* Examples:

```text
Expense Approval

Account Approval

Content Approval

Refund Approval

Access Request
```

---

## Test

```text
Self-Approval

Lower-Role Approval

Step Skipping

Repeated Approval

Approval After Rejection

Approval After Cancellation
```

---

# Phase 22 — Test State Manipulation

* State values may appear as:

```text
status

state

approved

verified

completed

active
```

* Determine whether client-controlled state changes are authorized.

---

## Core Rule

```text
State Transition Request

Must Validate:

Current State

+

Requested State

+

Actor Permission

+

Business Preconditions
```

---

# Phase 23 — Test Role-Dependent Workflows

* Compare identical operations as:

```text
User

Manager

Administrator
```

---

## Ask

```text
Can a Lower Role Trigger the Endpoint Directly?

Can a User Modify the Role Field?

Can a User Create a Privileged State Indirectly?
```

---

# Phase 24 — Test Resource Ownership Changes

* Workflows may allow:

```text
Transfer

Assign

Share

Move

Merge
```

---

## Verify

```text
Who Can Initiate?

Who Can Accept?

Is Ownership Rechecked?

Are Old Permissions Removed?

Can Transfers Be Replayed?
```

---

# Phase 25 — Test Duplicate Submission

* Candidate operations include:

```text
Order Creation

Payment

Coupon Redemption

Reward Claim

Invitation Acceptance

Refund

Transfer

Registration
```

---

## Workflow

```text
Send Once

↓

Record Effect

↓

Send Duplicate

↓

Compare Final State
```

---

# Phase 26 — Understand Race Conditions

* A race condition occurs when the result depends on the timing or interleaving of operations.

---

## Typical Pattern

```text
Request A:

Check Rule

↓

Modify State
```

```text
Request B:

Check Same Rule

↓

Modify Same State
```

* If both checks occur before either update becomes visible:

```text
Invariant May Be Broken
```

---

# Phase 27 — Identify Race Candidates

* Look for operations involving:

```text
Single-Use Actions

Balances

Credits

Coupons

Inventory

Limits

Rewards

Invitations

Votes

Likes

Transfers

Refunds

File Replacement

Username Claims

Resource Creation
```

---

# Race Candidate Matrix

| Operation | Invariant      | Shared State  | Potential Impact |
| --------- | -------------- | ------------- | ---------------- |
| Coupon    | One use        | Usage counter | Extra discount   |
| Credit    | Spend once     | Balance       | Financial        |
| Reward    | Claim once     | Claim state   | Duplicate reward |
| Inventory | Limited stock  | Stock count   | Oversell         |
| Invite    | One acceptance | Membership    | Access issue     |

---

# Phase 28 — Establish a Sequential Baseline

* Before concurrency testing:

```text
Send Request Once

↓

Record Result

↓

Send Again Sequentially

↓

Record Result
```

* Determine expected duplicate behavior.

---

# Why This Matters

* If sequential requests already break the rule:

```text
It Is Primarily a Logic / Replay Flaw

Not Necessarily a Race Condition
```

---

# Phase 29 — Define the Race Invariant

* Write the expected rule explicitly.

---

## Example

```text
Expected:

Coupon Can Produce One Discount Effect
```

* Race hypothesis:

```text
Two Near-Simultaneous Redemptions

↓

Both Pass Eligibility Check

↓

Two Discount Effects Occur
```

---

# Phase 30 — Use Controlled Concurrency

* Burp tools may help coordinate requests in authorized environments.

* Keep request counts minimal.

---

## Conceptual Test

```text
Prepare Equivalent Requests

↓

Synchronize Dispatch

↓

Send Small Controlled Batch

↓

Retrieve Final State

↓

Compare Against Invariant
```

---

# Important

* Do not perform uncontrolled high-volume flooding.

* Race-condition testing is about synchronization, not raw request volume.

---

# Phase 31 — Verify Final State

* Do not rely only on HTTP responses.

---

## Check

```text
Database-Backed State Through UI/API

Balance

Order Count

Coupon Count

Reward Count

Inventory

Transaction History

Audit Log
```

---

# Core Rule

```text
Multiple 200 Responses

≠

Confirmed Race Condition
```

* The business invariant must actually be violated.

---

# Phase 32 — Distinguish Race Noise

* Concurrent requests may produce:

```text
Timeouts

5xx Errors

Connection Errors

Duplicate Responses

Temporary UI Inconsistency
```

* These alone do not prove a vulnerability.

---

## Require

```text
Reproducible Concurrency-Dependent Behavior

+

Persistent Security-Relevant State Difference
```

---

# Phase 33 — Compare Sequential and Concurrent Results

| Test                 | Requests | Final Effect |
| -------------------- | -------: | ------------ |
| Sequential           |        1 | Expected     |
| Sequential duplicate |        2 | One effect   |
| Concurrent           |        2 | Test         |
| Concurrent repeat    |        2 | Reproduce    |

* This comparison helps demonstrate that timing is necessary.

---

# Phase 34 — Test Limit Enforcement

* Example rule:

```text
Maximum 5 Resources Per User
```

* Sequential baseline:

```text
Resource 1

...

Resource 5

Resource 6 → Denied
```

* Controlled race hypothesis:

```text
Multiple Requests Near Limit

↓

Concurrent Limit Check

↓

Limit Exceeded?
```

---

# Phase 35 — Test One-Time Operations

* Candidates:

```text
Claim Reward

Redeem Token

Accept Invite

Verify Action

Use Coupon

Apply Referral
```

---

## Invariant

```text
One Token

=

One Valid Business Effect
```

---

# Phase 36 — Test Balance Operations

* Financial or credit-like operations require extra care.

---

## Invariant Examples

```text
Spend ≤ Available Balance

Transfer Cannot Duplicate Value

Debit and Credit Remain Consistent

Refund Cannot Exceed Eligible Amount
```

* Use sandbox funds, test credits, or non-production environments whenever possible.

---

# Phase 37 — Test Multi-Step Races

* Some races occur between different operations.

---

## Example

```text
Request A:

Use Credit
```

```text
Request B:

Withdraw / Transfer Same Credit
```

* The issue may exist only when two different workflows compete for the same state.

---

# Phase 38 — Test State-Transition Races

* Example:

```text
Request A:

Approve
```

```text
Request B:

Cancel
```

* Ask:

```text
Can the Resource Reach an Impossible Final State?

Can Both Effects Occur?
```

---

# Phase 39 — Test Object-Creation Races

* Candidate uniqueness rules:

```text
One Username

One Coupon Claim

One Membership

One Reservation

One Resource Per Account
```

* Verify whether concurrent creation violates uniqueness or business constraints.

---

# Phase 40 — Analyze Idempotency

* Idempotency prevents repeated requests from producing unintended duplicate effects.

---

## Review

```text
Idempotency Keys

Transaction IDs

Request IDs

Replay Tokens

One-Time Nonces
```

---

## Ask

```text
Is the Same Operation Safe to Retry?

Can the Same Key Be Reused?

Is the Key Bound to the User?

Is It Bound to the Request Body?

How Long Is It Stored?
```

---

# Phase 41 — Test Retry Behavior

* Real systems retry requests due to:

```text
Network Failure

Timeout

Client Retry

Proxy Retry

Payment Gateway Retry
```

* Determine whether retries can duplicate business effects.

---

# Phase 42 — Test Partial Failure

* Multi-step operations may fail halfway.

---

## Example

```text
Debit Balance

↓

Create Order

↓

Send Confirmation
```

* Ask:

```text
What Happens if Step 2 Fails?

Is Step 1 Rolled Back?

Can Retry Duplicate Step 1?
```

---

# Phase 43 — Test Rollback and Recovery

* Controlled failure conditions may reveal:

```text
Partial Transactions

Duplicate Objects

Orphaned Records

Incorrect Balances

Reusable Tokens
```

* Avoid intentionally disrupting external payment or production infrastructure.

---

# Phase 44 — Test Time-Based Rules

* Candidate rules:

```text
Coupon Expiration

Trial Expiration

Reservation Timeout

OTP Validity

Invitation Expiration

Rate Window

Subscription Renewal
```

---

## Test Boundaries

```text
Just Before Expiration

At Expiration

Just After Expiration
```

* Use safe test data and controlled timing.

---

# Phase 45 — Test Cross-Feature Logic

* Business rules may fail when two legitimate features interact.

---

## Examples

```text
Coupon

+

Refund
```

```text
Credit

+

Cancellation
```

```text
Invitation

+

Role Change
```

```text
Account Deletion

+

Active Session
```

---

# Key Mindset

```text
Feature A Secure Alone

+

Feature B Secure Alone

≠

A + B Secure Together
```

---

# Phase 46 — Build the Business Invariant Matrix

| Workflow | Invariant | Sequential Test | Concurrent Test | Result  |
| -------- | --------- | --------------- | --------------- | ------- |
| Coupon   | One use   | Pass            | Pending         | Pending |
| Refund   | ≤ payment | Pass            | Pending         | Pending |
| Reward   | One claim | Pass            | Pending         | Pending |
| Limit    | Max N     | Pass            | Pending         | Pending |

---

# Phase 47 — Validate Candidate Findings

* Logic flaws require clear proof of the violated rule.

---

## Validation Workflow

```text
Document Intended Rule

↓

Capture Normal Workflow

↓

Change One Variable

↓

Reproduce Unexpected Behavior

↓

Verify Persistent Final State

↓

Use Control Test

↓

Determine Security / Business Impact
```

---

# Example — Workflow Bypass

```text
Expected:

Approval Required Before Completion
```

```text
Observed:

Unapproved Controlled Request

↓

Direct Completion Endpoint Called

↓

Server Marks Request Completed

↓

Approval Requirement Bypassed
```

---

# Example — Race Condition

```text
Expected:

Reward Can Be Claimed Once
```

```text
Sequential Duplicate:

One Reward
```

```text
Concurrent Requests:

Two Reward Effects
```

```text
Final State:

Persistent Duplicate Reward
```

---

# Phase 48 — Determine Impact

* Potential impact includes:

```text
Financial Loss

Duplicate Credits

Unauthorized Discounts

Inventory Abuse

Limit Bypass

Unauthorized Approval

Privilege Escalation

Cross-Tenant Access

Duplicate Rewards

Unauthorized Resource Creation

State Corruption
```

---

# Phase 49 — Identify Root Cause

* Common root causes include:

```text
Client-Side Business Enforcement

Missing Server-Side Preconditions

Trust in Client-Supplied Values

Weak State Validation

Replayable Operations

Missing Idempotency

Non-Atomic Check-and-Update

Missing Transaction Isolation

Inconsistent Cross-Feature Rules

Weak Ownership Revalidation
```

---

# Phase 50 — Collect Evidence

* Preserve:

```text
Business Rule

Expected Behavior

Account / Role Context

Baseline Sequence

Modified Sequence

Sequential Control

Concurrent Test

Responses

Final Persistent State

Impact
```

---

## Evidence Structure

```text
F001/
│
├── 01-business-rule.md
├── 02-baseline-flow.md
├── 03-baseline-request.txt
├── 04-test-request.txt
├── 05-sequential-control.txt
├── 06-concurrent-results.txt
├── 07-final-state.png
└── notes.md
```

---

# Capstone Challenge

## Stage 1 — Business Mapping

* Identify at least:

```text
10 Important Business Rules
```

* Where the application exposes sufficient functionality.

---

## Stage 2 — Workflow Mapping

* Map at least three important workflows from start to finish.

---

## Stage 3 — State Analysis

* Build state-transition diagrams for important resources.

---

## Stage 4 — Logic Testing

* Test:

```text
Step Skipping

Out-of-Order Requests

Replay

Stale Requests

Client-Supplied Values

Boundary Values
```

---

## Stage 5 — Financial / Value Logic

* Where applicable, test:

```text
Coupons

Credits

Balances

Refunds

Quantities

Limits
```

* using controlled test data.

---

## Stage 6 — Role and Approval Logic

* Test:

```text
Self-Approval

Lower-Role Actions

State Changes

Resource Transfers
```

---

## Stage 7 — Race Candidate Identification

* Identify operations involving:

```text
One-Time Rules

Limits

Shared Balances

Shared Inventory

Unique Resources
```

---

## Stage 8 — Sequential Baseline

* Confirm expected behavior without concurrency.

---

## Stage 9 — Controlled Concurrency

* Use the minimum number of synchronized requests necessary.

---

## Stage 10 — Final-State Verification

* Verify persistent business state after each test.

---

## Stage 11 — Validation

* Classify each candidate:

```text
Confirmed

Rejected

Needs More Evidence
```

---

# Challenge Success Criteria

* You should be able to answer:

```text
What Are the Important Business Rules?

Which Rules Must Always Remain True?

Which Workflows Exist?

Which States Exist?

Which Transitions Are Allowed?

Can Steps Be Skipped?

Can Requests Be Replayed?

Are Stale Requests Revalidated?

Are Sensitive Values Calculated Server-Side?

Are Limits Enforced Server-Side?

Can One-Time Actions Occur Twice?

Can Concurrent Requests Break an Invariant?

Is the Behavior Timing-Dependent?

Does the Final Persistent State Show Real Impact?

Are Retries Idempotent?

Are Partial Failures Handled Safely?

Do Interacting Features Preserve Business Rules?
```

---

# Business Logic Testing Workflow

```text
Understand Application

↓

Identify Business Rules

↓

Define Invariants

↓

Map Workflows

↓

Map States

↓

Capture Valid Baselines

↓

Test Sequence

↓

Test Replay

↓

Test Stale State

↓

Test Values / Limits

↓

Test Roles / Approvals

↓

Test Cross-Feature Interactions

↓

Identify Race Candidates

↓

Establish Sequential Control

↓

Perform Controlled Concurrency

↓

Verify Final State

↓

Validate Impact

↓

Collect Evidence

↓

Report
```

---

# Common Mistakes

* Starting with payloads before understanding the application.
* Testing only technical vulnerabilities.
* Failing to write down the expected business rule.
* Assuming UI restrictions are server-side rules.
* Ignoring workflow sequencing.
* Ignoring stale requests.
* Testing only one step of a multi-step process.
* Trusting client-supplied prices or totals without checking server behavior.
* Using destructive financial tests.
* Testing real payment methods unnecessarily.
* Ignoring coupons, credits, rewards, and refunds.
* Ignoring role-dependent workflow behavior.
* Ignoring state transitions.
* Reporting duplicate HTTP responses as duplicate business effects.
* Calling every replay flaw a race condition.
* Performing high-volume concurrency tests.
* Using request volume instead of synchronization.
* Failing to establish a sequential baseline.
* Failing to verify persistent final state.
* Treating transient 5xx responses as race-condition proof.
* Ignoring idempotency.
* Ignoring retry behavior.
* Ignoring partial failures.
* Ignoring rollback behavior.
* Ignoring time-based boundaries.
* Testing features only in isolation.
* Failing to compare cross-feature interactions.
* Overstating theoretical financial impact.
* Failing to restore controlled test state.

---

# Professional Tips

* Learn the application's business model before hunting for logic flaws.
* Write every important rule as an invariant.
* Ask what must never happen.
* Map workflows before mutating requests.
* Capture a complete valid baseline.
* Change one workflow property at a time.
* Test whether steps can be skipped, reordered, repeated, or replayed.
* Replay requests after the underlying state changes.
* Identify which values are client supplied and which should be server derived.
* Test numeric boundaries safely.
* Use sandbox transactions and controlled credits whenever possible.
* Compare rules across coupons, refunds, credits, and cancellations.
* Test role changes and approval workflows with controlled identities.
* Map state transitions explicitly.
* Look for operations that check state and then modify the same state.
* Establish sequential duplicate behavior before testing races.
* Use synchronized requests rather than large request volumes.
* Keep concurrency batches minimal.
* Verify final state through an independent request or UI.
* Distinguish response anomalies from actual invariant violations.
* Test idempotency for operations likely to be retried.
* Consider partial failures in multi-step transactions.
* Test important rules at time boundaries.
* Combine features mentally: many strong logic bugs exist between two valid workflows.
* Document the violated business rule clearly in the report.
* Quantify impact conservatively from demonstrated evidence.

---

# Practice Tasks

1. Select an authorized application with meaningful workflows.

2. Identify its users and roles.

3. Identify important resources.

4. Identify financial or value-related operations.

5. Write at least ten business rules.

6. Convert important rules into invariants.

7. Map one registration or onboarding workflow.

8. Map one resource-creation workflow.

9. Map one transaction or approval workflow.

10. Build state-transition diagrams.

11. Capture a normal workflow in Burp.

12. Record all important IDs and state values.

13. Test skipping one intermediate step.

14. Test one out-of-order request sequence.

15. Replay a completed action.

16. Verify whether the business effect repeats.

17. Capture a valid request and replay it after state changes.

18. Identify client-supplied prices or totals.

19. Test safe numeric boundaries.

20. Test quantity limits.

21. Test maximum limits.

22. Test one-time restrictions.

23. Test coupon reuse where available.

24. Test promotion combination rules.

25. Test credits with controlled values.

26. Test refund boundaries using sandbox transactions.

27. Test account lifecycle states.

28. Test invitation replay.

29. Test expired or revoked invitations.

30. Test approval sequencing.

31. Test self-approval where relevant and authorized.

32. Test lower-role access to workflow endpoints.

33. Test state parameters.

34. Test resource transfer rules.

35. Identify duplicate-submission candidates.

36. Build a race-candidate matrix.

37. Select one safe race candidate.

38. Establish sequential baseline behavior.

39. Write the expected invariant.

40. Prepare equivalent requests.

41. Send a minimal synchronized test.

42. Retrieve final state.

43. Compare sequential and concurrent results.

44. Repeat only if necessary for reproducibility.

45. Test one limit-boundary race safely.

46. Test one one-time-action race safely.

47. Review idempotency controls.

48. Test retry behavior.

49. Analyze a multi-step transaction for partial failure.

50. Review rollback behavior.

51. Test one time-based rule near its boundary.

52. Identify two interacting features.

53. Test whether their combined behavior preserves invariants.

54. Build the business invariant matrix.

55. Validate every suspicious result.

56. Classify confirmed, rejected, or unresolved candidates.

57. Determine demonstrated impact.

58. Identify root cause.

59. Collect minimal sufficient evidence.

60. Restore controlled application state.

61. Write the final business-logic summary.

---

# Interview Questions

1. What is a business logic vulnerability?
2. Why are business logic flaws difficult for automated scanners to detect?
3. What is a business invariant?
4. How do you identify important business rules?
5. Why should workflows be mapped before testing?
6. What is a state-transition diagram?
7. How do you test step skipping?
8. How do you test out-of-order workflow execution?
9. How do you test replay attacks in business workflows?
10. Why does a duplicate HTTP 200 response not prove duplicate impact?
11. What is a stale-request vulnerability?
12. Why should prices and totals usually be calculated server-side?
13. How do you test numeric business boundaries?
14. What should be tested in coupon functionality?
15. What is promotion stacking?
16. What invariants should be tested around credits and balances?
17. How do you test refund logic?
18. What account-lifecycle states should be reviewed?
19. What security issues can exist in invitation workflows?
20. How do you test approval workflows?
21. What checks should a secure state transition perform?
22. How do you test role-dependent business logic?
23. What risks exist in resource-transfer workflows?
24. What is duplicate submission?
25. What is a race condition?
26. What is a TOCTOU-style issue conceptually?
27. Which operations are common race-condition candidates?
28. Why should you establish a sequential baseline first?
29. How do you distinguish a replay flaw from a race condition?
30. Why is synchronization more important than request volume?
31. How do you test race conditions safely?
32. Why must final persistent state be verified?
33. Why do multiple successful responses not prove a race condition?
34. How do you distinguish concurrency noise from a real vulnerability?
35. How do you test limit enforcement for races?
36. How do you test one-time operations?
37. What risks exist in concurrent balance operations?
38. What is a multi-endpoint race condition?
39. What is a state-transition race?
40. What is an object-creation race?
41. What is idempotency?
42. What is an idempotency key?
43. What properties should secure idempotency controls have?
44. Why are retry behaviors security relevant?
45. What risks exist in partial transaction failures?
46. Why should time-based rules be tested at boundaries?
47. What are cross-feature business logic vulnerabilities?
48. How do you demonstrate business impact without overstating it?
49. What evidence should a race-condition report contain?
50. Walk me through your complete business logic and race-condition methodology.

---

# Quick Revision

* Business logic:

```text
Rule

↓

Invariant

↓

Workflow

↓

Mutation

↓

Final State
```

* Workflow testing:

```text
Skip

Reorder

Replay

Stale State

Boundary

Cross-Feature
```

* Race condition:

```text
Check

↓

Concurrent Requests

↓

State Changes

↓

Invariant Broken?
```

* Validation:

```text
Sequential Control

vs

Concurrent Test
```

* Core rule:

```text
Multiple Successful Responses

≠

Multiple Business Effects
```

* Strong evidence:

```text
Expected Invariant

↓

Controlled Mutation / Concurrency

↓

Persistent Invariant Violation

↓

Meaningful Impact
```

---

# Business Logic & Race Conditions Quick Checklist

```text
BUSINESS MODEL

[ ] Users
[ ] Roles
[ ] Resources
[ ] Values
[ ] Limits
[ ] Approvals
[ ] States


RULES

[ ] Business rules
[ ] Invariants
[ ] One-time actions
[ ] Limits
[ ] Preconditions


WORKFLOWS

[ ] Normal baseline
[ ] Step skipping
[ ] Reordering
[ ] Replay
[ ] Stale requests
[ ] Duplicate submission


VALUES

[ ] Price
[ ] Quantity
[ ] Discount
[ ] Credit
[ ] Balance
[ ] Refund
[ ] Boundaries


STATE

[ ] Current state
[ ] Requested state
[ ] Actor permission
[ ] Preconditions
[ ] Forbidden transitions


ROLES

[ ] User
[ ] Privileged user
[ ] Admin
[ ] Self-approval
[ ] Ownership changes


RACE CONDITIONS

[ ] Candidate identified
[ ] Shared state
[ ] Invariant defined
[ ] Sequential baseline
[ ] Minimal concurrency
[ ] Final state verified
[ ] Reproducible


IDEMPOTENCY

[ ] Retry behavior
[ ] Request IDs
[ ] Idempotency keys
[ ] Duplicate effect
[ ] Key binding


FAILURES

[ ] Partial failure
[ ] Rollback
[ ] Recovery
[ ] Stale tokens
[ ] Duplicate operations


CROSS-FEATURE

[ ] Coupon + refund
[ ] Credit + cancellation
[ ] Invite + role
[ ] Delete + active session
[ ] Other interactions


VALIDATION

[ ] Expected rule documented
[ ] Controlled accounts/data
[ ] Baseline captured
[ ] One variable changed
[ ] Persistent state checked
[ ] Security impact demonstrated
[ ] Root cause identified
```

---

# Key Takeaways

* Business logic testing begins with understanding how the application is supposed to work rather than immediately sending attack payloads.
* Important business rules should be expressed as invariants: conditions that must remain true regardless of request order, repetition, timing, or client behavior.
* Workflow mapping reveals prerequisites, state transitions, role requirements, and assumptions that may not be enforced server-side.
* Important workflow mutations include step skipping, reordering, replay, stale requests, duplicate submission, and cross-feature interaction.
* UI restrictions do not prove that business rules are enforced by the server.
* Client-supplied values such as price, discount, total, balance, status, or role require careful trust analysis.
* Coupon, credit, reward, refund, invitation, approval, and transfer workflows frequently contain application-specific security rules.
* State transitions should validate the current state, requested state, actor authorization, and required business preconditions.
* A race condition exists when concurrency causes a security-relevant result that does not occur under equivalent sequential execution.
* Sequential baseline testing is essential for distinguishing race conditions from ordinary replay or logic flaws.
* Race-condition testing depends on synchronization and shared state, not simply sending a large number of requests.
* High-volume flooding is unnecessary and may be unsafe; minimal synchronized batches are preferable.
* Multiple successful HTTP responses do not prove a race condition—the persistent business state must violate the expected invariant.
* Common race candidates include single-use actions, balances, credits, inventory, coupons, rewards, limits, invitations, transfers, refunds, and unique-resource creation.
* Multi-endpoint races can occur when different operations compete over the same underlying state.
* Idempotency controls help prevent retries and duplicate submissions from creating repeated business effects.
* Partial failures and retry behavior can expose inconsistent transactions, duplicated actions, or reusable one-time resources.
* Time-sensitive rules should be tested at controlled boundaries such as immediately before and after expiration.
* Strong business logic vulnerabilities often emerge from interactions between two individually legitimate features.
* Findings should clearly document the intended rule, normal workflow, manipulated sequence or concurrency condition, final persistent state, and demonstrated impact.
* The complete methodology is **understand the business → identify rules → define invariants → map workflows and states → capture valid baselines → test sequence, replay, stale state, values, limits, roles, and cross-feature interactions → identify race candidates → establish sequential controls → perform minimal controlled concurrency → verify persistent final state → validate impact → collect evidence → report**.
