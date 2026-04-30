# Phase 5 – Security Testing (Red vs. Blue)

## Objective
Simulate a real-world password spray attack against the domain-joined Windows 11 client, detect the attack using Windows Event Logs, and apply Group Policy hardening to mitigate future attacks.

---

## 🔴 Red Team – Attack Simulation

### 1. Network Reconnaissance (Nmap)
Discovered live hosts on the `192.168.100.0/24` network:


sudo nmap -sn 192.168.100.0/24
Result:

192.168.100.10 – Windows Server 2022 (Domain Controller)

192.168.100.20 – Windows 11 Client

192.168.100.30 – Kali Linux (attacker)

https://../Screenshots/nmap-scan.png

2. User Enumeration (enum4linux)
Attempted to enumerate domain users without credentials:


enum4linux -U 192.168.100.10
Result: Limited results due to restricted null sessions (good security control). However, we knew valid usernames: jdoe, jsmith.

3. Password Spray Attack (Hydra)
Tried a single common password (P@ssw0rd123) against multiple users via RDP:


hydra -L users.txt -p "P@ssw0rd123" rdp://192.168.100.20 -t 1
Result: Hydra reported the password as valid for jdoe, but RDP access was denied (user not in Remote Desktop Users group).

https://../Screenshots/hydra-valid.png

🔵 Blue Team – Detection
Event 4776 – Credential Validation
On the Domain Controller, Event Viewer → Security log showed:

Event ID: 4776

Status: 0x0 (success)

Target User: jdoe

Workstation: kali (IP 192.168.100.30)

This confirms the attacker successfully validated a valid domain credential.

https://../Screenshots/event-4776.png

Event 4625 – Failed Logon (if present)
Repeated failed logon attempts from the same source IP were logged, indicating a password spray.

https://../Screenshots/event-4625.png

Event 4740 – Account Lockout
After applying the lockout policy, the account locked after 5 bad attempts.

https://../Screenshots/event-4740.png

🛡️ Engineering – Hardening
Applied Account Lockout Policy (Group Policy)
Location: Default Domain Policy → Computer Configuration → Policies → Windows Settings → Security Settings → Account Policies → Account Lockout Policy

Setting	Value
Account lockout duration	30 minutes
Account lockout threshold	5 invalid attempts
Reset account lockout counter after	30 minutes
https://../Screenshots/gpo-lockout-policy.png

Verification
Manual RDP test from Windows Server to Windows 11 client:

Entered wrong password for jdoe 5 times

6th attempt immediately failed with "account is currently locked out"

✅ Policy working as expected.

📊 Outcome
Phase	Result
Attack	Credential successfully validated (jdoe) but RDP access denied
Detection	Event 4776 alerted to successful credential validation from unknown IP
Hardening	Account lockout policy prevents further password spraying
🧠 Lessons Learned
Hydra's RDP module can be unreliable – always verify with manual testing.

Event 4776 is a critical log for detecting credential validation from untrusted sources.

Account lockout policies are essential for mitigating online brute‑force attacks.

Without RDP access, an attacker might pivot to other services (SMB, WinRM).

🛠️ Tools Used
Nmap

enum4linux

Hydra

Windows Event Viewer

Group Policy Management Console

🔗 Related Phases
Phase 2 – Domain Setup

Phase 3 – Client Setup

Phase 4 – User Management


