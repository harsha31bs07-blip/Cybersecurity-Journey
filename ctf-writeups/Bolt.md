# Bolt — TryHackMe

Bolt CMS credentials leaked through blog content, exploited via a
known authenticated RCE using Metasploit.

## Recon
`nmap` showed three ports open: SSH (22) and two HTTP ports (80, 8000).
Browsing the site's blog articles revealed a username in one post and
a password in another — carelessly left in public content.

## Getting in
Logged into the Bolt CMS admin panel with the leaked credentials and
identified the CMS version (Bolt 3.7.1). Searched Exploit-DB for known
vulnerabilities and found a public authenticated RCE exploit for this
version (EDB-ID 48296).

## Exploitation
Used the matching Metasploit module instead of the raw exploit script:
use exploit/unix/webapp/bolt_authenticated_rce
set username <found username>
set password <found password>
set RHOSTS <target>
exploit
Got a shell directly — no separate privilege escalation step needed,
since the exploit lands with high enough privileges on its own.

**Takeaway:** an outdated CMS version plus credentials casually left
in public-facing content was enough for full compromise — a reminder
that content review matters as much as code review in a real
assessment.
