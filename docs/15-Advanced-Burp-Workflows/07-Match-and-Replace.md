# Match and Replace

> **Module 15 — Advanced Burp Workflows**

> **Progress**

```text
████████░░░░░░░ 8 / 15 Files
```

---

## Overview

* Burp Suite's **Match and Replace** feature allows requests and responses passing through the Proxy to be modified automatically according to predefined rules.

* Instead of manually editing the same value repeatedly, Match and Replace can apply consistent transformations to traffic.

* Common uses include:

  * Adding or replacing HTTP headers.
  * Modifying cookies.
  * Changing request parameters.
  * Replacing response content.
  * Removing unwanted headers.
  * Simulating different client behavior.
  * Testing application behavior under controlled modifications.

* A simple conceptual workflow is:

  ```text
  Original Traffic

  ↓

  Match Rule

  ↓

  Replacement Applied

  ↓

  Modified Traffic
  ```

* For example:

  ```http
  User-Agent: Browser-A
  ```

* Can automatically become:

  ```http
  User-Agent: Browser-B
  ```

* Match and Replace is powerful because modifications happen automatically.

* That same automation creates risk.

* A forgotten rule can silently modify requests and responses, causing the tester to analyze traffic that differs from what the browser or application originally generated.

* Professional use therefore requires:

  ```text
  Clear Purpose

  +

  Precise Matching

  +

  Narrow Scope

  +

  Verification

  +

  Cleanup
  ```

---

## Learning Objectives

* By the end of this lesson, you will be able to:

  * Explain how Match and Replace works.
  * Understand where transformations occur in the traffic flow.
  * Distinguish request and response modifications.
  * Create precise matching conditions.
  * Understand literal and regex-based matching.
  * Add, replace, and remove HTTP headers safely.
  * Recognize useful penetration testing scenarios.
  * Understand the risks of global transformations.
  * Identify interactions with session handling and extensions.
  * Troubleshoot unexpected traffic modifications.
  * Verify the final outgoing request.
  * Design controlled Match and Replace workflows.

---

## What Is Match and Replace?

* Match and Replace is a Proxy feature that automatically transforms HTTP messages when configured conditions are met.

* Conceptually:

  ```text
  HTTP Message

  ↓

  Does Rule Match?

  ├── No
  │   ↓
  │   Leave Unchanged
  │
  └── Yes
      ↓
      Apply Replacement

  ↓

  Continue Processing
  ```

* The transformation can target parts of:

  ```text
  Requests

  Responses

  Headers

  Bodies
  ```

* This allows repetitive modifications to be applied consistently without manually editing every message.

---

## Where Match and Replace Fits

* Without automatic transformation:

  ```text
  Browser

  ↓

  Request

  ↓

  Manual Modification

  ↓

  Server
  ```

* With Match and Replace:

  ```text
  Browser

  ↓

  Burp Proxy

  ↓

  Match and Replace Rule

  ↓

  Modified Request

  ↓

  Server
  ```

* Response transformations follow the reverse direction:

  ```text
  Server

  ↓

  Response

  ↓

  Match and Replace Rule

  ↓

  Modified Response

  ↓

  Browser
  ```

---

## Why Match and Replace Is Useful

* During an assessment, you may need to repeat the same modification across many requests.

* Examples include:

  ```text
  Add a Testing Header

  Remove a Header

  Replace a Cookie Value

  Modify User-Agent

  Add Authorization State

  Rewrite a Response Header

  Alter Repeated Response Content
  ```

* Manually performing the same modification repeatedly is:

  ```text
  Slow

  Error-Prone

  Inconsistent
  ```

* Match and Replace can make the transformation deterministic.

---

## Core Rule Components

* A Match and Replace rule generally answers:

  ```text
  What traffic should be modified?

  What should be matched?

  What should replace it?

  Is regex required?

  Is the rule enabled?
  ```

* Think of every rule as:

  ```text
  Target

  +

  Condition

  +

  Transformation
  ```

---

## Request vs Response Rules

* Rules may affect different parts of the HTTP conversation.

### Request Rules

* Request rules modify traffic before it reaches the target server.

* Examples:

  ```text
  Add Header

  Replace Header

  Remove Header

  Modify Request Body
  ```

* Conceptually:

  ```text
  Browser Request

  ↓

  Match and Replace

  ↓

  Modified Request

  ↓

  Server
  ```

### Response Rules

* Response rules modify traffic before the response reaches the browser.

* Examples:

  ```text
  Remove Security Header

  Replace Response Header

  Modify Response Body

  Change Client-Side Content
  ```

* Conceptually:

  ```text
  Server Response

  ↓

  Match and Replace

  ↓

  Modified Response

  ↓

  Browser
  ```

* Always distinguish:

  ```text
  What the server actually returned

  from

  What the browser received after Burp modification
  ```

---

## Match and Replace Rule Types

* Depending on the Burp version and configuration, rules can target message components such as:

  * Request headers.
  * Request bodies.
  * Response headers.
  * Response bodies.

* Select the narrowest appropriate target.

* Do not modify an entire message when only one header requires transformation.

---

## Literal Matching

* A literal match looks for an exact value.

* Example:

  ```http
  User-Agent: Browser-A
  ```

* Replace with:

  ```http
  User-Agent: Browser-B
  ```

* Conceptually:

  ```text
  Match:

  Browser-A

  Replace:

  Browser-B
  ```

* Literal matching is useful when the target value is:

  ```text
  Stable

  Exact

  Predictable
  ```

---

## Regex Matching

* Regular expressions can match dynamic or patterned values.

* Example concept:

  ```text
  Match:

  X-Test-ID: .*
  ```

* This could match different values such as:

  ```text
  X-Test-ID: 123

  X-Test-ID: ABC

  X-Test-ID: session-45
  ```

* Regex is useful when values vary but follow a predictable structure.

---

## Regex Risk

* Broad regular expressions can modify more traffic than intended.

* Example:

  ```text
  .*
  ```

* This is extremely broad.

* A poorly designed rule can:

  ```text
  Match Unexpected Content

  Modify Multiple Headers

  Alter Request Bodies

  Corrupt Application Traffic

  Produce Misleading Results
  ```

* Prefer the narrowest reliable pattern.

---

## Adding Request Headers

* One common use is adding a header to requests.

* Example:

  ```http
  X-Security-Test: authorized-assessment
  ```

* This may be useful in environments where the organization uses a special testing header to:

  * Identify authorized security traffic.
  * Route test requests.
  * Exclude traffic from certain defensive actions.
  * Correlate assessment activity in logs.

* Only add such headers when explicitly provided or approved by the target organization.

---

## Replacing Request Headers

* Suppose requests contain:

  ```http
  User-Agent: Mozilla/5.0
  ```

* A rule could replace it with another controlled value.

* This can help investigate:

  ```text
  User-Agent-Based Behavior

  Device-Specific Responses

  Client Compatibility Logic

  Header-Dependent Routing
  ```

* Always compare behavior before and after the transformation.

---

## Removing Request Headers

* Match and Replace can also help investigate behavior when a header is absent.

* Example candidates may include:

  ```text
  Origin

  Referer

  Custom Client Headers

  Application-Specific Headers
  ```

* The testing question should be explicit.

* Example:

  ```text
  Hypothesis:

  Does the application enforce this control when the header is absent?
  ```

* Do not remove headers globally without understanding their purpose.

---

## Modifying Cookies

* Cookies may control:

  ```text
  Authentication

  Session State

  Preferences

  Feature Flags

  Tracking

  Application Behavior
  ```

* Automatically replacing a cookie can be useful in a controlled workflow.

* Example:

  ```http
  Cookie: theme=light
  ```

* Becomes:

  ```http
  Cookie: theme=dark
  ```

* However, automatically replacing authentication cookies requires much greater care.

---

## Authentication Cookie Risk

* Suppose the browser is authenticated as:

  ```text
  User B
  ```

* But Match and Replace changes:

  ```http
  Cookie: session=USER_B
  ```

* To:

  ```http
  Cookie: session=USER_A
  ```

* The browser may visually appear to represent User B while outgoing requests actually authenticate as User A.

* This can invalidate:

  ```text
  IDOR Testing

  Authorization Testing

  Role Testing

  Session Testing
  ```

* Authentication state should remain explicit and verifiable.

---

## Modifying Authorization Headers

* Applications may use:

  ```http
  Authorization: Bearer TOKEN
  ```

* Automatically replacing tokens can help maintain a controlled authentication context.

* However:

  ```text
  Token Replacement

  ≠

  Authentication Strategy
  ```

* For dynamic or expiring authentication, session handling or authentication automation may be more appropriate.

* Match and Replace should not become an uncontrolled substitute for proper session management.

---

## Request Body Modification

* Match and Replace can modify request-body content.

* This may be useful when a predictable value appears repeatedly.

* Example:

  ```json
  {
    "client": "web"
  }
  ```

* Could be transformed into:

  ```json
  {
    "client": "mobile"
  }
  ```

* This can help investigate client-specific application behavior.

---

## Request Body Risks

* Body transformations require care because the same text may appear in unrelated contexts.

* Example:

  ```text
  role=user
  ```

* A broad replacement could affect:

  ```text
  JSON

  Form Data

  Nested Objects

  Logging Fields

  Descriptions

  Unrelated Parameters
  ```

* Prefer manual Repeater testing when the hypothesis concerns only a small number of requests.

---

## Response Header Modification

* Response rules can modify headers before they reach the browser.

* Example investigation scenarios include temporarily changing:

  ```text
  Content-Security-Policy

  X-Frame-Options

  Cache-Control

  Content-Type

  Application-Specific Headers
  ```

* This can help study client-side behavior.

* Remember:

  ```text
  Modified Browser Behavior

  does not prove

  Server-Side Vulnerability
  ```

---

## Removing Security Headers

* During controlled analysis, you may temporarily remove a response security header to understand its effect.

* Example:

  ```text
  Original Server Response

  ↓

  Security Header Present

  ↓

  Match and Replace Removes Header

  ↓

  Browser Receives Modified Response
  ```

* This may help isolate browser-side behavior.

* However, the original server still sent the header.

* Do not report:

  ```text
  Missing Security Header
  ```

* When Burp itself removed it.

---

## Response Body Modification

* Response-body rules can change content before the browser renders it.

* This can help investigate:

  ```text
  Client-Side Feature Flags

  Hidden Interface Elements

  Front-End Validation

  JavaScript Behavior

  UI Restrictions
  ```

* Example concept:

  ```text
  Server:

  "featureEnabled": false
  ```

* Burp modifies it to:

  ```text
  Browser:

  "featureEnabled": true
  ```

* This can reveal client-side functionality.

* It does not automatically prove that the server permits the corresponding action.

---

## Client-Side vs Server-Side Controls

* Suppose modifying a response reveals an admin button.

* The correct next question is:

  ```text
  Can an unauthorized user successfully perform the underlying server-side action?
  ```

* The visible button itself is not enough.

* Professional workflow:

  ```text
  Modify Client-Side Response

  ↓

  Reveal Hidden Functionality

  ↓

  Identify Underlying Request

  ↓

  Test Server-Side Authorization

  ↓

  Validate Impact
  ```

---

## Match and Replace as an Investigation Tool

* Match and Replace should support a hypothesis.

* Example:

  ```text
  Observation:

  Application behavior changes based on a custom header.

  ↓

  Hypothesis:

  The header controls a client or feature context.

  ↓

  Controlled Rule:

  Replace only that header.

  ↓

  Compare Behavior

  ↓

  Validate Server-Side Effect
  ```

* Avoid creating transformations without a clear testing question.

---

## Global Rules Are Dangerous

* A global rule may affect:

  ```text
  Every Host

  Every Path

  Every Request

  Every Browser Tab

  Background Requests

  API Traffic

  Authentication Traffic
  ```

* A simple rule can therefore change much more than expected.

* Before enabling a broad rule, ask:

  ```text
  Which exact traffic should this affect?
  ```

---

## Scope Before Automation

* Prefer:

  ```text
  Specific Host

  +

  Specific Message Type

  +

  Specific Match

  +

  Specific Purpose
  ```

* Over:

  ```text
  All Traffic

  +

  Broad Regex

  +

  Permanent Rule
  ```

---

## Rule Naming and Documentation

* If your Burp version or workflow allows descriptive organization, document each transformation clearly.

* Example:

  ```text
  Purpose:

  Add authorized testing header

  Target:

  api.example.test

  Transformation:

  Add X-Security-Test header

  Reason:

  Required by engagement rules
  ```

* You should always be able to explain why a rule exists.

---

## Temporary vs Persistent Rules

* Think of rules as either:

  ```text
  Temporary Investigation Rule

  or

  Stable Workflow Rule
  ```

### Temporary Investigation Rule

* Created for a specific hypothesis.

* Example:

  ```text
  Remove CSP temporarily to inspect client-side behavior.
  ```

* Disable or delete it when the investigation ends.

### Stable Workflow Rule

* Required throughout an authorized assessment.

* Example:

  ```text
  Add organization-provided testing identifier header.
  ```

* Even stable rules should be reviewed regularly.

---

## One Rule, One Purpose

* Prefer:

  ```text
  Rule A:

  Add testing header
  ```

  ```text
  Rule B:

  Replace client identifier
  ```

* Instead of one complex transformation attempting to handle multiple unrelated tasks.

* Single-purpose rules are easier to:

  ```text
  Understand

  Test

  Disable

  Troubleshoot

  Audit
  ```

---

## Change One Variable at a Time

* This principle is especially important with automatic transformations.

* Poor workflow:

  ```text
  Change User-Agent

  +

  Replace Cookie

  +

  Remove Origin

  +

  Modify Response
  ```

* Then observe different behavior.

* You cannot easily determine which change caused it.

* Better:

  ```text
  Baseline

  ↓

  Change One Variable

  ↓

  Observe

  ↓

  Record

  ↓

  Continue
  ```

---

## Match and Replace With Proxy

* Proxy is the primary context for Match and Replace.

* Traffic may flow:

  ```text
  Browser

  ↓

  Proxy

  ↓

  Match and Replace

  ↓

  Other Burp Processing

  ↓

  Server
  ```

* Because transformations can happen automatically, intercepted traffic may differ from the browser's original request.

* Always inspect the effective request.

---

## Match and Replace With Repeater

* Requests sent from Proxy to Repeater may already contain transformed values.

* Example:

  ```text
  Browser Original

  ↓

  Match and Replace

  ↓

  Proxy History

  ↓

  Send to Repeater
  ```

* The Repeater request may represent the transformed state rather than the browser's original state.

* Before testing, identify:

  ```text
  What was original?

  What was modified?

  What is Repeater actually sending?
  ```

---

## Match and Replace With Intruder

* Intruder testing requires controlled inputs.

* Hidden transformations can affect payload interpretation.

* Example:

  ```text
  Intruder Payload

  ↓

  Match and Replace

  ↓

  Modified Final Request
  ```

* If the rule changes the same field Intruder is testing, results may become unreliable.

* Validate the final outgoing request before trusting the attack results.

---

## Match and Replace With Scanner

* Automated scanning depends on consistent request behavior.

* A transformation may:

  ```text
  Add Authentication

  Remove Authentication

  Change Headers

  Modify Request Bodies

  Alter Application Behavior
  ```

* This can affect scanner coverage and findings.

* Before scanning, review active Match and Replace rules.

---

## Match and Replace With Session Handling

* Match and Replace and session-handling rules may both modify authentication-related values.

* Example:

  ```text
  Session Rule:

  Set session=NEW
  ```

* Another rule:

  ```text
  Match and Replace:

  session=NEW

  →

  session=OLD
  ```

* The final request may use the wrong state.

* Avoid overlapping responsibility.

---

## Match and Replace With Authentication Automation

* Authentication automation may generate fresh state.

* Match and Replace may then overwrite it.

* Conceptually:

  ```text
  Authentication Workflow

  ↓

  Fresh Token B

  ↓

  Match and Replace

  ↓

  Old Token A

  ↓

  Target Request
  ```

* The authentication workflow appears successful, but the target request still fails.

* Inspect the final outgoing request.

---

## Match and Replace With Extensions

* Extensions may also modify traffic.

* Example:

  ```text
  Original Request

  ↓

  Match and Replace

  ↓

  Extension

  ↓

  Session Handling

  ↓

  Final Request
  ```

* Or processing may occur differently depending on the feature and extension behavior.

* The important principle is:

  ```text
  Multiple Automation Layers

  =

  Multiple Possible Mutation Sources
  ```

* Keep the workflow simple whenever possible.

---

## Hidden Transformation Problem

* One of the biggest risks is forgetting that a rule is active.

* Example:

  ```text
  Monday:

  Create rule to replace role=user with role=admin
  ```

* Later:

  ```text
  Wednesday:

  Begin unrelated API testing
  ```

* The forgotten rule still modifies traffic.

* You may misinterpret application behavior.

* Treat active transformation rules as part of the testing environment.

---

## Baseline Contamination

* A baseline request should represent normal application behavior.

* If Match and Replace is already modifying it:

  ```text
  Baseline

  ≠

  Original Behavior
  ```

* Before establishing a baseline:

  ```text
  Review Active Rules

  ↓

  Disable Unrelated Rules

  ↓

  Capture Fresh Request

  ↓

  Record Original Behavior
  ```

---

## False Positive Scenario

* Suppose a response normally contains:

  ```http
  Content-Security-Policy: default-src 'self'
  ```

* An active rule removes it.

* You inspect the browser and conclude:

  ```text
  CSP is missing.
  ```

* This is a false positive caused by your own tooling.

* Always compare against the unmodified server response.

---

## False Negative Scenario

* Suppose the application normally rejects requests without a custom authorization header.

* A forgotten rule automatically adds:

  ```http
  X-Internal-Access: true
  ```

* You test unauthenticated access.

* The request succeeds.

* You may incorrectly conclude that the endpoint is publicly accessible.

* The rule changed the security context.

---

## Evidence vs Transformed Evidence

* Separate:

  ```text
  Server Evidence
  ```

* From:

  ```text
  Burp-Modified Evidence
  ```

* For reporting, preserve the actual request and response that demonstrate the vulnerability.

* Document intentional transformations when they materially affect reproduction.

---

## Verifying the Final Outgoing Request

* Before trusting an important result, confirm:

  ```text
  Method

  URL

  Headers

  Cookies

  Authorization

  Body

  Content-Length

  Identity

  Transformation State
  ```

* Ask:

  ```text
  Is this the request I intended to send?
  ```

---

## Verifying the Original Response

* When using response transformations, also ask:

  ```text
  What did the server actually return?
  ```

* Distinguish:

  ```text
  Original Server Response

  from

  Modified Browser Response
  ```

* This distinction is critical for evidence.

---

## Safe Testing Workflow

* Use:

  ```text
  Define Hypothesis

  ↓

  Capture Baseline

  ↓

  Create One Narrow Rule

  ↓

  Enable Rule

  ↓

  Trigger Controlled Request

  ↓

  Inspect Final Request / Response

  ↓

  Compare With Baseline

  ↓

  Record Result

  ↓

  Disable Rule
  ```

---

## Rule Validation

* Before applying a rule broadly:

  1. Create the rule.
  2. Enable it for a controlled test.
  3. Send one known request.
  4. Inspect the transformation.
  5. Confirm only the intended value changed.
  6. Verify authentication context.
  7. Check for unexpected side effects.
  8. Expand usage only if necessary.

* Never assume a rule behaves exactly as intended without observing it.

---

## Troubleshooting — Expected Replacement Does Not Occur

* Check:

  ```text
  Rule Enabled?

  ↓

  Correct Message Type?

  ↓

  Exact Match Correct?

  ↓

  Regex Correct?

  ↓

  Case / Whitespace Difference?

  ↓

  Traffic Passing Through Proxy?

  ↓

  Another Rule Changing Value?
  ```

---

## Troubleshooting — Too Much Traffic Is Modified

* Check:

  ```text
  Match Too Broad?

  ↓

  Regex Too General?

  ↓

  Wrong Message Type?

  ↓

  Rule Applied Globally?

  ↓

  Same Text Appears in Multiple Contexts?
  ```

* Narrow the rule before continuing.

---

## Troubleshooting — Authentication Suddenly Breaks

* Check whether a rule modifies:

  ```text
  Cookie

  Authorization Header

  CSRF Token

  Host Header

  Origin

  Referer

  Request Body
  ```

* Temporarily disable Match and Replace rules and retest the baseline.

---

## Troubleshooting — Browser and Repeater Behave Differently

* Compare:

  ```text
  Browser Request

  Proxy History

  Repeater Request

  Final Outgoing Request
  ```

* Look for:

  ```text
  Automatic Header Changes

  Cookie Replacement

  Session Handling

  Extensions

  Manual Repeater Edits
  ```

---

## Troubleshooting — Response Looks Different in Browser

* Check whether a response rule modifies:

  ```text
  Headers

  HTML

  JavaScript

  JSON

  Security Policies
  ```

* Compare the raw server response with the browser-visible result.

---

## Troubleshooting — Rule Works Sometimes

* Investigate:

  ```text
  Dynamic Values

  Different Hosts

  Different Paths

  Different Content Types

  Encoding

  Case Differences

  Multiple Similar Headers

  Rule Ordering

  Other Automation
  ```

* Intermittent behavior often indicates that the matching condition is incomplete.

---

## Troubleshooting — Intruder or Scanner Results Look Wrong

* Disable unrelated transformations.

* Establish a clean baseline.

* Then compare:

  ```text
  Expected Request

  vs

  Actual Final Request
  ```

* Automated results are only trustworthy when the request state is controlled.

---

## Performance Considerations

* Simple rules generally add little overhead.

* However, performance can degrade when using:

  ```text
  Many Rules

  Broad Regex

  Large Response Bodies

  High Request Volume

  Multiple Automation Layers
  ```

* Keep rules minimal and purposeful.

---

## Safety Considerations

* Automatic transformations can increase risk when they:

  ```text
  Change Authentication

  Change Authorization Context

  Modify State-Changing Requests

  Affect Production Traffic

  Interact With Automated Scanning

  Apply Outside Intended Scope
  ```

* Always respect the authorized testing scope.

---

## Match and Replace Safety Checklist

* Before enabling a rule, verify:

  ```text
  [ ] Clear testing purpose defined

  [ ] Baseline captured

  [ ] Correct request/response type selected

  [ ] Match condition is precise

  [ ] Regex reviewed if used

  [ ] Replacement value verified

  [ ] Authentication impact understood

  [ ] Authorization impact understood

  [ ] Scope minimized

  [ ] Interaction with session handling checked

  [ ] Interaction with authentication automation checked

  [ ] Extension interaction considered

  [ ] Final outgoing request can be inspected

  [ ] Original server response can be preserved

  [ ] Cleanup plan exists
  ```

---

## Match and Replace Rule Card

* Document important rules:

  ```text
  Rule Name:

  Purpose:

  Hypothesis:

  Message Type:

  Match:

  Replace:

  Regex:

  Target Host:

  Target Path:

  Authentication Context:

  Expected Transformation:

  Potential Side Effects:

  Related Session Rule:

  Related Extension:

  Validation Request:

  Enabled During:

  Disable After:
  ```

---

## Match and Replace Strategy Matrix

| Scenario                                   | Recommended Approach              |
| ------------------------------------------ | --------------------------------- |
| Add authorized testing header              | Narrow automatic rule             |
| Change one value in one request            | Repeater may be better            |
| Repeated stable header replacement         | Match and Replace                 |
| Dynamic token refresh                      | Authentication/session automation |
| Test missing authentication                | Disable auth-related replacement  |
| Modify response for client-side analysis   | Temporary response rule           |
| Remove security header for browser testing | Temporary controlled rule         |
| Multi-user IDOR testing                    | Avoid hidden session replacement  |
| Large Scanner run                          | Review all active rules first     |
| Complex conditional transformation         | Consider an extension             |
| One-time hypothesis                        | Manual modification may be safer  |

---

## Match and Replace vs Repeater

* Use Match and Replace when:

  ```text
  The Same Transformation

  Must Be Repeated

  Across Multiple Requests
  ```

* Use Repeater when:

  ```text
  One or Few Requests

  Need Precise Manual Changes
  ```

* Do not automate a one-time modification unnecessarily.

---

## Match and Replace vs Session Handling

* Use Match and Replace for:

  ```text
  Stable Message Transformation
  ```

* Use session handling for:

  ```text
  Dynamic Session State
  ```

* Example:

  ```text
  Add static testing header

  → Match and Replace
  ```

  ```text
  Refresh expired CSRF token

  → Session Handling / Macro
  ```

---

## Match and Replace vs Extensions

* Use Match and Replace when the transformation is:

  ```text
  Simple

  Predictable

  Declarative
  ```

* Consider an extension when logic requires:

  ```text
  Complex Conditions

  Parsing

  Stateful Decisions

  Multiple Dependent Transformations

  Custom Processing
  ```

* Choose the simplest reliable tool.

---

## Professional Workflow

* Use:

  ```text
  Identify Repetitive Modification

  ↓

  Define Security Hypothesis

  ↓

  Capture Clean Baseline

  ↓

  Determine Exact Message Component

  ↓

  Create Minimal Rule

  ↓

  Validate With One Request

  ↓

  Inspect Final Traffic

  ↓

  Confirm Authentication Context

  ↓

  Expand Only If Necessary

  ↓

  Compare Results

  ↓

  Preserve Evidence

  ↓

  Disable Rule

  ↓

  Return to Clean Baseline
  ```

---

## Practical Exercise

* Use a deliberately vulnerable lab or another system you are authorized to test.

### Task 1 — Capture a Baseline

* Send a normal request through Burp.

* Record:

  ```text
  Method:

  URL:

  Important Headers:

  Cookies:

  Body:

  Response Status:
  ```

---

### Task 2 — Create a Harmless Header Rule

* Create a controlled rule that modifies or adds a harmless test header.

* Example concept:

  ```text
  X-Lab-Test: enabled
  ```

* Send one request.

* Confirm the final outgoing request contains the expected value.

---

### Task 3 — Compare Behavior

* Compare:

  ```text
  Baseline Request

  vs

  Modified Request
  ```

* Record exactly what changed.

---

### Task 4 — Test a Response Transformation

* In a lab environment, create a harmless response transformation.

* Confirm:

  ```text
  Original Server Response

  ≠

  Browser-Visible Modified Response
  ```

* Record both states.

---

### Task 5 — Test Rule Precision

* Trigger several different requests.

* Determine:

  ```text
  Which Requests Changed?

  Which Requests Did Not?

  Was That Expected?
  ```

* Narrow the rule if necessary.

---

### Task 6 — Check Authentication Context

* If authenticated, verify:

  ```text
  Current User:

  Current Role:

  Session Cookie:

  Authorization Header:
  ```

* Confirm the rule did not unintentionally alter identity.

---

### Task 7 — Disable the Rule

* Disable the rule.

* Repeat the baseline request.

* Confirm normal behavior returns.

---

## Investigation Journal

* Record:

  ```text
  Application:

  Rule Purpose:

  Security Hypothesis:

  Message Type:

  Match:

  Replacement:

  Regex Used:

  Target Host:

  Target Path:

  Baseline Captured:

  Original Request:

  Modified Request:

  Original Response:

  Modified Response:

  Authentication Context:

  Identity Changed?:

  Unexpected Requests Modified?:

  Session Rule Interaction:

  Extension Interaction:

  Scanner / Intruder Impact:

  Rule Disabled After Test?:

  Result:

  Security Impact:

  Follow-Up:
  ```

---

## Common Mistakes

* Common mistakes include:

  * Creating broad global rules.
  * Forgetting that a rule is enabled.
  * Using overly broad regular expressions.
  * Modifying several variables simultaneously.
  * Replacing authentication state unintentionally.
  * Confusing modified responses with original server responses.
  * Establishing a baseline while transformation rules are active.
  * Allowing Match and Replace to conflict with session handling.
  * Trusting Scanner or Intruder results without checking final requests.
  * Leaving temporary investigation rules enabled after testing.
  * Reporting behavior created by Burp itself as an application vulnerability.

---

## Interview Questions

1. What is Match and Replace in Burp Suite?
2. Why is Match and Replace useful during penetration testing?
3. What types of HTTP message components can it modify?
4. What is the difference between request and response transformations?
5. When should literal matching be preferred?
6. When might regex matching be useful?
7. What risks are introduced by broad regex patterns?
8. How can Match and Replace be used with HTTP headers?
9. Why is automatically replacing authentication cookies risky?
10. How can response modification help investigate client-side controls?
11. Why does revealing an admin button not automatically prove an authorization vulnerability?
12. What is baseline contamination?
13. How can a forgotten Match and Replace rule create a false positive?
14. How can it create a false negative?
15. Why should the final outgoing request be inspected?
16. How can Match and Replace conflict with session-handling rules?
17. How can it interfere with authentication automation?
18. What risks exist when extensions also modify traffic?
19. When should Repeater be preferred over Match and Replace?
20. When should session handling be preferred?
21. Why should one rule generally have one purpose?
22. Why should you change one variable at a time?
23. What should you do before running Scanner with active transformation rules?
24. Why must original server responses be distinguished from transformed responses?
25. What should happen to temporary Match and Replace rules after an investigation?

---

## Quick Revision

* Core concept:

  ```text
  HTTP Message

  ↓

  Match Condition

  ↓

  Replacement

  ↓

  Modified Message
  ```

* Request flow:

  ```text
  Browser

  ↓

  Burp

  ↓

  Match and Replace

  ↓

  Server
  ```

* Response flow:

  ```text
  Server

  ↓

  Burp

  ↓

  Match and Replace

  ↓

  Browser
  ```

* Safe design:

  ```text
  One Purpose

  +

  Precise Match

  +

  Narrow Scope

  +

  Verified Result
  ```

* Investigation model:

  ```text
  Baseline

  ↓

  One Transformation

  ↓

  Compare

  ↓

  Validate

  ↓

  Disable
  ```

* Remember:

  ```text
  Browser-Visible Response

  ≠

  Original Server Response
  ```

  ```text
  Valid Session

  ≠

  Intended Identity
  ```

  ```text
  Automated Modification

  ≠

  Application Behavior
  ```

* Professional principle:

  ```text
  Controlled Transformation

  +

  Observable Final Traffic

  +

  Clean Baseline

  =

  Reliable Testing
  ```

---

## Key Takeaways

* Match and Replace automatically transforms HTTP requests or responses according to predefined rules.
* Use it when the same predictable modification must be applied repeatedly.
* Request transformations affect what reaches the server, while response transformations affect what reaches the browser.
* Literal matching is preferable for stable exact values, while regex should be used carefully for dynamic patterns.
* Broad rules and broad regular expressions can silently modify unrelated traffic.
* Automatically replacing cookies or authorization headers can change the testing identity and invalidate authorization results.
* Response modification is useful for investigating client-side behavior but does not automatically prove a server-side vulnerability.
* Always distinguish the original server response from Burp-modified content.
* Match and Replace can interact with session handling, authentication automation, Scanner, Intruder, Repeater, and extensions.
* Multiple automation layers make traffic harder to reason about and troubleshoot.
* Capture a clean baseline before enabling experimental transformations.
* Change one variable at a time whenever possible.
* Verify the final outgoing request before trusting an important security result.
* Temporary rules should be disabled or removed when the investigation is complete.
* Professional use of Match and Replace is based on precise, observable, reversible transformations.
