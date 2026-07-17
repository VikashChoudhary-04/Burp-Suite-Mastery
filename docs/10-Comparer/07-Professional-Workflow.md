# Professional Workflow

> **Module Progress**

```text
███████░░░░░░░░ 7 / 15 Lessons
```

- Comparer is rarely the first Burp Suite tool you use during an assessment.

- Instead, it supports investigations after you've collected evidence.

- Professional testers use Comparer to verify observations, identify meaningful changes, and guide their next testing step.

---

## Where Comparer Fits

- A typical workflow looks like this:

  ```text
  Reconnaissance

          │

          ▼

        Proxy

          │

          ▼

      Repeater

          │

          ▼

  Intruder (if needed)

          │

          ▼

      Comparer

          │

          ▼

  Interpret Differences

          │
  
          ▼

  Validate Findings

          │

          ▼

  Document Results
  ```

- Comparer is an **analysis tool**, not a discovery tool.

---

## Workflow 1 — Authorization Testing

- Objective:

  - Determine whether different users receive different access.

- Example process:

  1. Capture a request as a regular user.
  2. Capture the same request as an administrator.
  3. Send both responses to Comparer.
  4. Review highlighted differences.
  5. Investigate any changes affecting permissions or exposed data.

- Possible observations:

  * Different status codes
  * Additional response fields
  * More accessible resources
  * Different navigation options

---

## Workflow 2 — IDOR Investigation

- Objective:

  - Understand the effect of changing object identifiers.

- Example:

  - Request A:

    ```http
    GET /api/orders/1001
    ```

  - Request B:

    ```http
    GET /api/orders/1002
    ```

- Compare the responses.

- Ask:

  * Did ownership change?
  * Was sensitive data exposed?
  * Did the authorization decision change?
  * Did the response structure remain consistent?

---

## Workflow 3 — JWT Analysis

- Compare two JWT payloads.

- Focus on:

  * Roles
  * Permissions
  * Expiration
  * Audience
  * Subject
  * Custom claims

- Do not stop at identifying differences.

- Determine whether those differences explain changes in application behavior.

---

## Workflow 4 — Cookie Analysis

- Compare cookies:

  - Before login

      ↓

    After login

      ↓
  
  Password reset

      ↓

  Privilege change

- Look for:

  * New cookies
  * Removed cookies
  * Changed flags
  * Session rotation
  * Unexpected persistence

---

## Workflow 5 — API Testing

- Compare:

  * Successful responses
  * Failed responses
  * Authenticated responses
  * Anonymous responses

- Questions to ask:

  * Which fields appeared?
  * Which disappeared?
  * Did the schema change?
  * Did permissions change?
  * Was additional metadata returned?

---

## Workflow 6 — Response Validation

- After using Intruder or Repeater:

  - Instead of manually reading dozens of responses:

    * Select two representative responses.
    * Compare them.
    * Identify meaningful differences.
    * Decide whether further testing is justified.

- Comparer helps reduce manual review time.

---

## Decision Framework

- Before opening Comparer, answer:

  1. What question am I trying to answer?
  2. Which two items best answer that question?
  3. Which differences would be meaningful?
  4. How will I validate those differences?

- Clear questions lead to better investigations.

---

## Investigation Checklist

- Before comparing:

  * [ ] Define the comparison goal.
  * [ ] Select related evidence.
  * [ ] Verify that both items represent comparable scenarios.

- During comparison:

  * [ ] Review every highlighted difference.
  * [ ] Ignore expected variation.
  * [ ] Prioritize behavioral changes.
  * [ ] Record important observations.

- After comparison:

  * [ ] Validate findings manually.
  * [ ] Reproduce important differences.
  * [ ] Document confirmed behavior.

---

## Difference Challenge

- You compare two responses after changing a single request parameter.

- Comparer highlights:

  * `403` → `200`
  * New `"permissions"` array
  * Response length increased
  * Different timestamp
  * Additional `"downloadAllowed": true`

- Questions:

  1. Which differences likely resulted from the parameter change?
  2. Which are probably unrelated?
  3. Which observation deserves immediate validation?
  4. What would your next request be?

---

## Professional Habits

- Experienced testers:

  * Compare with a purpose.
  * Validate every meaningful difference.
  * Ignore cosmetic variation.
  * Record observations before conclusions.
  * Treat Comparer as part of a larger investigation—not the investigation itself.

---

## Key Takeaways

* Comparer supports investigation rather than discovery.
* Always compare evidence that answers a specific question.
* Use Comparer after Proxy, Repeater, or Intruder when verification is needed.
* Validate important differences before reporting them.
* Professional workflows combine observation, comparison, and confirmation.
