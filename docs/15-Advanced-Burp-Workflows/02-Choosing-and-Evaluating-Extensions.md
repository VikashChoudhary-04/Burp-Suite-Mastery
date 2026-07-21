# Choosing and Evaluating Extensions

> **Module 15 — Advanced Burp Workflows**

> **Progress**

```text
███░░░░░░░░░░░░ 3 / 15 Files
```

---

## Overview

* Burp extensions can dramatically improve testing efficiency, but every extension also becomes part of your security testing environment.

* The correct question is not:

  ```text
  Which extensions should I install?
  ```

* A better question is:

  ```text
  What testing problem am I trying to solve,
  and is an extension the safest and most reliable way to solve it?
  ```

* Professional extension selection requires evaluating:

  * Purpose
  * Source and trust
  * Maintenance
  * Compatibility
  * Traffic behavior
  * Data access
  * External communication
  * Scope behavior
  * Automation
  * Performance
  * Reproducibility

* This lesson provides a repeatable methodology for deciding whether an extension belongs in your Burp environment.

---

## Learning Objectives

- By the end of this lesson, you will be able to:

  * Decide when an extension is actually necessary.
  * Evaluate an extension before installation.
  * Assess extension trust and maintenance.
  * Identify passive, active, and hybrid behavior.
  * Evaluate data-handling and privacy implications.
  * Determine whether an extension communicates externally.
  * Assess scope and traffic-generation behavior.
  * Test extensions safely before real engagements.
  * Compare extensions that solve similar problems.
  * Detect extension overlap and unnecessary complexity.
  * Build a minimal extension profile.
  * Document extension decisions professionally.

---

## Start With the Security Question

- Do not begin with:

  ```text
  Extension

  ↓

  Find a Use for It
  ```  

- Begin with:

  ```text
  Security Question
  
  ↓

  Required Capability
  
  ↓

  Can Core Burp Handle It?
  
  ↓
  
  If Not, Evaluate Extension  
  ```

- Example:

  ```text
  Question:

  Do responses contain authorization differences
  between User A and User B?

  ↓

  Need:

  Efficient multi-user response comparison

  ↓

  Core Burp:

  Possible manually with Repeater and Comparer

  ↓
  
  Scale Problem:

  Many endpoints require repeated comparison

  ↓
  
  Extension:
  
  May improve efficiency
  ```

- The extension solves a defined problem.

---

## The Extension Necessity Test

- Before installing an extension, ask five questions.

  ```text
  1. What exact problem does it solve?

  2. Can built-in Burp functionality already solve it?

  3. Will I use it often enough to justify additional complexity?
  
  4. Does it introduce meaningful security or operational risk?

  5. Can I explain exactly what it does?
  ```

- If you cannot answer these questions, do not immediately add it to your environment.

---

## Core Burp First

- Burp already provides powerful capabilities through:

  ```text
  Proxy

  Target

  Repeater
  
  Intruder
  
  Decoder
  
  Comparer

  Sequencer

  Logger

  Scanner
  
  Collaborator
  ```  

- Many tasks can be solved without extensions.

- Example:

  ```text
  Need to compare two responses
  ```

- Before installing a specialized comparison extension:

  ```text
  Can Comparer answer the question?
  ```  

- If yes, additional complexity may be unnecessary.

---

## When Extensions Add Real Value

- Extensions are especially useful when they provide:

  ```text
  Specialized Protocol Support

  Repeated Workflow Automation
  
  Improved Visualization

  Complex Data Transformation

  Testing Capabilities Not Conveniently Available in Core Burp

  Large-Scale Comparison
  
  Assessment-Specific Integrations
  ```

- The key word is:

  ```text
  Value
  ```

not novelty.

---

## Extension Evaluation Framework

- Use the following framework:

  ```text
  NEED

  ↓
  
  TRUST

  ↓
  
  BEHAVIOR
  
  ↓

  DATA

  ↓
  
  SCOPE
  
  ↓
  
  IMPACT

  ↓
  
  COMPATIBILITY
  
  ↓
  
  TEST
  
  ↓

  DECISION
  ```  

- Each stage answers a different risk question.

---

### 1 — Need

- Ask:

  ```text
  What problem am I solving?
  ```

- Document:

  ```text
  Problem:

  Required Capability:
  
  Why Core Burp Is Insufficient:
    
  Expected Benefit:
  ```  

- Example:

  ```text
  Problem:
  Repeatedly comparing authorization behavior across two user sessions.

  Required Capability:
  Automated multi-session comparison.

  Why Core Burp Is Insufficient:
  Manual comparison works but becomes inefficient across many endpoints.

  Expected Benefit:
  Reduce repetitive comparison while preserving manual validation.
  ```

- This prevents tool-driven testing.

---

### 2 — Trust

- An extension executes code in your testing environment.

- Evaluate:

  ```text
  Who created it?

  Where is it distributed?

  Is the source available?
  
  Is the project maintained?
  
  Is there documentation?
  
  Are issues being addressed?
  
  Is its purpose clear?
  ```

- Trust should be based on evidence, not popularity alone.

---

## Source Evaluation

- Possible sources include:

  ```text
  BApp Store

  Official Project Repository

  Vendor Distribution

  Internal Organization Extension
  
  Unknown Third-Party Download
  ```  

- Risk generally increases when provenance becomes unclear.

- Prefer software whose origin and behavior can be reasonably verified.

---

## Popularity Is Not Proof

- An extension may be:

  ```text
  Popular

  Frequently Recommended
  
  Widely Installed
  ```  

- and still:

  ```text
  Have Bugs

  Be Outdated

  Behave Unexpectedly
  
  Conflict With Your Workflow

  Be Inappropriate for Sensitive Data
  ```

- Popularity is useful context.

- It is not a security guarantee.

---

## Maintenance

- Check whether the extension appears actively maintained.

- Look for:

  ```text
  Recent Updates

  Compatibility Information
  
  Resolved Issues

  Clear Documentation
  
  Supported Burp Versions
  ```

- An abandoned extension may still function, but the risk of:

  ```text
  Compatibility Problems

  Unfixed Bugs
  
  Deprecated APIs

  Unexpected Failures
  ```

may increase over time.

---

### 3 — Behavior

- Determine what the extension actually does.

- Classify it:

  ```text
  Passive

  Active
  
  Hybrid
  ```

- Then identify whether it can:

  ```text
  Read Requests

  Read Responses
  
  Modify Requests
  
  Modify Responses
  
  Generate Requests
  
  Replay Requests
  
  Perform Background Actions

  Add Scanner Checks

  Trigger Collaborator Interactions
  ```  

- Do not rely only on the extension's name.

---

## Example — Misleading Assumption

- Suppose an extension is called:

  ```text
  Parameter Analyzer
  ```

- You may assume:

  ```text
  It only analyzes existing parameters.
  ```

- But it might actually:

  ```text
  Discover Candidate Parameters

  ↓
  
  Generate Requests With Each Candidate
  
  ↓

  Compare Responses
  ```

- That is active behavior.

- The name alone does not tell you the traffic impact.

---

### 4 — Data Access

- Burp may contain highly sensitive information.

- Examples:

  ```text
  Session Cookies

  Bearer Tokens

  API Keys
  
  Password Reset Links
  
  Personal Data
  
  Internal Hostnames

  Source Code Fragments
  
  Confidential API Responses
  ```  

- Ask:

  ```text
  What Burp data can the extension access?

  What does it store?
  
  Where does it store it?

  Does it retain logs?
  
  Does it transmit anything externally?
  ```  

- Treat extensions as part of the engagement's data-handling boundary.

---

## Local Processing vs External Processing

- A critical distinction:

  ```text
  LOCAL

  Burp Traffic
    ↓
  Extension
    ↓
  Local Analysis
  ```

- versus:

  ```text
  EXTERNAL

  Burp Traffic
      ↓
  Extension
      ↓
  Third-Party Service
      ↓
  Result
  ```

- The second model may introduce:

  ```text
  Confidentiality Concerns

  Client Restrictions
  
  Data Residency Issues
  
  Third-Party Exposure
  
  Retention Questions
  ```  

- External processing should never be assumed acceptable.

---

## External Communication Checklist

- Determine whether the extension:

  ```text
  Calls Cloud APIs

  Downloads Data
  
  Uploads Requests or Responses
    
  Uses External AI Services
    
  Performs Telemetry

  Checks for Updates
  
  Sends Crash Reports

  Uses Third-Party Authentication
  ```

- Not all external communication is malicious or inappropriate.

- But it must be understood.

---

### 5 — Scope Behavior

- Ask:
  
  ```text
  Does the extension respect Burp target scope?

  Does it operate only on selected requests?
  
  Does it automatically process all proxy traffic?

  Does it follow redirects?
  
  Does it discover and contact new hosts?

  Does it test third-party resources?
  ```

- This is particularly important for modern applications that contact:

  ```text
  Analytics

  CDNs
  
  Identity Providers

  Payment Systems
  
  Third-Party APIs
  ```

- Remember:

  ```text
  Visible in Burp

  ≠

  Authorized for Active Testing
  ```

---

## Scope Validation Test

- Before real use:

  ```text
  Configure Controlled Scope

  ↓
  
  Use Extension

  ↓

  Observe Logger
  
  ↓
  
  Check Destination Hosts
  
  ↓

  Confirm Expected Scope Behavior
  ```

- Do not rely solely on assumptions.

---

### 6 — Traffic Impact

- Estimate:

  ```text
  How many requests can it generate?

  At what rate?
  
  What HTTP methods?

  Can it replay state-changing requests?
  
  Can it trigger authentication events?

  Can it create or delete resources?
  ```  

- A useful classification:

  ```text
  LOW

  Local analysis only
  ```

  ```text
  MODERATE

  Small number of controlled requests
  ```

  ```text
  HIGH

  Automated fuzzing / scanning / large request volume
  ```

- The classification helps determine where and when the extension is appropriate.

---

## State-Changing Risk

- Suppose the original request is:

  ```http
  POST /api/orders HTTP/1.1
  ```

- An extension creates 50 test variations.

- Potential result:

  ```text
  50 Orders
  ```

- Similarly:

  ```http
  POST /password-reset
  ```

- could generate:

  ```text
  Multiple Emails
  ```

- or:

  ```http
  DELETE /api/resource/100
  ```

could alter real data.

- Always understand request semantics before applying automation.

---

## Rate and Resource Impact

- Extensions may cause:

  ```text
  High Request Rates

  Application Load

  Rate-Limit Triggers

  Account Lockouts

  WAF Alerts

  Large Burp Project Growth
  ```  

- Ask:

  ```text
  Can request rate be controlled?

  Can testing be paused?

  Can concurrency be limited?

  Can target scope be restricted?
  ```

- Controlled automation is preferable to uncontrolled volume.

---

### 7 — Compatibility

- Evaluate compatibility with:

  ```text
  Current Burp Version

  Extension API
  
  Required Runtime

  Other Extensions
  
  Operating System

  Project Configuration
  ```  

- Possible incompatibility symptoms:

  ```text
  Extension Fails to Load

  Missing UI
  
  Runtime Errors

  Burp Instability
  
  Requests Not Processed Correctly
  ```

- Test compatibility before depending on the extension.

---

## Extension Conflict Matrix

- If multiple extensions modify traffic, document overlap.

- Example:

| Extension   | Reads Traffic | Modifies Traffic | Generates Requests |
| ----------- | ------------: | ---------------: | -----------------: |
| Extension A |           Yes |              Yes |                 No |
| Extension B |           Yes |               No |                Yes |
| Extension C |           Yes |              Yes |                Yes |

- The more overlapping behavior exists, the harder troubleshooting becomes.

---

### 8 — Safe Testing

- Never make a sensitive client environment the first place you test an unfamiliar extension.

- Prefer:

  ```text
  Local Lab

  ↓
  
  Known Baseline
  
  ↓
  
  Enable Extension
  
  ↓
  
  Perform Controlled Action
  
  ↓
  
  Observe Logger

  ↓
  
  Compare
  ```

- Suitable environments include deliberately vulnerable applications or systems you own and are authorized to test.

---

## Controlled Extension Test

- Use this sequence:

  ```text
  1. Disable Extension

  2. Perform One Known Request

  3. Record Baseline Traffic
  
  4. Enable Extension

  5. Repeat Same Request
  
  6. Compare Traffic
  
  7. Check Logs
  
  8. Check External Connections if Relevant

  9. Confirm Scope Behavior
  
  10. Document Findings
  ```

- This reveals actual behavior rather than relying entirely on descriptions.

---

## The Baseline Principle

- Suppose without an extension:

  ```text
  1 Browser Action

  =
  
  3 Requests
  ```

- After enabling it:

  ```text
  1 Browser Action

  =
  
  47 Requests
  ```  

- That difference matters.

- Without a baseline, you may never notice.

---

## Test the Extension, Not Just the Target

- When evaluating a new extension, your initial question is not:

  ```text
  Did it find vulnerabilities?
  ```  

- First ask:

  ```text
  What did the extension do?
  ```

- Only after understanding its behavior should you trust its output.

---

### 9 — Decision

- After evaluation, choose one:

  ```text
  INSTALL

  Useful and acceptable
  ```

  ```text
  INSTALL ONLY WHEN NEEDED

  Useful but adds complexity or risk
  ```

  ```text
  LAB ONLY

  Educational value but inappropriate for sensitive assessments
  ```

  ```text
  REJECT

  Risk or complexity exceeds benefit
  ```

- Not every useful tool belongs in your permanent environment.

---

## Permanent vs Assessment-Specific Extensions

- A useful strategy is to separate:

  ```text
  BASELINE EXTENSIONS
  ```

- from:

  ```text
  ASSESSMENT-SPECIFIC EXTENSIONS
  ```

- Baseline extensions should be:

  ```text
  Frequently Used

  Well Understood

  Trusted

  Low Surprise
  ```

- Assessment-specific extensions may be loaded only for:

  ```text
  GraphQL

  JWT

  Authorization

  Special Encoding

  Specific Frameworks

  Specialized APIs
  ```  

- This keeps the environment predictable.

---

## Minimal Extension Profile

- Conceptually:

  ```text
  Burp Baseline
  │
  ├── Core Burp
  ├── Extension A — Daily Workflow
  ├── Extension B — Traffic Organization
  └── Extension C — Common Analysis
  ```

- During a specific engagement:

  ```text
  Baseline
      +
  Assessment-Specific Extension
  ```

- After the engagement:

  ```text
  Review

  ↓
  
  Disable / Remove if No Longer Needed
  ```

---

## Extension Selection Matrix

- When comparing multiple extensions, use a simple matrix.

| Criterion               | Extension A | Extension B |
| ----------------------- | ----------: | ----------: |
| Solves required problem |         Yes |         Yes |
| Maintained              |      Strong |     Limited |
| Generates traffic       |          No |         Yes |
| External communication  |          No |         Yes |
| Easy to reproduce       |        High |      Medium |
| Complexity              |         Low |        High |

- The "most powerful" option is not always the best choice.

- Choose based on the engagement.

---

## Capability Duplication

- Avoid unnecessary overlap.

- Example:

  ```text
  Extension A

  → JWT decoding
  ```

  ```text
  Extension B

  → JWT decoding + editing
  ```

  ```text
  Extension C

  → JWT decoding + editing + automated tests
  ```

- You probably do not need all three enabled simultaneously.

- Ask:

  ```text
  Which capability do I actually need?
  ```

---

## Complexity Budget

- Every additional extension adds some amount of:

  ```text
  Configuration

  Memory Usage

  UI Complexity

  Traffic Behavior

  Potential Conflicts

  Troubleshooting Cost
  ```

- Think of this as a:

  ```text
  Complexity Budget
  ```

- Spend it only when the extension provides meaningful value.

---

## Reliability Over Features

- Compare:

  ```text
  Environment A

  40 Extensions

  Unknown Configurations

  Unexpected Traffic

  Hard to Troubleshoot
  ```

- with:

  ```text
  Environment B

  5 Trusted Extensions

  Known Behavior

  Predictable Traffic

  Easy to Troubleshoot
  ```

- Environment B is often more professional.

---

## Evaluating Security Findings From Extensions

- Suppose an extension reports:

  ```text
  Potential Authorization Bypass
  ```

- Do not immediately report it.

- Use:

  ```text
  Extension Finding

  ↓

  Understand Trigger

  ↓
  
  Capture Original Request

  ↓
  
  Establish Baseline

  ↓
  
  Reproduce Manually
  
  ↓

  Verify Authentication Context
  
  ↓

  Verify Authorization Boundary
  
  ↓

  Confirm Impact
  
  ↓
  
  Report
  ```

- The extension provides a lead.

- You provide the conclusion.

---

## False Positives

- Extensions may misinterpret:

  ```text
  Dynamic Responses

  Random Tokens
  
  Timestamps

  Caching
  
  Personalization
  
  Redirects
  
  Error Handling
  ```

as security-relevant differences.

- Manual validation is essential.

---

## False Negatives

- An extension may also miss vulnerabilities because:

  ```text
  Application Logic Is Complex

  Workflow Requires Context
  
  State Changes Dynamically
  
  Authorization Depends on Business Rules
  
  Custom Protocol Behavior Exists
  ```

- Therefore:

  ```text
  No Extension Finding

  ≠
  
  No Vulnerability
  ```

---

## Extension Version Awareness

- If an extension plays an important role in reproducing a result, record its version.

- Example:

  ```text
  Extension:

  Version:
  
  Configuration:

  Burp Version:
  
  Testing Date:
  ```

- Why?

  - Because future behavior may change after updates.

---

## Update Evaluation

- When an important extension updates:

  ```text
  Read Changes

  ↓

  Check Compatibility
  
  ↓
  
  Retest Critical Workflow
  
  ↓
  
  Observe Traffic

  ↓
  
  Confirm Expected Behavior
  ```

- Do not assume an update is behavior-neutral.

---

## Extension Removal Review

- Periodically ask:

  ```text
  Do I still use this?

  Do I understand what it does?
  
  Is it maintained?

  Does another tool now replace it?
  
  Does it introduce unnecessary risk?
  ```

- Remove unnecessary components.

---

## Example Evaluation — Authorization Helper

- Suppose you want an extension to assist with multi-user authorization testing.

### Need

```text
Repeated comparison between User A and User B.
```

### Benefit

```text
Reduces repetitive manual request duplication.
```

### Risks

```text
May replay state-changing requests.

May send requests under wrong session.

May create false positives from dynamic content.
```

### Evaluation

```text
Test only safe GET requests first.

Confirm session mapping.

Observe Logger.

Validate results manually.
```

### Decision

```text
Useful for scaling known methodology.

Not a replacement for manual authorization analysis.
```

---

## Example Evaluation — External Analysis Extension

- Suppose an extension sends content to an external API for analysis.

- Ask:

  ```text
  What data is transmitted?

  Can requests contain credentials?
  
  Where is data processed?

  Is data retained?
  
  Is client approval required?
  
  Can sensitive fields be excluded?
  ```

- Even if the capability is useful:

  ```text
  Useful

  ≠
  
  Automatically Appropriate
  ```

- Engagement data-handling rules take priority.

---

## Example Evaluation — Active Parameter Discovery

- Suppose an extension attempts to discover hidden parameters.

- Possible behavior:

  ```text
  Original Request

  ↓
  
  Generate Candidate Parameters
  
  ↓
  
  Send Many Variants
  
  ↓

  Compare Responses
  ```

- Evaluate:

  ```text
  Request Volume

  Scope

  Methods Tested
  
  State-Changing Risk

  Rate Control
  
  False Positive Handling
  ```

- Test it safely before using it broadly.

---

## Extension Evaluation Card

- For each important extension, maintain:

  ```text
  Name:

  Purpose:
  
  Source:

  Version:
  
  Maintained:
  
  Core Burp Alternative:

  Passive / Active / Hybrid:

  Reads Traffic:
  
  Modifies Traffic:
  
  Generates Requests:
  
  External Communication:

  Scope Behavior:

  State-Changing Risk:
  
  Performance Impact:

  Known Conflicts:
  
  Testing Environment Verified:

  Decision:
  
  Notes:
  ```

- This creates a professional record.

---

## Three-Level Trust Model

- A practical mental model:

### Level 1 — Trusted Baseline

```text
Well Understood

Frequently Used

Verified Behavior

Minimal Surprise
```

---

### Level 2 — Assessment Specific

```text
Useful for Particular Technology

Enabled Only When Needed

Behavior Reviewed
```

---

### Level 3 — Experimental

```text
New

Unfamiliar

Under Evaluation
```

- Experimental extensions belong in controlled labs first.

---

## Extension Deployment Workflow

```text
Discover

↓

Research

↓

Evaluate

↓

Test Locally

↓

Document

↓

Approve for Use

↓

Enable When Needed

↓

Monitor Behavior

↓

Review After Assessment
```

- This is more reliable than permanent uncontrolled accumulation.

---

## Troubleshooting an Extension Decision

- If you are unsure whether to install something, ask:

  ```text
  Can I solve this manually first?
  ```

- If yes:

  ```text
  Perform Manual Workflow

  ↓

  Understand Problem

  ↓

  Identify Repetition

  ↓

  Decide Whether Automation Adds Value
  ```

- Automation is easier to evaluate when you already understand the manual process.

---

## Professional Decision Tree

```text
Need New Capability?

↓

Can Core Burp Handle It Efficiently?

├── Yes
│   ↓
│   Use Core Burp
│
└── No
    ↓

Is There a Suitable Extension?

├── No
│   ↓
│   Use Manual / Alternative Workflow
│
└── Yes
    ↓

Can Its Source and Behavior Be Reasonably Trusted?

├── No
│   ↓
│   Reject / Investigate Further
│
└── Yes
    ↓

Does It Handle Data Appropriately?

├── No
│   ↓
│   Do Not Use With Sensitive Data
│
└── Yes
    ↓

Is Traffic / Scope Behavior Acceptable?

├── No
│   ↓
│   Reconfigure or Reject
│
└── Yes
    ↓

Test in Controlled Environment

↓

Use Minimally

↓

Validate Output Manually
```

---

## Common Mistakes

- Avoid:

  ```text
  Installing an extension because everyone recommends it.
  ```

- Recommendation does not replace evaluation.

---

```text
Installing several tools that perform the same task.
```

- This increases complexity.

---

```text
Testing unfamiliar extensions directly on client systems.
```

- Use a controlled environment first.

---

```text
Ignoring data-handling behavior.
```

- Extensions may process sensitive traffic.

---

```text
Assuming scope is automatically respected.
```

- Verify behavior.

---

```text
Ignoring state-changing requests.
```

- Automated replay may create real effects.

---

```text
Assuming extension output is proof.
```

- Validate manually.

---

```text
Never reviewing old extensions.
```

- Your environment gradually becomes unpredictable.

---

## Practical Exercise

- Use a safe local lab.

### Task 1 — Identify a Need

- Choose one testing problem.

- Document:

  ```text
  Security / Workflow Problem:

  Can Core Burp Solve It?:
  
  Why an Extension Might Help:
  ```

---

### Task 2 — Select a Candidate

- Record:

  ```text
  Extension:

  Source:

  Purpose:
  
  Maintenance Status:
  
  Compatibility:
  ```

---

### Task 3 — Evaluate Behavior

- Determine:

  ```text
  Passive / Active / Hybrid:

  Reads Traffic:

  Modifies Traffic:

  Generates Requests:

  External Communication:
  
  Scope Behavior:
  ```

---

### Task 4 — Establish Baseline

- With the extension disabled:

  ```text
  Perform One Known Action

  ↓
  
  Record Requests
  
  ↓
  
  Record Responses
  ```

---

### Task 5 — Enable and Compare

- Repeat the same action.

- Compare:

  ```text
  Request Count

  Destinations
  
  Request Modifications

  Response Behavior

  New Background Traffic
  ```

---

### Task 6 — Make a Decision

- Choose:

  ```text
  Permanent Baseline

  Assessment Specific

  Lab Only

  Reject
  ```

- Explain why.

---

## Investigation Journal

```text
Extension:

Problem Being Solved:

Core Burp Alternative:

Reason for Extension:

Source / Trust:

Traffic Behavior:

Data Access:

External Communication:

Scope Behavior:

Request Volume:

State-Changing Risk:

Compatibility:

Baseline Test:

Observed Differences:

Decision:

Reasoning:
```

---

## Interview Questions

1. How do you decide whether to install a Burp extension?
2. Why should core Burp functionality be considered before extensions?
3. What factors determine whether an extension can be trusted?
4. What is the difference between passive, active, and hybrid extensions?
5. Why is extension data handling important during client assessments?
6. How would you determine whether an extension communicates externally?
7. Why should extension scope behavior be verified?
8. What risks arise when extensions replay state-changing requests?
9. How would you safely evaluate a new extension?
10. Why is establishing a baseline useful during extension evaluation?
11. How would you compare two extensions that solve the same problem?
12. Why can too many extensions reduce testing reliability?
13. What is the difference between baseline and assessment-specific extensions?
14. Why should extension-generated findings be manually validated?
15. How can an extension produce false positives?
16. Why does absence of extension findings not prove security?
17. What information would you document about an important extension?
18. Why should extensions be reviewed after updates?
19. What is a practical way to classify extension trust?
20. What should you do if you cannot explain what an extension is doing?

---

## Quick Revision

- Start with:

  ```text
  Problem

  ↓

  Required Capability
  ```  

- Then:

  ```text
  Can Core Burp Solve It?

  ↓  

  If Not
  
  ↓

  Evaluate Extension
  ```

- Evaluation:

  ```text
  NEED

  ↓

  TRUST

  ↓  
  
  BEHAVIOR

  ↓

  DATA

  ↓

  SCOPE

  ↓

  IMPACT

  ↓

  COMPATIBILITY

  ↓
  
  TEST
  
  ↓
  
  DECISION
  ```

- Prefer:

  ```text
  Minimal

  Trusted

  Purpose-Driven

  Verified
  ```

- over:

  ```text
  Large
  
  Overlapping

  Unreviewed
  
  Unpredictable
  ```

- Remember:

  ```text
  Popular

  ≠
  
  Automatically Trusted
  ```

  ```text
  Installed

  ≠

  Passive
  ```

  ```text
  Extension Finding

  ≠
  
  Confirmed Vulnerability
  ```

  ```text
  No Finding

  ≠

  No Vulnerability
  ```

---

## Key Takeaways

* Extension selection should begin with a defined testing problem rather than tool popularity.
* Built-in Burp functionality should be considered before introducing additional complexity.
* Evaluate extension trust, maintenance, behavior, data access, external communication, scope, traffic impact, and compatibility.
* Distinguish passive, active, and hybrid extensions.
* Never assume an extension respects engagement scope without understanding or verifying its behavior.
* State-changing requests require particular caution when automated tools replay or modify traffic.
* Test unfamiliar extensions in controlled environments before using them during real assessments.
* Establish a baseline so extension-generated traffic and modifications can be identified.
* Prefer a small set of trusted baseline extensions plus assessment-specific tools when necessary.
* Extension findings are leads that require manual validation.
* Periodically review, update, disable, or remove extensions to keep the Burp environment predictable.
* The best extension is not the one with the most features. It is the one that **solves a defined problem with acceptable risk, minimal complexity, and behavior you fully understand**.

