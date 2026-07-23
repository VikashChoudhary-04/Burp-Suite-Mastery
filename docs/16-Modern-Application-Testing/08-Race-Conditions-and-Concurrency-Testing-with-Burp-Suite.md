# Race Conditions & Concurrency Testing with Burp Suite

> **Module 16 — Modern Application Testing**

> **Progress**

```text
█████████░░░░ 9 / 13 Files
```

---

## Overview

* Race conditions occur when an application's security or business logic depends on the timing and order of operations performed concurrently.

* A typical race condition looks like:

  ```text
  Check Current State

  ↓

  Assume State Will Remain Unchanged

  ↓

  Another Request Changes the State

  ↓

  Original Operation Continues

  ↓

  Invalid or Duplicate Outcome
  ```

* These vulnerabilities are especially important in operations involving:

  * Payments.
  * Account balances.
  * Coupon redemption.
  * Gift cards.
  * Reward claims.
  * Inventory.
  * Booking systems.
  * Invitations.
  * One-time tokens.
  * Rate limits.
  * Usage quotas.
  * File processing.
  * Workflow transitions.
  * Account recovery.

* Burp Suite can help investigate concurrency behavior by capturing legitimate requests, reproducing controlled operations, sending requests in parallel, and comparing the resulting application state.

* Race-condition testing must be performed carefully because concurrent requests can create duplicate transactions, consume resources, or modify state unexpectedly.

---

## Learning Objectives

* By the end of this lesson, you will be able to:

  * Understand race conditions and concurrency vulnerabilities.
  * Understand TOCTOU weaknesses.
  * Identify race-condition candidates.
  * Recognize vulnerable check-then-act patterns.
  * Distinguish race conditions from ordinary replay vulnerabilities.
  * Define concurrency invariants.
  * Establish reliable sequential baselines.
  * Use Burp Repeater for controlled parallel-request testing.
  * Understand synchronized request techniques conceptually.
  * Test one-time actions safely.
  * Investigate duplicate-processing vulnerabilities.
  * Test limits, balances, coupons, rewards, and inventory safely.
  * Analyze workflow-state races.
  * Understand optimistic and pessimistic locking.
  * Understand atomicity and database transactions.
  * Recognize idempotency controls.
  * Verify final application state after concurrent requests.
  * Reduce false positives caused by asynchronous processing.
  * Build a repeatable concurrency-testing methodology.

---

## What Is Concurrency?

* Concurrency occurs when multiple operations are processed during overlapping periods of time.

* Example:

  ```text
  Request A ───────────────►

        Request B ───────────────►
  ```

* The requests do not necessarily execute in a perfectly sequential order.

---

## What Is a Race Condition?

* A race condition occurs when the application's outcome depends incorrectly on which concurrent operation reaches or modifies shared state first.

* Example:

  ```text
  Balance = 100

  Request A Checks Balance = 100

  Request B Checks Balance = 100

  Request A Spends 80

  Request B Spends 80

  ↓

  Both Requests Were Approved Using the Same Initial State
  ```

* If the intended rule is:

  ```text
  Total Spending ≤ Available Balance
  ```

* The application may have violated an important invariant.

---

## Core Race Pattern

```text
Read Shared State

↓

Validate Condition

↓

Perform Action

↓

Update Shared State
```

* If another operation can intervene between these steps, inconsistent behavior may occur.

---

## TOCTOU

* TOCTOU stands for:

  ```text
  Time Of Check To Time Of Use
  ```

* It describes a gap between:

  ```text
  Checking a Condition

  and

  Acting on That Condition
  ```

---

## TOCTOU Example

```text
Check:

Coupon Has Not Been Used

↓

Concurrent Requests Both Pass Check

↓

Request A Redeems Coupon

Request B Redeems Coupon

↓

Coupon Benefit Granted Twice
```

---

## Race Condition vs Replay

* Replay vulnerability:

  ```text
  Request A

  ↓

  Completes

  ↓

  Request A Replayed

  ↓

  Completes Again
  ```

* Race condition:

  ```text
  Request A ─────►

  Request B ─────►

  Both Reach Critical Logic Before State Is Safely Updated
  ```

* The distinction matters.

---

## Important Principle

```text
Sequential Replay Success

≠

Race Condition
```

* If an action can simply be repeated after completion, the issue may be missing replay protection or broken business logic rather than concurrency.

---

## Another Important Principle

```text
Sequential Requests Safe

+

Concurrent Requests Unsafe

→

Strong Race-Condition Signal
```

---

## Race Window

* The race window is the period during which concurrent requests can interact with stale or incomplete state.

* Conceptually:

  ```text
  Check

  ├──────── Race Window ────────┤

                              Update
  ```

* Larger race windows may make a flaw easier to reproduce.

---

## Shared State

* Race conditions usually involve shared state.

* Examples:

  ```text
  Account Balance

  Coupon Usage Counter

  Inventory Quantity

  Reward Status

  Token State

  Booking Capacity

  Usage Quota

  Workflow Status

  Account Role

  Database Record
  ```

---

## Concurrency Invariants

* Before testing, define what must remain true even when operations occur concurrently.

* Examples:

  ```text
  Balance Must Never Be Overspent

  One-Time Coupon Must Produce One Benefit

  One Seat Must Be Sold Once

  One Reward Must Be Granted Once

  Usage Must Not Exceed the Defined Limit

  One-Time Token Must Produce One Successful Action
  ```

---

## Invariant Template

```text
Resource:

Shared State:

Operation:

Invariant:

Expected Sequential Behavior:

Expected Concurrent Behavior:

Maximum Allowed Successful Operations:
```

---

## Example

```text
Resource:

Single-Use Coupon

Shared State:

Coupon Redemption Status

Operation:

Redeem Coupon

Invariant:

Coupon May Produce Benefit Once

Expected Sequential Behavior:

First Request Succeeds
Second Request Fails

Expected Concurrent Behavior:

Maximum One Successful Redemption

Maximum Allowed Successful Operations:

1
```

---

## Where Race Conditions Commonly Appear

* High-value candidates include:

  ```text
  Financial Transactions

  Coupon Redemption

  Reward Claims

  Gift Card Usage

  Inventory Reservation

  Booking Capacity

  Password Reset

  Email Verification

  Invitation Acceptance

  Rate Limiting

  Usage Quotas

  Workflow Approval

  File Operations

  Subscription Changes
  ```

---

## Candidate Selection

* Look for operations involving:

  ```text
  Check

  ↓

  Decision

  ↓

  State Change
  ```

* Especially when the operation depends on a limited resource.

---

## High-Value Questions

```text
Can This Action Succeed Only Once?

Is There a Limited Balance?

Is There a Maximum Quantity?

Is There a Shared Counter?

Is There a Unique Resource?

Does the Server Check State Before Updating It?

Could Two Requests Observe the Same Old State?
```

---

## Start With Sequential Behavior

* Before testing concurrency, establish normal sequential behavior.

* Example:

  ```text
  Request 1

  ↓

  Success

  ↓

  Request 2

  ↓

  Rejected
  ```

* This proves the application normally enforces the one-time rule.

---

## Why Sequential Baselines Matter

* Suppose two concurrent requests both succeed.

* Without a sequential baseline, you may not know whether:

  ```text
  The Action Is Intentionally Repeatable
  ```

* Or:

  ```text
  Concurrency Bypassed a Restriction
  ```

---

## Baseline Workflow

```text
Prepare Clean State

↓

Send Request Once

↓

Verify State

↓

Send Same Logical Action Again

↓

Verify Expected Rejection

↓

Reset Test State
```

---

## Burp Suite Workflow

```text
Proxy

↓

Capture Legitimate State-Changing Request

↓

Send to Repeater

↓

Establish Sequential Baseline

↓

Reset Controlled State

↓

Duplicate Request

↓

Prepare Concurrent Send

↓

Send Requests

↓

Observe Responses

↓

Verify Final Backend State

↓

Compare With Invariant
```

---

## Burp Repeater

* Repeater is useful for:

  ```text
  Preserving Exact Requests

  Creating Multiple Tabs

  Modifying Controlled Values

  Comparing Sequential Behavior

  Sending Request Groups

  Analyzing Responses
  ```

---

## Parallel Requests

* Modern Burp Suite versions provide mechanisms for sending groups of requests in parallel.

* The exact interface may vary by version.

* The important concept is:

  ```text
  Prepare Equivalent Requests

  ↓

  Release Them With Maximum Practical Overlap

  ↓

  Observe Whether Shared-State Enforcement Remains Correct
  ```

---

## Synchronization Problem

* Simply clicking:

  ```text
  Send

  Send

  Send
  ```

* Quickly does not guarantee that requests reach the critical server-side operation simultaneously.

* Network timing may introduce:

  ```text
  Connection Setup

  TLS Processing

  Network Latency

  Server Scheduling

  Application Processing Differences
  ```

---

## Synchronized Request Techniques

* Race testing may use techniques designed to reduce timing differences between requests.

* Conceptually:

  ```text
  Prepare Requests

  ↓

  Hold Them Near the Critical Processing Point

  ↓

  Release Together
  ```

* The exact technique depends on protocol and Burp version.

---

## HTTP/2 and Concurrency

* HTTP/2 supports multiple streams over a connection.

* This can help reduce some connection-level timing differences during controlled concurrency testing.

* However:

  ```text
  HTTP/2

  ≠

  Guaranteed Simultaneous Server Execution
  ```

---

## Single-Packet Synchronization

* Some race-testing approaches attempt to place critical portions of multiple requests into a tightly synchronized network delivery.

* The goal is to reduce timing jitter.

* Conceptually:

  ```text
  Request A ─┐

  Request B ─┼──► Server Processes Near-Simultaneously

  Request C ─┘
  ```

* Use only where supported and appropriate.

---

## Important Testing Rule

```text
Start Small
```

* Begin with:

  ```text
  2 Requests
  ```

* Not:

  ```text
  100 Requests
  ```

* Increase only when necessary, safe, and explicitly authorized.

---

## Why Small Request Counts Matter

* Excessive concurrency can:

  ```text
  Create Duplicate Transactions

  Consume Inventory

  Trigger Rate Limits

  Cause Account Locks

  Create Operational Noise

  Affect Other Users

  Resemble Denial-of-Service Activity
  ```

---

## Controlled Test Objects

* Prefer:

  ```text
  Test Accounts

  Disposable Objects

  Sandbox Transactions

  Non-Production Credits

  Lab Coupons

  Controlled Bookings
  ```

---

## One-Time Action Race

* Expected:

  ```text
  Action Available = Yes

  ↓

  First Request

  ↓

  Action Available = No
  ```

* Potential race:

  ```text
  Request A Checks = Yes

  Request B Checks = Yes

  ↓

  Both Execute
  ```

---

## Example — Coupon Redemption

### Invariant

```text
One Coupon

↓

Maximum One Discount
```

### Sequential Baseline

```text
Request A

↓

Discount Applied

↓

Request B

↓

Rejected
```

### Concurrent Test

```text
Request A ─────►

Request B ─────►
```

### Potential Vulnerable Result

```text
Two Benefits Granted

or

Coupon Value Applied More Than Allowed
```

* Verify actual account/order state.

---

## Example — Reward Claim

### Rule

```text
One Eligible Event

↓

One Reward
```

### Test

```text
Prepare Controlled Eligible Account

↓

Capture Claim Request

↓

Verify Sequential Protection

↓

Reset State

↓

Send Two Controlled Concurrent Claims

↓

Inspect Reward Balance
```

---

## Duplicate Response vs Duplicate Effect

* Two responses may both say:

  ```text
  Success
  ```

* But the backend may still apply the action once.

* Therefore:

  ```text
  Two Success Responses

  ≠

  Two Successful State Changes
  ```

---

## State Verification

* Always inspect authoritative state.

* Examples:

  ```text
  Balance Before / After

  Coupon Usage Count

  Reward Balance

  Inventory Count

  Number of Orders

  Number of Transactions

  Token State

  Workflow State
  ```

---

## Example — Balance Race

### Initial State

```text
Balance = 100
```

### Rule

```text
Total Debit Must Not Exceed 100
```

### Controlled Operations

```text
Debit A = 80

Debit B = 80
```

### Secure Result

* At most one operation should succeed if both consume the same limited balance.

### Potential Vulnerable Result

```text
Both Debits Succeed

↓

Total Debit = 160
```

* Financial testing should use sandbox balances or explicitly authorized test environments.

---

## Check-Then-Update Pattern

* Vulnerable conceptual logic:

  ```text
  Read Balance

  ↓

  If Balance >= Amount

  ↓

  Process Transaction

  ↓

  Update Balance
  ```

* Two concurrent operations may read the same original balance.

---

## Safer Conceptual Design

* Security may require:

  ```text
  Atomic Database Operation

  Transaction Isolation

  Row Lock

  Compare-and-Swap

  Unique Constraint

  Idempotency Control
  ```

* The exact solution depends on the system.

---

## Atomicity

* An atomic operation behaves as one indivisible logical action.

* Conceptually:

  ```text
  Check + Update

  ↓

  One Protected Operation
  ```

* Other operations should not observe an unsafe intermediate state.

---

## Database Transactions

* Transactions can group operations into a controlled unit.

* Correct transaction design can help preserve invariants.

* However:

  ```text
  Uses Database Transaction

  ≠

  Automatically Race-Safe
  ```

* Isolation level and application logic still matter.

---

## Pessimistic Locking

* Pessimistic locking restricts concurrent access to shared state while an operation is in progress.

* Conceptually:

  ```text
  Request A Locks Resource

  ↓

  Request B Waits

  ↓

  Request A Updates

  ↓

  Lock Released

  ↓

  Request B Rechecks Current State
  ```

---

## Optimistic Locking

* Optimistic locking allows concurrent reads but detects conflicting updates.

* Common concepts include:

  ```text
  Version Number

  Revision

  ETag

  Updated Timestamp
  ```

---

## Example

```text
Version = 5

Request A Updates Version 5

↓

New Version = 6

Request B Attempts Update Using Version 5

↓

Rejected as Stale
```

---

## Compare-and-Swap

* Conceptually:

  ```text
  Update Resource

  Only If

  Current State = Expected State
  ```

* This can prevent stale updates from silently overwriting newer state.

---

## Lost Update

* A lost update occurs when concurrent changes overwrite one another.

* Example:

  ```text
  Initial Value = 10

  Request A Reads 10

  Request B Reads 10

  Request A Writes 11

  Request B Writes 11

  ↓

  One Increment Is Lost
  ```

---

## Lost Update Security Impact

* Depending on context, this may affect:

  ```text
  Counters

  Quotas

  Inventory

  Balances

  Security Settings

  Workflow State
  ```

---

## Limit Race

* Suppose:

  ```text
  Maximum Claims = 1
  ```

* Two requests may both observe:

  ```text
  Claims Used = 0
  ```

* Before either updates the counter.

---

## Quota Race

* Example:

  ```text
  Remaining Quota = 1

  Request A Checks = 1

  Request B Checks = 1

  ↓

  Both Consume Resource
  ```

---

## Rate-Limit Races

* Some applications enforce limits through counters.

* Concurrency may expose inconsistent counter updates.

* Avoid high-volume testing.

* A few controlled requests are usually sufficient to investigate a hypothesis.

---

## Inventory Race

### Initial State

```text
Available Stock = 1
```

### Concurrent Requests

```text
Purchase A ─────►

Purchase B ─────►
```

### Invariant

```text
Sold Quantity

≤

Available Quantity
```

* Do not test against scarce real-world inventory in a way that affects legitimate users.

---

## Booking Race

* Similar risks occur with:

  ```text
  Seats

  Rooms

  Appointments

  Reservation Slots
  ```

* Example invariant:

  ```text
  One Exclusive Slot

  →

  Maximum One Confirmed Booking
  ```

---

## Reservation vs Confirmation

* Some systems intentionally allow temporary reservations.

* Therefore distinguish:

  ```text
  Reserved

  vs

  Confirmed
  ```

* Multiple temporary holds may be intended.

---

## Important Principle

```text
Concurrent Reservation

≠

Automatically Double Booking
```

* Verify the final authoritative state.

---

## Token Race

* One-time tokens may be vulnerable when validation and invalidation are separate operations.

* Conceptual pattern:

  ```text
  Validate Token

  ↓

  Perform Sensitive Action

  ↓

  Invalidate Token
  ```

* Concurrent requests may both validate before invalidation occurs.

---

## Password Reset Race

* Potential security rule:

  ```text
  One-Time Reset Token

  →

  One Valid Password Change
  ```

* Testing must use only controlled accounts.

---

## Invitation Race

* Invitation workflows may involve:

  ```text
  Validate Invitation

  ↓

  Join Organization

  ↓

  Consume Invitation
  ```

* Concurrent acceptance may create unexpected membership behavior if state handling is weak.

---

## Workflow-State Race

* Example:

  ```text
  Request Status = Pending
  ```

* Concurrent actions:

  ```text
  Approve ─────►

  Cancel  ─────►
  ```

* Security question:

  ```text
  Can the Final State Become Invalid or Contradictory?
  ```

---

## Conflicting Operations

* Useful controlled pairs may include:

  ```text
  Approve vs Cancel

  Accept vs Reject

  Update vs Delete

  Purchase vs Cancel

  Redeem vs Revoke
  ```

* Only test safe combinations on disposable objects.

---

## State Transition Invariant

```text
Current State

+

Allowed Action

→

Exactly One Valid Next State
```

* Concurrent conflicting operations should not create impossible states.

---

## Double-Spend Pattern

* A double-spend-style race occurs when the same limited resource is consumed multiple times because concurrent operations use stale state.

* Limited resources may include:

  ```text
  Money

  Credit

  Gift Card Balance

  Reward Points

  Usage Credits

  Inventory
  ```

---

## Gift Card Example

### Initial State

```text
Gift Card Balance = 50
```

### Concurrent Requests

```text
Spend 40 ─────►

Spend 40 ─────►
```

### Invariant

```text
Total Successful Spend ≤ 50
```

---

## Idempotency

* Idempotency helps prevent duplicate processing of logically identical operations.

* Example header:

  ```http
  Idempotency-Key: controlled-test-123
  ```

---

## Idempotency Questions

```text
Is the Key Required?

Is It Bound to the User?

Is It Bound to the Operation?

Is It Bound to the Request Body?

How Long Is It Stored?

What Happens During Concurrent Reuse?
```

---

## Same Key, Same Request

* Expected behavior may be:

  ```text
  First Request

  ↓

  Operation Processed

  ↓

  Duplicate Request With Same Key

  ↓

  Original Result Returned Without Duplicate Processing
  ```

---

## Same Key, Different Request

* Applications should define safe behavior.

* Security question:

  ```text
  Can One Idempotency Key Be Reused
  for Different Sensitive Operations?
  ```

---

## Unique Constraints

* Database uniqueness can protect certain invariants.

* Example:

  ```text
  One Reward Record Per Eligible Event
  ```

* A unique constraint may prevent duplicate creation even when requests race.

---

## Application-Level Check Only

* Conceptually weak pattern:

  ```text
  SELECT reward

  ↓

  None Found

  ↓

  INSERT reward
  ```

* Concurrent requests may both observe:

  ```text
  None Found
  ```

* Database-level constraints can provide an additional enforcement layer.

---

## Asynchronous Processing

* Some applications process requests through:

  ```text
  Queues

  Background Jobs

  Event Systems

  Workers
  ```

* Responses may arrive before final state is committed.

---

## Why This Matters

* You may observe:

  ```text
  Two Accepted Responses
  ```

* But later:

  ```text
  One Operation Is Deduplicated
  ```

* Or:

  ```text
  Both Operations Execute
  ```

* Final-state verification is essential.

---

## Eventual Consistency

* Distributed systems may temporarily show inconsistent state.

* Example:

  ```text
  Request Succeeds

  ↓

  Read Immediately

  ↓

  Old Value

  ↓

  Read Later

  ↓

  Updated Value
  ```

* This is not automatically a vulnerability.

---

## Avoiding False Positives

* Wait for expected processing to complete.

* Check authoritative records where available.

* Repeat from a clean state.

---

## Race Test Card

```text
Test ID:

Feature:

Endpoint / Operation:

User:

Role:

Starting State:

Shared Resource:

Business Rule:

Invariant:

Sequential Request 1:

Sequential Result 1:

Sequential Request 2:

Sequential Result 2:

Expected Maximum Successes:

Concurrent Request Count:

Synchronization Method:

Concurrent Responses:

Final State:

Actual Successful Effects:

Timing Notes:

Reproducible?:

Security / Business Impact:

Evidence:

Conclusion:
```

---

## Race-Condition Testing Workflow

```text
Define Scope

↓

Identify Limited or Shared State

↓

Understand Business Rule

↓

Define Concurrency Invariant

↓

Create Disposable Test State

↓

Capture Legitimate Request

↓

Establish Sequential Baseline

↓

Reset State

↓

Prepare Minimal Concurrent Requests

↓

Synchronize Where Appropriate

↓

Send Requests

↓

Record Every Response

↓

Wait for Processing

↓

Verify Authoritative Final State

↓

Compare With Invariant

↓

Repeat From Clean State

↓

Validate Impact

↓

Document Evidence
```

---

## Step 1 — Identify Candidate Operations

* Prioritize operations involving:

  ```text
  One-Time Actions

  Limited Resources

  Counters

  Balances

  Unique Objects

  State Transitions
  ```

---

## Step 2 — Define the Rule

* Example:

  ```text
  Reward May Be Claimed Once
  ```

---

## Step 3 — Define the Invariant

* Example:

  ```text
  One Eligible Event

  →

  Maximum One Reward Credit
  ```

---

## Step 4 — Prepare Controlled State

* Use:

  ```text
  Disposable Account

  Disposable Object

  Sandbox Balance

  Lab Coupon
  ```

---

## Step 5 — Capture Request

* Use Proxy and HTTP history.

* Send the important request to Repeater.

---

## Step 6 — Test Sequentially

* Determine whether:

  ```text
  First Request Succeeds

  Second Request Fails
  ```

* If both succeed sequentially, investigate replay/business logic first.

---

## Step 7 — Reset State

* Return to the same clean starting condition.

---

## Step 8 — Start With Two Requests

* Duplicate the request.

* Keep them logically equivalent unless testing conflicting operations.

---

## Step 9 — Send Concurrently

* Use the safest available synchronized mechanism.

---

## Step 10 — Record Responses

* Capture:

  ```text
  Status

  Body

  Timing

  Transaction IDs

  Object IDs

  Errors
  ```

---

## Step 11 — Verify Final State

* Determine how many real effects occurred.

---

## Step 12 — Repeat

* Reset the environment and reproduce the behavior.

* Race conditions can be timing-sensitive.

---

## Step 13 — Increase Carefully

* Only if two requests are insufficient and the test remains safe.

* Avoid unnecessary request volume.

---

## Step 14 — Measure Impact

* Determine whether the race enables:

  ```text
  Unauthorized Financial Gain

  Duplicate Benefit

  Limit Bypass

  Double Spend

  Invalid State

  Security-Control Bypass

  Unauthorized Resource Consumption
  ```

---

## Example — Controlled One-Time Reward Test

### Rule

```text
One Account

+

One Eligible Event

→

One Reward
```

### Sequential Baseline

```text
Claim 1

↓

Success

Claim 2

↓

Rejected
```

### Concurrent Test

```text
Reset Eligible State

↓

Prepare Two Identical Claim Requests

↓

Send Concurrently
```

### Verification

```text
Reward Balance Before = 0

Reward Balance After = ?
```

### Vulnerable Result

```text
Reward Balance After = 2 Rewards
```

* This demonstrates actual duplicate effect rather than duplicate response text.

---

## Example — Conflicting State Race

### Initial State

```text
Request = Pending
```

### Operations

```text
Approve

and

Cancel
```

### Invariant

```text
Request Must End in One Valid State
```

### Potential Vulnerable Outcomes

```text
Approved and Cancelled Side Effects Both Occur

or

Impossible State Created
```

---

## Example — Concurrent Limit Bypass

### Rule

```text
Maximum One Active Reservation
```

### Sequential Baseline

```text
Reservation 1

↓

Success

Reservation 2

↓

Rejected
```

### Concurrent Test

```text
Reservation A ─────►

Reservation B ─────►
```

### Verification

* Check the final list of active reservations.

---

## Example — Lost Update

### Initial State

```text
Counter = 10
```

### Requests

```text
Request A:

Increment Counter

Request B:

Increment Counter
```

### Expected

```text
Final Counter = 12
```

### Potential Race Result

```text
Final Counter = 11
```

* Security relevance depends on what the counter controls.

---

## Timing Measurements

* Response timing can help identify behavior.

* Record:

  ```text
  Request Start

  Response Time

  Completion Order
  ```

* But timing alone does not prove a race condition.

---

## Important Principle

```text
Timing Anomaly

≠

Race Vulnerability
```

* The vulnerability is the violated security or business invariant.

---

## Reproducibility

* Race conditions may not trigger every time.

* Record:

  ```text
  Attempts

  Successful Reproductions

  Request Count

  Starting State

  Timing Method

  Final State
  ```

---

## Example

```text
Attempts: 5

Race Triggered: 3

Concurrent Requests: 2
```

* This is more useful than claiming:

  ```text
  Sometimes Works
  ```

---

## Evidence Requirements

* Strong race-condition evidence should include:

  ```text
  Business Rule

  Concurrency Invariant

  Controlled Starting State

  Sequential Baseline

  Concurrent Requests

  Synchronization Method

  Responses

  Final Authoritative State

  Number of Actual Effects

  Reproduction Rate

  Security / Business Impact
  ```

---

## Strong Evidence Pattern

```text
Sequential:

Request 1 → Success

Request 2 → Rejected

Concurrent:

Request A + Request B

↓

Both Produce Real Effects

↓

Invariant Violated
```

---

## Anomaly vs Vulnerability

* Anomaly:

  ```text
  Two Concurrent Requests Return 200
  ```

* Validated vulnerability:

  ```text
  Sequential Duplicate Is Rejected

  +

  Concurrent Requests Both Pass

  +

  Two Real Sensitive Effects Occur

  +

  Business Invariant Is Violated
  ```

---

## False Positive Causes

* Common causes include:

  ```text
  Duplicate Responses but Single State Change

  Intended Repeatable Action

  Eventual Consistency

  Background Processing Delay

  Temporary UI State

  Test-State Contamination

  Cached Data

  Different Objects Processed

  Expected Reservation Semantics
  ```

---

## False Positive Control

```text
Define Invariant

↓

Establish Sequential Baseline

↓

Reset Clean State

↓

Use Minimal Concurrent Requests

↓

Wait for Processing

↓

Verify Authoritative State

↓

Repeat From Clean State

↓

Confirm Meaningful Impact
```

---

## Race Testing Checklist

### Candidate

* Does the operation involve:

  ```text
  Shared State?

  Limited Resource?

  One-Time Action?

  Counter?

  Unique Object?

  State Transition?
  ```

---

### Baseline

* Confirm:

  ```text
  First Sequential Action

  Second Sequential Action

  Expected State
  ```

---

### Concurrency

* Start with:

  ```text
  Two Requests
  ```

* Record synchronization method.

---

### Verification

* Check:

  ```text
  Final Balance

  Final Counter

  Final Object Count

  Final Status

  Transaction Records

  Side Effects
  ```

---

### Safety

* Use:

  ```text
  Controlled Accounts

  Disposable Objects

  Sandbox Value

  Small Request Counts
  ```

---

### Reproduction

* Record:

  ```text
  Attempts

  Success Rate

  Starting State

  Request Count
  ```

---

## Race-Condition Investigation Journal

```text
Application:

Date:

Scope:

Feature:

Users:

Roles:

Candidate Operation:

Shared Resource:

Business Rule:

Concurrency Invariant:

Starting State:

Sequential Baseline:

Sequential Request 1:

Sequential Result 1:

Sequential Request 2:

Sequential Result 2:

Concurrent Request Count:

Synchronization Method:

Concurrent Responses:

Response Timing:

Final State:

Actual Effects:

Expected Effects:

Invariant Violated?:

Attempts:

Successful Reproductions:

Idempotency Controls:

Locking Indicators:

Version / ETag Controls:

Async Processing:

Eventual Consistency Notes:

Security Impact:

Business Impact:

False Positive Checks:

Evidence:

Next Steps:
```

---

## Professional Tips

* Identify the business invariant before sending concurrent requests.
* Always establish sequential behavior first.
* Distinguish race conditions from ordinary replay vulnerabilities.
* Start with two requests and increase only when necessary.
* Use disposable objects and controlled accounts.
* Avoid concurrency testing that could affect real users, inventory, money, or availability.
* Verify actual backend state rather than trusting response messages.
* Record the exact starting state before each attempt.
* Reset the environment between attempts whenever possible.
* Treat two `200 OK` responses as an observation, not proof.
* Allow asynchronous processing to complete before concluding the result.
* Account for eventual consistency in distributed systems.
* Test one-time actions, limited resources, counters, and state transitions first.
* Compare sequential and concurrent behavior directly.
* Record how requests were synchronized.
* Record the reproduction rate of timing-sensitive findings.
* Test conflicting operations when the workflow makes them security-relevant.
* Consider lost-update problems as well as duplicate processing.
* Review idempotency controls for sensitive create or transaction operations.
* Understand that database transactions alone do not guarantee race safety.
* Look for optimistic locking indicators such as versions and ETags.
* Keep request volume minimal.
* Stop immediately if testing creates unexpected operational impact.
* Document the violated invariant clearly in the final report.

---

## Common Mistakes

* Calling every duplicate request a race condition.
* Skipping the sequential baseline.
* Sending excessive numbers of concurrent requests immediately.
* Testing real financial transactions without explicit authorization.
* Affecting scarce inventory or real bookings.
* Trusting response text without verifying state.
* Assuming two success responses mean two successful effects.
* Ignoring asynchronous processing.
* Ignoring eventual consistency.
* Failing to reset test state.
* Using different objects unintentionally between requests.
* Failing to define the expected maximum number of successful operations.
* Confusing replay vulnerabilities with concurrency vulnerabilities.
* Ignoring conflicting-operation races.
* Ignoring lost updates.
* Assuming HTTP/2 guarantees simultaneous execution.
* Assuming database transactions automatically prevent races.
* Failing to record synchronization technique.
* Failing to record reproduction rate.
* Reporting timing differences without an invariant violation.
* Testing at volumes that resemble denial-of-service activity.
* Failing to explain actual security or business impact.

---

## Practice Tasks

### Task 1 — Select an Authorized Environment

* Use:

  ```text
  Deliberately Vulnerable Lab

  Local Application

  Sandbox

  or

  Explicitly Authorized Target
  ```

---

### Task 2 — Identify Three Race Candidates

* Look for:

  ```text
  One-Time Action

  Limited Resource

  State Transition
  ```

---

### Task 3 — Define Invariants

* For each candidate, write:

  ```text
  What Must Remain True Under Concurrency?
  ```

---

### Task 4 — Choose One Safe Candidate

* Use a disposable object with no real-world financial or operational impact.

---

### Task 5 — Capture the Request

* Use Burp Proxy.

* Send the state-changing request to Repeater.

---

### Task 6 — Establish Sequential Baseline

* Send the operation normally.

* Repeat it sequentially.

* Record both results.

---

### Task 7 — Reset State

* Return to the original controlled condition.

---

### Task 8 — Prepare Two Requests

* Use identical logical operations.

* Do not start with a large request group.

---

### Task 9 — Send Concurrently

* Use the available Burp request-group mechanism where appropriate.

---

### Task 10 — Verify Final State

* Compare:

  ```text
  Before

  vs

  After
  ```

* Count actual effects.

---

### Task 11 — Repeat Three Times

* Reset the state between attempts.

* Record reproducibility.

---

### Task 12 — Analyze Controls

* Look for:

  ```text
  Idempotency Keys

  Version Fields

  ETags

  Unique Constraints

  Locking Behavior
  ```

* Where visible.

---

### Task 13 — Test One Conflicting Pair

* In a safe lab, test something like:

  ```text
  Approve

  vs

  Cancel
  ```

* Verify final state.

---

### Task 14 — Write a Finding

* Include:

  ```text
  Invariant

  Sequential Baseline

  Concurrent Test

  Final State

  Reproduction Rate

  Impact
  ```

---

## Interview Questions

1. What is concurrency?
2. What is a race condition?
3. What is TOCTOU?
4. What is a race window?
5. What types of shared state commonly create race conditions?
6. What is a concurrency invariant?
7. Why should you define an invariant before testing?
8. What is the difference between a race condition and a replay vulnerability?
9. Why is a sequential baseline essential?
10. What does it suggest if sequential duplicates fail but concurrent duplicates succeed?
11. Why should race testing begin with two requests?
12. What risks are created by high-volume concurrency testing?
13. How can Burp Repeater assist with race-condition testing?
14. Why does clicking Send quickly not guarantee synchronized requests?
15. How can HTTP/2 help concurrency testing?
16. Does HTTP/2 guarantee simultaneous server execution?
17. What is synchronized request delivery?
18. What is the purpose of single-packet synchronization conceptually?
19. Why must final backend state be verified?
20. Why do two success responses not necessarily prove duplicate processing?
21. How would you test a single-use coupon for a race condition?
22. How would you test a reward-claim race?
23. What is a balance or double-spend race?
24. What is a check-then-update vulnerability pattern?
25. What is atomicity?
26. How can database transactions help prevent races?
27. Why do database transactions not automatically make an application race-safe?
28. What is pessimistic locking?
29. What is optimistic locking?
30. How can version numbers prevent stale updates?
31. What is compare-and-swap?
32. What is a lost update?
33. How can lost updates create security impact?
34. What is a quota race?
35. How can inventory systems be affected by races?
36. Why are temporary reservations not automatically evidence of double booking?
37. How can one-time tokens be vulnerable to concurrency?
38. What is a workflow-state race?
39. What are conflicting operations?
40. What is idempotency?
41. What is an idempotency key?
42. What should happen when the same idempotency key is reused concurrently?
43. How can unique database constraints prevent duplicate effects?
44. How does asynchronous processing complicate race testing?
45. What is eventual consistency?
46. How do you avoid false positives caused by eventual consistency?
47. How should race-condition reproducibility be documented?
48. What evidence is required for a strong race-condition report?
49. What safety precautions should be used during concurrency testing?
50. Describe your complete methodology for testing race conditions using Burp Suite.

---

## Quick Revision

* Race condition:

  ```text
  Concurrent Operations

  +

  Shared State

  +

  Unsafe Timing

  →

  Invalid Outcome
  ```

* TOCTOU:

  ```text
  Check

  ↓

  Race Window

  ↓

  Use
  ```

* Strong signal:

  ```text
  Sequential Duplicate

  →

  Rejected

  Concurrent Duplicate

  →

  Multiple Real Effects
  ```

* Important:

  ```text
  Replay Vulnerability

  ≠

  Race Condition
  ```

* Candidate operations:

  ```text
  One-Time Actions

  Balances

  Limits

  Counters

  Inventory

  Rewards

  State Transitions
  ```

* Baseline:

  ```text
  Clean State

  ↓

  Request 1

  ↓

  Verify

  ↓

  Request 2

  ↓

  Verify Expected Rejection
  ```

* Concurrent test:

  ```text
  Reset

  ↓

  Prepare Two Requests

  ↓

  Synchronize

  ↓

  Send

  ↓

  Verify Final State
  ```

* Key rule:

  ```text
  Two Success Responses

  ≠

  Two Successful Effects
  ```

* Atomicity:

  ```text
  Check + Update

  →

  Protected as One Logical Operation
  ```

* Optimistic locking:

  ```text
  Read Version

  ↓

  Update Only If Version Still Matches
  ```

* Safety:

  ```text
  Controlled Accounts

  +

  Disposable Data

  +

  Minimal Requests

  +

  Explicit Authorization
  ```

* Professional workflow:

  ```text
  Identify Shared State

  ↓

  Define Invariant

  ↓

  Establish Sequential Baseline

  ↓

  Reset

  ↓

  Send Minimal Concurrent Requests

  ↓

  Verify Authoritative State

  ↓

  Reproduce

  ↓

  Validate Impact
  ```

---

## Key Takeaways

* Race conditions occur when concurrent operations interact with shared state in a way that violates security or business rules.
* TOCTOU vulnerabilities arise when state can change between a security-relevant check and the later use of that result.
* Race testing should begin by identifying shared or limited resources and defining an explicit concurrency invariant.
* Sequential replay vulnerabilities and race conditions are different: a strong race signal exists when sequential duplicates are correctly rejected but overlapping requests bypass that protection.
* Always establish a clean sequential baseline before testing concurrency.
* Start with the smallest practical number of concurrent requests, usually two.
* Burp Repeater can preserve exact requests and support controlled parallel-request experiments.
* Network timing makes true concurrency difficult, so synchronized-request techniques may be needed for narrow race windows.
* HTTP/2 can reduce some timing differences but does not guarantee simultaneous server-side execution.
* One-time actions, balances, counters, rewards, coupons, inventory, quotas, tokens, and workflow transitions are high-value race-condition candidates.
* Two successful HTTP responses do not prove two successful operations; authoritative backend state must be verified.
* Atomic operations, locking, version checks, compare-and-swap mechanisms, unique constraints, and idempotency controls can help preserve concurrency invariants.
* Database transactions alone do not automatically eliminate race conditions because isolation and application-level logic still matter.
* Lost updates are another concurrency problem and may affect security-sensitive counters, quotas, balances, or settings.
* Conflicting operations such as approve-versus-cancel can expose invalid workflow-state transitions.
* Asynchronous processing and eventual consistency can create misleading observations, so allow processing to settle before concluding that a vulnerability exists.
* Timing differences alone are not vulnerabilities; a security- or business-relevant invariant must actually be violated.
* Race-condition findings should document the sequential baseline, concurrent experiment, synchronization method, final state, actual number of effects, reproducibility, and impact.
* Concurrency testing can be operationally risky, so use controlled accounts, disposable objects, sandbox value, minimal request counts, and explicit authorization.
* The professional race-condition workflow is **identify shared state → understand the business rule → define the concurrency invariant → prepare controlled state → capture the legitimate request → establish sequential behavior → reset → send minimal synchronized concurrent requests → record responses → verify authoritative final state → reproduce from clean state → validate impact → document evidence**.
