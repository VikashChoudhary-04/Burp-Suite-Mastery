# Scope Configuration and Management

## Overview

- **Scope** defines the assets and application areas that belong to an authorized security assessment.

- In Burp Suite, configuring scope helps separate relevant target traffic from unrelated requests and supports a safer, more organized testing workflow.

- Scope is not merely a convenience filter.

- It represents an operational boundary that should reflect the actual authorization provided by the program policy, rules of engagement, or assessment agreement.

---

## Learning Objectives

- By the end of this lesson, you should be able to:

  * Explain what scope means in Burp Suite.
  * Distinguish Burp's configured scope from legal or contractual authorization.
  * Add appropriate targets to scope.
  * Understand host, protocol, port, and path considerations.
  * Use scope to reduce irrelevant traffic.
  * Recognize third-party and out-of-scope dependencies.
  * Manage scope safely during penetration tests and bug bounty work.
  * Verify scope before performing active security testing.

---

## What Is Scope?

- Scope defines which targets Burp should treat as relevant to the current assessment.

- A scope definition may consider:

  * Protocol.
  * Hostname.
  * Port.
  * Path.
  * File or endpoint patterns.

- Conceptually:

  ```text
  Observed Traffic
        │
        ├── Authorized Target Traffic
        │
        └── Unrelated / Out-of-Scope Traffic
  ```

- Scope helps Burp and the tester distinguish between these categories.

---

## Burp Scope Is Not Authorization

- This distinction is critical:

  ```text
  Added to Burp Scope

  ≠

  Legally Authorized to Test
  ```

- Burp allows you to configure technical scope rules.

- Authorization comes from sources such as:

  * A signed penetration-testing agreement.
  * Rules of engagement.
  * A bug bounty program policy.
  * A vulnerability disclosure policy.
  * Explicit written permission.
  * An intentionally vulnerable training environment you control or are permitted to use.

- Never use a Burp configuration as evidence that testing is permitted.

---

## Why Scope Management Matters

- Modern browsers generate traffic to many unrelated systems.

- A single application may contact:

  * Analytics services.
  * Advertising networks.
  * Content delivery networks.
  * Identity providers.
  * Payment processors.
  * Support platforms.
  * Cloud-storage services.
  * Monitoring services.
  * CAPTCHA providers.
  * Social-login services.

- Without scope management, this traffic can clutter your testing environment.

---

## Example

- Suppose the authorized target is:

  ```text
  https://app.example.com
  ```

- While browsing, the application may also contact:

  ```text
  analytics.example-third-party.com
  cdn.vendor.example
  identity.provider.example
  api.payment-provider.example
  ```

- These external systems may appear in Proxy history or Target.

- Their presence does not make them authorized targets.

---

## Scope as a Safety Boundary

- A disciplined workflow is:

  ```text
  Read Authorization
        │
        ▼
  Identify Exact Allowed Assets
        │
        ▼
  Identify Explicit Exclusions
        │
        ▼
  Configure Burp Scope
        │
        ▼
  Verify Scope Rules
        │
        ▼
  Browse and Map
        │
        ▼
  Test Only Authorized Assets
  ```

- Scope configuration should follow authorization, not define it.

---

## Common Scope Dimensions

### Protocol

- Examples:

  ```text
  http
  https
  ```

- Authorization may apply only to a particular scheme or deployment.

### Host

- Examples:

  ```text
  app.example.com
  api.example.com
  ```

- Be precise about whether authorization covers:

  * One exact hostname.
  * Multiple named hosts.
  * A wildcard subdomain range.
  * An entire registered domain.

### Port

- Examples:

  ```text
  80
  443
  8443
  ```

- A hostname being authorized does not necessarily mean every exposed service or port is authorized.

### Path

- Authorization may be restricted to specific application paths.

  ```text
  https://example.com/app/
  ```

- Other paths on the same host may remain excluded.

---

## Exact Host vs Wildcard Scope

- These are materially different:

  ```text
  app.example.com
  ```

  and:

  ```text
  *.example.com
  ```

- Exact-host authorization applies only to the named host unless the governing policy says otherwise.

- Wildcard scope may include multiple subdomains, but exclusions and testing restrictions can still apply.

- Never infer wildcard authorization from ownership alone.

---

## Root Domain and Subdomains

- Consider:

  ```text
  example.com
  www.example.com
  app.example.com
  api.example.com
  admin.example.com
  ```

- These are separate hosts.

- If only:

  ```text
  app.example.com
  ```

  is authorized, do not assume the others are permitted.

---

## Path-Restricted Scope

- Some engagements restrict testing to part of an application.

- Example:

  ```text
  Included:

  https://example.com/customer-portal/

  Excluded:

  https://example.com/internal-admin/
  ```

- Even though both paths use the same host, the authorization boundary is different.

- Burp scope rules can help reflect this distinction.

---

## Included and Excluded Targets

- Think of scope as two complementary concepts:

  ```text
  Included
  │
  └── Assets permitted by the assessment rules

  Excluded
  │
  └── Assets or functionality that must not be tested
  ```

- Exclusions can be especially important when broad wildcard scope exists.

---

## Examples of Possible Exclusions

- Depending on the engagement, exclusions may include:

  * Third-party services.
  * Production payment processing.
  * Denial-of-service testing.
  * Social engineering.
  * Destructive testing.
  * Specific legacy systems.
  * Customer-owned tenants.
  * Particular endpoints.
  * Automated scanning.
  * High-volume requests.

- Technical scope and permitted testing techniques are separate dimensions.

---

## Asset Scope vs Testing Rules

- An asset can be in scope while certain actions remain prohibited.

  ```text
  Asset Authorized
        │
        ▼
  Technique Restrictions Still Apply
  ```

- Example:

  ```text
  app.example.com

  Allowed:
  - Manual web testing

  Not allowed:
  - Denial of service
  - High-volume automation
  - Social engineering
  ```

- Always review both asset scope and testing restrictions.

---

## Adding a Target to Scope

- A common workflow is:

  ```text
  Observe Authorized Host
        │
        ▼
  Confirm Against Rules
        │
        ▼
  Add Appropriate Target to Scope
        │
        ▼
  Review Scope Definition
        │
        ▼
  Apply Scope-Based Filters
  ```

- Do not blindly add every host observed during browsing.

---

## Scope Verification Checklist

- Before active testing, verify:

  * Is the hostname explicitly authorized?
  * Is the protocol relevant?
  * Are port restrictions specified?
  * Are path restrictions specified?
  * Are wildcard subdomains permitted?
  * Are third-party systems excluded?
  * Are specific testing techniques prohibited?
  * Are automated tools restricted?
  * Are rate limits specified?
  * Are test accounts required?
  * Are production-impact restrictions documented?

---

## Scope and Proxy History

- Scope can help filter Proxy history so that relevant application traffic is easier to analyze.

  ```text
  All Browser Traffic
        │
        ▼
  Scope Filter
        │
        ▼
  Relevant Assessment Traffic
  ```

- This reduces noise from:

  * Browser background traffic.
  * Search engines.
  * Analytics.
  * CDNs.
  * Extensions.
  * Third-party integrations.

---

## Scope and the Site Map

- The Site Map may contain both relevant and unrelated hosts depending on observed traffic and configuration.

- Scope helps focus attention on authorized application content.

  ```text
  Site Map
  │
  ├── app.example.com          ← authorized
  ├── api.example.com          ← verify authorization
  ├── cdn.vendor.example       ← third party
  └── analytics.vendor.example ← third party
  ```

- Never treat Site Map presence as permission.

---

## Third-Party Dependencies

- Third-party systems are common sources of scope mistakes.

- Examples include:

  * OAuth providers.
  * Payment gateways.
  * Cloud storage.
  * CAPTCHA services.
  * Customer-support widgets.
  * Analytics platforms.
  * CDN infrastructure.

- Ask:

  ```text
  Who owns this asset?

  Is it explicitly listed?

  Is testing permitted?

  Is it excluded?
  ```

- If authorization is unclear, do not actively test it until clarified.

---

## Redirects and Scope

- Redirects can move the browser to another host.

  ```text
  app.example.com/login
        │
        ▼
  identity-provider.example/auth
  ```

- The destination may belong to a third party.

- Follow the application workflow as needed for normal use, but do not assume the redirected host is authorized for security testing.

---

## APIs on Separate Hosts

- Applications frequently use separate API hosts.

  ```text
  Web Application:
  app.example.com

  API:
  api.example.com
  ```

- The API may or may not be separately authorized.

- Confirm scope before testing API endpoints.

---

## Wildcard Scope Risks

- A wildcard such as:

  ```text
  *.example.com
  ```

  can represent many systems.

- Potential issues include:

  * Third-party-hosted subdomains.
  * Acquired-company infrastructure.
  * Legacy systems.
  * Customer-specific environments.
  * Explicit exclusions.
  * Services governed by separate policies.

- Read the full policy rather than relying only on the wildcard notation.

---

## Scope Drift

- **Scope drift** occurs when testing gradually moves beyond the original authorized boundaries.

- Example:

  ```text
  Start:
  app.example.com

        │
        ▼

  Discover:
  api.example.com

        │
        ▼

  Discover:
  internal.example.net

        │
        ▼

  Begin testing without verification
  ```

- Discovery should trigger verification, not automatic expansion.

---

## Preventing Scope Drift

- Use this decision process:

  ```text
  New Asset Discovered
        │
        ▼
  Explicitly Authorized?
      /       \
    Yes        No / Unclear
     │              │
     ▼              ▼
  Continue      Do Not Test
                    │
                    ▼
               Verify Scope
  ```

---

## Scope Changes During an Engagement

- Scope may change.

- Examples:

  * A new host is added.
  * A fragile system is removed.
  * An API becomes available for testing.
  * A specific endpoint is temporarily excluded.
  * Testing hours change.

- When scope changes:

  * Obtain authoritative confirmation.
  * Update your notes.
  * Update Burp scope.
  * Inform relevant team members.
  * Re-check automation and filters.
  * Preserve the change history when appropriate.

---

## Multi-Environment Applications

- Organizations may operate:

  ```text
  dev.example.com
  staging.example.com
  app.example.com
  ```

- Authorization for one environment does not automatically imply authorization for another.

- Environment boundaries should be treated explicitly.

---

## Multi-Tenant Applications

- SaaS applications may contain multiple customer tenants.

  ```text
  tenant-a.example.com
  tenant-b.example.com
  tenant-c.example.com
  ```

- Even if the platform itself is authorized, other customers' data or tenants may be subject to strict restrictions.

- Use only authorized test accounts and tenant contexts.

---

## Scope and Test Accounts

- Scope is not limited to hosts and URLs.

- Operational boundaries may include:

  * Which accounts you may use.
  * Which roles you may assume.
  * Which tenants you may access.
  * Which data you may modify.
  * Whether account creation is permitted.

- Document these constraints alongside technical scope.

---

## Scope and Automation

- Automated tools can generate many requests quickly.

- Before running automation:

  ```text
  Verify Target
      │
      ▼
  Verify Scope
      │
      ▼
  Verify Technique Is Allowed
      │
      ▼
  Verify Rate Limits
      │
      ▼
  Configure Tool Carefully
      │
      ▼
  Monitor Results and Impact
  ```

- A correct hostname does not make every automated technique acceptable.

---

## Professional Scope Record

- Maintain a concise scope record.

  ```text
  Engagement:
  Example Web Application Assessment

  Included:
  - https://app.example.com
  - https://api.example.com

  Excluded:
  - Third-party services
  - payment.vendor.example

  Accounts:
  - test-user-1
  - test-admin-1

  Restrictions:
  - No denial-of-service testing
  - No social engineering
  - Automation limited by engagement rules

  Source:
  - Rules of engagement / program policy

  Last Verified:
  - YYYY-MM-DD
  ```

- This record supplements the authoritative policy; it does not replace it.

---

## Professional Workflow

```text
Receive Authorization
        │
        ▼
Read Full Scope
        │
        ▼
Record Included Assets
        │
        ▼
Record Exclusions
        │
        ▼
Record Technique Restrictions
        │
        ▼
Configure Burp Scope
        │
        ▼
Verify Configuration
        │
        ▼
Begin Mapping
        │
        ▼
Re-Verify New Discoveries
        │
        ▼
Test Within Boundaries
```

---

## Common Mistakes

- Adding an entire parent domain when only one host is authorized.

- Assuming all subdomains are automatically in scope.

- Treating third-party integrations as valid targets.

- Ignoring path-level restrictions.

- Ignoring prohibited testing techniques.

- Running automation before checking program rules.

- Assuming a redirect destination is authorized.

- Continuing to test a newly discovered host without verifying it.

- Failing to update Burp after an official scope change.

- Confusing technical discoverability with permission.

---

## Professional Tips

- Read the complete rules of engagement before configuring Burp.

- Keep a written scope summary available during testing.

- Prefer precise scope definitions over unnecessarily broad ones.

- Use scope-based filtering to reduce noise.

- Re-check authorization whenever a new host appears.

- Treat third-party services cautiously.

- Keep separate notes for:

  * Included assets.
  * Excluded assets.
  * Technique restrictions.
  * Test accounts.
  * Rate limits.
  * Environment restrictions.

- If scope is ambiguous, obtain clarification before active testing.

---

## Practice Exercise

- Use an explicitly authorized training environment.

1. Write down the authorized host.
2. Record the protocol and port.
3. Identify any path restrictions.
4. Configure the target in Burp scope.
5. Browse the application.
6. Review Proxy history and Site Map.
7. Identify any additional hosts contacted by the browser.
8. Classify each as:

   ```text
   Confirmed In Scope

   Confirmed Out of Scope

   Unknown — Requires Verification
   ```

9. Apply scope-based filtering.
10. Confirm that active testing is restricted to authorized assets.

---

## Scenario Exercise

- Assume a fictional policy states:

  ```text
  In Scope:
  https://app.example.com
  https://api.example.com

  Out of Scope:
  status.example.com
  support.example.com

  Restrictions:
  No denial-of-service testing
  No social engineering
  No testing of third-party services
  ```

- During browsing you observe:

  ```text
  app.example.com
  api.example.com
  status.example.com
  cdn.vendor.example
  auth.identity-provider.example
  ```

- Classify them:

  ```text
  app.example.com
  → In scope

  api.example.com
  → In scope

  status.example.com
  → Explicitly out of scope

  cdn.vendor.example
  → Third party; do not test

  auth.identity-provider.example
  → Third party unless explicitly authorized
  ```

---

## Interview Questions

1. What does scope mean in Burp Suite?
2. Is adding a host to Burp scope equivalent to receiving authorization?
3. Why is scope management important?
4. What factors can define technical scope?
5. What is the difference between exact-host and wildcard scope?
6. Why are third-party services a common scope risk?
7. What is scope drift?
8. How should you handle a newly discovered host?
9. Why must technique restrictions be reviewed separately from asset scope?
10. How can scope filters improve Proxy and Target workflows?
11. How should scope changes be handled during an engagement?
12. Why should API hosts be verified separately?
13. What risks exist in multi-tenant applications?
14. Why should automation be configured only after reviewing scope and rate restrictions?
15. What information should a professional scope record contain?

---

## Quick Revision

```text
Authorization
      │
      ▼
Define Boundaries
      │
      ├── Hosts
      ├── Protocols
      ├── Ports
      ├── Paths
      ├── Accounts
      └── Techniques
      │
      ▼
Configure Burp Scope
      │
      ▼
Filter and Organize
      │
      ▼
Test Only What Is Authorized
```

- Remember:

  ```text
  Discovered ≠ Authorized

  Added to Burp ≠ Permission

  Same Domain ≠ Same Scope

  In-Scope Asset ≠ Every Technique Allowed

  New Discovery → Verify Before Testing
  ```

---

## Key Takeaways

- Scope is a core safety and organization concept in professional web security testing.

- Burp's configured scope should reflect authoritative engagement boundaries but does not itself grant permission.

- Scope may depend on hosts, protocols, ports, paths, accounts, environments, and other constraints.

- Asset authorization and permitted testing techniques must both be reviewed.

- Third-party systems, redirects, APIs, wildcard domains, and multi-tenant environments require careful handling.

- Scope-based filtering reduces noise and helps keep investigations focused.

- Newly discovered assets should be verified before active testing.

- Strong testers continuously maintain awareness of authorization boundaries throughout the engagement.
