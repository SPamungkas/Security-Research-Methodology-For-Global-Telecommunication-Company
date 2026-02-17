# 🌐 Infrastructure Reconnaissance: Global Telecommunication Sector
Security Research &amp; Offensive Security Portfolio. Focused on web infrastructure reconnaissance and vulnerability analysis following OWASP WSTG methodologies.

## 1. 🛡️ Project Overview

This repository documents a systematic **Vulnerability Disclosure Program (VDP)** engagement conducted against a global telecommunications infrastructure. The primary objective was to perform a non-intrusive security assessment to map the external attack surface and evaluate the organization's public security posture.

### 🎯 Key Focus Areas
* **Attack Surface Management:** Identifying live assets and dormant subdomains.
* **Security Control Evaluation:** Analyzing the effectiveness of Web Application Firewalls (WAF) and Intrusion Prevention Systems (IPS).
* **Public Data Exposure:** Scrutinizing publicly indexed documents for potential sensitive information leakage.

---

### ⚠️ Ethical Compliance & Rules of Engagement
> [!IMPORTANT]
> **Responsible Disclosure Statement:** > This research was conducted under a strict ethical framework. No service disruption, unauthorized data modification, or exploitation of identified vulnerabilities took place. All findings are intended for defensive enhancement and coordinated disclosure.

* ✅ **Non-Intrusive:** No brute-force or destructive payloads were used.
* ✅ **Data Privacy:** Respecting the organization's Terms of Service and data boundaries.
* ✅ **Professionalism:** All activities followed industry-standard Rules of Engagement.

## 2. 📑 Methodology & Compliance

This research strictly adheres to the **OWASP Web Security Testing Guide (WSTG) 4.2**, ensuring a comprehensive and industry-standard approach to security testing.

<div align="center">
  
| Phase | Category | Activity Description |
| :---: | :--- | :--- |
| 🔍 | **WSTG-INFO-01** | **Information Gathering:** Systematic subdomain discovery and asset enumeration. |
| 🕵️‍♂️ | **WSTG-INFO-02** | **Search Engine Discovery:** Leveraging Google Dorking to find hidden endpoints. |
| 📂 | **WSTG-INFO-05** | **Metadata Analysis:** Inspecting publicly available documents for sensitive info. |
| 🌐 | **WSTG-INFO-08** | **Infrastructure Mapping:** Analyzing network responses and defensive layers. |

</div>

### 🛠️ Tooling & Stack
To maintain precision and minimize false positives, the following industry-standard tools were utilized:
* **Enumeration:** `Subfinder`, `Assetfinder`
* **Analysis:** `Httpx`, `WhatWeb`
* **Scanning:** `Nmap` (Service Version Detection)
* **Fuzzing:** `FFUF` (Directory & Parameter Discovery)

## 3. 🚀 Reconnaissance Workflow & Technical Analysis

The reconnaissance process was executed in two strategic stages: **Passive Information Gathering** and **Active Infrastructure Profiling**.

### 🔍 Phase A: Intelligent Asset Enumeration
The objective was to identify the organization's public-facing footprint.
* **Vertical Enumeration:** Leveraged `Subfinder` and `Assetfinder` to discover subdomains. Over **50+ live assets** were identified, ranging from primary marketing sites to internal API gateways.
* **Filtering & Validation:** Used `httpx` to probe for live web services (ports 80, 443). This process filtered out "ghost" subdomains and focused the assessment on reachable attack surfaces.
* **Architecture Fingerprinting:** Utilizing `WhatWeb`, I identified a modern tech stack consisting of **Sitecore CMS** and **Vue.js**. This helped in understanding the potential attack vectors specific to these frameworks.

### 🛡️ Phase B: Defensive Mechanism Analysis (The Honeypot Case)
During the active scanning of specific IP ranges hosted on **Google Cloud Platform (GCP)**, a sophisticated defensive layer was encountered.

* **The Observation:** An `Nmap` service scan returned an unusually high number of "open" ports (ranging from legacy ports like 23/Telnet to high-range ports).
* **Technical Deep-Dive:**
    * **Status `tcpwrapped`:** Most ports completed the TCP handshake but immediately terminated the session.
    * **Tarpitting Behavior:** Response times were artificially inflated, a classic sign of a **Tarpit** designed to exhaust the attacker's resources.
* **Conclusion:** This infrastructure implements **Deception Technology (Port Spoofing)**. 
* **Strategic Adjustment:** Recognizing this as a **Honeypot**, the testing was adjusted to avoid these IP ranges, preventing "False Positives" and evading the organization's automated blocking systems (IPS).

<div align="center">
  
#### 📊 Evidence: Nmap Scan Analysis (Deception Detection)
| Port | State | Service | Reason | Observation/Analysis |
| :--- | :--- | :--- | :--- | :--- |
| 21/tcp | open | ftp | tcpwrapped | Initial handshake success, immediate termination. |
| 23/tcp | open | telnet | tcpwrapped | Deception port used to lure automated brute-force tools. |
| 80/tcp | open | http | tcpwrapped | Possible tarpit to slow down directory fuzzing. |
| 110/tcp | open | pop3 | tcpwrapped | Non-standard for cloud infrastructure; indicates spoofing. |
| 443/tcp | open | https | tcpwrapped | Security control intercepting the request. |

</div>

> [!NOTE]
> **Technical Note:** The uniform `tcpwrapped` status across disparate services (FTP, Telnet, HTTP) on a Google Cloud IP is a high-confidence indicator of a **Low-Interaction Honeypot**.

## 4. 📂 Public Data Exposure (OSINT Analysis)

During the **WSTG-INFO-05** phase (Analysis of Web Metadata), I conducted a targeted OSINT investigation using advanced Google Dorking techniques to identify sensitive files indexed by search engines.

### 🔍 Discovery: Information Leakage via Metadata
The investigation uncovered several internally-classified documents (e.g., **"Strategic Infrastructure Handbooks"**) that were publicly accessible due to improper indexing or misconfigured directory permissions.

| File Type | Category | Potential Impact |
| :--- | :--- | :--- |
| **PDF** | **Technical Infrastructure Guide** | Exposure of internal network naming conventions and deployment standards. |
| **PDF** | **Internal Organizational Chart** | Leakage of project stakeholders, internal stakeholders name, and department hierarchies. |

Note: For ethical reasons and data privacy compliance, no copies of the discovered documents were retained, and sensitive details have been redacted from this report.

### ⚠️ Risk Assessment: The Social Engineering Vector
While these documents do not provide a direct "RCE" or "SQLi" exploit, they are highly valuable for the **Weaponization** phase of a sophisticated attack:

1. **Institutional Intelligence:** The documents reveal key personnel names and internal approval workflows.
2. **Spear Phishing:** An attacker could use the identified technical jargon and stakeholder names to craft highly convincing phishing emails.
3. **Internal Footprinting:** Technical handbooks often contain naming conventions for internal servers or private API endpoints, aiding in lateral movement planning.

> [!WARNING]
> **Security Recommendation:** Organizations should implement strict **Metadata Scrubbing** policies and ensure that `robots.txt` or `X-Robots-Tag` headers are correctly configured to prevent sensitive internal documents from being crawled by public search engines.

## 5. 🛡️ Security Recommendations

Based on the reconnaissance findings, the following recommendations are proposed to enhance the organization's security posture, aligned with **OWASP Best Practices**:

### A. Mitigation for Information Leakage (WSTG-INFO-05)
* **Metadata Scrubbing:** Implement automated tools to strip metadata from all public-facing documents before they are uploaded to the production environment.
* **Indexing Control:** Ensure that sensitive directories are protected via `robots.txt` (Disallow) and use `X-Robots-Tag: noindex` headers on sensitive PDF files to prevent search engine indexing.
* **Access Control Review:** Audit public storage buckets or document repositories to ensure the "Principle of Least Privilege" is applied.

### B. Attack Surface Management
* **Continuous Asset Discovery:** Regularly perform automated subdomain enumeration to identify and decommission "Shadow IT" or forgotten staging environments.
* **Egress Filtering:** While the current Deception (Honeypot) layers are effective, continuous monitoring of outbound traffic from these assets is recommended to identify potential lateral movement attempts.

> [!TIP]
> **Technical References (OWASP Cheat Sheet Series):**
> * **Attack Surface Analysis:** [Guidance on identifying and mapping digital assets to reduce the organizational risk profile.](https://cheatsheetseries.owasp.org/cheatsheets/Attack_Surface_Analysis_Cheat_Sheet.html)
> * **User Privacy Protection:** [Standards for preventing accidental exposure of sensitive information and metadata leakage.](https://cheatsheetseries.owasp.org/cheatsheets/User_Privacy_Protection_Cheat_Sheet.html)

## 6. 🏁 Conclusion

This reconnaissance project against a Global Telecommunication infrastructure successfully demonstrates that effective cybersecurity starts with comprehensive **Information Gathering** and **Asset Mapping**. 

### 🔑 Key Takeaways:
* **Analytical Accuracy:** By identifying **Deception Mechanisms (Honeypots)** early, I avoided false-positive reports and adapted the testing strategy to be more efficient.
* **OSINT Significance:** The discovery of publicly indexed internal documents proves that technical vulnerabilities aren't the only risk; **Information Leakage** is a critical factor in the "Weaponization" phase of an attack.
* **Ethical Standards:** This engagement was conducted under a strict "Non-Intrusive" framework, ensuring zero disruption to the organization's infrastructure while providing valuable security insights.


---
*Developed by **Satria** | Junior Pentester & Security Researcher*
