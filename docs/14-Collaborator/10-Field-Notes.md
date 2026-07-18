# Field Notes

> **Module Progress**

```text
██████████░░░░░ 10 / 15 Lessons
```

- Burp Collaborator becomes most valuable when it is used with disciplined investigation habits.

- The following field notes capture practical lessons that help testers maintain strong correlation, reduce confusion, and turn out-of-band interactions into reliable evidence.

---

## Use Unique Payloads Early

- Do not wait until an interaction appears before thinking about correlation.

- Assign unique Collaborator payloads to separate tests whenever practical.

  ```text
  Feature A → Payload A
  
  Feature B → Payload B

  Feature C → Payload C
  ```

- If Payload B later receives an interaction, identifying the likely trigger becomes much easier.

- Good evidence begins with good test design.

---

## Keep a Payload Journal

- For meaningful OAST tests, record:

  * Feature tested
  * Unique payload
  * Request or test case
  * Submission time
  * Immediate response
  * Interaction time
  * Interaction type
  * Validation status

- This prevents confusion during long assessments.

---

## Record Time Carefully

- Timing can reveal important clues.

- Consider:

  ```text
  Request Submitted
        ↓
  2 seconds
        ↓
  DNS Interaction
        ↓
  1 second
        ↓
  HTTP Interaction
  ```

- Compare this with:

  ```text
  Request Submitted
        ↓
  10 minutes
        ↓
  HTTP Interaction
  ```

- The second pattern may suggest asynchronous or background processing.

- Timing does not prove architecture, but it helps guide investigation.

---

## Expect Delayed Callbacks

- Do not stop thinking about a test simply because nothing happens immediately.

- Possible causes of delay include:

  * Queues
  * Background workers
  * Scheduled jobs
  * Retry mechanisms
  * Deferred processing

- Poll again when the application's workflow suggests delayed execution.

---

## Distinguish Signal From Noise

- Not every interaction necessarily represents meaningful application behavior.

- Ask:

  * Was the payload unique?
  * Does the timing match the test?
  * Can the behavior be reproduced?
  * Could another system have processed the identifier?
  * Is the interaction expected functionality?

- Strong correlation helps separate useful evidence from unrelated activity.

---

## DNS Is a Clue, Not the Whole Story

- A DNS interaction can be extremely useful.

- But phrase the observation accurately:

  > "The supplied hostname was resolved."

- Do not automatically upgrade that statement to:

  > "The server successfully connected to an arbitrary external system."

- Different evidence supports different conclusions.

---

## Preserve the Original Request

- Always keep the request or workflow associated with an important interaction.

- A Collaborator record without the triggering test context is much less useful.

- Strong evidence connects:

  ```text
  Original Request
  
  ↓

  Unique Payload

  ↓

  Observed Interaction

  ↓
  
  Reproduction

  ↓

  Security Impact
  ```

- Preserve the complete chain.

---

## Re-Test With a Fresh Payload

- When a significant interaction appears and reproduction is appropriate, use a new unique payload.

- This helps determine whether the behavior is consistently associated with the tested feature.

- Avoid repeatedly reusing the original identifier when fresh correlation would provide clearer evidence.

---

## Watch for Interaction Sequences

- Individual interactions may form a pattern.

- For example:

  ```text
  DNS

  ↓

  HTTP
  
  ↓

  Delayed HTTP
  ```

- The sequence may reveal more about application behavior than any single event.

- Record the order and timing rather than treating every interaction independently.

---

## Do Not Over-Interpret Source Information

- The system visible in the interaction metadata may not be the original application server.

- Infrastructure may involve:

  * DNS resolvers
  * Proxies
  * Workers
  * Gateways
  * Shared services

- State only what the evidence supports.

- Avoid inventing an architecture from one IP address.

---

## Validate Before Escalating Severity

- An interesting interaction is not automatically a high-severity vulnerability.

- Before assigning impact, establish:

  * Root cause
  * Attacker control
  * Reachable security boundary
  * Reproducibility
  * Realistic consequences

- Interaction strength and vulnerability severity are different concepts.

---

## Know When You Have Enough Evidence

- More testing is not always better.

- Once you have:

  * Strong correlation
  * Reliable reproduction
  * Clear root cause
  * Sufficient impact evidence

additional callbacks may add little value.

- Stop when the security question has been answered sufficiently.

---

## Investigation Notes Example

- A concise OAST journal entry might look conceptually like:

  ```text
  Feature:
  External resource import

  Hypothesis:
  Server-side processing of supplied URL

  Payload:
  Unique identifier assigned to this test

  Submitted:
  14:32:10

  Application Response:
  Import scheduled

  Observed:
  DNS at 14:32:34
  HTTP at 14:32:35

  Reproduced:
  Yes, with fresh unique payload

  Status:
  Requires impact assessment
  ```

- Clear notes make later reporting much easier.

---

## Collaborator Challenge

- During an assessment:

  * Payload A produces DNS immediately.
  * Payload B produces nothing initially but HTTP ten minutes later.
  * Payload C produces repeated DNS interactions over an hour.

- Ask yourself:

  * Which behavior may indicate asynchronous processing?
  * Why might repeated interactions matter?
  * What additional context is needed?
  * Which tests should be reproduced?
  * When would you decide you have enough evidence?

- Treat interaction patterns as clues—not automatic conclusions.

---

## Professional Habits

- Experienced testers:

  * Assign unique payloads deliberately.
  * Maintain a payload journal.
  * Preserve original requests.
  * Track timing and interaction sequences.
  * Reproduce with fresh identifiers.
  * Distinguish DNS evidence from stronger network evidence.
  * Avoid over-attributing infrastructure.
  * Stop testing when sufficient evidence exists.

---

## Key Takeaways

* Strong OAST investigations depend on strong correlation.
* Timing can reveal asynchronous behavior.
* Unique payloads reduce ambiguity.
* Preserve the complete evidence chain.
* Interaction sequences may reveal useful patterns.
* Reproduction should use fresh identifiers when practical.
* Severity must be based on validated impact.
* Know when further testing no longer adds meaningful evidence.
