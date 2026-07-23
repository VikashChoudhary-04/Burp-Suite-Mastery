# Match and Replace

## Learning Objectives

- By the end of this lesson, you will be able to:

  * Understand the purpose of Match and Replace in Burp Proxy.
  * Explain where automatic request and response transformations fit into a testing workflow.
  * Create controlled Match and Replace rules.
  * Modify headers and other supported message content automatically.
  * Choose appropriate matching strategies.
  * Recognize ordering, scope, and unintended-modification risks.
  * Validate transformations before relying on them during an assessment.
  * Use Match and Replace safely in authorized penetration tests and bug bounty workflows.

---

## Introduction

- Repeatedly making the same modification to intercepted traffic is inefficient and error-prone.

- During an assessment, you may repeatedly need to:

  * Add a custom request header.
  * Replace a header value.
  * Remove an unwanted header.
  * Modify selected request or response content.
  * Apply a consistent transformation across matching traffic.

- Burp Proxy's **Match and Replace** functionality can automate these repetitive transformations.

- The core idea is:

  ```text
  Traffic Passes Through Proxy
          │
          ▼
  Match Rule Evaluated
          │
          ▼
  Matching Content Identified
          │
          ▼
  Replacement Applied
          │
          ▼
  Modified Message Continues
  ```

- This can make testing faster and more consistent, but automation must remain visible and controlled.

---

## What Is Match and Replace?

- Match and Replace is a Burp Proxy feature that automatically modifies matching parts of HTTP messages as traffic passes through Burp.

- A rule generally defines:

  ```text
  What message area should be considered?

  What should be matched?

  What should replace it?

  Should the rule be enabled?
  ```

- Depending on the rule type and Burp version, transformations can be applied to supported request or response components.

- Common use cases include:

  * Adding request headers.
  * Replacing request-header values.
  * Removing selected headers.
  * Modifying response headers.
  * Performing controlled text substitutions.

---

## Why Match and Replace Matters

- Manual editing is useful when investigating one request.

- It becomes inefficient when the same change must be made repeatedly.

- Consider a workflow requiring a custom header on many requests:

  ```http
  X-Test-Mode: enabled
  ```

- Without automation:

  ```text
  Intercept Request
      ↓
  Add Header
      ↓
  Forward
      ↓
  Repeat for Next Request
      ↓
  Repeat Again
  ```

- With an appropriate Match and Replace rule:

  ```text
  Request
      ↓
  Burp Proxy
      ↓
  Rule Applies Automatically
      ↓
  Modified Request
      ↓
  Server
  ```

- This improves:

  * Consistency.
  * Repeatability.
  * Testing speed.
  * Reduction of manual editing mistakes.

---

## Where Match and Replace Fits

- Match and Replace operates as part of the Proxy traffic-processing workflow.

  ```text
  Browser
      │
      ▼
  Burp Proxy
      │
      ├── Interception
      │
      ├── Match and Replace
      │
      └── Other Configured Processing
      │
      ▼
  Target Application
  ```

- Because transformations may occur automatically, the traffic leaving Burp may differ from what the browser originally generated.

- That difference must be understood when interpreting application behavior.

---

## Accessing Match and Replace

- In current Burp Suite versions, Match and Replace configuration is available through Proxy-related settings.

- Interface names and locations can change between Burp releases.

- Rather than relying only on memorized menu positions, understand the underlying configuration model:

  ```text
  Proxy Settings
      ↓
  Match and Replace Rules
      ↓
  Define Transformation
      ↓
  Enable Rule
      ↓
  Validate Result
  ```

- Always verify the behavior in the Burp version you are using.

---

## Anatomy of a Rule

- A Match and Replace rule typically contains several important elements.

### Rule Type

- Determines which part of the HTTP message the rule affects.

- Examples may include supported request or response headers and body content.

### Match

- Defines the content Burp should identify.

- Matching may be:

  * Literal.
  * Pattern-based where supported.
  * Empty in specific rule types where the intention is to add content.

### Replace

- Defines the replacement content.

- Depending on the rule, this may:

  * Replace existing text.
  * Insert content.
  * Remove matched content when the replacement is empty.

### Enabled State

- Determines whether the rule is currently active.

- A correctly configured but disabled rule does nothing.

---

## Basic Rule Model

```text
Message
    ↓
Select Applicable Message Component
    ↓
Search for Match Condition
    │
    ├── No Match → Leave Unchanged
    │
    └── Match
          ↓
      Apply Replacement
          ↓
      Continue Processing
```

- This model is simple, but multiple active rules can create more complex behavior.

---

## Adding a Request Header

- One common use case is automatically adding a header to matching requests.

- Example testing header:

  ```http
  X-Security-Test: authorized
  ```

- Conceptually:

  ```text
  Original Request
      ↓
  Match and Replace Rule
      ↓
  Add Header
      ↓
  Modified Request
  ```

- Example:

  ```http
  GET /account HTTP/1.1
  Host: example.test
  ```

- After transformation:

  ```http
  GET /account HTTP/1.1
  Host: example.test
  X-Security-Test: authorized
  ```

- After creating the rule, verify that:

  * The header appears only where intended.
  * Existing headers are not accidentally overwritten.
  * The target receives the expected value.
  * The rule does not affect unrelated traffic.

---

## Replacing a Request Header Value

- Match and Replace can also help when a consistent header transformation is required.

- Example conceptual transformation:

  ```text
  Existing Header Value
          ↓
  Match
          ↓
  Replacement Value
  ```

- Before:

  ```http
  X-Environment: production
  ```

- After:

  ```http
  X-Environment: test
  ```

- This can be useful in controlled environments where specific header-driven behavior is intentionally being tested.

- Do not assume a changed header proves a security issue.

- Always validate:

  ```text
  Request Modification

  +

  Server Behavior

  +

  Security Boundary

  +

  Reproducible Impact
  ```

---

## Removing a Header

- Some investigations require determining whether a header affects application behavior.

- A controlled test may compare:

  ```text
  Baseline Request
      ↓
  Header Present

  versus

  Modified Request
      ↓
  Header Removed
  ```

- Depending on the supported rule configuration, matching content can be replaced with an empty value to remove it.

- Examples of legitimate testing questions include:

  * Does the application require this header?
  * Is the header security-sensitive?
  * Does server behavior change when it is absent?
  * Is enforcement performed server-side?

- Change one variable at a time.

---

## Response Transformations

- Match and Replace can also be useful for controlled response-side transformations where supported.

- A response-side rule changes what the client receives through Burp.

  ```text
  Server Response
      ↓
  Burp Proxy
      ↓
  Response Transformation
      ↓
  Browser
  ```

- This is especially important to interpret correctly.

- Modifying a response in Burp does **not** mean the server itself is vulnerable.

- For example:

  ```text
  Server Sends Restrictive Value
      ↓
  Burp Locally Changes Response
      ↓
  Browser Behaves Differently
  ```

- This proves that the client received modified data.

- It does not automatically prove that an attacker can make the server send that modified data to another user.

---

## Client-Side Observation vs Server-Side Vulnerability

- A critical testing principle is:

  ```text
  Local Response Modification

  ≠

  Server-Side Security Failure
  ```

- Match and Replace can help investigate hypotheses, but conclusions require server-side validation.

- Ask:

  * Did the server accept unauthorized input?
  * Did server-side state change?
  * Was an authorization boundary violated?
  * Can the behavior be reproduced without relying on a tester-controlled local transformation?
  * Is there meaningful security impact?

---

## Literal Matching

- Literal matching is appropriate when the target text is stable and exact.

- Conceptual example:

  ```text
  Match:
  X-Mode: normal

  Replace:
  X-Mode: test
  ```

- Literal rules are generally easier to understand and troubleshoot.

- Prefer the simplest matching strategy that reliably satisfies the testing requirement.

---

## Pattern-Based Matching

- More flexible matching may be useful when values change dynamically.

- Conceptual examples include:

  ```text
  Header values with changing identifiers

  Dynamic tokens

  Variable prefixes or suffixes

  Repeated response patterns
  ```

- Flexible matching increases power but also increases risk.

- A broad pattern may modify traffic you did not intend to change.

- Before enabling a broad rule:

  1. Define exactly what should match.
  2. Test it against known examples.
  3. Check for unintended matches.
  4. Restrict the affected traffic where possible.
  5. Inspect the resulting messages.

---

## Rule Scope and Traffic Boundaries

- Automatic modification should be constrained to authorized testing traffic.

- A rule that is too broad may affect:

  * Unrelated domains.
  * Third-party services.
  * Authentication flows.
  * Static resources.
  * Background API traffic.
  * Requests you intended to preserve as baselines.

- Before enabling a rule, ask:

  ```text
  Which Traffic Should Change?

  Which Traffic Must Remain Untouched?

  How Will I Verify the Difference?
  ```

- Scope discipline is especially important when browsing multiple applications through the same Burp instance.

---

## Rule Ordering

- When multiple transformations are enabled, their interaction matters.

- Conceptually:

  ```text
  Original Message
      ↓
  Rule A
      ↓
  Modified Message
      ↓
  Rule B
      ↓
  Final Message
  ```

- Rule B may receive content already changed by Rule A.

- This can create:

  * Unexpected substitutions.
  * Duplicate headers.
  * Missing values.
  * Confusing test results.

- When troubleshooting, temporarily disable unrelated rules and test transformations individually.

---

## Hidden Automation Risk

- Match and Replace introduces **hidden automation** into traffic processing.

- You may think you are testing:

  ```text
  Browser-Generated Request
  ```

- But the server may actually receive:

  ```text
  Browser-Generated Request
          +
  Automatic Burp Modification
  ```

- This matters when:

  * Establishing baselines.
  * Reproducing findings.
  * Comparing responses.
  * Sharing requests with teammates.
  * Writing vulnerability reports.
  * Retesting after remediation.

- Always know which automatic rules are active.

---

## Baseline Contamination

- A baseline should represent expected application behavior under known conditions.

- If an automatic rule silently changes the request, the baseline may no longer be valid.

- Example:

  ```text
  Intended Baseline
      ↓
  Normal Request

  Actual Baseline
      ↓
  Normal Request
      +
  Hidden Header Modification
  ```

- This can lead to incorrect conclusions.

- Before important comparisons:

  * Review enabled rules.
  * Disable unnecessary transformations.
  * Capture a clean baseline.
  * Record required automation explicitly.

---

## Professional Workflow

- A disciplined Match and Replace workflow can follow this sequence:

  ```text
  Identify Repetitive Modification
          ↓
  Confirm It Is Necessary
          ↓
  Capture Clean Baseline
          ↓
  Create Narrow Rule
          ↓
  Enable Rule
          ↓
  Generate Controlled Test Traffic
          ↓
  Inspect Modified Message
          ↓
  Confirm Expected Server Behavior
          ↓
  Document Active Automation
          ↓
  Disable Rule When Finished
  ```

---

## Example Investigation Workflow

- Suppose an authorized test requires comparing behavior when a specific test header is present.

### Step 1 — Capture Baseline

```http
GET /profile HTTP/1.1
Host: example.test
Cookie: session=CONTROLLED_SESSION
```

- Record the normal response.

### Step 2 — Define One Variable

- Add only:

  ```http
  X-Test-Context: enabled
  ```

### Step 3 — Validate Manually First

- Send a controlled request through Repeater.

- Confirm that the modification produces the behavior you intend to investigate.

### Step 4 — Automate Only if Repetition Is Needed

- Create a narrow Match and Replace rule.

### Step 5 — Inspect Traffic

- Verify that the header is actually present in affected requests.

### Step 6 — Compare

- Compare:

  ```text
  Baseline

  versus

  Modified Request
  ```

### Step 7 — Disable the Rule

- Remove hidden state from the rest of the assessment.

---

## Match and Replace vs Manual Interception

- Manual interception is better when:

  * Testing one or a few requests.
  * Learning how a request behaves.
  * Establishing a baseline.
  * Making experimental modifications.
  * Investigating an uncertain hypothesis.

- Match and Replace is better when:

  * The same known transformation is required repeatedly.
  * Consistency matters.
  * Manual repetition would create unnecessary errors.

- A useful principle is:

  ```text
  Understand Manually First

  ↓

  Automate Repetition Second
  ```

---

## Match and Replace vs Repeater

- Repeater is designed for controlled manual request replay and modification.

- Match and Replace modifies traffic automatically as it passes through Proxy.

- Use Repeater when you need:

  * Precise request-by-request control.
  * Baseline comparisons.
  * Repeated manual experimentation.
  * Clear visibility into each change.

- Use Match and Replace when you need:

  * Consistent transformations across matching proxy traffic.
  * Reduced repetitive editing.
  * A known modification applied automatically.

---

## Match and Replace vs Extensions

- Extensions can perform more complex traffic processing.

- Match and Replace is preferable when the requirement is simple enough to express with a built-in rule.

- Prefer:

  ```text
  Simplest Reliable Mechanism
  ```

- Avoid introducing unnecessary complexity when a native feature already solves the problem.

---

## Troubleshooting

- If a rule does not behave as expected, investigate systematically.

### Rule Is Disabled

- Confirm the rule is enabled.

### Match Is Incorrect

- Check:

  * Exact spelling.
  * Capitalization assumptions.
  * Whitespace.
  * Header formatting.
  * Pattern behavior.

### Wrong Message Component

- Confirm the rule targets the intended request or response area.

### Another Rule Interferes

- Disable other transformations temporarily.

### Traffic Does Not Pass Through the Expected Proxy Path

- Confirm the request is actually passing through the Burp listener where the rule applies.

### Scope Is Broader or Narrower Than Expected

- Review the traffic affected by the configuration.

### Expected Change Is Not Visible

- Inspect the actual request or response rather than assuming the transformation occurred.

---

## Verification Checklist

- After creating a rule, verify:

  ```text
  [ ] Correct rule type selected
  [ ] Match condition is precise
  [ ] Replacement value is correct
  [ ] Rule is enabled
  [ ] Intended traffic is affected
  [ ] Unrelated traffic is not affected
  [ ] No duplicate content is introduced
  [ ] Baseline remains available
  [ ] Resulting request or response is inspected
  [ ] Rule is documented if required
  [ ] Rule is disabled when no longer needed
  ```

---

## Common Mistakes

* Creating overly broad match patterns.
* Forgetting that a rule is enabled.
* Modifying unrelated traffic.
* Allowing automation to contaminate a baseline.
* Enabling several interacting rules without testing them individually.
* Assuming the browser-generated request is identical to what the server receives.
* Treating a locally modified response as proof of a server-side vulnerability.
* Automating a transformation before understanding the manual behavior.
* Forgetting to disable temporary rules after testing.
* Failing to document automatic modifications needed to reproduce a result.

---

## Professional Tips

* Keep rules as narrow as possible.
* Use descriptive rule names where the interface supports them.
* Establish clean baselines before enabling automation.
* Test one rule independently before combining transformations.
* Inspect actual modified traffic.
* Disable temporary rules immediately after use.
* Record required transformations in assessment notes.
* Prefer manual testing when the hypothesis is still uncertain.
* Use automation to improve consistency, not to replace reasoning.

---

## Field Notes

- Match and Replace is most valuable when it removes repetitive work without hiding the testing logic.

- A professional tester should always be able to answer:

  ```text
  What changed?

  Why was it changed?

  Which traffic was affected?

  Was the change automatic?

  How was the result verified?

  Can the behavior be reproduced?
  ```

- If those questions cannot be answered clearly, the automation is making the workflow less reliable rather than more efficient.

---

## Practice Tasks

1. Open Match and Replace configuration in Burp Proxy.
2. Review the available rule types in your installed Burp version.
3. In an authorized lab, capture a clean baseline request.
4. Create a harmless rule that adds a custom testing header.
5. Generate a request and verify the header was added.
6. Disable the rule and confirm the request returns to baseline behavior.
7. Create a controlled replacement rule for a non-security-sensitive test value.
8. Compare the original and transformed messages.
9. Test how two enabled rules interact.
10. Disable all temporary rules when finished.
11. Record the rule purpose and observed behavior in your notes.

---

## Interview Questions

1. What is Match and Replace in Burp Suite?
2. Why would you use Match and Replace instead of manually editing every request?
3. What kinds of traffic transformations can Match and Replace support?
4. Why should rules be narrowly scoped?
5. How can an enabled rule contaminate a testing baseline?
6. Why does rule ordering matter?
7. What is the difference between Match and Replace and Repeater?
8. Why should a transformation usually be validated manually before automating it?
9. Why does modifying a response locally not automatically prove a vulnerability?
10. How would you troubleshoot a Match and Replace rule that appears not to work?
11. What should you document when automatic traffic modification is required to reproduce a finding?
12. Why should temporary Match and Replace rules be disabled after testing?

---

## Quick Revision

```text
Match and Replace
    ↓
Automatically Transforms Matching Proxy Traffic

Primary Uses
    ↓
Add
Replace
Remove
Normalize

Professional Workflow
    ↓
Baseline
→ Define One Transformation
→ Create Narrow Rule
→ Validate
→ Inspect
→ Compare
→ Document
→ Disable

Key Risk
    ↓
Hidden Automation

Remember
    ↓
Local Modification
≠
Server-Side Vulnerability

Best Principle
    ↓
Understand Manually First
Automate Repetition Second
```

---

## Key Takeaways

- Match and Replace automates controlled transformations of matching traffic passing through Burp Proxy.
- It is useful when the same known modification must be applied repeatedly and consistently.
- Rules should be precise, limited to intended traffic, and validated before being trusted.
- Automatic transformations can contaminate baselines and complicate troubleshooting if they are forgotten.
- Multiple rules may interact, so ordering and rule isolation matter.
- Response modification inside Burp does not by itself demonstrate a server-side security vulnerability.
- Manual understanding should come before automation.
- Temporary rules should be documented when relevant and disabled when they are no longer required.
