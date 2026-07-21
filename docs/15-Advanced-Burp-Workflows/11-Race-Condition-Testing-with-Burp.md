# Race Condition Testing with Burp

> **Module 15 — Advanced Burp Workflows**

> **Progress**

```text
████████████░░░ 12 / 15 Files
```

---

## Overview

* Race conditions occur when an application's security or business logic depends on operations happening in a specific order, but multiple requests can execute concurrently and interfere with that assumption.

* A typical application may perform:

  ```text
  Check State

  ↓

  Perform Action

  ↓

  Update State
  ```

* If two requests reach the server at nearly the same time:

  ```text
  Request A ───► Check State = Valid

  Request B ───► Check State = Valid

  Request A ───► Perform Action

  Request B ───► Perform Action

  Request A ───► Update State

  Request B ───► Update State
  ```

* Both requests may pass the same validation before either request updates the underlying state.

* This can create vulnerabilities involving:

  * Multiple coupon redemption.
  * Duplicate reward claims.
  * Reusing one-time tokens.
  * Exceeding purchase limits.
  * Double spending.
  * Duplicate withdrawals.
  * Multiple password-reset actions.
  * Repeated invitation acceptance.
  * Duplicate voting.
  * Inventory manipulation.
  * Conflicting account changes.
  * State-machine bypasses.

* Burp Suite provides useful capabilities for investigating race conditions, particularly through:

  * Repeater.
  * Request grouping.
  * Parallel request sending.
  * HTTP/2 multiplexing.
  * Intruder in selected scenarios.
  * Extensions designed for concurrency testing.
  * Logger and Comparer for analysis.

* The professional methodology is:

  ```text
  Understand Workflow

  +

  Identify Shared State

  +

  Define Expected Invariant

  +

  Establish Baseline

  +

  Synchronize Requests

  +

  Observe Final State

  +

  Reproduce Safely

  +

  Prove Security Impact
  ```

---

## Learning Objectives

* By the end of this lesson, you will be able to:

  * Explain what a race condition is.
  * Understand the check-then-act problem.
  * Identify workflows that may contain race conditions.
  * Distinguish race conditions from ordinary replay flaws.
  * Identify shared server-side state.
  * Define security invariants before testing.
  * Establish a reliable single-request baseline.
  * Use Burp Repeater for concurrent request testing.
  * Understand parallel request sending.
  * Understand last-byte synchronization.
  * Understand single-packet attack concepts.
  * Explain why HTTP/2 can improve synchronization.
  * Test one-time actions safely.
  * Investigate limit-overrun vulnerabilities.
  * Analyze multi-endpoint races.
  * Investigate partial-construction race conditions.
  * Separate timing noise from reproducible vulnerabilities.
  * Validate final application state.
  * Build a structured race-condition testing methodology.
  * Preserve clear evidence for race-condition findings.

---

## What Is a Race Condition?

* A race condition occurs when application behavior depends on the timing or ordering of concurrent operations.

* Simplified example:

  ```text
  Account Balance = ₹1,000

  Withdrawal A = ₹800

  Withdrawal B = ₹800
  ```

* Correct behavior should prevent both withdrawals if only ₹1,000 is available.

* Vulnerable logic might behave like:

  ```text
  Request A:

  Check Balance → ₹1,000

  ↓

  Enough Funds
  ```

  ```text
  Request B:

  Check Balance → ₹1,000

  ↓

  Enough Funds
  ```

* Both requests may then proceed before the balance is safely updated.

---

## The Check-Then-Act Pattern

* Many race conditions involve:

  ```text
  Check

  ↓

  Act
  ```

* Example:

  ```text
  Is Coupon Unused?

  ↓ Yes

  Apply Discount

  ↓

  Mark Coupon Used
  ```

* Concurrent requests may create:

  ```text
  Request A → Coupon Unused

  Request B → Coupon Unused

  ↓

  Both Apply Coupon

  ↓

  Coupon Marked Used Too Late
  ```

---

## Time-of-Check to Time-of-Use

* A related concept is:

  ```text
  TOCTOU
  ```

* Meaning:

  ```text
  Time of Check

  to

  Time of Use
  ```

* The application validates a condition at one moment and uses that result later.

* If state changes between those moments, the earlier validation may no longer be valid.

---

## Race Window

* The **race window** is the period during which concurrent operations can interfere.

* Example:

  ```text
  Check Coupon

  │
  │  ← Race Window
  │
  ▼

  Mark Coupon Used
  ```

* A smaller race window requires more precise synchronization.

---

## Why Sequential Testing May Miss Race Conditions

* Suppose you send:

  ```text
  Request A

  ↓ Complete

  Request B

  ↓ Complete
  ```

* The application may correctly reject Request B because Request A already changed the state.

* Result:

  ```text
  Request A → Success

  Request B → Already Used
  ```

* But concurrent requests may produce:

  ```text
  Request A ─────►

  Request B ─────►

  ↓ Nearly Simultaneous

  Both → Success
  ```

* Therefore:

  ```text
  Sequential Replay

  ≠

  Race Condition Testing
  ```

---

## Race Condition vs Replay Vulnerability

* These are related but different.

### Replay Vulnerability

* A valid request can simply be sent again later.

  ```text
  Request

  ↓ Success

  Wait

  ↓

  Same Request

  ↓ Success Again
  ```

### Race Condition

* Requests must overlap within a timing window.

  ```text
  Request A ─────►

  Request B ─────►

  Request C ─────►

  ↓

  Concurrent Processing

  ↓

  Unexpected Result
  ```

* Test sequential replay first.

* If sequential replay already succeeds, concurrency may not be necessary to explain the vulnerability.

---

## Why Race Conditions Matter

* Race conditions can break security assumptions such as:

  ```text
  One-Time Means Once

  Balance Cannot Go Below Zero

  Limit Cannot Be Exceeded

  One Vote Per User

  One Reward Per Account

  Token Can Be Used Once

  State Must Progress Sequentially
  ```

* These are **invariants** the application is expected to preserve.

---

## Define the Security Invariant

* Before testing, write the rule that should always remain true.

* Examples:

  ```text
  Coupon can be redeemed only once.
  ```

  ```text
  Account cannot spend more than available balance.
  ```

  ```text
  User can claim reward only once.
  ```

  ```text
  Only one username can own a unique identifier.
  ```

  ```text
  Stock cannot fall below zero.
  ```

* Then test whether concurrency can violate that invariant.

---

## Identify Shared State

* Race conditions require operations that interact with shared or related state.

* Examples:

  ```text
  Account Balance

  Coupon Status

  Reward Claim Status

  Inventory Count

  Token Validity

  Username Availability

  Vote Count

  Order Status

  Credit Limit

  Invitation Status
  ```

* Ask:

  ```text
  What server-side value is being checked?

  What request changes it?

  Can two operations observe the same old state?
  ```

---

## High-Value Race Condition Targets

* Look closely at workflows involving:

  ```text
  Payments

  Transfers

  Withdrawals

  Coupons

  Gift Cards

  Loyalty Points

  Rewards

  Inventory

  Limited Purchases

  Voting

  Invitations

  Password Reset

  Email Verification

  MFA

  Username Changes

  Account Creation

  Role Changes

  One-Time Tokens
  ```

---

## Workflow Mapping

* Before sending concurrent requests, map the normal workflow.

* Example:

  ```text
  Add Product

  ↓

  Apply Coupon

  ↓

  Checkout

  ↓

  Coupon Marked Used

  ↓

  Order Created
  ```

* Ask:

  ```text
  When is the coupon checked?

  When is it marked used?

  Which request performs each action?

  Is the discount stored before checkout?

  Does another endpoint finalize the state?
  ```

---

## Build a State Machine

* Example:

  ```text
  Coupon:

  UNUSED

  ↓ Apply

  APPLIED

  ↓ Checkout

  REDEEMED
  ```

* Race testing may investigate whether concurrent requests produce:

  ```text
  UNUSED

  ↓

  APPLIED TO MULTIPLE ORDERS
  ```

* State modeling makes race candidates easier to identify.

---

## Establish a Single-Request Baseline

* Never begin with concurrency.

* First send one normal request.

* Record:

  ```text
  Request

  Response

  State Before

  State After

  Side Effect

  Database-Visible Result

  UI Result
  ```

* Confirm exactly what one successful operation does.

---

## Establish a Sequential Baseline

* Next, send the action twice sequentially.

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

* This confirms that:

  ```text
  Normal Sequential Protection Works
  ```

* Then test concurrency.

---

## Baseline Testing Sequence

* Use:

  ```text
  Single Request

  ↓

  Confirm Expected Result

  ↓

  Sequential Duplicate

  ↓

  Confirm Protection

  ↓

  Reset State

  ↓

  Concurrent Requests
  ```

* This produces a strong control comparison.

---

## Burp Repeater for Race Testing

* Repeater is useful because it allows controlled request duplication and grouping.

* General workflow:

  ```text
  Capture Candidate Request

  ↓

  Send to Repeater

  ↓

  Confirm Baseline

  ↓

  Duplicate / Group Requests

  ↓

  Configure Same Test State

  ↓

  Send Concurrently

  ↓

  Compare Responses

  ↓

  Verify Final State
  ```

---

## Request Groups

* Related requests can be organized into a group for coordinated testing.

* Conceptually:

  ```text
  Group: Coupon Race

  ├── Request 1
  ├── Request 2
  ├── Request 3
  ├── Request 4
  └── Request 5
  ```

* Requests may be:

  ```text
  Identical
  ```

* Or represent different operations:

  ```text
  Request A → Change Email

  Request B → Verify Email
  ```

---

## Same-Endpoint Race

* The simplest race candidate uses identical requests.

* Example:

  ```http
  POST /api/redeem-coupon
  ```

* Send several equivalent requests concurrently.

* Expected invariant:

  ```text
  Coupon Redeemed Once
  ```

* Vulnerable result:

  ```text
  Coupon Applied Multiple Times
  ```

---

## Multi-Endpoint Race

* Some race conditions involve different requests.

* Example:

  ```text
  Request A:

  POST /change-email
  ```

  ```text
  Request B:

  POST /confirm-email
  ```

* Or:

  ```text
  Request A:

  POST /withdraw
  ```

  ```text
  Request B:

  POST /transfer
  ```

* These may compete over shared state.

---

## Parallel Request Sending

* The basic idea is:

  ```text
  Prepare Multiple Requests

  ↓

  Release Them Together

  ↓

  Minimize Timing Difference

  ↓

  Observe Results
  ```

* Exact synchronization matters because the vulnerable race window may be very small.

---

## Why Normal Parallel Sending Is Imperfect

* Even if requests are sent at nearly the same time, differences can arise from:

  ```text
  TCP Scheduling

  TLS Processing

  Network Latency

  Server Scheduling

  Connection Establishment

  Proxy Processing
  ```

* This can make one request arrive significantly earlier than another.

---

## Last-Byte Synchronization

* A classic synchronization concept is to send almost all of multiple requests, pause before the final portion, and then release the remaining bytes together.

* Conceptually:

  ```text
  Request A → Almost Complete ─┐

  Request B → Almost Complete ─┼─► Release Final Bytes Together

  Request C → Almost Complete ─┘
  ```

* This attempts to reduce network timing differences.

---

## Why Last-Byte Synchronization Helps

* Without synchronization:

  ```text
  Request A → Arrives

  20 ms later

  Request B → Arrives
  ```

* With tighter synchronization:

  ```text
  Request A ─┐

  Request B ─┼──► Server Processes Nearly Together

  Request C ─┘
  ```

* This increases the chance that multiple requests enter the race window.

---

## Single-Packet Attack Concept

* HTTP/2 enables a powerful synchronization concept often called a:

  ```text
  Single-Packet Attack
  ```

* The idea is to prepare multiple HTTP/2 requests and arrange for their final parts to be transmitted together as tightly as possible.

* Conceptually:

  ```text
  HTTP/2 Connection

  ├── Stream 1 → Request A almost complete
  ├── Stream 3 → Request B almost complete
  ├── Stream 5 → Request C almost complete
  └── Stream 7 → Request D almost complete

               ↓

         Final parts released

               ↓

        One tightly timed send
  ```

* This reduces network jitter between requests.

---

## Why HTTP/2 Helps Race Testing

* HTTP/2 supports:

  ```text
  One Connection

  +

  Multiple Streams

  +

  Multiplexing
  ```

* This avoids needing a separate connection for every request.

* It can reduce variation caused by:

  ```text
  TCP Handshakes

  TLS Handshakes

  Separate Connection Scheduling
  ```

---

## HTTP/1.1 vs HTTP/2 Synchronization

| Characteristic         | HTTP/1.1                   | HTTP/2             |
| ---------------------- | -------------------------- | ------------------ |
| Concurrent requests    | Often separate connections | Multiple streams   |
| Connection overhead    | Higher                     | Lower              |
| Synchronization        | More difficult             | Often tighter      |
| Multiplexing           | No native equivalent       | Native             |
| Race-testing precision | Variable                   | Potentially better |

---

## Burp Repeater Send Modes

* Depending on the current Burp version and request protocol, Repeater may provide options for sending grouped requests in coordinated ways.

* Conceptually, you may use:

  ```text
  Send Group Sequentially

  Send Group in Parallel

  Send Group With Synchronization Technique
  ```

* Exact labels and availability can vary by Burp version and protocol.

* Understand what each mode does before using it.

---

## Choosing the Number of Requests

* More requests do not automatically mean a better test.

* Start conservatively.

* Example:

  ```text
  2 Requests

  ↓

  3–5 Requests

  ↓

  Increase Only if Justified
  ```

* Avoid:

  ```text
  Hundreds or Thousands of Requests
  ```

* Unless explicitly authorized and operationally safe.

---

## State Reset Is Critical

* Race testing often changes server-side state.

* Example:

  ```text
  Coupon = UNUSED

  ↓ Test

  Coupon = USED
  ```

* You cannot reliably repeat the same test unless the state is reset.

* Use:

  ```text
  New Coupon

  New Test Account

  New Order

  Reset Lab

  New One-Time Token
  ```

---

## One-Time Token Races

* Candidate workflow:

  ```text
  Token Issued

  ↓

  Token Validated

  ↓

  Sensitive Action

  ↓

  Token Invalidated
  ```

* Potential race:

  ```text
  Request A → Token Valid

  Request B → Token Valid

  ↓

  Both Use Same Token
  ```

* Expected invariant:

  ```text
  Token Produces One Successful Sensitive Action
  ```

---

## Password Reset Race Example

* Conceptual workflow:

  ```text
  Reset Token

  ↓

  Submit New Password

  ↓

  Validate Token

  ↓

  Change Password

  ↓

  Invalidate Token
  ```

* Test whether concurrent submissions using the same token can cause multiple unintended state changes.

* Use only dedicated test accounts.

---

## Coupon Redemption Race

* Example:

  ```text
  Coupon Value = ₹500

  Allowed Uses = 1
  ```

* Sequential baseline:

  ```text
  Request A → Success

  Request B → Coupon Already Used
  ```

* Concurrent test:

  ```text
  Request A ─┐

  Request B ─┼──► Both Reach Validation

  Request C ─┘
  ```

* Vulnerable outcome:

  ```text
  Multiple Discounts Applied
  ```

---

## Reward Claim Race

* Example invariant:

  ```text
  User May Claim Welcome Reward Once
  ```

* Vulnerable sequence:

  ```text
  Request A → Reward Not Claimed

  Request B → Reward Not Claimed

  ↓

  Both Credit Reward

  ↓

  Claimed Flag Updated
  ```

---

## Limit-Overrun Race Conditions

* These occur when concurrent requests exceed a limit that works correctly under sequential use.

* Examples:

  ```text
  Purchase Limit = 5

  Withdrawal Limit = ₹10,000

  Maximum Invitations = 10

  Inventory = 1

  Vote Limit = 1
  ```

* Concurrent requests may each observe the old limit state.

---

## Limit-Overrun Example

* Suppose:

  ```text
  Remaining Stock = 1
  ```

* Two requests:

  ```text
  Request A → Stock > 0

  Request B → Stock > 0
  ```

* Both may proceed.

* Final result:

  ```text
  Two Purchases

  for

  One Item
  ```

---

## Financial Race Conditions

* Financial workflows require extreme caution.

* Candidate shared state:

  ```text
  Balance

  Credit

  Refund Status

  Payment Status

  Gift Card Value

  Reward Points
  ```

* Never perform uncontrolled financial race tests against real production accounts.

* Use:

  ```text
  Dedicated Sandbox

  Test Funds

  Explicit Authorization

  Minimal Transaction Values
  ```

---

## Double-Spend Pattern

* Conceptual example:

  ```text
  Balance = 100
  ```

* Two concurrent operations:

  ```text
  Spend 80

  Spend 80
  ```

* Both check:

  ```text
  Balance >= 80
  ```

* Before either deducts the amount.

* Vulnerable outcome:

  ```text
  Total Spend = 160
  ```

---

## Inventory Races

* Candidate invariant:

  ```text
  Sold Quantity

  ≤

  Available Inventory
  ```

* Race testing may target:

  ```text
  Last Item

  Limited Edition

  Per-User Quantity Limit

  Reservation Window
  ```

* Verify the final inventory and order state, not only HTTP responses.

---

## Voting and Rating Races

* Example invariant:

  ```text
  One Vote Per Account
  ```

* Sequential:

  ```text
  Vote 1 → Accepted

  Vote 2 → Rejected
  ```

* Concurrent:

  ```text
  Vote A ─┐

  Vote B ─┼──► Validation

  Vote C ─┘
  ```

* Check the final recorded count.

---

## Invitation Races

* Invitation workflows may contain:

  ```text
  Invite Created

  ↓

  Invite Accepted

  ↓

  Invite Invalidated
  ```

* Potential questions:

  ```text
  Can invite be accepted twice?

  Can multiple accounts accept same invite?

  Can role assignment happen more than once?

  Can acceptance race with revocation?
  ```

---

## Race Between Accept and Revoke

* Example:

  ```text
  Request A:

  Accept Invitation
  ```

  ```text
  Request B:

  Revoke Invitation
  ```

* Expected invariant:

  ```text
  Revoked Invitation Cannot Grant Access
  ```

* Concurrent processing may expose inconsistent state transitions.

---

## Account Registration Races

* Candidate invariant:

  ```text
  Username or Email Must Be Unique
  ```

* Vulnerable pattern:

  ```text
  Request A → Identifier Available

  Request B → Identifier Available

  ↓

  Both Create Account
  ```

* Modern databases often enforce uniqueness, but application-level checks may still be flawed.

---

## MFA and Verification Races

* Candidate workflows include:

  ```text
  OTP Validation

  Email Verification

  MFA Enrollment

  Recovery Code Use

  Device Approval
  ```

* Ask:

  ```text
  Is token invalidation atomic?

  Can same code trigger multiple actions?

  Can verification race with account changes?
  ```

---

## Partial Construction Race Conditions

* Some objects are created in multiple stages.

* Example:

  ```text
  Create User Record

  ↓

  Apply Default Role

  ↓

  Add Security Restrictions

  ↓

  Finalize Account
  ```

* During a short interval, the object may exist in an incomplete state.

* Another request may interact with it before initialization finishes.

---

## Object Construction Window

* Conceptually:

  ```text
  Object Created

  ↓

  [Temporary Incomplete State]

  ↓

  Security Properties Applied
  ```

* A concurrent request may target:

  ```text
  Temporary Incomplete State
  ```

* This is sometimes called a partial-construction race.

---

## Detecting Partial Construction

* Look for multi-step server behavior such as:

  ```text
  Create

  ↓

  Configure

  ↓

  Assign Permissions

  ↓

  Finalize
  ```

* Test whether another endpoint can interact with the object during creation.

* These tests often require different concurrent requests.

---

## Multi-Endpoint Race Workflow

* Example:

  ```text
  Request A:

  POST /create-object
  ```

* Concurrently:

  ```text
  Request B:

  POST /modify-object
  ```

* Or:

  ```text
  Request A:

  POST /change-role
  ```

  ```text
  Request B:

  POST /perform-privileged-action
  ```

* The requests race over shared state.

---

## State Machine Races

* Consider:

  ```text
  PENDING

  ↓

  APPROVED

  ↓

  COMPLETED
  ```

* Concurrent requests may attempt:

  ```text
  APPROVE

  +

  CANCEL
  ```

* Or:

  ```text
  COMPLETE

  +

  REFUND
  ```

* Resulting state may violate expected transitions.

---

## Conflicting Operations

* High-value combinations include:

  ```text
  Accept vs Revoke

  Buy vs Cancel

  Withdraw vs Transfer

  Approve vs Delete

  Redeem vs Refund

  Verify vs Change

  Login vs Disable Account

  Role Change vs Privileged Action
  ```

* Think beyond identical-request races.

---

## Hidden Multi-Step Sequences

* One visible UI action may trigger several HTTP requests.

* Example:

  ```text
  Click "Complete Purchase"

  ↓

  POST /reserve

  ↓

  POST /charge

  ↓

  POST /confirm
  ```

* Use Proxy history or Logger to identify all steps.

* The race may exist between two different stages.

---

## Race Window Widening

* Sometimes the vulnerable window is too small for reliable detection.

* In controlled labs, certain inputs may cause one processing stage to take longer.

* Conceptually:

  ```text
  Check

  ↓

  Slow Operation

  ↓

  Update
  ```

* A longer interval may make concurrent interaction easier.

* Avoid intentionally creating resource exhaustion or denial-of-service conditions.

---

## Server-Side Delays

* Potential natural delays may come from:

  ```text
  External API Calls

  Database Queries

  File Processing

  Email Delivery

  Payment Processing

  Complex Validation
  ```

* These may widen race windows.

* Observe normal behavior before designing a test.

---

## Connection Warm-Up

* Timing can differ when requests require:

  ```text
  DNS Resolution

  TCP Connection

  TLS Handshake

  Authentication Setup
  ```

* Reusing an established connection can reduce this variability.

* HTTP/2 is particularly useful because multiple streams can share one warm connection.

---

## Network Jitter

* Network variability can cause false negatives.

* Example:

  ```text
  Request A Sent at T0

  Request B Sent at T0

  but

  Request A Arrives at T0 + 10 ms

  Request B Arrives at T0 + 70 ms
  ```

* Better synchronization reduces this gap.

---

## Response Success Does Not Prove the Race

* Suppose five requests return:

  ```text
  200 OK
  ```

* That does not necessarily mean five actions occurred.

* The server may return a generic success response while performing only one state change.

* Always verify:

  ```text
  Final State
  ```

---

## Final-State Verification

* After a race attempt, check:

  ```text
  Account Balance

  Number of Orders

  Coupon Usage

  Reward Balance

  Inventory

  Vote Count

  Token Status

  Account Permissions

  Database-Visible Application State
  ```

* Security impact exists in the resulting state, not merely the response codes.

---

## Response Comparison

* Compare concurrent responses for:

  ```text
  Status

  Length

  Body

  Error Message

  Transaction ID

  Object ID

  Timestamp

  State Value
  ```

* Different responses can reveal which requests reached which processing stage.

---

## Use Comparer

* Burp Comparer can help analyze subtle differences.

* Example:

  ```text
  Successful Response A

  vs

  Successful Response B
  ```

* Look for:

  ```text
  Different Transaction IDs

  Different Order IDs

  Different Balance Values

  Different State Messages
  ```

---

## Use Logger

* Logger can help reconstruct:

  ```text
  Request Timing

  Related Requests

  Authentication Activity

  Follow-Up State Checks

  Tool-Generated Traffic
  ```

* This is especially useful when a race test involves several endpoints.

---

## Use Intruder Carefully

* Intruder can send repeated requests, but repeated high-speed traffic is not automatically synchronized race testing.

* Intruder may be useful for:

  ```text
  Identifying Timing Patterns

  Repeating Candidate Actions

  Testing Small Controlled Variations
  ```

* For precise synchronization, purpose-built Repeater race workflows may be more appropriate.

---

## Turbo Intruder Context

* Specialized extensions such as Turbo Intruder are commonly associated with advanced race-condition and request-timing research.

* They can provide:

  ```text
  High-Control Request Scheduling

  Concurrency

  Connection Management

  Advanced Synchronization Techniques
  ```

* Before using any extension:

  ```text
  Understand Generated Traffic

  ↓

  Practice in a Lab

  ↓

  Confirm Scope

  ↓

  Start With Minimal Request Counts
  ```

---

## Avoid Blind High-Volume Testing

* A race condition is not:

  ```text
  Send 10,000 Requests

  and

  Hope Something Breaks
  ```

* Professional testing uses:

  ```text
  Workflow Understanding

  +

  Precise Hypothesis

  +

  Controlled Concurrency

  +

  Final-State Validation
  ```

---

## Single-Endpoint Race Methodology

* Use:

  ```text
  Identify One-Time / Limited Action

  ↓

  Capture Request

  ↓

  Confirm Single Success

  ↓

  Confirm Sequential Duplicate Fails

  ↓

  Reset State

  ↓

  Duplicate Request

  ↓

  Send Concurrently

  ↓

  Compare Responses

  ↓

  Verify Final State

  ↓

  Repeat With Fresh State
  ```

---

## Multi-Endpoint Race Methodology

* Use:

  ```text
  Map State Machine

  ↓

  Identify Two Conflicting Operations

  ↓

  Capture Both Requests

  ↓

  Understand Normal Order

  ↓

  Establish Baseline

  ↓

  Reset State

  ↓

  Send Requests Concurrently

  ↓

  Verify Resulting State

  ↓

  Repeat
  ```

---

## Race Condition Hypothesis Template

* Write:

  ```text
  Invariant:

  Shared State:

  Request A:

  Request B:

  Normal Sequential Result:

  Suspected Race Window:

  Expected Vulnerable Result:

  Final-State Verification Method:
  ```

* Example:

  ```text
  Invariant:

  Coupon may be redeemed once.

  Shared State:

  coupon.used

  Request A:

  Redeem coupon.

  Request B:

  Redeem same coupon.

  Normal Sequential Result:

  Second request rejected.

  Suspected Race Window:

  Between validation and used-state update.

  Expected Vulnerable Result:

  Both requests apply discount.

  Verification:

  Confirm two discounted transactions.
  ```

---

## Race Condition Testing Workflow

* Use:

  ```text
  Map Workflow

  ↓

  Identify Shared State

  ↓

  Define Invariant

  ↓

  Identify Candidate Requests

  ↓

  Capture Baseline

  ↓

  Test Sequentially

  ↓

  Reset State

  ↓

  Prepare Concurrent Requests

  ↓

  Synchronize

  ↓

  Send

  ↓

  Compare Responses

  ↓

  Verify Final State

  ↓

  Repeat With Fresh State

  ↓

  Minimize Proof

  ↓

  Preserve Evidence
  ```

---

## Race Condition Attack Surface Map

* Document:

  ```text
  Feature:

  User:

  Role:

  Shared State:

  Security Invariant:

  Request A:

  Request B:

  Additional Requests:

  One-Time Token?:

  Limit?:

  Balance?:

  Inventory?:

  State Transition?:

  Sequential Behavior:

  Concurrent Behavior:

  Synchronization Method:

  Protocol:

  Final-State Check:

  Side Effects:

  Reset Method:
  ```

---

## Race Condition Investigation Card

* Record:

  ```text
  Investigation:

  Feature:

  User:

  Role:

  Account:

  Invariant:

  Shared State:

  Initial State:

  Request A:

  Request B:

  Request Count:

  Protocol:

  Sequential Baseline:

  Synchronization Method:

  Concurrent Responses:

  Final State:

  Expected State:

  Actual State:

  Security Impact:

  Reproduced?:

  Reproduction Count:

  State Reset Method:

  Evidence Preserved?:

  Follow-Up:
  ```

---

## Race Condition Testing Checklist

* Verify:

  ```text
  [ ] Workflow mapped

  [ ] Shared state identified

  [ ] Security invariant defined

  [ ] Candidate request identified

  [ ] Single-request baseline captured

  [ ] Sequential duplicate tested

  [ ] Normal protection confirmed

  [ ] State reset method available

  [ ] Request side effects understood

  [ ] Concurrency method selected

  [ ] Request count kept minimal

  [ ] Authentication context confirmed

  [ ] HTTP protocol identified

  [ ] Requests synchronized appropriately

  [ ] Responses compared

  [ ] Final state verified

  [ ] Test repeated with fresh state

  [ ] Replay flaw ruled out where relevant

  [ ] Network noise considered

  [ ] Multi-endpoint races considered

  [ ] Partial-construction races considered

  [ ] State-machine conflicts considered

  [ ] Production impact minimized

  [ ] Finding reproduced cleanly

  [ ] Evidence preserved
  ```

---

## Testing Strategy Matrix

| Scenario                 | Primary Race Test                 |
| ------------------------ | --------------------------------- |
| One-time coupon          | Concurrent redemption             |
| Reward claim             | Concurrent claim requests         |
| Limited inventory        | Concurrent purchase               |
| Balance operation        | Concurrent spending actions       |
| One-time token           | Concurrent token use              |
| Voting                   | Concurrent vote submissions       |
| Invitation               | Concurrent acceptance             |
| Accept vs revoke         | Multi-endpoint race               |
| Account creation         | Concurrent uniqueness check       |
| Object initialization    | Partial-construction race         |
| Multi-step state machine | Conflicting transition race       |
| Password reset           | Concurrent token consumption      |
| Rate/quantity limit      | Limit-overrun race                |
| HTTP/2 endpoint          | Tight multiplexed synchronization |

---

## Troubleshooting — Race Never Triggers

* Check:

  ```text
  Is There Actually Shared State?

  ↓

  Does Sequential Protection Work?

  ↓

  Is State Reset Correctly?

  ↓

  Are Requests Truly Concurrent?

  ↓

  Is Synchronization Tight Enough?

  ↓

  Is Race Window Extremely Small?

  ↓

  Is Application Properly Atomic?
  ```

* A failed race attempt does not prove the application is vulnerable or secure.

---

## Troubleshooting — All Requests Succeed

* Determine:

  ```text
  Is This Normal Replay Behavior?

  ↓

  Does Each Success Produce a Real Side Effect?

  ↓

  Was the Action Supposed to Be Limited?

  ↓

  Does Final State Violate the Invariant?
  ```

* If sequential duplicates also succeed, it may be a replay or business-logic issue rather than a race condition.

---

## Troubleshooting — Responses Differ but State Is Correct

* Example:

  ```text
  Response A → 200

  Response B → 200
  ```

* But:

  ```text
  Only One Reward Added
  ```

* This may indicate:

  ```text
  Misleading Response Handling

  but not necessarily

  Exploitable Race Condition
  ```

* Focus on security impact.

---

## Troubleshooting — Results Are Inconsistent

* Check:

  ```text
  Network Jitter

  Load Balancing

  Connection Reuse

  Server Load

  State Reset

  Cache

  Authentication

  Background Jobs

  Asynchronous Processing
  ```

* Repeat using controlled fresh state.

---

## Troubleshooting — One Request Always Wins

* Possible causes:

  ```text
  Requests Not Synchronized

  Different Connections

  TLS / Network Delay

  Server Serialization

  Database Locking

  Application Mutex

  Queue Processing
  ```

* Improve synchronization only within safe testing limits.

---

## Troubleshooting — HTTP/2 Test Behaves Sequentially

* Check:

  ```text
  Is HTTP/2 Actually Negotiated?

  ↓

  Are Requests Sharing One Connection?

  ↓

  Is Tool Using Parallel Streams?

  ↓

  Is Server Serializing Operations Internally?
  ```

---

## Troubleshooting — State Cannot Be Reset

* Do not repeatedly damage production state.

* Use:

  ```text
  New Test Account

  New Object

  New Coupon

  New Token

  Lab Reset

  Administrative Test Reset
  ```

* If safe reset is impossible, reconsider the test.

---

## Troubleshooting — Unexpected Financial or Destructive Effect

* Stop further testing.

* Preserve:

  ```text
  Requests

  Responses

  Timestamps

  Transaction IDs

  Final State
  ```

* Follow the engagement's reporting and escalation process.

* Do not continue multiplying the impact merely to strengthen proof.

---

## Avoiding False Positives

* Apparent race behavior may result from:

  ```text
  UI Delay

  Cache

  Asynchronous Updates

  Duplicate Response Messages

  Eventual Consistency

  Retry Logic

  Background Jobs

  Network Timing

  Stale Client State
  ```

* Verify authoritative server-side state wherever possible.

---

## Eventual Consistency

* Some distributed systems intentionally update state asynchronously.

* Example:

  ```text
  Action Accepted

  ↓

  Queue

  ↓

  Background Processing

  ↓

  Final State Updated Later
  ```

* Temporary inconsistent UI values do not automatically prove a race vulnerability.

* Wait for the system to settle before evaluating final state.

---

## Idempotency

* An operation is **idempotent** when repeating it does not create additional unintended effects.

* Conceptual example:

  ```text
  Set Status = Active

  repeated several times

  →

  Still Active
  ```

* Compare with:

  ```text
  Add ₹500 Credit

  repeated twice

  →

  ₹1,000 Added
  ```

* Security-sensitive actions often require idempotency or equivalent duplicate protection.

---

## Idempotency Keys

* APIs may use:

  ```text
  Idempotency-Key
  ```

* To prevent duplicate transaction processing.

* Test questions include:

  ```text
  Is key required?

  Is same key rejected or deduplicated?

  Is key scoped to user?

  Is key scoped to operation?

  Can concurrent requests bypass deduplication?
  ```

* Use only safe test transactions.

---

## Database-Level Protection

* Secure implementations may rely on:

  ```text
  Transactions

  Unique Constraints

  Row Locks

  Atomic Updates

  Compare-and-Swap

  Optimistic Locking

  Idempotency Controls
  ```

* External testing generally cannot prove the exact internal mechanism.

* Judge security based on observable behavior.

---

## Atomic Operations

* Secure logic should make critical state checks and updates atomic where necessary.

* Vulnerable pattern:

  ```text
  Read State

  ↓

  Validate

  ↓

  Separate Update
  ```

* Safer conceptual pattern:

  ```text
  Atomically:

  Validate Current State

  +

  Update State
  ```

---

## Evidence Collection

* Preserve:

  ```text
  Security Invariant

  Initial State

  Single-Request Baseline

  Sequential Duplicate Result

  Concurrent Requests

  Synchronization Method

  Responses

  Final State

  Reproduction Result

  User / Account

  Object IDs

  Transaction IDs

  Timestamps
  ```

* Race-condition reports require more context than a single request-response pair.

---

## Strong Race Condition Evidence

* Example:

  ```text
  Initial State:

  Reward Balance = 0

  Claim Status = Unclaimed
  ```

* Sequential control:

  ```text
  Request 1 → Reward +100

  Request 2 → Already Claimed
  ```

* Fresh-state concurrent test:

  ```text
  Request A ─┐

  Request B ─┼──► Concurrent

  Request C ─┘
  ```

* Final state:

  ```text
  Reward Balance = 300

  Claim Status = Claimed
  ```

* This demonstrates:

  ```text
  Expected Invariant

  +

  Sequential Protection

  +

  Concurrent Bypass

  +

  Measurable Impact
  ```

---

## Minimal Proof of Concept

* Once a race is confirmed, reduce the proof.

* Example:

  ```text
  Initial Discovery:

  10 Concurrent Requests
  ```

* But if reproducible with:

  ```text
  2 Concurrent Requests
  ```

* Prefer the minimal version.

* This reduces impact and improves clarity.

---

## Clean Reproduction

* Before reporting:

  ```text
  Create Fresh State

  ↓

  Confirm Identity

  ↓

  Capture Baseline

  ↓

  Confirm Sequential Protection

  ↓

  Reset State

  ↓

  Send Minimal Concurrent Set

  ↓

  Verify Final State

  ↓

  Repeat Once if Safe

  ↓

  Preserve Evidence
  ```

---

## Practical Exercise

* Use a deliberately vulnerable race-condition lab or another system you are explicitly authorized to test.

### Task 1 — Map the Workflow

* Identify one limited or one-time action.

* Record:

  ```text
  Feature:

  Action:

  Request:

  Shared State:

  Expected Limit:
  ```

---

### Task 2 — Define the Invariant

* Write one sentence.

* Example:

  ```text
  This coupon can reduce the order price only once.
  ```

* Record:

  ```text
  Invariant:
  ```

---

### Task 3 — Establish Single Baseline

* Perform the action once.

* Record:

  ```text
  Initial State:

  Response:

  Final State:
  ```

---

### Task 4 — Test Sequential Duplicate

* Repeat the action normally.

* Record:

  ```text
  First Result:

  Second Result:

  Protection Working?:
  ```

---

### Task 5 — Reset State

* Use:

  ```text
  New Account

  New Token

  New Coupon

  or

  Lab Reset
  ```

* Record the fresh initial state.

---

### Task 6 — Prepare Concurrent Requests

* Add a minimal number of equivalent requests to a Repeater group.

* Example:

  ```text
  Request 1

  Request 2

  Request 3
  ```

* Confirm they use the same intended test state.

---

### Task 7 — Send Concurrently

* Use the appropriate safe parallel or synchronization method available in your Burp setup.

* Record:

  ```text
  Protocol:

  Request Count:

  Send Method:

  Response Statuses:
  ```

---

### Task 8 — Verify Final State

* Do not rely only on responses.

* Record:

  ```text
  Expected Final State:

  Actual Final State:

  Invariant Violated?:
  ```

---

### Task 9 — Reduce the Test

* If the race succeeds, determine whether fewer concurrent requests reproduce it.

* Record:

  ```text
  Initial Request Count:

  Minimal Reproducing Count:
  ```

---

### Task 10 — Build Evidence

* Preserve:

  ```text
  Baseline

  Sequential Control

  Concurrent Requests

  Responses

  Final State

  Reproduction Steps
  ```

---

## Investigation Journal

* Record:

  ```text
  Application:

  Feature:

  User:

  Role:

  Account:

  Shared State:

  Security Invariant:

  Initial State:

  Candidate Request:

  Additional Conflicting Request:

  Single Baseline:

  Sequential Duplicate:

  State Reset:

  Protocol:

  Request Count:

  Synchronization Method:

  Concurrent Responses:

  Final State:

  Expected State:

  Actual State:

  Race Window Hypothesis:

  Reproduction Count:

  Minimal Request Count:

  Security Impact:

  Side Effects:

  Evidence:

  Follow-Up:
  ```

---

## Common Mistakes

* Common mistakes include:

  * Sending repeated requests sequentially and calling the result a race condition.
  * Testing concurrency before understanding the normal workflow.
  * Failing to define the security invariant.
  * Ignoring shared server-side state.
  * Not establishing a single-request baseline.
  * Not testing sequential duplicate behavior.
  * Failing to reset application state between attempts.
  * Assuming multiple `200 OK` responses mean multiple actions occurred.
  * Checking only HTTP responses instead of final application state.
  * Sending excessive numbers of requests unnecessarily.
  * Using production financial workflows without explicit authorization.
  * Ignoring multi-endpoint race possibilities.
  * Testing only identical-request races.
  * Ignoring partial-construction windows.
  * Confusing eventual consistency with a race vulnerability.
  * Ignoring connection and protocol effects on synchronization.
  * Assuming requests sent at the same time arrive at the same time.
  * Treating one inconsistent result as confirmed vulnerability.
  * Failing to reproduce with fresh state.
  * Continuing destructive testing after impact has already been demonstrated.
  * Reporting a race without showing the expected invariant and actual final-state violation.

---

## Interview Questions

1. What is a race condition?
2. What is the check-then-act pattern?
3. What does TOCTOU mean?
4. What is a race window?
5. Why can sequential testing miss race conditions?
6. What is the difference between a replay vulnerability and a race condition?
7. What is a security invariant?
8. Why should you define the invariant before race testing?
9. What is shared state?
10. Which application workflows are common race-condition targets?
11. Why should you establish a single-request baseline?
12. Why should you test sequential duplicates before concurrency?
13. Why is state reset important during race testing?
14. How can Burp Repeater help test race conditions?
15. What are request groups?
16. What is parallel request sending?
17. What is last-byte synchronization?
18. Why does synchronization matter?
19. What is the single-packet attack concept?
20. Why can HTTP/2 improve race-condition testing?
21. What is a limit-overrun race condition?
22. How would you test a one-time coupon for a race condition?
23. How would you test a one-time token safely?
24. What is a multi-endpoint race?
25. What is a partial-construction race condition?
26. How can state-machine transitions create race conditions?
27. Why does `200 OK` not prove that multiple operations occurred?
28. Why must final server-side state be verified?
29. What is eventual consistency, and how can it cause false positives?
30. What is idempotency?
31. How can idempotency keys help prevent duplicate operations?
32. What mechanisms can developers use to prevent race conditions?
33. Why should race testing use minimal request counts?
34. Why can network jitter create false negatives?
35. What evidence should be included in a race-condition report?
36. Why should a confirmed race be reduced to a minimal proof of concept?
37. How do you distinguish a genuine race from ordinary business-logic replay?
38. Why should financial race-condition testing be handled carefully?
39. How can Logger and Comparer assist race-condition analysis?
40. What is the correct end-to-end methodology for testing race conditions with Burp?

---

## Quick Revision

* Core race model:

  ```text
  Check State

  ↓

  Race Window

  ↓

  Update State
  ```

* Vulnerable concurrency:

  ```text
  Request A → Check Valid

  Request B → Check Valid

  ↓

  Both Act

  ↓

  Invariant Violated
  ```

* Baseline methodology:

  ```text
  Single Request

  ↓

  Sequential Duplicate

  ↓

  Reset State

  ↓

  Concurrent Requests

  ↓

  Final-State Verification
  ```

* Race hypothesis:

  ```text
  Shared State

  +

  Non-Atomic Check and Update

  +

  Concurrent Requests

  =

  Potential Race Condition
  ```

* Synchronization:

  ```text
  Prepare Requests

  ↓

  Minimize Timing Difference

  ↓

  Release Together

  ↓

  Enter Same Race Window
  ```

* HTTP/2 advantage:

  ```text
  One Connection

  +

  Multiple Streams

  +

  Tight Synchronization
  ```

* Strong proof:

  ```text
  Sequential Protection Works

  +

  Concurrent Protection Fails

  +

  Final State Violates Invariant
  ```

* Remember:

  ```text
  Repeated Requests

  ≠

  Race Condition
  ```

  ```text
  Same Send Time

  ≠

  Same Arrival Time
  ```

  ```text
  Multiple 200 Responses

  ≠

  Multiple Successful State Changes
  ```

  ```text
  Temporary UI Inconsistency

  ≠

  Confirmed Race
  ```

  ```text
  More Requests

  ≠

  Better Testing
  ```

* Professional principle:

  ```text
  Understand State

  +

  Define Invariant

  +

  Synchronize Precisely

  +

  Verify Final State

  +

  Reproduce Minimally

  =

  Reliable Race Condition Testing
  ```

---

## Key Takeaways

* Race conditions occur when application behavior depends on timing or ordering and concurrent operations interact with shared state.
* Many race conditions follow a check-then-act pattern in which validation and state modification are not performed atomically.
* The race window is the period during which another operation can interfere with a security-sensitive workflow.
* Sequential replay and race-condition testing are different; if a duplicate action works sequentially, the issue may not require concurrency.
* Define the application's security invariant before testing so you know exactly what condition must never be violated.
* Identify shared state such as balances, coupon status, inventory, tokens, reward claims, limits, and workflow states.
* Establish both single-request and sequential-duplicate baselines before attempting concurrent requests.
* State must be reset between tests using fresh accounts, tokens, coupons, objects, or lab resets.
* Burp Repeater request groups and coordinated sending can support controlled concurrency testing.
* Last-byte synchronization and HTTP/2 single-packet concepts can reduce timing differences between requests.
* HTTP/2 multiplexing can provide tighter synchronization because multiple streams share one established connection.
* High-value race targets include one-time actions, financial operations, limits, inventory, voting, invitations, verification workflows, and state transitions.
* Race conditions can involve identical requests or different endpoints competing over the same state.
* Partial-construction races occur when an object can be accessed while it is temporarily in an incompletely initialized state.
* Multiple successful HTTP responses do not prove a vulnerability; always verify the authoritative final application state.
* Eventual consistency, asynchronous processing, UI delays, retries, and caching can create misleading observations.
* Race testing should use the minimum number of requests necessary and avoid uncontrolled high-volume traffic.
* Financial and destructive workflows require dedicated test environments, minimal impact, and explicit authorization.
* Strong evidence shows the invariant, initial state, sequential protection, concurrent bypass, and measurable final-state violation.
* Once confirmed, reduce the race to a minimal, clean, reproducible proof of concept.
