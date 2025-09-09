# Google Dorking — Information Gathering

## 🎯 Objective
Use Google Dorking techniques to identify publicly exposed information and potential vulnerabilities.

## 🛠 Tools
- Google Search
- Advanced search operators

## 🔎 Examples
1. Find login pages: inurl:login.php
2. Find exposed database files: filetype:sql site:example.com
3. Find configuration files: ext:ini OR ext:conf OR ext:env site:example.com
4. Find cameras and IoT devices: inurl:/view.shtml

## ✅ Result
- Demonstrated how sensitive files, admin panels, or devices can be indexed by Google.  
- Proved the importance of restricting search engine crawling and securing configuration files.  

## 📌 Conclusion
Google Dorking is a powerful **reconnaissance technique**.  
Mitigation steps:
- Use `robots.txt` to restrict crawling.  
- Protect sensitive files with authentication.  
- Never expose `.sql`, `.conf`, or `.env` files publicly.  
