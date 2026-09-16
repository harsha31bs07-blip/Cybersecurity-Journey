# Blue — TryHackMe

Windows box vulnerable to EternalBlue (MS17-010) — the same exploit
used in the WannaCry ransomware outbreak.

## Recon
`nmap -sV -sC` showed port 445 (SMB) open. Ran Nmap's vulnscan script
(`--script smb-vuln-ms17-010`) to confirm the target was exploitable.

## Exploitation
Used Metasploit (`search eternalblue` -> `exploit/windows/smb/ms17_010_eternalblue`),
set `RHOSTS` to the target, configured a Meterpreter payload, and ran
the exploit.

## Result
MS17-010 executes in the context of the SMB service itself, so
successful exploitation typically lands a SYSTEM-level shell directly —
no separate privilege escalation needed on this box.

**Takeaway:** first Windows box and first taste of a real, historically
significant CVE — a good reminder that unpatched SMB services can be
a single-step full compromise, not just a Linux CTF pattern.
