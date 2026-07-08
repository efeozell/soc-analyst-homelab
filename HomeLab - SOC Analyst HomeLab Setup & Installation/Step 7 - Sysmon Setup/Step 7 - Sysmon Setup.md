# Step 7 - Sysmon Setup

**Why Sysmon?**

- Because default windows log is not show deeply information but sysmon logs shows everthing from log. And we need to send these logs to Splunk. On the splunk logs needs contains deep information

**Where we setup Sysmon?**

- ✅ DC-01
- ✅ WIN 11

Move Sysmon official page on the DC-01 and download the ZIP folder from this page.

![image.png](Step%207%20-%20Sysmon%20Setup/image.png)

![image.png](Step%207%20-%20Sysmon%20Setup/image%201.png)

Now we need to configure sysmon because default sysmon generate many garbage logs.

Most popular Sysmon Config is SwiftOnSecurity Sysmon Config, therefore i need to download this and saved an .xml file.

![image.png](Step%207%20-%20Sysmon%20Setup/image%202.png)

You can download this config file with this command

```jsx
 Invoke-WebRequest -Uri "https://raw.githubusercontent.com/SwiftOnSecurity/sysmon-config/master/sysmonconfig-export.xml" -OutFile "C:\sysmonconfig.xml"
```

After installation move the Sysmon folder and execute Sysmon64.exe

![image.png](Step%207%20-%20Sysmon%20Setup/image%203.png)

```jsx
.\Sysmon64.exe -accepteula -i C:\sysmonconfig.xml
```

Check Sysmon service

![image.png](Step%207%20-%20Sysmon%20Setup/image%204.png)

Now we setup Sysmon on the DC-01 but i need to setup WIN11, you can do same steps on the WIN 11.