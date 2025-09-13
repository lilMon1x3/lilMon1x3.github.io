# Pickle Rick CTF — TryHackMe

---

## TL;DR

* Lab: Pickle Rick CTF (TryHackMe).
* Chain: Recon (nmap) → Web enumeration → Credentials from source/robots → login to web panel → limited web-shell → local enumeration → `sudo` misconfiguration (NOPASSWD) → root access.
* Key artefacts: discovered web files (e.g. `Sup3rS3cretPickl3Ingred.txt`, `clue.txt`) and privileged content accessed via `sudo` as `www-data`.

---

## Environment / scope

* Attacker: Kali Linux (local).
* Target: lab VM (example masked as `10.10.214.***`).
* Tools: `nmap`, browser + view-source, `curl`, `less`/`strings`, `sudo -l`.

---

## 1) Reconnaissance

### 1.1 Nmap scan

Quick service discovery:

```
nmap -sC -sV 10.10.214.***
```

**Observed services (example):**

* `22/tcp` → SSH (OpenSSH X.YpZ)
* `80/tcp` → HTTP (Apache/2.x)
* Website title: *Rick is sup4r cool*

---

## 2) Web enumeration

### 2.1 Source and robots

* Open the site in a browser and view page source. A username was discovered in the HTML comments/source: `R1ckRul3s`.
* Check `robots.txt`:

```
curl -sS http://10.10.214.***/robots.txt
# → contains a string/password: Wubbalubbadubdub
```

### 2.2 Combine credentials

* Using the username from source and password from robots, tested login on the site (e.g. `/login.php`) — successful authentication.

---

## 3) Exploitation (web panel / limited shell)

### 3.1 Accessing the command panel

* Login provided access to a restricted Command Panel (a web-based panel capable of running some commands).
* The web shell was limited (certain commands like `cat` disabled), so alternative tools were used to read files (for example `less`, `strings`, `head`).

### 3.2 Enumerating web files

Found interesting files on the webserver:

* `Sup3rS3cretPickl3Ingred.txt`
* `clue.txt`
* Other web assets and potential clues.

Used `less` or `strings` where `cat` was unavailable:

```
less Sup3rS3cretPickl3Ingred.txt
# or
strings Sup3rS3cretPickl3Ingred.txt | less
```

---

## 4) Local enumeration & privilege escalation

### 4.1 Follow clue to local user

* `clue.txt` indicated a local directory (`/home/rick`) worth checking.
* From web-accessible files and clues, discovered ingredients and hints pointing to local user artifacts.

### 4.2 Check sudo privileges

As part of enumeration, checked `sudo -l` for the web service user (commonly `www-data`):

```
sudo -l
# example output: www-data ALL=(ALL) NOPASSWD: ALL
```

### 4.3 Abuse NOPASSWD sudo

Because `www-data` could run `sudo` without a password, the web shell / command panel was leveraged to execute privileged commands as root. For example:

```
# from web-shell context (if possible to run sudo):
sudo /bin/bash -i
# or run specific commands:
sudo cat /root/Sup3rS3cretPickl3Ingred.txt
```

This revealed additional sensitive content (e.g., `Ingredient #3: fleeb juice`) and allowed access to privileged files.

---

## 5) Lessons learned & mitigations

* **Check source & robots.txt.** Small artifacts in page source and robots often contain creds or hints in CTFs and real assessments.
* **Web-shells often limited.** If `cat` is disabled, try `less`, `strings`, `head`, redirecting, or upload minimal helpers (only in lab/with permission).
* **Sudo misconfigurations are critical.** `NOPASSWD` entries are high-value and can directly lead to full root access. Audit `/etc/sudoers` and `sudo -l` outputs.
* **Document step-by-step** — preserving commands and reasoning helps reproducibility and makes a stronger portfolio entry.

---

## 6) Cleanup & responsible handling

* Remove any web payloads or temporary files you created in the lab. Example:

```
# inspect then remove if you uploaded anything:
rm /tmp/<your-upload>.php 2>/dev/null || true
# if you modified theme files, restore them
```
---

*End of Pickle Rick write-up.*
