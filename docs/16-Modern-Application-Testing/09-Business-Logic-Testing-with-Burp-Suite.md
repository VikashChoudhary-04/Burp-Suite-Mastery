# Business Logic Testing with Burp Suite

> **Module 16 — Modern Application Testing**

> **Progress**

```text
██████████░░░░░ 10 / 15 Files
```

---

## Overview

* Business logic vulnerabilities occur when an application correctly processes requests technically but allows users to perform actions that violate intended business rules.

* Unlike many traditional vulnerabilities, business logic flaws often cannot be detected reliably by automated scanners.

* They require understanding:

  ```text
  What the Application Is Designed to Do

  ↓

  What Rules Should Restrict That Behavior

  ↓

  What the Server Actually Enforces

  ↓

  Whether Those Rules Can Be Broken
  ```

* Common examples include:

  * Skipping required workflow steps.
  * Reusing one-time actions.
  * Manipulating prices or quantities.
  * Applying discounts incorrectly.
  * Bypassing transaction limits.
  * Performing actions in an invalid order.
  * Repeating sensitive operations.
  * Exploiting inconsistent validation between endpoints.
  * Manipulating application state.
  * Violating assumptions about roles, objects, or relationships.

* Burp Suite is particularly useful for business logic testing because it allows you to capture legitimate workflows, replay requests, modify individual values, reorder operations, and compare application state.

---

## Learning Objectives

* By the end of this lesson, you will be able to:

  * Understand what business logic vulnerabilities are.
  * Distinguish logic flaws from technical vulnerabilities.
  * Identify business rules and application invariants.
  * Map multi-step workflows.
  * Build workflow and state diagrams.
  * Establish legitimate baselines.
  * Test step skipping and step reordering.
  * Test repeated and duplicate actions.
  * Analyze client-controlled business values.
  * Test quantity, price, discount, credit, and limit logic safely.
  * Identify inconsistent validation across endpoints.
  * Test state transitions and stale requests.
  * Analyze one-time operations and replay behavior.
  * Understand race-condition relevance to business logic.
  * Test role and identity transitions.
  * Build abuse-case hypotheses.
  * Verify backend state rather than relying only on responses.
  * Avoid false positives caused by intended business behavior.
  * Build a repeatable business logic testing methodology.

---

## What Is Business Logic?

* Business logic defines the rules that determine how an application should behave.

* Examples:

  ```text
  A Coupon Can Be Used Once

  A User Cannot Approve Their Own Request

  A Refund Cannot Exceed the Original Payment

  An Order Must Be Paid Before Shipment

  A Free Trial Can Be Claimed Once

  A Withdrawal Cannot Exceed Available Balance
  ```

* These are application-specific rules.

---

## Business Logic Vulnerability

* A business logic vulnerability occurs when an application allows a user to violate an intended rule in a security-relevant way.

* Example:

  ```text
  Intended:

  Coupon Can Be Used Once

  Actual:

  Same Coupon Can Be Applied Repeatedly

  Result:

  Unauthorized Discount
  ```

---

## Technical Vulnerability vs Logic Vulnerability

### Technical Vulnerability

* Often involves implementation weaknesses such as:

  ```text
  SQL Injection

  XSS

  SSRF

  Path Traversal
  ```

### Business Logic Vulnerability

* Often involves valid functionality used in an unintended sequence or context.

  ```text
  Valid Requests

  +

  Unexpected Sequence / Values / State

  →

  Security Impact
  ```

---

## Why Logic Flaws Are Difficult to Automate

* Automated scanners understand patterns.

* They usually do not fully understand:

  ```text
  Business Intent

  Financial Rules

  Workflow Requirements

  User Relationships

  One-Time Restrictions

  Application-Specific Invariants
  ```

* Human reasoning is therefore essential.

---

## Think in Rules

* For every important feature, ask:

  ```text
  What Must Always Be True?
  ```

* Example:

  ```text
  Refund Amount

  ≤

  Original Payment
  ```

* This is an invariant.

---

## Business Invariants

* An invariant is a rule that should remain true regardless of how requests are sent.

* Examples:

  ```text
  Balance Must Not Become Invalid

  Discount Must Not Exceed Allowed Maximum

  User Must Not Approve Own Request

  Order Must Belong to Current User

  One-Time Token Must Not Be Reusable

  Completed Transaction Must Not Execute Twice
  ```

---

## Invariant Template

```text
For:

[Feature / Object]

The Following Must Always Be True:

[Business Rule]

Regardless Of:

[Request Order / Client Input / Repetition / Interface]
```

---

## Example

```text
For:

Password Reset

The Following Must Always Be True:

A Reset Token Can Be Used Only According to Its Intended Lifetime and Usage Rules

Regardless Of:

Request Replay or Browser Navigation
```

---

## Start With Workflow Mapping

* Business logic testing begins by understanding legitimate application behavior.

* Record:

  ```text
  Entry Point

  Steps

  Requests

  Parameters

  State Changes

  Decisions

  Final Outcome
  ```

---

## Example Workflow

```text
Add Product

↓

View Cart

↓

Apply Coupon

↓

Enter Shipping Details

↓

Choose Payment

↓

Confirm Order

↓

Order Created
```

* Every transition creates testing opportunities.

---

## Workflow Questions

* Ask:

  ```text
  Can a Step Be Skipped?

  Can a Step Be Repeated?

  Can Steps Be Reordered?

  Can Old Requests Be Replayed?

  Can Values Change Between Steps?

  Is State Revalidated?

  Does Another Endpoint Enforce the Same Rules?
  ```

---

## Workflow Card

```text
Workflow:

Starting State:

User:

Role:

Tenant:

Step 1:

Request:

State After Step 1:

Step 2:

Request:

State After Step 2:

Step 3:

Request:

Final State:

Important Business Rules:

Client-Controlled Values:

Server-Controlled Values:

One-Time Actions:

Sensitive Transitions:
```

---

## State Machines

* Many workflows can be modeled as states.

* Example:

  ```text
  Draft

  ↓

  Submitted

  ↓

  Reviewed

  ↓

  Approved

  ↓

  Completed
  ```

* Expected transitions may be strictly controlled.

---

## Invalid State Transition

* Example:

  ```text
  Draft

  ─────────────►

  Approved
  ```

* Security question:

  ```text
  Can Required Intermediate Validation Be Skipped?
  ```

---

## State Transition Matrix

| Current State | Action   | Expected Next State |
| ------------- | -------- | ------------------- |
| Draft         | Submit   | Submitted           |
| Submitted     | Review   | Reviewed            |
| Reviewed      | Approve  | Approved            |
| Approved      | Complete | Completed           |

* Then test invalid transitions separately.

---

## Burp Workflow

```text
Proxy

↓

Capture Legitimate Workflow

↓

HTTP History

↓

Identify Important Requests

↓

Send to Repeater

↓

Record Baseline State

↓

Modify Sequence / Value / Timing

↓

Send Controlled Request

↓

Observe Response

↓

Verify Backend State

↓

Compare With Business Rule
```

---

## Baseline First

* Before testing a workflow, complete it normally.

* Record:

  ```text
  User

  Role

  Starting State

  Requests

  Parameters

  Responses

  Final State

  Expected Outcome
  ```

---

## Why Baselines Matter

* Without a baseline, you cannot reliably determine whether modified behavior is abnormal.

* Use:

  ```text
  Legitimate Workflow

  ↓

  Record Expected Result

  ↓

  Change One Condition

  ↓

  Compare
  ```

---

## Business Logic Test Dimensions

* For each important workflow, consider:

  ```text
  Sequence

  Repetition

  Values

  Identity

  Role

  State

  Time

  Quantity

  Limits

  Relationships

  Concurrency

  Alternate Interfaces
  ```

---

## Step Skipping

* Suppose the intended workflow is:

  ```text
  Step 1

  ↓

  Step 2

  ↓

  Step 3

  ↓

  Step 4
  ```

* Test whether:

  ```text
  Step 1

  ↓

  Step 4
  ```

* Is accepted.

---

## Example — Approval Workflow

```text
Create Request

↓

Manager Review

↓

Approval

↓

Execution
```

* Security question:

  ```text
  Can the Execution Request Be Sent Before Approval?
  ```

---

## Testing Step Skipping

```text
Complete Legitimate Workflow

↓

Capture Final-Step Request

↓

Create New Controlled Object

↓

Do Not Complete Required Intermediate Step

↓

Replay Final-Step Request With New Object

↓

Verify State
```

* Use disposable test objects.

---

## Step Reordering

* Expected:

  ```text
  A → B → C
  ```

* Controlled tests:

  ```text
  A → C → B
  ```

  or

  ```text
  B → A → C
  ```

* Determine whether server-side state validation prevents invalid order.

---

## Repeated Steps

* Some steps should occur only once.

* Example:

  ```text
  Redeem Coupon

  Confirm Payment

  Claim Reward

  Accept Invitation

  Use Recovery Code
  ```

* Security question:

  ```text
  Can the Same Action Be Repeated?
  ```

---

## Replay Testing

* Capture a successful request.

* Determine whether replaying it causes:

  ```text
  Duplicate Credit

  Duplicate Refund

  Duplicate Reward

  Duplicate Order

  Repeated State Change
  ```

* Only use controlled, low-impact actions.

---

## Important Principle

```text
Valid Once

≠

Valid Forever
```

* A request that was legitimate when originally sent may become invalid after state changes.

---

## Stale Request Testing

* A stale request is a previously valid request replayed after relevant state has changed.

* Example:

  ```text
  Capture Update Request

  ↓

  Object State Changes

  ↓

  Replay Old Request
  ```

* Security question:

  ```text
  Does the Server Revalidate Current State?
  ```

---

## One-Time Tokens

* One-time mechanisms may include:

  ```text
  Password Reset Tokens

  Invitation Tokens

  Verification Links

  Coupon Codes

  Recovery Codes

  Transaction Tokens
  ```

* Test whether intended one-time semantics are enforced.

---

## Token Consumption

* Expected model:

  ```text
  Token Valid

  ↓

  Token Used Successfully

  ↓

  Token Invalid
  ```

* Verify actual behavior.

---

## Client-Controlled Business Values

* Important values may include:

  ```text
  price

  quantity

  discount

  shipping_cost

  tax

  credit

  balance

  currency

  plan

  tier

  status
  ```

* Determine which values the server trusts.

---

## Important Principle

```text
Displayed by Client

≠

Trusted by Server
```

* Sensitive business calculations should generally be validated or derived server-side.

---

## Price Manipulation

* Example request:

  ```json
  {
    "product_id": 501,
    "quantity": 1,
    "price": 999
  }
  ```

* Security question:

  ```text
  Does the Server Trust the Client-Supplied Price?
  ```

* Use test environments or disposable low-value transactions.

---

## Safer Price Testing

* Instead of completing a real financial transaction:

  ```text
  Modify Controlled Cart

  ↓

  Observe Server Calculation

  ↓

  Stop Before External Payment or Fulfillment
  ```

* Unless explicit authorization permits full transaction testing.

---

## Quantity Manipulation

* Test meaningful boundaries such as:

  ```text
  0

  1

  Maximum Allowed

  Maximum + 1
  ```

* Negative or extreme values should be tested only where safe and relevant.

---

## Negative Values

* A negative quantity or amount may cause unexpected calculations.

* Example:

  ```text
  Quantity = -1
  ```

* Potential consequences depend on application logic.

* Never assume a vulnerability without verifying actual state and impact.

---

## Zero Values

* Test whether:

  ```text
  Quantity = 0

  Price = 0

  Transfer = 0
  ```

* Is handled according to intended business rules.

---

## Boundary Testing

* If a rule states:

  ```text
  Maximum Transfer = 10,000
  ```

* Relevant controlled tests include:

  ```text
  9,999

  10,000

  10,001
  ```

* This is more informative than arbitrary extreme values.

---

## Minimum Limits

* If:

  ```text
  Minimum Purchase = 100
  ```

* Test:

  ```text
  99

  100

  101
  ```

---

## Discount Logic

* Discounts may involve:

  ```text
  Coupon Codes

  Referral Credits

  Promotional Offers

  Loyalty Rewards

  Subscription Benefits
  ```

---

## Discount Questions

```text
Can Discounts Stack?

Can the Same Coupon Be Reused?

Can a Coupon Be Applied After Expiration?

Can a Coupon Be Used by an Ineligible Account?

Can a Discount Exceed the Purchase Value?

Can Removing an Item Preserve an Invalid Discount?
```

---

## Coupon Workflow

```text
Cart Total

↓

Apply Coupon

↓

Eligibility Check

↓

Discount Applied

↓

Order Confirmed
```

* Determine whether eligibility is revalidated at final confirmation.

---

## Stale Discount State

* Example:

  ```text
  Add Eligible Product

  ↓

  Apply Coupon

  ↓

  Remove Eligible Product

  ↓

  Checkout
  ```

* Security question:

  ```text
  Is the Discount Revalidated After Cart Changes?
  ```

---

## Limit Enforcement

* Applications may enforce:

  ```text
  Daily Limits

  Monthly Limits

  Per-Transaction Limits

  Per-User Limits

  Per-Device Limits

  Per-Promotion Limits
  ```

* Determine where enforcement occurs.

---

## Limit Splitting

* Example:

  ```text
  Maximum Single Transaction = 1,000
  ```

* Two transactions of:

  ```text
  600 + 600
  ```

* May be legitimate if only a per-transaction limit exists.

* Do not assume the intended rule.

---

## Important Principle

```text
Observed Limit

≠

Assumed Business Rule
```

* Confirm intended semantics before reporting a bypass.

---

## Cumulative Limits

* If the intended rule is:

  ```text
  Maximum Daily Withdrawal = 1,000
  ```

* The server should enforce cumulative state across transactions.

---

## Identity-Based Limits

* Limits may apply per:

  ```text
  Account

  User

  Organization

  Device

  Payment Method

  Promotion
  ```

* Test only within authorized controlled identities.

---

## Duplicate Operations

* Sensitive operations should handle duplicate submissions appropriately.

* Examples:

  ```text
  Payment

  Refund

  Withdrawal

  Order Creation

  Reward Claim

  Invitation Acceptance
  ```

---

## Double Submission

* A user may:

  ```text
  Double-Click

  Refresh

  Retry

  Replay Request
  ```

* The backend should handle duplicate-sensitive operations according to business requirements.

---

## Idempotency

* An idempotent operation produces the same intended state when repeated.

* Example concept:

  ```text
  Set Profile Name = Alice
  ```

* Repeating the same update generally does not create multiple effects.

---

## Non-Idempotent Operations

* Examples:

  ```text
  Create Order

  Send Payment

  Issue Refund

  Claim Reward
  ```

* Repetition may create additional effects.

---

## Idempotency Keys

* Some APIs use:

  ```text
  Idempotency-Key
  ```

* To prevent accidental duplicate processing.

* Security questions:

  ```text
  Is the Key Enforced?

  What Is Its Scope?

  Can the Same Operation Execute Twice?

  How Long Is the Key Remembered?
  ```

---

## Reusing Idempotency Keys

* Test only with harmless controlled operations.

* Determine whether identical or modified requests with the same key behave according to intended semantics.

---

## Alternate Endpoint Validation

* The same business action may be available through:

  ```text
  Web UI

  REST API

  GraphQL

  Mobile API

  Legacy API
  ```

* Security rules should remain consistent.

---

## Example

```text
Web UI

↓

Maximum Quantity = 10
```

* But direct API request:

  ```text
  quantity = 11
  ```

* Security question:

  ```text
  Does the Backend Independently Enforce the Limit?
  ```

---

## Client-Side Validation

* Frontend controls may enforce:

  ```text
  Minimum Values

  Maximum Values

  Required Steps

  Disabled Buttons

  Date Restrictions
  ```

* These controls improve UX but should not be the sole enforcement mechanism for security-sensitive rules.

---

## Important Principle

```text
UI Restriction

≠

Server-Side Business Rule Enforcement
```

---

## Hidden Fields

* Requests may contain:

  ```text
  status

  role

  discount

  plan

  approved

  verified

  owner_id
  ```

* Determine whether such fields are genuinely client-controlled or should be derived server-side.

---

## Server-Calculated Values

* Sensitive calculations may include:

  ```text
  Total Price

  Tax

  Discount

  Account Balance

  Reward Points

  Shipping Fee

  Commission
  ```

* Compare values before and after controlled modifications.

---

## Currency Logic

* Multi-currency systems may introduce:

  ```text
  Conversion

  Rounding

  Currency Selection

  Minimum Amounts

  Exchange Rates
  ```

* Financial testing should be performed only within explicit authorization and controlled environments.

---

## Rounding Logic

* Small rounding differences are not automatically vulnerabilities.

* Security relevance requires meaningful abuse such as repeatable unauthorized gain or loss.

---

## Integer and Decimal Boundaries

* Applications may process:

  ```text
  1

  1.0

  1.00

  0.01
  ```

* Differently across components.

* Test only values meaningful to the business rule.

---

## Workflow Parameter Tampering

* A later request may include data originally selected earlier.

* Example:

  ```json
  {
    "plan": "premium",
    "price": 100
  }
  ```

* Security question:

  ```text
  Does the Server Revalidate That the Price Matches the Selected Plan?
  ```

---

## Cross-Step Parameter Consistency

* Values may need to remain consistent across steps.

* Example:

  ```text
  Step 1:

  Select Product A

  Step 2:

  Calculate Price for Product A

  Step 3:

  Change Product ID to Product B

  Step 4:

  Submit Old Price
  ```

* The server should validate current authoritative state.

---

## Time-Based Rules

* Business rules may depend on:

  ```text
  Expiration

  Trial Period

  Booking Window

  Cancellation Deadline

  Subscription Renewal

  Promotion Period
  ```

---

## Expiration Testing

* Security question:

  ```text
  Does the Server Enforce Time Rules
  or Does the Client Merely Hide the Action?
  ```

---

## Date Parameters

* If the client sends:

  ```text
  start_date

  end_date

  booking_date

  expiration
  ```

* Determine whether the server independently validates allowed ranges.

---

## Role Transitions

* Business workflows may depend on changing roles.

* Example:

  ```text
  User Creates Request

  ↓

  Manager Approves
  ```

* Security question:

  ```text
  Can the Same User Act as Both Requester and Approver?
  ```

---

## Separation of Duties

* Sensitive workflows may require different actors.

* Examples:

  ```text
  Requester ≠ Approver

  Creator ≠ Reviewer

  Initiator ≠ Final Authorizer
  ```

* This is known as separation of duties.

---

## Self-Approval

* Example:

  ```text
  User Creates Expense

  ↓

  User Changes Role / Context

  ↓

  Same User Approves Expense
  ```

* Whether this is prohibited depends on intended policy.

---

## Identity Switching

* With controlled accounts, test whether workflow state remains securely bound to the correct identity.

* Example:

  ```text
  User A Starts Workflow

  ↓

  User B Attempts to Complete It
  ```

* This may overlap with authorization testing.

---

## Ownership Changes

* Some objects can change owners.

* Security questions:

  ```text
  Does Old Owner Retain Access?

  Does New Owner Receive Correct Permissions?

  Are Pending Actions Revalidated?
  ```

---

## State Changes Between Requests

* A workflow may assume state remains unchanged between steps.

* Example:

  ```text
  Step 1 Checks Balance

  ↓

  State Changes

  ↓

  Step 2 Uses Old Assumption
  ```

* This can create logic flaws.

---

## TOCTOU

* TOCTOU stands for:

  ```text
  Time Of Check To Time Of Use
  ```

* Conceptually:

  ```text
  Check Condition

  ↓

  Delay / Concurrent Change

  ↓

  Perform Action Using Stale Assumption
  ```

---

## Race Conditions

* Race conditions can occur when concurrent requests interact with shared state before updates are safely committed.

* Examples may include:

  ```text
  Redeem Reward Twice

  Spend Same Balance Twice

  Claim One-Time Offer Multiple Times

  Exceed Quantity Limit
  ```

---

## Important Safety Rule

* Concurrency testing can create duplicate transactions or operational impact.

* Use:

  ```text
  Controlled Lab

  Disposable Data

  Explicit Authorization

  Small Request Counts
  ```

---

## Race Testing Concept

```text
Initial State

↓

Prepare Equivalent Requests

↓

Send Near-Simultaneously

↓

Observe Results

↓

Verify Final State

↓

Compare With Invariant
```

* Detailed race-condition techniques can be treated as a dedicated advanced topic.

---

## Error Paths

* Business logic may behave differently when operations fail.

* Test controlled scenarios such as:

  ```text
  Payment Failure

  Validation Failure

  Partial Completion

  Retry

  Cancellation
  ```

* Determine whether state remains consistent.

---

## Partial Failure

* Example:

  ```text
  Payment Fails

  But

  Order Marked Paid
  ```

* Or:

  ```text
  Refund Fails

  But

  Balance Updated
  ```

* Verify actual state across all affected components where permitted.

---

## Retry Logic

* Applications may automatically retry failed operations.

* Security question:

  ```text
  Can Manual Replay Plus Automatic Retry
  Cause Duplicate Effects?
  ```

---

## Cancellation Logic

* Test whether cancellation correctly affects:

  ```text
  Payment

  Inventory

  Credits

  Reservations

  Permissions

  Workflow State
  ```

---

## Reversal Logic

* Sensitive workflows may include:

  ```text
  Purchase → Refund

  Transfer → Reversal

  Booking → Cancellation
  ```

* Security rules should remain consistent across forward and reverse operations.

---

## Refund Logic

* Relevant invariants may include:

  ```text
  Total Refund ≤ Original Payment

  Refund Applies to Correct Transaction

  Refund Cannot Be Repeated Beyond Allowed Amount

  Unauthorized User Cannot Trigger Refund
  ```

---

## Credit and Reward Systems

* Test rules around:

  ```text
  Referral Credits

  Loyalty Points

  Promotional Balance

  Gift Credits

  Reward Claims
  ```

* Use controlled accounts and non-production value where possible.

---

## Referral Logic

* Security questions may include:

  ```text
  Can a User Refer Themselves?

  Can Multiple Accounts Abuse the Same Relationship?

  Is Reward Granted Before Eligibility Is Final?

  Can Referral State Be Replayed?
  ```

* Actual policy must be understood before concluding abuse.

---

## Free Trials

* Trial restrictions may apply to:

  ```text
  Account

  User

  Organization

  Payment Method

  Device
  ```

* Avoid attempting real-world identity evasion or fraudulent account creation.

* Test only permitted controlled scenarios.

---

## Inventory Logic

* E-commerce and reservation systems may enforce:

  ```text
  Available Quantity

  Reservation State

  Purchase Limits

  Stock Locks
  ```

* Security testing should not disrupt real inventory or deny access to legitimate users.

---

## Booking Logic

* Common rules include:

  ```text
  Cannot Book Past Dates

  Cannot Double-Book Resource

  Capacity Must Not Be Exceeded

  Cancellation Deadline Must Be Enforced
  ```

---

## Subscription Logic

* Subscription workflows may include:

  ```text
  Trial

  Upgrade

  Downgrade

  Renewal

  Cancellation

  Grace Period
  ```

* Test transitions rather than isolated requests.

---

## Upgrade / Downgrade Logic

* Security questions:

  ```text
  Are Entitlements Updated Immediately?

  Can Old Premium Access Persist?

  Is Billing State Consistent?

  Can Transitions Be Replayed?
  ```

---

## Feature Entitlements

* Applications may enable features based on:

  ```text
  Plan

  Role

  License

  Subscription

  Organization
  ```

* Backend enforcement should match intended entitlement rules.

---

## Abuse Cases

* A business logic test should begin with abuse hypotheses.

* Instead of asking only:

  ```text
  Is This Endpoint Vulnerable?
  ```

* Ask:

  ```text
  How Could a User Gain More Than Intended?

  How Could a User Avoid a Required Step?

  How Could a One-Time Action Be Repeated?

  How Could State Become Inconsistent?

  How Could Limits Be Bypassed?
  ```

---

## Abuse-Case Template

```text
Feature:

Normal User Goal:

Business Rule:

Attacker / Abuser Goal:

Assumption Being Tested:

Controlled Manipulation:

Expected Secure Behavior:

Observed Behavior:

State Impact:

Security / Business Impact:
```

---

## Negative Requirements

* Requirements often describe what users can do.

* Security testing also needs rules describing what must never happen.

* Examples:

  ```text
  A User Must Never Spend More Credit Than Available

  A Coupon Must Never Produce a Negative Total

  An Unapproved Request Must Never Execute

  A One-Time Reward Must Never Be Granted Twice
  ```

---

## Build Negative Requirements

* For every sensitive feature:

  ```text
  Normal Requirement

  ↓

  Convert to Abuse Question

  ↓

  Define Invariant

  ↓

  Test Controlled Violations
  ```

---

## Cross-Feature Logic

* Features may be secure individually but unsafe when combined.

* Example:

  ```text
  Coupon System

  +

  Refund System

  →

  Unexpected Credit Gain
  ```

* Test interactions between related features.

---

## Feature Chaining

* Potential combinations include:

  ```text
  Referral + Trial

  Coupon + Refund

  Gift Card + Cancellation

  Role Change + Approval

  Subscription + Feature Access

  Invitation + Account Recovery
  ```

* Only investigate combinations relevant to observed application functionality.

---

## Alternate Interfaces

* The same business operation may be exposed through:

  ```text
  Browser UI

  REST API

  GraphQL

  WebSocket

  Mobile API
  ```

* Compare whether the same invariant is enforced everywhere.

---

## Example

```text
Web UI:

Cannot Submit Without Approval
```

* But:

  ```text
  Direct API:

  POST /api/execute
  ```

* May fail to verify approval state.

---

## Business Logic Test Matrix

| Dimension   | Questions                                        |
| ----------- | ------------------------------------------------ |
| Sequence    | Can steps be skipped or reordered?               |
| Repetition  | Can one-time actions repeat?                     |
| Values      | Can sensitive values be manipulated?             |
| Limits      | Can boundaries or cumulative limits be bypassed? |
| Identity    | Can another user complete the workflow?          |
| Role        | Can restricted actors perform actions?           |
| State       | Is current state revalidated?                    |
| Time        | Are expiry and timing rules enforced?            |
| Concurrency | Can simultaneous actions break invariants?       |
| Interface   | Are rules consistent across APIs and clients?    |

---

## Business Logic Test Card

```text
Test ID:

Feature:

Workflow:

User:

Role:

Tenant:

Starting State:

Business Rule:

Invariant:

Baseline:

Changed Variable:

Modified Sequence / Request:

Expected Result:

Actual Response:

Final State:

Side Effects:

Reproducible?:

Security Impact:

Business Impact:

Evidence:

Conclusion:
```

---

## Business Logic Testing Workflow

```text
Define Scope

↓

Understand Feature Purpose

↓

Map Legitimate Workflow

↓

Identify Business Rules

↓

Define Invariants

↓

Identify Client-Controlled Inputs

↓

Identify State Transitions

↓

Establish Baseline

↓

Generate Abuse Hypotheses

↓

Change One Condition

↓

Replay / Reorder / Modify Safely

↓

Observe Response

↓

Verify Final State

↓

Repeat for Alternate Paths

↓

Validate Reproducibility

↓

Measure Impact

↓

Document Evidence
```

---

## Step 1 — Understand the Feature

* Ask:

  ```text
  What Is the User Trying to Achieve?

  What Does the Business Need to Prevent?

  What Creates Value?

  What Creates Risk?
  ```

---

## Step 2 — Map the Workflow

* Capture every meaningful request from start to finish.

---

## Step 3 — Identify Rules

* Examples:

  ```text
  Maximum

  Minimum

  One-Time

  Ownership

  Eligibility

  Required Order

  Approval

  Expiration
  ```

---

## Step 4 — Define Invariants

* Write explicit statements that should always remain true.

---

## Step 5 — Identify Trust Boundaries

* Determine which values come from:

  ```text
  Client

  Server

  Third Party

  Previous Workflow Step
  ```

---

## Step 6 — Establish Baseline

* Complete the workflow normally and record final state.

---

## Step 7 — Generate Abuse Cases

* Consider:

  ```text
  Skip

  Repeat

  Reorder

  Modify

  Replay

  Switch Identity

  Change State

  Cross Interface
  ```

---

## Step 8 — Change One Condition

* Keep the experiment controlled.

---

## Step 9 — Verify State

* Inspect the authoritative backend-visible result where possible.

---

## Step 10 — Test Alternate Paths

* Review:

  ```text
  Error Paths

  Cancellation

  Retry

  Refresh

  Back Button

  Alternate API
  ```

---

## Step 11 — Test Boundary Conditions

* Use meaningful values around documented or observed limits.

---

## Step 12 — Review Repetition

* Determine which actions should be:

  ```text
  Repeatable

  Idempotent

  Single Use
  ```

---

## Step 13 — Review Concurrency

* Only when relevant and explicitly safe.

---

## Step 14 — Validate Impact

* Determine:

  ```text
  What Rule Was Broken?

  What Unauthorized Benefit Occurred?

  What Loss or Security Impact Is Possible?

  Is It Repeatable?
  ```

---

## Example — Coupon Reuse

### Business Rule

```text
Coupon TEST10

Can Be Used Once Per Controlled Account
```

### Baseline

```text
Apply Coupon

↓

Discount Applied

↓

Complete Controlled Order
```

### Test

```text
Create New Controlled Order

↓

Apply Same Coupon Again
```

### Validate

* Determine whether:

  ```text
  Coupon Is Rejected

  or

  Unauthorized Second Discount Is Granted
  ```

* Confirm the intended one-use policy before reporting.

---

## Example — Workflow Step Skipping

### Expected

```text
Create Request

↓

Review

↓

Approve

↓

Execute
```

### Controlled Test

```text
Create New Request

↓

Replay Execute Request Directly
```

### Vulnerable Result

```text
Request Executes Without Required Review / Approval
```

* Verify final state.

---

## Example — Stale Price

### Expected

```text
Select Product

↓

Server Determines Current Price

↓

Checkout Uses Current Price
```

### Controlled Test

```text
Capture Checkout Request

↓

Change Product / Plan State

↓

Replay Old Request
```

### Security Question

```text
Does the Server Recalculate the Authoritative Price?
```

---

## Example — Duplicate Reward

### Invariant

```text
One Eligible Event

↓

Maximum One Reward
```

### Controlled Test

```text
Trigger Reward Once

↓

Replay Same Controlled Claim
```

### Validate

```text
Reward Balance Before

vs

Reward Balance After
```

* A duplicate response alone is insufficient; verify actual credit.

---

## Example — Limit Boundary

### Rule

```text
Maximum Allowed Quantity = 10
```

### Tests

```text
9

10

11
```

* Compare behavior.

* If `11` succeeds, verify whether the limit is genuinely security- or business-relevant rather than merely a UI preference.

---

## Example — Self-Approval

### Rule

```text
Requester Must Not Approve Own Request
```

### Test

```text
Controlled User Creates Request

↓

Same Identity Attempts Approval
```

* If multiple roles are involved, verify the backend checks actor identity rather than only role labels.

---

## Example — Cross-Protocol Inconsistency

```text
Web UI

↓

Blocks Invalid Transition
```

* But:

  ```text
  REST API

  ↓

  Accepts Same Invalid Transition
  ```

* Verify that both interfaces represent the same business operation and rule.

---

## Example — Idempotency

### Operation

```text
Create Controlled Payment Intent
```

### Expected

```text
Same Idempotency Key

↓

No Duplicate Processing
```

### Test

* Use a sandbox or authorized test environment.

* Verify backend transaction state rather than relying only on response text.

---

## Evidence Requirements

* Strong business logic evidence should include:

  ```text
  Feature

  Intended Business Rule

  Source of Rule / Observed Expected Behavior

  User / Role

  Starting State

  Legitimate Baseline

  Exact Modified Condition

  Request Sequence

  Responses

  Final State

  Side Effects

  Reproduction Steps

  Security / Business Impact
  ```

---

## Proving the Rule

* One of the hardest parts of business logic reporting is proving that the behavior violates intended rules.

* Evidence may come from:

  ```text
  Application UI

  Terms / Documentation

  Error Messages

  Workflow Design

  Consistent Baseline Behavior

  Program Rules

  Explicit Stakeholder Confirmation
  ```

---

## Important Principle

```text
Unexpected Behavior

≠

Business Logic Vulnerability
```

* You must establish:

  ```text
  Intended Rule

  +

  Rule Violation

  +

  Meaningful Impact
  ```

---

## Anomaly vs Vulnerability

* Anomaly:

  ```text
  Request Can Be Replayed
  ```

* Validated vulnerability:

  ```text
  Request Should Be Single Use

  +

  Replay Is Accepted

  +

  Duplicate Sensitive Effect Occurs

  +

  Meaningful Impact Is Demonstrated
  ```

---

## False Positive Causes

* Common causes include:

  ```text
  Misunderstood Business Rule

  Intended Retry Behavior

  Intentionally Reusable Coupon

  Shared Account Entitlement

  Eventually Consistent State

  Sandbox Behavior

  UI Recommendation Rather Than Hard Rule

  Duplicate Response Without Duplicate State Change
  ```

---

## False Positive Control

```text
Confirm Business Rule

↓

Restore Clean Baseline

↓

Record Starting State

↓

Change One Condition

↓

Send Controlled Request

↓

Verify Final State

↓

Repeat

↓

Check Alternate Explanation

↓

Confirm Meaningful Impact
```

---

## Business Logic Testing Checklist

### Workflow

* Review:

  ```text
  Steps

  Order

  Required Transitions

  Optional Transitions

  Error Paths
  ```

---

### Sequence

* Test:

  ```text
  Skip

  Reorder

  Repeat

  Replay
  ```

---

### Values

* Review:

  ```text
  Price

  Quantity

  Discount

  Credit

  Balance

  Currency

  Status
  ```

---

### Limits

* Review:

  ```text
  Minimum

  Maximum

  Per Transaction

  Cumulative

  Per User

  Per Tenant
  ```

---

### One-Time Actions

* Review:

  ```text
  Coupons

  Tokens

  Rewards

  Invitations

  Recovery Codes

  Confirmations
  ```

---

### State

* Review:

  ```text
  Current State

  Stale Requests

  Invalid Transitions

  Cancellation

  Retry

  Reversal
  ```

---

### Identity

* Review:

  ```text
  User Switching

  Role Switching

  Ownership Changes

  Separation of Duties

  Self-Approval
  ```

---

### Time

* Review:

  ```text
  Expiration

  Trial

  Deadlines

  Booking Windows

  Renewal
  ```

---

### Concurrency

* Where explicitly safe, review:

  ```text
  Duplicate Submission

  Race Conditions

  TOCTOU

  Shared-State Updates
  ```

---

### Interfaces

* Compare:

  ```text
  Web UI

  REST

  GraphQL

  WebSocket

  Mobile / Legacy APIs
  ```

---

## Business Logic Investigation Journal

```text
Application:

Date:

Scope:

Feature:

Users:

Roles:

Tenants:

Normal Workflow:

Starting State:

Final State:

Business Rules:

Invariants:

Client-Controlled Values:

Server-Controlled Values:

State Transitions:

One-Time Actions:

Limits:

Eligibility Rules:

Approval Rules:

Time Rules:

Baseline Requests:

Step-Skipping Tests:

Step-Reordering Tests:

Replay Tests:

Duplicate Tests:

Boundary Tests:

Value Manipulation:

Stale Request Tests:

Identity Transition Tests:

Role Transition Tests:

Cancellation Tests:

Retry Tests:

Cross-Interface Tests:

Concurrency Tests:

High-Priority Abuse Cases:

Validated Findings:

False Positives:

Evidence:

Next Steps:
```

---

## Professional Tips

* Understand the feature before attempting to break it.
* Write down the business rule you are testing.
* Convert important rules into explicit invariants.
* Complete the legitimate workflow first.
* Record starting and final state for every meaningful test.
* Change one condition at a time.
* Test workflow sequence, repetition, values, identity, state, limits, and time separately.
* Treat client-side validation as a usability control, not a trusted security boundary.
* Determine which business values are authoritative on the server.
* Test meaningful boundaries around actual limits rather than random extreme values.
* Verify one-time actions by checking real backend state after replay.
* Distinguish repeatable, idempotent, and single-use operations.
* Test stale requests after state changes.
* Review error, cancellation, retry, and reversal paths.
* Compare the same business rule across web, REST, GraphQL, WebSocket, and legacy interfaces.
* Use disposable objects and controlled accounts for state-changing tests.
* Avoid real financial impact, inventory disruption, or abuse of third-party systems.
* Treat race-condition testing as potentially disruptive and use explicit authorization.
* Confirm the intended rule before reporting a bypass.
* Measure actual security or business impact rather than reporting unusual behavior alone.
* Preserve the complete request sequence because business logic findings often depend on workflow context.

---

## Common Mistakes

* Testing requests without understanding the business workflow.
* Assuming unexpected behavior is automatically a vulnerability.
* Failing to establish the intended business rule.
* Changing multiple conditions simultaneously.
* Ignoring backend state after a successful-looking response.
* Treating duplicate responses as duplicate transactions without verification.
* Testing only individual endpoints instead of complete workflows.
* Ignoring step skipping.
* Ignoring step reordering.
* Ignoring stale requests.
* Ignoring retry and cancellation paths.
* Assuming every action must be single use.
* Assuming every observed limit is a security rule.
* Testing arbitrary extreme values without business relevance.
* Performing real financial abuse.
* Disrupting real inventory or bookings.
* Creating uncontrolled duplicate transactions.
* Ignoring cumulative limits.
* Ignoring cross-feature interactions.
* Ignoring role or identity transitions.
* Ignoring separation-of-duties requirements.
* Ignoring alternate APIs.
* Performing aggressive race testing without explicit authorization.
* Reporting client-side validation bypass without proving backend impact.
* Failing to prove reproducibility.
* Failing to explain the violated invariant clearly.

---

## Practice Tasks

### Task 1 — Select an Authorized Application

* Use:

  ```text
  Deliberately Vulnerable Lab

  System You Own

  or

  Explicitly Authorized Target
  ```

---

### Task 2 — Choose One Workflow

* Examples:

  ```text
  Registration

  Shopping Cart

  Coupon

  Subscription

  Approval

  Password Reset

  Booking
  ```

---

### Task 3 — Map the Workflow

* Record:

  ```text
  Every Step

  Request

  Response

  State Change
  ```

---

### Task 4 — Define Five Business Rules

* Example:

  ```text
  Coupon Can Be Used Once

  Quantity Cannot Exceed Limit

  Approval Required Before Execution
  ```

---

### Task 5 — Convert Rules Into Invariants

* Write:

  ```text
  This Must Always Be True...
  ```

* For each rule.

---

### Task 6 — Establish Baseline

* Complete the workflow normally.

* Record starting and final state.

---

### Task 7 — Test Step Skipping

* Skip one non-destructive intermediate step.

* Verify whether the server rejects the invalid transition.

---

### Task 8 — Test Step Reordering

* Reorder two safe steps.

* Observe state handling.

---

### Task 9 — Test Replay

* Replay one harmless action.

* Verify whether actual state changes more than once.

---

### Task 10 — Test a Boundary

* Identify one legitimate minimum or maximum.

* Test:

  ```text
  Boundary - 1

  Boundary

  Boundary + 1
  ```

---

### Task 11 — Test a Client-Controlled Value

* Identify one harmless business parameter.

* Determine whether the server independently validates it.

---

### Task 12 — Test a Stale Request

* Capture a request.

* Change relevant state legitimately.

* Replay the old request.

* Verify current-state validation.

---

### Task 13 — Compare Interfaces

* If available, compare the same business rule through:

  ```text
  UI

  and

  API
  ```

---

### Task 14 — Generate Abuse Hypotheses

* Write at least five questions such as:

  ```text
  Can this required step be skipped?

  Can this one-time action be repeated?

  Can this limit be bypassed across multiple requests?

  Can stale state be reused?

  Does another API enforce the same rule?
  ```

---

## Interview Questions

1. What is a business logic vulnerability?
2. How does a business logic flaw differ from a technical vulnerability?
3. Why are business logic vulnerabilities difficult for automated scanners to detect?
4. What is a business invariant?
5. Why should business rules be written explicitly before testing?
6. How do you map a multi-step workflow in Burp Suite?
7. Why is a legitimate baseline important?
8. What is step-skipping testing?
9. How would you test whether workflow steps can be reordered?
10. What is replay testing?
11. Why can a previously valid request become invalid later?
12. What is a stale request?
13. How do you test one-time actions?
14. What types of business values should be considered security-sensitive?
15. How would you test whether a server trusts a client-supplied price?
16. Why should financial testing avoid real-world impact?
17. How do you test quantity boundaries?
18. Why are boundary values more useful than arbitrary extreme inputs?
19. What business logic flaws can occur in coupon systems?
20. What is stale discount state?
21. What is the difference between per-transaction and cumulative limits?
22. Why must you confirm the intended semantics of a limit?
23. What is idempotency?
24. What is an idempotency key?
25. Why are duplicate-sensitive operations important to test?
26. How can client-side validation create false confidence?
27. What is cross-step parameter consistency?
28. How can time-based business rules fail?
29. What is separation of duties?
30. What is self-approval?
31. How can identity switching affect workflow security?
32. What is TOCTOU?
33. What is a race condition?
34. Why can race-condition testing be dangerous in production?
35. How can partial failures create business logic vulnerabilities?
36. Why should retry and cancellation paths be tested?
37. What invariants are relevant to refund systems?
38. What risks exist in referral and reward systems?
39. How can subscription upgrade and downgrade workflows create logic flaws?
40. What are abuse cases?
41. What are negative requirements?
42. Why should related features be tested together?
43. How can alternate APIs create inconsistent business-rule enforcement?
44. Why is backend state verification essential?
45. What evidence is required for a strong business logic vulnerability report?
46. How do you prove that observed behavior actually violates an intended rule?
47. What is the difference between an anomaly and a validated logic flaw?
48. What are common false positives in business logic testing?
49. How would you prioritize business logic tests on an unfamiliar application?
50. Describe your complete methodology for business logic testing using Burp Suite.

---

## Quick Revision

* Business logic:

  ```text
  Application-Specific Rules
  ```

* Logic vulnerability:

  ```text
  Intended Rule

  +

  Rule Violation

  +

  Meaningful Impact
  ```

* Invariant:

  ```text
  Something That Must Always Remain True
  ```

* Workflow:

  ```text
  Start

  ↓

  Step 1

  ↓

  Step 2

  ↓

  Step 3

  ↓

  Final State
  ```

* Core tests:

  ```text
  Skip

  Reorder

  Repeat

  Replay

  Modify

  Change State

  Switch Identity

  Test Boundaries
  ```

* Important rule:

  ```text
  UI Restriction

  ≠

  Server-Side Enforcement
  ```

* One-time action:

  ```text
  Valid

  ↓

  Used

  ↓

  Invalid
  ```

* Boundary testing:

  ```text
  Limit - 1

  Limit

  Limit + 1
  ```

* State testing:

  ```text
  Initial State

  ↓

  Controlled Action

  ↓

  Final State
  ```

* Replay:

  ```text
  Same Request

  ≠

  Necessarily Same Validity
  ```

* Race concept:

  ```text
  Check

  ↓

  Concurrent Change

  ↓

  Use Stale Assumption
  ```

* Professional workflow:

  ```text
  Understand Feature

  ↓

  Map Workflow

  ↓

  Identify Rules

  ↓

  Define Invariants

  ↓

  Baseline

  ↓

  Generate Abuse Cases

  ↓

  Change One Condition

  ↓

  Verify Final State

  ↓

  Validate Impact
  ```

---

## Key Takeaways

* Business logic vulnerabilities arise when users can violate intended application rules in a security- or business-relevant way.
* These flaws require understanding application intent and are often poorly suited to purely automated discovery.
* Map legitimate workflows before attempting modified sequences.
* Convert important business rules into explicit invariants that should always remain true.
* Test sequence, repetition, values, identity, role, state, time, limits, concurrency, and alternate interfaces systematically.
* Step skipping and step reordering can reveal missing server-side workflow enforcement.
* Previously valid requests should not automatically remain valid after relevant application state changes.
* One-time actions require verification of actual consumption and replay behavior.
* Sensitive values such as prices, quantities, discounts, balances, credits, and status fields should not be trusted merely because the client supplies them.
* Boundary tests should focus on meaningful values around actual business limits.
* Per-transaction limits and cumulative limits are different rules and must not be confused.
* Duplicate-sensitive operations may require idempotency or other server-side safeguards.
* UI restrictions and client-side validation do not replace backend business-rule enforcement.
* Cross-step values should be revalidated against authoritative current state.
* Separation-of-duties rules can prevent the same actor from controlling incompatible stages of sensitive workflows.
* Retry, cancellation, reversal, and failure paths can create inconsistent state and deserve dedicated testing.
* Race conditions and TOCTOU issues can break business invariants but require careful, explicitly authorized testing.
* Related features may become vulnerable only when chained together, so cross-feature interactions matter.
* The same business rules should be enforced consistently across web interfaces, REST APIs, GraphQL, WebSockets, mobile APIs, and legacy endpoints.
* A successful response is not sufficient evidence; verify actual backend-visible state and side effects.
* Unexpected behavior becomes a valid business logic finding only when the intended rule, rule violation, reproducibility, and meaningful impact are established.
* The professional business logic workflow is **understand feature → map legitimate workflow → identify business rules → define invariants → identify trust boundaries → establish baseline → generate abuse hypotheses → change one condition → replay/reorder/modify safely → verify final state → test alternate paths → validate reproducibility and impact → document evidence**.
