This document summarizes how each virtual machine in the Project homelab was provisioned, configured, domain-joined, hardened/loosened, and prepared for the cyber attack simulation.  
All VMs were deployed using VMware Workstation Pro (or VirtualBox equivalent) on a shared NAT network (`10.0.0.0/24`).

---

## Overall Homelab Topology

| Host                   | OS                    | IP         | Role                                   |
| ---------------------- | --------------------- | ---------- | -------------------------------------- |
| `project-dc`           | Windows Server 2025   | 10.0.0.5   | Domain Controller, DNS, DHCP           |
| `project-win-client`   | Windows 11 Enterprise | 10.0.0.100 | Windows workstation                    |
| `project-linux-client` | Ubuntu Desktop 22.04  | 10.0.0.101 | Linux workstation                      |
| `project-corp-svr`     | Ubuntu Desktop 22.04  | 10.0.0.8   | Corporate Jumpbox Server               |
| `project-sec-box`      | Ubuntu Server 22.04   | 10.0.0.10  | Security server (Wazuh/SO integration) |
| `project-sec-work`     | Security Onion        | 10.0.0.103 | Network monitoring                     |
| `project-attacker`     | Kali Linux            | DHCP       | Offensive machine                      |

Default gateway for all: 10.0.0.1
DNS for all: 10.0.0.5 (the Domain Controller)

---

## Windows 11 Enterprise: Workstation

This VM acts as the general employee workstation.
### **Installation steps**

- Boot from Windows 11 Enterprise ISO.
- Create partition on Disk 0 → select largest partition → Install.
- During OOBE, bypass Microsoft Account:
    - Switch NIC to Host-Only to cut Internet.
    - Press Shift + F10
    - Run:
        ``` oobe\bypassnro  ```    
- Create a temporary local user (this will later be replaced by domain login).
- Disable privacy settings & finish setup.
- Switch NIC back to NAT Network (`project-network`).
### **Assign static IP**

```
IP: 10.0.0.100  
Mask: 255.255.255.0  
Gateway: 10.0.0.1  
DNS: 10.0.0.5  
```

### **Join Domain**

Control Panel → System → Rename PC → Domain →

```
corp.project-dc.com
```

Login with domain user:

```
Username: johnd@corp.project-dc.com
Password: @password123!
```

Snapshot taken after domain join.

---

## 2. Ubuntu Desktop 22.04 – Linux Workstation

This VM represents a Linux engineer/developer workstation.
### **Installation**

- Start installer → “Install Ubuntu”.
- Choose defaults, erase disk, install.
- Create local user (e.g., janed).
- Skip “Use Active Directory” option.
- Finish first-boot wizard.

### **Static IP Configuration**

Settings → Network → Add new Wired connection

```
IP: 10.0.0.101  
Mask: 255.255.255.0  
Gateway: 10.0.0.1  
DNS: 10.0.0.5  
```

---

## 3. Ubuntu Desktop (Clone) → Corporate Server (`project-corp-svr`)

The corporate server acts as a jumpbox for internal services (dockerized apps, internal access point).

### **Steps**

1. Clone `project-linux-client` VM.
2. Change IP
    ```    IPv4 → 10.0.0.8    ```
3. Change hostname:
  ```   sudo hostnamectl set-hostname corp-svr    ```
4. Create admin user:
 ```bash
    sudo adduser project-admin
    sudo usermod -aG sudo project-admin
    ```

5. Join Domain:

    `sudo net ads join -U Administrator`

6. Install Docker Engine:
  ```bash
    sudo apt-get update
    # Add Docker repo (per Docker docs)
    sudo apt-get install docker-ce docker-ce-cli containerd.io
    docker pull hello-world
    docker run hello-world
    ```
Snapshot after Docker and AD join confirmed.

---

## 4. Ubuntu Server (Clone) → Security Server (`project-sec-box`)

Acts as dedicated security stack host (Wazuh agents, logs, SO integration).
### Steps

1. Clone `project-linux-client`.
2. Change hostname:
 ```bash
    sudo nano /etc/hostname
    sec-box
    ```
3. Create `sec-user`:
   ```bash
    sudo adduser sec-user
    sudo usermod -aG sudo sec-user
    ```

4. Join Active Directory:

  ```bash
    sudo net ads join -U Administrator
    ```

5. Create AD account `secuser@corp.project-dc.com`.

---

## 5. Security Onion – Network Monitoring Workstation

Used for Zeek, Suricata, and Elastic dashboards.

## Install Steps

- Boot Security Onion ISO → choose:
    - Install Security Onion Desktop
- Create user:
  ```
    project-sec-work / @password123!
    ```

- Assign static IP:
  ```
    10.0.0.103/24
    Gateway: 10.0.0.1
    DNS: 10.0.0.5
    ```

- Add DNS search domain:
  ```
    corp.project-dc.com
    ```

- Reboot:
  ```bash
    reboot
    ```

- Set root password:
  ```bash
    sudo passwd root
    ```

Snapshot taken.

---

## 6. Kali Linux: Attacker Machine

Primary offensive operations machine for phishing, brute force, reverse shells, lateral movement.

## Setup steps

- Start installer → “Graphical Install”.
- Create user:
  ```
    attacker / attacker
    ```

- Guided partition → use entire disk.
- Enable SSH:
 ```bash
    sudo systemctl start ssh
    ```
- Optional: disable auto-logoff (XFCE Power Manager).
Snapshot taken.

---

## 7. Windows Server 2025: Domain Controller (`project-dc`)

Central authentication, DHCP, DNS.
### Installation

- Boot ISO → "Desktop Experience"
- Create partitions → install.
- Set Administrator password:
  ```
    @Deeboodah1!
    ```
### Static IP

```
10.0.0.5  
255.255.255.0  
10.0.0.1  
```

### Promote to Domain Controller

Server Manager → Add Roles and Features:
- AD Domain Services
- DNS Server
- DHCP Server

Configure AD DS:
- New forest:
  ```
    corp.project-dc.com
    ```
- DSRM password = @Deeboodah1!
### Configure DNS Forwarder

DNS Manager → Forwarders → Add:

```
8.8.8.8
```

### DHCP Scope (10.0.0.100–10.0.0.200)

Router IP:

```
10.0.0.1
```

### Add Domain Users

- John Doe (for Windows workstation)
- Jane Doe (Linux workstation)
- Admin/service accounts as needed

Snapshot taken.

---

## 8 . Corporate Server – Docker Component Setup

After provisioning:

```bash
docker pull hello-world
docker run hello-world
```

Ensures containerization ready for FTP, DNS, Email (MailHog), etc.

---

## Final Summary

After provisioning, the homelab consists of:

- A fully functional Windows domain
- Both Windows & Linux domain-joined workstations
- A corporate jumpbox server running Dockerized internal services
- A security monitoring server + Security Onion
- A dedicated attacker machine
- Proper network segmentation and static addressing
- Snapshots taken at each milestone
