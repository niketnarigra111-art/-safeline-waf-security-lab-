¡

# SafeLine Web Application Firewall (WAF) Home Lab

## What I Built
I deployed an Apache web server running the Damn Vulnerable Web Application (DVWA) on Ubuntu, and protected it using SafeLine WAF configured as a reverse proxy. I then used a Kali Linux machine to simulate attacks and verify the firewall's defense mechanisms.

## Tools & Virtual Machines
- **Hypervisor:** VirtualBox (Bridged Networking)
- **Attacker Machine:** Kali Linux
- **Target Machine:** Ubuntu running Apache2, MySQL, and DVWA
- **Firewall:** SafeLine WAF

## What I Configured & Tested
1. **HTTPS / SSL:** Generated self-signed OpenSSL certificates so traffic to the website is encrypted.
2. **Rate Limiting (HTTP Flood):** Configured SafeLine to temporarily ban any IP address making more than 3 requests in 10 seconds.
3. **SQL Injection Defense:** Set SafeLine to detect and drop SQL injection queries targeting the backend database.

## Results & Proof

### 1. Attack Blocked by WAF
(Drag and drop your screenshot of the blocked attack here)

### 2. SafeLine Monitoring Dashboard
(Drag and drop your screenshot of the dashboard here)
