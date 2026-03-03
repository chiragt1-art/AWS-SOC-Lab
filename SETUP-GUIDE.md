# 📋 Step-by-Step Instructions — AWS SOC Lab

## ⚠️ Before You Start

- You need an **AWS Account** (free tier works)
- You need **PuTTY** installed on Windows
- You need a **Key Pair** (.ppk file) for PuTTY
- Estimated cost: ~$0.30 for 3 hours (m7i.flex.large)
- **Always terminate instances when done!**

---

## 🖥️ STEP 1 — Create Key Pair

1. Go to **AWS Console → EC2 → Key Pairs**
2. Click **Create key pair**
3. Name: `mykeypair`
4. Format: **.ppk** (for PuTTY)
5. Click **Create**
6. Save the `.ppk` file safely ✅

---

## 🖥️ STEP 2 — Launch Wazuh Server EC2

1. Go to **AWS Console → EC2 → Launch Instance**
2. Fill in:

| Setting | Value |
|---------|-------|
| Name | `Wazuh-Server` |
| OS | Ubuntu Server 22.04 LTS |
| Instance Type | m7i.flex.large |
| Key Pair | your key pair |
| Storage | 30GB gp3 |

3. Security Group — Add these rules:

| Type | Port | Source |
|------|------|--------|
| All Traffic | All | My IP |
| Custom TCP | 1514 | 0.0.0.0/0 |
| Custom TCP | 1515 | 0.0.0.0/0 |
| Custom UDP | 1514 | 0.0.0.0/0 |
| HTTPS | 443 | My IP |

4. Click **Launch Instance** ✅

---

## 🖥️ STEP 3 — Launch Victim EC2

1. Go to **AWS Console → EC2 → Launch Instance**
2. Fill in:

| Setting | Value |
|---------|-------|
| Name | `Victim-Machine` |
| OS | Ubuntu Server 22.04 LTS |
| Instance Type | t3.micro |
| Key Pair | your key pair |
| Storage | 15GB gp3 |

3. Security Group:

| Type | Port | Source |
|------|------|--------|
| All Traffic | All | 0.0.0.0/0 |

4. Click **Launch Instance** ✅

---

## 🖥️ STEP 4 — Launch Kali Attacker EC2

1. Go to **AWS Console → EC2 → Launch Instance**
2. Click **Browse more AMIs**
3. Click **AWS Marketplace AMIs**
4. Search: `Kali Linux`
5. Select: **Kali Linux by Kali** ($0.00/hr)
6. Fill in:

| Setting | Value |
|---------|-------|
| Name | `Kali-Attacker` |
| Instance Type | t3.micro |
| Key Pair | your key pair |
| Storage | 15GB gp3 |

7. Security Group:

| Type | Port | Source |
|------|------|--------|
| All Traffic | All | My IP |

8. Click **Launch Instance** ✅

---

## 🔌 STEP 5 — Connect via PuTTY

### For Ubuntu Instances:
1. Open PuTTY
2. Host Name: `ubuntu@YOUR_PUBLIC_IP`
3. Port: `22`
4. Go to **Connection → SSH → Auth → Credentials**
5. Browse and select your `.ppk` file
6. Go to **Connection** → Set keepalive to `60`
7. Click **Open**

### For Kali Instance:
1. Same steps but Host Name: `kali@YOUR_PUBLIC_IP`

---

## ⚙️ STEP 6 — Install Wazuh on Server

Connect to **Wazuh Server** terminal and run:

```bash
# Update system
sudo apt update && sudo apt upgrade -y

# Add 4GB swap memory
sudo fallocate -l 4G /swapfile
sudo chmod 600 /swapfile
sudo mkswap /swapfile
sudo swapon /swapfile

# Download Wazuh installer
curl -sO https://packages.wazuh.com/4.9/wazuh-install.sh

# Install Wazuh (takes 10-15 minutes)
sudo bash wazuh-install.sh -a -i
```

> ⚠️ At end of installation you will see:
> ```
> Username: admin
> Password: XXXXXXXXXX
> ```
> **Save this password!** You need it to login to dashboard.

---

## 🌐 STEP 7 — Access Wazuh Dashboard

1. Open browser
2. Go to: `https://YOUR_WAZUH_SERVER_PUBLIC_IP`
3. You'll see security warning — type `thisisunsafe` in Chrome
4. Login:
   - Username: `admin`
   - Password: (saved from installation)
5. Dashboard should load ✅

---

## 🤝 STEP 8 — Install Wazuh Agent on Victim

Connect to **Victim Machine** terminal and run:

```bash
# Add Wazuh GPG key
curl -s https://packages.wazuh.com/key/GPG-KEY-WAZUH | sudo gpg --no-default-keyring --keyring gnupg-ring:/usr/share/keyrings/wazuh.gpg --import
sudo chmod 644 /usr/share/keyrings/wazuh.gpg

# Add Wazuh repository
echo "deb [signed-by=/usr/share/keyrings/wazuh.gpg] https://packages.wazuh.com/4.x/apt/ stable main" | sudo tee /etc/apt/sources.list.d/wazuh.list

# Update and install agent
# ⚠️ Replace 172.31.92.121 with YOUR Wazuh Server PRIVATE IP
sudo apt update
sudo WAZUH_MANAGER='172.31.92.121' apt install wazuh-agent=4.9.2-1 -y

# Start and enable agent
sudo systemctl start wazuh-agent
sudo systemctl enable wazuh-agent

# Check agent is running
sudo systemctl status wazuh-agent
```

You should see: `Active: active (running)` ✅

---

## 🔓 STEP 9 — Enable SSH Password Auth on Victim

```bash
# Edit SSH config
sudo nano /etc/ssh/sshd_config

# Find and change these lines:
# PasswordAuthentication no  →  PasswordAuthentication yes
# KbdInteractiveAuthentication no  →  KbdInteractiveAuthentication yes

# Save: Ctrl+X → Y → Enter

# Restart SSH
sudo systemctl restart ssh

# Set password for ubuntu user
sudo passwd ubuntu
# Enter password: Password123
```

---

## ✅ STEP 10 — Verify Agent Connected

On **Wazuh Server** terminal run:
```bash
sudo /var/ossec/bin/agent_control -l
```

You should see:
```
ID: 000, Name: wazuh-server, IP: 127.0.0.1, Active/Local
ID: 001, Name: victim-machine, IP: any, Active ✅
```

Also check Wazuh Dashboard:
- Go to **Server Management → Endpoints Summary**
- Victim should show **Active** ✅

---

## 🛠️ STEP 11 — Setup Kali Attacker

Connect to **Kali terminal** and run:

```bash
# Update Kali
sudo apt update -y

# Install Nmap and Hydra
sudo apt install nmap hydra wordlists -y

# Extract rockyou wordlist
sudo gunzip /usr/share/wordlists/rockyou.txt.gz

# Verify installation
nmap --version
hydra --version
```

---

## ⚔️ STEP 12 — Run Attacks

Connect to **Kali terminal** and run:

```bash
# ⚠️ Replace 172.31.67.164 with YOUR Victim PRIVATE IP

# Attack 1 — Nmap Port Scan
nmap -A 172.31.67.164

# Attack 2 — Hydra SSH Brute Force
hydra -l ubuntu -P /usr/share/wordlists/rockyou.txt ssh://172.31.67.164 -t 4 -V
```

Hydra will find password and show:
```
[22][ssh] host: 172.31.67.164  login: ubuntu  password: 12345 ✅
```

---

## 🚨 STEP 13 — Monitor in Wazuh Dashboard

1. Open browser → `https://YOUR_WAZUH_SERVER_PUBLIC_IP`
2. Login with admin credentials
3. Go to **Threat Intelligence → Threat Hunting**
4. You should see:

| Alert | Status |
|-------|--------|
| Brute Force | ✅ Detected |
| Password Guessing | ✅ Detected |
| Authentication Failures | ✅ Logged |
| MITRE ATT&CK Mapping | ✅ Active |

---

## 💰 STEP 14 — Terminate Instances (Important!)

After you're done:
1. Go to **AWS Console → EC2 → Instances**
2. Select **ALL instances**
3. Click **Instance State → Terminate**
4. Confirm ✅

> ⚠️ m7i.flex.large costs ~$0.09/hour
> Always terminate to avoid unexpected charges!

---

## 🔧 Troubleshooting

| Problem | Solution |
|---------|----------|
| PuTTY connection timeout | Update security group with current IP |
| Agent not connecting | Check ports 1514/1515 open in security group |
| Dashboard not loading | Check port 443 open in security group |
| Hydra connection error | Enable password auth on victim |
| Disk full on server | Run `sudo journalctl --vacuum-size=100M` |
| Agent version mismatch | Install exact version 4.9.2-1 |

---

## 📞 Need Help?

- Wazuh Documentation: https://documentation.wazuh.com
- AWS Documentation: https://docs.aws.amazon.com
- Kali Linux Docs: https://www.kali.org/docs/

---

*This project is for educational purposes only. Do not use these techniques on systems you don't own.*
