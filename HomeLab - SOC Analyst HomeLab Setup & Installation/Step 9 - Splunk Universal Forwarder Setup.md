# Step 9 - Splunk Universal Forwarder Setup

**Why we doing?**

Splunk Enterprise → Works on DC-01, but it cant see their logs

Universal Forwarder →A lightweight agent, Installed on every Windows machine, Collects logs and sends them to Splunk

Download Forwarder.

Go to Splunk Universal Forwarder page and download for Windows.

![image.png](Step%209%20-%20Splunk%20Universal%20Forwarder%20Setup/image.png)

Execute downloaded .msi file.

1. Accept the licence ✅
2. Select "An on-premises Splunk Enterprise instance"
3. Username: admin
Password: set a password (make a note of it)
4. Deployment Server: leave blank
5. Receiving Indexer:
IP: 127.0.0.1 (as it is on DC-01)
Port: 9997
6. Install

![image.png](Step%209%20-%20Splunk%20Universal%20Forwarder%20Setup/image%201.png)

![image.png](Step%209%20-%20Splunk%20Universal%20Forwarder%20Setup/image%202.png)

After the setup forwarder for DC-01. I need to also install WIN 11. I am doing same steps again for WIN 11.

What we sends Logs?

- I setup forwarder but forwarder dont know what send logs. With inputs.conf file i configure this.

To configure this i need to move this file path:

```jsx
C:\Program Files\SplunkUniversalForwarder\etc\system\local\
```

Create an file name inputs.conf 

I write these:

```jsx
[WinEventLog://Application]
index = wineventlog
disabled = false

[WinEventLog://Security]
index = wineventlog
disabled = false

[WinEventLog://System]
index = wineventlog
disabled = false

[WinEventLog://Microsoft-Windows-Sysmon/Operational]
index = wineventlog
disabled = false
renderXml = true
```

Application → App Logs

Security → Login, Logout, Privelege logs

System → System Logs

Sysmon → Deeply logs

![image.png](Step%209%20-%20Splunk%20Universal%20Forwarder%20Setup/image%203.png)

Save this file and Restart forwarder.

After saved conf file i can look at this settings work or not. To be sure and look logs i moved Splunk and filter by index=”wineventlog”

![image.png](Step%209%20-%20Splunk%20Universal%20Forwarder%20Setup/image%204.png)

I done this step successfully. I collect logs from 2 machine with Splunk Forwarder and show with Splunk Enterprise.

Now i can try attack scenarios with these machines and Kali VM. I clapped my self i moved step by step. After this point i simulate attacks if you want anything you can.

**Current Situation**

✅ VirtualBox + NAT Network

✅ Windows Server 2022 (DC-01)

✅ Active Directory Domain Services

✅ Windows 11 added Active Directory

✅ Kali Linux on the same network

✅ Sysmon installed (DC-01 + Windows 11)

✅ Splunk Enterprise Installed

✅ Splunk Universal Forwarder Insatlled (DC-01 + Windows 11)

✅ Logs sends Splunk