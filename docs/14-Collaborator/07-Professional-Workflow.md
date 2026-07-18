# Professional Workflow

> **Module Progress**

```text id="r5n9qt"
███████░░░░░░░░ 7 / 15 Lessons
```

- Professional out-of-band testing is a structured investigation.

- The goal is not simply to generate Collaborator interactions.

- The goal is to determine:

  * What behavior occurred.
  * What triggered it.
  * Whether it is reproducible.
  * Whether it creates a meaningful security impact.

- A disciplined workflow produces stronger evidence and fewer incorrect conclusions.

---

## Step 1 — Define the Security Hypothesis

- Begin with a specific question.

- For example:

  > **"Does this server-side feature process user-controlled external references?"*  *

- A clear hypothesis helps determine whether OAST is appropriate.

- Do not introduce Collaborator simply because an input field exists.

---

## Step 2 — Understand the Application Flow

- Before testing, determine:

  * Where the input goes.
  * Whether processing appears server-side.
  * Whether the operation may be asynchronous.
  * Which functionality is relevant.
  * What normal behavior looks like.

- Understanding the workflow makes later evidence easier to interpret.

---

## Step 3 — Confirm Authorization and Scope

- Before causing any external interaction, verify that the test is permitted.

- Confirm:

  * The target is authorized.
  * The feature is within scope.
  * Out-of-band testing is allowed.
  * The testing method respects engagement constraints.

- Authorization comes before experimentation.

---

## Step 4 — Create Strong Correlation

- Use a unique Collaborator payload for the specific test whenever practical.

- Record:

  * The payload used.
  * The original request.
  * The tested feature.
  * The time of submission.
  * Any immediate response.

- Conceptually:

  ```text id="m7q2vp"
  Test Case

  ↓

  Unique Payload

  ↓

  Recorded Request

  ↓

  Timestamp

  ↓

  Possible Interaction
  ```

- Good correlation begins before the interaction occurs.

---

## Step 5 — Introduce the Payload

- Use the payload only in the authorized input or workflow being investigated.

- Then record what happens in the normal request-response channel.

- Ask:

  * Did the application accept the input?
  * Was there an error?
  * Was processing immediate?
  * Could processing continue in the background?

- The direct response remains part of the evidence.

---

## Step 6 — Poll for Interactions

- Check Collaborator for associated activity.

- Do not assume the first poll tells the complete story.

- Possible outcomes include:

  ```text id="x4k8nr"
  No Interaction

  DNS Only

  HTTP Interaction

  Multiple Interaction Types

  Delayed Interaction
  ```

- Each outcome requires different interpretation.

---

## Step 7 — Correlate the Evidence

- If an interaction appears, compare it with:

  * The unique payload.
  * Submission time.
  * Original request.
  * Tested feature.
  * Interaction type.
  * Other related activity.

- Ask:

  > **"How confidently can I connect this interaction to my test?"**

- Strong findings require strong correlation.

---

## Step 8 — Form the Smallest Supported Conclusion

- Avoid immediately assigning a vulnerability name.

- Start with what the evidence directly supports.

- For example:

  ```text id="p3w9qm"
  Observation:
  A unique hostname triggered DNS activity.

  ↓

  Supported Inference:
  A server-side or related component processed the hostname.

  ↓

  Further Investigation:
  Determine why, how, and with what security impact.
  ```

- Make the smallest claim that the evidence can defend.

---

## Step 9 — Validate and Reproduce

- For significant behavior, determine whether it can be reproduced safely and within authorization.

- Reproduction helps answer:

  * Was the interaction caused by the tested feature?
  * Does the behavior occur consistently?
  * Can unrelated explanations be ruled out?
  * Does the evidence support the suspected vulnerability class?

- Reproducibility increases confidence.

---

## Step 10 — Assess Security Impact

- An interaction alone does not define severity.

- Investigate:

  * What capability the behavior exposes.
  * What security boundary may be affected.
  * Whether sensitive systems or data could be impacted.
  * What realistic attacker control exists.

- Impact determines why the behavior matters.

---

## Step 11 — Document the Evidence

- Maintain clear records of:

  * Tested functionality.
  * Relevant requests and responses.
  * Unique Collaborator identifiers.
  * Interaction types.
  * Timestamps.
  * Reproduction results.
  * Security impact.
  * Limitations or uncertainty.

- Another tester should be able to understand how you reached your conclusion.

---

## Complete Workflow

```text id="n8v4rx"
Define Hypothesis

↓

Understand Application

↓

Confirm Authorization

↓

Create Unique Payload

↓

Submit Controlled Test

↓

Poll for Interactions

↓

Correlate Evidence

↓

Validate & Reproduce

↓

Assess Impact

↓

Report Confirmed Finding
```

- Collaborator is one component of the investigation—not the entire methodology.

---

## No Interaction Workflow

- What if nothing appears?

- Do not immediately conclude:

  > "Not vulnerable."

- Instead consider:

  ```text id="q6m2kt"
  No Interaction

  ↓  
  
  Was the Correct Code Path Reached?

  ↓
  
  Could Processing Be Delayed?

  ↓

  Could Outbound Communication Be Restricted?

  ↓

  Was the Test Appropriate?

  ↓

  Continue Manual Investigation
  ```

- Negative OAST evidence must also be interpreted carefully.

---

## Collaborator Challenge

- You test an authorized URL-processing feature with a unique Collaborator payload.

- Results:

  1. The application immediately returns a generic success message.
  2. Fifteen seconds later, Collaborator records DNS activity.
  3. No HTTP interaction appears.

- Before reporting anything, ask:

  * What can you directly prove?
  * What might explain the DNS interaction?
  * What can you *not* yet claim?
  * How would you strengthen correlation?
  * What additional validation is required to establish security impact?

- Your report should never be stronger than your evidence.

---

## Professional Habits

- Experienced testers:

  * Start with a hypothesis.
  * Understand the feature before testing.
  * Confirm authorization.
  * Use unique payloads.
  * Record timestamps and requests.
  * Account for delayed processing.
  * Make evidence-supported claims.
  * Reproduce significant behavior when appropriate.
  * Assess impact separately from interaction strength.

---

## Key Takeaways

* Professional OAST begins with a clear security hypothesis.
* Strong correlation requires deliberate test design.
* Direct responses and out-of-band interactions should be analyzed together.
* Use the smallest conclusion supported by the evidence.
* Reproducibility strengthens confidence.
* Interaction strength does not automatically determine severity.
* Only validated, impactful behavior should become a confirmed finding.
