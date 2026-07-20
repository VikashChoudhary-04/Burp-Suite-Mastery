# How Burp Tools Share Traffic and Data

> **Module 04 — How Burp Suite Works**

> **Progress**

```text
████████░░░ 8 / 11 Lessons
```

- Burp Suite is described as an **integrated platform** because its tools are designed to work together.

- A request may begin in the browser, pass through Proxy, appear in HTTP history and Target, then be copied into Repeater, Intruder, Scanner, Sequencer, or an extension for further analysis.

- However, this creates an important distinction:

  ```text
  Observed Traffic

  ≠

  Copied Request

  ≠

  Newly Generated Traffic
  ```  

- Understanding these differences prevents one of the most common Burp mistakes: assuming that every tool is operating on the same live request or sharing exactly the same state.

- A useful mental model is:

  ```text
  Browser Traffic

  ↓

  Burp Proxy

  ↓

  Observed / Recorded Traffic
  
  ↓
  
  Select Interesting Request
  
  ├── Send Copy to Repeater
  ├── Send Copy to Intruder
  ├── Send Copy to Sequencer
  ├── Audit / Scan
  ├── Compare Data
  ├── Decode Data
  └── Extension Processing

  ↓

  Tools May Generate New Independent Activity
  ```  

- Burp's integration makes movement between tools easy, but each tool has a different role.

---

## Learning Objectives

- By the end of this lesson, you should be able to:

  * Explain how traffic enters Burp.
  * Distinguish observed traffic from generated traffic.
  * Understand how Proxy and HTTP history relate.
  * Understand how Target builds application knowledge.
  * Explain what happens when a request is sent to Repeater.
  * Explain how Intruder generates requests from a base request.
  * Understand Scanner-generated traffic conceptually.
  * Explain Sequencer's relationship to captured tokens.
  * Distinguish Decoder and Comparer from network-generating tools.
  * Understand Logger as a broader diagnostic view.
  * Explain how Collaborator interacts with other Burp workflows.
  * Understand how extensions can observe, modify, or generate traffic.
  * Recognize stale state when copying requests between tools.
  * Trace the origin of a request before interpreting its results.

---

## The Integrated Burp Model

- A simplified architecture:

  ```text
  Browser / Client

  ↓

  Burp Proxy

  ├── Intercept
  ├── HTTP History
  ├── Target Knowledge
  ├── Logger
  └── Extensions

  ↓

  Interesting Request Identified

  ├── Repeater
  ├── Intruder
  ├── Scanner
  ├── Sequencer
  ├── Decoder
  ├── Comparer
  └── Other Extensions
  ```

- Not every arrow represents the same kind of data movement.

- Sometimes a tool:

  ```text
  Observes Existing Traffic
  ```

- Sometimes it:

  ```text
  Receives a Copy
  ```

- Sometimes it:

  ```text
  Generates New Requests
  ```

- Sometimes it:

  ```text
  Performs Local Analysis Only
  ```

- These distinctions matter.

---

## Four Tool Behavior Categories

- A useful classification is:

  ```text
  1. Observe / Record Traffic

  2. Receive and Manipulate Copies

  3. Generate New Network Traffic

  4. Perform Local Analysis
  ```

- Some tools can belong to more than one category depending on how they are used.

---

### Category 1 — Observe or Record Traffic

- Examples include:

  ```text
  Proxy HTTP History

  Target Site Map

  Logger

  Some Extensions
  ```

- These can learn from traffic already passing through Burp.

- Conceptually:
  
  ```text
  Browser

  ↓

  Request

  ↓

  Burp

  ├── Record
  ├── Analyze
  └── Forward

  ↓

  Server
  ```

- The recording itself does not necessarily create another request.

---

### Category 2 — Receive a Copy

- Examples:

  ```text
  Repeater

  Intruder Base Request
  
  Sequencer Token Source
      
  Comparer Input
      
  Decoder Input
  ```

- A request or piece of data can be copied from one Burp context into another.

- Conceptually:

  ```text
  Original Request

  ↓

  Copy
  
  ↓
  
  Tool
  ```  

- The original message and the copied version are now separate artifacts.

---

### Category 3 — Generate New Traffic

- Examples can include:

  ```text
  Repeater

  Intruder

  Scanner

  Sequencer Live Capture

  Extensions
  ```  

- When these tools send requests, they create new server interactions.

- Conceptually:

  ```text
  Stored / Base Request

  ↓
  
  Tool Sends
  
  ↓
  
  New Network Request

  ↓

  Server

  ↓

  New Response
  ```

- This is not merely re-reading the old response.

---

### Category 4 — Local Analysis

- Examples:

  ```text
  Decoder

  Comparer
  ```  

- These generally work on data locally unless another workflow explicitly sends network traffic elsewhere.

- Conceptually:

  ```text
  Input Data

  ↓

  Local Processing

  ↓

  Output
  ```  

- No target request is inherently required.

---

## Proxy — The Main Traffic Gateway

- Proxy commonly receives browser traffic.

  ```text
  Browser
  
  ↓

  Proxy Listener

  ↓

  Burp

  ↓

  Target
  ```

- Proxy allows you to:

  * Intercept requests.
  * Intercept selected responses.
  * Observe HTTP history.
  * Inspect messages.
  * Send interesting items to other Burp tools.

- Proxy is therefore one of the main entry points for live application traffic.

---

## HTTP History

- HTTP history acts as a record of proxy traffic.

- Example:

  ```text
  Browser Request A

  ↓

  Burp

  ↓

  HTTP History Entry A
  ```

- Then:

  ```text
  Browser Request B

  ↓

  Burp

  ↓
  
  HTTP History Entry B
  ```

- This allows you to reconstruct application behavior without manually pausing every request.

---

## HTTP History Does Not Mean "Intercepted Traffic Only"

- A request can be:

  ```text
  Automatically Forwarded
  ```  

and still appear in history.

- Therefore:

  ```text
  HTTP History

  ≠
  
  List of Only Manually Intercepted Requests
  ```

- This is why Intercept OFF remains useful during application mapping.

---

## Target — Building Application Knowledge

- The Target tool helps organize information about applications and hosts Burp has observed or been configured to work with.

- A simplified flow:

  ```text
  Observed Traffic

  ↓

  Host / Path / Request Knowledge
  
  ↓
  
  Target Site Map
  ```

- This can create a hierarchical representation such as:

  ```text
  example.com

  ├── /
  ├── login
  ├── account
  ├── api
  │   ├── profile
  │   └── orders
  └── static
  ```

- Target provides structure around discovered application content.

---

## Proxy vs Target

- Think:

  ```text
  Proxy

  =
  
  Traffic Flow and History
  ```

- while:

  ```text
  Target

  =

  Application Structure and Scope Context
  ```

- They overlap in the information they expose, but they serve different investigation purposes.

---

## Passive Discovery

- Suppose the browser requests:

  ```text
  GET /

  GET /app.js
  
  GET /api/profile
  
  GET /api/orders
  ```  
  
- Burp may use observed traffic to build application knowledge.

- Conceptually:

  ```text
  Live Traffic

  ↓
  
  Passive Observation
  
  ↓
  
  Site Map Growth
  ```

- No additional requests are necessarily required just to record what was already observed.

---

## Active Discovery Is Different

- Some Burp functionality may actively request resources.

- Conceptually:

  ```text
  Known Application Information

  ↓

  Burp Generates Additional Requests

  ↓

  New Resources / Behavior Discovered
  ```

- This is fundamentally different from passive observation.

- Always know whether a feature is:

  ```text
  Passive

  or
  
  Active
  ```

before using it against a target.

---

## Repeater — A Copied Request Becomes Independent

- Suppose Proxy captures:

  ```http
  GET /api/profile HTTP/1.1
  Host: example.com
  Cookie: session=abc123
  ```

- You choose:

  ```text
  Send to Repeater
  ```

- Conceptually:

  ```text
  Proxy Request

  ↓

  COPY

  ↓  

  Repeater Tab
  ```  

- The Repeater request is now independent.

---

## Repeater Does Not Control the Original Browser Request

- After copying:

  ```text
  Browser Request

  and

  Repeater Request
  ```
  
are separate.

- Changing:

  ```text
  Cookie: session=abc123
  ```

- to:

  ```text
  Cookie: session=xyz789
  ```

in Repeater does not modify the request that already passed through Proxy.

---

## Sending From Repeater Creates New Traffic

- When you click Send:

  ```text
  Repeater

  ↓

  Burp Networking

  ↓

  Target

  ↓

  New Response

  ↓

  Repeater
  ```  
  
- This is a new server interaction.

- If you click Send five times:

  ```text
  Send 1 → Request 1

  Send 2 → Request 2
  
  Send 3 → Request 3

  Send 4 → Request 4

  Send 5 → Request 5
  ```

- The server may process the operation five times.

- This matters greatly for state-changing requests.

---

## Repeater and Stale State

- Suppose you copy:

  ```http
  Cookie: session=old123
  ```

into Repeater.

- Later, the browser logs in again and receives:

  ```http
  Cookie: session=new456
  ```

- The existing Repeater tab may still contain:

  ```http
  Cookie: session=old123
  ```

- Therefore:

  ```text
  Current Browser State

  ≠

  Automatically Current Repeater State
  ```

- Always inspect:

  * Cookies.
  * Authorization headers.
  * CSRF tokens.
  * Timestamps.
  * Signatures.
  * Nonces.

before replaying old requests.

---

## Intruder — Base Request Plus Payload Generation

- Intruder commonly starts with a request copied into it.

- Conceptually:

  ```text
  Captured Request

  ↓

  Send to Intruder
  
  ↓
  
  Base Request
  ```  

- You then define payload positions.

- Example:

  ```http
  GET /product?id=§105§ HTTP/1.1
  Host: example.com
  ```

- Intruder can generate variations.

---

## Intruder Generates New Requests

- Payloads:

  ```text
  105

  106

  107
  ```

- produce:

  ```text
  GET /product?id=105

  GET /product?id=106

  GET /product?id=107
  ```  

- Each is a new request.

- Therefore:

  ```text
  One Base Request

  ↓
  
  Many Generated Requests
  ```

---

## Intruder Is Not Editing the Browser

- The browser does not automatically perform these requests.

- Intruder acts as its own request-generating tool through Burp.

- Conceptually:

  ```text
  Browser

  ×

  Not Required for Each Payload
  ```  

- Instead:

  ```text
  Intruder

  ↓
  
  Burp

  ↓

  Target
  ```

---

## Why Intruder Requires Care

- Generated traffic can cause:

  ```text
  Rate Limiting

  Account Lockout

  Data Modification

  Duplicate Transactions

  Large Server Load

  Alerting

  Unexpected State Changes
  ```  

- Use it only within authorization and with controlled request volume.

---

## Scanner — New Audit Traffic

- Burp Scanner can analyze application traffic and, during active auditing, generate additional requests to investigate potential vulnerabilities.

- Conceptually:

  ```text
  Observed Request

  ↓

  Scanner

  ↓

  Analysis
  
  ↓

  Additional Test Requests

  ↓

  Target
  ```  

- These requests may differ substantially from the browser's original request.

---

## Passive vs Active Scanning

- A crucial distinction:

  ```text
  Passive Analysis

  ↓

  Analyze Existing Traffic
  ```  

- versus:

  ```text
  Active Audit

  ↓

  Generate New Requests
  ```  

- Passive analysis generally observes messages without needing to modify target state through additional attack requests.

- Active auditing interacts with the target.

---

## Why Scanner Traffic Matters

- Suppose logs show:

  ```text
  Unusual Parameter Values

  Unexpected HTTP Methods

  Additional Requests
  ```  

- Do not immediately assume:

  ```text
  The browser sent them.
  ```  

- They may have been generated by:

  ```text
  Scanner
  ```

or another Burp tool.

- Request origin matters when interpreting logs and application behavior.

---

## Sequencer — Token Analysis

- Sequencer is designed to analyze the quality of tokens that should be unpredictable.

- Examples may include:

  ```text
  Session Tokens

  CSRF Tokens

  Password Reset Tokens

  Other Security-Relevant Random Values
  ```  
  
- Its workflow can involve collecting many token samples.

---

## Sequencer Live Capture

- Conceptually:

  ```text
  Token-Generating Request

  ↓

  Sequencer Sends Repeated Requests

  ↓

  Responses Contain Tokens

  ↓

  Tokens Extracted
  
  ↓

  Statistical Analysis
  ```  

 - This can generate substantial traffic.

- Therefore:

  ```text
  Sequencer Analysis

  may require

  Many New Requests
  ```  

depending on the workflow.

---

## Sequencer Is Not Just Looking at One Token

- One token cannot establish randomness quality.

- The conceptual process is:

  ```text
  Token 1

  Token 2

  Token 3
  
  ...

  Many Samples
  
  ↓

  Statistical Analysis
  ```

- This is why understanding request generation matters before using live capture.

---

## Decoder — Local Transformation

- Decoder works primarily with data.

- Example input:

  ```text
  SGVsbG8=
  ```

- Decode:

  ```text
  Hello
  ```  

- Conceptually:

  ```text
  Data

  ↓
  
  Decoder
  
  ↓

  Transformed Data
  ```

- No network request is inherently required.

---

## Decoder Does Not "Test" the Target Automatically

- If you decode:

  ```text
  eyJ1c2VyIjoiYWxpY2UifQ==
  ```

- Burp does not automatically send the decoded value anywhere.

- You must deliberately use the result in another workflow if needed.

- Therefore:

  ```text
  Decode

  ≠

  Send
  ```

---

## Comparer — Local Difference Analysis

- Comparer helps identify differences between pieces of data.

- Example:

  ```text
  Response A

  vs

  Response B
  ```

- Conceptually:

  ```text
  Item A

  ↓

  Comparer
  
  ↑

  Item B
  ```

- Comparer can highlight differences without creating new target requests.

---

## Typical Comparer Workflow

  ```text
  Repeater Baseline Response

  ↓

  Send to Comparer
  ```

- Then:

  ```text
  Repeater Modified Response

  ↓

  Send to Comparer
  ```

- Compare:

  ```text
  Baseline

  vs

  Modified
  ```  

- The network activity occurred earlier in Repeater.

- Comparer performs the comparison locally.

---

## Logger — Broader Burp Visibility

- Logger can provide visibility into HTTP traffic generated by Burp tools and extensions.

- This is useful because:

  ```text
  HTTP History

  focuses heavily on proxy traffic
  ```

- while:

  ```text
  Logger

  can help reveal broader Burp-generated activity
  ```

depending on the Burp version and configuration.

---

## Why Logger Matters

- Suppose you observe unexpected requests.

- Potential sources:

  ```text
  Proxy

  Repeater

  Intruder

  Scanner

  Extension
  ```

- Logger can help answer:

  ```text
  Which Burp component generated this traffic?
  ```

- This makes it valuable for troubleshooting complex setups.

---

## Proxy History vs Logger

- A useful mental distinction:

  ```text
  Proxy HTTP History

  =
  
  Traffic handled through the Proxy workflow
  ```

- while:

  ```text
  Logger

  =

  Broader visibility into Burp tool and extension traffic
  ```

- Do not assume they always contain identical records.

---

## Collaborator — Out-of-Band Interaction Workflow

- Collaborator is different from ordinary request-response analysis.

- A test may insert a unique Collaborator address into a request.

- Conceptually:

  ```text
  Test Request

  ↓

  Target Application

  ↓  

  Later Server-Side Interaction
  
  ↓
  
  Collaborator
  ```

- The interaction may occur outside the original HTTP response cycle.

---

## Collaborator Does Not Mean the Original Request "Stayed Open"

- Example:

  ```text
  Request Sent

  ↓

  Response Received

  ↓

  Several Seconds Later

  ↓

  Target Performs DNS / HTTP Interaction

  ↓  

  Collaborator Records It
  ```

- The original request-response lifecycle may already be complete.

- This is why Collaborator is useful for out-of-band behavior.

---

## Collaborator Can Be Used by Multiple Tools

- Depending on workflow and Burp functionality, Collaborator payloads may appear in testing performed through:

  ```text
  Manual Repeater Testing

  Scanner

  Extensions
  
  Custom Workflows
  ```

- The Collaborator service records interactions, but you still need to determine which test request caused them.

---

## Correlation Matters

- Suppose you receive:

  ```text
  DNS Interaction
  ```

- You need to correlate:

  ```text
  Unique Payload

  ↓

  Specific Test Request

  ↓
  
  Target Behavior

  ↓
  
  Observed Interaction
  ```

- Without correlation, an interaction is weaker evidence.

---

## Extensions — The Flexible Layer

- Extensions can interact with Burp in many ways.

- Depending on the extension, they may:

  ```text
  Observe Traffic

  Analyze Requests
  
  Analyze Responses

  Modify Messages

  Add UI Features

  Generate New Requests

  Integrate External Tools
  ```

- Therefore:

  ```text
  Extension Installed

  ↓

  Potentially Changes Burp Behavior
  ```

---

## Extensions Can Observe Existing Traffic

- Conceptually:

  ```text
  Request Passes Through Burp

  ↓

  Extension Receives Message

  ↓

  Analyzes It
  ```  

- This may be passive.

---

## Extensions Can Modify Traffic

- Conceptually:

  ```text
  Original Request

  ↓
  
  Extension Changes Header

  ↓

  Modified Request

  ↓

  Target
  ```

- This can happen even if you did not manually edit the message.

- When troubleshooting unexpected behavior, inspect enabled extensions.

---

## Extensions Can Generate New Traffic

- An extension may:

  ```text
  Take Observed Endpoint

  ↓

  Generate Additional Request
  
  ↓

  Analyze Response
  ```

- Therefore:

  ```text
  Request Seen by Server

  ≠

  Necessarily Browser-Generated
  ```

- This principle applies throughout Burp.

---

## BApp Store Extensions Require Trust

- Extensions run within your security testing environment and may access sensitive traffic.

- Burp may contain:

  ```text
  Credentials

  Session Tokens

  Personal Data

  API Keys

  Internal URLs
  ```

- Therefore evaluate extensions carefully before installation and use.

---

## Tool Integration Through Context Menus

- Burp commonly lets you move data between tools through actions such as:

  ```text
  Send to Repeater

  Send to Intruder

  Send to Sequencer

  Send to Comparer
  
  Send to Decoder
  ```  
  
- The phrase:

  ```text
  Send to
  ```

- often means:

  ```text
  Create a Copy in Another Tool
  ```
  
- not:

  ```text
  Move the Only Existing Request
  ```

- The original generally remains available in its original context.

---

## Copy vs Move

- Think:

  ```text
  Proxy Request

  ├── Original remains in history
  │
  └── Copy → Repeater
  ```

- Then:

  ```text
  Repeater Copy

  ↓

  Edited Independently
  ```

- This is why you can maintain multiple testing variations.

---

## One Request Can Branch Into Many Workflows

- Example:

  ```text
  POST /login

  ↓

  Proxy History

  ├── Repeater
  ├── Intruder
  ├── Scanner
  ├── Comparer
  └── Extension Analysis
  ```

- Each branch may evolve independently.

- This is powerful, but it creates state-management challenges.

---

## State Divergence

- Suppose original request:

  ```http
  Cookie: session=A
  CSRF: token1
  ```

- You copy it to:

  ```text
  Repeater

  Intruder
  ```

- Later:

  ```text
  Browser Session → B

  CSRF → token2
  ```

- Now:

  ```text
  Browser

  session=B
  token2
  ```

- while:

  ```text
  Repeater

  session=A
  token1
  ```

- and:

  ```text
  Intruder

  session=A
  token1
  ```

- This is state divergence.

---

## Why Old Requests Fail

- Possible causes:

  ```text
  Expired Session

  Rotated CSRF Token

  Changed Authorization Token

  Timestamp Expired

  Nonce Reused

  Signature Invalid

  Workflow State Changed
  ```

- Do not assume:

  ```text
  It worked in browser earlier

  therefore

  the copied request must still work now.
  ```

---

## Updating Tool State

- Before testing an old request, compare it with current browser traffic.

- Check:

  ```text
  Cookie

  Authorization

  CSRF Token

  Request Body

  Timestamp

  Nonce

  Signature

  Relevant Headers
  ```  

- Update only what is required while preserving the variable you are testing.

---

## Cookie Jar and Session Handling

- Different Burp tools and configurations may have mechanisms for session handling, cookie management, or automatic updates.

- However, do not build the beginner mental model:

  ```text
  Every Burp tab always shares one perfectly synchronized browser session.
  ```

- That is unreliable.

- Instead:

  ```text
  Inspect the actual request being sent.
  ```

- The request itself is the evidence.

---

## Macros and Session Handling

- Advanced Burp workflows can automate session-related behavior.

- Conceptually:

  ```text
  Request About to Be Sent

  ↓

  Session Handling Logic

  ↓

  Login / Token Refresh / Macro

  ↓

  Request Updated

  ↓

  Target
  ```

- This can help maintain valid sessions during automated testing.

- But it also means the final request may differ from the originally captured request.

---

## The "Actual Request Sent" Principle

- When investigating unexpected behavior, ask:

  ```text
  What exact request was actually sent?
  ```

- not merely:

  ```text
  What request did I originally capture?
  ```

- Possible transformations may occur through:

  ```text
  Manual Editing

  Session Handling
  
  Match and Replace

  Extensions

  Tool Payload Processing

  Protocol Normalization
  ```

- Always inspect the final behavior.

---

## Request Origin Classification

- When you see a request, classify its origin.

- Possible origins:

  ```text
  Browser / Client

  Repeater

  Intruder

  Scanner

  Sequencer

  Extension
  
  Other Burp Functionality
  ```

- This helps explain why the request exists.

---

## Example — Unexpected Login Attempts

- Suppose server logs show:

  ```text
  POST /login

  POST /login
  
  POST /login

  POST /login
  ```

- Possible explanations:

  ```text
  User clicked repeatedly

  Browser retried
  
  Intruder attack running

  Scanner testing
  
  Extension generated requests
  ```

- Do not infer origin from endpoint alone.

---

## Example — Duplicate Purchase

- You captured:

  ```text
  POST /purchase
  ```

and sent it to Repeater.

- Then clicked Send three times.

- Conceptually:

  ```text
  Original Browser Purchase

  +

  Repeater Send 1

  +

  Repeater Send 2

  +

  Repeater Send 3
  ```  

- could equal:

  ```text
  Four Server-Side Purchase Attempts
  ```

depending on application safeguards.

- This is why state-changing requests require care.

---

## Example — Scanner Changes Application State

- An active test against a state-changing endpoint may create unexpected effects if the endpoint is not safe to replay.

- Before active testing, understand:

  ```text
  What does this request do?

  Can it create data?
  
  Can it delete data?

  Can it send email?

  Can it charge money?

  Can it lock accounts?
  ```  

- Automation amplifies consequences.

---

## Tool Safety Classification

- Before using a Burp tool, ask:

  ```text
  Does this tool only analyze local data?

  Does it observe existing traffic?

  Does it generate new requests?

  How many requests?
  
  Can those requests change state?
  ```

- This is a professional habit.

---

## Simplified Tool Matrix

| Tool                 |  Observe Existing Traffic |                    Receive Copies |                     Generate Requests | Local Analysis |
| -------------------- | ------------------------: | --------------------------------: | ------------------------------------: | -------------: |
| Proxy / HTTP History |                       Yes |                                 — |               Normal proxy forwarding |            Yes |
| Target               |                       Yes |                                 — |              Some active features may |            Yes |
| Repeater             |                         — |                               Yes |                                   Yes |            Yes |
| Intruder             |                         — |                               Yes |                 Yes, potentially many |            Yes |
| Scanner              |                       Yes | Uses observed/configured requests |               Yes during active audit |            Yes |
| Sequencer            |         Uses token source |                               Yes | Can generate many during live capture |            Yes |
| Decoder              |                         — |                               Yes |            No inherent target traffic |            Yes |
| Comparer             |                         — |                               Yes |            No inherent target traffic |            Yes |
| Logger               |                       Yes |                                 — |        No inherent request generation |            Yes |
| Collaborator         | Records OAST interactions |           Payloads used elsewhere |  Interactions depend on test workflow |            Yes |
| Extensions           |                   Depends |                           Depends |                               Depends |        Depends |

- This is a conceptual model; exact behavior depends on configuration and Burp version.

---

## Tool-to-Tool Workflow Example

- A professional investigation might look like:

  ```text
  1. Browse Application

  ↓

  2. Proxy Records Traffic

  ↓

  3. Target Organizes Endpoints
  
  ↓
  
  4. Identify Interesting API Request

  ↓

  5. Send Copy to Repeater

  ↓
  
  6. Establish Baseline
  
  ↓
  
  7. Modify One Parameter
  
  ↓
  
  8. Send New Request

  ↓
  
  9. Compare Responses

  ↓
  
  10. Use Comparer if Difference Is Subtle

  ↓

  11. Document Evidence
  ```

- Each step has a specific purpose.

---

## Extended Workflow Example

- Suppose a response contains a token:

  ```text
  eyJ...
  ```

- Possible workflow:

  ```text
  Proxy

  ↓
  
  Send Request to Repeater

  ↓

  Test Baseline

  ↓  

  Copy Token to Decoder

  ↓
  
  Inspect Structure

  ↓

  Return Modified Value to Repeater

  ↓

  Send New Request
  
  ↓
  
  Compare Response
  ```

- Decoder itself did not send the request.

- Repeater did.

---

## Another Workflow — Intruder

```text
Proxy Captures Search Request

↓

Send Copy to Intruder

↓

Mark One Parameter

↓

Provide Small Authorized Payload Set

↓

Intruder Generates Requests

↓

Review Response Differences

↓

Send Interesting Result to Repeater

↓

Manually Validate
```

- Automation discovers patterns.

- Manual validation establishes meaning.

---

## Another Workflow — Scanner

```text
Application Traffic

↓

Passive Analysis

↓

Potential Issue Identified

↓

Authorized Active Audit

↓

Scanner Generates Test Requests

↓

Issue Evidence Produced

↓

Manual Verification in Repeater
```

- Do not treat automated findings as unquestionable truth.

---

## Another Workflow — Collaborator

```text
Request Copied to Repeater

↓

Unique Collaborator Payload Inserted

↓

Request Sent

↓

Application Processes Input

↓

Server Makes External Interaction

↓

Collaborator Records Interaction

↓

Tester Correlates Interaction

↓

Finding Manually Validated
```

- The tools cooperate, but each performs a distinct role.

---

## Why Manual Validation Matters

- Automated tools can produce:

  ```text
  False Positives

  Ambiguous Behavior

  Environmental Noise

  Unexpected Side Effects
  ```  

- A professional workflow is:

  ```text
  Automation

  ↓

  Evidence
  
  ↓
  
  Manual Validation
  
  ↓

  Impact Analysis
  
  ↓
  
  Report
  ```  

- not:

  ```text
  Tool Says Vulnerable

  ↓
  
  Immediately Report
  ```

---

## Data Can Move Without Network Traffic

- Suppose:

  ```text
  Response

  ↓
  
  Send to Comparer
  ```  

- or:

  ```text
  Token

  ↓

  Decoder
  ```  

- This is internal data movement.

- No new target interaction is inherently created.

- This distinction is useful when working under strict rate limits.

---

## Network Traffic Can Occur Without Browser Activity

- Conversely:

  ```text
  Browser Idle
  ```

- does not imply:

  ```text
  No Burp Traffic
  ```

- because:

  ```text
  Intruder may be running

  Scanner may be auditing

  Sequencer may be collecting tokens

  Extension may be making requests
  ```

- Always know which Burp activities are active.

---

## Stopping Browser Activity Is Not Enough

- If you want to stop active testing:

  ```text
  Close Browser Tab
  ```

- may not stop:

  ```text
  Intruder Attack

  Scanner Audit
  
  Extension Task
  
  Sequencer Capture
  ```

- These tools may operate independently.

- Check Burp itself.

---

## Burp Project State

- A Burp project may preserve significant testing context, including information such as:

  ```text
  Target Knowledge

  Traffic History
  
  Tool Tabs

  Issues

  Configuration

  Captured Sensitive Data
  ```

depending on project type and configuration.

- Treat project files as sensitive.

---

## Sensitive Data Propagation

- A single captured request containing:

  ```text
  Authorization: Bearer SECRET
  ```

- may be copied into:

  ```text
  Proxy History

  Repeater
  
  Intruder
  
  Logger
  
  Project Data
  ```

depending on your workflow.

- Sensitive information can therefore propagate across the project.

---

## Repository Safety

- Never casually copy raw Burp traffic into a public GitHub repository.

- Before publishing examples, remove or replace:
  
  ```text
  Real Session Cookies

  Authorization Tokens

  API Keys

  Personal Information

  Private Hostnames

  Customer Data

  Internal IPs

  Secrets
  ```  

- Use clearly fictional examples.

---

## Common Mistakes

- Avoid:

```text
Send to Repeater moves the original request out of Proxy.
```

- Incorrect.

- It conceptually creates a separate copy.

---

```text
Editing Repeater changes the browser's original request.
```

- Incorrect.

- Repeater is independent.

---

```text
Intruder just analyzes one request locally.
```

- Incorrect.

- It can generate many new requests.

---

```text
Decoder sends decoded values to the target automatically.
```

- Incorrect.

- Decoder is primarily local transformation.

---

```text
Comparer generates requests to compare responses.
```

- Incorrect.

- It compares supplied data locally.

---

```text
Every request visible in Burp came from the browser.
```

- Incorrect.

- Burp tools and extensions may generate traffic.

---

```text
HTTP history shows all traffic generated by every Burp tool in exactly the same way.
```

- Incorrect.

- Use the appropriate traffic views, including Logger when broader visibility is needed.

---

```text
All Burp tools automatically share the browser's latest session state.
```

- Incorrect.

- Copied requests can become stale.

---

```text
Closing the browser stops all Burp-generated traffic.
```

- Incorrect.

- Active tools may continue independently.

---

```text
A Scanner finding requires no manual verification.
```

- Incorrect.

- Automated evidence should be validated and interpreted.

---

```text
Collaborator interaction automatically proves maximum impact.
```

- Incorrect.

- It proves an interaction occurred; context determines vulnerability and impact.

---

## Professional Mental Model

- Memorize:

  ```text
  Live Client Traffic

  ↓

  Proxy

  ↓

  Observe / Record

  ↓

  Target + History + Logger
  
  ↓

    Select Interesting Data
  
  ├── Copy to Repeater
  ├── Copy to Intruder
  ├── Copy to Sequencer
  ├── Copy to Decoder
  ├── Copy to Comparer
  └── Analyze with Other Tools
  ```

- Then:

  ```text
  Repeater / Intruder / Scanner / Sequencer / Extensions

  ↓

  May Generate New Traffic
  ```

- while:

  ```text
  Decoder / Comparer

  ↓

  Primarily Local Data Processing
  ```  
  
- The core distinction:

  ```text
  Observe

  ≠
  
  Copy

  ≠

  Generate

  ≠
  
  Analyze Locally
  ```

---

## Practical Exercise

- Use only an authorized practice target.

### Exercise 1 — Proxy to Repeater

1. Browse a harmless page.
2. Find a request in HTTP history.
3. Send it to Repeater.
4. Change one harmless header.
5. Send it from Repeater.
6. Confirm the original history entry remains unchanged.

- Conclusion:

  ```text
  Repeater receives an independent copy.
  ```

---

### Exercise 2 — Repeated Sends

- Use a harmless GET request.

- Send it three times from Repeater.

- Observe:

  ```text
  One Captured Request

  ↓

  Three New Repeater Requests
  ```

- Confirm that each produces a separate response.

---

### Exercise 3 — Decoder

- Copy a harmless Base64 value such as:

  ```text
  SGVsbG8=
  ```

- Decode it.

- Confirm:

  ```text  
  No target request is required.
  ```

---

### Exercise 4 — Comparer

- Take two harmless responses with small differences.

- Send them to Comparer.

- Identify:

  ```text
  Changed Words

  Changed Bytes
  ```

- Confirm that comparison itself does not require another target request.

---

### Exercise 5 — Request Origin

- Generate:

  ```text
  One Browser Request

  One Repeater Request
  ```

- Use Burp's traffic views to distinguish their origins.

- Ask:

  ```text
  Which tool generated each request?
  ```

---

## Tool Integration Checklist

```text
[ ] I understand how traffic enters Burp through Proxy.

[ ] I know HTTP history can record automatically forwarded traffic.

[ ] I understand Target organizes application knowledge.

[ ] I distinguish passive observation from active request generation.

[ ] I understand Send to Repeater creates an independent copy.

[ ] I know each Repeater Send creates a new request.

[ ] I understand Intruder generates requests from a base request and payloads.

[ ] I understand active Scanner testing generates additional traffic.

[ ] I understand Sequencer may generate many requests during live token capture.

[ ] I know Decoder primarily performs local transformations.

[ ] I know Comparer performs local comparison.

[ ] I understand Logger can provide broader Burp traffic visibility.

[ ] I understand Collaborator records out-of-band interactions.

[ ] I understand extensions may observe, modify, or generate traffic.

[ ] I know copied requests can contain stale sessions or tokens.

[ ] I inspect the actual request being sent rather than relying only on the original capture.

[ ] I can identify whether a tool observes, copies, generates, or locally analyzes data.

[ ] I understand browser inactivity does not guarantee Burp inactivity.

[ ] I use caution with state-changing requests and automated tools.

[ ] I protect sensitive data stored across Burp tools and project files.
```

---

## Key Takeaways

* Burp is an integrated platform, but its tools do not all interact with traffic in the same way.
* The most important distinction is between observing existing traffic, copying data, generating new requests, and performing local analysis.
* Proxy is a primary gateway for browser traffic, while HTTP history records traffic for later investigation.
* Target organizes knowledge about discovered application structure and scope.
* Sending a request to Repeater creates an independent testing copy; it does not modify the original browser request.
* Every Repeater send creates a new server interaction.
* Intruder generates potentially many requests from a base request and payload configuration.
* Active Scanner auditing generates additional target traffic, while passive analysis is conceptually different.
* Sequencer may generate many requests when collecting token samples.
* Decoder and Comparer primarily process data locally and do not inherently need to contact the target.
* Logger is valuable for understanding broader traffic generated by Burp tools and extensions.
* Collaborator observes out-of-band interactions that may occur after the original request-response lifecycle.
* Extensions can observe, modify, and generate traffic, so they can materially change Burp behavior.
* Copied requests can become stale as cookies, tokens, signatures, and application state change.
* Browser inactivity does not mean Burp is inactive; automated tools may continue generating requests.
* Before interpreting a request, determine which client or Burp tool generated it.
* Before using automation, understand how many requests it may generate and whether those requests can change application state.
* Burp project data can contain sensitive information copied across multiple tools and should be protected accordingly.
