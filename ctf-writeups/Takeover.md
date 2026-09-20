# Takeover — TryHackMe

Subdomain enumeration challenge — no exploitation, just careful
reconnaissance to find what a fictional company's attackers could
actually take over.

## Recon
Added the target domain (`futurevera.thm`) to `/etc/hosts` pointing
at the target IP. Nmap showed SSH, HTTP, and HTTPS open, nothing else
interesting on the main site itself.

## Subdomain enumeration
Used ffuf to brute-force virtual hosts against the domain:
ffuf -H "Host: FUZZ.futurevera.thm" -u https://<target-ip>
-w /usr/share/seclists/Discovery/DNS/subdomains-top1million-*.txt
-fs <baseline-size>
This surfaced hidden subdomains (e.g. `blog.`, `support.`) not linked
anywhere on the main site. Added each discovered subdomain to
`/etc/hosts` to actually browse them.

## Finding the answer
The key info wasn't from brute-forcing alone — inspecting the SSL
certificate of one subdomain revealed additional DNS names, pointing
toward what was actually vulnerable to takeover.

**Takeaway:** no exploit here — this one's a reminder that
reconnaissance itself is a skill. Subdomain takeover vulnerabilities
are a real, common bug-bounty finding: forgotten subdomains still
pointing at external services (like an unclaimed cloud resource) that
an attacker can register and hijack.
