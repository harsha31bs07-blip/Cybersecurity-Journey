# Overpass — TryHackMe

## Recon
- Nmap scan: ports 22 (SSH), 80 (HTTP) open
- Directory brute-force revealed /admin login path

## Initial Access
- Inspected login.js — found the app only checked if a
  SessionToken cookie *existed*, not its value
- Manually set the cookie in browser devtools -> bypassed auth

## Foothold
- Admin panel exposed an encrypted SSH private key for user james
- Extracted the key hash with ssh2john, cracked passphrase via
  john + rockyou.txt
- SSH'd in as james using the key + cracked passphrase

## Privilege Escalation
- /etc/crontab: root-owned cron job running every minute:
  `curl overpass.thm/downloads/src/buildscript.sh | bash`
- /etc/hosts was world-writable -> pointed overpass.thm to
  localhost
- Hosted a malicious buildscript.sh locally, cron executed it
  as root

## Key takeaway
Client-side-only validation and world-writable system files
are both trivially exploitable — this chain relied on three
separate small misconfigurations, not one big vulnerability.
