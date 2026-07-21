# Business Logic Challenge

> **Module 18 — Capstone, Interview & Quick Reference**

> **Progress**

```text
███████░░░░░░░░ 7 / 15 Files
```

---

## Overview

* Business logic vulnerabilities occur when an application correctly processes requests from a technical perspective but allows users to violate the intended rules of the business.

* Unlike many traditional vulnerabilities, business logic flaws may not involve:

  * Injection.
  * Malformed syntax.
  * Dangerous characters.
  * Memory corruption.
  * Obvious server errors.

* Instead, they often involve valid application functionality used in an unintended sequence, context, quantity, or combination.

```text
Valid Request

+

Unexpected Workflow

+

Missing Business Rule

=

Business Logic Vulnerability
```

* Examples include:

  * Purchasing below the intended price.
  * Reusing one-time discounts.
  * Skipping required workflow stages.
  * Performing actions in the wrong order.
  * Repeating operations intended to occur once.
  * Manipulating quantities or prices.
  * Bypassing limits.
  * Exploiting stale application state.
  * Abusing invitation or referral systems.
  * Violating approval workflows.
  * Exploiting race conditions.
  * Creating inconsistent states across related operations.

* Effective testing requires understanding:

```text
What Should Happen?

↓

What Assumptions Does the Application Make?

↓

Can Those Assumptions Be Violated?
```

---

## Objectives

* By completing this challenge, you should be able to:

  * Understand application business rules.
  * Map workflows and state transitions.
  * Identify trusted client-controlled values.
  * Identify hidden business assumptions.
  * Test sequence manipulation.
  * Test step skipping.
  * Test replay and reuse.
  * Test quantity and numeric boundaries.
  * Test pricing and discount logic.
  * Test limits and quotas.
  * Test invitation and referral systems.
  * Test approval workflows.
  * Test stale-state behavior.
  * Test multi-account interactions.
  * Analyze race-condition candidates.
  * Test idempotency.
  * Identify inconsistent validation across workflows.
  * Validate business impact safely.
  * Build reusable business-logic test matrices.

---

# Challenge Scenario

* You are testing an authorized application containing workflows such as:

```text
Registration

Subscription

Shopping / Checkout

Coupons

Credits

Invitations

Referrals

Approvals

Account Changes

Resource Creation

Resource Deletion
```

* Your objective is to determine whether the application's intended business rules can be violated using otherwise legitimate requests.

---

# Challenge Deliverables

* Produce:

```text
Business-Rules.md

Workflow-Maps.md

Business-Logic-Test-Matrix.md

State-Transition-Matrix.md

Race-Condition-Candidates.md

Validated-Findings/

Evidence/

Business-Logic-Summary.md
```

---

# Phase 1 — Understand the Business

* Before testing logic, understand what the application is designed to do.

---

## Ask

```text
What Does the Application Sell or Provide?

Who Are the Users?

What Roles Exist?

What Has Monetary Value?

What Has Privilege Value?

Which Actions Have Limits?

Which Actions Should Happen Only Once?

Which Workflows Require Multiple Steps?

Which Actions Require Approval?
```

---

# Phase 2 — Identify Business Assets

* Business assets may include:

```text
Money

Credits

Points

Coupons

Subscriptions

Licenses

Inventory

Orders

Reservations

Invitations

Referrals

Approvals

Premium Features

Account Privileges
```

---

## Asset Inventory

| Asset      | Owner        | Value    | Creation | Modification | Consumption  |
| ---------- | ------------ | -------- | -------- | ------------ | ------------ |
| Credit     | User         | Monetary | Refund   | Adjustment   | Purchase     |
| Coupon     | Campaign     | Discount | Admin    | Rules        | Checkout     |
| Invitation | Organization | Access   | Admin    | Limited      | Registration |

---

# Phase 3 — Document Business Rules

* Write expected rules explicitly.

---

## Example

```text
RULE-001

A coupon may be used once per account.
```

```text
RULE-002

Order quantity must be greater than zero.
```

```text
RULE-003

Only approved orders may be fulfilled.
```

```text
RULE-004

A free trial may be activated once per eligible customer.
```

---

# Why This Matters

```text
Undefined Expected Behavior

↓

Weak Testing

↓

Ambiguous Finding
```

* Business logic testing requires a clear expected rule before attempting to violate it.

---

# Phase 4 — Map Workflows

* Represent each important workflow as a sequence.

---

## Example — Purchase

```text
Select Product

↓

Add to Cart

↓

Apply Discount

↓

Calculate Total

↓

Choose Payment

↓

Pay

↓

Order Created

↓

Fulfillment
```

---

## Example — Approval

```text
Create Request

↓

Submit

↓

Review

↓

Approve

↓

Execute
```

---

# Phase 5 — Build State Machines

* Many business workflows are state machines.

---

## Example

```text
Draft

↓

Submitted

↓

Approved

↓

Completed
```

* Alternative transitions:

```text
Submitted

↓

Rejected
```

---

## State Transition Matrix

| Current State | Action  | Expected Next State | Allowed Actor |
| ------------- | ------- | ------------------- | ------------- |
| Draft         | Submit  | Submitted           | Owner         |
| Submitted     | Approve | Approved            | Approver      |
| Submitted     | Reject  | Rejected            | Approver      |
| Approved      | Execute | Completed           | System/Admin  |

---

# Core Question

```text
Can I Force an Invalid Transition?
```

---

# Phase 6 — Identify Business Assumptions

* Applications often assume:

```text
Users Follow the UI

Steps Occur in Order

Requests Are Sent Once

Values Come From Trusted Pages

Client-Side Calculations Are Honest

One User Controls One Account

Requests Do Not Arrive Simultaneously

State Does Not Change Between Steps
```

---

# Professional Testing Mindset

```text
Assumption

↓

Challenge It

↓

Observe Enforcement

↓

Verify Server-Side Rule
```

---

# Phase 7 — Capture Complete Workflows

* Use Burp Proxy and HTTP history to capture normal flows.

---

## Record

```text
Step

Endpoint

Method

Parameters

Cookies

Tokens

State Before

State After

Side Effects
```

---

## Workflow Table

| Step | Request      | State Before | State After     |
| ---- | ------------ | ------------ | --------------- |
| 1    | Add item     | Empty cart   | Item added      |
| 2    | Apply coupon | No discount  | Discount active |
| 3    | Checkout     | Cart ready   | Order pending   |
| 4    | Pay          | Pending      | Paid            |

---

# Phase 8 — Test Step Skipping

* Attempt to omit required workflow stages.

---

## Example

```text
Create Order

↓

Skip Payment

↓

Request Fulfillment
```

---

## Ask

```text
Does the Server Verify Previous State?

or

Assume the User Reached This Step Legitimately?
```

---

# Step-Skipping Workflow

```text
Normal Flow

↓

Identify Required Intermediate Step

↓

Capture Later Request

↓

Create Fresh Controlled Object

↓

Send Later Request Early

↓

Verify Final State
```

---

# Phase 9 — Test Out-of-Order Execution

* Try legitimate steps in an unintended order.

---

## Example

```text
Normal:

Submit

→

Approve

→

Execute
```

* Test:

```text
Submit

→

Execute
```

* Or:

```text
Execute

→

Approve
```

---

# Core Principle

```text
Endpoint Exists

≠

Action Is Valid in Every State
```

---

# Phase 10 — Test Request Replay

* Some operations should occur only once.

---

## Candidates

```text
Coupon Redemption

Refund

Credit Claim

Reward Claim

Invitation Acceptance

Passwordless Login Link

Referral Reward

Payment Confirmation

Withdrawal

Order Cancellation
```

---

## Workflow

```text
Perform Action Once

↓

Capture Request

↓

Confirm State Change

↓

Replay Exact Request

↓

Verify Whether Effect Repeats
```

---

# Important

```text
Same HTTP Response

≠

Same Business Effect
```

* Verify balances, counts, ownership, order state, or other persistent results.

---

# Phase 11 — Test One-Time Operations

* Identify actions expected to be:

```text
Once Per Account

Once Per Order

Once Per Token

Once Per Campaign

Once Per Device

Once Per Customer
```

---

## Ask

```text
What Defines "Once"?

Account ID?

Email?

Phone?

Session?

Device?

Payment Method?

Order ID?

Server-Side Redemption Record?
```

---

# Phase 12 — Test Numeric Boundaries

* Numeric inputs frequently encode business rules.

---

## Candidates

```text
Quantity

Price

Discount

Credit

Balance

Transfer Amount

Refund Amount

Percentage

Seat Count

Subscription Duration
```

---

## Boundary Classes

```text
Zero

Negative

Minimum - 1

Minimum

Maximum

Maximum + 1

Large Value

Decimal

Unexpected Precision
```

---

# Example

```text
quantity = 1

↓

Baseline
```

* Then test controlled boundaries such as:

```text
quantity = 0

quantity = -1
```

* Determine whether the server enforces the intended rule.

---

# Phase 13 — Test Client-Controlled Pricing

* Identify values such as:

```text
price

subtotal

discount

tax

total

currency

product_id

plan_id
```

---

## Core Rule

```text
Client May Select Product

But

Server Should Determine Authoritative Price
```

---

## Test

```text
Baseline Purchase

↓

Identify Price-Related Fields

↓

Modify One Field

↓

Submit

↓

Verify Server Recalculation

↓

Verify Final Transaction
```

---

# Phase 14 — Test Coupon and Discount Logic

* Map:

```text
Eligibility

Usage Limit

Expiration

Minimum Spend

Applicable Products

Account Restrictions

Stacking Rules
```

---

## Test Cases

```text
Reuse Same Coupon

Apply Multiple Coupons

Apply After Expiration

Apply Below Minimum Spend

Change Cart After Coupon

Remove Qualifying Item

Switch Product

Switch Account

Replay Redemption
```

---

# Important

* Use only test coupons, credits, or transactions authorized for assessment.

---

# Phase 15 — Test Cart and Checkout Recalculation

* Pricing state may change between:

```text
Cart

↓

Checkout

↓

Payment

↓

Order Creation
```

---

## Ask

```text
Is Price Recalculated?

Is Inventory Rechecked?

Is Discount Revalidated?

Is Currency Revalidated?

Is Ownership Revalidated?

Is Final Amount Bound to the Payment?
```

---

# Phase 16 — Test Quantity Manipulation

* Quantity logic may fail in:

```text
Cart Updates

Bulk Purchases

Inventory

Reservations

Licenses

Credits

Resource Allocation
```

---

## Verify

```text
Input Quantity

↓

Server Validation

↓

Calculated Price

↓

Inventory Effect

↓

Final Order
```

---

# Phase 17 — Test Limits and Quotas

* Identify restrictions such as:

```text
5 Requests Per Day

10 Team Members

1 Free Trial

3 Invitations

100 API Calls

1 Coupon Per User

Maximum Transfer Amount
```

---

## Test

```text
At Limit

↓

Limit + 1

↓

Alternate Endpoint

↓

Concurrent Request

↓

Different Session

↓

Different Workflow
```

---

# Core Question

```text
Is the Limit Enforced Centrally

or

Only in One Interface?
```

---

# Phase 18 — Test Free Trials and Promotions

* Promotions often depend on eligibility logic.

---

## Review

```text
Account

Customer Identity

Email

Phone

Payment Method

Organization

Device

Previous Subscription
```

---

## Ask

```text
What Defines a New Customer?

Can Eligibility State Be Reset?

Can Alternate Workflows Bypass the Check?
```

* Do not create fraudulent accounts or obtain unauthorized monetary benefit.

* Use controlled testing approved by the program.

---

# Phase 19 — Test Invitation Workflows

* Map:

```text
Create Invitation

↓

Send Token

↓

Recipient Accepts

↓

Membership Created

↓

Token Invalidated
```

---

## Test

```text
Reuse

Wrong Account

Wrong Organization

Expired Invitation

Revoked Invitation

Multiple Acceptance

Role Manipulation

Tenant Switching
```

---

# Phase 20 — Test Referral Logic

* Referral systems may involve:

```text
Referrer

Referred User

Eligibility

Conversion Event

Reward

Reward Limit
```

---

## Ask

```text
Can Users Refer Themselves?

Can Rewards Trigger Multiple Times?

Can the Conversion Event Be Replayed?

Can Eligibility Be Manipulated?
```

---

# Phase 21 — Test Approval Workflows

* Approval systems rely heavily on state and role.

---

## Example

```text
Requester

↓

Submits Request

↓

Approver

↓

Approves Request

↓

System Executes
```

---

## Test

```text
Self Approval

Approval Before Submission

Execution Before Approval

Repeated Approval

Approval After Rejection

Approval After Cancellation

Stale Approval Request
```

---

# Phase 22 — Test Separation of Duties

* Some workflows require different actors.

---

## Example

```text
User A

→

Creates Payment
```

```text
User B

→

Approves Payment
```

---

## Security Rule

```text
Creator

≠

Approver
```

* Test whether the server actually enforces this requirement.

---

# Phase 23 — Test Cancellation and Refund Logic

* State reversal creates complex logic.

---

## Workflow

```text
Purchase

↓

Payment

↓

Cancellation

↓

Refund
```

---

## Test

```text
Repeated Cancellation

Repeated Refund

Refund Before Payment

Refund Greater Than Payment

Refund After Full Refund

Cancel After Fulfillment

Replay Old Refund Request
```

---

* Use controlled transactions and avoid real financial impact.

---

# Phase 24 — Test State Changes Between Steps

* A workflow may begin under one state and finish under another.

---

## Example

```text
Checkout Started

↓

Coupon Valid

↓

Coupon Revoked

↓

Old Checkout Submitted
```

---

## Ask

```text
Does the Final Step Revalidate Current State?
```

---

# TOCTOU Concept

```text
Check

↓

State Changes

↓

Use
```

* This is a:

```text
Time-of-Check

to

Time-of-Use
```

* problem.

---

# Phase 25 — Identify Race-Condition Candidates

* Look for operations involving:

```text
Check Limit

↓

Modify Shared State
```

---

## High-Value Candidates

```text
Coupon Redemption

Credit Spending

Inventory Purchase

Reward Claim

Invitation Acceptance

Username Claim

Voting

Reservation

Withdrawal

Resource Creation Limits
```

---

# Race Candidate Formula

```text
Check

↓

Small Timing Window

↓

State Change
```

* If multiple requests enter the timing window simultaneously, the intended rule may fail.

---

# Phase 26 — Test Concurrency Safely

* Use controlled resources and low request counts.

---

## Workflow

```text
Establish Single-Request Baseline

↓

Confirm Expected Limit

↓

Prepare Small Concurrent Request Set

↓

Send Simultaneously

↓

Verify Persistent Final State
```

---

## Record

```text
Requests Sent

Successful Responses

Expected State Change

Actual State Change

Final Balance / Count / Ownership
```

---

# Important

```text
Multiple 200 Responses

≠

Confirmed Race Condition
```

* Verify the actual business state.

---

# Phase 27 — Test Idempotency

* Some operations should produce the same final state even when repeated.

---

## Candidates

```text
Payment Creation

Order Submission

Refund

Resource Creation

Webhook Processing

Subscription Change
```

---

## Ask

```text
Does the Request Have an Idempotency Key?

Is It Enforced?

What Scope Does It Use?

What Happens on Replay?
```

---

# Phase 28 — Test Duplicate Submission

* Users may:

```text
Double Click

Refresh

Retry

Replay

Send Concurrently
```

* Determine whether duplicate requests create:

```text
Duplicate Orders

Duplicate Charges

Duplicate Credits

Duplicate Resources

Duplicate Invitations
```

---

# Phase 29 — Test Stale Requests

* Capture a valid request.

* Change the underlying state.

* Replay the old request.

---

## Example

```text
User Has Permission

↓

Request Captured

↓

Permission Removed

↓

Old Request Replayed
```

* Or:

```text
Price = 100

↓

Checkout Request Captured

↓

Price Changes to 150

↓

Old Request Replayed
```

---

# Core Question

```text
Does the Server Trust Stale Client State?
```

---

# Phase 30 — Test Multi-Tab and Multi-Session Behavior

* Different browser contexts may preserve different states.

---

## Example

```text
Tab A

→

Checkout State 1
```

```text
Tab B

→

Changes Cart
```

```text
Tab A

→

Submits Old Checkout
```

---

## Test

```text
Multiple Tabs

Multiple Browsers

Multiple Sessions

Same Account

Different Controlled Accounts
```

---

# Phase 31 — Test Multi-Account Interactions

* Some business rules depend on relationships between accounts.

---

## Examples

```text
Invitation

Referral

Transfer

Sharing

Approval

Gift

Delegation
```

---

## Use Controlled Accounts

```text
User A

User B

User C
```

* Map:

```text
Who Initiates?

Who Receives?

Who Approves?

Who Benefits?

Who Owns the Result?
```

---

# Phase 32 — Test Alternative Workflows

* The same business action may exist through:

```text
Web UI

REST API

Mobile API

GraphQL

Legacy Endpoint

Administrative Interface
```

---

## Example

```text
UI

→

Enforces Coupon Limit
```

```text
API

→

Must Enforce Same Limit
```

---

# Phase 33 — Test Workflow Parameter Tampering

* Look for fields representing business state.

---

## Examples

```text
status

price

discount

approved

paid

role

plan

quantity

owner

currency

balance

limit
```

---

## Ask

```text
Is This Value Authoritative?

or

Should the Server Derive It?
```

---

# Phase 34 — Test Hidden Fields

* UI forms may contain hidden business values.

---

## Example

```text
<input type="hidden" name="price" value="100">
```

* Hidden means:

```text
Not Visible in UI
```

* It does not mean:

```text
Trusted
```

---

# Phase 35 — Test Validation Consistency

* The same rule may be enforced differently across:

```text
Create

Update

Import

Bulk Update

API

Mobile

Admin

Legacy
```

---

## Example

```text
Create User

→

Role Protected
```

* But:

```text
Update User

→

Role Field Accepted
```

* Business rules should remain consistent across equivalent operations.

---

# Phase 36 — Test Error Recovery Paths

* Unexpected paths may bypass normal rules.

---

## Candidates

```text
Retry

Resume

Recover

Cancel

Undo

Restore

Reopen

Resubmit
```

---

## Ask

```text
Does Recovery Revalidate the Original Business Rules?
```

---

# Phase 37 — Test Partial Failure

* Multi-step operations may partially succeed.

---

## Example

```text
Payment Deducted

↓

Order Creation Fails
```

* Or:

```text
Coupon Consumed

↓

Checkout Fails
```

---

## Ask

```text
Is State Rolled Back?

Can the User Retry?

Can Retry Duplicate the Effect?

Can Partial State Be Abused?
```

---

# Phase 38 — Build the Business Logic Test Matrix

| Rule              | Baseline       | Mutation     | Expected | Actual  | Status |
| ----------------- | -------------- | ------------ | -------- | ------- | ------ |
| Coupon once/user  | Redeem once    | Replay       | Reject   | Pending | Test   |
| Quantity > 0      | `1`            | `0`          | Reject   | Pending | Test   |
| Approval required | Submit→Approve | Skip approve | Reject   | Pending | Test   |
| Limit 5/day       | 5 actions      | 6th action   | Reject   | Pending | Test   |

---

# Phase 39 — Build the State Transition Matrix

| Current State | Requested Action | Expected        | Actual  |
| ------------- | ---------------- | --------------- | ------- |
| Draft         | Approve          | Deny            | Pending |
| Submitted     | Approve          | Allow           | Pending |
| Rejected      | Execute          | Deny            | Pending |
| Completed     | Modify           | Deny/Restricted | Pending |

---

# Phase 40 — Validate Candidate Findings

* Business logic findings require proof of the violated rule.

---

## Validation Workflow

```text
Define Business Rule

↓

Establish Normal Workflow

↓

Create Controlled Test State

↓

Perform Minimal Logic Mutation

↓

Verify Persistent Result

↓

Repeat if Safe

↓

Demonstrate Rule Violation

↓

Determine Impact
```

---

# Example — Coupon Reuse

## Weak Evidence

```text
Second Request Returned 200
```

## Strong Evidence

```text
Coupon Defined as One-Time

↓

User Redeems Once

↓

Discount Applied

↓

Same Controlled User Replays Redemption

↓

Discount Applied Again

↓

Final Order Confirms Repeated Benefit
```

---

# Example — Workflow Bypass

```text
Expected:

Draft

→

Submit

→

Approve

→

Execute
```

* Actual:

```text
Draft

→

Execute

↓

Final Action Completed
```

* This demonstrates a server-side state-transition failure.

---

# Phase 41 — Determine Business Impact

* Impact may include:

```text
Financial Loss

Unauthorized Discount

Duplicate Credit

Inventory Abuse

Limit Bypass

Privilege Gain

Unauthorized Approval

Fraud Opportunity

Resource Exhaustion

Cross-Tenant Effect

Workflow Integrity Failure
```

---

# Impact Formula

```text
Violated Rule

+

Attacker Benefit

+

Business Cost

+

Exploitability

=

Meaningful Impact
```

---

# Phase 42 — Identify Root Cause

* Common root causes include:

```text
Client-Side Rule Enforcement

Missing Server-Side State Validation

Trusted Client-Controlled Values

Missing Revalidation

Missing Atomicity

Missing Idempotency

Inconsistent Validation Across Endpoints

Improper One-Time Token Handling

Missing Limit Enforcement

Stale State Trust

Missing Separation of Duties
```

---

# Phase 43 — Collect Evidence

* Preserve:

```text
Business Rule

Normal Workflow

Baseline Request

Modified / Replayed Request

State Before

State After

Persistent Result

Attacker Benefit

Business Impact
```

---

## Evidence Structure

```text
F001/
│
├── 01-rule.md
├── 02-baseline-request.txt
├── 03-baseline-state.png
├── 04-test-request.txt
├── 05-test-response.txt
├── 06-final-state.png
└── notes.md
```

---

# Capstone Challenge

## Stage 1 — Business Rules

* Document at least:

```text
10 Business Rules
```

* Include:

  * Limits.
  * State requirements.
  * One-time actions.
  * Role requirements.
  * Pricing rules.
  * Ownership rules.

---

## Stage 2 — Workflow Mapping

* Map at least three important workflows.

* For each:

```text
Start State

↓

Intermediate States

↓

Final State
```

---

## Stage 3 — Sequence Testing

* Test:

```text
Step Skipping

Out-of-Order Steps

Replay

Duplicate Submission

Stale Requests
```

---

## Stage 4 — Numeric Testing

* Test appropriate controlled values:

```text
Zero

Negative

Boundary

Large

Decimal
```

---

## Stage 5 — Limits

* Identify at least three application limits.

* Test whether they are consistently enforced.

---

## Stage 6 — Multi-Account Logic

* Use controlled accounts to test:

```text
Invitations

Referrals

Sharing

Transfers

Approvals
```

* Where supported.

---

## Stage 7 — Concurrency

* Identify at least three potential race-condition candidates.

* Test only where safe and authorized.

---

## Stage 8 — Validation

* Classify every candidate:

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

Which Assets Have Value?

Which Workflows Exist?

Which States Exist?

Which Transitions Are Allowed?

Which Actions Are One-Time?

Which Values Should the Server Calculate?

Which Limits Exist?

Which Steps Require Revalidation?

Which Operations Need Atomicity?

Which Operations Need Idempotency?

Can Stale Requests Be Reused?

Can Concurrent Requests Break Rules?

Are Rules Consistent Across Interfaces?
```

---

# Business Logic Testing Workflow

```text
Understand Business

↓

Identify Valuable Assets

↓

Document Business Rules

↓

Map Workflows

↓

Map States

↓

Identify Assumptions

↓

Capture Baselines

↓

Test Sequence Changes

↓

Test Replay

↓

Test Numeric Boundaries

↓

Test Pricing and Limits

↓

Test Multi-Account Logic

↓

Test Stale State

↓

Test Concurrency

↓

Test Alternate Interfaces

↓

Verify Persistent Result

↓

Validate Rule Violation

↓

Determine Business Impact

↓

Report
```

---

# Common Mistakes

* Testing features without understanding their intended business rules.
* Assuming valid HTTP requests cannot create vulnerabilities.
* Focusing only on technical vulnerability classes.
* Following only the intended UI sequence.
* Failing to map workflow states.
* Failing to identify one-time operations.
* Testing only positive numeric values.
* Ignoring zero and negative values.
* Ignoring decimal and precision behavior.
* Trusting hidden fields.
* Trusting client-calculated prices.
* Ignoring coupon stacking.
* Ignoring state changes after discount application.
* Ignoring limits and quotas.
* Testing limits only through the UI.
* Ignoring alternate APIs.
* Ignoring stale requests.
* Ignoring multi-tab behavior.
* Ignoring duplicate submissions.
* Ignoring idempotency.
* Ignoring partial failures.
* Ignoring recovery workflows.
* Ignoring race-condition candidates.
* Treating multiple 200 responses as proof of a race condition.
* Failing to verify persistent state.
* Using uncontrolled real transactions.
* Creating financial impact unnecessarily.
* Testing against real-user resources.
* Failing to document the exact violated business rule.
* Reporting unusual behavior without demonstrating attacker benefit.
* Overstating impact without understanding business consequences.

---

# Professional Tips

* Learn the business before attempting to break the business.
* Write important rules explicitly.
* Model workflows as state machines.
* Identify where the application assumes users follow the UI.
* Capture complete normal workflows before mutation.
* Test later steps directly.
* Replay operations intended to happen once.
* Verify persistent business state after every important test.
* Treat price, discount, quantity, role, status, and balance fields as high-value inputs.
* Determine which values the server should calculate independently.
* Test boundaries around every important limit.
* Compare equivalent rules across UI and API interfaces.
* Use multiple controlled accounts for relational workflows.
* Test invitation and approval lifecycle states.
* Check whether revoked or expired objects remain usable.
* Look for check-then-update operations as race candidates.
* Keep concurrency tests small and controlled.
* Test idempotency for financially or operationally sensitive actions.
* Revalidate state at final workflow steps.
* Check stale requests after underlying state changes.
* Test retries, resumes, cancellations, and recovery paths.
* Analyze partial failures for inconsistent state.
* Distinguish technical success from actual business effect.
* Describe findings in terms of violated rules and measurable impact.

---

# Practice Tasks

1. Select an authorized application with multi-step workflows.

2. Identify its primary business purpose.

3. List valuable business assets.

4. Document at least ten business rules.

5. Identify all one-time actions.

6. Identify all limits and quotas.

7. Identify all client-controlled monetary or numeric values.

8. Map three important workflows.

9. Create state diagrams for each workflow.

10. Record allowed state transitions.

11. Capture complete baseline requests.

12. Test step skipping.

13. Test out-of-order execution.

14. Replay one-time operations.

15. Verify persistent state after replay.

16. Test zero values.

17. Test negative values.

18. Test minimum boundaries.

19. Test maximum boundaries.

20. Test decimal and precision behavior where relevant.

21. Identify price-related parameters.

22. Verify server-side price calculation.

23. Map coupon rules.

24. Test coupon reuse using controlled data.

25. Test coupon stacking where permitted.

26. Modify cart state after applying a discount.

27. Verify checkout recalculation.

28. Identify three application limits.

29. Test each limit at its boundary.

30. Compare limit enforcement across interfaces.

31. Map invitation workflows.

32. Test invitation reuse with controlled accounts.

33. Map referral logic where available.

34. Map approval workflows.

35. Test invalid state transitions.

36. Test separation of duties.

37. Review cancellation and refund logic with controlled transactions.

38. Capture a valid request and then change underlying state.

39. Replay the stale request.

40. Test multi-tab behavior.

41. Test multiple sessions.

42. Identify three race-condition candidates.

43. Establish single-request baselines.

44. Perform safe concurrency testing where authorized.

45. Verify final persistent state.

46. Identify idempotency-sensitive endpoints.

47. Test duplicate submissions.

48. Compare equivalent web and API workflows.

49. Test recovery and retry paths.

50. Analyze partial-failure behavior.

51. Build the business-logic test matrix.

52. Build the state-transition matrix.

53. Validate every suspicious result.

54. Determine attacker benefit.

55. Determine business impact.

56. Identify root cause.

57. Collect minimal sufficient evidence.

58. Restore controlled test state where necessary.

59. Write the final business-logic summary.

---

# Interview Questions

1. What is a business logic vulnerability?
2. How is business logic testing different from injection testing?
3. Why can syntactically valid requests still be malicious?
4. How do you understand an application's business logic?
5. What is a business asset?
6. Why should business rules be documented before testing?
7. How do you map a workflow?
8. What is a state machine?
9. What is a state-transition vulnerability?
10. How do you test step skipping?
11. How do you test out-of-order workflow execution?
12. What types of operations should be tested for replay?
13. Why must persistent state be verified after replay?
14. How do you test one-time operations?
15. What defines "once" in a business rule?
16. Which numeric boundaries should you test?
17. Why are negative values important?
18. Why should the server calculate authoritative prices?
19. How do you test price manipulation?
20. How do you test coupon logic?
21. What happens if cart state changes after a coupon is applied?
22. How do you test application limits?
23. Why should limits be tested across multiple interfaces?
24. How do you test free-trial eligibility safely?
25. What should be tested in invitation workflows?
26. What business logic issues can affect referral systems?
27. How do you test approval workflows?
28. What is separation of duties?
29. How do you test cancellation and refund logic?
30. What is TOCTOU?
31. How do you identify race-condition candidates?
32. How do you validate a race condition?
33. Why do multiple successful HTTP responses not prove a race condition?
34. What is idempotency?
35. Which operations commonly require idempotency?
36. What is a stale-request vulnerability?
37. How can multiple tabs expose logic flaws?
38. Why are multiple controlled accounts useful?
39. How do hidden fields relate to business logic testing?
40. Why should validation consistency be compared across endpoints?
41. What logic flaws can occur in retry or recovery workflows?
42. What is a partial-failure state?
43. How do you validate a business logic vulnerability?
44. What evidence should be collected?
45. How do you calculate business impact?
46. What are common root causes of business logic flaws?
47. How do you test financially sensitive workflows safely?
48. How do you prioritize business logic tests?
49. Why are business logic flaws difficult for automated scanners?
50. Walk me through your complete business logic testing methodology.

---

# Quick Revision

* Core concept:

```text
Valid Functionality

+

Invalid Business Use

=

Business Logic Vulnerability
```

* Start with:

```text
Business Assets

↓

Business Rules

↓

Workflows

↓

States

↓

Assumptions
```

* Sequence testing:

```text
Skip

Reorder

Replay

Repeat
```

* Numeric testing:

```text
Zero

Negative

Boundary

Large

Decimal
```

* State testing:

```text
Current State

+

Requested Action

↓

Is Transition Allowed?
```

* Race condition:

```text
Check

↓

Timing Window

↓

Update
```

* Validation:

```text
Defined Rule

↓

Controlled Violation

↓

Persistent Result

↓

Attacker Benefit

↓

Business Impact
```

---

# Business Logic Quick Checklist

```text
BUSINESS

[ ] Purpose understood
[ ] Valuable assets identified
[ ] Business rules documented
[ ] Roles identified
[ ] Limits identified


WORKFLOWS

[ ] Steps mapped
[ ] States mapped
[ ] Allowed transitions
[ ] Required approvals
[ ] One-time actions


SEQUENCE

[ ] Step skipping
[ ] Out-of-order
[ ] Replay
[ ] Duplicate submission
[ ] Stale request


NUMERIC

[ ] Zero
[ ] Negative
[ ] Minimum boundary
[ ] Maximum boundary
[ ] Large values
[ ] Decimal / precision


VALUE

[ ] Price
[ ] Quantity
[ ] Discount
[ ] Credit
[ ] Balance
[ ] Currency
[ ] Server recalculation


LIMITS

[ ] Per user
[ ] Per account
[ ] Per tenant
[ ] Per order
[ ] Per day
[ ] Alternate interfaces


WORKFLOWS

[ ] Coupons
[ ] Trials
[ ] Invitations
[ ] Referrals
[ ] Approvals
[ ] Refunds
[ ] Cancellations


STATE

[ ] Current-state validation
[ ] Final-step revalidation
[ ] Stale requests
[ ] Multi-tab
[ ] Multi-session
[ ] Partial failures


CONCURRENCY

[ ] Race candidates
[ ] Single-request baseline
[ ] Small concurrent test
[ ] Final state verified
[ ] Idempotency


VALIDATION

[ ] Rule defined
[ ] Baseline captured
[ ] Controlled mutation
[ ] Persistent result
[ ] Attacker benefit
[ ] Business impact
[ ] Root cause
```

---

# Key Takeaways

* Business logic vulnerabilities arise when legitimate application functionality can be used to violate intended business rules.
* They often require no malformed syntax or traditional exploit payload.
* Effective testing begins with understanding the application's purpose, valuable assets, roles, rules, workflows, and assumptions.
* Important business rules should be explicitly documented before attempting to violate them.
* Multi-step workflows should be modeled as state machines with defined allowed transitions.
* Applications should not assume users will follow the UI, execute steps in order, send requests only once, or preserve expected client-side state.
* Step skipping and out-of-order execution can reveal missing server-side workflow enforcement.
* One-time actions should be tested for replay and duplicate effects.
* Persistent state must be verified because successful HTTP responses alone do not prove business impact.
* Numeric testing should include zero, negative, minimum, maximum, large, decimal, and precision boundaries where relevant.
* Authoritative values such as price, discounts, balances, and permissions should be derived or validated by the server.
* Coupon, promotion, trial, invitation, referral, approval, cancellation, and refund workflows often contain complex state-dependent rules.
* Limits and quotas should be tested at their boundaries and across alternate interfaces.
* Separation-of-duties requirements must be enforced server-side.
* Final workflow steps should revalidate current security-sensitive state rather than trusting stale earlier checks.
* Race-condition candidates commonly involve a check followed by modification of shared state.
* Concurrency testing requires persistent-state verification; multiple successful responses alone are insufficient.
* Idempotency prevents duplicate effects when sensitive operations are retried or replayed.
* Stale requests can expose failures to revalidate prices, permissions, eligibility, or workflow state.
* Multi-tab, multi-session, and multi-account testing can reveal state inconsistencies hidden during linear testing.
* Equivalent business rules should be enforced consistently across web, API, mobile, GraphQL, legacy, and administrative interfaces.
* Retry, resume, undo, cancellation, recovery, and partial-failure paths deserve independent testing.
* Strong findings explain the exact violated business rule, attacker benefit, resulting business cost, exploitability, and root cause.
* Automated scanners are generally weak at finding business logic vulnerabilities because the tester must understand what the application is supposed to permit.
* The complete methodology is **understand the business → identify valuable assets → document rules → map workflows and states → identify assumptions → capture baselines → manipulate sequence and state → test replay and boundaries → test value calculations and limits → test multi-account workflows → test stale state and concurrency → compare alternate interfaces → verify persistent effects → validate rule violation → determine business impact → report**.
