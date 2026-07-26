# 🛡️ UniShield — University Network Security

**Enterprise-grade university network security lab** — designed, implemented, and validated end-to-end inside **PNETLab** + **VMware Workstation Pro 17**.

Graduation Project — Department of Electrical Engineering (Electronics & Communications), Faculty of Engineering, Minia University — 2026

![Status](https://img.shields.io/badge/status-completed-brightgreen)
![Cisco](https://img.shields.io/badge/Cisco%20IOS-15.2-blue)
![FortiGate](https://img.shields.io/badge/FortiOS-7.0-red)
![Wazuh](https://img.shields.io/badge/Wazuh%20SIEM-4.9-orange)
![Windows](https://img.shields.io/badge/Windows%20Server-2016-informational)

---

## 🗺️ Network Topology

![Network Topology](topology/University_Network_Topology.png)

Full topology details, VLAN diagram, IP addressing, and architecture breakdown: **[topology/](topology/)**

---

## 📖 Overview

This project integrates five domains into one cohesive, defense-in-depth architecture:

1. **Network Architecture** — 6-VLAN campus network, dual-core Cisco routers, Router-on-a-Stick, redundant WAN links
2. **Perimeter Security** — FortiGate NGFW (IPS, App Control, Web Filtering, SSL Deep Inspection, SSL-VPN, IPsec)
3. **Endpoint Security** — Port Security (sticky MAC) + Access Control Lists
4. **Security Monitoring** — Wazuh SIEM, MITRE ATT&CK–aligned, custom detection rules, live attack simulation
5. **Identity Management** — Windows Server 2016 (AD DS, DNS, GPO)

📄 **[Read the full 120-page project report →](docs/UniShield_Full_Report.pdf)**

---

## 🔐 Attack Detection Results

| Attack | Rule | Hits | Level | Result |
|---|---|---|---|---|
| SSH Brute Force | Rule 5763 | 78 | 10 | ✅ |
| Nmap Network Scan | Rule 20101 | 195 | 6 | ✅ |
| Sudo Privilege Escalation | Custom Rule `100120` | 410 | 10 | ✅ |
| Unauthorized Account Creation | Custom Rule `100110` | 424 | 8 | ✅ |

Screenshots: [`screenshots/wazuh/`](screenshots/wazuh/)

---

## 📂 Repository Structure

```
UniShield/
├── README.md
├── docs/
│   └── UniShield_Full_Report.pdf
├── topology/
│   ├── README.md
│   ├── University_Network_Topology.png     # ⭐ full topology
│   ├── VLAN_Segmentation.png
│   ├── Network_Architecture.png            # simplified flow diagram
│   ├── Defense_in_Depth.png
│   └── IP_Addressing.pdf                   # full interface IP scheme
├── cisco/
│   ├── ACLs/
│   │   └── vlan30_vlan60_acl.txt          # real config, Ch. 5.2
│   └── PortSecurity/
│       └── faculty_switch_port_security.txt   # real config, Ch. 5.3
├── fortigate/
│   ├── interface_configuration.png
│   ├── firewall_policies.png
│   ├── ips_profile.png
│   └── ssl_deep_inspection.png
├── windows-server/
│   └── GPO/
│       ├── student_ou_gpos.png
│       ├── staff_ou_gpos.png
│       ├── management_ou_gpos.png
│       └── policy_enforcement_verification.png
├── wazuh/
│   └── custom-rules/
│       └── local_rules.xml                # Rules 100110 & 100120, deployable as-is
└── screenshots/
    └── wazuh/                              # attack detection dashboards
```

> **Note on completeness:** Router/switch CLI and Wazuh rules are captured as real, deployable config text. FortiGate policies and Windows GPOs were configured through their GUIs in the original lab, so those are documented here as annotated screenshots rather than exportable config files — that's an accurate reflection of how the project was built, not a placeholder.

---

## ⚙️ How to Reproduce the Lab

1. Install **VMware Workstation Pro 17** and **PNETLab** (community edition is sufficient).
2. Import Cisco IOS images (c7200 / IOSv) matching version 15.2 into PNETLab.
3. Build the topology per [`topology/University_Network_Topology.png`](topology/University_Network_Topology.png): 2 routers (R1, R2), access/distribution switches, 6 VLANs.
4. Deploy `FortiGate VM64-KVM` (FortiOS 7.0) as a separate VM, bridge it to PNETLab as the Internet-facing gateway.
5. Deploy `Wazuh 4.9` on an Ubuntu 22.04 LTS VM; bridge it via VMware's Cloud0 network to receive Syslog from R1/R2.
6. Apply the configs in [`cisco/ACLs/`](cisco/ACLs/) and [`cisco/PortSecurity/`](cisco/PortSecurity/) to the relevant interfaces.
7. Load [`wazuh/custom-rules/local_rules.xml`](wazuh/custom-rules/local_rules.xml) into `/var/ossec/etc/rules/`, then validate and restart:
   ```bash
   /var/ossec/bin/wazuh-analysisd -t
   systemctl restart wazuh-manager
   ```
8. Deploy Windows Server 2016, promote to a Domain Controller, and configure OUs/GPOs as documented in the report (Chapter 9).
9. Reproduce the attack simulations (SSH brute force, Nmap scan, sudo escalation, `useradd`) and confirm detection in the Wazuh dashboard.

---

## 🛠️ Technologies

Cisco IOS 15.2 · FortiGate VM64-KVM (FortiOS 7.0) · Wazuh SIEM 4.9 · Ubuntu Server 22.04 LTS · Windows Server 2016 · VMware Workstation Pro 17 · PNETLab

## 🎯 Skills Demonstrated

Enterprise Networking · VLAN Design · Firewall Administration · SIEM Engineering · Detection Engineering · Blue Team Operations · MITRE ATT&CK Mapping · Windows Server Administration · Network Security

## 🚧 Future Improvements

- Dynamic routing (OSPF)
- IPv6 implementation
- WSUS centralized patch management
- Windows Server Certificate Authority (PKI)
- Wazuh File Integrity Monitoring + VirusTotal integration
- High-availability firewall clustering

---

## 👥 Team

| Name |
|---|
| Mina Ayman Mehany |
| Mina Ezzat Boshra |
| Keroles Hany Gerges |
| Amir Ashraf Zaghloul |
| Khaled Mohamed Fahem |

**Supervisor:** Prof. Dr. Gerges Mansour Salama — Electrical Engineering Department, Faculty of Engineering, Minia University

---

## 📜 License

Shared for educational and portfolio purposes. Please credit the original authors if referencing this architecture or ruleset.
