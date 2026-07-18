# Core Concepts

> **Module Progress**

```text id="j8v2pk"
██████░░░░░░░░░ 6 / 15 Lessons
```

- Before using Burp Scanner professionally, it's important to understand the terminology that appears throughout scan configurations, issue reports, and technical discussions.

- These concepts describe how Scanner performs assessments and how findings should be interpreted.

---

## Passive Scanning

- Passive scanning analyzes normal application traffic without intentionally altering requests.

- It may identify issues such as:

  * Missing security headers
  * Information disclosure
  * Cookie attribute problems
  * Technology fingerprinting

- Passive scanning observes—it does not actively probe.

---

## Active Scanning

- Active scanning sends additional requests to test application behavior.

- Examples include:

  * Injection testing
  * Input validation checks
  * Authentication analysis
  * Common vulnerability detection

- Because active scanning interacts with the application, it should only be performed on systems you are authorized to test.

---

## Scan Scope

- The scan scope defines **what Scanner is allowed to test**.

- A well-defined scope:

  * Reduces unnecessary traffic.
  * Prevents accidental testing of unauthorized targets.
  * Improves efficiency.
  * Produces more relevant findings.

- Good scanning begins with good scope definition.

---

## Scan Configuration

- Configuration determines **how Scanner performs the assessment**.

- Examples include:

  * Scan type
  * Authentication settings
  * Performance limits
  * Included targets
  * Excluded targets

- Thoughtful configuration improves both scan quality and system stability.

---

## Insertion Points

- Insertion points are locations where Scanner can introduce test payloads during active scanning.

- Examples include:

  * URL parameters
  * Form fields
  * HTTP headers
  * Cookies
  * JSON values
  * XML elements

- The available insertion points influence which tests Scanner can perform.

---

## Severity

- Severity estimates the **potential impact** of a confirmed issue.

- Typical levels include:

  * High
  * Medium
  * Low
  * Information

- Severity answers:

  > **"If this issue is real, how serious could it be?"**

---

## Confidence

- Confidence estimates how strongly the available evidence supports the reported finding.

- Typical levels include:

  * Certain
  * Firm
  * Tentative

- Confidence answers:

  > **"How convincing is the evidence?"**

- Confidence is not the same as proof.

---

## Crawl and Audit

- Scanner generally performs two complementary activities:

**Crawl**

* Discover application content.
* Identify pages and endpoints.
* Build knowledge of the application.

**Audit**

* Test discovered content for security issues.
* Analyze behavior.
* Produce findings.

Discovery comes before assessment.

---

## Core Relationship

```text id="h4m7ry"
Scope

↓

Discover Content

↓

Identify Insertion Points

↓

Passive Analysis

↓

Active Testing

↓

Generate Findings

↓

Manual Validation
```

- Every stage builds on the previous one.

- Skipping an earlier stage often reduces the quality of later results.

---

## Scanner Challenge

- During a scan you notice:

  * High severity
  * Tentative confidence

- Ask yourself:

  * Why is confidence not higher?
  * What evidence is available?
  * How would you reproduce the finding?
  * What additional testing could strengthen or weaken the conclusion?

- Good testers investigate uncertainty rather than ignoring it.

---

## Quick Reference

| Concept          | Purpose                                     |
| ---------------- | ------------------------------------------- |
| Passive Scan     | Observe traffic without modifying it        |
| Active Scan      | Send test requests to evaluate security     |
| Scope            | Defines what may be tested                  |
| Configuration    | Controls scan behavior                      |
| Insertion Points | Locations where payloads are introduced     |
| Severity         | Potential impact of a confirmed issue       |
| Confidence       | Strength of the supporting evidence         |
| Crawl            | Discover application content                |
| Audit            | Test discovered content for vulnerabilities |

---

## Key Takeaways

* Passive and active scanning serve different purposes.
* Scope determines what Scanner is allowed to test.
* Configuration affects scan quality.
* Insertion points determine where Scanner can test.
* Severity and confidence answer different questions.
* Crawling discovers; auditing investigates.
