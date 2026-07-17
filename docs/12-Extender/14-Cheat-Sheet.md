# Cheat Sheet

> **Module Progress**

```text id="k5w9rb"
██████████████░ 14 / 15 Lessons
```

- This cheat sheet summarizes the key ideas, workflows, and decision-making principles from the Extender module.

- Keep it nearby as a quick reference during assessments, interviews, and Burp environment maintenance.

---

## Extension Workflow

```text id="z7q1nv"
Security Objective

↓

Built-in Tool Sufficient?

     │
 Yes │ No
     ▼
Continue     Evaluate Extension

             ↓

      Install & Configure

             ↓

      Validate Results

             ↓

     Maintain or Remove
```

- Always begin with the objective—not the extension.

---

## Extension Lifecycle

```text id="b2m8xt"
Discover

↓

Evaluate

↓

Install

↓

Configure

↓

Use

↓

Maintain

↓

Update or Remove
```

- Every extension should be managed throughout its lifecycle.

---

## Core Concepts

| Concept       | Purpose                                        |
| ------------- | ---------------------------------------------- |
| Extension     | Adds new functionality to Burp Suite           |
| BApp Store    | Curated catalog of Burp extensions             |
| Compatibility | Ensures an extension works correctly           |
| Dependencies  | Supporting components required by an extension |
| Configuration | Adjusts extension behavior                     |
| Maintenance   | Keeps the toolkit reliable and efficient       |

---

## Before Installing an Extension

- Ask:

  * What problem am I solving?
  * Can Burp already solve it?
  * Is this extension actively maintained?
  * Is it compatible with my environment?
  * What resources will it consume?
  * How will I validate its output?

- If you cannot answer these questions, pause before installing.

---

## Professional Workflow

1. Define the objective.
2. Use built-in features if sufficient.
3. Evaluate candidate extensions.
4. Install intentionally.
5. Configure carefully.
6. Validate important findings.
7. Review your toolkit regularly.

---

## Troubleshooting Checklist

- If an extension behaves unexpectedly:

  * Review logs.
  * Verify compatibility.
  * Check configuration.
  * Disable recent extensions.
  * Test one change at a time.
  * Confirm the issue is reproducible.

- Avoid making multiple changes simultaneously.

---

## Common Mistakes

* Installing too many extensions.
* Blindly trusting automation.
* Ignoring documentation.
* Skipping compatibility checks.
* Never reviewing the toolkit.
* Automating before understanding the manual process.

---

## Professional Habits

- Experienced testers:

  * Solve problems before choosing tools.
  * Keep their toolkit focused.
  * Validate extension output.
  * Remove unused extensions.
  * Review compatibility regularly.
  * Document important configuration changes.

---

## Decision Tree

```text id="w8n4jp"
Need New Capability?

↓

Built-in Feature Enough?

     │
 Yes │ No
     ▼
Use It      Evaluate Extension

            ↓

   Worth the Complexity?

     │
 No  │ Yes
     ▼
Skip It     Install

             ↓

      Validate & Maintain
```

- The simplest effective solution is usually the best one.

---

## Interview Revision

- Be prepared to explain:

  * Why Burp Extender exists.
  * What the BApp Store provides.
  * The extension lifecycle.
  * Compatibility and dependencies.
  * How to evaluate an extension.
  * Why manual validation remains important.

- Focus on *reasoning*, not memorizing extension names.

---

## Golden Rules

* Solve the problem first.
* Install with purpose.
* Understand before automating.
* Validate automation.
* Keep the toolkit lean.
* Review your environment regularly.
