# Interface

> **Module Progress**

```text
███░░░░░░░░░░░░ 3 / 15 Lessons
```

- Burp Sequencer provides a workspace for collecting, analyzing, and interpreting security-critical values.

- Unlike tools such as Proxy or Repeater, Sequencer focuses on **one type of evidence**:

  > Generated values that should be difficult for an attacker to predict.

- The interface is organized to support that investigation from collection to analysis.

---

## Main Components

- The Sequencer interface generally includes:

  * Live capture or selected request
  * Token location configuration
  * Collection controls
  * Sample counter
  * Analysis controls
  * Statistical results
  * Graphs and visualizations
  * Summary findings

- Each component contributes to answering the same question:

  > **Are these values sufficiently unpredictable?**

---

## Target Value Selection

- Before analysis begins, Sequencer must know **which value** to evaluate.

- Examples include:

  * Session cookies
  * CSRF tokens
  * Password reset tokens
  * Authorization tokens
  * Custom identifiers

- Choosing the correct value is one of the most important parts of the investigation.

- Analyzing the wrong value produces meaningless results.

---

## Collection Controls

- Sequencer gathers many samples before analysis.

- The collection controls allow you to:

  * Start collection
  * Stop collection
  * Monitor progress
  * Build a sufficiently large sample

- Larger sample sizes generally provide stronger statistical evidence than a handful of values.

---

## Analysis Controls

- After collecting enough samples, Sequencer performs statistical analysis.

- The interface provides controls to:

  * Start analysis
  * Review completed analysis
  * Examine statistical outputs

- Analysis should only begin after you have collected an appropriate dataset.

---

## Results Overview

- The results area summarizes the collected evidence.

- Depending on the dataset, you may see:

  * Overall assessment
  * Statistical measurements
  * Visual summaries
  * Individual test results
  * Confidence indicators

- These outputs support investigation—they do not replace professional judgment.

---

## Graphs and Visualizations

- Graphs help you recognize trends that may be difficult to notice in raw data.

- Visualizations can highlight:

  * Distribution patterns
  * Bias
  * Unexpected clustering
  * Other statistical characteristics

- Use them as supporting evidence rather than proof.

---

## Investigation Workflow

```text
Identify Token

↓

Collect Samples

↓

Run Analysis

↓

Review Results

↓

Interpret Findings

↓

Validate Conclusions
```

- The interface mirrors the investigation process.

---

## Prediction Challenge

- You accidentally configure Sequencer to analyze a timestamp instead of a session identifier.

- The statistical report indicates obvious predictability.

- Ask yourself:

  * Does this indicate a vulnerability?
  * Did you analyze the correct value?
  * What should you verify before reporting a finding?

- Correct target selection is as important as the analysis itself.

---

## Practical Tips

* Verify the token being analyzed.
* Collect enough samples before drawing conclusions.
* Keep notes on how values were collected.
* Avoid mixing different token types in one dataset.
* Repeat testing if collection conditions change.

---

## Key Takeaways

* Sequencer's interface supports a structured investigation.
* Selecting the correct token is essential.
* Statistical results depend on good sample collection.
* Visualizations complement—not replace—analysis.
* Every result should be interpreted in context.
