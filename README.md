# Cybersecurity Incident Report: Network Traffic Analysis

## 📌 Scenario Overview
As a Cybersecurity Analyst, I investigated an incident where multiple clients were unable to access the company's website (`www.yummyrecipesforme.com`). Users encountered a "Destination port unreachable" error. To resolve the issue, network traffic was captured and analyzed using the command-line packet analyzer tool **tcpdump**.

---

## 🔍 Part 1: Network Log Analysis Summary
Below is the captured network log showing the interaction between the analyst's machine and the server:

![Network Log](image_90b920.jpg)

### Key Observations from the Log:
* **The UDP protocol reveals that:** The analyzer's browser sent outbound DNS queries from the source IP `192.51.100.15` to the destination DNS server `192.51.100.15` (mapped to `203.0.113.2.domain`) on port 53. The browser requested an **A record** (indicated by `A?`) to resolve the domain name `www.yummyrecipesforme.com`.
* **ICMP Error Response:** Based on the results, the ICMP echo reply returned the error message: `ICMP 203.0.113.2 udp port 53 unreachable length 254`. This exact delivery failure message was repeated three times in the log at timestamps `13:24:32`, `13:26:32`, and `13:28:32`.
* **Port Significance:** Port **53** noted in the error message is dedicated to **DNS (Domain Name System)** services, which translate human-readable domain names into IP addresses.
* **The Most Likely Issue:** A critical failure in the DNS resolution process. Because the destination server sent an ICMP "unreachable" reply, it indicates that no active service was listening on port 53 to handle the incoming requests.

---

## 🛠️ Part 2: Detailed Analysis & Suspected Root Cause

* **Time Incident Occurred:** The logs show the active failures taking place between **13:24:32** and **13:28:50**.
* **Incident Detection:** The IT department became aware after several clients reported prolonged page load times resulting in a "Destination port unreachable" error.
* **Investigation Actions Taken:** The security analyst reproduced the issue, confirmed the error, and launched **tcpdump** to capture and analyze live network packets during a page reload attempt.
* **Key Investigation Findings:** Port 53 on the DNS server (`203.0.113.2`) is completely unreachable. 

### 🚨 Suspected Root Causes:
1. **Service Crash:** The DNS service (e.g., BIND or alternative DNS software) on the server has stopped running or crashed.
2. **Firewall Misconfiguration:** A firewall rule might be blocking or dropping traffic on UDP port 53, forcing the server to send an ICMP unreachable message.
3. **Denial of Service (DoS):** The server might be under an active attack that crashed the specific network service.

### 📋 Recommended Next Steps:
* Log into the DNS server (`203.0.113.2`) and verify the status of the DNS daemon/service. Restart the service if it's down.
* Audit the server's firewall configuration to ensure port 53 is open for both UDP and TCP traffic.
* Check system error logs (`/var/log/`) on the server to identify if a crash or malicious activity caused the disruption.

---
_Note: IP addresses and scenarios used in this lab are for educational purposes and document simulation (RFC 5737 compliant)._
