# IR-2026-002-Kerberoasting.md

## **Incident Response Report**

### Kerberoasting Attack - DC-01

| Field | Details |
| --- | --- |
| **Report Date** | 2026-07-13 23:33 |
| **Analyst** | Efe Özel |
| **Incident ID** | IR-2026-002 |
| **Severity** | High |
| **Status** | Closed |
| **Environment** | Home SOC Lab — soclab.local |

### 1. Incident Summary

On July 13, 2026 a Kerberoasting attack was detected via Splunk SIEM in the Home SOC Lab Environment. The attacker machine (Kali - 10.10.10.4) sent a Kerberos RGS request using the Administrator acc, targeting the SQL service acc on DC-01.

The attack was identified through an anomalous EventCode 4769 log with RC4 Encryption type (0x17) which is the primary indicator of a Kerberoasting attempt. The attacker obtained the encrypted TGS ticket and attempted to crack it offline to retrieve the service acc password.

### 2. Affected Systems

| System | IP Address | Role | Status |
| --- | --- | --- | --- |
| DC-01 | 10.10.10.10 | Domain Controller | Target |
| Kali Linux | 10.10.10.4 | Attacker Machine | Source |
| svc_sql | N/A | SQL Service Account | Compromised Target |

### 3. Timeline

| Time | Event |
| --- | --- |
| 2026-07-13 20:50:03 | First TGS request for svc_sql (RC4) from 10.10.10.4 |
| 2026-07-13 21:48:47 | Second TGS request for svc_sql (RC4) from 10.10.10.4 |
| 2026-07-13 23:00 | Analyst detected anomaly in Splunk (EventCode 4769, 0x17) |
| 2026-07-13 23:33 | Incident confirmed and report opened |

### **4. Technical Findings**

Splunk Query Used:

```jsx
index=wineventlog EventCode=4769 Ticket_Encryption_Type=0x17
| table _time, Account_Name, Service_Name, Client_Address
| sort -_time
```

Results:

| Time | Account | Service | Source IP | Encryption |
| --- | --- | --- | --- | --- |
| 2026-07-13 21:48:47 | [Administrator@SOCLAB.LOCAL](mailto:Administrator@SOCLAB.LOCAL) | svc_sql | 10.10.10.4 | 0x17 (RC4) |
| 2026-07-13 20:50:03 | [Administrator@SOCLAB.LOCAL](mailto:Administrator@SOCLAB.LOCAL) | svc_sql | 10.10.10.4 | 0x17 (RC4) |

Why 0x17 is Suspicious:

| Encryption Type | Value | Meaning |
| --- | --- | --- |
| AES256 | 0x12 | Normal — secure, used by legitimate services |
| RC4 | 0x17 | Suspicious — weak encryption, easy to crack offline |
|  |  |  |

In a normal environment, TGS requests use AES256 (0x12). RC4 (0x17) requests are specifically made by Kerberoasting tools because RC4-encrypted hashes are significantly easier to crack offline using tools like Hashcat or John the Ripper.

### **5. MITRE ATT&CK Mapping**

| Field | Details |
| --- | --- |
| **Tactic** | Credential Access |
| **Technique** | Steal or Forge Kerberos Tickets |
| **Sub-technique** | Kerberoasting |
| **ID** | T1558.003 |

### 6. Impact Analysis

**Actual Damage:** Password cracked — svc_sql credentials exposed.

**Potential Impact:**

- svc_sql could be used for lateral movement
- If svc_sql had admin privileges — full domain compromise possible
- Attacker could access SQL databases containing sensitive data
- Persistence mechanisms could be established

### 7. Recommendations

Change svc_sql password to 25+ character

Disable RC4 Encryption, Force AES256 only via Policy

Splunk Alert for 0x17, Auto alert on RC4 TGS Requests

 

### 8. Splunk Detection Rule

```jsx
index=wineventlog EventCode=4769 Ticket_Encryption_Type=0x17
| stats count by Account_Name, Service_Name, Client_Address
| where Service_Name!="WINDOWS11$" AND Service_Name!="DC-01$"
| eval alert="[HIGH] Kerberoasting Attempt: " + Account_Name + " -> " + Service_Name
```