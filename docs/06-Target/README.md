# Target Tool

## Overview

- The Target tool is Burp Suite's workspace for organizing discovered application content, defining testing scope, and building a structured understanding of the application's attack surface.

- This module develops Target from a basic Site Map viewer into a professional application-mapping workflow.

- You will learn how to:

  * Understand the Target interface and architecture.
  * Configure and manage scope safely.
  * Navigate and analyze the Site Map.
  * Discover and organize application content.
  * Map endpoints, APIs, objects, roles, and workflows.
  * Analyze attack surface and prioritize high-value areas.
  * Build coverage matrices and security hypotheses.
  * Integrate Target with Proxy and Repeater.
  * Troubleshoot incomplete or misleading Target data.
  * Apply Target during penetration tests and bug bounty assessments.

---

## Learning Objectives

- By completing this module, you should be able to:

  * Explain the purpose of the Target tool.
  * Understand how Burp builds and updates the Site Map.
  * Distinguish Burp scope from actual testing authorization.
  * Configure scope according to explicit authorization.
  * Classify observed hosts safely.
  * Navigate large Site Maps efficiently.
  * Build a functional application map.
  * Create structured endpoint inventories.
  * Group related endpoints into endpoint families.
  * Map objects, ownership, and relationships.
  * Compare unauthenticated and authenticated attack surfaces.
  * Compare authorized user roles and privilege boundaries.
  * Map APIs and business workflows.
  * Identify state-changing and security-sensitive operations.
  * Track testing coverage systematically.
  * Prioritize high-value attack surface.
  * Convert observations into testable security hypotheses.
  * Select requests for controlled testing in Repeater.
  * Troubleshoot missing, noisy, or incomplete Target data.

---

## Why the Target Tool Matters

- Before testing an application effectively, you need to understand what exists.

- A modern application may contain:

  * Multiple hosts.
  * Hundreds or thousands of routes.
  * API endpoints.
  * Different authentication states.
  * Multiple user roles.
  * Business workflows.
  * Object relationships.
  * JavaScript-driven functionality.
  * Third-party dependencies.

- Testing randomly creates coverage gaps and duplicated effort.

- Target helps transform observed traffic into an organized application model.

```text
Observe Traffic
      │
      ▼
Build Site Map
      │
      ▼
Understand Structure
      │
      ▼
Map Attack Surface
      │
      ▼
Prioritize
      │
      ▼
Create Hypotheses
      │
      ▼
Perform Controlled Testing
```

---

## Core Mental Model

```text
Target
│
├── Site Map
│     │
│     ├── Hosts
│     ├── Directories
│     ├── Pages
│     ├── APIs
│     ├── Parameters
│     ├── JavaScript
│     └── Resources
│
└── Scope
      │
      └── Defines what Burp should treat as relevant
```

- The Site Map answers:

  ```text
  What has Burp learned about the application?
  ```

- Scope answers:

  ```text
  What should Burp treat as relevant to this assessment?
  ```

- Always remember:

  ```text
  Burp Scope ≠ Authorization
  ```

---

## Module Structure

```text
06-Target/
│
├── README.md
├── 01-Introduction.md
├── 02-Site-Map-and-Application-Structure.md
├── 03-Scope-Configuration-and-Management.md
├── 04-Attack-Surface-Mapping.md
├── 05-Content-Discovery-and-Endpoint-Analysis.md
├── 06-Target-Organization-and-Investigation-Workflow.md
├── 07-Professional-Target-Workflow.md
├── 08-Common-Mistakes-and-Troubleshooting.md
├── 09-Practical-Exercises.md
├── 10-Interview-Questions.md
└── 11-Quick-Revision.md
```

---

## Lesson 01 — Introduction

- Introduces the Target tool and establishes its role in professional web application testing.

- Covers:

  * Purpose of Target.
  * Site Map and scope.
  * Application mapping.
  * Attack-surface awareness.
  * Target's place in the Burp workflow.
  * Core professional use cases.

- Main idea:

  ```text
  Understand the Application Before Testing It Deeply
  ```

---

## Lesson 02 — Interface and Target Architecture

- Explains how the Target workspace is structured and how its components relate to Burp's traffic-processing workflow.

- Covers:

  * Target interface.
  * Site Map organization.
  * Scope controls.
  * Request and response context.
  * Relationship with Proxy.
  * How observed traffic becomes structured Target data.

- Main idea:

  ```text
  Traffic → Burp Processing → Target Organization
  ```

---

## Lesson 03 — Site Map

- Develops practical mastery of Burp's hierarchical application map.

- Covers:

  * Site Map population.
  * Hosts and paths.
  * Request organization.
  * Dynamic application content.
  * Authenticated and unauthenticated mapping.
  * Interpreting incomplete Site Maps.
  * Using Site Map as an investigation workspace.

- Main idea:

  ```text
  Site Map = Evolving Model of Observed Application Content
  ```

---

## Lesson 04 — Scope and Scope Management

- Covers safe and precise scope configuration.

- Topics include:

  * Scope concepts.
  * Include and exclude rules.
  * Host, protocol, port, and path considerations.
  * Scope-based filtering.
  * Third-party dependencies.
  * Unknown hosts.
  * Scope validation.
  * Authorization boundaries.

- Critical rule:

  ```text
  Technical Scope Must Follow Explicit Authorization
  ```

---

## Lesson 05 — Content Discovery and Endpoint Analysis

- Moves beyond simple URL collection into structured endpoint understanding.

- Covers:

  * Content-discovery sources.
  * Endpoint inventories.
  * HTTP methods.
  * Parameters and inputs.
  * Endpoint families.
  * API routes.
  * JavaScript-derived leads.
  * Authentication requirements.
  * State-changing operations.
  * Endpoint context.

- Main idea:

  ```text
  URL Discovery
       │
       ▼
  Endpoint Context
       │
       ▼
  Security-Relevant Understanding
  ```

---

## Lesson 06 — Attack Surface Mapping and Investigation

- Builds a complete security model from discovered application content.

- Covers:

  * Functional mapping.
  * Object mapping.
  * Object ownership.
  * CRUD operations.
  * Role mapping.
  * Authentication states.
  * Trust boundaries.
  * Business workflows.
  * Sensitive data flows.
  * Coverage matrices.
  * Prioritization.
  * Security hypotheses.

- Main idea:

  ```text
  Map Structure
      +
  Map Context
      +
  Map Trust Boundaries
      =
  Actionable Attack Surface
  ```

---

## Lesson 07 — Professional Target Workflow

- Combines the previous lessons into a repeatable assessment methodology.

- Covers:

  * Authorization confirmation.
  * Scope setup.
  * Baseline browsing.
  * Site Map population.
  * Functional mapping.
  * Endpoint and API analysis.
  * Role and object mapping.
  * Coverage tracking.
  * Prioritization.
  * Hypothesis creation.
  * Target-to-Repeater workflow.
  * Validation and documentation.

- Core workflow:

  ```text
  Authorize
      │
      ▼
  Scope
      │
      ▼
  Browse
      │
      ▼
  Map
      │
      ▼
  Organize
      │
      ▼
  Prioritize
      │
      ▼
  Hypothesize
      │
      ▼
  Test
      │
      ▼
  Validate
      │
      ▼
  Document
  ```

---

## Lesson 08 — Common Mistakes and Troubleshooting

- Covers errors that commonly reduce Target effectiveness.

- Topics include:

  * Treating Site Map as complete.
  * Confusing observed hosts with authorized hosts.
  * Overly broad scope.
  * Overly narrow scope.
  * Missing authenticated content.
  * Missing role-specific content.
  * Third-party noise.
  * Ignoring HTTP methods.
  * Losing object ownership context.
  * Ignoring application state.
  * Incorrect interpretation of status codes.
  * Empty Site Maps.
  * Missing endpoints.
  * Missing API hosts.
  * Troubleshooting workflows.

- Main idea:

  ```text
  Troubleshoot Systematically
  ```

---

## Lesson 09 — Practical Exercises

- Provides hands-on exercises that build Target proficiency progressively.

- Exercises include:

  * Observing Site Map population.
  * Configuring scope.
  * Classifying hosts.
  * Building functional maps.
  * Creating endpoint inventories.
  * Mapping endpoint families.
  * Mapping objects.
  * Comparing authentication states.
  * Comparing roles.
  * Mapping business workflows.
  * Analyzing APIs.
  * Reviewing JavaScript-derived leads.
  * Building coverage matrices.
  * Prioritizing attack surface.
  * Creating security hypotheses.
  * Sending requests to Repeater.
  * Troubleshooting missing endpoints.
  * Performing a complete Target investigation.

---

## Lesson 10 — Interview Questions

- Provides interview preparation from foundational concepts through professional scenarios.

- Covers:

  * Target fundamentals.
  * Site Map.
  * Scope.
  * Authorization.
  * Host classification.
  * Attack-surface mapping.
  * Endpoint analysis.
  * Object ownership.
  * CRUD.
  * Roles.
  * Trust boundaries.
  * Workflows.
  * Coverage.
  * Hypothesis-driven testing.
  * Troubleshooting.
  * Scenario-based questions.
  * Rapid-fire revision.

---

## Lesson 11 — Quick Revision

- Provides a compact review of the entire module.

- Includes:

  * Target mental models.
  * Site Map concepts.
  * Scope rules.
  * Authorization reminders.
  * Functional mapping.
  * Endpoint inventories.
  * Endpoint families.
  * Object mapping.
  * Role mapping.
  * Trust boundaries.
  * Business workflows.
  * API mapping.
  * Coverage matrices.
  * Prioritization.
  * Hypotheses.
  * Baselines.
  * Troubleshooting.
  * Interview revision.
  * Final assessment checklist.

---

## Target and Proxy

```text
Browser
   │
   ▼
Proxy
   │
   ├── Observe Requests
   ├── Inspect Traffic
   └── Record History
   │
   ▼
Target
   │
   ├── Organize Hosts
   ├── Organize Paths
   ├── Build Site Map
   └── Support Attack-Surface Mapping
```

- Proxy is primarily traffic-oriented.

- Target is primarily structure-oriented.

---

## Target and Repeater

```text
Target
   │
   ▼
Identify High-Value Request
   │
   ▼
Send to Repeater
   │
   ▼
Capture Baseline
   │
   ▼
Modify One Variable
   │
   ▼
Compare Response
   │
   ▼
Validate Behavior
```

- Target helps decide what deserves investigation.

- Repeater helps perform controlled manual testing.

---

## Attack-Surface Mapping Model

```text
Application
│
├── Hosts
├── Functions
├── Authentication States
├── Roles
├── Objects
├── Endpoints
├── APIs
├── Inputs
├── State Changes
├── Sensitive Data
├── Business Workflows
└── Trust Boundaries
```

- The goal is not simply to collect URLs.

- The goal is to understand how the application behaves and where important security boundaries exist.

---

## Endpoint Analysis Model

```text
Endpoint
│
├── Method
├── Path
├── Function
├── Authentication
├── Role
├── Inputs
├── Object
├── State Change
├── Sensitive Data
└── Baseline Behavior
```

- This context makes endpoint discovery useful for security testing.

---

## Coverage Model

```text
Area              Guest   User   Admin   Status
------------------------------------------------
Authentication    Yes     N/A    N/A     Reviewed
Profile           No      Yes    Yes     Reviewed
Projects          No      Yes    Yes     Partial
Reports           No      No     Yes     Not Tested
Administration    No      No     Yes     Not Tested
```

- Coverage tracking helps prevent:

  * Missed functionality.
  * Duplicate work.
  * Forgotten roles.
  * Incomplete workflow testing.

---

## Security Hypothesis Model

```text
Observation
      │
      ▼
Understand Expected Control
      │
      ▼
Write Testable Hypothesis
      │
      ▼
Capture Baseline
      │
      ▼
Controlled Test
      │
      ▼
Validate Evidence
```

- Remember:

  ```text
  Observation ≠ Finding

  Hypothesis ≠ Finding

  Reproducible Evidence + Impact → Finding
  ```

---

## Common High-Value Areas

- Prioritize according to application context.

- Common areas include:

  * Authentication.
  * Authorization.
  * Account recovery.
  * Administrative functionality.
  * Sensitive data.
  * File handling.
  * APIs.
  * State-changing actions.
  * Business-critical workflows.
  * Multi-tenant boundaries.

---

## Common Mistakes to Avoid

- Do not:

  * Assume the Site Map is complete.
  * Assume every observed host is authorized.
  * Expand scope for convenience.
  * Ignore Proxy history.
  * Ignore HTTP methods.
  * Ignore authentication states.
  * Ignore user roles.
  * Lose track of object ownership.
  * Ignore application state.
  * Treat interesting endpoints as vulnerabilities.
  * Change multiple variables without a baseline.
  * Test randomly without tracking coverage.

---

## Professional Workflow

```text
Confirm Authorization
      │
      ▼
Configure Scope
      │
      ▼
Browse Normally
      │
      ▼
Populate Site Map
      │
      ▼
Classify Hosts
      │
      ▼
Map Functions
      │
      ▼
Map Endpoints and APIs
      │
      ▼
Map Roles and Objects
      │
      ▼
Map Workflows and Trust Boundaries
      │
      ▼
Track Coverage
      │
      ▼
Prioritize
      │
      ▼
Create Hypotheses
      │
      ▼
Send Selected Requests to Repeater
      │
      ▼
Perform Controlled Testing
      │
      ▼
Validate
      │
      ▼
Document
      │
      ▼
Update Coverage
```

---

## Recommended Learning Order

- Complete the lessons in sequence:

  1. Introduction.
  2. Interface and Target Architecture.
  3. Site Map.
  4. Scope and Scope Management.
  5. Content Discovery and Endpoint Analysis.
  6. Attack Surface Mapping and Investigation.
  7. Professional Target Workflow.
  8. Common Mistakes and Troubleshooting.
  9. Practical Exercises.
  10. Interview Questions.
  11. Quick Revision.

- The progression is intentional:

  ```text
  Understand
      │
      ▼
  Navigate
      │
      ▼
  Scope
      │
      ▼
  Discover
      │
      ▼
  Analyze
      │
      ▼
  Map
      │
      ▼
  Investigate
      │
      ▼
  Practice
      │
      ▼
  Review
  ```

---

## Module Completion Checklist

```text
[ ] Understand the purpose of Target

[ ] Understand how Site Map is populated

[ ] Navigate Target confidently

[ ] Configure scope correctly

[ ] Explain scope vs authorization

[ ] Classify observed hosts safely

[ ] Build a functional application map

[ ] Create an endpoint inventory

[ ] Identify endpoint families

[ ] Map APIs

[ ] Map objects and ownership

[ ] Compare authentication states

[ ] Compare authorized roles

[ ] Identify trust boundaries

[ ] Map business workflows

[ ] Identify state-changing operations

[ ] Track testing coverage

[ ] Prioritize high-value attack surface

[ ] Create security hypotheses

[ ] Capture baseline requests

[ ] Send selected requests to Repeater

[ ] Troubleshoot missing Target content

[ ] Complete practical exercises

[ ] Review interview questions

[ ] Complete quick revision
```

---

## Key Takeaways

- The Target tool is the organizational foundation for understanding a web application's attack surface in Burp Suite.

- Site Map provides an evolving view of application content Burp has learned.

- Scope helps Burp focus on relevant targets, but technical scope never replaces explicit authorization.

- Professional Target usage goes beyond collecting URLs.

- Effective application mapping includes:

  * Functions.
  * Endpoints.
  * APIs.
  * Roles.
  * Objects.
  * Ownership.
  * Workflows.
  * Trust boundaries.
  * Coverage.

- High-value observations should be converted into testable hypotheses before controlled testing.

- Target works naturally with Proxy for traffic observation and Repeater for manual validation.

- A disciplined Target workflow is:

  ```text
  Authorize → Scope → Browse → Map → Organize → Prioritize
  → Hypothesize → Baseline → Test → Validate → Document
  ```
