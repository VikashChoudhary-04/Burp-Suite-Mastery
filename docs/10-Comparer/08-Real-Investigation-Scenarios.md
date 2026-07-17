# Real Investigation Scenarios

> **Module Progress**

```text
████████░░░░░░░ 8 / 15 Lessons
```

- Comparer becomes most valuable when investigating real application behavior.

- This lesson presents realistic scenarios where comparing requests or responses helps answer important security questions.

- The examples use simplified data for learning purposes.

---

## Scenario 1 — Authorization Testing

- **Objective:** Determine whether an administrator receives additional access compared to a regular user.

- Response A:

  ```http
  HTTP/1.1 200 OK

  {
    "username": "alice",
    "role": "user"  
  }
  ```

- Response B:

  ```http
  HTTP/1.1 200 OK

  {
    "username": "alice",
    "role": "admin",
    "permissions": [
      "manage_users",
      "view_reports"
    ]
  }
  ```

### Investigation

- Questions to ask:

  * Which fields were added?
  * Did the application expose new capabilities?
  * Are these changes expected for the authenticated role?
  * Can an ordinary user trigger the same response?
  
---

## Scenario 2 — IDOR Investigation

- **Objective:** Understand the effect of changing an object identifier.

- Request A:

  ```http
  GET /api/orders/1001
  ```

- Request B:

  ```http
  GET /api/orders/1002
  ```

- Possible observations:

  * Different customer information
  * Different order contents
  * Different authorization decisions
  * Identical response structure

### Investigation

- Ask:

  * Did ownership change?
  * Did authorization remain consistent?
  * Was sensitive information exposed?

---

## Scenario 3 — Authentication State

- Compare responses before and after login.

- Before login:

  ```http
  HTTP/1.1 401 Unauthorized
  ```

- After login:

  ```http
  HTTP/1.1 200 OK
  ```

- Response body differences:

  * New account information
  * Session cookie
  * User preferences
  * Available features

### Investigation

- Determine:

  * Which changes are expected?
  * Which require further testing?

---

## Scenario 4 — JWT Comparison

- JWT A:

  ```json  
  {
    "role": "user",
    "exp": 1799999999  
  }
  ```

- JWT B:

  ```json  
  {
    "role": "admin",
    "exp": 1799999999
  }
  ```

### Investigation

- Focus on:

  * Changed claims
  * New permissions
  * Modified roles
  * Unexpected custom fields

- Then ask:

  - Does the application's behavior match the token differences?

---

## Scenario 5 — Cookie Evolution

- Compare cookies:

  * Before login
  * After login
  * After logout

- Look for:

  * Session rotation
  * Cookie removal
  * Security flags
  * Unexpected persistence

### Investigation

- Questions:

  * Was the session identifier replaced?
  * Were authentication cookies removed after logout?
  * Do security flags remain consistent?

---

## Scenario 6 — API Response Evolution

- Anonymous response:

  ```json
  {
    "id": 42,
    "name": "Alice"
  }
  ```

- Authenticated response:

  ```json  
  {
    "id": 42,
    "name": "Alice",
    "email": "alice@example.com",
    "orders": 12
  }
  ```

### Investigation

- Determine:

  * Which fields were added?
  * Are they appropriate for the user's authorization level?
  * Does additional data increase attack surface?

---

## Scenario 7 — Security Header Comparison

- Response A:

  ```http
  X-Frame-Options: DENY
  Content-Security-Policy: default-src 'self'
  ```

- Response B:

  ```http
  Content-Security-Policy: default-src 'self'
  ```

### Investigation

- Questions:

  * Which header disappeared?
  * Could this affect clickjacking protection?
  * Was the removal intentional?

---

## Scenario 8 — Intruder Validation

- After running an Intruder attack, two responses appear nearly identical.

- Comparer highlights:

  * Response length increased.
  * New `"downloadAllowed": true` field.
  * Status changed from `403` to `200`.

### Investigation

- Determine:

  * Which parameter caused the behavioral change?
  * Can the result be reproduced manually?
  * Does this represent an authorization issue?

---

## Investigation Framework

- For every comparison:

  1. Define the question.
  2. Compare related evidence.
  3. Identify differences.
  4. Classify each difference.
  5. Prioritize behavioral changes.
  6. Validate manually.
  7. Record confirmed observations.

- Following this framework keeps investigations systematic and reproducible.

---

## Difference Challenge

- You compare two API responses.

- Comparer reports:

  * New timestamp
  * Session cookie changed
  * HTTP `403` → `200`
  * `"role": "user"` → `"role": "admin"`
  * Added `"permissions"` array
  * Response body grew by 1,200 bytes

- Without making assumptions:

  1. Rank these differences by investigative priority.
  2. Explain why.
  3. Describe the next manual test you would perform.
  4. State what evidence you would need before reporting a vulnerability.

---

## Key Takeaways

* Comparer is most valuable when answering specific investigative questions.
* Small differences often explain major behavioral changes.
* Always validate important observations manually.
* Use structured reasoning rather than intuition.
* Evidence should drive conclusions.
