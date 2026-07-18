# Installation and First Launch

> **Module Progress**

```text id="h7sppv"
█░░░░░░░░ 1 / 9 Lessons
```

- A reliable Burp Suite environment begins with a clean installation and a basic understanding of what happens when Burp starts.

- The objective of this lesson is not simply to install the application.

- You should understand:

  * Where Burp comes from.
  * Which installation method you are using.
  * What happens during startup.
  * What projects and configurations represent.
  * How to confirm that Burp itself is running correctly before configuring traffic interception.

---

## Download From the Official Source

- Burp Suite should be obtained from the official PortSwigger distribution.

- Avoid downloading installers from:

  * Unofficial mirrors.
  * File-sharing websites.
  * Random software repositories.
  * Modified third-party packages.

- Security tools operate on sensitive application traffic.

- The integrity of the software matters.

---

## Choose the Correct Platform

- Burp Suite supports major desktop operating systems, including:

  ```text id="amzq4j"  
  Windows

  Linux

  macOS
  ```

- Choose the installer appropriate for your operating system and architecture.

- Modern official installers generally provide the runtime components needed for normal operation.

---

## Installer vs JAR

- You may encounter Burp distributed through different formats.

- Conceptually:

  ```text id="uxt2rk"
  Native Installer

  ↓
  
  Simpler Desktop Installation

  ↓

  Integrated Runtime / Launcher
  ```

or:

  ```text id="mv3e52"
  JAR Distribution

  ↓

  Requires Compatible Java Environment

  ↓
  
  Manual Launch Control
  ```

- For most beginners, the official platform installer provides the simplest setup.

- Understanding the JAR approach becomes useful when working with custom environments or troubleshooting Java-related setups.

---

## Installation Principle

- During a normal beginner installation, default settings are usually sufficient.

- The basic workflow is:

  ```text id="c4u08e"
  Download Official Installer

  ↓

  Verify Correct Platform

  ↓

  Install Burp Suite

  ↓

  Launch Application

  ↓
  
  Confirm Startup
  ```

- Do not change advanced settings without understanding why they need to change.

---

## First Launch

- When Burp starts, you may encounter project and configuration options before reaching the main workspace.

- At this stage, understand two separate concepts:

  ```text id="c1etmn"
  Project

  =

  Assessment Workspace / Stored State
  ```

and:

  ```text id="t3o4df"
  Configuration

  =

  How Burp Behaves
  ```

- These concepts will be explored in the next lesson.

---

## Temporary Project

- For early learning sessions, a temporary project is often sufficient when available for your edition and workflow.

- Conceptually:

  ```text id="2uyij3"
  Launch Burp

  ↓

  Temporary Project

  ↓

  Default Configuration

  ↓

  Start Burp
  ```

- This allows you to begin learning without worrying about persistent project management.

---

## Default Configuration

- When starting out, use Burp's default configuration unless a lesson specifically requires a change.

- Why?

  - Because troubleshooting becomes easier when you know the environment began from a predictable state.

- Avoid the beginner pattern:

  ```text id="k5oblv"
  Install Burp

  ↓

  Change Many Settings

  ↓

  Something Breaks

  ↓

  Unknown Cause
  ```

- Prefer:

  ```text id="mvnv8h"
  Install Burp

  ↓

  Use Known Defaults

  ↓

  Verify Basic Functionality

  ↓

  Change One Setting When Needed

  ↓

  Verify Again
  ```

- Controlled configuration changes follow the same reasoning as controlled security tests.

---

## Understanding the Main Workspace

- After startup, Burp presents its main interface containing different tools and views.

- Depending on the edition and version, the exact interface may evolve.

- You will encounter capabilities related to areas such as:

  * Dashboard
  * Target
  * Proxy
  * Repeater
  * Intruder
  * Other testing and analysis tools

- Do not try to learn every interface element immediately.

- At this stage, your objective is simply:

  > **Confirm that Burp starts correctly and that you can access the main workspace.**

---

## Do Not Configure the Browser Yet

- A common mistake is trying to troubleshoot everything simultaneously.

- Separate setup into stages:

  ```text id="e8j8hc"
  Stage 1

  Does Burp Launch Correctly?

  ↓

  Stage 2

  Is the Proxy Listener Working?

  ↓

  Stage 3

  Is the Browser Using Burp?

  ↓

  Stage 4

  Does HTTP Work?

  ↓

  Stage 5

  Does HTTPS Work?
  ```

- This layered approach makes troubleshooting much easier.

---

## Basic Installation Verification

- Before configuring interception, verify:

  ```text id="txb4vz"
  [ ] Burp launches without unexpected errors.

  [ ] The main interface appears.

  [ ] A project can be started.

  [ ] Burp remains running normally.
  
  [ ] Core tool tabs or navigation are accessible.
  ```

- If these conditions are met, the application itself is probably installed correctly.

- The next task is network configuration.

---

## Keep Burp Updated

- Burp Suite evolves frequently.

- Updates may include:

  * Security fixes.
  * New capabilities.
  * Scanner improvements.
  * Interface changes.
  * Protocol support improvements.
  * Bug fixes.

- For a security testing tool, keeping the application current is particularly important.

---

## Version Differences

- Screenshots and menu locations in tutorials may differ from your installation.

- Do not assume:

  > "My Burp is broken because the interface looks different."

- Instead identify the underlying capability being discussed.

- Interfaces change.

- Concepts are more durable.

---

## Installation vs Configuration Problems

- Learn to separate these categories.

### Installation Problem

- Examples:

  ```text id="ql1edb"
  Burp does not start

  Application crashes

  Runtime problem

  Installer failure
  ```

### Configuration Problem

- Examples:

  ```text id="8gvw6w"
  Browser cannot connect

  No requests appear

  HTTPS certificate warnings

  Proxy listener mismatch
  ```

- If Burp launches normally but browser traffic does not work, reinstalling Burp is usually not the first troubleshooting step.

- Investigate configuration first.

---

## Security Considerations

- Burp can handle sensitive traffic, including:

  * Authentication cookies.
  * Authorization headers.
  * API tokens.
  * Form submissions.
  * Application data.

- Treat your Burp environment as a security-sensitive workspace.

- Avoid:

  * Sharing project data carelessly.
  * Exposing captured credentials.
  * Using unknown third-party builds.
  * Leaving sensitive assessment data unprotected.

---

## Professional Habit — Record Your Environment

- For repeatable lab or assessment setups, know:

  ```text id="3x9v3f"
  Burp Edition:

  Burp Version:
  
  Operating System:

  Browser:

  Proxy Address:
  
  Proxy Port:

  Project Type:

  Special Configuration:
  ```

- This information can significantly simplify troubleshooting.

---

## Installation Challenge

- Suppose:

  * Burp launches correctly.
  * The Dashboard opens.
  * Repeater is accessible.
  * Your browser cannot load websites after you configure a proxy.

- Should you reinstall Burp immediately?

- Probably not.

- You have already established that:

  ```text id="7tx9ns"
  Burp Installation

  ↓

  Appears Functional
  ```

- The problem is more likely in the next layers:

  ```text id="6esosn"
  Proxy Listener

  Browser Proxy Configuration

  HTTPS Trust
  
  Network Configuration
  ```

- Troubleshoot the failing layer rather than restarting from zero.

---

## Key Takeaways

* Obtain Burp Suite from the official distribution.
* Use the correct installer for your environment.
* Default installation settings are usually sufficient for beginners.
* Projects and configurations are separate concepts.
* Start with a known default configuration.
* Verify Burp itself before configuring browser traffic.
* Troubleshoot installation and configuration as separate layers.
* Interface details may change between versions.
* Keep Burp updated.
* Treat captured Burp data as security-sensitive.
