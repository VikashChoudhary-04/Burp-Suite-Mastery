# Behind the Scenes

> **Module Progress**

```text id="x9m4tr"
████░░░░░░░░░░░ 4 / 15 Lessons
```

- When you start a scan, Burp Scanner does far more than simply send requests.

- It systematically observes application behavior, performs automated security checks, analyzes responses, and records evidence that may indicate potential vulnerabilities.

- Understanding this process helps you interpret scan results more accurately and validate them effectively.

---

## Step 1 — Discover the Target

- Scanner begins by identifying the parts of the application that are within scope.

- This may include:

  * Pages
  * Endpoints  
  * Parameters
  * Forms
  * APIs
  * Linked resources

- Only the configured scope should be tested.

- A well-defined scope reduces unnecessary traffic and focuses testing on authorized targets.

---

## Step 2 — Observe the Application

- Before performing active tests, Scanner can gather information by observing normal application traffic.

- This passive analysis may identify:

  * Missing security headers
  * Information disclosure
  * Outdated technologies
  * Cookie attributes
  * Configuration weaknesses

- Passive analysis does not intentionally modify application behavior.

---

## Step 3 — Perform Active Testing

- Where appropriate, Scanner sends additional requests to evaluate how the application responds.

- Examples include testing for:
  
  * Injection points
  * Input validation
  * Authentication weaknesses
  * Common web vulnerabilities

- These tests intentionally interact with the application, which is why active scanning should only be performed on authorized targets.

---

## Step 4 — Analyze Responses

- Scanner compares responses against expected patterns.

- It looks for evidence such as:

  * Unexpected output
  * Error messages
  * Behavioral differences
  * Reflected input
  * Missing protections

- The goal is to identify observations that may indicate a security issue.

---

## Step 5 — Record Findings

- When sufficient evidence is detected, Scanner records a finding.

- Each finding typically includes:

  * Issue type
  * Severity
  * Confidence
  * Supporting evidence
  * Relevant requests and responses

- A finding represents a hypothesis supported by evidence—not a confirmed vulnerability.

---

## Conceptual Workflow

```text id="q2w7jk"
Discover Target

↓

Observe Traffic

↓

Active Testing

↓

Analyze Responses

↓

Generate Findings

↓

Manual Validation
```

- Automation generates evidence.

- The tester determines whether that evidence supports a valid conclusion.

---

## Severity vs Confidence

- Scanner often reports both **severity** and **confidence**.

- Think of them as different questions:

  * **Severity:** If this issue is real, how serious could its impact be?
  * **Confidence:** How certain is Scanner that the evidence indicates the reported issue?

- A high-severity issue with low confidence deserves investigation—not immediate reporting.

---

## Scanner Challenge

- Scanner reports a high-severity issue with medium confidence.

- Ask yourself:

  * What evidence supports the finding?
  * Can I reproduce it manually?
  * Are there alternative explanations?
  * What additional testing would increase confidence?

- Good reports are built on verified evidence.

---

## Common Misunderstandings

### "Scanner confirms vulnerabilities."

- No.

- It reports findings based on observed evidence.

- Confirmation requires manual investigation.

---

### "Passive findings are always harmless."

- Not necessarily.

- Some passive observations reveal significant security weaknesses even without active interaction.

---

### "Confidence equals certainty."

- No.

- Confidence reflects how strongly the available evidence supports the finding—not absolute proof.

---

## Key Takeaways

* Scanner combines passive observation with active testing.
* Findings are based on evidence collected during analysis.
* Severity and confidence describe different aspects of a finding.
* Manual validation converts findings into confirmed vulnerabilities.
* Understanding Scanner's process leads to better investigations.
