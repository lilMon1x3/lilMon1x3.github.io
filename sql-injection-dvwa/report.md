# SQL Injection — DVWA

## 🎯 Objective
Test DVWA (Damn Vulnerable Web Application) for SQL Injection vulnerabilities.

## 🛠 Tools
- Kali Linux
- sqlmap
- BurpSuite

## 🔎 Steps
1. Navigated to DVWA → "SQL Injection" page.
2. Tested input with `' OR '1'='1` → bypassed login/authentication.
3. Used `sqlmap` to automate analysis and extract database tables.

## ✅ Result
- Database tables dumped successfully.
- Application confirmed vulnerable to SQL Injection.

## 📌 Conclusion
The vulnerability exists due to **lack of parameterized queries**.  
Mitigation: use **prepared statements** and **input validation**.
