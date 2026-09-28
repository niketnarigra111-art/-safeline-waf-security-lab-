
# SafeLine Web Application Firewall (WAF) Home Lab

## What I Built
I deployed an Apache web server running the Damn Vulnerable Web Application (DVWA) on Ubuntu and hardened it using Chaitin SafeLine WAF configured as an inline reverse proxy. I then used a Kali Linux virtual machine to simulate real-world attacks and validate the firewall's threat mitigation, rate limiting, and access control capabilities.

## Environment & Architecture
- **Hypervisor:** VirtualBox (Isolated NAT Network: `homelab network`)
- **Attacker Machine:** Kali Linux (`10.0.2.3`)
- **Target & Security Gateway:** Ubuntu Server (`10.0.2.15`) running Apache2, MySQL, DVWA, and SafeLine WAF
- **DNS Resolution:** Local `/etc/hosts` mapping to `webserver.nik` / `www.webserver.nik`

---

## What I Configured & Tested

1. **Reverse Proxy & SSL/TLS Termination:**
   Generated custom 2048-bit self-signed X.509 certificates using OpenSSL CLI and bound SafeLine to HTTPS (port 443), proxying clean requests to DVWA on backend port 8080.
2. **OWASP Top 10 Defense (SQL Injection):**
   Tested exploitation using `' OR '1'='1`. Proved vulnerability baseline on uninspected port 8080, then verified 403 Forbidden interception when routed through the WAF.
3. **Layer 7 Rate Limiting (HTTP Flood):**
   Configured automated IP banning for clients exceeding 3 requests within 10 seconds to mitigate brute-force and application-layer DoS.
4. **Access Gating & Identity Challenge:**
   Created conditional authentication policies requiring admin verification before sensitive endpoints could be accessed from the attacker's IP.
5. **Custom Firewall Rules:**
   Configured explicit Layer 7 deny rules to blacklist malicious source IPs.

---

## Results & Technical Proof

### 1. Reverse Proxy & Upstream Mapping
SafeLine terminates TLS on port 443 and forwards traffic to the backend DVWA instance on `10.0.2.15:8080`.

| OpenSSL Certificate Generation | SafeLine Upstream Configuration |
|:---:|:---:|
|<img width="1024" height="768" alt="03-openssl-generation png⁠" src="https://github.com/user-attachments/assets/440e570b-c99f-4086-9559-52e3b6a4ec74" />|<img width="1024" height="768" alt="⁠04-reverse-proxy-config png" src="https://github.com/user-attachments/assets/4819356f-f88f-4cd4-ba9b-33616414a74d" />|


---

### 2. SQL Injection Mitigation (Before vs. After)
* **Direct Backend Bypass (Port 8080):** Payload executes successfully, dumping all user accounts and hashes.
* **WAF Inspection (Port 443):** SafeLine identifies the SQL payload in real time and drops the connection with a 403 Forbidden intercept.

| Vulnerable: Direct Backend Access (Port 8080) | Protected: Intercepted by SafeLine (Port 443) |
|:---:|:---:|
| <img width="1600" height="780" alt="⁠05-sqli-unprotected png⁠" src="https://github.com/user-attachments/assets/b07e171e-b475-40b2-9125-4506863a29c2" /> |<img width="1475" height="720" alt="06-sqli-waf-blocked png⁠" src="https://github.com/user-attachments/assets/ea0aa370-22d4-447b-8f51-facdfb97768c" />|

---

### 3. Layer 7 Rate Limiting & DoS Mitigation
Rapid request bursts trigger an automated 5-minute IP quarantine.

| Client Lockout Screen | SafeLine Event Telemetry Log |
|:---:|:---:|
| <img width="1600" height="780" alt="07-ratelimit-lockout png⁠" src="https://github.com/user-attachments/assets/176ebc43-c8da-4a6a-ace1-c06b08ced2b3" />| <img width="1475" height="720" alt="⁠08-ratelimit-waf-log png" src="https://github.com/user-attachments/assets/3d1bf683-0f7d-4a8a-a4ca-200cdc3cb47a" />|

---
### 4. Zero-Trust Access Gating & IP Blacklisting

| Client Auth Challenge | Admin Approval Modal | Custom Blacklist Hit Log |
|:---:|:---:|:---:|
| <img width="900" height="780" alt="10-auth-client-blocked png" src="https://github.com/user-attachments/assets/3eab9441-e56d-45c1-bd19-a06c221062bb" /> | <img width="900" height="780" alt="11-auth-admin-approval png" src="https://github.com/user-attachments/assets/8ce635a8-7438-4c8f-9fc0-9a7ec7b8ea76" /> | <img width="900" height="780" alt="13-blacklist-telemetry png" src="https://github.com/user-attachments/assets/58828207-e21a-4a4e-904c-cb1d87e9a1db" /> |


### 5. SafeLine Monitoring Dashboard
Aggregated attack analytics, unique visitors, blocked request spikes, and threat telemetry.

<img width="1600" height="780" alt="⁠01-dashboard-analytics png⁠" src="https://github.com/user-attachments/assets/1261d0a9-454f-4eab-9f9c-e70a1c335040" />

---

## 📚 References & Acknowledgments

* **Tutorial & Project Guide:** 
  * [EASY CYBERSECURITY Home Lab to get you HIRED - SafeLine Web Application Firewall](https://youtu.be/N0dEC1nuWCQ) by *The Social Dork | Cyber Security*
* **Security Technologies & Documentation:**
  * [SafeLine WAF Documentation](https://waf-ce.chaitin.cn/en/) - Chaitin SafeLine Community Edition architecture, reverse proxy setup, and rule configurations
  * [Damn Vulnerable Web Application (DVWA)](https://github.com/digininja/DVWA) - Vulnerable PHP/MySQL web application for offensive and defensive security exercises
  * [OWASP Top 10: A03:2021 – Injection](https://owasp.org/Top10/A03_2021-Injection/) - Industry vulnerability classification and mitigation standards for SQL Injection (SQLi)
* **Underlying Platforms:**
  * [Oracle VM VirtualBox](https://www.virtualbox.org/) - Hypervisor and virtual networking
  * [Kali Linux](https://www.kali.org/) - Penetration testing distribution
  * [Ubuntu Server](https://ubuntu.com/server) - Host OS for Apache2, PHP, MySQL, and SafeLine Docker containers
  * [OpenSSL](https://www.openssl.org/) - Cryptographic toolkit for X.509 certificate generation

