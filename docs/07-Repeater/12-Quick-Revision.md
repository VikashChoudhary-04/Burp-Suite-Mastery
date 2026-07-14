# Quick Revision

> **Estimated Reading Time:** 2–3 Minutes

- Use this page before:

  * Interviews
  * Bug bounty hunting
  * Penetration tests  
  * Labs
  * CTFs
  * Practice sessions

---

## What is Repeater?

- Burp Repeater is a **manual HTTP request editor** that allows you to:

  * Modify requests
  * Replay requests
  * Compare responses
  * Investigate application behavior

- It is the primary manual testing tool in Burp Suite.

---

## Professional Workflow

```text
Capture Request

↓

Understand It

↓

Baseline

↓

Hypothesis

↓

One Change

↓

Send

↓

Compare

↓

Collect Evidence

↓

Next Experiment
```

- Never skip the baseline.

---

## Investigation Mindset

- Always ask:

  * What is this request doing?
  * Why does this parameter exist?
  * What assumptions is the server making?
  * What happens if this value changes?
  * What evidence supports my conclusion?

---

## What Can Be Modified?

✓ URL

✓ Method

✓ Path

✓ Parameters

✓ Headers

✓ Cookies

✓ JSON

✓ XML

✓ Body

✓ Authentication

---

## What Should Be Compared?

✓ Status Code

✓ Headers

✓ Cookies

✓ Response Body

✓ Response Length

✓ Redirects

✓ Error Messages

✓ Timing (when relevant)

---

## Golden Rules

### Rule 1

- Always establish a baseline.

---

### Rule 2

- Modify one variable at a time.

---

### Rule 3

- Record observations.

---

### Rule 4

- Build hypotheses before testing.

---

### Rule 5

- Evidence before conclusions.

---

### Rule 6

- Reproduce unusual behavior.

---

## Common Mistakes

- ❌ Random modifications

- ❌ No baseline

- ❌ Multiple changes at once

- ❌ Ignoring headers

- ❌ Poor documentation

- ❌ Jumping to conclusions

---

## Professional Habits

- Read before editing.

- Think before sending.

- Observe before concluding.

- Document before forgetting.

---

## Investigation Loop

```text
Observe

↓

Question

↓

Hypothesis

↓

Experiment

↓

Evidence

↓

Conclusion

↓

Repeat
```

---

## Response Checklist

- When a response arrives, check:

  □ Status Code

  □ Headers

  □ Cookies

  □ Body

  □ Length

  □ Redirects

  □ Reflection

  □ Errors

---

## Questions Every Pentester Should Ask

* Is this expected?
* Is this reproducible?
* Does this violate the application's intended security model?
* Do I have enough evidence?
* What should I test next?

---

## Interview Revision

- Can you explain:

  * What Repeater is?
  * Why it exists?
  * Difference between Proxy and Repeater?
  * Why a baseline matters?
  * Why one variable at a time?
  * Why evidence matters?

- If not, revisit the relevant lesson.

---

## Repeater Mastery Checklist

- I can:

  ☐ Capture requests

  ☐ Send requests to Repeater

  ☐ Modify parameters

  ☐ Modify headers

  ☐ Modify cookies

  ☐ Modify request bodies

  ☐ Compare responses

  ☐ Build hypotheses
  
  ☐ Collect evidence

  ☐ Document findings

  ☐ Explain my methodology

  ☐ Use Repeater confidently during an assessment

---

## Module Summary

- Congratulations!

- You have completed the **Repeater** module.

- You now understand:

  * How Repeater works
  * Why professionals rely on it
  * How to edit requests
  * How to analyze responses
  * How to conduct structured investigations
  * How to avoid common mistakes
  * How to think like a penetration tester

- Repeater is no longer just a Burp Suite feature.

- It is now one of your primary investigative tools.
