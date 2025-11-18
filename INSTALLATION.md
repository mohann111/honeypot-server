# Installation Instructions

Complete step-by-step installation guide for Cowrie SSH honeypot deployment.

## System Requirements

- **OS:** Ubuntu 20.04 LTS or 22.04 LTS
- **RAM:** Minimum 4GB (recommended 8GB)
- **Disk Space:** Minimum 20GB free space
- **Network:** Internet access for package downloads
- **Access:** sudo/root privileges

## Phase 1: Environment Preparation (5 minutes)

### 1.1 Update System

```bash
sudo apt-get update
sudo apt-get upgrade -y
```

### 1.2 Verify System Information

```bash
uname -a
lsb_release -a
df -h
```

## Phase 2: Install Dependencies (10 minutes)

```bash
sudo apt-get install -y \
  git \
  python3-virtualenv \
  libssl-dev \
  libffi-dev \
  build-essential \
  libpython3-dev \
  python3-minimal \
  authbind \
  virtualenv \
  python3-pip \
  vim \
  jq
```

## Phase 3: Create Cowrie User (2 minutes)

```bash
# Create non-privileged user
sudo adduser --disabled-password --gecos '' cowrie

# Verify creation
id cowrie
```

## Phase 4: Install Cowrie (15 minutes)

```bash
# Switch to cowrie user
sudo su - cowrie

# Clone repository
git clone https://github.com/cowrie/cowrie
cd cowrie

# Create virtual environment
python3 -m venv cowrie-env

# Activate environment
source cowrie-env/bin/activate

# Upgrade pip
pip install --upgrade pip

# Install dependencies (takes 10-15 minutes)
pip install --upgrade -r requirements.txt

# Install cowrie command
pip install -e .

# Verify
which cowrie
cowrie --help
```

## Phase 5: Network Configuration (10 minutes)

### 5.1 Setup Authbind

```bash
# Exit cowrie user first
exit

# Create port bindings
sudo touch /etc/authbind/byport/22
sudo chown cowrie:cowrie /etc/authbind/byport/22
sudo chmod 770 /etc/authbind/byport/22

sudo touch /etc/authbind/byport/23
sudo chown cowrie:cowrie /etc/authbind/byport/23
sudo chmod 770 /etc/authbind/byport/23

# Verify
ls -la /etc/authbind/byport/{22,23}
```

### 5.2 Configure iptables

```bash
# Add NAT rules
sudo iptables -t nat -A PREROUTING -p tcp --dport 22 -j REDIRECT --to-port 2222
sudo iptables -t nat -A PREROUTING -p tcp --dport 23 -j REDIRECT --to-port 2223

# Verify
sudo iptables -t nat -L -n -v | grep REDIRECT

# Install persistence
sudo apt-get install iptables-persistent -y
sudo netfilter-persistent save
```

### 5.3 Change SSH Port

```bash
# Edit SSH config
sudo nano /etc/ssh/sshd_config

# Find and change:
# Port 22  →  Port 22222

# Save and exit (Ctrl+O, Enter, Ctrl+X)

# Restart SSH
sudo systemctl restart sshd

# Verify
sudo ss -tlnp | grep ssh
```

## Phase 6: Start Honeypot (5 minutes)

```bash
# Switch to cowrie user
sudo su - cowrie

# Go to directory
cd ~/cowrie

# Activate venv
source cowrie-env/bin/activate

# Start honeypot
cowrie start

# Check status
cowrie status

# Verify listening
sudo ss -tlnp | grep 2222

# Monitor logs
tail -f var/log/cowrie/cowrie.json
```

## Phase 7: Setup fail2ban (10 minutes)

```bash
# Exit cowrie user
exit

# Install fail2ban
sudo apt-get install fail2ban -y

# Create filter
sudo nano /etc/fail2ban/filter.d/cowrie.conf

# Add:
# [Definition]
# failregex = .*"eventid":\s*"cowrie\.login\.failed".*"src_ip":\s*"<HOST>".*
# ignoreregex =

# Create jail
sudo nano /etc/fail2ban/jail.d/cowrie.local

# Add:
# [cowrie]
# enabled = true
# port = ssh,telnet
# filter = cowrie
# logpath = /home/cowrie/cowrie/var/log/cowrie/cowrie.json
# maxretry = 3
# findtime = 3600
# bantime = 86400
# action = iptables-multiport[name=cowrie, port="22,23", protocol=tcp]

# Restart fail2ban
sudo systemctl restart fail2ban

# Verify
sudo fail2ban-client status cowrie
```

## Phase 8: Test Honeypot (5 minutes)

### From another terminal:

```bash
# SSH to honeypot
ssh root@YOUR_VM_IP

# Try passwords:
# 123456
# admin
# password
# root

# Exit
exit
```

### Monitor in honeypot terminal:

```bash
# Watch logs in real-time
sudo su - cowrie
cd cowrie
tail -f var/log/cowrie/cowrie.json | jq '.'
```

## Verification Checklist

- [ ] Ubuntu system updated
- [ ] Dependencies installed (30 packages)
- [ ] Cowrie user created
- [ ] Cowrie cloned and installed
- [ ] Virtual environment operational
- [ ] Authbind configured (ports 22, 23)
- [ ] iptables rules set and persistent
- [ ] SSH moved to port 22222
- [ ] Honeypot starts without errors
- [ ] SSH listening on port 2222
- [ ] fail2ban installed and active
- [ ] fail2ban filter and jail configured
- [ ] Test SSH connection succeeds
- [ ] Logs captured in JSON format

## Troubleshooting Installation

**Pip install fails**
```bash
# Update pip and try again
pip install --upgrade pip
pip install --upgrade -r requirements.txt
```

**Port already in use**
```bash
# Check what's using the port
sudo ss -tlnp | grep 2222
# Kill process or change port
```

**Authbind permission denied**
```bash
# Verify permissions
ls -la /etc/authbind/byport/
# Should be: cowrie:cowrie
```

**fail2ban not detecting logs**
```bash
# Check permissions on log file
ls -la /home/cowrie/cowrie/var/log/cowrie/cowrie.json
# Test filter regex
sudo fail2ban-regex /home/cowrie/cowrie/var/log/cowrie/cowrie.json \
  /etc/fail2ban/filter.d/cowrie.conf
```

## Post-Installation

After successful installation:

1. Run analysis: `python3 scripts/cowrie_analyzer.py`
2. Generate visualizations: `python3 scripts/visualize_attacks.py`
3. Setup daily reports: `python3 scripts/daily_report.py`
4. Monitor with: `tail -f ~/cowrie/var/log/cowrie/cowrie.json`

## Next Steps

- Read [FULL_TUTORIAL.md](FULL_TUTORIAL.md) for detailed explanation
- Review [ARCHITECTURE.md](ARCHITECTURE.md) for security design
- Check [TROUBLESHOOTING.md](TROUBLESHOOTING.md) for issues
- See [QUICK_REFERENCE.md](QUICK_REFERENCE.md) for commands

---

**Installation complete!** Your honeypot is ready to capture attacks. 🎉
