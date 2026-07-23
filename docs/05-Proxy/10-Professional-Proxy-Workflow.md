# Professional Proxy Workflow

## Overview

- Burp Proxy is most effective when used as part of a disciplined investigation workflow rather than as a tool for randomly intercepting and modifying traffic.

- A professional Proxy workflow combines:

  * Scope control.
  * Normal application browsing.
  * HTTP history analysis.
  * Request and response interception.
  * WebSocket observation.
  * Controlled request modification.
  * Traffic filtering.
  * Evidence preservation.
  * Handoff to other Burp tools.

- The objective is to turn raw application traffic into an organized testing process.

```text
Browse
    ↓
Observe
    ↓
Filter
    ↓
Identify
    ↓
Investigate
    ↓
Validate
    ↓
Document
```

---

## Learning Objectives

- By the end of this lesson, you will be able to:

  * Build a repeatable Proxy workflow for web application testing.
  * Configure Proxy behavior before beginning an assessment.
  * Use HTTP history as the primary traffic-analysis workspace.
  * Separate baseline observation from active modification.
  * Identify high-value requests efficiently.
  * Move requests from Proxy to appropriate Burp tools.
  * Maintain authentication and application state during testing.
  * Reduce noise using scope and filters.
  * Preserve evidence during investigations.
  * Avoid common workflow mistakes that create false conclusions.
  * Use Proxy efficiently during penetration tests and bug bounty assessments.

---

## Core Workflow

```text
Confirm Authorization and Scope
        ↓
Configure Browser and Proxy
        ↓
Verify Traffic Flow
        ↓
Browse Application Normally
        ↓
Build HTTP History and Site Map
        ↓
Filter and Prioritize Traffic
        ↓
Identify Security-Relevant Requests
        ↓
Send Requests to Appropriate Tools
        ↓
Perform Controlled Testing
        ↓
Compare Results
        ↓
Validate Final State and Impact
        ↓
Preserve Evidence
        ↓
Document Findings
```

- Each stage has a specific purpose.

- Skipping stages often creates incomplete coverage or unreliable conclusions.

---

## Phase 1 — Confirm Authorization and Scope

- Before testing, identify exactly what you are authorized to assess.

- Confirm:

  * Allowed domains.
  * Allowed subdomains.
  * APIs.
  * Test accounts.
  * User roles.
  * Prohibited actions.
  * Rate limits.
  * Testing windows.
  * Third-party exclusions.

```text
Authorized Target
        ↓
Defined Scope
        ↓
Controlled Testing
```

- Public accessibility does not imply authorization.

---

## Phase 2 — Prepare the Proxy Environment

- Before browsing the target, verify that the environment is predictable.

- Check:

  * Browser proxy configuration.
  * Burp Proxy listener.
  * HTTPS certificate trust.
  * Interception state.
  * Scope configuration.
  * Interception rules.
  * Match and Replace rules.
  * Upstream proxy settings if applicable.

- Unexpected configuration can silently alter traffic.

---

## Pre-Assessment Proxy Checklist

```text
[ ] Correct project loaded
[ ] Correct Proxy listener active
[ ] Browser using intended listener
[ ] HTTPS interception working
[ ] Target scope configured
[ ] Interception rules reviewed
[ ] Match and Replace rules reviewed
[ ] No stale automatic modifications
[ ] Test accounts available
[ ] Notes/evidence workspace ready
```

- This checklist reduces configuration-related mistakes.

---

## Phase 3 — Verify Traffic Flow

- Before testing complex functionality, use a simple request to confirm the Proxy path.

```text
Browser
    ↓
Burp Proxy
    ↓
Target
    ↓
Burp Proxy
    ↓
Browser
```

- Verify:

  * Requests appear in HTTP history.
  * HTTPS responses load normally.
  * The expected host is visible.
  * Cookies are being sent correctly.
  * No unexpected transformations occur.

- If basic traffic is unreliable, fix the environment before continuing.

---

## Phase 4 — Keep Intercept Off During General Browsing

- During normal application exploration, keeping Intercept off is usually more efficient.

```text
Intercept OFF
      ↓
Browse Normally
      ↓
Traffic Recorded Automatically
      ↓
Review in HTTP History
```

- This prevents every resource from stopping manually.

- HTTP history still records proxied traffic.

- Turn Intercept on only when you need precise control over a specific request or response.

---

## Phase 5 — Establish a Baseline

- Before modifying traffic, understand legitimate application behavior.

- Record:

  * Normal request.
  * Normal response.
  * User role.
  * Authentication state.
  * Object ownership.
  * Expected application state.
  * Relevant cookies or tokens.

```text
Legitimate Action
      ↓
Capture Request
      ↓
Capture Response
      ↓
Verify Result
      ↓
Baseline Established
```

- Without a baseline, unexpected behavior is difficult to interpret.

---

## Phase 6 — Browse Systematically

- Avoid random navigation.

- Explore application functionality by category.

- Example:

```text
Authentication
    ↓
Account
    ↓
Profile
    ↓
User Data
    ↓
Transactions
    ↓
Uploads
    ↓
Administrative Features
    ↓
APIs
    ↓
Real-Time Features
```

- For each feature, identify:

  * Requests.
  * Parameters.
  * Methods.
  * Objects.
  * Roles.
  * State transitions.
  * Security boundaries.

---

## Phase 7 — Use HTTP History as the Main Traffic Workspace

- HTTP history should normally be your primary Proxy analysis view.

- Use it to identify:

  * Interesting endpoints.
  * State-changing requests.
  * API calls.
  * Authentication requests.
  * Authorization-sensitive objects.
  * File operations.
  * Hidden background requests.
  * Redirect chains.
  * Error responses.

- Do not inspect every request equally.

- Prioritize security-relevant traffic.

---

## High-Value Request Categories

```text
Authentication

Session Management

Password Changes

Email Changes

Account Recovery

User and Object IDs

Role or Permission Changes

Administrative Actions

Payments and Orders

File Uploads

API Requests

GraphQL Operations

WebSocket Handshakes

State-Changing POST/PUT/PATCH/DELETE Requests
```

- These requests often cross important security boundaries.

---

## Phase 8 — Reduce Noise

- Modern applications generate large amounts of traffic.

- Common noise includes:

  * Images.
  * Fonts.
  * Analytics.
  * Advertising.
  * Static CSS.
  * Third-party scripts.
  * Telemetry.
  * Unrelated external services.

- Use:

  * Target scope.
  * HTTP history filters.
  * Search.
  * MIME-type filtering.
  * Host filtering.

```text
All Traffic
    ↓
Scope
    ↓
Filters
    ↓
Relevant Requests
```

- Reducing noise improves both speed and accuracy.

---

## Phase 9 — Understand the Request Before Modifying It

- Before testing a request, answer:

  * What feature generated it?
  * Which user generated it?
  * What object does it affect?
  * Which parameters are client-controlled?
  * Which values appear security-sensitive?
  * Does it change server state?
  * What should the server enforce?

- Example:

```http
PATCH /api/users/142/profile HTTP/1.1
Host: app.example.test
Cookie: session=...

{"displayName":"Alice"}
```

- Questions:

```text
Who is user 142?

Does the authenticated user own object 142?

Can the object ID be changed?

What authorization rule should apply?

How will final state be verified?
```

---

## Phase 10 — Send Requests to the Right Tool

- Proxy is the observation and traffic-capture layer.

- Other Burp tools support deeper investigation.

```text
Proxy
  │
  ├── Repeater
  │      Controlled manual testing
  │
  ├── Intruder
  │      Systematic request variation
  │
  ├── Decoder
  │      Data transformation
  │
  ├── Comparer
  │      Difference analysis
  │
  ├── Sequencer
  │      Token randomness analysis
  │
  └── Scanner
         Automated analysis where authorized
```

- Avoid forcing every task into Proxy interception.

---

## Proxy to Repeater Workflow

- One of the most common professional workflows is:

```text
Browse Feature
      ↓
Find Request in HTTP History
      ↓
Send to Repeater
      ↓
Preserve Baseline
      ↓
Change One Variable
      ↓
Send
      ↓
Compare Response
      ↓
Verify Final State
```

- Repeater is usually better than repeated manual interception for controlled vulnerability testing.

---

## One Variable at a Time

- Suppose a request contains:

```text
user_id=142
role=user
tenant=4
status=active
```

- Changing all four values at once makes the result difficult to interpret.

- Prefer:

```text
Baseline
    ↓
Change user_id only
    ↓
Observe
    ↓
Restore
    ↓
Change role only
    ↓
Observe
```

- Controlled testing produces stronger evidence.

---

## Authentication Workflow

- For authentication testing:

```text
Capture Legitimate Login
      ↓
Understand Request Structure
      ↓
Observe Cookies/Tokens Issued
      ↓
Track Redirects
      ↓
Identify Session Establishment
      ↓
Test Controlled Variations
      ↓
Verify Authentication State
```

- Important observations may include:

  * Session cookies.
  * CSRF tokens.
  * Authorization headers.
  * Redirects.
  * MFA steps.
  * Device identifiers.
  * Refresh tokens.

---

## Session Workflow

- During authenticated testing, preserve session awareness.

- Track:

  * Which account is active.
  * Which browser session belongs to which role.
  * Session expiration.
  * Token refresh behavior.
  * Logout behavior.
  * Cookie changes.

```text
User A Session
≠
User B Session
≠
Admin Session
```

- Mixing sessions can invalidate authorization testing.

---

## Multi-Role Testing Workflow

- Authorization testing often requires multiple controlled accounts.

```text
User A
    ↓
Create or Access Object A
    ↓
Capture Baseline Request

User B
    ↓
Attempt Equivalent Request Against Object A
    ↓
Compare Result
    ↓
Verify Final State
```

- Clearly label sessions and evidence.

---

## Authorization Workflow

```text
Identify Protected Object or Action
        ↓
Capture Authorized Baseline
        ↓
Identify Authorization Boundary
        ↓
Change One Relevant Variable
        ↓
Replay Under Controlled Identity
        ↓
Compare Response
        ↓
Verify Object Ownership / Final State
        ↓
Repeat
        ↓
Validate Impact
```

- A different HTTP status alone is not enough.

- Confirm whether the protected action or data was actually accessible.

---

## State-Changing Request Workflow

- For requests that modify data:

```text
Capture Baseline
      ↓
Record Initial State
      ↓
Send Controlled Modified Request
      ↓
Observe Response
      ↓
Reload or Query Object
      ↓
Verify Final State
```

- Remember:

```text
200 OK

≠

State Definitely Changed
```

- And:

```text
Error Response

≠

State Definitely Did Not Change
```

---

## API Workflow

- Modern applications often send important requests in the background.

- Proxy can reveal API traffic generated by the frontend.

- Look for:

  * `/api/`
  * `/v1/`
  * `/v2/`
  * JSON requests.
  * Bearer tokens.
  * Object IDs.
  * Pagination.
  * Filters.
  * Role-dependent endpoints.

```text
Frontend Action
      ↓
API Request Captured
      ↓
Understand Object and Authorization Context
      ↓
Send to Repeater
      ↓
Test Controlled Variations
```

---

## WebSocket Workflow

- When the application uses WebSockets:

```text
HTTP Upgrade Handshake
      ↓
WebSocket Connection
      ↓
Client Messages
      ↓
Server Messages
      ↓
Identify Message Structure
      ↓
Analyze Authentication and Authorization Context
```

- Review both the handshake and subsequent messages.

- Do not assume authorization enforced during the HTTP handshake also protects every WebSocket action.

---

## File Upload Workflow

```text
Choose File
    ↓
Capture Upload Request
    ↓
Identify Multipart Structure
    ↓
Record Filename and Content Type
    ↓
Observe Server Response
    ↓
Determine Storage/Retrieval Behavior
    ↓
Test Validation Safely
    ↓
Verify Final State
```

- Preserve the legitimate baseline before changing file properties.

---

## Business Logic Workflow

- Business logic testing begins with understanding the intended workflow.

```text
Map Normal Sequence
      ↓
Identify Required Preconditions
      ↓
Identify Security Invariants
      ↓
Capture Requests for Each Step
      ↓
Test Replay / Skip / Reorder / Modify
      ↓
Verify Server State
      ↓
Validate Impact
```

- Proxy helps reveal the actual requests behind visible application steps.

---

## Search-Driven Investigation

- Search can be useful when reviewing large traffic histories.

- Useful terms may include:

```text
admin

token

role

user

account

password

upload

graphql

api

redirect

callback

id
```

- Search is a prioritization aid, not proof of vulnerability.

---

## Request Prioritization

- A simple prioritization model:

```text
Does It Change State?
      ↓
Does It Reference an Object?
      ↓
Does It Cross a Role or Tenant Boundary?
      ↓
Does It Handle Sensitive Data?
      ↓
Does It Affect Authentication or Authorization?
```

- Requests with several "yes" answers deserve closer review.

---

## Evidence Collection Workflow

- When a candidate issue appears, preserve:

  * Baseline request.
  * Baseline response.
  * Modified request.
  * Modified response.
  * User identity and role.
  * Object ownership.
  * Relevant timestamps.
  * Final state.
  * Reproduction steps.
  * Security impact.

```text
Observation
    ↓
Reproduction
    ↓
Evidence
    ↓
Impact
    ↓
Conclusion
```

---

## Screenshots vs Raw HTTP Evidence

- Screenshots can support a report, but raw HTTP evidence is often more precise.

- Strong evidence may include:

```text
Request A
Response A

Request B
Response B

Exact Difference

Final Application State
```

- Use screenshots when they clarify visible impact.

- Do not rely on screenshots alone when request-level evidence is important.

---

## Preserve Baselines

- Never overwrite your only known-good request.

- Keep a clean baseline before experimentation.

```text
Baseline Request
      │
      ├── Test Variant 1
      ├── Test Variant 2
      └── Test Variant 3
```

- This makes comparisons reliable and helps recover when testing changes application state.

---

## Avoid Hidden Tool State

- Proxy behavior can be influenced by:

  * Interception rules.
  * Match and Replace.
  * Extensions.
  * Session handling.
  * Project settings.
  * Browser extensions.
  * Upstream proxies.

- When behavior becomes confusing:

```text
Return to Known Baseline
      ↓
Review Automatic Modifications
      ↓
Disable Unnecessary Features
      ↓
Retest
```

---

## Professional Bug Bounty Workflow

```text
Read Program Rules
      ↓
Confirm Scope
      ↓
Configure Burp
      ↓
Browse Application Normally
      ↓
Build Traffic History
      ↓
Map High-Value Features
      ↓
Select Candidate Requests
      ↓
Send to Repeater
      ↓
Test One Hypothesis at a Time
      ↓
Validate Reproducibility
      ↓
Confirm Impact
      ↓
Prepare Clear Evidence
```

- Stay within program rules throughout the process.

---

## Professional Pentest Workflow

```text
Review Rules of Engagement
      ↓
Configure Scope
      ↓
Create Testing Workspace
      ↓
Capture Baseline Traffic
      ↓
Map Application Functionality
      ↓
Prioritize Attack Surface
      ↓
Perform Structured Testing
      ↓
Track Findings and Evidence
      ↓
Validate Findings
      ↓
Report
      ↓
Retest
```

- Proxy supports nearly every stage involving application traffic.

---

## End-of-Session Cleanup

- Before ending a testing session:

```text
[ ] Save required evidence
[ ] Export or preserve important requests
[ ] Record unresolved hypotheses
[ ] Disable temporary Match and Replace rules
[ ] Review interception rules
[ ] Confirm active account/session state
[ ] Save project if required
[ ] Secure notes and sensitive data
```

- Cleanup prevents temporary testing configuration from affecting later work.

---

## Common Mistakes

* Leaving Intercept on during normal browsing and repeatedly blocking page loads.

* Testing before defining scope.

* Modifying requests before understanding legitimate behavior.

* Changing several variables simultaneously.

* Treating every unusual response as a vulnerability.

* Ignoring final application state.

* Mixing sessions from different users.

* Allowing stale Match and Replace rules to modify traffic.

* Testing directly from Proxy when Repeater would provide better control.

* Ignoring background API requests.

* Failing to filter third-party traffic.

* Losing the original baseline request.

* Collecting screenshots without preserving raw HTTP evidence.

* Assuming a successful response means a security boundary was violated.

---

## Professional Tips

* Keep Intercept off during broad exploration.

* Use HTTP history as your traffic journal.

* Define scope early.

* Filter aggressively when applications generate noisy traffic.

* Understand the feature before testing it.

* Preserve clean baselines.

* Send important requests to Repeater.

* Change one variable at a time.

* Maintain separate sessions for separate roles.

* Verify final state after state-changing requests.

* Record evidence while the investigation is fresh.

* Review automatic Proxy modifications whenever behavior becomes unexpected.

* Prefer reproducible workflows over clever but uncontrolled testing.

---

## Field Notes

- Strong Proxy usage is mostly about organization.

- A beginner often asks:

```text
What request should I modify?
```

- A professional tester asks:

```text
What feature am I testing?

What security boundary should exist?

Which request represents that boundary?

What is the legitimate baseline?

Which single change tests my hypothesis?

What evidence would prove failure?

How will I verify final state?
```

- Proxy provides visibility.

- Methodology turns that visibility into reliable security testing.

---

## Practice Tasks

1. Configure Burp Proxy for an authorized lab.

2. Confirm the listener and HTTPS interception work correctly.

3. Add the lab target to scope.

4. Browse the application with Intercept off.

5. Review HTTP history.

6. Filter traffic to the target.

7. Identify:

   * One authentication request.
   * One state-changing request.
   * One request containing an object identifier.
   * One API request.
   * One static resource.

8. Choose one high-value request.

9. Record its legitimate baseline.

10. Send it to Repeater.

11. Change one non-destructive variable.

12. Compare the result.

13. Restore the baseline.

14. Verify final application state.

15. Record the complete investigation in notes.

---

## Practical Workflow Exercise

- In an authorized lab, choose one application feature such as profile editing.

- Build the following investigation:

```text
Open Profile
      ↓
Capture GET Request
      ↓
Edit Profile Normally
      ↓
Capture State-Changing Request
      ↓
Record User and Object Context
      ↓
Send Request to Repeater
      ↓
Preserve Baseline
      ↓
Identify Client-Controlled Values
      ↓
Form One Security Hypothesis
      ↓
Change One Variable
      ↓
Compare Response
      ↓
Reload Profile
      ↓
Verify Final State
      ↓
Document Conclusion
```

- Your notes should distinguish:

  * Observation.
  * Hypothesis.
  * Test.
  * Evidence.
  * Conclusion.

---

## Interview Questions

1. What is a professional workflow for using Burp Proxy?

2. Why is Intercept usually kept off during general browsing?

3. Why should you establish a baseline before modifying requests?

4. How do scope and HTTP history filters improve testing?

5. What types of requests should be prioritized during Proxy analysis?

6. When should a request be sent from Proxy to Repeater?

7. Why should you change one variable at a time?

8. Why is final-state verification important?

9. How should multiple user roles be handled during authorization testing?

10. Why can automatic Proxy modifications create misleading results?

11. What evidence should be preserved for a candidate vulnerability?

12. How does Proxy support API testing?

13. How does Proxy support WebSocket testing?

14. What should you check when application behavior becomes unexpectedly different?

15. What is the difference between observing traffic and validating a vulnerability?

---

## Quick Revision

```text
Professional Proxy Workflow

Scope
→ Configure
→ Browse
→ Observe
→ Filter
→ Prioritize
→ Investigate
→ Validate
→ Document
```

```text
General Browsing

Intercept OFF
```

```text
Precise Live Modification

Intercept ON
```

```text
Main Traffic Workspace

HTTP History
```

```text
Controlled Manual Testing

Proxy
→ Repeater
```

```text
Reliable Testing

Baseline
→ One Variable
→ Compare
→ Verify Final State
```

```text
Authorization Testing

Identity
+ Object Ownership
+ Controlled Request
+ Final-State Verification
```

```text
Unexpected Response

≠

Validated Vulnerability
```

```text
Strong Evidence

Baseline
+ Modified Request
+ Response
+ Final State
+ Reproducibility
+ Impact
```

---

## Summary

- A professional Proxy workflow begins with authorization, scope, and predictable configuration.

- During general exploration, HTTP history is usually more useful than intercepting every request.

- High-value traffic should be filtered, prioritized, understood, and handed off to the appropriate Burp tools for controlled testing.

- Reliable investigations preserve baselines, change one variable at a time, maintain clear session context, verify final state, and collect reproducible evidence.

- Burp Proxy provides visibility into application communication; disciplined methodology turns that visibility into professional penetration-testing and bug-bounty workflows.
