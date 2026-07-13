# 🛡️ OPNsense Enterprise Firewall Implementation

A complete implementation guide for building a secure and highly available network infrastructure using **OPNsense 26.x**.

This repository documents my practical implementation of OPNsense, covering everything from installation to advanced networking features such as VLANs, OpenVPN, NAT, Multi-WAN Failover, and Backup & Restore.

The goal of this project is not only to configure OPNsense but also to understand the purpose behind every implementation and maintain detailed documentation for future deployments.

---

# 📖 Project Overview

This project covers the implementation of:

- OPNsense Installation
- Network Design
- VLAN Configuration
- DHCP Server Configuration
- Static Server Network
- Firewall Rules
- Network Address Translation (NAT)
- OpenVPN Remote Access
- Multi-WAN Automatic Failover
- Backup & Restore

---

# 🏗️ Network Architecture

> **Network Design Diagram**

![Network Design](images/network-design.png)

---

# 🚀 Features

- Secure Network Segmentation using VLANs
- DHCP and Static Network Configuration
- Secure Firewall Policies
- Application Publishing using NAT
- OpenVPN Remote Access
- Multi-WAN Automatic Failover
- Gateway Monitoring
- Policy-Based Routing
- Backup & Restore Strategy

---

# 📂 Project Structure

```text
OPNsense-Enterprise-Firewall/

│
├── README.md
├── conclusion.md
│
├── 01-installation/
│
├── 02-network-design/
│
├── 03-vlan-configuration/
│     ├── vlan10-user-network.md
│     └── vlan20-server-network.md
│
├── 04-firewall-rules/
│
├── 05-nat-configuration/
│
├── 06-openvpn-configuration/
│
├── 07-multiwan-failover/
│
├── 08-backup-and-restore/
```

---

# 📑 Documentation

| Module | Status |
|----------|:------:|
| Installation | ✅ |
| Network Design | ✅ |
| VLAN Configuration | ✅ |
| Firewall Rules | ✅ |
| NAT Configuration | ✅ |
| OpenVPN Configuration | ✅ |
| Multi-WAN Failover | ✅ |
| Backup & Restore | ✅ |

---

# 🖼️ Architecture Diagrams

This repository contains architecture diagrams for:

- Network Design
- Multi-WAN Failover

---

# 🛠️ Technologies Used

- OPNsense 26.x
- OpenVPN
- VLAN
- DHCP
- NAT
- Multi-WAN
- Gateway Groups
- Policy-Based Routing

---

# 📚 Purpose of this Repository

This repository was created to:

- Document a complete OPNsense implementation.
- Maintain a reusable deployment reference.
- Demonstrate practical networking knowledge.
- Share implementation experience with the community.

---

# 🚀 Future Improvements

Planned enhancements include:

- IDS / IPS (Suricata)
- WireGuard VPN
- High Availability (CARP)
- Centralized Monitoring Integration
- Additional Security Hardening
- Infrastructure Automation

---

# 📜 License

This project is intended for educational and learning purposes.