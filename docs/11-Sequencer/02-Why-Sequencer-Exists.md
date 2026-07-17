# Why Sequencer Exists

> **Module Progress**

```text
██░░░░░░░░░░░░░ 2 / 15 Lessons
```

- Modern web applications depend on unpredictable values to protect users.

- Session identifiers, password reset tokens, CSRF tokens, and authentication tokens all rely on one important property:

  > **Attackers should not be able to predict future values.**

- Determining whether that property holds is not as simple as looking at a few examples.

- That is why Burp Sequencer exists.

---

## The Problem

- Imagine collecting five session tokens:

  ```text
  F82A91
  91BC04
  AD23EF
  7C18B2
  C901FA
  ```

- At first glance, they look random.

- Now imagine collecting another thousand.

- Would you still be confident judging their quality by eye?

- Probably not.

- Humans are good at spotting obvious patterns.

- They are not good at evaluating statistical randomness across large datasets.

---

## Why Visual Inspection Isn't Enough

- Some values appear random but are generated using predictable algorithms.

- Others appear structured yet are produced securely using cryptographically strong random sources.

- Looking at a handful of examples rarely tells the full story.

- For example:

  ```text
  8F2C11
  8F2C12
  8F2C13
  8F2C14
  ```

- The pattern is obvious.

- Now compare that with:

  ```text
  B7A91F
  42E8D3
  C11A6B
  98FD02
  ```

- These *look* random.

- Appearance alone is not evidence.

---

## What Sequencer Does

- Sequencer collects a large number of values and performs statistical analysis to help determine whether they appear sufficiently unpredictable.

- It helps answer questions such as:

  * Do values repeat unexpectedly?
  * Are certain bits more predictable than others?  
  * Does the generator show measurable bias?
  * Are there patterns that deserve further investigation?

- Sequencer provides evidence—not certainty.

---

## Investigation Workflow

```text
Collect Tokens

↓

Analyze

↓

Interpret Statistics

↓

Form Hypothesis

↓

Validate

↓

Document Findings
```

- The workflow does not end when the report is generated.

- Human interpretation remains essential.

---

## Prediction Challenge

- Suppose you collect 500 session identifiers.

- Sequencer reports no obvious statistical weaknesses.

- Can you conclude the implementation is secure?

- Consider:

  * Could the application still use a weak random source?
  * Are tokens generated differently under other conditions?
  * Would additional testing improve confidence?

- Think about what Sequencer can—and cannot—prove.

---

## Common Misconceptions

### "Sequencer proves randomness."

- No.

- It evaluates whether collected values appear statistically unpredictable.

---

### "If values look random, they're secure."

- Not necessarily.

- Secure generation depends on the underlying implementation, not appearance.

---

### "One failed statistical test means the application is vulnerable."

- Not necessarily.

- Unexpected results require investigation and validation before drawing conclusions.

---

## Key Takeaways

* Human intuition is unreliable for judging randomness.
* Visual inspection alone is insufficient.
* Sequencer uses statistical analysis to support investigation.
* Statistical results guide further testing rather than replace it.
* Conclusions should always be evidence-based.
