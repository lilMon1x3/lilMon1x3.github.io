# Pickle Rick CTF — TryHackMe

## 1. Nmap Scan

nmap -sC -sV 10.10.214.204

**Results:**
- 22/tcp → SSH (OpenSSH 8.2p1, Ubuntu)
- 80/tcp → HTTP (Apache/2.4.41)
- Website title: *Rick is sup4r cool*

---

## 2. Web Enumeration
- On the main page source code, discovered a **username**: `R1ckRul3s`.
- Checked `/robots.txt` → discovered **password**: `Wubbalubbadubdub`.
- Combined credentials → successful login at `/login.php`.

---

## 3. Exploitation
- Login provided access to a restricted **Command Panel** (web shell).
- Enumeration revealed files on the web server:
  - `Sup3rS3cretPickl3Ingred.txt`
  - `clue.txt`
  - other web assets.

- Used `less` to read files (since `cat` was disabled).

---

## 4. Privilege Escalation
- Found `clue.txt` → indicated `/home/rick` directory.
- Ingredient files discovered:
  - **Ingredient #1:** `mr. meeseek hair`
  - **Ingredient #2:** `1 jerry tear`
- Checked `sudo -l` → `www-data` had `NOPASSWD: ALL`.
- Ran commands with `sudo` → accessed `/root`:
  - **Ingredient #3:** `fleeb juice`

---

## 5. Lessons Learned
- Always check **source code** and **robots.txt** — credentials often hide there.
- Web shells may restrict some commands → try alternatives (`less`, `strings`, `head`).
- `sudo -l` is critical in privilege escalation — can reveal full root access.
- Documenting step-by-step findings is key for building a professional pentest portfolio.
