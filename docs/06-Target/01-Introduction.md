# Introduction

## Overview

- The **Target** tool is Burp Suite's central workspace for understanding the structure, boundaries, and attack surface of a web application.

- Before testing individual requests for vulnerabilities, a tester needs to understand:

  * What hosts and applications are part of the assessment.
  * Which assets are explicitly authorized for testing.
  * What pages, directories, APIs, and endpoints have been discovered.
  * Which application areas require authentication.
  * Which functions appear security-sensitive.
  * How the application is organized.
  * Which requests deserve deeper investigation.

- The Target tool helps transform observed traffic into an organized model of the application.

---

## Learning Objectives

- By the end of this lesson, you should be able to:

  * Explain the purpose of the Target tool.
  * Understand the relationship between Target and Proxy.
  * Distinguish application mapping from vulnerability testing.
  * Explain why scope management is essential.
  * Describe how Burp builds knowledge about an application.
  * Identify information useful for attack-surface analysis.
  * Apply an investigation-first mindset before deeper testing.

---

## What Is the Target Tool?

- The Target tool provides an organized view of applications and resources Burp Suite has discovered.

- As traffic passes through Burp, information about visited hosts, paths, files, endpoints, requests, and responses contributes to Burp's representation of the target.

- Instead of treating every HTTP request as an isolated event, Target helps you understand how requests relate to the larger application.

  ```text
  Browser Activity
        │
        ▼
  Burp Proxy Observes Traffic
        │
        ▼
  Application Resources Are Discovered
        │
        ▼
  Target Organizes the Application
        │
        ├── Hosts
        ├── Directories
        ├── Pages
        ├── API Endpoints
        ├── Parameters
        └── Other Resources
        │
        ▼
  Tester Maps the Attack Surface
  ```

---

## Why the Target Tool Matters

- Modern applications may contain:

  * Multiple domains and subdomains.
  * Public and authenticated areas.
  * REST or GraphQL APIs.
  * Administrative interfaces.
  * JavaScript-driven endpoints.
  * File-upload functionality.
  * Account-management workflows.
  * Payment or checkout processes.
  * WebSocket communication.
  * Role-specific functionality.
  * Legacy endpoints.
  * Third-party integrations.

- Without structured mapping, important functionality can easily be overlooked.

- Target gives the tester a structured representation of discovered application content.

---

## Mapping Before Testing

- Effective security testing begins with understanding the system.

  ```text
  Understand Scope
      │
      ▼
  Explore Application
      │
      ▼
  Build Application Map
      │
      ▼
  Identify Security Boundaries
      │
      ▼
  Prioritize Interesting Functionality
      │
      ▼
  Establish Baselines
      │
      ▼
  Form Testable Hypotheses
      │
      ▼
  Perform Controlled Testing
      │
      ▼
  Validate Results
  ```

- Target supports the early stages of this process.

---

## Target Is More Than a List of URLs

- A URL list tells you which locations have been observed.

- A useful application map helps you understand relationships between those locations.

  ```text
  example.com
  │
  ├── /login
  ├── /register
  ├── /account
  │   ├── /profile
  │   ├── /security
  │   └── /billing
  ├── /orders
  ├── /admin
  └── /api
      ├── /users
      ├── /orders
      └── /payments
  ```

- This structure suggests security questions:

  * Is `/admin` restricted by role?
  * Can one user access another user's order?
  * Does `/api/users` expose sensitive information?
  * Are sensitive account operations properly protected?
  * Do API endpoints enforce the same authorization rules as the browser interface?

- Mapping creates context for meaningful security hypotheses.

---

## The Two Core Concepts

### Site Map

- The Site Map organizes resources Burp has discovered.

- It can help reveal:

  * Hosts.
  * Directories.
  * Files.
  * Pages.
  * API paths.
  * Parameters.
  * HTTP methods.
  * Response information.
  * Application structure.

### Scope

- Scope defines which targets belong to the authorized assessment.

- Scope management helps:

  * Separate relevant traffic from unrelated traffic.
  * Reduce accidental interaction with unauthorized systems.
  * Improve filtering and organization.
  * Keep testing focused.
  * Support disciplined engagement boundaries.

---

## Discovery Does Not Equal Authorization

```text
Discovered Asset

≠

Authorized Target
```

- Applications frequently communicate with third-party services such as analytics providers, CDNs, identity providers, payment processors, support platforms, and cloud services.

- Their presence in Burp does not grant permission to test them.

- Always treat the documented scope and rules of engagement as authoritative.

---

## How Target Relates to Proxy

### Proxy

- Proxy is primarily used to:

  * Observe traffic.
  * Intercept requests and responses.
  * Review HTTP history.
  * Modify traffic.

### Target

- Target is primarily used to:

  * Organize discovered content.
  * Define and manage scope.
  * Map application structure.
  * Identify interesting functionality.
  * Organize investigation targets.

  ```text
  Browser
      │
      ▼
  Proxy
      │
      ├── Observe Traffic
      ├── Intercept Traffic
      └── Record Traffic
      │
      ▼
  Target
      │
      ├── Organize Discovered Content
      ├── Define Scope
      ├── Map Application Structure
      └── Identify Investigation Areas
  ```

---

## How Target Connects to Other Burp Tools

```text
Target
    │
    ▼
Identify Interesting Resource
    │
    ▼
Inspect Request and Response
    │
    ├───────────────┐
    ▼               ▼
Repeater         Intruder
    │               │
    ▼               ▼
Manual Testing   Controlled Automation
    │               │
    └───────┬───────┘
            ▼
     Compare Behavior
            │
            ▼
     Validate Finding
```

- Tool selection should follow the testing hypothesis.

---

## Understanding the Attack Surface

- An application's attack surface includes the interfaces, inputs, functionality, and trust boundaries through which security-relevant interaction can occur.

- Examples include:

  * Authentication endpoints.
  * Registration and password-reset flows.
  * Account settings.
  * Object identifiers.
  * Administrative functions.
  * Search functionality.
  * File uploads.
  * Import and export features.
  * APIs.
  * Webhooks.
  * Payment workflows.
  * Role-management functionality.
  * JavaScript-discovered endpoints.
  * GraphQL operations.
  * WebSocket messages.

---

## High-Value Application Areas

- High-value areas commonly involve:

  * Authentication.
  * Authorization.
  * Sensitive data.
  * Privileged actions.
  * Account recovery.
  * Role changes.
  * Financial operations.
  * Object ownership.
  * Tenant boundaries.
  * File handling.
  * Workflow state.
  * External integrations.

- Prioritization focuses testing effort where meaningful security boundaries are most likely to exist.

---

## Security Boundaries

- Common boundaries include:

  ```text
  Unauthenticated
        │
        ▼
  Authenticated
        │
        ▼
  Privileged
  ```

- Ownership boundaries:

  ```text
  User A Objects

  ≠

  User B Objects
  ```

- Tenant boundaries:

  ```text
  Tenant A

  ≠

  Tenant B
  ```

- Mapping these boundaries before testing makes authorization investigations more systematic.

---

## Example Mapping Process

- Suppose an authorized training application contains:

  ```text
  lab.example
  │
  ├── /login
  ├── /register
  ├── /dashboard
  ├── /profile
  ├── /documents
  │   ├── /101
  │   ├── /102
  │   └── /upload
  ├── /admin
  └── /api
      ├── /profile
      ├── /documents
      └── /users
  ```

- You might classify it as:

  ```text
  Authentication
  ├── /login
  └── /register

  Account Management
  └── /profile

  Object Authorization
  ├── /documents/101
  ├── /documents/102
  └── /api/documents

  File Handling
  └── /documents/upload

  Privileged Functionality
  ├── /admin
  └── /api/users
  ```

- This creates a structured testing plan instead of a random URL list.

---

## Professional Target Workflow

```text
Read Scope and Rules of Engagement
            │
            ▼
     Configure Burp Scope
            │
            ▼
    Browse the Application
            │
            ▼
      Populate Site Map
            │
            ▼
   Identify Major Features
            │
            ▼
  Identify Security Boundaries
            │
            ▼
    Map Inputs and Objects
            │
            ▼
 Prioritize High-Value Areas
            │
            ▼
 Send Requests to Testing Tools
            │
            ▼
     Record Investigation
```

- Mapping is iterative. New discoveries may reveal additional functionality requiring further analysis.

---

## Questions to Ask While Mapping

- Ask:

  * What functionality exists?
  * Which functionality requires authentication?
  * What user roles exist?
  * Which endpoints reference user-controlled objects?
  * Where are identifiers used?
  * Which requests change application state?
  * Which functions handle sensitive information?
  * Are APIs used behind the browser interface?
  * Are multiple hosts involved?
  * Which assets are actually in scope?
  * Where are trust boundaries enforced?
  * Which workflows deserve deeper testing?

---

## Common Beginner Mistakes

- Beginning vulnerability testing before understanding the application.
- Assuming every host visible in Burp is authorized for testing.
- Treating every endpoint as equally important.
- Ignoring API requests generated by browser actions.
- Failing to distinguish authenticated, privileged, and unauthenticated functionality.
- Treating the Site Map as automatically complete.
- Failing to document important application areas.

---

## The Site Map Is an Evolving Model

- The Site Map represents what Burp has discovered, not necessarily everything that exists.

- Functionality may remain undiscovered because:

  * A feature has not been visited.
  * A specific role is required.
  * An endpoint is referenced only by JavaScript.
  * A workflow requires a particular state.
  * Content is loaded conditionally.
  * An API operation is triggered only by specific actions.

  ```text
  Observed Application

  ≠

  Necessarily Complete Application
  ```

---

## Professional Mindset

```text
Scope

↓

Structure

↓

Functionality

↓

Trust Boundaries

↓

Inputs

↓

Security Assumptions

↓

Testable Hypotheses
```

- Target is most valuable when it helps turn application observations into structured security investigations.

---

## Field Notes

- Keep authorized scope clearly understood throughout an assessment.
- Browse important workflows deliberately.
- Map unauthenticated and authenticated functionality when authorized.
- Distinguish traffic from different test accounts and roles.
- Pay attention to API calls generated by browser actions.
- Identify state-changing requests separately from read-only requests.
- Record important object identifiers and ownership relationships.
- Revisit the Site Map as new functionality is discovered.

---

## Practice Tasks

1. Open an explicitly authorized training application through Burp Suite.
2. Define the permitted target in scope.
3. Browse the application's major functionality.
4. Review Target and identify:

   * Hosts.
   * Major directories.
   * Application pages.
   * API endpoints.
   * Authentication functionality.
   * State-changing requests.

5. Create an attack-surface inventory:

   ```text
   Authentication:

   Account Management:

   Authorization-Relevant Objects:

   APIs:

   File Handling:

   Administrative Functions:

   Other High-Value Features:
   ```

6. Choose three high-value areas and explain why each deserves deeper testing.
7. Verify authorization before performing security tests.

---

## Interview Questions

1. What is the purpose of Burp Suite's Target tool?
2. What is the difference between Proxy and Target?
3. What is the Site Map?
4. What does scope represent in Burp Suite?
5. Why does discovering a host not automatically authorize testing it?
6. Why should an application be mapped before deeper vulnerability testing?
7. What is an application attack surface?
8. What functionality would you prioritize during attack-surface analysis?
9. Why should the Site Map not automatically be considered complete?
10. How does Target support deeper testing with other Burp tools?

---

## Quick Revision

```text
Target
│
├── Organizes Discovered Application Content
├── Supports Scope Management
├── Helps Map the Attack Surface
├── Provides Context for Manual Testing
└── Supports Structured Investigation
```

- Remember:

  ```text
  Discovered ≠ Authorized

  Observed ≠ Complete

  Endpoint List ≠ Application Understanding

  Mapping Before Testing

  Context Before Conclusions
  ```

---

## Key Takeaways

- Target helps organize and understand the application being assessed.
- Site Map and Scope are central concepts.
- Application mapping should happen before deep vulnerability testing.
- Discovered assets must not be confused with authorized targets.
- A useful attack-surface map identifies functionality, inputs, objects, roles, and security boundaries.
- The Site Map is an evolving representation of observed content, not guaranteed proof of completeness.
- Target helps transform application observations into structured, testable security hypotheses.
