# Live Domain Security Audit & Analysis Lab

## 📌 Overview
This project documents a comprehensive security audit and analysis of a live `.uk` domain provided as a sandbox environment through a volunteering opportunity. The environment was a fully provisioned cPanel server with root access, allowing unrestricted testing and analysis.

The objective was to conduct external reconnaissance, identify security misconfigurations, assess potential vulnerabilities, and document findings from both defensive and offensive perspectives.

---

## 🎯 Objectives
- Perform external reconnaissance using OSINT techniques
- Identify security misconfigurations and potential attack vectors
- Assess SSL/TLS implementation and security headers
- Document all findings with risk assessments
- Propose remediation strategies for identified vulnerabilities

---

## 🛠️ Tools & Technologies
| Category | Tools |
|----------|-------|
| **DNS Reconnaissance** | `whois`, `nslookup`, `dig` |
| **Web Analysis** | `curl`, browser developer tools |
| **Environment** | cPanel (v132.0), Apache (2.4.66), MariaDB (10.6.25) |
| **OS** | Linux (Kernel 4.18.0) |

---

## 🔍 Initial Observations

Upon first access to the sandbox domain, several immediate issues were identified:

| Observation | Risk Level | Potential Impact |
|-------------|------------|------------------|
| Root disk at 95% usage | **High** | Database corruption, service crashes, logging failures |
| Server load 9.41 on 32 CPUs | **Low-Medium** | Potential performance degradation if usage increases |
| PhpMyAdmin accessible without proper session management | **Medium-High** | Session cookies visible in URL; potential for session hijacking |
| No 2FA on admin control panel | **Medium** | Increased risk of brute force attacks on admin credentials |

> **Note:** The disk usage issue is primarily a hosting infrastructure concern, but was documented as it could impact system stability.

---

## 🔴 External Reconnaissance

### WHOIS Lookup

whois [domain].uk
Findings:

Registrar: Namecheap, Inc. t/a Spaceship

Registration Date: 05-Jun-202x

Expiry Date: 05-Jun-202x

Name Servers: *.mysecurecloudhost.com

DNS Records Analysis
bash
nslookup [domain].uk
dig [domain].uk ANY
A Record: 77.95.xxx.xxx

Web Server Header Analysis
bash
curl -I http://[domain].uk
curl -I https://[domain].uk
Critical Finding: Missing HSTS Header

Protocol	Response	Security Issues
HTTP (port 80)	301 Moved Permanently (LiteSpeed)	No forced HTTPS enforcement
HTTPS (port 443)	HTTP/2 200 OK	No Strict-Transport-Security header
🟡 Vulnerability Assessment
Missing HSTS (Strict-Transport-Security)
Risk Level: Medium-High

Description: The domain does not implement HSTS headers, allowing potential downgrade attacks where traffic could be served over unencrypted HTTP.

Potential Attack Scenarios:

Man-in-the-Middle (MITM) attacks intercepting sensitive data

Session cookie theft leading to account impersonation

Malicious script injection via modified HTTP responses

Phishing redirection

Compliance Impact:

GDPR Article 32 requires encryption for data in transit

Potential breach of data protection obligations

Remediation:

Strict-Transport-Security: max-age=31536000; includeSubDomains; preload
PhpMyAdmin Exposure
Risk Level: Medium-High

Issue: Session identifiers visible in URL parameters; insufficient access controls on management interfaces.

Remediation:

Restrict PhpMyAdmin access by IP whitelist

Implement additional authentication layer

Configure proper session timeout and regeneration

No Multi-Factor Authentication
Risk Level: Medium

Issue: Admin control panel accessible with only username/password authentication.

Remediation: Enforce MFA for all administrative accounts.

🟢 Best Practices Identified
LiteSpeed Web Server: Modern, performant web server with good security features

301 Redirect (HTTP → HTTPS): Correctly configured to redirect HTTP traffic to HTTPS

Secure Headers Present:

X-Content-Type-Options

Appropriate cache controls (no-store, no-cache, must-revalidate)

📊 Technical Inventory
Component	Version	Notes
cPanel	132.0 (Build 26)	Up-to-date, no known critical vulnerabilities
Apache	2.4.66	Current stable version
MariaDB	10.6.25	Supported version
OS Kernel	4.18.0-477.13.1.lve.el.x86_64	CloudLinux/LVE environment
Perl	5.26.3	Standard for cPanel environment
Architecture	x86_64	Standard 64-bit
📝 Update Log
10/04/2026
Gained credentials and initial access to domain and control panel

Familiarised with website structure and cPanel interface

Initial reconnaissance and planning

13/04/2026
Comprehensive review of domain and server configuration

Documented initial findings and observations

Completed external reconnaissance (WHOIS, DNS, headers)

📋 Risk Summary Table
Finding	Severity	Impact	Remediation Priority
Missing HSTS header	High	MITM attacks, session hijacking	Immediate
PhpMyAdmin session exposure	Medium-High	Unauthorised database access	High
No MFA on admin panel	Medium	Credential brute force	Medium
High disk usage (95%)	High	Service disruption	Immediate (Infrastructure)
🔧 Recommended Remediation Actions
Enable HSTS – Implement HSTS header with max-age=31536000 and includeSubDomains

Restrict PhpMyAdmin – IP whitelist or VPN-only access

Enforce MFA – Implement for all administrative accounts

Session Security – Regenerate session IDs after authentication; use HTTP-only, Secure flags

Infrastructure – Address disk capacity issue (hosting provider)

🧠 Lessons Learned
Live domain auditing requires careful documentation of every observation, even those outside direct control (e.g., hosting issues)

External reconnaissance using native OS tools (whois, dig, curl) provides valuable intelligence without specialised software

Security headers (especially HSTS) remain commonly misconfigured even on production domains

Administrative interfaces (cPanel, PhpMyAdmin) are frequent attack targets and require additional hardening

🛠️ Tools & Commands Used
bash
# WHOIS enumeration
whois [domain].uk

# DNS enumeration
nslookup [domain].uk
dig [domain].uk ANY

# HTTP header analysis
curl -I http://[domain].uk
curl -I https://[domain].uk
📚 References
HSTS Explained (MDN)

OWASP Secure Headers Project

cPanel Security Best Practices

GDPR Article 32 - Security of Processing
