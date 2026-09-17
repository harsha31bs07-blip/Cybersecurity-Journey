# Bounty Hacker — TryHackMe

FTP anonymous access leaking credentials, chained into an SSH
brute-force and a sudo misconfig for root.

## Recon
`nmap` showed FTP (21), SSH (22), and HTTP (80) open.

## Getting in
FTP allowed anonymous login. Inside, found two files: a task list
(`task.txt`, signed by a user named `lin`) and a password wordlist
(`locks.txt`) — clearly meant for brute-forcing SSH.

## Foothold
Ran Hydra against SSH using the username from the task file and the
wordlist found on FTP:

hydra -l lin -P locks.txt ssh://<target>
Got a hit and SSH'd in as `lin`.

## Root
`sudo -l` showed `lin` could run `tar` as root with no password.
Used the classic checkpoint-action trick to spawn a shell:
sudo tar -cf /dev/null /dev/null --checkpoint=1 --checkpoint-action=exec=/bin/sh
Landed a root shell.

**Takeaway:** anonymous FTP access is a surprisingly common real-world
finding, and it directly handed over the exact wordlist needed for
the next step — a good reminder to always check FTP for anonymous
login early in recon.
