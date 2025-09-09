# XSS (Cross-Site Scripting) — DVWA

## 🎯 Objective
Identify XSS vulnerability in DVWA and demonstrate possible impact.

## 🛠 Tools
- Kali Linux
- BurpSuite
- Web browser

## 🔎 Steps
1. Opened DVWA → "XSS (Reflected)" section.
2. Injected `<script>alert("XSS")</script>` into input field.
3. Observed JavaScript execution in the browser.

## ✅ Result
- Successfully executed custom JavaScript in victim’s browser.
- Vulnerability confirmed.

## 📌 Conclusion
The application is vulnerable to **reflected XSS**.  
Mitigation: implement **output encoding** and **input sanitization**.
