# Installation & Initial Setup

## Objectives

- By the end of this chapter, you will be able to:

  * Install Burp Suite
  * Understand the differences between Community and Professional editions
  * Configure Burp Suite for the first time
  * Set up a browser for proxying traffic
  * Verify that Burp Suite is intercepting HTTP and HTTPS traffic

---

## System Requirements

- Burp Suite is available for:

  * Windows
  * Linux
  * macOS

- Recommended:

  * 8 GB RAM or more
  * Java Runtime (included in modern installers)
  * Latest version of Google Chrome or Mozilla Firefox

---

## Installing Burp Suite

1. Download Burp Suite from the official PortSwigger website.
2. Choose the appropriate installer for your operating system.
3. Install using the default settings.
4. Launch Burp Suite.

---

## Community vs Professional

| Feature        | Community | Professional |
| -------------- | --------- | ------------ |
| Manual Testing | ✅         | ✅            |
| Proxy          | ✅         | ✅            |
| Repeater       | ✅         | ✅            |
| Intruder       | Limited   | Full         |
| Scanner        | ❌         | ✅            |
| Collaborator   | ❌         | ✅            |
| Extensions     | ✅         | ✅            |

- For learning web security, Community Edition is sufficient. Professional Edition is recommended for bug bounty hunting and professional penetration testing.

---

## First Launch

- When Burp Suite starts:

  * Create a temporary project for practice.
  * Use the default configuration.
  * Open the main dashboard.

- Do not worry about advanced project options yet.

---

## Browser Configuration

- To intercept traffic:

  1. Configure your browser to use Burp Suite as its proxy.
  2. Default proxy settings:

     * Host: `127.0.0.1`
     * Port: `8080`
  3. Enable interception in Burp Suite.
  4. Visit a website in your browser.
  5. Confirm that the request appears in Burp Suite.

---

## HTTPS Interception

- Modern websites use HTTPS. To inspect encrypted traffic:

  1. Install Burp Suite's CA certificate in your browser.
  2. Trust the certificate.
  3. Restart the browser if necessary.
  4. Visit an HTTPS website.
  5. Verify that traffic is visible in Burp Suite.

---

## Verifying the Setup

- Your setup is correct if:

  * HTTP requests appear in Proxy → HTTP history.
  * HTTPS requests appear without certificate warnings.
  * You can enable and disable interception.
  * Forwarding requests loads pages successfully.

---

## Common Installation Issues

| Problem                        | Possible Cause                         |
| ------------------------------ | -------------------------------------- |
| Browser cannot access websites | Incorrect proxy configuration          |
| HTTPS certificate warning      | Burp CA certificate not installed      |
| No traffic appears             | Proxy not configured correctly         |
| Port already in use            | Another application is using port 8080 |

---

## Professional Tips

* Use a dedicated browser profile for Burp Suite.
* Keep Burp Suite updated.
* Avoid installing the Burp certificate in your everyday browser profile.
* Learn keyboard shortcuts early to improve efficiency.

---

## Common Mistakes

* Forgetting to enable interception.
* Misconfiguring the browser proxy.
* Ignoring certificate installation.
* Testing outside the defined scope of an engagement.

---

## Practice Tasks

* Install Burp Suite.
* Configure a browser to use Burp Suite.
* Intercept a request to `https://example.com`.
* Forward the request successfully.
* Verify that the request appears in HTTP history.

---

## Interview Questions

1. What is the default proxy listener address and port in Burp Suite?
2. Why is the Burp CA certificate required?
3. What is the difference between Community and Professional editions?
4. How can you verify that Burp Suite is intercepting HTTPS traffic?
5. Why should you use a dedicated browser profile for Burp Suite?

---

## Summary

- You have successfully installed Burp Suite and configured it to intercept web traffic. This forms the foundation for all subsequent modules, where you'll begin using Burp Suite's tools to inspect, modify, and analyze HTTP requests and responses.
