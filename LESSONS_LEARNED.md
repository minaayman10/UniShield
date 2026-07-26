# Lessons Learned

Practical issues encountered during design, implementation, and testing — and how they were resolved. These are the kind of details that only show up when you actually build and break things, not when you just read the docs.

---

### 1. VMware NAT Gateway is `.2`, not `.1`
A recurring error during FortiGate configuration was pointing the default route's next-hop at `192.168.183.1`. In VMware NAT mode, the gateway is always the `.2` address — `.1` is reserved for the host. Using the wrong address caused a complete Internet connectivity failure until corrected.

### 2. Cisco IOS `ip domain-lookup` Slows Down Configuration
Mistyping a command in Cisco IOS global config mode makes the router try to resolve it as a DNS hostname, causing a ~30 second delay before the error appears. Running `no ip domain-lookup` early eliminates this and speeds up config work significantly.

### 3. IPsec Site-to-Site VPN Requires Non-Overlapping Subnets
The FortiGate IPsec wizard refuses to complete if local and remote protected subnets overlap. This isn't a FortiGate quirk — it's a fundamental IPsec requirement. Any inter-campus connection needs distinct subnet ranges on each side.

### 4. SSL-VPN Split Tunnel Can't Use `all` as Destination
In split-tunnel mode, the firewall policy destination must list specific internal subnets. Using `all` creates a routing conflict, and the FortiGate GUI rejects the policy outright.

### 5. Port Security: Restrict vs. Shutdown
`Restrict` was chosen over `Shutdown` because it keeps the port operational while dropping unauthorized frames and logging the violation. `Shutdown` is more aggressive — it puts the interface into `error-disabled` state, requiring manual `shutdown` / `no shutdown` recovery.

| Violation Mode | Port State | Frames Dropped | Counter Incremented | Syslog Generated | Recovery |
|---|---|---|---|---|---|
| Protect | Remains UP | Yes | No | No | None — transparent |
| **Restrict** (used here) | Remains UP | Yes | Yes | Yes | None — automatic |
| Shutdown | ERROR-DISABLED | N/A (port down) | Yes | Yes | Manual |

### 6. Wazuh Dashboard Needs an Agent Filter
The Threat Hunting view shows events from every source at once unless filtered to a specific agent, making correlation difficult. Cisco IOS events forwarded via Syslog appear under the `wazuh-server` agent since the Manager receives them directly.

### 7. Cloud0 is the Only Bridge to the VMware Host Network
Connecting PNETLab devices to the Wazuh VM through generic bridge nodes (Cloud1, Cloud2, etc.) fails — those only reach internal PNETLab virtual bridges. Only **Cloud0 (Management)** bridges to the VMware host network, where the Wazuh VM actually lives.

---

## Future Work

| Enhancement | Priority | Description | Expected Benefit |
|---|---|---|---|
| Port Security on remaining switches | **High** | Sticky MAC + Syslog on Students, Management, Staff switches | Closes Layer 2 blind spots; full SIEM visibility |
| Wazuh FIM + VirusTotal | **High** | File Integrity Monitoring with hash comparison | Detects malware / unauthorized file changes |
| Windows Server CA | Medium | Internal Certificate Authority for SSL/TLS | Certificate-based VPN auth; removes PSK risk |
| WSUS Patch Management | Medium | Centralized Windows Update Services | Ensures patch compliance across endpoints |
| VLAN Access Control Matrix | Medium | Formal inter-VLAN policy document | Governance framework for future ACLs |
| Wireless Integration | Medium | WiFi controller + APs for students | Secure mobile/laptop access |
| Wazuh Active Response | Medium | Automatic IP blocking on brute-force detection | Automated response without manual intervention |
| Dynamic Routing (OSPF) | Low | For larger future network expansion | Better scalability beyond 6 VLANs |
| FortiAnalyzer Integration | Low | Advanced FortiGate log analysis | Richer reporting and compliance docs |
| IPv6 Support | Low | Plan IPv6 addressing for all VLANs | Future-proofing as IPv4 space shrinks |
