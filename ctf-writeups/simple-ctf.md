# Simple CTF — TryHackMe

CMS exploitation, credential cracking, and a classic sudo misconfig
chained into root.

## Recon
`nmap` scan showed a web server plus SSH running on a non-standard
high port (2222). `whatweb` against the site only returned Apache's
default page, so ran `gobuster` to find hidden directories — turned
up the real application underneath: **CMS Made Simple**.

## Getting in
Fingerprinted the CMS version and searched for known CVEs — found
**CVE-2019-9053**, an unauthenticated blind SQL injection in CMS Made
Simple's News module. Used a public exploit script for it, which
leaked a username (`mitch`) found referenced in a CMS post.

## Foothold
Cracked the SSH password for `mitch` using Hydra against rockyou.txt,
targeting SSH on its non-standard port:

hydra -l mitch -P rockyou.txt -t 4 -s 2222 ssh://<target>
Got a hit (`mitch:secret`) and SSH'd in.

## Root
`sudo -l` showed `mitch` could run `vim` as root with no password.
Vim can spawn a shell, so:
sudo vim -c ':!/bin/sh'
dropped straight into a root shell.

**Takeaway:** a real CVE and a weak, guessable password got me in —
but it was a completely unrelated sudo misconfig (an editor, of all
things) that handed over root. Worth remembering: privesc paths
rarely match the entry vector.
