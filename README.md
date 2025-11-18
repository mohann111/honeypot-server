# Cowrie SSH Honeypot Deployment

A complete guide and implementation for deploying a Cowrie SSH honeypot on virtualized Linux infrastructure for threat intelligence gathering and attack pattern analysis.

## 📋 Overview

This repository contains all resources needed to deploy a production-grade SSH honeypot using Cowrie, capture real attack data, analyze attacker behavior, and implement automated threat response mechanisms.

**Project Status:** ✅ Complete & Operational

---

## 🎯 Features

- ✅ **Full Honeypot Deployment** - Complete setup guide from VM creation to operational honeypot
- ✅ **Real Attack Capture** - JSON-formatted logs capturing credentials, commands, and session details
- ✅ **Automated Threat Response** - fail2ban integration for automatic attacker IP blocking
- ✅ **Threat Intelligence** - Geolocation analysis, credential patterns, attacker behavior classification
- ✅ **Forensic Capability** - TTY session recordings for complete attack playback
- ✅ **Comprehensive Documentation** - Step-by-step tutorials, quick reference guides, troubleshooting

---


## 🚀 Quick Start (5 minutes)

### Prerequisites
- Ubuntu 20.04 LTS or 22.04 LTS VM
- 4GB RAM minimum
- 20GB disk space
- SSH access

### Installation

```bash
# Clone repository
git clone https://github.com/yourusername/cowrie-honeypot-deployment.git
cd cowrie-honeypot-deployment

# Run automated setup
chmod +x scripts/setup.sh
sudo ./scripts/setup.sh

# Start honeypot
sudo su - cowrie
cd ~/cowrie
cowrie start

# Verify running
cowrie status
```

See [QUICK_START.md](docs/QUICK_START.md) for detailed instructions.

---

## 📚 Complete Installation (45 minutes)

For comprehensive setup with detailed explanations, see [FULL_TUTORIAL.md](docs/FULL_TUTORIAL.md)

**Installation phases:**
1. VM environment preparation
2. System package installation
3. Cowrie installation and configuration
4. Network security (iptables + authbind)
5. Honeypot startup
6. fail2ban integration
7. Attack capture and analysis

---

## 🔧 Tools & Technologies

| Component | Version | Purpose |
|-----------|---------|---------|
| **Cowrie** | 2.8.2+ | SSH honeypot |
| **Ubuntu** | 22.04 LTS | Base OS |
| **Python** | 3.12+ | Virtual environment |
| **iptables** | 5.2+ | Port redirection |
| **fail2ban** | 1.0.2+ | Threat response |
| **VirtualBox** | 7.0+ | Virtualization |

---

## 📊 Key Statistics from Deployment

- **SSH Sessions Captured:** 3
- **Successful Authentications:** 1
- **Attacker IPs:** 1 (192.168.100.9 in test environment)
- **Credentials Captured:** 3 password attempts
- **Commands Logged:** Complete command history
- **Log Entries:** 8+ structured JSON events
- **Session Duration:** Up to 117 seconds

---

## 🛡️ Security Features

✅ **Privilege Separation** - Honeypot runs as non-root user (cowrie)  
✅ **Network Isolation** - Isolated VM with zero production impact  
✅ **Port Redirection** - iptables NAT transparent to attacker  
✅ **Automated Response** - fail2ban blocking after threshold  
✅ **Comprehensive Logging** - JSON format for analysis and SIEM integration  
✅ **Session Recording** - TTY playback for forensic investigation  

---

## 📖 Documentation

### Getting Started
- [QUICK_START.md](docs/QUICK_START.md) - 15-minute setup
- [FULL_TUTORIAL.md](docs/FULL_TUTORIAL.md) - Complete guide with 13 phases
- [QUICK_REFERENCE.md](docs/QUICK_REFERENCE.md) - Command reference

### Advanced Topics
- [ARCHITECTURE.md](docs/ARCHITECTURE.md) - Network design and security architecture
- [ANALYSIS.md](docs/ANALYSIS.md) - Log analysis techniques
- [VISUALIZATION.md](docs/VISUALIZATION.md) - Geolocation and attack visualization

### Troubleshooting
- [TROUBLESHOOTING.md](docs/TROUBLESHOOTING.md) - Common issues and solutions
- [DEPLOYMENT_CHECKLIST.md](docs/DEPLOYMENT_CHECKLIST.md) - Pre-deployment verification

---

## 🔍 Log Analysis

### Analyze Attack Logs

```bash
# Count total events
cat ~/cowrie/var/log/cowrie/cowrie.json | wc -l

# Extract attacker IPs
cat ~/cowrie/var/log/cowrie/cowrie.json | jq -r '.src_ip' | sort -u

# Top attempted usernames
cat ~/cowrie/var/log/cowrie/cowrie.json | jq -r '.username' | sort | uniq -c | sort -rn

# Top attempted passwords
cat ~/cowrie/var/log/cowrie/cowrie.json | jq -r '.password' | sort | uniq -c | sort -rn

# Commands executed
cat ~/cowrie/var/log/cowrie/cowrie.json | grep "command.input" | jq -r '.input'
```

### Run Analysis Scripts

```bash
# Comprehensive analysis
python3 scripts/cowrie_analyzer.py ~/cowrie/var/log/cowrie/cowrie.json

# Generate visualizations
python3 scripts/visualize_attacks.py ~/cowrie/var/log/cowrie/cowrie.json

# Create daily report
python3 scripts/daily_report.py
```

---

## 🎯 Use Cases

1. **Threat Intelligence Gathering** - Understand attacker behavior and techniques
2. **Security Training** - Learn about attack patterns in controlled environment
3. **SIEM Integration** - Forward logs to centralized monitoring
4. **Credential Harvesting** - Understand password selection patterns
5. **Incident Response Practice** - Analyze and respond to captured attacks
6. **Research** - Study botnet behavior and malware campaigns

---

## 🔐 Fail2ban Integration

### Monitor Threat Response

```bash
# Check jail status
sudo fail2ban-client status cowrie

# View banned IPs
sudo fail2ban-client status cowrie | grep "Banned IP"

# Unban specific IP
sudo fail2ban-client set cowrie unbanip 192.168.1.100

# View fail2ban logs
sudo tail -f /var/log/fail2ban.log
```

### Configuration

Edit `/etc/fail2ban/jail.d/cowrie.local`:
- `maxretry` - Number of attempts before ban (default: 3)
- `findtime` - Detection window in seconds (default: 3600 = 1 hour)
- `bantime` - Ban duration in seconds (default: 86400 = 24 hours)

---

## 📈 Scalability & Production Deployment

### For Production:

1. **Deploy Multiple Honeypots** - Across different geographic locations
2. **Integrate with SIEM** - Forward logs to Splunk, ELK, or Wazuh
3. **Automate Analysis** - Run scripts on schedule
4. **Share Intelligence** - Send IoCs to threat communities
5. **Monitor 24/7** - Maintain continuous operation
6. **Add Services** - Deploy honeypots for FTP, HTTP, Database

---

## 🤝 Contributing

Contributions welcome! Please:
1. Fork the repository
2. Create feature branch (`git checkout -b feature/improvement`)
3. Commit changes (`git commit -am 'Add improvement'`)
4. Push to branch (`git push origin feature/improvement`)
5. Submit Pull Request

---

## 📝 License

MIT License - See [LICENSE](LICENSE) file for details

---

## ⚠️ Disclaimer

This honeypot should only be deployed in controlled environments with proper authorization. Ensure compliance with local laws and organizational policies before deployment.

---

## 📞 Support & Contact

- **Issues:** GitHub Issues
- **Discussions:** GitHub Discussions
- **Email:** your-email@example.com
- **Documentation:** See [docs/](docs/) directory

---

## 🎓 Learning Resources

- [Cowrie Official Documentation](https://cowrie.readthedocs.io/)
- [fail2ban Documentation](https://www.fail2ban.org/)
- [iptables Tutorial](https://www.netfilter.org/projects/iptables/)
- [SSH Protocol (RFC 4251-4256)](https://tools.ietf.org/html/rfc4251)
- [Threat Intelligence Basics](https://www.sans.org/white-papers/)

---

## 📊 Project Statistics

- **Development Time:** 45+ hours
- **Lines of Code:** 2,000+
- **Configuration Files:** 5
- **Documentation Pages:** 10+
- **Code Examples:** 50+
- **Supported Platforms:** Ubuntu 20.04 LTS, 22.04 LTS

---

## 🎉 Acknowledgments

- Cowrie project team for excellent honeypot framework
- fail2ban developers for threat response automation
- Security community for best practices and guidance

---

## 🔄 Version History

**v1.0.0** (November 17, 2025)
- Initial release
- Complete deployment guide
- Automated setup scripts
- Analysis tools
- Comprehensive documentation

---

**Last Updated:** November 17, 2025  
**Status:** ✅ Production Ready  
**Maintained by:** Security Team

---

## Star this Repository! ⭐

If this project helped you, please star it to show support!

```
███████████ Cowrie SSH Honeypot Deployment ███████████
█ Real Attack Capture | Threat Intelligence | Security █
█████████████████████████████████████████████████████████
```

---

**Ready to deploy? Start with [QUICK_START.md](docs/QUICK_START.md)** 🚀
