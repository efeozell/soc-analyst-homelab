# Step 3 - Windows Server 2022 VM Setup

#### **Let’s Understand the Concept First**

Windows Server 2022 will be our Domain Controller. What is a Domain Controller?

The main of everything in the corporate network:

→Manage user accounts

→Performs identity verification

→Deploys Group Policy

→Running DNS server

The attacker first target because if th DC is compromies, the whole network is compromised.

On the VirtualBox:

1- Click “New” button

![image.png](image.png)

2-On the opened screen

![image.png](image%201.png)

Continue with Finish button.

3- Connect the VM to the SOC-Lab-Network

Right click DC-01 and move the Network tab and change to our creat NAT Network

![image.png](image%202.png)

4-Start Windows Server Installation

Start DC-01 on the VirtualBox

![image.png](image%203.png)

And Next

![image.png](image%204.png)

Select Windows Server 2022 Standard Evaluation (Desktop Experience) because we need to graphical interface.

![image.png](image%205.png)

Selecet Custom

![image.png](image%206.png)

The amoun of storage space you allocate will be displayed here. Select it and click “Next”

And then setup will be started you need to wait 10-20 minute to finish setup.

![image.png](image%207.png)

After the setup you need to be chose password.

![image.png](image%208.png)

And you can login with this password.

![image.png](image%209.png)