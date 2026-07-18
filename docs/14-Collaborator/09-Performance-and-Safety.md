# Performance & Safety

> **Module Progress**

```text
█████████░░░░░░ 9 / 15 Lessons
```

- Burp Collaborator enables testers to observe external interactions generated during application security testing.

- Because these interactions may leave the target environment and reach external infrastructure, OAST testing requires careful planning.

- Professional testers balance investigative value with:

  * Authorization
  * Privacy
  * Operational safety
  * Data minimization
  * Evidence integrity

---

## Confirm Authorization First

- Before performing OAST testing, confirm that the engagement permits it.

- Review:

  * Authorized targets
  * Testing boundaries  
  * Third-party restrictions
  * Production limitations
  * Rules of engagement

- Authorization to test an application does not automatically mean every testing technique is permitted.

---

## Understand External Communication

- Traditional testing may remain entirely between:

  ```text
  Tester ↔ Target
  ```

- OAST may introduce another communication path:

  ```text
  Tester
     ↓
  Target Application
     ↓
  External OAST Infrastructure
  ```

- This matters because application-generated traffic may leave the organization's network.

- Always understand the implications before testing.

---

## Minimize Sensitive Data Exposure

- Do not unnecessarily place sensitive information inside Collaborator identifiers or test inputs.

- Avoid embedding data such as:

  * Credentials
  * Session tokens
  * Personal information
  * Confidential business data
  * Internal secrets

- Use minimal, controlled identifiers sufficient for correlation.

---

## Payload Hygiene

- Good payload management improves both safety and evidence quality.

- Prefer:

  ```text
  Test A → Unique Identifier A

  Test B → Unique Identifier B
  ```

- Avoid unnecessarily encoding sensitive contextual information into identifiers.

- Your payload only needs enough uniqueness to support reliable correlation.

---

## Consider Production Impact

- OAST testing may trigger backend behavior such as:

  * DNS resolution
  * Network connections
  * Background processing
  * Logging
  * Security monitoring

- Before testing production systems, consider:

  * Whether the test could trigger expensive processing.
  * Whether repeated interactions could create unnecessary load.
  * Whether monitoring systems may generate alerts.
  * Whether testing should occur during an approved window.

- Use the minimum activity necessary to answer the security question.

---

## Avoid Unnecessary Repetition

- Reproducibility strengthens findings, but repeated testing should remain controlled.

- Do not generate excessive interactions merely to accumulate evidence.

- A better principle is:

  > **Generate enough evidence to establish confidence—then stop.**

- This reduces unnecessary traffic and operational impact.

---

## Respect Third-Party Boundaries

- Applications may integrate with external services.

- Be cautious when testing workflows involving:

  * External APIs
  * SaaS platforms
  * Email infrastructure
  * Cloud services
  * Partner systems

- Your authorization may cover the target application but not unrelated third-party infrastructure.

- Keep testing within approved boundaries.

---

## Protect Collaborator Evidence

- Interaction records may contain useful investigative metadata.

- Treat them as assessment evidence.

- Good practices include:

  * Recording only necessary information.
  * Protecting screenshots and exported evidence.
  * Avoiding unnecessary disclosure of identifiers.
  * Following engagement data-retention requirements.

- Evidence handling is part of professional security testing.

---

## Public vs Private Infrastructure

- Depending on the engagement, organizations may have different requirements for OAST infrastructure.

- Sensitive environments may restrict the use of externally hosted interaction services.

- Before testing, determine whether:

  * Public Collaborator infrastructure is permitted.
  * Organization-controlled infrastructure is required.
  * External callbacks are prohibited entirely.

- The testing method must fit the engagement.

---

## Monitor for Delayed Interactions

- OAST behavior may occur after the original test.

- Consider:

  ```text
  Test Submitted  
  
  ↓
  
  Immediate Processing

  ↓

  Background Queue

  ↓

  Delayed Interaction
  ```

- Continue polling for a reasonable period when asynchronous behavior is expected.

- However, avoid indefinite or unnecessary monitoring.

---

## Safe Testing Workflow

```text
Confirm Authorization

↓

Understand Data Flow

↓

Minimize Sensitive Data

↓

Create Controlled Unique Payload

↓

Perform Minimal Necessary Test

↓

Monitor Interactions

↓

Validate Evidence

↓

Stop When Sufficient

↓

Protect Documentation
```

- Safety should be built into the methodology from the beginning.

---

## Collaborator Challenge

- You are assessing a production application that processes uploaded documents asynchronously.

- You want to investigate possible out-of-band behavior.

- Before testing, ask:

  * Does the engagement permit external callbacks?
  * Could sensitive document contents leave the environment?
  * Is public OAST infrastructure acceptable?
  * How many tests are actually necessary?
  * Could background processing continue after testing ends?
  * How will evidence be stored securely?

- Technical possibility does not automatically equal testing permission.

---

## Common Safety Mistakes

- Avoid:

  * Performing OAST without explicit authorization.
  * Embedding sensitive information in payloads.
  * Generating unnecessary repeated callbacks.
  * Ignoring third-party boundaries.
  * Assuming public OAST infrastructure is always acceptable.
  * Leaving evidence unprotected.
  * Forgetting about delayed background interactions.

---

## Professional Habits

- Experienced testers:

  * Verify authorization before testing.
  * Minimize data exposure.
  * Use unique but non-sensitive identifiers.
  * Limit unnecessary interactions.
  * Respect third-party boundaries.
  * Consider production impact.
  * Protect collected evidence.
  * Stop testing once sufficient evidence exists.

---

## Key Takeaways

* OAST can cause communication outside the target environment.
* Authorization must cover the testing technique being used.
* Sensitive information should never be exposed unnecessarily.
* Use the minimum testing necessary to establish evidence.
* Third-party infrastructure requires special caution.
* Public Collaborator infrastructure may not suit every engagement.
* Evidence must be handled securely.
