This section documents how I intentionally weakened my enterprise homelab environment to simulate real-world insecure configurations. These misconfigurations allow for credential harvesting, brute-force, lateral movement, exfiltration, and endpoint monitoring through Wazuh.

> **⚠️ Note:** These configurations are for educational homelab use only and should never be applied to production systems.

---
## 1. Open SSH on `project-corp-svr`

This Linux server is intentionally left without a Wazuh agent to demonstrate detection gaps.
### Install & enable SSH

```bash
sudo apt update
sudo apt install openssh-server -y
sudo systemctl start ssh
sudo systemctl enable ssh
```

### Allow SSH in the firewall

```bash
sudo ufw allow 22
sudo ufw enable
sudo ufw status
```

### Enable password login & root access

```bash
sudo nano /etc/ssh/sshd_config
```

Modify:

```
PasswordAuthentication yes
PermitRootLogin yes
```

Restart SSH:

```bash
sudo systemctl restart ssh
```

Set root password:

```bash
sudo passwd root
# password: november
```

Detection Insight: No Wazuh agent → no SSH visibility → blind spot for lateral movement.

---

## 2. Open SSH on `project-linux-client`

Same process as above, but this machine has a Wazuh agent.

### Install & enable SSH

```bash
sudo apt update
sudo apt install openssh-server -y
sudo systemctl enable ssh
sudo systemctl start ssh
```

### Allow SSH through UFW

```bash
sudo ufw allow 22
sudo ufw status
```

### Enable password authentication

```bash
sudo nano /etc/ssh/sshd_config
```

Modify:

```
PasswordAuthentication yes
```

Restart SSH:

```bash
sudo systemctl restart ssh
```

Set root password:

```bash
sudo passwd root
# password: november
```

---
## Wazuh Detection

Wazuh detects SSH auth failures using built-in rule:

- Rule ID:  5760
- Description: `sshd: authentication failed`

You can monitor logs via:
- _Explore → Discover → search “sshd”_

---
## 3. Create Detection Alert – Failed SSH Attempts

Configure Wazuh to alert when >3 SSH failures occur.
### Create Monitor

Explore → Alerting → Monitors → Create Monitor
- Name: 3 Failed SSH Attempts
- Index: `wazuh-alerts-4.x-*`
- Time Field: `@timestamp`
### Data Filters
Add filters:
- `data.wazuh.rule.groups: authentication_failed`
- `process.name: sshd`
### Trigger

- Severity: 3 (Medium)
- Condition: count > 2

This produces alerts for brute-force attempts.

---

## 4. SMTP Inbox Simulation (MailHog)

Purpose: simulate phishing, credential capture, and internal email flow.
### On `project-corp-svr`

Start MailHog:

```bash
cd /home/mailhog
sudo docker compose up -d
```

### On `project-linux-client`

Run email polling script:

```bash
cd /home
./email_poller.sh &
```

Stop script:

```bash
pkill -f email_poller
```

Detection Insight:  
Again, `project-corp-svr` has no agent → email activity is invisible.

---

## 5. Enable WinRM on `project-win-client`

WinRM enables remote execution, necessary for tools like Evil-WinRM.

Open PowerShell as Administrator:

```powershell
powershell -ep bypass
Enable-PSRemoting -force
winrm quickconfig -transport:https
Set-Item wsman:\localhost\client\trustedhosts *
net localgroup "Remote Management Users" /add administrator
Restart-Service WinRM
```

---

## Wazuh Detection – WinRM

Windows events for WinRM authentication:
- 4624 – successful
- 4625 – failed

Wazuh rule for logon success:
- Rule ID: 60106
- Look for: `data.win.eventdata.logonProcessName: Kerberos`

---

## 6. Create Detection Alert – WinRM Logon

Same process as SSH monitor:
- Name: WinRM Logon
- Index: `wazuh-alerts-4.x-*`

Filters:
- `win.system.eventID: 4624`
- `win.eventdata.logonProcessName: Kerberos`

Trigger:
- Severity 3
- Condition: `>1` events

---

## 7. Enable RDP on `project-dc`

Windows settings:

```
Settings → System → Remote Desktop → Enable
```

---

## Wazuh Detection – RDP

- Event ID: 4624
- Process: User32 (RDP logon)

Wazuh Rule:
- Rule ID: 92653
- Description: Administrator logged in via RDP

---
## 8. Create a Sensitive File on the Domain Controller

Path:  
`C:\Users\Administrator\Documents\ProductionFiles\secrets.txt`

Content example:

```
DEE BOO DAH
```

---
## Enable File Integrity Monitoring (FIM)

In Wazuh:

Server Management → Endpoint Groups → Windows → Files (agent.conf)

Add:

```xml
<syscheck>
  <directories check_all="yes" report_changes="yes" realtime="yes">
    C:\Users\Administrator\Documents\ProductionFiles
  </directories>
  <frequency>60</frequency>
</syscheck>
```

FIM now logs edits/deletes/changes to `secrets.txt`.

---
## 9. Custom Detection Rule – Sensitive File Access

Modify `local_rules.xml`:

```xml
<group name="syscheck">
  <rule id="100002" level="10">
    <field name="file">secrets.txt</field>
    <match>modified</match>
    <description>FIM Alert: sensitive.txt file modified</description>
  </rule>
</group>
```

Restart Wazuh.

---
## 10. Create Monitor – Sensitive File Access

- Name: File Accessed
- Filters:
    - `full_log: secrets.txt`
    - `syscheck.event: modified`
- Trigger:
    - Severity 2 (High)
    - Condition: `>1`

---
## 11. Exfiltration to `project-attacker`

Goal: simulate data exfiltration via SSH/SCP.

### On Kali attacker machine

Enable SSH:

```bash
sudo systemctl start ssh.service
```

Create exfiltration file:

```bash
touch /home/attacker/my_exfil.txt
```

### Then from Windows:

Use SCP (or PowerShell equivalents) to copy `secrets.txt` → Kali.

Detection:  
FIM + custom rule should alert on file access.

---
## 12. Enable Insecure Guest Logons (SMB)

This simulates outdated enterprise environments.

### Using Local Group Policy Editor

```
gpedit.msc → 
Computer Configuration → 
Administrative Templates → 
Network → Lanman Workstation → 
Enable insecure guest logons = Enabled
```

### Registry equivalent:

```powershell
Set-ItemProperty -Path "HKLM:\SYSTEM\CurrentControlSet\Services\LanmanWorkstation\Parameters" `
-Name AllowInsecureGuestAuth -Value 1 -Force
```


---

## Summary

By the end of this guide, the environment now contains intentional weaknesses:
- SSH with password & root login enabled
- WinRM & RDP exposed
- Misconfigured SMB guest access
- Sensitive file monitored via Wazuh FIM
- No agent on corporate server (visibility gap)
- MailHog simulating phishing environment
- Full detection alerts built (SSH, WinRM, file access)

This setup prepares the environment for the Cyber Attack Simulation phase of the homelab.
