# Incident Response Case Study: Live Phishing & Data Exfiltration Analysis

## 🛡️ Project Overview
This project documents a high-pressure incident response exercise conducted within a **Live SOC Simulator**. I successfully triaged and mitigated a multi-stage phishing attack as it unfolded across a corporate network, identifying the full attack chain from initial access to data exfiltration.

**Scenario:** Phishing Unfolding (TryHackMe SOC Simulator)  
**Status:** Victory - Security Breach Prevented!  
**Triage Accuracy:** 100% (True Positives and False Positives)  
**Total Alerts Processed:** 35

---

## 📊 Performance Metrics
| Metric | Value |
| :--- | :--- |
| **Mean Time to Resolve (MTTR)** | 2.0 Minutes |
| **Mean Dwell Time** | 16.0 Minutes |
| **TP Identification Rate** | 100% |
| **FP Identification Rate** | 100% |

---

## 🕵️ Incident Analysis & Attack Chain

### 1. Initial Access & Execution (T1566 / T1059)
The attack initiated via a phishing payload. A malicious PowerShell script was identified being created and executed in the user's Downloads folder.
* **Indicator:** File creation of `PowerView.ps1` in `C:\Users\michael.ascot\Downloads\`.
* **Evidence:** `powershell.exe` (PID 3728) acting as the primary execution vehicle.

### 2. Reconnaissance & Lateral Movement (T1087 / T1021)
Using **PowerView**, the adversary enumerated the network and identified high-value targets. 
* **Lateral Pivot:** The attacker utilized `net.exe` to map a network drive to a sensitive financial share: `\\FILESRV-01\SSF-FinancialRecords`.
* **Artifact:** `net.exe use Z: \\FILESRV-01\SSF-FinancialRecords`.

### 3. Data Collection & Staging (T1074)
The attacker moved into the collection phase by bulk-copying data from the file server to a local staging directory.
* **Mechanism:** Abuse of `Robocopy.exe`.
* **Command:** `robocopy.exe Z:\ C:\Users\michael.ascot\downloads\exfiltration /E`.

### 4. Exfiltration via DNS Tunneling (T1048)
The most sophisticated part of the attack involved tunneling stolen data inside DNS queries to bypass network filters.
* **Technique:** High-entropy Base64 strings sent as subdomains to `haz4rdw4re.io`.
* **Decoded Payloads:** Metadata for `InvestorPresentation.pptx`, `ClientPortfolio.zip`, and `Summary.xlsx`.

### 5. Defense Evasion (T1070)
Post-exfiltration, the attacker attempted to remove the drive mapping to hide the path of lateral movement.
* **Command:** `net use Z: /delete`.

---

## 🛡️ Remediation & Response Actions
1. **Host Isolation:** Isolated `WIN-3450` via EDR to sever the Command & Control (C2) channel.
2. **Network Mitigation:** Sinkholed the malicious domain `haz4rdw4re.io` at the enterprise DNS level.
3. **Identity Security:** Revoked active tokens and initiated a mandatory password reset for the `michael.ascot` account.
4. **Forensics:** Captured the staging directory `\downloads\exfiltration\` for a full data impact assessment.

---

## 📝 Lessons Learned
* **LotL Binary Vigilance:** This case highlighted how attackers abuse legitimate tools like `nslookup` and `robocopy` to blend in.
* **Reporting Granularity:** Improved documentation by strictly mapping the "When" and "Where" to provide better forensic context.
* **Efficiency:** Maintained a 2-minute MTTR while handling a high volume of noise/false positive spam.

---
*This write-up is part of my Cybersecurity Portfolio. For more information or collaboration, feel free to connect with me.*
