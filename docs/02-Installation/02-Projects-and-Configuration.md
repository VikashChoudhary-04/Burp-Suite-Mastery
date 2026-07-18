# Projects and Configuration

> **Module Progress**

```text id="r5w9mt"
██░░░░░░░ 2 / 9 Lessons
```

- Burp Suite does more than simply open and intercept traffic.

- It maintains information about your testing session and uses configuration settings that control how Burp behaves.

- Understanding the difference between **project data** and **configuration** helps you build predictable environments and troubleshoot unexpected behavior.

---

## The Core Mental Model

- Think of the distinction as:

  ```text id="m8q3vn"
  Project

  =

  What Burp Knows About This Testing Session
  ```

- while:

  ```text id="p4n7kx"
  Configuration

  =

  How Burp Is Instructed to Behave
  ```

- These concepts are related, but they are not the same.

---

## What Is a Burp Project?

- A project represents a Burp testing workspace.

- Depending on the edition, project type, configuration, and Burp version, project-related state may include information such as:

  * Discovered application content.
  * HTTP history.
  * Site map information.
  * Testing activity.
  * Issue information.
  * Tool-specific state.

- Conceptually:

  ```text id="q6m2wr"
  Assessment

  ↓

  Burp Project

  ↓

  Application Data

  +

  Testing State

  +

  Project-Specific Information
  ```

- A project helps keep an assessment organized.

---

## Temporary Projects

- A temporary project is useful when you do not need long-term persistence.

- Typical uses include:

  * Learning.
  * Short labs.
  * Quick experiments.
  * Temporary troubleshooting sessions.

- Conceptually:

  ```text id="v9p4mx"
  Start Burp

  ↓

  Temporary Project

  ↓

  Perform Work

  ↓

  Close Session

  ↓

  Do Not Depend on Long-Term Persistence
  ```

- Temporary projects reduce project-management complexity for beginners.

---

## Saved Projects

- Where supported by your Burp edition and workflow, persistent projects can retain assessment state for later use.

- They are useful for:

  * Longer engagements.
  * Multi-day testing.
  * Returning to previous application state.
  * Preserving organized assessment information.

- Persistent project data should be treated as sensitive.

---

## Why Project Files Are Sensitive

- A Burp project may contain security-relevant information gathered during testing.

- Potential examples include:

  * URLs.  
  * Requests and responses.
  * Session-related data.
  * Application structure.
  * Vulnerability evidence.
  * Sensitive response content.

- Treat project files like assessment evidence.

- Do not casually:

  * Upload them to public repositories.
  * Share them through insecure channels.
  * Leave them exposed on shared systems.

---

## What Is Configuration?

- Configuration controls Burp's behavior.

- Examples of configurable areas may include:

  * Proxy behavior.
  * Network connections.
  * TLS settings.
  * Tool behavior.
  * Display preferences.
  * Session handling.
  * Logging.
  * Extensions.

- The exact organization can change between Burp versions.

- Focus on the concept:

  > **Configuration determines how your Burp environment operates.**

---

## User-Level vs Project-Level Thinking

- A useful conceptual distinction is:

  ```text id="x3k8pv"
  User-Level Settings

  ↓

  How I Generally Want Burp to Behave
  ```

- versus:

  ```text id="n7q4mr"
  Project-Level Settings

  ↓

  How Burp Should Behave for This Assessment
  ```

- Some settings make sense globally.

- Others should differ between engagements.

---

## Example — UI Preference

- Suppose you change a personal interface preference.

- That is conceptually a user-level choice:

  ```text id="t5m9qw"
  Tester Preference

  ↓

  Used Across Work
  ```

- It is not necessarily specific to one target.

---

## Example — Target-Specific Behavior

- Suppose a particular assessment requires special connection or testing behavior.

- That is conceptually closer to project-specific configuration:

  ```text id="k2p7nx"
  Assessment Requirement

  ↓

  Project-Specific Configuration

  ↓

  Used for This Engagement
  ```
  
- Separating these ideas helps prevent accidental configuration leakage between projects.

---

## Why Configuration Scope Matters

- Imagine you change a setting during Assessment A.

- Later, during Assessment B, Burp behaves unexpectedly.

- The question becomes:

  > Did that setting persist globally, or was it limited to the previous project?

- Understanding configuration scope makes troubleshooting easier.

---

## Configuration Drift

- Configuration drift occurs when your environment gradually accumulates changes until you no longer remember why Burp behaves a certain way.

- For example:

  ```text id="r8m3qv"
  Change Setting A

  ↓

  Install Extension

  ↓

  Change Network Setting

  ↓

  Modify Proxy Behavior

  ↓

  Months Later

  ↓

  Unexpected Behavior
  ```

- The tester may no longer know which change caused the problem.

---

## Controlled Configuration Changes

- Use the same principle as controlled security testing:

  ```text id="w4n9pk"
  Known Working State

  ↓

  Change One Relevant Setting

  ↓

  Test Behavior

  ↓

  Document if Important

  ↓

  Keep or Revert
  ```

- Avoid changing many settings simultaneously when troubleshooting.

---

## Default Configuration as a Baseline

- Burp's default configuration provides a useful baseline.

- If something behaves unexpectedly, ask:

  ```text id="m6q2vx"
  What Did I Change?

  ↓

  Is the Problem Project-Specific?

  ↓
  
  Is the Problem User-Level?

  ↓

  Does a Clean Configuration Behave Differently?
  ```

- A known baseline helps isolate configuration problems.

---

## Reusable Configurations

- Professional testers often develop preferred Burp configurations over time.

- These may support:

  * Repeated lab environments.
  * Standard assessment workflows.
  * Team consistency.
  * Specific testing requirements.

- However, avoid blindly importing configurations you do not understand.

- A configuration can change important behavior.

---

## Configuration Import Safety

- Before using someone else's configuration, consider:

  * What settings will change?  
  * Does it affect network routing?
  * Does it alter proxy behavior?
  * Does it load extensions?
  * Is it appropriate for this engagement?

- Treat configuration files as operational instructions.

- Understand them before trusting them.

---

## Project Isolation

- Separate projects help reduce confusion between assessments.

- Prefer:

  ```text id="p9m4kn"
  Client / Lab A

  ↓

  Project A
  ```

  ```text id="y3q7mr"
  Client / Lab B

  ↓

  Project B
  ```

rather than mixing unrelated testing activity into one workspace.

- Isolation improves:

  * Organization.
  * Evidence handling.
  * Troubleshooting.
  * Reporting.

---

## Naming Projects

- For saved assessment work, use clear, non-confusing names.

- A useful pattern might be:

  ```text id="v5n8qw"
  Organization-or-Lab

  Application

  Assessment-Date-or-Identifier
  ```

- Avoid unnecessarily placing sensitive secrets or credentials in filenames.

---

## Start Simple as a Beginner

- For initial learning:

  ```text id="q7m2pt"
  Temporary Project

  +

  Default Configuration

  ↓

  Learn Core Workflow
  ```

- Do not spend hours customizing Burp before understanding the defaults.

- Customization becomes valuable when you understand what problem each change solves.

---

## Troubleshooting Model

- If Burp behaves differently than expected, ask:

  ```text id="n4k9vx"
  Is Burp Installed Correctly?

  ↓

  Is This a Project-State Issue?

  ↓  

  Is This a Project Configuration Issue?

  ↓

  Is This a User-Level Setting?

  ↓

  Is an Extension Affecting Behavior?

  ↓

  Can I Reproduce It From a Clean Baseline?
  ```

- This is much stronger than immediately reinstalling the application.

---

## Professional Environment Principle

- Aim for:

  ```text id="t8p3mw"
  Predictable

  +

  Documented

  +

  Isolated

  +

  Reproducible
  ```

- A professional Burp setup should behave intentionally.

- You should know why important settings exist.

---

## Configuration Challenge

- Suppose Burp behaves normally in a fresh temporary project but unexpectedly in an older assessment project.

- What does this suggest?

  - The problem may be related to:

    * Project-specific state.
    * Project-specific configuration.
    * Assessment-specific settings.

- A complete reinstall would probably be an unnecessarily broad first response.

- Compare the working and failing environments systematically.

---

## Key Takeaways

* A project represents a testing workspace and its associated state.
* Configuration controls how Burp behaves.
* Temporary projects are useful for short learning sessions.
* Persistent project data may contain sensitive assessment information.
* Think separately about user-level and project-level configuration.
* Configuration drift can cause unexpected behavior.
* Change settings deliberately and test after each important change.
* Use default configuration as a troubleshooting baseline.
* Keep unrelated assessments isolated.
* A predictable environment is more valuable than excessive customization.

