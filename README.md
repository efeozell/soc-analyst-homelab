# HomeLab - SOC Analyst HomeLab

I'm Efe, an aspiring SOC analyst focused on Blue Team work. This repo documents the home lab I built to practice detection and investigation: Windows Server 2022 with Active Directory, a Windows 11 client, a Kali attacker, Sysmon and Splunk. I run simple attack scenarios against the lab, then investigate the logs and write up what I find.

The goal is not just to finish exercises, but to understand what each attack looks like in the logs and how to detect it. Follow the setup steps in order, then read the incident reports.

You can follow by:

Step 1 - Target Archirecture + Download Windows Server 2022.md

Step 2 - Create NAT Network.md

Step 3 - Windows Server 2022 VM Setup.md

Step 4 - Active Directory Domain Services Setup.md

Step 5 - Connect Windows 11 to Domain.md

Step 6 - Kali Linux Move to SOC-Lab Network.md

Step 7 - Sysmon Setup.md

Step 8 - Splunk Setup.md

Step 9 - Splunk Universal Forwarder Setup.md

I Simulate the Attacks if you want to see incidents reports you can move "incidents" folder and look at the incidents.

Attack 1 - Nmap Scan & Brute Force Attack Simulation

Attack 2 - Kerberoasting Attack

Attack 3 - Pass-the-Hash (Lateral Movement)

# Target Architecture

## Target Architecture

![image.png](./HomeLab%20-%20SOC%20Analyst%20HomeLab%20Setup%20&%20Installation/Step%201%20-%20Target%20Archirecture%20+%20Download%20Windows%20Server%202022/image.png)

I aim this architecture to setup my homelab. After i decide the architecture i need to determine VM source Plan

## VM Source Plan

![image.png](./HomeLab%20-%20SOC%20Analyst%20HomeLab%20Setup%20&%20Installation/Step%201%20-%20Target%20Archirecture%20+%20Download%20Windows%20Server%202022/image%201.png)

## My Current Situation

✅ VirtualBox already installed

✅ Kali Linux already installed 

❌ Windows Server 2022 (Domain Controller)

❌ Splunk

❌ Sysmon

## See More

⚠️Enter the Home-Lab - SOC Analyst HomeLab folder -> and You can follow by:

1️⃣Step 1 - Target Archirecture + Download Windows Server 2022.md

2️⃣Step 2 - Create NAT Network.md

3️⃣Step 3 - Windows Server 2022 VM Setup.md

4️⃣Step 4 - Active Directory Domain Services Setup.md

5️⃣Step 5 - Connect Windows 11 to Domain.md

6️⃣Step 6 - Kali Linux Move to SOC-Lab Network.md

7️⃣Step 7 - Sysmon Setup.md

8️⃣Step 8 - Splunk Setup.md

9️⃣Step 9 - Splunk Universal Forwarder Setup.md

☠️ Attack Simulation

Attack 1 - Nmap Scan & Brute Force Attack Simulation

Attack 2 - Kerberoasting Attack