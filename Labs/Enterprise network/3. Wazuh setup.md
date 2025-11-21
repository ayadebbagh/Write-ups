
This document summarizes the setup of the Wazuh SIEM stack and the deployment of Wazuh agents across Windows, Linux, and server systems in the homelab.
## Overview

Wazuh is the SIEM/XDR platform used in this project to collect logs, detect malicious activity, monitor file integrity, and centralize security analytics.  
The homelab uses these Wazuh components:

- Wazuh Indexer (Elasticsearch-based storage + search)
- Wazuh Server (rules engine, threat intelligence, agent management)
- Wazuh Dashboard (Kibana-style UI for detections, alerts, and configuration)
- Wazuh Agents (installed on every workstation and server)

Wazuh runs on the dedicated security server:  
`project-sec-box` (Ubuntu Server 22.04).

## Install Wazuh Indexer + Server + Dashboard

1. Log into the security server:
   ```
    su sec-user
    ```

2. Install cURL if missing:
   ```
    sudo apt install curl
    ```

3. Run the Wazuh installation wizard:
   ```
    curl -sO https://packages.wazuh.com/4.9/wazuh-install.sh && sudo bash ./wazuh-install.sh -a -i
    ```

    - `-a`: unattended
    - `-i`: ignore minimum hardware checks

4. After installation, the script prints:
    - Dashboard URL
    - Admin username
    - Randomly generated admin password
    
    Passwords can be retrieved later from:
   ```
    sudo tar -O -xvf wazuh-install-files.tar wazuh-install-files/wazuh-passwords.txt
    ```
    
5. Access dashboard in the VM’s browser:
   ```
    https://localhost
    ```
    Accept certificate warning and log in.
    
## Deploying Wazuh Agents

### Windows Workstation: project-win-client (Method 1)

1. Download MSI installer:
   ```
    https://packages.wazuh.com/4.x/windows/wazuhagent-4.9.2-1.msi
    ```

2. Install → enter:
   ```
    Wazuh Server Address: 10.0.0.10
    Admin credentials (domain admin)
    ```

3. On Wazuh server:
   ```
    su root
    /var/ossec/bin/manage_agents
    ```

    - Press A to add new agent
    - Enter Windows IP → copy generated key 

4. On Windows agent, paste key into installer.

5. Start service:
   ```
    NET START WAZUH
    ```

### Linux Workstation: project-linux-client

From Wazuh Dashboard → Deploy new agent → DEB amd64  
Run on Linux:

```
sudo wget https://packages.wazuh.com/4.x/apt/pool/main/w/wazuh-agent/wazuh-agent_4.9.2-1_amd64.deb
sudo WAZUH_MANAGER='10.0.0.10' WAZUH_AGENT_GROUP='default' WAZUH_AGENT_NAME='project-linux-client' dpkg -i ./wazuh-agent_4.9.2-1_amd64.deb
```

Enable + start:

```
sudo systemctl daemon-reload
sudo systemctl enable wazuh-agent
sudo systemctl start wazuh-agent
```
## Organizing Agent Groups

In Wazuh Dashboard:
- Server Management → Endpoint Groups
- Create:
    - Linux
    - Windows
- Assign each agent to its appropriate group.
## Onboarding Custom Log Sources

### Windows agent.conf:

```
<agent_config>
  <localfile>
    <location>Security</location>
    <log_format>eventchannel</log_format>
  </localfile>
  <localfile>
    <location>Application</location>
    <log_format>eventchannel</log_format>
  </localfile>
</agent_config>
```

### Linux agent.conf:

```
<agent_config>
  <localfile>
    <log_format>syslog</log_format>
    <location>/var/log/auth.log</location>
  </localfile>
  <localfile>
    <log_format>syslog</log_format>
    <location>/var/log/secure</location>
  </localfile>
  <localfile>
    <log_format>audit</log_format>
    <location>/var/log/audit/audit.log</location>
  </localfile>
</agent_config>
```

Wazuh is now ready to ingest event logs, authentication logs, suspicious behavior, and security-relevant telemetry from every system in the homelab.



