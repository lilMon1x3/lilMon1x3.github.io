# Google Dorking — Information Gathering

> Note: This document demonstrates Google Dorking as a reconnaissance technique for educational purposes. Do not use these techniques for unauthorized scanning or information theft.

---

## TL;DR

* Objective: Use advanced Google search operators ("dorks") to discover publicly exposed information, configuration files, admin panels, and potentially vulnerable endpoints.
* Outcome: Demonstrated how sensitive files, admin panels, and devices can be indexed by search engines and why this is a significant reconnaissance vector.

---

## Environment / scope

* Tools: Google Search (advanced operators), browser.
* Use-case: Open-source intelligence (OSINT) and reconnaissance during security assessments.

---

## Examples of useful dorks

**Common operators and examples:**

```text
# find login pages
inurl:login.php

# find exposed SQL dumps on a domain
filetype:sql site:example.com

# find configuration files
ext:ini OR ext:conf OR ext:env site:example.com

# find camera/web interface pages
inurl:/view.shtml
```

**Notes:**

* Combine operators to narrow results (e.g., `site:example.com inurl:admin ext:php`).
* Be cautious and respect robots and site terms of service.

---

## Results / impact

* Google Dorking can reveal accidental exposures of sensitive files (backups, DB dumps), admin interfaces, and IoT devices.
* The technique is valuable for red teams and penetration testers during information gathering and for defenders to validate what their organization unintentionally exposes.

---

## Mitigation & best practices

* Use `robots.txt` to guide crawlers (do not rely on it as a security control).
* Ensure all sensitive files are access-controlled and not publicly accessible.
* Remove or restrict indexing of staging/dev sites and backups.
* Monitor for public exposures using automated tools and alerts.

---

*End of report.*
