# HTTPS, TLS, and the Burp CA Certificate

> **Module 04 — How Burp Suite Works**

> **Progress**

```text
████░░░░░░░ 4 / 11 Lessons
```

- Most modern web applications use HTTPS.

- This creates an important question:

  > If HTTPS encrypts communication between a browser and a server, how can Burp Suite inspect and modify that traffic?

- The answer is not that Burp simply "breaks HTTPS."

- When Burp is intentionally configured as an intercepting proxy, it establishes **separate TLS relationships** with the client and the destination server.

- Conceptually:

  ```text
  Browser

  ↓ TLS Connection 1

  Burp Suite

  ↓ TLS Connection 2

  Target Server
  ```  

- Burp can therefore decrypt traffic arriving on one TLS connection, inspect or modify the HTTP message, and then encrypt the message again on the other connection.

- For the browser to accept this arrangement without certificate warnings, it must trust the **Burp Certificate Authority (CA)** used to generate certificates for intercepted HTTPS destinations.

- Understanding this architecture is essential for both using Burp correctly and troubleshooting HTTPS interception problems.

---

## Learning Objectives

- By the end of this lesson, you should be able to:

  * Explain the purpose of HTTPS.
  * Understand the relationship between HTTP, TLS, and HTTPS.
  * Explain why encrypted traffic cannot simply be read by an ordinary intermediary.
  * Understand the role of the HTTP `CONNECT` method.
  * Explain Burp's two-TLS-connection model.
  * Understand what a Certificate Authority does.
  * Explain how Burp's CA certificate enables HTTPS interception.
  * Understand dynamically generated certificates conceptually.
  * Explain certificate hostname validation.
  * Understand why certificate warnings occur.
  * Distinguish browser-side and server-side certificate validation.
  * Understand certificate pinning conceptually.
  * Recognize the security implications of trusting a testing CA.
  * Troubleshoot common HTTPS interception failures.

---

## HTTP vs HTTPS

- HTTP is an application-layer protocol used to exchange web messages.

- A simplified HTTP request:

  ```http
  GET /account HTTP/1.1
  Host: example.com
  ```

- Without transport encryption, the communication is conceptually:

  ```text
  Browser

  ↓

  HTTP

  ↓

  Server
  ```

- HTTPS means HTTP is communicated through TLS.

- Conceptually:

  ```text
  HTTP

  ↓
  
  TLS Protection

  ↓

  Network
  ```

- So:

  ```text
  HTTPS

  ≈

  HTTP over TLS
  ```  

- TLS provides security properties such as:

  * Confidentiality.
  * Integrity.
  * Authentication of the server identity through certificates.

---

## Normal HTTPS Communication

- Without Burp:

  ```text
  Browser

  ↓
  
  TLS Connection

  ↓
  
  example.com
  ```

- Inside that protected connection, HTTP messages are exchanged.

- Conceptually:

  ```text
  Browser

  HTTP Request
  
  ↓
  
  TLS Encryption
  
  ↓

  Network
  
  ↓
  
  TLS Decryption

  ↓

  Server
  ```

- The response travels back through the protected connection.

---

## What TLS Protects Against

- Suppose an ordinary intermediary can observe network traffic:

  ```text
  Browser

  ↓

  Network Observer
  
  ↓
  
  Server
  ```

- With correctly established HTTPS, the observer should not simply see plaintext such as:

  ```text
  username=alice
  password=ExamplePass
  ```

- Instead, the application data is protected by TLS.

- This prevents passive observers from casually reading or modifying the HTTP contents.

---

## The Burp Problem

- Security testing requires visibility into requests such as:

  ```http
  POST /login HTTP/1.1
  Host: example.com
  Content-Type: application/x-www-form-urlencoded

  username=alice&password=ExamplePass
  ```

- But if the browser established one untouched end-to-end TLS connection directly with the real server:

  ```text
  Browser

  ↓ Encrypted TLS
  
  Burp

  ↓ Encrypted TLS

  Server
  ```

- Burp could not simply read the plaintext HTTP messages inside that encryption.

- Therefore HTTPS interception requires a different architecture.

---

## Explicit Proxy and CONNECT

- When a browser uses an HTTP proxy for an HTTPS destination, it commonly begins by asking the proxy to establish communication toward the target.

- Example:

  ```http
  CONNECT example.com:443 HTTP/1.1
  Host: example.com:443
  ```

- Conceptually:

  ```text
  Browser

  ↓
  
  CONNECT example.com:443
  
  ↓
  
  Burp
  ```

- The browser is effectively saying:

  ```text
  I need a connection toward:

  example.com:443
  ```

---

## What CONNECT Normally Enables

- A basic proxy could create a tunnel:

  ```text
  Browser

  ↓
  
  Encrypted TLS Bytes
  
  ↓

  Proxy

  ↓

  Encrypted TLS Bytes

  ↓

  Server
  ```  

- If the proxy only tunnels bytes, it does not automatically see the decrypted HTTP contents.

- Burp's interception mode goes further.

---

## Burp's HTTPS Interception Architecture

- The key mental model is:

  ```text
  Browser

  ↓

  TLS Connection A
  
  ↓

  Burp
  
  ↓

  TLS Connection B
  
  ↓

  Target Server
  ```

- These are separate TLS sessions.

- Burp terminates one TLS relationship and establishes another.

---

## Connection A — Browser to Burp

- The browser establishes TLS with Burp for the requested hostname.

- Conceptually:

  ```text
  Browser

  ↓
  
  TLS

  ↓
  
  Burp
  ```

- For example, the browser expects a certificate valid for:

  ```text
  example.com
  ```  

- Burp presents an appropriate certificate for that intercepted hostname.

---

## Connection B — Burp to Server

- Separately:

  ```text
  Burp

  ↓
  
  TLS

  ↓
  
  Real example.com Server
  ```

- The real server presents its certificate to Burp.

- Burp communicates securely with the actual destination.

---

## HTTP Becomes Visible Inside Burp

- Conceptually:

  ```text
  Browser

  ↓
  
  Encrypted

  ↓
  
  Burp decrypts browser-side TLS

  ↓

  Plain HTTP available to Burp tools

  ↓

  Burp can inspect / modify

  ↓

  Burp encrypts upstream TLS

  ↓
  
  Server
  ```

- For the response:

  ```text
  Server
  
  ↓
    
  Encrypted
  
  ↓  

  Burp decrypts upstream TLS

  ↓

  HTTP response available to Burp

  ↓
  
  Burp encrypts browser-side TLS
  
  ↓
  
  Browser
  ```

- This is why Burp can display HTTPS requests in readable form.

---

## Burp Is Not Simply "Removing HTTPS"

- A common oversimplification is:

  ```text
  Burp breaks HTTPS.
  ```  

- A better model is:

  ```text
  TLS Session 1

  Browser ↔ Burp
  ```  

- and:

  ```text
  TLS Session 2

  Burp ↔ Server
  ```

- HTTPS still protects both network legs.

- Burp can inspect the plaintext because it is an intentional endpoint of both TLS relationships in the configured testing architecture.

---

## Why the Browser Does Not Automatically Trust Burp

- Suppose you visit:

  ```text
  https://example.com
  ```

- The browser expects a certificate chain it trusts for:

  ```text
  example.com
  ```

- Burp cannot simply present an arbitrary untrusted certificate and expect the browser to accept it.

- The browser performs certificate validation.

- Without the appropriate trust configuration, you may see:

  ```text
  Certificate Warning

  Untrusted Issuer

  Secure Connection Failed
  ```  

---

## Digital Certificates

- A TLS certificate helps associate a public key with an identity such as a hostname.

- Conceptually, a certificate contains information related to:

  ```text
  Identity / Hostname

  Public Key

  Validity

  Issuer

  Digital Signature
  ```  

- Browsers validate certificate chains before trusting secure connections.

---

## Certificate Authorities

- A Certificate Authority is trusted to issue or sign certificates.

- Conceptually:

  ```text
  Trusted CA

  ↓

  Signs Certificate
  
  ↓

  Certificate for example.com
  
  ↓

  Browser Verifies Trust Chain
  ```

- Browsers and operating systems maintain trust stores containing trusted root authorities.

---

## Certificate Chain

- A simplified certificate chain may look like:

  ```text
  Trusted Root CA

  ↓
    
  Intermediate CA

  ↓

  Server Certificate

  ↓
  
  example.com
  ```  

- The browser verifies whether the chain leads to a trusted authority and whether other validation requirements are satisfied.

---

## Burp's Certificate Authority

- Burp can create its own CA for HTTPS interception.

- Conceptually:

  ```text
  Burp CA

  ↓

  Signs Interception Certificates
  
  ↓

  example.com  
  api.example.com
  shop.example.com
  ...
  ```

- When you intentionally install and trust Burp's CA in a dedicated testing browser, certificates signed by that CA can be accepted by that trust context.

---

## Dynamic Certificate Generation

- Suppose you browse to:

  ```text
  https://example.com
  ```

- Burp needs to present a certificate appropriate for:

  ```text
  example.com
  ```  

- Later you visit:

  ```text
  https://api.example.org
  ```

- Burp needs a certificate appropriate for:

  ```text
  api.example.org
  ```

- Conceptually:

  ```text
  Requested Hostname

  ↓
  
  Burp Generates / Uses Suitable Certificate

  ↓

  Certificate Signed by Burp CA
  
  ↓

  Browser Validates Against Trusted Burp CA
  ```  

- This enables interception across many HTTPS hosts.

---

## Browser Trust Model With Burp

- Without trusting Burp CA:

  ```text
  Browser

  ↓

  Receives Burp-Generated Certificate
  
  ↓

  Issuer Not Trusted

  ↓

  Warning / Failure
  ```  

- After intentionally trusting Burp CA:

  ```text
  Browser

  ↓

  Receives Burp-Generated Certificate
  
  ↓

  Certificate Chains to Trusted Burp CA
  
  ↓

  Connection Can Proceed
  ```

- This is why installing Burp's CA certificate is necessary for many external-browser interception setups.

---

## The Trust Is Local and Intentional

- Installing Burp's CA does **not** make Burp a globally trusted public Certificate Authority.

- It means the particular trust store where you installed the CA has been configured to trust it.

- Conceptually:

  ```text
  Dedicated Testing Browser

  ↓
  
  Trusts Burp CA
  ```

- Other devices or applications may still reject Burp certificates unless separately configured.

---

## Why Use a Dedicated Browser Profile?

- Trusting a testing CA changes the browser's security trust configuration.

- Therefore a good professional practice is:

  ```text
  Dedicated Testing Browser/Profile

  ↓
  
  Burp CA Trusted
  ```

rather than unnecessarily modifying your everyday browsing environment.

- This helps isolate testing configuration.

---

## Burp's Embedded Browser

- Burp provides an embedded browser designed to work conveniently with Burp.

- Conceptually:

  ```text
  Burp Browser

  ↓

  Preconfigured Proxy / Trust Context
  
  ↓

  Burp
  
  ↓

  Target
  ```  

- This can reduce manual setup for basic testing.

- An external browser may require explicit proxy and CA configuration.

---

## External Browser Architecture

- Example:

  ```text
  Firefox

  ↓
  
  Proxy:
  127.0.0.1:8080

  ↓

  Burp

  ↓

  Target
  ```  

- For HTTPS interception:

  ```text
  Firefox Trust Store

  ↓

  Trust Burp CA

  ↓

  Accept Interception Certificates
  ```  

- Exact certificate-trust behavior varies between browsers, operating systems, and applications.

---

## Hostname Validation

- Trusting the issuer alone is not enough.

- The certificate must also be appropriate for the hostname being accessed.

- Suppose the browser requests:

  ```text
  example.com
  ```

but receives a certificate valid only for:

  ```text
  different.example
  ```

- The browser may reject it because of a hostname mismatch.

---

## Subject Alternative Name

- Modern certificates commonly identify valid hostnames using the **Subject Alternative Name (SAN)** extension.

- Conceptually:

  ```text
  Certificate

  SAN:
  example.com
  www.example.com
  ```

- The browser checks whether the requested hostname is covered appropriately.

---

## Burp Must Present the Correct Host Identity

- When intercepting:

  ```text
  https://example.com
  ```

- Burp needs a browser-side certificate appropriate for:

  ```text
  example.com
  ```

- This is part of why Burp dynamically handles interception certificates.

---

## What the Browser Sees

- During interception, the browser does not receive the real server's certificate directly as its TLS peer certificate.

- Instead:

  ```text
  Browser

  ↓

  Sees certificate presented by Burp
  ```

- while:

  ```text
  Burp

  ↓
  
  Sees certificate presented by real server
  ```  

- These belong to separate TLS connections.

---

## What Burp Sees Upstream

- The real server may present:

  ```text
  Real Server Certificate

  ↓
  
  Issued by Public / Private CA
  ```

- Burp evaluates the upstream TLS connection according to its configuration and TLS behavior.

- This is separate from the browser's trust in Burp.

---

## Two Different Certificate Validation Problems

### Browser-Side Problem

```text
Browser

↓

Does it trust the certificate Burp presents?
```

- Potential failure:

  ```text
  Burp CA not trusted
  ```  

### Server-Side Problem

```text
Burp

↓

Can it establish and validate TLS with the real destination?
```

- Potential failure:

  ```text
  Upstream certificate / protocol / network problem
  ```

- These should not be confused.

---

## Why Certificate Warnings Occur

- Common causes include:

  ```text
  Burp CA Not Installed

  Burp CA Installed in Wrong Trust Store

  Certificate Not Trusted for Website Identification

  Hostname Mismatch

  Expired Certificate

  Incorrect System Time

  Application Certificate Pinning

  TLS Negotiation Problem

  Wrong Proxy / Routing Configuration
  ```  

- Do not assume every HTTPS error has the same cause.

---

## Trust Store Differences

- Different applications may use different trust stores.

- For example:

  ```text
  Browser A

  ↓

  Operating-System Trust Store
  ```  

- while another application may use:

  ````text
  Application-Specific Trust Store
  ```

- or:

  ```text
  Bundled CA Set
  ```  

- Therefore:

  ```text
  Burp Works in One Browser

  ≠  

  Burp Must Work in Every Application
  ```

---

## Example — Browser Trust Failure

- Architecture:

  ```text
  Browser

  ↓

  Burp

  ↓

  Server
  ```  

- HTTP traffic reaches Burp, but the browser displays a certificate warning.

- Likely investigation:

  ```text
  Does Browser Trust Burp CA?

  ↓

  Was CA Imported Correctly?

  ↓

  Was Correct Trust Purpose Enabled?

  ↓

  Is Correct Browser Profile Being Used?

  ↓

  Is Hostname Correct?  
  ```

- This points primarily to the browser-side TLS relationship.

---

## Example — Upstream TLS Failure

- Suppose the browser trusts Burp correctly.

- Flow:

  ```text
  Browser

  ↓
  
  TLS to Burp succeeds
  
  ↓
  
  Burp

  ─X─►
  
  Server TLS fails
  ```

- Possible causes:

  * Server certificate problem.
  * Unsupported TLS configuration.
  * Mutual TLS requirements.
  * Routing to the wrong host.
  * Incorrect SNI/hostname context.
  * Network interception.
  * Upstream proxy issues.

- The browser CA configuration is not necessarily the problem.

---

## Server Name Indication

- During TLS, the intended hostname can matter before HTTP is exchanged.

- One mechanism is **Server Name Indication (SNI)**.

- Conceptually:

  ```text
  Burp

  ↓

  TLS Connection

  ↓

  "I want example.com"

  ↓

  Server / Reverse Proxy
  ```

- This helps infrastructure select the appropriate certificate or virtual service.

---

## Why SNI Matters

- One IP address may host multiple HTTPS applications:

  ```text
  203.0.113.10

  ├── app.example.com
  ├── api.example.com
  └── shop.example.com
  ```

- The intended hostname can influence which TLS certificate and service are selected.

- Therefore:

  ```text
  Correct IP

  but

  Wrong Hostname Context
  ```  

can still cause TLS or application problems.

---

## TLS Certificate vs HTTP Host

- These occur at different layers.

- Conceptually:

  ```text
  TLS

  ↓

  SNI / Certificate Identity
  ```  

- then:

  ```text
  HTTP

  ↓

  Host Header / :authority
  ```

- Both may reference the intended hostname, but they serve different protocol roles.

---

## Certificate Pinning

- Some applications do more than normal CA-based certificate validation.

- They may expect a specific:

  ```text
  Certificate

  Public Key
  
  Certificate Chain
  
  or
  
  Cryptographic Identity
  ```

- This is broadly known as **certificate pinning**.

---

## Normal Trust Model

- Conceptually:

  ```text
  Certificate

  ↓

  Chains to Trusted CA

  ↓

  Accepted
  ```  

- With Burp CA trusted:

  ```text
  Burp Certificate

  ↓

  Chains to Trusted Burp CA

  ↓

  Accepted
  ```  

---

## Pinning Model

- A pinned application may effectively require:

  ```text
  Certificate / Key

  =

  Expected Specific Identity
  ```  

- Burp's dynamically generated certificate may be valid under the local trust store but still fail the application's additional pinning check.

---

## Symptoms of Certificate Pinning

- Possible symptoms include:

  ```text
  Browser Works Through Burp

  but

  Mobile App Fails
  ```

- or:

  ```text
  Application Rejects Connection

  despite

  Burp CA Being Installed
  ```  

- This does not automatically prove pinning, but it is one possible explanation.

---

## Certificate Pinning Is Application-Specific

- Do not assume:

  ```text
  HTTPS App

  =

  Certificate Pinning
  ```

- Many applications rely only on normal platform certificate validation.

- Pinning is an additional security control used by some applications.

---

## Mutual TLS

- Ordinary HTTPS commonly authenticates the server to the client.

- Some systems also require the client to present a certificate.

- This is called **mutual TLS (mTLS)**.

- Conceptually:

  ```text
  Client verifies Server

  and

  Server verifies Client Certificate
  ```  

- Architecture:

  ```text
  Client

  ↔ Mutual TLS ↔

  Server
  ```  

- When Burp is inserted, certificate handling becomes more complex because the two TLS relationships remain separate.

---

## mTLS Through Burp

- Conceptually:

  ```text
  Client

  ↓

  TLS to Burp
  
  ↓

  Burp

  ↓

  TLS to Server
  
  ↓

  Server Requests Client Certificate
  ```

- Burp may need appropriate client-certificate configuration for the upstream connection.

- A normal Burp CA installation alone does not solve mTLS requirements.

---

## Why Some Sites Behave Differently Through Burp

- Introducing Burp changes the connection architecture.

- Differences may involve:

  ```text
  TLS Fingerprint

  HTTP Version

  Connection Reuse

  Certificates

  Proxy Headers
  
  Network Source

  Protocol Negotiation
  ```  

- Most applications work normally, but some may behave differently because the upstream connection is now established by Burp rather than directly by the browser.

---

## TLS Negotiation

- Before encrypted application data is exchanged, the endpoints negotiate TLS parameters.

- Conceptually:

  ```text
  Client Hello

  ↓
  
  Server Hello
  
  ↓
  
  Certificate / Key Exchange

  ↓
  
  Secure Session Established
  ```  

- The real protocol is more detailed, but the important point is:

  ```text
  Browser ↔ Burp

  negotiates independently from
  
  Burp ↔ Server
  ```

---

## Independent TLS Versions and Parameters

- Because these are separate sessions:

  ```text
  Browser ↔ Burp

  TLS Session A
  ```  

- and:

  ```text
  Burp ↔ Server

  TLS Session B
  ```

- their negotiated parameters do not have to be identical.

- Potential differences include:

  ```text
  TLS Version

  Cipher Suite
  
  ALPN / HTTP Protocol

  Certificate Chain

  Session Reuse
  ```  

---

## ALPN and HTTP Protocol Selection

- TLS can help negotiate which application protocol will be used, such as:

  ```text
  HTTP/1.1

  HTTP/2
  ```

- Because Burp creates separate connections:

  ```text
  Browser

  ↓

  HTTP/2 over TLS

  ↓

  Burp

  ↓

  HTTP/1.1 over TLS

  ↓

  Server
  ```  

may be possible depending on configuration and support.

- Again:

  ```text
  Browser-Side Protocol

  ≠

  Necessarily Server-Side Protocol
  ```  

---

## HTTPS Does Not Prevent Authorized Interception

- HTTPS protects communication against unauthorized or untrusted intermediaries.

- In a Burp testing setup, the user intentionally:

  ```text
  Configures Browser to Use Burp

  and

  Trusts Burp CA
  ```  

- This creates a controlled interception environment.

- The security model has been intentionally changed for testing.

---

## Why Attackers Cannot Normally Do This Automatically

- An arbitrary network attacker generally cannot simply present:

  ```text
  Fake Certificate for example.com
  ```  

and have a properly configured browser trust it.

- The browser should reject certificates that do not chain to an accepted trust anchor or otherwise fail validation.

- Burp works smoothly because the tester explicitly configures trust in Burp's CA.

---

## Security Implications of Installing a CA

- A trusted CA has significant authority within the trust context where it is installed.

- Therefore:

  ```text
  Treat Burp CA Private Key and Trust Configuration Carefully
  ```

- Good practices include:

  * Use a dedicated testing browser/profile.
  * Install the CA only where required.
  * Do not distribute private CA material unnecessarily.
  * Avoid installing testing CAs on unrelated production systems.
  * Remove unnecessary trust configuration after temporary testing environments are retired.
  * Protect the Burp project and environment appropriately.

---

## Do Not Install Burp CA Everywhere

- Avoid:

  ```text
  Every Personal Device

  Every Browser

  Every Production System
  ```  

when only one dedicated testing environment needs interception.

- Prefer:

  ```text
  Dedicated Security Testing Environment
  ```  

with controlled trust.

---

## Certificate Installation Does Not Grant Authorization

- Trusting Burp's CA gives technical ability to inspect HTTPS traffic routed through Burp.

- It does not provide permission to test arbitrary systems.

- Remember:

  ```text
  Technical Capability

  ≠

  Authorization
  ```  

---

## HTTPS Request Lifecycle Through Burp

- A simplified complete flow:

  ```text
  1. User visits https://example.com

          ↓

  2. Browser sees proxy configuration

          ↓

  3. Browser connects to Burp listener

          ↓

  4. Browser requests CONNECT example.com:443

          ↓
  
  5. Burp prepares upstream connection

          ↓

  6. Browser establishes TLS with Burp
  
        ↓

  7. Burp presents certificate for example.com
   signed by Burp CA

          ↓

  8. Browser validates certificate
   against trusted Burp CA

          ↓

  9. Browser sends encrypted HTTP to Burp

          ↓

  10. Burp decrypts and exposes HTTP message

          ↓

  11. Burp can inspect / modify request

          ↓

  12. Burp establishes / uses TLS with real server

          ↓

  13. Burp sends request upstream

          ↓

  14. Server returns HTTPS response to Burp

          ↓

  15. Burp decrypts upstream response
  
        ↓
  
  16. Burp can inspect / process response
  
        ↓

  17. Burp encrypts response for browser-side TLS

          ↓

  18. Browser receives and processes response
  ```  

- The actual implementation can involve connection reuse and protocol-specific behavior, but this is the essential model.

---

## Intercept ON During HTTPS

- Once TLS interception is successfully established:

  ```text
  Encrypted Browser Traffic

  ↓

  Burp TLS Termination

  ↓

  Plain HTTP Message

  ↓

  Intercept
  ```  

- You can modify the HTTP request just as you would with ordinary HTTP.

- Burp then handles re-encryption automatically.

---

## You Do Not Manually Encrypt Modified Requests

- Suppose you change:

  ```text
  id=105
  ```  

- to:

  ```text
  id=106
  ```  

- You do not need to manually perform TLS encryption.

- Conceptually:

  ```text
  Modify HTTP in Burp

  ↓

  Burp Handles TLS
  
  ↓
  
  Encrypted Upstream Transmission
  ```

- This separation lets testers focus on application-layer behavior.

---

## Why HTTP History Shows Plaintext HTTPS

- HTTP history may show:

  ```http
  GET /account HTTP/1.1
  Host: example.com
  Cookie: session=abc123
  ```

- even though the browser visited:

  ```text
  https://example.com
  ```

- This does not mean HTTPS was absent.

- It means Burp is displaying the HTTP message **after TLS decryption within the proxy**.

---

## HTTPS and Sensitive Data

- Because Burp can see decrypted traffic, HTTP history may contain:

  ```text
  Session Cookies

  Authorization Tokens

  Passwords

  Personal Data

  API Keys

  Application Secrets
  ```  

- Treat Burp projects and saved traffic as sensitive security data.

---

## Project Data Security

- Professional practices include:

  ```text
  Protect Project Files

  Limit Access
  
  Avoid Unnecessary Sensitive Retention

  Redact Secrets in Public Screenshots

  Do Not Commit Captured Credentials to GitHub

  Follow Engagement Data-Handling Requirements
  ```  

- A Burp project can contain far more sensitive information than expected.

---

## Troubleshooting HTTPS — Layered Method

- When HTTPS fails, identify which stage fails.

---

### Stage 1 — Does Traffic Reach Burp?

- Check:

  ```text
  Browser Proxy Configuration

  ↓

  Burp Listener
  
  ↓

  HTTP History / Event Information
  ```

- If nothing reaches Burp, certificate troubleshooting is premature.

---

### Stage 2 — Does CONNECT Succeed?

- Ask:

  ```text
  Can Browser Reach Burp?

  Can Burp identify the HTTPS destination?
  
  Is proxy routing correct?
  ```

- A proxy-routing failure may occur before certificate trust becomes relevant.

---

### Stage 3 — Browser-Side TLS

- Ask:

  ```text
  Does browser trust Burp CA?

  Is CA installed in correct trust store?
  
  Is correct browser profile being used?

  Is certificate valid for requested hostname?

  Is system time reasonable?
  ```

---

### Stage 4 — Burp-Side Upstream TLS

- Ask:

  ```text
  Can Burp resolve target?

  Can Burp reach port 443?

  Does upstream TLS negotiation succeed?

  Is SNI/hostname correct?

  Is an upstream proxy interfering?

  Does server require mTLS?
  ```  

---

### Stage 5 — Application-Level Behavior

- If TLS succeeds:

  ```text
  Does HTTP request reach server?

  Is response valid?

  Is application redirecting?

  Is authentication required?

  Does application behave differently through proxy?
  ```  

- Do not treat application errors as certificate errors without evidence.

---

## Common Error — CA Not Trusted

- Symptom:

  ```text
  Certificate Authority Invalid

  Unknown Issuer

  Untrusted Certificate
  ```  

- Likely model:

  ```text
  Browser

  ↓

  Receives Burp Certificate

  ↓

  Cannot Build Trusted Chain

  ↓

  Rejects Connection
  ```  

- Investigate the browser-side trust store.

---

## Common Error — Wrong Browser Profile

- You may install the Burp CA in:

  ```text
  Firefox Profile A
  ```  

- but browse through:

  ```text
  Firefox Profile B
  ```

- Result:

  ```text
  Profile B

  ↓

  Does Not Trust Burp CA
  ```

- Always confirm which profile is actually running.

---

## Common Error — HTTPS Works in Burp Browser but Not Firefox

- This often indicates:

  ```text
  Burp Proxy / Upstream Path Works

  but

  External Browser Configuration Differs
  ```  

- Investigate:

  * External browser proxy settings.
  * CA trust.
  * Proxy bypass.
  * Browser-specific trust behavior.

- This comparison is useful evidence.

---

## Common Error — HTTP Works but HTTPS Fails

- This narrows the problem.

- If:

  ```text
  http://example.com

  works
  ```

- but:

  ```text
  https://example.com

  fails
  ```  

- investigate layers specific to HTTPS:

  ```text
  CONNECT

  TLS

  CA Trust

  Certificate Validation
  
  SNI

  Upstream TLS
  ```

rather than assuming the proxy listener is completely broken.

---

## Common Error — Browser Loads Normally With Intercept OFF

- If HTTPS works with:

  ```text
  Intercept OFF
  ```  

- but appears frozen with:

  ```text
  Intercept ON
  ```

the TLS setup may be perfectly fine.

- The browser may simply be waiting because Burp has paused one or more requests.

- Check the intercepted request queue.

---

## Common Error — Certificate Pinning Suspected Too Early

- Do not jump immediately to:

  ```text
  Certificate Pinning
  ```  

- First verify:

  ```text
  Proxy Configuration

  Listener

  CA Trust

  Correct Certificate Installation

  DNS

  Routing
  
  TLS
    
  Application Logs
  ```

- Pinning is one possibility, not the universal explanation for HTTPS failure.

---

## Common Mistakes

- Avoid:

```text
HTTPS means Burp cannot inspect traffic.
```

- Incorrect when Burp is intentionally configured as a trusted interception proxy.

---

```text
Burp simply decrypts the browser's direct end-to-end TLS connection.
```

- Incorrect mental model.

- Think in two separate TLS relationships.

---

```text
Installing Burp CA disables HTTPS.
```

- Incorrect.

- TLS still protects both communication legs.

---

```text
Burp shows plaintext, so traffic crossed the network unencrypted.
```

- Incorrect.

- Burp displays plaintext after decrypting traffic at a TLS endpoint.

---

```text
The browser sees the real server certificate directly.
```

- Not during normal Burp HTTPS interception.

- The browser sees the certificate presented by Burp for that intercepted host.

---

```text
The server sees Burp's CA certificate.
```

- Incorrect.

- The Burp CA is primarily involved in the browser/client-side interception trust relationship.

---

```text
If Burp CA is installed, every application will trust it.
```

- Incorrect.

- Applications may use different trust stores or certificate pinning.

---

```text
Every mobile application uses certificate pinning.
```

- Incorrect.

- Pinning is application-specific.

---

```text
Every HTTPS failure is a CA problem.
```

- Incorrect.

- Failures may occur at routing, DNS, CONNECT, TLS, mTLS, protocol, or application layers.

---

```text
Installing a CA gives permission to test the target.
```

- Incorrect.

- Authorization is separate.

---

## Professional Mental Model

- Memorize:

  ```text
  Browser

  ↓

  CONNECT / Proxy Relationship
  
  ↓
  
  TLS Session A
  
  ↓

  Burp
  
  ↓

  HTTP Visible for Testing

  ↓

  TLS Session B

  ↓

  Server
  ```

- Trust model:

  ```text
  Browser

  ↓
  
  Trusts Burp CA
  
  ↓
  
  Accepts Burp-Generated Host Certificate
  ```

- Upstream model:

  ```text
  Burp

  ↓
  
  Connects to Real Server

  ↓
  
  Validates / Negotiates Server TLS
  ```

- These are separate responsibilities.

---

## Practical Exercise

- Use only an authorized practice environment.

### Exercise 1 — Compare HTTP and HTTPS

- Visit:

  ```text
  http://example.com
  ```  

- and:

  ```text
  https://example.com
  ```

through Burp.

- Observe that both appear as readable HTTP messages inside Burp even though the second uses HTTPS on the network.

---

### Exercise 2 — Inspect Certificate Context

- Using your dedicated testing browser:

  1. Visit an HTTPS site through Burp.
  2. Inspect the certificate information shown by the browser.
  3. Identify the issuer.
  4. Compare the browser-side certificate context with what you expect from direct browsing.

- Do not export or share private CA key material.

---

### Exercise 3 — Trace the Architecture

- Write the flow from memory:

  ```text
  Browser

  ↓

  Burp Listener

  ↓
  
  CONNECT

  ↓
  
  Browser-Side TLS
  
  ↓
  
  Burp HTTP Inspection
  
  ↓

  Server-Side TLS
  
  ↓

  Target
  ```  

- Then trace the response in reverse.

---

## HTTPS Interception Checklist

```text
[ ] I understand HTTP vs HTTPS.

[ ] I understand that TLS protects HTTP in transit.

[ ] I know the purpose of CONNECT in explicit HTTPS proxying.

[ ] I understand Browser ↔ Burp is one TLS relationship.

[ ] I understand Burp ↔ Server is another TLS relationship.

[ ] I know why Burp can see plaintext HTTP.

[ ] I understand what a Certificate Authority does.

[ ] I understand why the browser must trust Burp CA.

[ ] I know Burp can generate certificates for intercepted hostnames.

[ ] I understand hostname validation and SAN conceptually.

[ ] I distinguish browser-side and server-side certificate validation.

[ ] I understand SNI conceptually.

[ ] I understand certificate pinning conceptually.

[ ] I understand mTLS conceptually.

[ ] I know different applications may use different trust stores.

[ ] I know Burp project data may contain sensitive decrypted information.

[ ] I troubleshoot HTTPS failures layer by layer.

[ ] I use Burp CA only in controlled testing environments.

[ ] I test only authorized systems.
```

---

## Key Takeaways

* HTTPS is HTTP protected by TLS.
* A normal passive intermediary cannot simply read correctly encrypted HTTPS application data.
* HTTPS proxying commonly uses the `CONNECT` method to identify the destination.
* Burp HTTPS interception creates separate TLS relationships: browser ↔ Burp and Burp ↔ server.
* Burp can inspect HTTP because it terminates and originates TLS within this controlled proxy architecture.
* Burp does not simply "turn off" HTTPS; both network legs can remain encrypted.
* The browser must trust Burp's CA to accept Burp-generated interception certificates without warnings.
* Burp dynamically presents certificates appropriate for intercepted hostnames.
* Hostname validation remains important, including SAN-based identity checks.
* The browser-side certificate and real server-side certificate belong to different TLS connections.
* SNI can influence which certificate and virtual service an upstream server provides.
* Certificate pinning can cause an application to reject Burp even when the Burp CA is trusted normally.
* Mutual TLS introduces separate client-certificate requirements.
* Different applications may use different certificate trust stores.
* HTTPS failures should be diagnosed across routing, CONNECT, browser-side TLS, upstream TLS, and application layers.
* Decrypted Burp traffic may contain highly sensitive information and must be handled securely.
* Trusting Burp's CA provides technical interception capability, not authorization to test systems.

