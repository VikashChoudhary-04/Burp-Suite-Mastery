# Core Concepts

> **Module Progress**

```text
██████░░░░░░░░░ 6 / 15 Lessons
```

- Comparer becomes far more useful when you understand *what kinds of differences you're looking at*.

- Not every highlighted change has the same meaning.

- Professional testers classify differences before deciding whether to investigate them further.

---

## Concept 1 — Structural vs. Value Differences

- Some differences change the **structure** of the data.

- Example:

  ```json  
  {
    "username": "alice",
    "role": "user"  
  }
  ```

becomes

  ```json  
  {
    "username": "alice",
    "role": "user",
    "permissions": ["read", "write"]
  }
  ```

- A new field was added.

- Other differences only change a value:

  ```json
  "role": "user"
  ```

becomes

```json
"role": "admin"
```

- Both are important, but they tell different stories.

---

## Concept 2 — Cosmetic vs. Behavioral Differences

- Some changes only affect presentation.

- Examples:

  * Whitespace
  * Formatting
  * Line ordering (where order is irrelevant)
  * Pretty-printed JSON
  * Header capitalization (when protocol rules make it equivalent)

- These are usually **cosmetic**.

- Behavioral differences may affect how the application responds.

- Examples include:

  * Status code changes
  * Authorization results
  * New permissions
  * Different account data
  * Missing security controls

- Always prioritize behavioral differences.

---

## Concept 3 — Expected vs. Unexpected Changes

- Some values naturally change every request.

- Expected:

  * Timestamp
  * Request ID
  * Nonce
  * CSRF token
  * Session identifier

- Unexpected:

  * User role
  * Account owner
  * HTTP status
  * Authorization decision
  * Sensitive response data

- Learning this distinction prevents you from chasing noise.

---

## Concept 4 — Additions, Removals, and Modifications

- Comparer generally reveals three kinds of changes:

### Addition

- New data appears.

- Example:

  ```json
  "isAdmin": true
  ```

### Removal

- Existing data disappears.

- Example:

  ```http
  X-Frame-Options
  ```

is no longer present.

### Modification

- Existing data changes.

- Example:

  ```http
  HTTP/1.1 403 Forbidden
  ```

becomes

  ```http
  HTTP/1.1 200 OK
  ```

- Each type can indicate a different security story.

---

## Concept 5 — Symmetric vs. Asymmetric Comparisons

- Sometimes you're comparing equivalent situations:

  * User A vs. User B
  * Before vs. After login
  * Request 1 vs. Request 2

- These are **symmetric** comparisons.

- Other times you're comparing intentionally different scenarios:

  * User vs. Admin
  * Authenticated vs. Anonymous
  * Normal request vs. Tampered request

- These are **asymmetric** comparisons.

- Understanding your comparison type helps you interpret the results correctly.

---

## Concept 6 — Difference Density

- Not all comparisons contain the same amount of change.

- Low difference density:

  * Only a few values changed.

- High difference density:

  * Many fields changed.

- High difference density often requires breaking the problem into smaller comparisons rather than trying to interpret everything at once.

---

## Concept 7 — Comparison Goals

- Before opening Comparer, define your objective.

- Examples:

  * Did authorization change?
  * Did parameter tampering work?
  * Did login modify the session?
  * Did the API expose additional fields?
  * Did the server return different permissions?

- A comparison without a goal is much harder to interpret.

---

## Concept 8 — Evidence Over Assumptions

- Comparer shows evidence.

- It does **not** explain why the difference exists.

- For example:

  ```http
  HTTP/1.1 200 OK
  ```

- instead of

  ```http
  HTTP/1.1 403 Forbidden
  ```

- The difference is real.

- The cause might be:

  * Authentication
  * Authorization
  * Caching
  * A configuration change
  * A testing mistake

- Always investigate before concluding.

---

## Classification Workflow

```text
Compare Two Items

        │

        ▼

Identify Differences

        │

        ▼

Classify Each Difference

        │

        ├── Cosmetic
        ├── Behavioral
        ├── Expected
        ├── Unexpected
        ├── Structural
        └── Value

        ▼

Prioritize Investigation
```

- Classification turns raw differences into actionable evidence.

---

## Difference Challenge

- You compare two responses.

- The differences are:

  * `403` → `200`
  * New `"permissions"` field
  * Updated timestamp
  * Different request ID
  * Added `X-Frame-Options` header
  * `"role": "user"` → `"role": "admin"`

- For each difference:

  1. Classify it.
  2. Decide whether it is expected.
  3. Decide whether it could affect application behavior.
  4. Explain which one you would investigate first.

---

## Key Takeaways

* Differences can be structural, value-based, cosmetic, or behavioral.
* Separate expected variation from unexpected behavior.
* Additions, removals, and modifications often have different implications.
* Define your comparison goal before using Comparer.
* Treat every highlighted difference as evidence that requires interpretation.
