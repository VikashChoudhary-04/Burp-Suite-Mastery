# Interface

> **Module Progress**

```text id="t5v8wr"
███░░░░░░░░░░░░ 3 / 15 Lessons
```

- The Burp Collaborator interface is designed to help testers generate unique Collaborator addresses and review any external interactions associated with them.

- Unlike tools such as Repeater or Proxy, Collaborator is focused on **observing evidence** rather than modifying requests.

---

## Main Components

- The Collaborator client generally consists of several key areas:

  * Collaborator server status
  * Generated Collaborator payloads
  * Interaction polling
  * Recorded interactions
  * Interaction details

- Together, these components support the investigation of out-of-band behavior.

---

## Collaborator Payload

- A Collaborator payload is a unique address generated for a specific investigation.

- Each payload acts as a controlled endpoint that can receive external interactions initiated by the target application.

- Using separate payloads for different tests helps keep investigations organized and makes it easier to associate observed interactions with a particular assessment.

---

## Polling for Interactions

- Collaborator does not always receive interactions immediately.

- Instead, the client periodically checks (or **polls**) for any new activity associated with generated payloads.

- This is important because:

  * Some applications process requests asynchronously.
  * Background jobs may introduce delays.
  * External interactions may occur several seconds—or longer—after the initial request.

- Patience is often part of effective out-of-band testing.

---

## Recorded Interactions

- When an external interaction occurs, the Collaborator client records information about it.

- Depending on the interaction type, the record may include:

  * Interaction type (such as DNS or HTTP)
  * Time observed
  * Associated payload
  * Basic protocol information
  * Metadata useful for investigation

- These records provide evidence that the target application initiated an outbound interaction.

---

## Reviewing Interaction Details

- Each recorded interaction should be reviewed carefully.

- Rather than asking:

  > "Is this a vulnerability?"

- Ask:

  * What behavior occurred?
  * When did it occur?
  * Which payload triggered it?
  * Does it match the expected application behavior?
  * What additional investigation is needed?

- The interface supports investigation—not automatic conclusions.

---

## Interface Workflow

```text id="y4n2qp"
Generate Payload
        ↓
Use During Assessment
        ↓
Poll for Interactions
        ↓
Review Recorded Evidence
        ↓
Investigate Context
        ↓
Validate Findings
```

- Each stage contributes to building reliable evidence.

---

## Collaborator Challenge

- During an assessment, you generate three different Collaborator payloads for three separate features of an application.

- Later, only one payload records an HTTP interaction.

- Consider:

  * Which feature likely triggered the interaction?
  * Why is using separate payloads helpful?
  * What would you investigate next before reaching a conclusion?

- Organized testing makes interpretation easier.

---

## Professional Habits

- Experienced testers often:

  * Use unique payloads for different tests.
  * Label investigations clearly in their notes.
  * Poll for interactions throughout the assessment.
  * Review interaction details carefully.
  * Correlate Collaborator evidence with application behavior.

- Good organization improves confidence in the final report.

---

## Key Takeaways

* The Collaborator interface centers on observing outbound interactions.
* Unique payloads help separate investigations.
* Polling retrieves new interaction data.
* Recorded interactions are evidence, not conclusions.
* Context determines the significance of each interaction.
