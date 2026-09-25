
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


---

### 2. SQL Injection Mitigation (Before vs. After)
* **Direct Backend Bypass (Port 8080):** Payload executes successfully, dumping all user accounts and hashes.
* **WAF Inspection (Port 443):** SafeLine identifies the SQL payload in real time and drops the connection with a 403 Forbidden intercept.

| Vulnerable: Direct Backend Access (Port 8080) | Protected: Intercepted by SafeLine (Port 443) |
|:---:|:---:|
| <img width="1600" height="780" alt="⁠05-sqli-unprotected png⁠" src="https://github.com/user-attachments/assets/b07e171e-b475-40b2-9125-4506863a29c2" />
 |  |

---

### 3. Layer 7 Rate Limiting & DoS Mitigation
Rapid request bursts trigger an automated 5-minute IP quarantine.

| Client Lockout Screen | SafeLine Event Telemetry Log |
|:---:|:---:|
| ![Rate Limit Lockout](images/07-ratelimit-lockout.png.JPG) | ![Rate Limit Log](images/08-ratelimit-waf-log.png.JPG) |

---

### 4. Zero-Trust Access Gating & IP Blacklisting<img width="1475" height="720" alt="06-sqli-waf-blocked png⁠" src="https://github.com/user-attachments/assets/e5e7a4be-4201-4a2f-bd29-ac79d140683a" />


| Client Auth Challenge | Admin Approval Modal | Custom Blacklist Hit Log |
|:---:|:---:|:---:|
| ![Client Blocked](images/10-auth-client-blocked.png.JPG) | ![Admin Modal](images/11-auth-admin-approval.png.JPG) | ![Blacklist Telemetry](images/13-blacklist-telemetry.png.JPG) |

---

### 5. SafeLine Monitoring Dashboard
Aggregated attack analytics, unique visitors, blocked request spikes, and threat telemetry.

![SafeLine Dashboard](images/01-dashboard-analytics.png.JPG)

