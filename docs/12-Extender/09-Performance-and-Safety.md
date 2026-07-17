# Performance & Safety

> **Module Progress**

```text id="p8k4ya"
█████████░░░░░░ 9 / 15 Lessons
```

- Every extension you install becomes part of your Burp Suite environment.

- While extensions can significantly improve productivity, they also consume system resources and may introduce additional complexity.

- Professional testers balance capability with stability.

---

## Performance Considerations

- Extensions may affect:

  * Memory usage
  * CPU utilization
  * Burp startup time
  * Interface responsiveness
  * Overall workflow efficiency

- One well-designed extension may have little noticeable impact, while several poorly optimized extensions can slow Burp considerably.

---

## Keep Your Toolkit Lean

- Install extensions because they solve a real problem—not because they are available.

- A smaller toolkit is generally:

  * Faster
  * Easier to maintain
  * Easier to troubleshoot  
  * Simpler to understand
  
- Removing unused extensions is part of good tool hygiene.

---

## Review Compatibility

- Before relying on an extension, verify that it is compatible with:

  * Your Burp Suite version
  * Your operating system  
  * Required runtime components
  * Other extensions in your environment

- Compatibility issues can lead to unexpected behavior or failed functionality.

---

## Understand What an Extension Does

- Before enabling an extension, understand:

  * What data it processes
  * Which Burp features it interacts with
  * What changes it may make
  * Whether it performs automated actions

- Never use a tool you do not understand in a security assessment.

---

## Troubleshooting

- If Burp begins behaving unexpectedly:

  1. Review extension logs.
  2. Disable recently installed extensions.
  3. Confirm compatibility.
  4. Check configuration.
  5. Re-enable extensions one at a time if needed.

- A systematic approach makes identifying the cause much easier.

---

## Safe Practices

- Professional testers commonly:

  * Install only required extensions.
  * Keep extensions updated when appropriate.
  * Remove obsolete tools.
  * Validate important findings manually.
  * Review their extension environment regularly.

- These habits reduce unnecessary risk during engagements.

---

## Decision Workflow

```text id="f7v2xn"
Need New Capability

↓

Evaluate Extension

↓

Compatible?

     │
 No  │ Yes
     ▼
Find Alternative

↓

Install

↓

Monitor Performance

↓

Review Regularly

↓

Remove if Unnecessary
```

- Every installation should be an informed decision rather than an impulse.

---

## Extension Selection Challenge

- You find an extension that promises to automate multiple testing tasks.

- However:

  * It has not been updated for several years.
  * It requires numerous dependencies.
  * It noticeably slows Burp during testing.

- Ask yourself:

  * Does the benefit justify the added complexity?
  * Is there a simpler alternative?
  * Can the task be completed effectively using built-in tools?

- Professional testers prioritize reliability over convenience.

---

## Common Mistakes

* Installing every recommended extension.
* Ignoring compatibility requirements.
* Trusting automated output without verification.
* Leaving unused extensions installed.
* Troubleshooting randomly instead of systematically.

Avoiding these habits leads to a more stable and predictable testing environment.

---

## Key Takeaways

* Every extension consumes resources.
* Keep your extension list focused and purposeful.
* Review compatibility before relying on an extension.
* Troubleshoot methodically.
* A stable environment is often more valuable than a feature-rich one.
