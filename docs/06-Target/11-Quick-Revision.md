# Quick Revision

## Target Tool in One Sentence

- Burp Suite's Target tool helps you define the authorized testing focus, organize discovered application content, understand the attack surface, and select high-value areas for deeper investigation.

---

## Core Components

```text
Target
│
├── Site Map
└── Scope
```

### Site Map

- Organizes application content learned from observed or discovered traffic.

- Common entries include:

  * Hosts.
  * Directories.
  * Pages.
  * API endpoints.
  * Parameters.
  * JavaScript.
  * Static resources.

### Scope

- Defines which targets Burp treats as relevant to the assessment.

- Scope can help:

  * Filter traffic.
  * Reduce noise.
  * Focus investigation.
  * Control scope-aware Burp behavior.

- Remember:

  ```text
  Burp Scope ≠ Authorization
  ```

---

## Authorization First

```text
Written Authorization / Program Rules
              │
              ▼
        Define Scope
              │
              ▼
       Configure Burp
              │
              ▼
           Testing
```

- Never assume that an observed host is authorized.

- Classify hosts:

  ```text
  Confirmed In Scope

  Confirmed Out of Scope

  Unknown — Requires Verification
  ```

---

## How the Site Map Grows

```text
Browse Application
      │
      ▼
Traffic Passes Through Burp
      │
      ▼
Burp Observes Resources
      │
      ▼
Site Map Expands
```

- More meaningful workflow coverage usually produces a more useful Site Map.

- The Site Map is not guaranteed to represent the complete application.

---

## Site Map Mental Model

```text
example.com
│
├── /
├── login
├── account
│   ├── profile
│   └── security
├── projects
│   └── {id}
├── api
│   ├── users
│   └── projects
└── admin
```

- Think of it as an evolving structural map of observed application resources.

---

## What to Map

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

---

## Functional Mapping

- Organize resources by business purpose.

```text
Authentication
├── Login
├── Registration
└── Password Reset

Account
├── Profile
└── Security

Projects
├── Create
├── View
├── Update
└── Delete

Administration
├── Users
└── Roles
```

- Functional organization is usually more useful than a raw URL list.

---

## Endpoint Inventory

- For important endpoints, record:

  ```text
  Method

  Path

  Function

  Authentication

  Role

  Inputs

  Object

  State Change

  Sensitive Data

  Normal Response
  ```

### Example

```text
PATCH /api/projects/42

Function:
Update project

Authentication:
Required

Role:
Project manager

Object:
Project 42

Input:
JSON

State Change:
Yes
```

---

## Endpoint Families

```text
GET    /api/projects
POST   /api/projects
GET    /api/projects/{id}
PATCH  /api/projects/{id}
DELETE /api/projects/{id}
```

- Treat different methods as distinct operations.

- Group related routes to understand the complete object lifecycle.

---

## CRUD

```text
C → Create

R → Read

U → Update

D → Delete
```

- Map which roles can perform each operation.

---

## Object Mapping

```text
Organization
    │
    ├── Project
    │      │
    │      └── Document
    │
    └── Members
```

- Record:

  * Identifier.
  * Owner.
  * Parent-child relationships.
  * Allowed roles.
  * Related endpoints.

- Object ownership is essential context for authorization testing.

---

## Authentication-State Mapping

```text
Guest
  │
  ▼
Authenticated User
  │
  ▼
Privileged User
```

- Compare functionality available at each state.

---

## Role Mapping

```text
User
├── Profile
└── Projects

Manager
├── Team
├── Reports
└── Approvals

Admin
├── Users
├── Roles
└── Settings
```

- Role differences reveal privilege boundaries.

---

## Trust Boundaries

```text
Guest → User

User → Admin

Tenant A → Tenant B

Browser → API

Application → Third Party
```

- Trust boundaries are high-value areas for investigation.

---

## Business Workflow Mapping

```text
Add Item
    │
    ▼
Apply Discount
    │
    ▼
Checkout
    │
    ▼
Payment
    │
    ▼
Order Confirmation
```

- Understand normal order and state transitions before testing deviations.

---

## Content Discovery

- Endpoint leads may come from:

  * Site Map.
  * Proxy history.
  * HTML.
  * Forms.
  * Redirects.
  * JavaScript.
  * API responses.
  * Error messages.
  * Authorized discovery.

- Remember:

  ```text
  Discovered ≠ Confirmed

  Confirmed ≠ Vulnerable
  ```

---

## JavaScript-Derived Endpoints

```text
JavaScript Reference
       │
       ▼
Verify Scope
       │
       ▼
Verify Reachability
       │
       ▼
Understand Context
       │
       ▼
Analyze
```

- A route string in JavaScript is only a discovery lead.

---

## API Mapping

- Common patterns:

  ```text
  /api/
  /api/v1/
  /api/v2/
  /graphql
  ```

- Record:

  * Method.
  * Route.
  * Authentication.
  * Role.
  * Inputs.
  * Object.
  * Response data.

---

## State-Changing Operations

- Prioritize operations such as:

  ```text
  Create

  Update

  Delete

  Upload

  Transfer

  Approve

  Cancel

  Change Role

  Reset Credential
  ```

---

## Sensitive Areas

- Common high-value areas:

  * Authentication.
  * Authorization.
  * Account recovery.
  * Administration.
  * Sensitive data.
  * File handling.
  * APIs.
  * Payments.
  * Multi-tenant boundaries.
  * State-changing actions.

---

## Coverage Matrix

```text
Area              Guest   User   Admin   Status
------------------------------------------------
Authentication    Yes     N/A    N/A     Reviewed
Profile           No      Yes    Yes     Reviewed
Projects          No      Yes    Yes     Partial
Reports           No      No     Yes     Not Tested
Administration    No      No     Yes     Not Tested
```

- Use coverage tracking to reduce:

  * Missed functionality.
  * Duplicate testing.
  * Forgotten roles.
  * Incomplete workflows.

---

## Useful Status Labels

```text
Not Reviewed

Mapped

Baseline Captured

Testing

Needs Follow-Up

Validated

No Issue Observed
```

- Avoid absolute labels such as:

  ```text
  Secure
  ```

---

## Prioritization Model

```text
Potential Impact
      +
Privilege Boundary
      +
Sensitive Data
      +
State Change
      +
Object Ownership
      +
Business Criticality
```

---

## Security Hypothesis

### Observation

```text
GET /api/projects/{id}
```

### Context

```text
Projects belong to different users.
```

### Hypothesis

```text
The server should verify project membership before returning project data.
```

- A hypothesis is not a finding.

---

## Baseline Workflow

```text
Original Request
      │
      ▼
Record Normal Response
      │
      ▼
Change One Variable
      │
      ▼
Compare
      │
      ▼
Interpret
```

- Preserve:

  * Role.
  * Session.
  * Object ownership.
  * Application state.

---

## Target and Other Burp Tools

```text
Proxy
→ Observe traffic

Target
→ Organize and map

Repeater
→ Controlled manual testing

Intruder
→ Authorized automated variation

Sequencer
→ Token randomness analysis

Decoder
→ Transform encoded data

Comparer
→ Compare selected data
```

---

## Target-to-Repeater Workflow

```text
Target
   │
   ▼
Identify Interesting Request
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
Compare
   │
   ▼
Validate
```

---

## Professional Target Workflow

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
Build Site Map
      │
      ▼
Classify Hosts
      │
      ▼
Map Functions
      │
      ▼
Map Roles and Objects
      │
      ▼
Map APIs and Workflows
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
Capture Baselines
      │
      ▼
Controlled Testing
      │
      ▼
Validate
      │
      ▼
Document and Update Coverage
```

---

## Observation vs Hypothesis vs Finding

```text
Observation
→ Something interesting was discovered.

Hypothesis
→ A security control is expected to behave in a specific way.

Finding
→ Evidence demonstrates a reproducible security weakness and impact.
```

---

## Common Mistakes

- Treating Site Map as complete.

- Assuming every observed host is in scope.

- Configuring overly broad scope.

- Configuring scope too narrowly.

- Confusing Burp scope with authorization.

- Ignoring Proxy history.

- Ignoring HTTP methods.

- Ignoring roles.

- Losing track of object ownership.

- Ignoring application state.

- Testing randomly.

- Failing to capture baselines.

- Changing several variables at once.

- Reporting interesting endpoints as vulnerabilities.

- Failing to revisit Target.

---

## Troubleshooting — Empty Site Map

```text
Is Traffic in Proxy History?
       │
   ┌───┴───┐
   │       │
  No      Yes
   │       │
   ▼       ▼
Check     Check
Proxy     Filters
Browser   Scope
Listener  Target View
```

---

## Troubleshooting — Missing Endpoint

```text
Exercise Feature
      │
      ▼
Check Proxy History
   /             \
Present         Missing
  │               │
  ▼               ▼
Check Target     Check Proxy
Filters          Auth
View             Role
                 State
                 Workflow
```

---

## Troubleshooting — Too Many Hosts

- Check:

  * Third-party dependencies.
  * Browser background traffic.
  * Scope configuration.
  * Filters.
  * External authentication.

- Focus only on confirmed authorized targets.

---

## Status Code Reminders

```text
200 ≠ Correct Authorization

404 ≠ Definitive Nonexistence

302 ≠ Automatically Safe or Unsafe
```

- Always analyze:

  * Response body.
  * Headers.
  * Returned data.
  * Application effect.
  * Baseline behavior.

---

## Interview Rapid Review

### What is Target?

- Burp's application mapping and scope-management workspace.

### What is Site Map?

- A hierarchical representation of application content learned by Burp.

### What is scope?

- Burp's configured set of relevant assessment targets.

### Does scope equal authorization?

- No.

### Is Site Map complete?

- Not necessarily.

### What is an endpoint family?

- Related operations around the same resource.

### Why map roles?

- To understand privilege boundaries.

### Why map objects?

- To understand ownership and authorization relationships.

### What is a coverage matrix?

- A record of what functionality, roles, or states have been reviewed.

### What is a hypothesis?

- A testable expectation about a security control.

### Why capture a baseline?

- To establish normal behavior before modification.

### Why change one variable?

- To isolate the cause of behavioral differences.

### Target or Proxy for chronological traffic?

- Proxy history.

### Target or Repeater for manual testing?

- Repeater.

### What should happen before reporting?

- Reproducible validation and evidence.

---

## Final Mental Model

```text
TARGET TOOL
    │
    ├── Scope
    │     └── What should Burp focus on?
    │
    └── Site Map
          └── What has Burp learned about the application?
                    │
                    ▼
              Organize
                    │
                    ├── Functions
                    ├── Roles
                    ├── Objects
                    ├── APIs
                    ├── Workflows
                    └── Trust Boundaries
                    │
                    ▼
              Track Coverage
                    │
                    ▼
               Prioritize
                    │
                    ▼
               Hypothesize
                    │
                    ▼
             Test in Repeater
                    │
                    ▼
                Validate
                    │
                    ▼
                Document
```

---

## Final Checklist

```text
[ ] Authorization confirmed

[ ] Scope configured correctly

[ ] Major hosts classified

[ ] Public functionality mapped

[ ] Authenticated functionality mapped

[ ] Roles compared

[ ] Objects and ownership documented

[ ] Endpoint families identified

[ ] APIs mapped

[ ] State-changing operations identified

[ ] Sensitive data flows identified

[ ] Business workflows mapped

[ ] Trust boundaries identified

[ ] Coverage matrix maintained

[ ] High-value areas prioritized

[ ] Security hypotheses documented

[ ] Baselines captured

[ ] Controlled tests performed

[ ] Results validated

[ ] Evidence recorded

[ ] Coverage updated
```

---

## Key Takeaways

- Target is the organizational foundation for understanding an application's attack surface.

- The Site Map is an evolving representation of discovered content, not proof of complete coverage.

- Burp scope must reflect authorization, but it does not create authorization.

- Professional mapping includes functions, roles, objects, endpoints, APIs, workflows, trust boundaries, and coverage.

- Endpoint discovery becomes useful when combined with context and prioritization.

- High-value observations should become explicit hypotheses before controlled testing.

- Target identifies and organizes interesting requests; Repeater is commonly used to test them manually.

- Strong Target usage follows a repeatable sequence:

  ```text
  Authorize → Scope → Browse → Map → Organize → Prioritize
  → Hypothesize → Baseline → Test → Validate → Document
  ```
