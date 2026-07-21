# Advanced Web Attacks Challenge

> **Module 18 — Capstone, Interview & Quick Reference**

> **Progress**

```text
██████████░░░░░ 10 / 15 Files
```

---

## Overview

* Advanced web application testing focuses on vulnerabilities that emerge from interactions between:

  * Browsers.
  * Reverse proxies.
  * CDNs.
  * Load balancers.
  * Web servers.
  * Application frameworks.
  * Caches.
  * Backend services.
  * URL parsers.
  * Authentication systems.
  * Cross-origin security controls.

* These vulnerabilities are often difficult to identify because each component may behave correctly in isolation while interpreting the same request differently.

* Important advanced attack classes include:

  * Server-Side Request Forgery.
  * HTTP Request Smuggling.
  * Web Cache Poisoning.
  * Web Cache Deception.
  * CORS Misconfiguration.
  * Host Header Attacks.
  * Open Redirect.
  * CRLF and Response Splitting.
  * Prototype Pollution.
  * WebSocket Security Issues.
  * OAuth and SSO Logic Flaws.
  * Parser and normalization discrepancies.

* The core testing mindset is:

```text
Understand Architecture

↓

Identify Trust Boundaries

↓

Identify Parsing Boundaries

↓

Form a Specific Hypothesis

↓

Use Minimal Controlled Tests

↓

Compare Behavior

↓

Validate Security Impact
```

---

## Objectives

* By completing this challenge, you should be able to:

  * Map advanced web attack surfaces.
  * Identify server-side URL-fetching functionality.
  * Test SSRF safely.
  * Analyze URL parser behavior.
  * Identify HTTP desynchronization candidates.
  * Understand request-smuggling methodology.
  * Analyze cache behavior.
  * Identify cache keys.
  * Test cache poisoning safely.
  * Distinguish cache poisoning from cache deception.
  * Analyze CORS configurations.
  * Test Host header handling.
  * Identify unsafe redirect behavior.
  * Analyze CRLF and response-header manipulation.
  * Identify prototype-pollution candidates.
  * Test WebSocket authorization.
  * Analyze OAuth and SSO flows.
  * Identify parser discrepancies.
  * Validate advanced findings conservatively.
  * Avoid unsafe infrastructure-level testing.

---

# Challenge Scenario

* You are assessing an authorized modern web application using:

```text
Browser

↓

CDN / Edge

↓

Reverse Proxy

↓

Web Server

↓

Application

↓

Internal Services
```

* The application also exposes:

```text
URL Import

Webhooks

Image Fetching

Redirects

Caching

WebSockets

OAuth Login

API Integrations
```

* Your objective is to identify weaknesses created by trust, parsing, caching, routing, and protocol boundaries.

---

# Challenge Deliverables

* Produce:

```text
Advanced-Attack-Surface.md

Trust-Boundary-Map.md

SSRF-Test-Matrix.md

Cache-Behavior-Matrix.md

CORS-Matrix.md

Host-Header-Notes.md

WebSocket-Matrix.md

OAuth-Flow-Map.md

Validated-Findings/

Evidence/

Advanced-Web-Attacks-Summary.md
```

---

# Phase 1 — Map the Architecture

* Begin by identifying visible infrastructure components.

---

## Look For

```text
CDN

Reverse Proxy

Load Balancer

WAF

Web Server

Application Framework

Object Storage

Internal APIs

Authentication Provider

Cache Layer
```

---

## Evidence Sources

```text
Response Headers

DNS

Cookies

TLS

Error Pages

Redirects

JavaScript

Application Behavior

Technology Fingerprinting
```

---

# Phase 2 — Build the Trust-Boundary Map

* Model how requests and data move between components.

```text
User

↓

Edge / CDN

↓

Reverse Proxy

↓

Application

↓

Internal Service

↓

External Service
```

* Ask:

```text
Which Component Trusts Which Input?

Which Headers Are Rewritten?

Which URLs Are Parsed More Than Once?

Which Responses Are Cached?

Which Requests Reach Internal Systems?
```

---

# Phase 3 — Identify SSRF Candidates

* SSRF may occur when the server makes requests based on attacker-controlled input.

---

## Candidate Features

```text
Import From URL

Image Fetching

Avatar From URL

Webhook Testing

URL Preview

PDF Generation

Screenshot Service

Feed Import

Link Metadata

External API Integration
```

---

## Candidate Parameters

```text
url

uri

link

target

callback

webhook

image

avatar

feed

endpoint

redirect
```

---

# Phase 4 — Establish SSRF Baseline

* Use a controlled external endpoint where explicitly permitted.

```text
Application

↓

User-Supplied URL

↓

Controlled Server
```

* Record:

```text
Request Time

Source Behavior

HTTP Method

Headers

Redirect Handling

DNS Behavior
```

---

# Core SSRF Question

```text
Can User-Controlled Input

Cause the Server

to Make an Unintended Network Request?
```

---

# Phase 5 — Test SSRF Validation

* Determine whether the application restricts:

```text
Scheme

Hostname

Port

IP Range

Redirect Destination

DNS Resolution

URL Syntax
```

---

## Compare

```text
Expected External URL

Invalid URL

Alternate Scheme

Alternate Port

Redirected URL

Normalized URL
```

* Use only destinations permitted by the assessment scope.

---

# Phase 6 — Analyze URL Parsing

* Different components may interpret URLs differently.

---

## URL Components

```text
scheme://userinfo@host:port/path?query#fragment
```

* Ask:

```text
Which Component Parses the URL?

Is Validation Performed Before Normalization?

Are Redirects Revalidated?

Is DNS Rechecked?

Are Alternate Representations Normalized?
```

---

# Phase 7 — Test Redirect Handling in SSRF

```text
Application

↓

Allowed Controlled URL

↓

Redirect

↓

Second Destination
```

* Determine whether destination validation occurs:

```text
Only Before First Request

or

After Every Redirect
```

* Keep all test destinations controlled and authorized.

---

# Phase 8 — Validate SSRF Conservatively

* Strong evidence may include:

```text
Server-Side Request Reaches Controlled Endpoint

+

Request Triggered by Attacker-Controlled Input

+

Security Boundary Bypassed

+

Meaningful Reachability or Data Impact
```

* Do not probe cloud metadata services, localhost services, private networks, or unrelated internal systems unless the rules of engagement explicitly permit it.

---

# Phase 9 — Identify HTTP Desynchronization Surface

* HTTP request smuggling can arise when multiple HTTP-processing components disagree about request boundaries.

```text
Client

↓

Front End

↓

Back End
```

* The central problem is:

```text
Front End Parses Request One Way

≠

Back End Parses Request the Same Way
```

---

# Phase 10 — Understand Framing Signals

* HTTP/1.1 request framing may involve:

```text
Content-Length

Transfer-Encoding
```

* Modern architectures may also translate between:

```text
HTTP/2

↓

HTTP/1.1
```

* Security testing focuses on whether components interpret request boundaries consistently.

---

# Phase 11 — Request-Smuggling Safety Rule

* Desynchronization testing can affect other users if performed carelessly.

* Only test when:

  * Explicitly authorized.
  * Using a safe lab or isolated environment where possible.
  * Using low request volume.
  * Using harmless markers.
  * Avoiding payloads designed to capture another user's traffic.

```text
Prove Parsing Discrepancy

Not

Harm Other Sessions
```

---

# Phase 12 — Identify Smuggling Candidates

* Indicators include:

```text
Reverse Proxy

CDN

Load Balancer

Multiple HTTP Layers

HTTP/2 Downgrading

Unusual Transfer-Encoding Behavior

Inconsistent Timeout Behavior
```

* A scanner signal alone is not confirmation.

---

# Phase 13 — Controlled Desynchronization Analysis

* Establish:

```text
Normal Request

↓

Expected Response
```

* Then investigate only minimal framing anomalies permitted by scope.

* Observe:

```text
Timeout Changes

Connection Behavior

Unexpected Response Ordering

Front-End / Back-End Error Differences
```

* Do not target unrelated users.

---

# Phase 14 — Identify Cacheable Content

* Determine which responses may be cached.

---

## Look For

```text
Cache-Control

Age

ETag

Expires

X-Cache

CF-Cache-Status

CDN Headers
```

---

## Candidate Content

```text
Static Assets

Public Pages

Search Results

Product Pages

Redirects

Error Pages

Generated Content
```

---

# Phase 15 — Determine the Cache Key

* A cache key may include:

```text
Host

Path

Query String

Selected Headers

Cookies
```

* Some request components may affect the response without being included in the cache key.

---

# Cache-Poisoning Model

```text
Unkeyed Input

↓

Changes Response

↓

Response Cached

↓

Other Users Receive Modified Response
```

---

# Phase 16 — Test Cache Behavior

* Use unique harmless markers.

---

## Workflow

```text
Baseline Request

↓

Record Cache Status

↓

Modify One Input

↓

Request Again

↓

Observe Cache

↓

Remove Input

↓

Request Original URL

↓

Check Persistence
```

---

# Phase 17 — Identify Unkeyed Inputs

* Candidate inputs include:

```text
Headers

Query Parameters

Host-Related Headers

Forwarding Headers

Protocol Indicators
```

* The important question is:

```text
Can an Input Change the Response

Without Separating the Cache Entry?
```

---

# Phase 18 — Validate Cache Poisoning Safely

* Prefer:

```text
Unique Test URL

Harmless Marker

Short-Lived Cache Entry

Controlled Scope
```

* Avoid poisoning shared high-traffic pages.

---

# Phase 19 — Understand Web Cache Deception

* Cache deception differs from cache poisoning.

```text
Cache Poisoning:

Attacker Influences Cached Response

↓

Victims Receive It
```

```text
Cache Deception:

Victim's Sensitive Response

↓

Incorrectly Cached

↓

Attacker Retrieves It
```

---

# Phase 20 — Test Cache Deception Safely

* Use only your own controlled account.

* Determine whether authenticated content can be cached under URL patterns that appear static.

* Verify:

```text
Authenticated Response

↓

Unexpectedly Cached

↓

Accessible Without Equivalent Authorization?
```

* Never test using another user's private data.

---

# Phase 21 — Analyze CORS

* CORS controls whether browser JavaScript from another origin may read responses.

---

## Important Headers

```text
Access-Control-Allow-Origin

Access-Control-Allow-Credentials

Access-Control-Allow-Methods

Access-Control-Allow-Headers
```

---

# Phase 22 — Build the CORS Matrix

| Origin            | Credentials       | ACAO     | ACAC     | Sensitive Read? |
| ----------------- | ----------------- | -------- | -------- | --------------- |
| Trusted origin    | Yes               | Expected | Expected | Expected        |
| Controlled origin | Yes               | Test     | Test     | Test            |
| `null`            | Context dependent | Test     | Test     | Test            |
| Unrelated origin  | Yes               | Test     | Test     | Test            |

---

# Phase 23 — Validate CORS Impact

* A suspicious header combination is not enough.

* Confirm:

```text
Attacker-Controlled Origin

↓

Victim Browser Sends Authentication

↓

Sensitive Response Returned

↓

Browser Allows Attacker Origin to Read It
```

---

# Core CORS Rule

```text
Permissive Header

≠

Exploitable CORS
```

---

# Phase 24 — Identify Host Header Attack Surface

* Applications may use host-related headers for:

```text
Password Reset Links

Absolute URLs

Redirects

Email Links

Routing

Cache Keys

Tenant Selection
```

---

## Relevant Inputs

```text
Host

X-Forwarded-Host

Forwarded

X-Host

Other Proxy-Specific Headers
```

---

# Phase 25 — Test Host Handling

* Use a harmless controlled hostname where permitted.

* Observe:

```text
Response Links

Redirects

Generated URLs

Emails

Cache Behavior

Routing
```

---

# Host Header Impact Model

```text
Attacker-Controlled Host Value

↓

Trusted by Application

↓

Security-Sensitive URL or Routing Decision

↓

Victim Impact
```

---

# Phase 26 — Test Password-Reset URL Generation

* Where authorized, request a password reset for your own controlled account.

* Inspect whether generated links derive their origin from:

```text
Trusted Configuration

or

Request Headers
```

* Do not target real users.

---

# Phase 27 — Test Open Redirects

* Candidate parameters:

```text
next

url

redirect

return

returnTo

continue

callback
```

---

## Workflow

```text
Known Internal Destination

↓

Controlled External Destination

↓

Observe Validation

↓

Test Normalization

↓

Determine Security Impact
```

---

# Open Redirect Impact

* Impact may increase when combined with:

```text
OAuth

SSO

Trusted Links

Phishing

Token Leakage

Security Filters
```

* An external redirect alone should be reported according to actual program severity and impact.

---

# Phase 28 — Analyze CRLF and Header Injection

* User-controlled values may enter:

```text
Location

Content-Disposition

Set-Cookie

Custom Headers
```

---

## Core Question

```text
Can Input Alter HTTP Header Structure

Instead of Remaining Data?
```

* Inspect raw responses.

* Use harmless header markers only.

---

# Phase 29 — Identify Prototype Pollution Candidates

* Prototype pollution is relevant primarily in JavaScript ecosystems when attacker-controlled properties can modify object prototypes.

---

## Candidate Inputs

```text
JSON Objects

Nested Parameters

Configuration Merge

Query Parsing

Object Assignment

Recursive Merge Functions
```

---

## Security Model

```text
Attacker-Controlled Property

↓

Unsafe Object Merge

↓

Prototype Modified

↓

Application Behavior Changes
```

---

# Phase 30 — Test Prototype Pollution Safely

* Use harmless marker properties in controlled environments.

* Determine whether:

```text
Unexpected Property Appears

Across Otherwise Unrelated Objects
```

* Then determine whether any security-sensitive gadget or behavior exists.

---

# Important

```text
Prototype Pollution Primitive

≠

Automatically High Impact
```

* Impact depends on how polluted properties are consumed.

---

# Phase 31 — Map WebSocket Surface

* Identify connections such as:

```text
ws://

wss://
```

* Record:

```text
Handshake Endpoint

Cookies

Tokens

Origin

Message Format

Actions

Object IDs
```

---

# Phase 32 — Test WebSocket Authentication

* Compare:

```text
Authenticated Connection

No Authentication

Expired Authentication

Logged-Out Session
```

* Determine whether authentication is checked:

```text
At Handshake Only

or

Throughout Session Lifecycle
```

---

# Phase 33 — Test WebSocket Authorization

* Use controlled accounts.

---

## Test

```text
User A Subscribes to Object A

User B Attempts Object A

User B Sends Action Against Object A
```

* Test:

```text
Read

Subscribe

Send

Modify

Delete
```

* as applicable.

---

# Phase 34 — Test WebSocket Origin Controls

* Inspect the handshake `Origin`.

* Determine whether cross-site WebSocket connections are accepted.

* Impact depends on:

```text
Cookie-Based Authentication

+

Weak Origin Validation

+

Sensitive WebSocket Actions
```

---

# Phase 35 — Test WebSocket Message Validation

* Messages may contain:

```text
action

object_id

user_id

channel

payload
```

* Treat them like API requests.

* Test:

```text
Authorization

Input Types

Unexpected Properties

State Transitions

Replay
```

---

# Phase 36 — Map OAuth / SSO Flow

* Document:

```text
Client

Authorization Endpoint

Identity Provider

Redirect URI

Callback

Token Exchange

Application Session
```

---

## Typical Flow

```text
User

↓

Application

↓

Identity Provider

↓

Authorization

↓

Callback

↓

Application Session
```

---

# Phase 37 — Record OAuth Parameters

* Common parameters include:

```text
client_id

redirect_uri

response_type

scope

state

code

code_challenge

code_verifier

nonce
```

---

# Phase 38 — Test Redirect URI Validation

* Determine whether redirect URIs are:

```text
Exact-Match

Allowlisted

Pattern-Matched

Dynamically Derived
```

* Use only controlled destinations permitted by scope.

---

# Phase 39 — Test OAuth State Handling

* `state` commonly protects authorization flows from request-forgery and flow confusion.

---

## Ask

```text
Is State Present?

Is It Unpredictable?

Is It Bound to the Session?

Is It Validated?

Can It Be Reused?
```

---

# Phase 40 — Test PKCE Where Applicable

* For public clients, review:

```text
code_challenge

code_verifier
```

* Determine whether PKCE is required and correctly validated where the architecture depends on it.

---

# Phase 41 — Test OAuth Account Linking

* Account linking can create high-impact logic issues.

---

## Ask

```text
How Is Identity Matched?

Email?

Provider Subject?

Verified Email?

Existing Session?
```

* Use only controlled identities.

---

# Phase 42 — Test OAuth Session Binding

* Determine:

```text
Which Browser Initiated the Flow?

Which Account Authorized It?

Which Application Account Receives It?
```

* Look for identity confusion using controlled accounts only.

---

# Phase 43 — Analyze Parser Discrepancies

* Different layers may parse the same input differently.

---

## Examples

```text
URL Parser A

≠

URL Parser B
```

```text
Proxy HTTP Parser

≠

Backend HTTP Parser
```

```text
Cache Key Normalization

≠

Application Routing
```

```text
WAF Decoder

≠

Application Decoder
```

---

# Core Advanced Testing Principle

```text
Same Input

+

Different Interpretation

=

Potential Security Boundary Failure
```

---

# Phase 44 — Test Normalization Differences

* Consider controlled variations involving:

```text
Encoding

Case

Path Normalization

Duplicate Parameters

Header Duplication

URL Parsing

Trailing Characters
```

* Determine whether:

```text
Security Control Sees A

↓

Application Interprets B
```

---

# Phase 45 — Build the Advanced Attack Matrix

| Surface   | Input        | Trust Boundary    | Expected Control        | Result  |
| --------- | ------------ | ----------------- | ----------------------- | ------- |
| URL fetch | `url`        | App → Network     | Destination restriction | Pending |
| Cache     | Header       | Edge → App        | Correct cache key       | Pending |
| CORS      | Origin       | Browser → API     | Trusted origins only    | Pending |
| Host      | Host header  | Proxy → App       | Trusted host            | Pending |
| WebSocket | Object ID    | User → WS backend | Object authorization    | Pending |
| OAuth     | Redirect URI | App → IdP         | Exact validation        | Pending |

---

# Phase 46 — Validate Findings

* Advanced findings often produce ambiguous signals.

* Require:

```text
Specific Security Hypothesis

↓

Controlled Baseline

↓

One-Variable Mutation

↓

Reproducible Difference

↓

Security Boundary Failure

↓

Meaningful Impact
```

---

# Example — SSRF

```text
Controlled URL Submitted

↓

Server Requests Controlled Endpoint

↓

Destination Restriction Bypassed

↓

Unauthorized Server-Side Reachability Demonstrated
```

---

# Example — Cache Poisoning

```text
Unkeyed Input

↓

Response Modified

↓

Modified Response Cached

↓

Clean Request Receives Modified Cached Response
```

---

# Example — CORS

```text
Controlled Attacker Origin

↓

Victim Credentials Included

↓

Sensitive Response Returned

↓

Browser Allows Attacker Origin to Read Response
```

---

# Phase 47 — Determine Impact

* Potential impact includes:

```text
Internal Service Reachability

Sensitive Data Exposure

Cross-User Response Manipulation

Cached Private Data Exposure

Account Compromise

Password Reset Poisoning

Cross-Origin Data Theft

Unauthorized WebSocket Actions

OAuth Account Confusion

Privilege Escalation
```

---

# Phase 48 — Identify Root Cause

* Common causes include:

```text
Unsafe URL Fetching

Weak Destination Validation

Parser Disagreement

Ambiguous HTTP Framing

Incomplete Cache Keys

Unsafe Cache Rules

Overly Permissive CORS

Trusted Host Headers

Weak Redirect Validation

Unsafe Header Construction

Unsafe Object Merging

Missing WebSocket Authorization

Weak OAuth Parameter Validation
```

---

# Phase 49 — Collect Evidence

* Preserve:

```text
Architecture Context

Baseline Request

Modified Request

Baseline Response

Modified Response

Authentication Context

Infrastructure Headers

Persistent Effect

Security Boundary

Impact
```

---

## Evidence Structure

```text
F001/
│
├── 01-context.md
├── 02-baseline-request.txt
├── 03-baseline-response.txt
├── 04-test-request.txt
├── 05-test-response.txt
├── 06-validation.txt
├── 07-impact.png
└── notes.md
```

---

# Capstone Challenge

## Stage 1 — Architecture

* Map:

```text
Browser

Edge

Proxy

Application

Backend Services

Authentication

Caching
```

---

## Stage 2 — SSRF

* Identify all server-side URL-fetching features.

* Test only with controlled destinations.

* Document validation and redirect behavior.

---

## Stage 3 — HTTP Parsing

* Identify potential multi-layer HTTP architectures.

* Document request-framing behavior.

* Perform desynchronization testing only where explicitly safe and authorized.

---

## Stage 4 — Caching

* Identify cacheable endpoints.

* Determine likely cache-key components.

* Test harmless unkeyed-input candidates.

---

## Stage 5 — CORS

* Build a CORS matrix for sensitive API endpoints.

---

## Stage 6 — Host and Redirects

* Test:

```text
Host Handling

Forwarded Host Handling

Absolute URL Generation

Redirect Parameters
```

---

## Stage 7 — WebSockets

* Test:

```text
Authentication

Authorization

Origin

Message Validation

Replay
```

---

## Stage 8 — OAuth / SSO

* Map:

```text
Redirect URI

State

PKCE

Account Linking

Session Binding
```

---

## Stage 9 — Validation

* Classify every candidate:

```text
Confirmed

Rejected

Needs More Evidence
```

---

# Challenge Success Criteria

* You should be able to answer:

```text
Which Trust Boundaries Exist?

Which Components Parse Requests?

Which Features Make Server-Side Requests?

How Are URL Destinations Validated?

Are Redirects Revalidated?

Do HTTP Layers Interpret Requests Consistently?

Which Responses Are Cached?

What Is Included in the Cache Key?

Can Unkeyed Inputs Affect Cached Responses?

Can Sensitive Responses Be Cached Incorrectly?

Which Origins Can Read Sensitive Responses?

Are Host Headers Trusted?

Are Redirect Destinations Restricted?

Are WebSockets Properly Authorized?

Are OAuth Flows Bound to the Correct User and Session?

Do Different Parsers Normalize Input Consistently?
```

---

# Advanced Web Testing Workflow

```text
Map Architecture

↓

Identify Trust Boundaries

↓

Identify Parsing Boundaries

↓

Map Advanced Features

↓

Capture Baselines

↓

Test SSRF Candidates

↓

Analyze HTTP Framing

↓

Map Cache Behavior

↓

Test CORS

↓

Test Host / Redirect Handling

↓

Review Header Construction

↓

Review Prototype Pollution

↓

Test WebSockets

↓

Map OAuth / SSO

↓

Compare Parser Behavior

↓

Validate Findings

↓

Determine Impact

↓

Collect Evidence

↓

Report
```

---

# Common Mistakes

* Testing advanced vulnerabilities without understanding architecture.
* Treating every URL parameter as SSRF.
* Using uncontrolled external infrastructure.
* Probing internal services without authorization.
* Testing cloud metadata endpoints without explicit permission.
* Assuming one successful outbound request proves high-impact SSRF.
* Performing aggressive request-smuggling tests on production.
* Using desynchronization techniques that may affect other users.
* Treating scanner findings as confirmed HTTP request smuggling.
* Assuming every response is cached.
* Failing to identify the cache key.
* Poisoning shared production pages unnecessarily.
* Confusing cache poisoning with cache deception.
* Testing cache deception using another user's data.
* Treating permissive CORS headers as automatically exploitable.
* Ignoring browser credential behavior.
* Testing only the `Host` header and ignoring proxy-derived host headers.
* Reporting open redirects without considering actual impact.
* Treating header reflection as confirmed CRLF injection.
* Treating a prototype-pollution primitive as automatically critical.
* Ignoring WebSocket authorization.
* Testing WebSocket handshake only.
* Ignoring object IDs inside WebSocket messages.
* Testing OAuth without mapping the complete flow.
* Ignoring `state`.
* Ignoring PKCE.
* Testing account linking with real users.
* Failing to distinguish parser discrepancy from exploitable impact.
* Making multiple mutations at once.
* Failing to preserve clean baselines.

---

# Professional Tips

* Draw the architecture before testing advanced protocol issues.
* Identify where trust changes between components.
* Treat server-side URL fetching as a separate attack surface.
* Use controlled callback infrastructure for SSRF validation.
* Recheck validation after redirects.
* Avoid internal-network probing unless explicitly authorized.
* Treat request smuggling as high-risk testing.
* Prefer isolated labs for desynchronization research.
* Identify cacheability before testing cache poisoning.
* Use unique URLs and harmless markers for cache tests.
* Determine which inputs are keyed and unkeyed.
* Test cache deception only with your own authenticated data.
* Validate CORS from a real browser security perspective.
* Check whether credentials are actually sent and responses readable.
* Review all host-derived inputs used by proxies and applications.
* Test password-reset link generation only with controlled accounts.
* Evaluate open redirects in the context of authentication and token flows.
* Inspect raw responses for header injection.
* Separate prototype-pollution primitives from exploitable gadgets.
* Treat WebSocket messages like API requests requiring authentication, authorization, and input validation.
* Test WebSocket object ownership with controlled users.
* Map OAuth flows before mutating parameters.
* Verify redirect URI, state, PKCE, identity mapping, and session binding.
* Look for places where different parsers interpret identical input differently.
* Use one-variable mutations whenever possible.
* Require reproducibility and concrete impact before reporting.

---

# Practice Tasks

1. Select an authorized modern web application or practice lab.

2. Map visible infrastructure layers.

3. Identify CDN and proxy indicators.

4. Draw the trust-boundary map.

5. Identify every URL-fetching feature.

6. Capture a normal URL-fetch request.

7. Use a controlled callback destination.

8. Record server-side request behavior.

9. Test URL validation boundaries safely.

10. Test redirect revalidation with controlled destinations.

11. Document SSRF candidates and results.

12. Identify multi-layer HTTP processing.

13. Review HTTP protocol versions.

14. Document request-framing behavior.

15. Perform only explicitly authorized desynchronization tests.

16. Identify cacheable endpoints.

17. Record caching headers.

18. Determine likely cache-key components.

19. Test one harmless candidate unkeyed input.

20. Verify whether modified responses persist.

21. Test cache deception only with your own account.

22. Build a cache-behavior matrix.

23. Identify sensitive CORS-enabled endpoints.

24. Test controlled origins.

25. Test credentialed behavior.

26. Build the CORS matrix.

27. Identify host-derived application behavior.

28. Test absolute URL generation.

29. Test password-reset URL generation with your own account.

30. Identify redirect parameters.

31. Test controlled redirect destinations.

32. Inspect response-header construction.

33. Review JavaScript object-merging functionality where relevant.

34. Identify WebSocket endpoints.

35. Capture WebSocket handshakes.

36. Record authentication mechanisms.

37. Test authentication after logout.

38. Test object authorization with User A and User B.

39. Test WebSocket message validation.

40. Review WebSocket Origin handling.

41. Map OAuth or SSO flows.

42. Record authorization parameters.

43. Review redirect URI validation.

44. Review `state`.

45. Review PKCE where applicable.

46. Test account linking only with controlled identities.

47. Test session binding.

48. Identify normalization boundaries.

49. Compare duplicate and encoded input behavior.

50. Build the advanced attack matrix.

51. Validate every suspicious result.

52. Classify confirmed, rejected, or unresolved candidates.

53. Determine actual impact.

54. Identify root cause.

55. Collect minimal sufficient evidence.

56. Restore any controlled state.

57. Write the final advanced-web-attacks summary.

---

# Interview Questions

1. What makes an advanced web vulnerability different from a basic input-validation flaw?
2. Why is architecture mapping important?
3. What is a trust boundary?
4. What is a parsing boundary?
5. What is SSRF?
6. Which application features commonly create SSRF attack surface?
7. How do you test SSRF safely?
8. Why is a controlled callback endpoint useful?
9. Why should redirect destinations be revalidated?
10. Why can URL parser differences create vulnerabilities?
11. What is HTTP request smuggling?
12. Why do front-end and back-end parsing differences matter?
13. What role do `Content-Length` and `Transfer-Encoding` play conceptually?
14. Why is request-smuggling testing risky?
15. How do you validate desynchronization safely?
16. What is web cache poisoning?
17. What is a cache key?
18. What is an unkeyed input?
19. How do you test cache poisoning safely?
20. What is web cache deception?
21. How does cache deception differ from cache poisoning?
22. What is CORS?
23. What makes a CORS misconfiguration exploitable?
24. Why is a permissive ACAO header alone insufficient?
25. What are Host header attacks?
26. Which application features commonly trust host information?
27. How can Host header handling affect password resets?
28. What is an open redirect?
29. When can an open redirect become more serious?
30. What is CRLF injection?
31. How do you distinguish header reflection from header injection?
32. What is prototype pollution?
33. What is the difference between a prototype-pollution primitive and a security impact?
34. How do WebSockets differ from ordinary HTTP security testing?
35. How do you test WebSocket authentication?
36. How do you test WebSocket authorization?
37. What is Cross-Site WebSocket Hijacking conceptually?
38. Why does the WebSocket Origin header matter?
39. What should be tested inside WebSocket messages?
40. How do you map an OAuth authorization flow?
41. What is the purpose of `redirect_uri`?
42. What is OAuth `state` used for?
43. What is PKCE?
44. What security issues can occur during OAuth account linking?
45. What is session binding in an OAuth flow?
46. What is a parser discrepancy?
47. How can normalization differences bypass security controls?
48. How do you validate advanced vulnerabilities without overstating impact?
49. What evidence should an advanced-web finding contain?
50. Walk me through your complete advanced web attack methodology.

---

# Quick Revision

* Core model:

```text
Architecture

↓

Trust Boundary

↓

Parsing Boundary

↓

Differential Behavior

↓

Security Impact
```

* SSRF:

```text
User-Controlled URL

↓

Server-Side Request

↓

Destination Control Failure?
```

* Request smuggling:

```text
Front-End Parsing

≠

Back-End Parsing
```

* Cache poisoning:

```text
Unkeyed Input

↓

Response Changed

↓

Cached

↓

Victim Response Changed
```

* Cache deception:

```text
Sensitive Response

↓

Incorrectly Cached

↓

Unauthorized Retrieval
```

* CORS:

```text
Attacker Origin

+

Victim Credentials

+

Readable Sensitive Response
```

* WebSockets:

```text
Handshake Authentication

+

Message Authorization

+

Object Authorization
```

* OAuth:

```text
Redirect URI

+

State

+

PKCE

+

Identity Binding

+

Session Binding
```

---

# Advanced Web Attacks Quick Checklist

```text
ARCHITECTURE

[ ] CDN
[ ] Proxy
[ ] Load balancer
[ ] WAF
[ ] Application
[ ] Internal services
[ ] Cache
[ ] Authentication provider


SSRF

[ ] URL-fetch features
[ ] Controlled callback
[ ] Scheme validation
[ ] Host validation
[ ] Port validation
[ ] Redirect validation
[ ] URL normalization
[ ] Safe scope


HTTP DESYNC

[ ] Multiple HTTP layers
[ ] Protocol versions
[ ] Request framing
[ ] Parser differences
[ ] Safe isolated testing
[ ] No cross-user impact


CACHE

[ ] Cacheable response
[ ] Cache headers
[ ] Cache key
[ ] Unkeyed inputs
[ ] Unique test URL
[ ] Harmless marker
[ ] Poisoning
[ ] Deception


CORS

[ ] Origin handling
[ ] Credentials
[ ] Sensitive endpoint
[ ] Browser readability
[ ] Controlled origin


HOST / REDIRECT

[ ] Host
[ ] Forwarded host
[ ] Absolute URLs
[ ] Password reset
[ ] Redirect parameters
[ ] Controlled destination


HEADERS

[ ] Header reflection
[ ] CRLF handling
[ ] Raw response
[ ] Harmless marker


PROTOTYPE

[ ] Object merge
[ ] Nested properties
[ ] Pollution primitive
[ ] Security-sensitive gadget


WEBSOCKETS

[ ] Handshake auth
[ ] Logout behavior
[ ] Origin
[ ] Object authorization
[ ] Message validation
[ ] Replay


OAUTH / SSO

[ ] Flow mapped
[ ] Redirect URI
[ ] State
[ ] PKCE
[ ] Account linking
[ ] Identity binding
[ ] Session binding


VALIDATION

[ ] Baseline
[ ] One-variable mutation
[ ] Reproducible
[ ] Security boundary
[ ] Controlled proof
[ ] Meaningful impact
```

---

# Key Takeaways

* Advanced web testing focuses heavily on trust boundaries, parsing boundaries, protocol behavior, and interactions between infrastructure components.
* Architecture mapping is essential because vulnerabilities may arise from disagreement between CDNs, proxies, caches, web servers, frameworks, and backend services.
* SSRF testing begins by identifying functionality that causes the server to fetch attacker-influenced URLs.
* SSRF should be validated using controlled destinations and without probing internal infrastructure unless explicitly authorized.
* URL validation must account for normalization, redirects, DNS behavior, schemes, hosts, ports, and the parser that ultimately performs the request.
* HTTP request smuggling arises from disagreement between HTTP-processing components about request boundaries.
* Desynchronization testing can affect other users and therefore requires explicit authorization, minimal testing, and preferably isolated environments.
* Scanner signals and unusual timeouts do not by themselves confirm request smuggling.
* Cache poisoning occurs when an input that is not properly included in the cache key changes a response that is then served to other requests.
* Cache deception occurs when sensitive personalized content is incorrectly cached and later retrievable outside the intended authorization context.
* Cache tests should use unique URLs, harmless markers, controlled accounts, and minimal shared impact.
* CORS vulnerabilities require more than permissive headers: an attacker-controlled origin must be able to cause authenticated requests and read sensitive responses in the browser.
* Host-related headers become security-sensitive when used for routing, absolute URL generation, password resets, redirects, tenant selection, or cache behavior.
* Open redirects should be evaluated in context, especially when combined with OAuth, SSO, trusted-domain assumptions, or token flows.
* CRLF testing requires demonstrating actual HTTP header-structure manipulation rather than simple reflection.
* Prototype pollution requires both a pollution primitive and a meaningful security-sensitive consequence.
* WebSockets require authentication, authorization, object-level access control, origin analysis, message validation, and lifecycle testing.
* WebSocket security must be tested beyond the initial handshake because authorization may fail at the individual message or object level.
* OAuth and SSO assessments should map redirect URI validation, `state`, PKCE, identity mapping, account linking, and session binding.
* Parser discrepancies occur when two security-relevant components interpret the same input differently.
* Encoding, normalization, duplicate parameters, headers, and protocol translations are common sources of interpretation differences.
* Advanced findings require conservative validation because infrastructure behavior can produce ambiguous signals and testing may have broader side effects.
* The complete methodology is **map architecture → identify trust and parsing boundaries → identify advanced features → capture baselines → test server-side URL fetching → analyze HTTP framing → map caching → test CORS and host handling → review redirects and headers → analyze prototype behavior → test WebSockets → map OAuth/SSO → investigate parser discrepancies → validate safely → determine impact → preserve evidence → report**.
