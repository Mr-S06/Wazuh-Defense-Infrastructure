# Wazuh-Defense-Infrastructure
This project documents a fully functional Security Operations Center (SOC) home lab built using VirtualBox, pfSense, and Wazuh SIEM.
The lab is designed to simulate real-world blue team operations, including log collection, firewall telemetry ingestion, host-based monitoring, and attack detection.
The primary goal of this lab is to gain practical SOC experience in log analysis, detection engineering, troubleshooting, and SIEM operations.

# 🛡️ Cybersecurity Home Lab – Wazuh Integration
<img width="1852" height="956" alt="dashboard wazuh - Copy" src="https://github.com/user-attachments/assets/ad4189d6-abe8-4b30-b940-3695e7f0b13c" />

A comprehensive security monitoring environment built to demonstrate enterprise-grade SIEM capabilities, threat detection, and incident response using Wazuh open-source security platform.

![Wazuh](https://img.shields.io/badge/Wazuh-4.12-blue)
![Platform](https://img.shields.io/badge/Platform-Linux%20%7C%20Windows-lightgrey)
![License](https://img.shields.io/badge/License-MIT-green)

## 📋 Table of Contents

- [Overview](#overview)
- [Architecture](#architecture)
- [Key Features](#key-features)
- [Lab Environment](#lab-environment)
- [Installation & Setup](#installation--setup)
- [Implemented Capabilities](#implemented-capabilities)
- [Use Cases & Demonstrations](#use-cases--demonstrations)
- [Screenshots](#screenshots)
- [Skills Demonstrated](#skills-demonstrated)
- [Future Enhancements](#future-enhancements)
- [Documentation](#documentation)
- [Acknowledgments](#acknowledgments)
- [Contact](#contact)


##  Overview

This project showcases a fully functional Security Operations Center (SOC) environment built in a home lab setting. The infrastructure integrates **Wazuh Manager, Indexer, and Dashboard** with multiple endpoints and network security components to provide real-time threat detection, log analysis, and incident response capabilities.

### Project Objectives

- Deploy enterprise-grade SIEM infrastructure in a virtualized environment
- Implement comprehensive endpoint and network monitoring
- Demonstrate practical security operations and incident response workflows
- Simulate real-world attack scenarios for detection validation
- Build expertise in security monitoring, log analysis, and dashboarding

##  Architecture

### Network Topology

```
┌──────────────────────────────────────────────────────────────────┐
│                       VirtualBox Host                             │
│                                                                  │
│  ┌──────────────────────── WAN (NAT) ─────────────────────────┐ │
│  │                                                             │ │
│  │                    [ Internet ]                             │ │
│  │                                                             │ │
│  └───────────────▲─────────────────────────────────────────────┘ │
│                  │                                                 │
│          ┌───────┴────────┐                                        │
│          │   pfSense       │                                        │
│          │ Firewall/Router │                                        │
│          │ WAN: NAT        │                                        │
│          │ LAN: Host-Only  │                                        │
│          │ 192.168.56.X    │                                        │
│          └───────┬────────┘                                        │
│                  │  Syslog (UDP 514)                                │
│                  ▼                                                  │
│  ┌────────────────────────────────────────────────────────────┐   │
│  │                    Wazuh Server                              │   │
│  │                192.168.56.X                                  │   │
│  │                                                            │   │
│  │  ┌─────────────┐   ┌─────────────┐   ┌─────────────────┐ │   │
│  │  │ Wazuh       │   │ Wazuh       │   │ Wazuh Dashboard │ │   │
│  │  │ Manager     │   │ Indexer     │   │ (OpenSearch UI) │ │   │
│  │  │ (1514/TCP)  │   │ (9200/TCP)  │   │ (5601/TCP)      │ │   │
│  │  └─────────────┘   └─────────────┘   └─────────────────┘ │   │
│  │                                                            │   │
│  │  Raw logs: /var/ossec/logs/archives/archives.json           │   │
│  │  Alerts:  /var/ossec/logs/alerts/alerts.json                │   │
│  └───────────────┬───────────────────────┬───────────────────┘   │
│                  │                       │                         │
│        Agent (1514/TCP)         Agent (1514/TCP)                   │
│                  │                       │                         │
│        ┌─────────▼────────┐   ┌──────────▼─────────┐              │
│        │   Windows 10     │   │   Ubuntu Linux     │              │
│        │   Wazuh Agent    │   │   Wazuh Agent      │              │
│        │ Auth / Proc logs │   │ Syslog / AppArmor  │              │
│        └─────────┬────────┘   └──────────┬─────────┘              │
│                  │                       │                         │
│                  └───────────────┬───────┘                         │
│                                  │                                 │
│                         ┌────────▼────────┐                        │
│                         │   Kali Linux     │                        │
│                         │   Attacker       │                        │
│                         │ Scans / Brute    │                        │
│                         └─────────────────┘                        │
│                                                                  │
└──────────────────────────────────────────────────────────────────┘
```

### Core Components

| Component                  | Platform                  | Role                                                                                            |
| -------------------------- | ------------------------- | ----------------------------------------------------------------------------------------------- |
| **pfSense Firewall**       | pfSense (FreeBSD)         | Network gateway enforcing routing, NAT, firewall policies, and exporting traffic logs to Wazuh. |
| **Wazuh Manager**          | Ubuntu Linux              | Receives agent data, evaluates rules, and generates security alerts.                            |
| **Wazuh Indexer**          | Ubuntu Linux (OpenSearch) | Indexes and stores security events for search and correlation.                                  |
| **Wazuh Dashboard**        | Ubuntu Linux (Web UI)     | Visualizes alerts, logs, and agent health for SOC analysis.                                     |
| **Windows Endpoint Agent** | Windows 10                | Generates Windows security and authentication events.                                           |
| **Linux Endpoint Agent**   | Ubuntu Linux              | Provides Linux system, authentication, and AppArmor logs.                                       |
| **Kali Linux (Attacker)**  | Kali Linux                | Simulates adversary activity to validate detections.                                            |
| **VirtualBox Host**        | Windows / Linux           | Hosts and isolates the SOC lab infrastructure.                                                  |


##  Key Features

### Monitoring & Detection

- Endpoint Telemetry Collection: Continuous collection of Windows and Linux security, authentication, and system events via Wazuh agents.
- Firewall Log Visibility: Centralized ingestion of pfSense firewall and traffic logs for network-level monitoring and correlation.
- Authentication Abuse Detection: Detection of failed and successful logons across Windows and Linux environments.
- Raw Log Archiving: Full-fidelity log retention using Wazuh archives for forensic review and detection tuning.
- Cross-Source Correlation: Correlation of endpoint and firewall telemetry to identify suspicious activity patterns.

### Security Operations

- Centralized SOC Visibility: Unified dashboards for alerts, agent health, and log investigation.
- Alert Triage & Analysis: Rule-based alerting with severity classification to support SOC workflows.
- Incident Investigation Workflow: End-to-end visibility from attack generation to alert validation.
- Attack Simulation & Validation: Controlled adversary activity using Kali Linux to validate detections and logging coverage.
- Operational Resilience Testing: Validation of SOC visibility during network failures, firewall misconfigurations, and recovery scenarios.

## Lab Environment

### Virtualization Platform

- Hypervisor: VirtualBox
- Deployment Model: Isolated SOC home lab
- Network Segmentation: Host-Only LAN with pfSense enforcing routing and inspection

### Network Configuration

| Network            | Purpose              | Details                          |
| ------------------ | -------------------- | -------------------------------- |
| **Host-Only LAN**  | Internal SOC network | `192.168.56.0/x`                |
| **pfSense LAN IP** | Default gateway      | `192.168.56.x`                   |
| **DHCP Scope**     | Endpoint addressing  | `192.168.56.x – 192.168.56.x` |
| **WAN Interface**  | Internet simulation  | VirtualBox NAT                   |


### Virtual Machines

| VM                   | Operating System  | Purpose                                                                     |
| -------------------- | ----------------- | --------------------------------------------------------------------------- |
| **pfSense**          | pfSense (FreeBSD) | Firewall, router, DHCP server, and primary network logging source           |
| **Wazuh Server**     | Ubuntu Linux      | Hosts Wazuh Manager, Indexer (OpenSearch), and Dashboard                    |
| **Windows Endpoint** | Windows 10        | Primary monitored endpoint generating Windows security events               |
| **Linux Endpoint**   | Ubuntu Linux      | Secondary monitored endpoint providing Linux system and authentication logs |
| **Attacker Machine** | Kali Linux        | Simulates adversary activity for detection validation                       |

### Resource Allocation

| Component        | RAM    | CPU      |
| ---------------- | ------ | -------- |
| pfSense          | 1 GB   | 1 vCPU   |
| Wazuh Server     | 4–6 GB | 2 vCPU   |
| Windows Endpoint | 4 GB   | 2 vCPU   |
| Linux Endpoint   | 2 GB   | 1 vCPU   |
| Kali Linux       | 2–4 GB | 1–2 vCPU |



### Software Stack

- Ubuntu Linux: Wazuh Server hosting Manager, Indexer (OpenSearch), and Dashboard (6GB RAM, 30GB disk)
- Windows 10: Primary monitored endpoint generating Windows security and authentication events
- Ubuntu Linux: Secondary Linux endpoint providing system, authentication, and AppArmor logs
- Kali Linux: Attacker machine for simulating scans, brute-force attempts, and adversary activity
- pfSense: Firewall and router enforcing network policies and exporting traffic logs via syslog
- Wazuh 4.x: Central SIEM platform for log collection, detection, alerting, and analysis

##  Installation & Setup

### Phase 1: Core Wazuh Deployment

```bash
# Become root
sudo -i

# Install prerequisites
apt update && apt install -y curl default-jdk

# Download and run Wazuh all-in-one installer
curl -sO https://packages.wazuh.com/4.x/wazuh-install.sh
bash wazuh-install.sh -a

Access the Wazuh Dashboard at:
https://<wazuh-server-ip>
```
### Phase 2: Agent Deployment

Windows Agent
- Download the Windows agent from the Wazuh Dashboard
- Install and configure the Manager IP
- Verify agent status in Dashboard → Agents

```
# Add Wazuh repository
curl -s https://packages.wazuh.com/key/GPG-KEY-WAZUH | apt-key add -
echo "deb https://packages.wazuh.com/4.x/apt/ stable main" \
> /etc/apt/sources.list.d/wazuh.list

# Install and start agent
apt update && apt install -y wazuh-agent
echo "WAZUH_MANAGER='<manager-ip>'" >> /var/ossec/etc/ossec.conf
systemctl enable --now wazuh-agent

```
### Phase 3: pfSense Integration

- Deploy pfSense with WAN (NAT) and LAN (Host-Only) interfaces
- Configure syslog forwarding (UDP 514) to the Wazuh Server
- Enable firewall logging on monitored interfaces
- Confirm logs appear in the Wazuh Dashboard

### Phase 4: Verification

- Confirm agents are Active in the Wazuh Dashboard
- Validate log ingestion from:
- Windows endpoints
- Linux endpoints
- pfSense firewall (syslog)

##  Use Cases & Demonstrations

### Test Scenario 1: File Integrity Validation

**Objective**: Verify FIM detection capabilities

**Steps**:
1. Create test file: `C:\Users\Public\Pictures\fim_test.txt`
2. Modify file content
3. Observe real-time alerts in Wazuh Dashboard

**Results**: Successfully detected file creation and modification with complete metadata

### Test Scenario 2: Simulated Attack

**Objective**: Test detection against common attack techniques

**Attack Vector**: Kali Linux → Windows 10

**Techniques Simulated**:
- Port scanning
- Brute force authentication attempts
- Privilege escalation attempts
- Suspicious process execution

**Detection**: Alerts generated with detailed forensic data for investigation

### Test Scenario 3: Network Traffic Analysis

**Objective**: Monitor and analyze network communications

**Activities**:
- Outbound connection monitoring
- Firewall rule violations
- Anomalous traffic patterns

**Visibility**: Complete packet-level insight through pfSense integration

##  Screenshots

### Wazuh Dashboard Overview
![Dashboard](link-to-screenshot)<img width="1852" height="956" alt="dashboard wazuh - Copy" src="https://github.com/user-attachments/assets/f6de356b-84bc-4f7e-8150-9e6914cbbd4f" />


### File Integrity Monitoring Events
![FIM Events](link-to-screenshot)<img width="1918" height="1021" alt="FIM added" src="https://github.com/user-attachments/assets/e601a7c4-430f-42b5-8355-49c74c1f7897" />

### Agent Inventory

![Agent Inventory](link-to-screenshot)<img width="1919" height="1014" alt="wazuh agent to inventory" src="https://github.com/user-attachments/assets/43863220-d2fb-4b23-89c5-dabe527c38d9" />

### Security Alerts
![Alerts](link-to-screenshot)<img width="1915" height="1017" alt="wazuh event dashboard" src="https://github.com/user-attachments/assets/b19285a0-ee42-4553-b25d-c1c23984bb73" />

##  Skills Demonstrated

### Technical Competencies

- **SIEM Operations**: Log analysis, alert tuning, incident investigation
- **System Administration**: Linux/Windows server management, service configuration
- **Network Security**: Firewall configuration, traffic analysis, network segmentation
- **Virtualization**: Multi-VM environment management

### Security Operations

- **Threat Detection**: Signature and behavior-based detection methods
- **Incident Response**: Investigation workflow and forensic analysis
- **Security Monitoring**: Dashboard creation and alert management
- **Attack Simulation**: Red team tactics and detection validation

### Analytical Skills

- **Log Analysis**: Pattern recognition and anomaly detection
- **Data Visualization**: Custom dashboard development
- **Threat Intelligence**: IOC correlation and threat mapping
- **Documentation**: Technical writing and procedure documentation

##  Future Enhancements

- [ ] Integration with threat intelligence feeds (MISP, STIX/TAXII)
- [ ] Active Response automation (automatic blocking, containment)
- [ ] Custom detection rules for advanced persistent threats
- [ ] Integration with Shuffle SOAR for automated playbooks
- [ ] Cloud workload monitoring (AWS/Azure agent deployment)
- [ ] Vulnerability assessment integration (OpenVAS/Nessus)
- [ ] Email alerting and notification system
- [ ] Compliance reporting (PCI-DSS, HIPAA, CIS benchmarks)

##  Documentation

Detailed documentation for this project is available in the following resources:

1. **[Wazuh Installation Guide](./docs/WAZUH-INSTALLATION.md)** - Complete installation procedures, architecture overview, and dashboard setup
2. **[File Integrity Monitoring Configuration](./docs/WAZUH-FIM.md)** - FIM setup, testing, and validation procedures
3. **[pfSense Integration Guide](./docs/WAZUH-PFSENSE-INTEGRATION.md)** - Firewall deployment and log forwarding configuration

### Additional Resources

- [Official Wazuh Documentation](https://documentation.wazuh.com/)
- [Wazuh Community Forums](https://groups.google.com/g/wazuh)
- [MITRE ATT&CK Framework](https://attack.mitre.org/)

##  Acknowledgments

- **Wazuh Team** for developing the open-source SIEM platform
- **Cybersecurity Community** for shared knowledge and best practices
- **pfSense Project** for the robust firewall solution

##  Contact

**Sufiyan Sageer**

For questions, suggestions, or collaboration opportunities, feel free to reach out!

---

 If you found this project helpful, please consider giving it a star!

**Disclaimer**: This lab environment is designed for educational and testing purposes only. Do not use these techniques on systems you do not own or have explicit permission to test.
