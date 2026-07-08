# IR-2026-001-RDP-BruteForce.md

## Incident Response Report

### RDP Brute Froce Attack - DC-01

- Report Date: 2026-07-08
- Analyst: Efe Ozel
- Incident ID: IR-2026-001
- Threat: High
- Status: Closed
- Environment: Home SOC Lab - soclab.local

### 1. Incident Summary

On 8 July 2026, an automated brute-force attack targeting the RDP service on Domain Controller DC-01 (10.10.10.10) was detected in the SOC Lab environment, originating from a Kali Linux machine (10.10.10.4). The attack was detected via an abnormal surge in EventCode 4625 (Failed Login) logs on the Splunk SIEM.

Detected, before the attack was network scan happened from the same source IP

### 2. Affected Systems

→ DC-01, 10.10.10.10, Domain Controller, Target

→ Kali Linux, 10.10.10.4, Attacker Machine, Source

### 3. Timeline

2026-07-08 16:50  - Nmap scan DC-01 from Kali

2026-07-08 16:53 - RDP Brute Force attack happened

2026-07-08 16:53:50 - First EventCode 4625 Log has been created

2026-07-08 16:53:59 - The most recent EventCode 4625 log entry

2026-07-08 16:57 - Anamoly Detected on the Splunk

### 4. Technical Findings

4.1 Discovert Attempt - Nmap Scan

Splunk Query:

```jsx
index=wineventlog EventCode=3
| stats count by SourceIp, DestinationIp
| sort -count
```

Findings:

SOURCE IP               DESTINATION OP             CONNECTION COUNT

10.10.10.4 (Kali)        10.10.10.10 (DC-01)           201

10.10.10.3 (WIN11)   10.10.10.10 (DC-01)           16

Detected Open Ports:

| Port | Service | Risk Level |
| --- | --- | --- |
| 445 | SMB | High |
| 3389 | RDP | High |
| 5985 | WinRM | High |
| 464 | Kerberos | Medium |
| 5357 | WS-Discovery | Low |
|  |  |  |

The fact that 201 connection attempts were made to different ports within a short period of time indicates that an automated scanning tool was used.

4.2 Access Attempt - RDP Brute Force

Splunk Query:

```jsx
index=wineventlog EventCode=4625
| stats count by Account_Name, Source_Network_Address, host
| sort -count
```

Findings:

ACCOUNT                  SOURCE IP           DESTINATION      ATTEMPT COUNT

Administrator             10.10.10.4            DC-01                   264

### 5. MITRE ATT&CK Mapping

| Tactic | Technique | ID | Desc |
| --- | --- | --- | --- |
| Reconnaissance | Active Scanning | T1595 | Network Scan with Nmap |
| Discovery | Network Service Scanning | T1046 | Detection of open ports and services |
| Credential Access | Brute Force: Password Guessing | T1110.001 | Password guessing via RDP |
| Initial Access | External Remote Services | T1133 | The RDP service was targeted |

### 6. Root Cause Analysis

| # | Bulgu |
| --- | --- |
| 1 | The RDP service has been left open to the internet/network |
| 2 | The Administrator account has not been disabled |
| 3 | Account Lockout Policy not configured — 264 attempts were not blocked |
| 4 | NLA (Network Level Authentication) has been disabled |
| 5 | MFA is not available for RDP |

### 7. Impact Analysis

Actual Damage: None — the password was not cracked.

Potential Damage (Had the Password Been Cracked):

Domain Admin privileges would have been compromised
The entire domain user database could have been stolen
Ransomware could have been deployed
The entire network could have been compromised via lateral movement

### 8. Recommendations

| Priority | Recommende | Desc |
| --- | --- | --- |
| Critical | Account Lockout Policy | Lock the account after 5 failed attempts |
| Critical | Restrict RDP | Allow access only via a VPN or from specific IP addresses |
| High | Enable NLA | Enable Network-Level Authentication |
| High | MFA | Add multi-factor authentication for RDP |
| Medium | Disable the Administrator account | Create a new admin account, disable the built-in Administrator |
| Medium | Honeypot Account | Create a fake "Administrator" account; trigger an alert when access is attempted |
| Low | Change RDP Port | A different port instead of 3389 (security measure, not the actual solution) |

### 9. Splunk Alert Suggestion

```jsx
index=wineventlog EventCode=4625
| bucket _time span=1m
| stats count by _time, Source_Network_Address, Account_Name
| where count > 10
| eval alert="Potential Brute Force: " + Source_Network_Address + " -> " + Account_Name
```

### 10. Result

This incident demonstrates just how quickly unprotected RDP services can be targeted. The attack was detected thanks to the Splunk SIEM and Sysmon logging infrastructure. The absence of an account lockout policy allowed the attacker to make hundreds of attempts.

Key Lesson: The RDP service must never be left directly exposed to the internet or the network. A combination of VPN and MFA is essential.

Efe Özel | Home SOC Lab | 2026-07-08