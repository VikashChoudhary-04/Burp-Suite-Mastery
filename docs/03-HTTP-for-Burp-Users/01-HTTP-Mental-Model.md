# HTTP Mental Model

> **Module Progress**

```text id="izjcck"
█░░░░░░░░░░░░ 1 / 13 Lessons
```

- Burp Suite becomes much easier to understand when you stop thinking primarily in terms of web pages and start thinking in terms of **HTTP messages**.

- A browser shows you a visual application.

- Burp shows you the communication that makes that application work.

---

## The Fundamental Model

- At the simplest level:

  ```text id="we88u2"
  Client

  ↓

  HTTP Request

  ↓

  Server

  ↓

  HTTP Response

  ↓

  Client
  ```

- The client asks for something.

- The server responds.

- This request-response model is the foundation of most Burp workflows.

---

## What Is a Client?

- A client is software that communicates with a server.

- Examples include:

  ```text id="b51j4g"
  Web Browser

  Mobile Application

  Desktop Application

  API Client

  Command-Line Tool

  Burp Repeater
  ```  

- Do not assume:

  ```text id="9ivayh"
  HTTP Client

  =

  Browser Only  
  ```

- Anything capable of constructing and sending HTTP messages can act as a client.

---

## What Is a Server?

- A server receives requests and produces responses.

- However, the "server" visible to the client may represent a much larger architecture.

- For example:

  ```text id="osld3z"
  Browser

  ↓

  CDN / Reverse Proxy

  ↓

  Load Balancer

  ↓

  Web Server

  ↓  

  Application

  ↓

  Database / Internal Services
  ```

- From Burp, you often observe only the HTTP-facing behavior of this larger system.

- This distinction matters.

- A response tells you what was externally observable—not necessarily every internal step that produced it.

---

## A Web Page Is Usually Many Requests

- When you enter:

  ```text id="o1znq1"
  https://example.com
  ```  

- you may think:

  > "The browser requested one page."

- In reality, the browser may request:

  ```text id="wsbmqz"
  HTML

  CSS

  JavaScript
  
  Images

  Fonts

  API Data

  Analytics Resources

  Authentication Checks

  Background Requests
  ```

- Conceptually:

  ```text id="hwx8we"
  Initial HTML Request
          │
          ├── CSS Request
          ├── JavaScript Request
          ├── Image Request
          ├── API Request
          └── Additional Requests
  ```

- One visible action can generate many HTTP messages.

---

## User Action vs HTTP Action

- Suppose you click:

  ```text id="saj26q"
  View Profile
  ```

- The browser may generate:

  ```http id="wjxg4y"
  GET /api/profile HTTP/1.1
  Host: example.com
  Cookie: session=abc123
  ```

- The important security question is not merely:

  > "What button did I click?"

- It is:

  > **"What HTTP behavior did that action trigger?"**

---

## The Browser Is an HTTP Generator

- A useful Burp mental model is:

  ```text id="rj9unf"
  User Action

  ↓

  Browser Logic

  ↓

  HTTP Request

  ↓

  Server Behavior

  ↓

  HTTP Response

  ↓

  Browser Interpretation
  
  ↓
  
  Visible Result
  ```  

- Burp gives you visibility near the middle of this chain.

---

## Browser UI vs Server Reality

- Suppose a page does not display an Admin button.

- A beginner may conclude:

  > "This user cannot perform admin actions."

- But the browser interface is only one layer.

- The security question is:

  > Does the **server** enforce that restriction?

- Conceptually:

  ```text id="ohynkp"
  Browser UI Restriction

  ≠

  Server-Side Authorization
  ```

- The browser may hide functionality.

- The server must enforce security.

---

## HTTP Messages Are Evidence

- When investigating with Burp, treat requests and responses as evidence.

- A request can reveal:

  * What resource is being accessed.
  * Which method is used.
  * Which parameters are supplied.
  * Which cookies identify the session.
  * Which authorization data is present.
  * Which content format is used.

- A response can reveal:

  * Whether the action succeeded.
  * What data was returned.
  * Whether state changed.
  * Which cookies were issued.
  * Where the client is redirected.
  * What security controls may be present.

---

## The Request-Response Pair

- Do not analyze requests and responses independently.

- Think:

  ```text id="dah6x4"
  Request

  ↓

  Server Processing

  ↓

  Response
  ```

- The meaning comes from the relationship between them.

- For example:

  ```http id="pjah6b"
  GET /account?id=105 HTTP/1.1
  Cookie: session=USER_A
  ```

followed by:

  ```http id="d5s9un"
  HTTP/1.1 200 OK
  Content-Type: application/json

  {
    "id": 105,
    "name": "Example User"
  }
  ```

- The response becomes meaningful only when you understand what was requested and under which session context.

---

## HTTP Is Usually Stateless

- A fundamental HTTP concept is that individual requests are generally independent at the protocol level.

- Conceptually:

  ```text id="e5i64w"
  Request 1

  ↓

  Response 1

  Request 2

  ↓

  Response 2
  ```  

- The protocol itself does not automatically understand:

  > "These requests belong to Vikash's logged-in session."

- Applications add state using mechanisms such as:

  * Cookies.
  * Session identifiers.
  * Authorization tokens.

- This becomes critical for authentication and authorization testing.

---

## Application State Is Built on Top of HTTP

- Consider login:

  ```text id="3kmc69"
  Submit Credentials

  ↓

  Server Validates

  ↓

  Session Created

  ↓

  Session Identifier Returned
  
  ↓

  Browser Sends Identifier on Later Requests

  ↓
  
  Server Associates Requests With User
  ```

- HTTP carries the messages.

- The application builds the authenticated state.

---

## One Request Can Have Multiple Inputs

- When reading a request, do not look only at URL parameters.

- Input may exist in:

  ```text id="c9md17"
  Path

  Query String

  Headers

  Cookies

  Request Body

  JSON

  Multipart Data
  ```

- For example:

  ```http id="k6qdkc"
  POST /api/users/105?notify=true HTTP/1.1
  Host: example.com
  Authorization: Bearer TOKEN
  Content-Type: application/json
  Cookie: preference=dark

  {
    "role": "user"
  }
  ```

- Potentially meaningful inputs exist throughout the message.

---

## Server Behavior Is What Matters

- A client can send almost anything.

- For example, a browser interface might normally send:

  ```json id="1d0zue"
  {
    "role": "user"
  }
  ```

- A tester might ask:

  > What happens if the request contains a different value?

- The important question is not whether the browser normally allows the change.

- It is:

  > **How does the server handle the request?**

- This is why Burp is powerful.

- It separates:

  ```text id="p1d3v1"
  What the Browser Chooses to Send
  ```

- from:

  ```text id="73q4cx"
  What the Client Is Technically Capable of Sending
  ```

---

## Trust Boundaries

- Whenever data crosses from client to server, treat it as crossing a trust boundary.

  ```text id="tihot8"
  Client-Controlled Environment

  ↓

  HTTP Request

  ↓

  Trust Boundary

  ↓

  Server-Side Environment
  ```

- The server should not blindly trust client-controlled data.

- Potential examples include:

  * User IDs.
  * Prices.
  * Roles.
  * File names.
  * Redirect destinations.
  * Object identifiers.

- Whether these are actually exploitable depends on server-side behavior.

---

## HTTP as a Security Conversation

- Think of every request as making a statement:

  ```text id="52l1ly"
  "I am this user."

  "I want this resource."

  "I want to perform this action."

  "Here is my input."

  "Here is my session."

  "Here is the format I expect."
  ```

- The server response answers:

  ```text id="oaq84h"
  "Accepted."
  
  "Rejected."

  "Redirected."

  "Not Found."

  "Here Is the Data."

  "Here Is a New Session."

  "An Error Occurred."
  ```

- Security testing investigates whether the server makes correct decisions during this conversation.

---

## Burp's Role

- Burp sits where these messages can be observed and controlled.

  ```text id="7i0nz9"
  Client

  ↓

  HTTP Request

  ↓

  BURP
  
  ↓

  Server
  ```

- and:

  ```text id="gmefth"
  Server

  ↓

  HTTP Response

  ↓
  
  BURP

  ↓

  Client
  ```

- This allows you to:

  * Observe.
  * Pause.
  * Modify.
  * Replay.
  * Compare.
  * Analyze.

- But the underlying object being investigated remains the HTTP conversation.

---

## Think in Messages, Not Pages

- A beginner sees:

  ```text id="3m8r4s"
  Account Settings Page
  ```

- A tester begins seeing:

  ```text id="8im71y"
  GET /account

  GET /api/profile

  PATCH /api/profile

  POST /api/avatar

  GET /api/preferences
  ```

- The visual page is an abstraction.

- The HTTP messages reveal the application's functional surface.

---

## Think in Transitions

- Many application behaviors are sequences.

- For example:

  ```text id="dcd7w2"
  GET /login

  ↓

  POST /login

  ↓

  302 Redirect

  ↓

  GET /dashboard
  
  ↓

  GET /api/user
  ```

- Understanding the sequence can reveal:

  * Authentication flow.  
  * Session creation.
  * Redirect behavior.
  * API dependencies.
  * State transitions.

- Do not always inspect requests in isolation.

---

## Baseline Before Modification

- Before changing an HTTP message, understand the normal conversation.

- Use:

  ```text id="d9c4fp"
  Normal User Action

  ↓
  
  Capture Request
  
  ↓

  Understand Components

  ↓

  Observe Normal Response

  ↓

  Establish Baseline
  
  ↓
  
  Form Security Question

  ↓

  Modify One Relevant Variable
  ```

- This produces much stronger evidence than random mutation.

---

## Example Mental Walkthrough

- Suppose clicking **View Invoice** generates:

  ```http id="24vfdc"
  GET /api/invoices/4821 HTTP/1.1
  Host: example.com
  Cookie: session=USER_A
  ```

- Ask:

### What is the client requesting?

```text id="fgw4jb"
Invoice 4821
```

### What identifies the authenticated session?

```text id="cfwrcq"
Cookie: session=USER_A
```

### Which value appears to identify the object?

```text id="rv8q5w"
4821
```

### What security assumption might exist?

```text id="y9z8fu"
The server should verify that USER_A
is authorized to access invoice 4821.
```

- This is how HTTP understanding turns into security reasoning.

---

## Do Not Overinterpret One Message

- A single request may not reveal:

  * Where the value originated.
  * Whether JavaScript generated it.
  * Whether another request established state.
  * Whether authorization depends on server-side session data.
  * Whether the response changes application state elsewhere.

- Investigate context.

---

## HTTP Mental Checklist

- When you see a request, ask:

  ```text id="96hs7b"
  What action triggered this?

  What resource is being requested?

  Which HTTP method is used?

  What identifies the user/session?

  Where are the inputs?

  Which values appear security-sensitive?

  What does the server return?

  Did server-side state change?

  What assumption is the server expected to enforce?
  ```  

- This checklist will become increasingly powerful as your HTTP knowledge grows.

---

## Key Takeaways

* Burp operates on HTTP conversations, not visual web pages.
* A single browser action may generate many requests.
* Clients are not limited to browsers.
* The visible server may represent a larger backend architecture.
* Requests and responses should be analyzed as pairs.
* HTTP is generally stateless; applications build state on top of it.
* Inputs can exist in paths, queries, headers, cookies, and bodies.
* Browser restrictions are not equivalent to server-side security.
* Client-to-server communication crosses a trust boundary.
* Server behavior—not browser behavior—determines whether a security control is effective.
* Think in messages, sequences, state transitions, and security assumptions.
