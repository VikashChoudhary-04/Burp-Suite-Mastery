# Target Tool

## Learning Objectives

- By the end of this chapter, you will be able to:

  * Understand the purpose of the Target tool.
  * Define and manage testing scope.
  * Navigate the Site Map effectively.
  * Organize discovered content during assessments.
  * Identify interesting attack surfaces.
  * Use the Target tool as part of a professional testing workflow.

---

## Introduction

- The Target tool helps you understand **what you are testing**.

- Before looking for vulnerabilities, you need to know:

  * Which hosts exist?
  * Which directories exist?
  * Which files exist?
  * Which APIs exist?
  * Which technologies are being used?
  * Which parts are in scope?

- The Target tool answers these questions.

---

## Why Does the Target Tool Exist?

- Imagine testing a web application with:

  * 15 subdomains
  * 2,000 URLs
  * Hundreds of API endpoints
  * Multiple user roles

- Trying to remember everything manually is impossible.

- The Target tool automatically organizes discovered content into a structured hierarchy, allowing you to understand the application's attack surface.

---

## Mental Model

- Think of the Target tool as a file explorer for a web application.

  ```text
  example.com
  │
  ├── /
  ├── /login
  ├── /register
  ├── /profile
  ├── /admin
  ├── /api
  │   ├── /users
  │   ├── /orders
  │   └── /products
  └── /static
  ```

- Instead of folders and files on a computer, you see pages, APIs, scripts, and endpoints discovered during testing.

---

## Behind the Scenes

- Whenever traffic passes through Burp Proxy, Burp analyzes every request and response.

- From that traffic, Burp extracts information such as:

  * Hostnames
  * Paths
  * Parameters
  * Response types
  * JavaScript files
  * Directories
  * HTTP methods

- Burp then builds the Site Map automatically.

- The more you browse, the more complete your Site Map becomes.

---

## Components of the Target Tool

- The Target tool primarily consists of:

  * Site Map
  * Scope

- Each serves a different purpose.

---

## Site Map

- The Site Map is a hierarchical view of everything Burp has discovered.

- It updates automatically as you browse the application.

- Information commonly shown includes:

  * Domains
  * Directories
  * Files
  * API endpoints
  * Parameters
  * Status codes
  * Content types

- The Site Map becomes your central reference during testing.

---

## Scope

- Scope defines **what Burp considers part of your assessment**.

- Anything outside the defined scope should generally not be tested.

- Benefits of using Scope include:

  * Cleaner HTTP History
  * Better organization
  * Reduced distractions
  * Safer testing
  * Easier reporting

- Defining scope is one of the first tasks in a professional engagement.

---

## Professional Workflow

- A typical workflow is:

  ```text
  Define Scope
          │
          ▼
  Browse the Application
          │
          ▼
  Populate Site Map
          │
          ▼
  Identify Interesting Areas
          │
          ▼
  Send Requests to Repeater
          │
          ▼
  Perform Manual Testing
          │
          ▼
  Document Findings
  ```

---

## Real-World Example

- Suppose your engagement covers:

  ```text
  https://shop.example.com
  ```

- While browsing, Burp discovers:

  ```text  
  /
  ├── login
  ├── register
  ├── cart
  ├── checkout
  ├── admin
  ├── api
  │   ├── orders
  │   ├── users
  │   └── products
  └── static
  ```

- From this structure alone, you can identify high-value testing targets such as:

  * Authentication (`/login`)
  * Authorization (`/admin`)
  * Business logic (`/checkout`)
  * API endpoints (`/api`)
  * Sensitive user data (`/users`)

---

## Bug Bounty Use Cases

- The Target tool is especially useful for:

  * Mapping large applications
  * Discovering hidden functionality
  * Tracking authenticated and unauthenticated content
  * Organizing API endpoints
  * Identifying administrative interfaces
  * Locating JavaScript-heavy areas

---

## Penetration Testing Use Cases

- During professional engagements, the Target tool helps you:

  * Verify testing scope
  * Estimate application complexity
  * Prioritize high-risk functionality
  * Organize notes
  * Reduce duplicate testing
  * Prepare evidence for reports

---

## Common Mistakes

* Forgetting to define scope.
* Testing assets outside the authorized scope.
* Ignoring the Site Map.
* Browsing randomly without understanding the application's structure.
* Assuming every discovered endpoint is accessible.

---

## Field Notes

- Professional testers spend significant time exploring an application before attempting exploitation.

- A well-understood application usually leads to better vulnerability discovery than immediately attacking random endpoints.

- Treat reconnaissance and mapping as part of the assessment—not as a separate activity.

---

## Practice Exercises

1. Define a target scope.
2. Browse the application normally.
3. Explore every major feature.
4. Review the Site Map.
5. Identify:

   * Authentication pages
   * Admin pages
   * API endpoints
   * Static resources
   * JavaScript files
6. Choose three interesting requests and send them to Repeater.

---

## Interview Questions

1. What is the purpose of the Target tool?
2. What is the Site Map?
3. Why should testing scope be defined before beginning an assessment?
4. How does Burp populate the Site Map?
5. Why is the Target tool valuable during large penetration tests?

---

## Quick Revision

* The Target tool organizes discovered content.
* The Site Map is built automatically from observed traffic.
* Scope defines what should be tested.
* Understanding the application's structure is essential before vulnerability testing.
* The Target tool is the foundation for organized manual testing.

---

## Self Assessment

Can you:

* Explain the difference between Scope and Site Map? □ Yes □ No
* Define a testing scope? □ Yes □ No
* Identify high-value endpoints from a Site Map? □ Yes □ No
* Use the Target tool during a real assessment? □ Yes □ No

---

## How This Connects to Other Burp Tools

```text
Target
   │
   ▼
Site Map
   │
   ▼
Select Interesting Request
   │
   ▼
Send to Repeater
   │
   ▼
Modify Request
   │
   ▼
Analyze Response
   │
   ▼
Send to Intruder (if needed)
   │
   ▼
Validate Finding
   │
   ▼
Report Vulnerability
```

---

## Key Takeaways

* The Target tool helps you understand the application before testing it.
* The Site Map provides a structured view of discovered content.
* Scope ensures you test only authorized assets.
* Good reconnaissance leads to better vulnerability discovery.
* Professional testers use the Target tool to organize their entire assessment.
