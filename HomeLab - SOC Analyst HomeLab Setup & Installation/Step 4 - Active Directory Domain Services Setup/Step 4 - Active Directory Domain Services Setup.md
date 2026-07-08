# Step 4 - Active Directory Domain Services Setup

Active Directory Domain Services (AD DS) is the role that transforms Windows Server into a full-fledged domain controller.

Now we have:

- Windows Server 2022 → Normal server, do not have any feature or something

After the AD DS Setup:

- Windows Server 2022 → Domain Controller, Manage Users, Running Kerberos, Running DNS, Running Group Policy

### Step 4.1 - Give Static IP

On the Domain Controller IP address musnt be change. If take IP from DHCP changes every restart. Therefore i need to do static.

→ Go Network & Internet on the Windows Server 

→ Change Adapter options

![image.png](image.png)

![image.png](image%201.png)

![image.png](image%202.png)

IP Address      : 10.10.10.10
Subnet Mask     : 255.255.255.0
Default Gateway : 10.10.10.1
DNS Server      : 127.0.0.1

![image.png](image%203.png)

### Step 4.2 - Change Computer Name

Start → Settings → System → About

→Rename this PC

![image.png](image%204.png)

### Step 4.3 - Create AD DS Role

On the Server Manager → Manage → Add Roles And Features

![image.png](image%205.png)

Next and Next again

![image.png](image%206.png)

On the Server Roles screen you must be select Active Directory Domain Services

![image.png](image%207.png)

And an pop-up appears, on this pop-up click the Add Features

![image.png](image%208.png)

On the Features screen, you cannot change anything and click Next

![image.png](image%209.png)

AD DS Screen → Next

Confirmation Screen

![image.png](image%2010.png)

Clich this options and click Install button on the bottom

Installation it may take 2-3 min. 

![image.png](image%2011.png)

### Step 4.4 - Upgrade to a Domain Controller

![image.png](image%2012.png)

![image.png](image%2013.png)

Next, Domain Controller Options:

![image.png](image%2014.png)

Next,

DNS Options → Next,

Additional Options → SOCLAB, Next

Paths → Next

 Prerequisites Check → Click Install

After these processes installation finish and restart automaticly.

After the Restart, On the login screen, you will now see the following:￼￼SOCLAB\Administrator.

![image.png](image%2015.png)