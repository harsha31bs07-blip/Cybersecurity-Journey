# Brute It — TryHackMe

Web login brute-force leading to a leaked SSH key, cracked
passphrase, and a sudo misconfig for root.

## Recon
`nmap` showed SSH (22) and HTTP (80) open. Directory brute-forcing
found a hidden `/admin` login page.

## Getting in
Brute-forced the admin login form with Hydra:
hydra -l admin -P rockyou.txt <target> http-post-form "/admin/:user=admin&pass=^PASS^:invalid"
Cracked the panel password. Inside, found an RSA private key and a
username (`john`).

## Foothold
Extracted the key's hash with `ssh2john`, cracked the passphrase with
John against rockyou, then SSH'd in as `john` using the key.

## Root
`sudo -l` showed `john` could run `/bin/cat` as root with no
password — enough to read the root flag directly.

**Takeaway:** a weak admin panel password led to real credential
material (an SSH key), showing how a "low severity" web finding can
cascade into full system access.
