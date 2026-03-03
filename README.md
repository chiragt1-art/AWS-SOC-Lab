# 🛡️ Cloud-Based SOC Lab on AWS

## 📌 Project Overview
A complete **Security Operations Center (SOC)** lab built on AWS Cloud to simulate, detect, and monitor real-world cyber attacks using **Wazuh SIEM**.

This project demonstrates how a SOC team monitors threats in real time using industry-standard tools.

---

## 🏗️ Architecture

```
┌─────────────────┐         ┌─────────────────┐         ┌─────────────────┐
│   Kali Linux    │ ──────► │  Ubuntu Victim  │ ──────► │  Wazuh Server   │
│   (Attacker)    │  Attack │  (Wazuh Agent)  │  Logs   │  (SIEM/Monitor) │
└─────────────────┘         └─────────────────┘         └─────────────────┘
                                                                  │
                                                                  ▼
                                                         ┌─────────────────┐
                                                         │ Wazuh Dashboard │
                                                         │    (Alerts)     │
                                                         └─────────────────┘
```

---

## ☁️ AWS Infrastructure

| Instance | Name | Type | Purpose |
|----------|------|------|---------|
| 1 | projectsoc1 | m7i.flex.large | Wazuh SIEM Server |
| 2 | victimsoc | t3.small | Target/Victim Machine |
| 3 | attackersoc | t3.small | Kali Linux Attacker |

---

## 🛠️ Tools Used

| Tool | Version | Purpose |
|------|---------|---------|
| AWS EC2 | - | Cloud Infrastructure |
| Ubuntu | 22.04 LTS | Server & Victim OS |
| Kali Linux | 2025.4 | Attacker OS |
| Wazuh SIEM | 4.9.2 | Security Monitoring |
| Wazuh Agent | 4.9.2 | Log Forwarding |
| Nmap | 7.98 | Port Scanning |
| Hydra | 9.6 | SSH Brute Force |
| PuTTY | Latest | SSH Client |

---

## ⚔️ Attacks Performed

### 1. Nmap Port Scan
```bash
nmap -A 172.31.67.164
```
- Scanned all 1000 ports on victim machine
- Generated reconnaissance activity logs

### 2. Hydra SSH Brute Force
```bash
hydra -l ubuntu -P /usr/share/wordlists/rockyou.txt ssh://172.31.67.164 -t 4 -V
```
- Used rockyou.txt wordlist (14 million passwords)
- Successfully found password: `12345`
- Attack completed in seconds

---

## 🚨 Results — Wazuh Detection

| Detection | Status |
|-----------|--------|
| Brute Force Attack | ✅ Detected |
| Password Guessing | ✅ Detected |
| Remote Services | ✅ Detected |
| Authentication Failures | ✅ 6 Detected |
| Authentication Success | ✅ 30 Logged |
| MITRE ATT&CK Mapping | ✅ Active |

---

## 🎥 Demo Video

[![SOC Lab Demo](https://img.youtube.com/vi/kERcBG1oRKc/0.jpg)](https://youtu.be/kERcBG1oRKc)

▶️ [Watch Full Demo on YouTube](https://youtu.be/kERcBG1oRKc)

---

## 📸 Screenshots

### 1. AWS EC2 — All 3 Instances Running
![AWS Instances](screenshots/1-aws-ec2-instances.png)

### 2. Kali Linux — Attacker Machine Connected
![Kali Login](screenshots/2-kali-login.png)

### 3. Wazuh Dashboard — Loading
![Wazuh Loading](screenshots/3-wazuh-loading.png)

### 4. All 3 PuTTY Terminals — Full Lab View
![Three Terminals](screenshots/4-three-terminals.png)

### 5. Nmap Port Scan — Attack Running
![Nmap Scan](screenshots/5-nmap-scan.png)

### 6. Hydra Brute Force — Attack Running
![Hydra Attack](screenshots/6-hydra-attack.png)

### 7. Password Found! — Attack Successful
![Password Found](screenshots/7-password-found.png)

### 8. Victim Agent — Connected to Wazuh
![Agent Connected](screenshots/8-victim-agent-connected.png)

### 9. Wazuh Threat Hunting — Attacks Detected!
![Threat Hunting](screenshots/9-wazuh-threat-hunting.png)

### 10. Wazuh Alerts — MITRE ATT&CK Detection
![Wazuh Alerts](screenshots/10-wazuh-alerts.png)

---

## 🔧 Security Group Configuration

### Wazuh Server
| Type | Port | Source |
|------|------|--------|
| All Traffic | All | My IP |
| Custom TCP | 1514 | 0.0.0.0/0 |
| Custom TCP | 1515 | 0.0.0.0/0 |
| Custom UDP | 1514 | 0.0.0.0/0 |
| HTTPS | 443 | My IP |

### Victim Machine
| Type | Port | Source |
|------|------|--------|
| All Traffic | All | 0.0.0.0/0 |

### Attacker Machine
| Type | Port | Source |
|------|------|--------|
| All Traffic | All | My IP |

---

## 📋 Step-by-Step Setup

### Step 1 — Launch EC2 Instances
- Launch Wazuh Server (m7i.flex.large, Ubuntu 22.04, 30GB)
- Launch Victim (t3.small, Ubuntu 22.04, 15GB)
- Launch Attacker (t3.small, Kali Linux 2025.4, 15GB)

### Step 2 — Install Wazuh Server
```bash
curl -sO https://packages.wazuh.com/4.9/wazuh-install.sh
sudo bash wazuh-install.sh -a -i
```

### Step 3 — Install Wazuh Agent on Victim
```bash
sudo WAZUH_MANAGER='172.31.92.121' apt install wazuh-agent=4.9.2-1 -y
sudo systemctl start wazuh-agent && sudo systemctl enable wazuh-agent
```

### Step 4 — Run Attacks from Kali
```bash
# Port Scan
nmap -A 172.31.67.164

# Brute Force
hydra -l ubuntu -P /usr/share/wordlists/rockyou.txt ssh://172.31.67.164 -t 4 -V
```

### Step 5 — Monitor in Wazuh Dashboard
- Open `https://WAZUH_SERVER_IP`
- Go to Threat Intelligence → Threat Hunting
- View real-time alerts and MITRE ATT&CK detections

---

## 💡 Key Learnings
- ✅ AWS Cloud infrastructure setup
- ✅ Linux server administration
- ✅ SIEM deployment and configuration
- ✅ Network security and firewall rules
- ✅ Real-world attack simulation
- ✅ Threat detection and monitoring
- ✅ MITRE ATT&CK framework
- ✅ Incident response basics

---

## 👨‍💻 Skills Demonstrated

`AWS` `EC2` `VPC` `Security Groups` `Ubuntu` `Kali Linux` `Wazuh SIEM` `Nmap` `Hydra` `SSH` `Linux Administration` `Threat Detection` `MITRE ATT&CK` `SOC Operations`

---

## 🔁 How to Build This Project Yourself

### Prerequisites
- AWS Account (Free tier eligible)
- PuTTY installed on Windows
- Basic Linux knowledge

---

### Step 1 — Launch 3 EC2 Instances on AWS

| Instance | Name | OS | Type | Storage |
|----------|------|----|------|---------|
| Wazuh Server | projectsoc1 | Ubuntu 22.04 LTS | m7i.flex.large | 30GB gp3 |
| Victim | victimsoc | Ubuntu 22.04 LTS | t3.micro | 15GB gp3 |
| Attacker | attackersoc | Kali Linux 2025.4 | t3.micro | 15GB gp3 |

**Security Group Rules for Wazuh Server:**
| Type | Port | Source |
|------|------|--------|
| All Traffic | All | My IP |
| Custom TCP | 1514 | 0.0.0.0/0 |
| Custom TCP | 1515 | 0.0.0.0/0 |
| Custom UDP | 1514 | 0.0.0.0/0 |
| HTTPS | 443 | My IP |

**Security Group Rules for Victim:**
| Type | Port | Source |
|------|------|--------|
| All Traffic | All | 0.0.0.0/0 |

**Security Group Rules for Attacker:**
| Type | Port | Source |
|------|------|--------|
| All Traffic | All | My IP |

---

### Step 2 — Connect to All Instances via PuTTY

1. Download and install PuTTY
2. Convert `.pem` key to `.ppk` using PuTTYgen
3. Open PuTTY → Enter Public IP → Load PPK key → Connect
4. Username for Ubuntu: `ubuntu`
5. Username for Kali: `kali`

---

### Step 3 — Install Wazuh Server

Connect to **Wazuh Server** and run:

```bash
# Update system
sudo apt update && sudo apt upgrade -y

# Add swap memory
sudo fallocate -l 4G /swapfile
sudo chmod 600 /swapfile
sudo mkswap /swapfile
sudo swapon /swapfile

# Download and install Wazuh
curl -sO https://packages.wazuh.com/4.9/wazuh-install.sh
sudo bash wazuh-install.sh -a -i
```

> ⚠️ Save the admin username and password shown at end of installation!

**Access Dashboard:**
```
https://YOUR_WAZUH_SERVER_PUBLIC_IP
Username: admin
Password: (shown after installation)
```

---

### Step 4 — Install Wazuh Agent on Victim

Connect to **Victim machine** and run:

```bash
# Add Wazuh repository
curl -s https://packages.wazuh.com/key/GPG-KEY-WAZUH | sudo gpg --no-default-keyring --keyring gnupg-ring:/usr/share/keyrings/wazuh.gpg --import
sudo chmod 644 /usr/share/keyrings/wazuh.gpg
echo "deb [signed-by=/usr/share/keyrings/wazuh.gpg] https://packages.wazuh.com/4.x/apt/ stable main" | sudo tee /etc/apt/sources.list.d/wazuh.list

# Install agent (replace with your Wazuh Server PRIVATE IP)
sudo apt update
sudo WAZUH_MANAGER='YOUR_WAZUH_SERVER_PRIVATE_IP' apt install wazuh-agent=4.9.2-1 -y

# Start agent
sudo systemctl start wazuh-agent
sudo systemctl enable wazuh-agent

# Verify agent is running
sudo systemctl status wazuh-agent
```

**Enable SSH Password Authentication:**
```bash
sudo nano /etc/ssh/sshd_config
# Change: PasswordAuthentication no → yes
# Change: KbdInteractiveAuthentication no → yes

sudo systemctl restart ssh

# Set password for ubuntu user
sudo passwd ubuntu
# Enter any simple password example: Password123
```

---

### Step 5 — Setup Kali Attacker

Connect to **Kali machine** and run:

```bash
# Update system
sudo apt update -y

# Install attack tools
sudo apt install nmap hydra wordlists -y

# Extract wordlist
sudo gunzip /usr/share/wordlists/rockyou.txt.gz

# Verify tools installed
nmap --version
hydra --version
```

---

### Step 6 — Verify Agent Connected

On **Wazuh Server** run:
```bash
sudo /var/ossec/bin/agent_control -l
```

You should see:
```
ID: 000, Name: wazuh-server (server), IP: 127.0.0.1, Active/Local
ID: 001, Name: victim-machine, IP: any, Active
```

Also verify in **Wazuh Dashboard:**
- Go to **Server Management → Endpoints Summary**
- Victim agent should show **Active** ✅

---

### Step 7 — Run Attacks from Kali

Connect to **Kali machine** and run:

```bash
# Attack 1 — Nmap Port Scan (replace with victim PRIVATE IP)
nmap -A YOUR_VICTIM_PRIVATE_IP

# Attack 2 — Hydra SSH Brute Force
hydra -l ubuntu -P /usr/share/wordlists/rockyou.txt ssh://YOUR_VICTIM_PRIVATE_IP -t 4 -V
```

---

### Step 8 — Monitor Alerts in Wazuh

1. Open browser → `https://YOUR_WAZUH_SERVER_PUBLIC_IP`
2. Login with admin credentials
3. Go to **Threat Intelligence → Threat Hunting**
4. You should see:
   - ✅ Brute Force detected
   - ✅ Password Guessing detected
   - ✅ Authentication failures logged
   - ✅ MITRE ATT&CK techniques mapped

---

### ⚠️ Important Notes

- Replace `YOUR_WAZUH_SERVER_PRIVATE_IP` with your actual Wazuh Server private IP
- Replace `YOUR_VICTIM_PRIVATE_IP` with your actual Victim private IP
- **Always terminate EC2 instances after use** to avoid AWS charges!
- m7i.flex.large costs ~$0.09/hour — terminate when done!
- This project is for **educational purposes only**

---

## 📝 License
This project is for educational purposes only. Do not use these techniques on systems you don't own.
