# Wazuh-Defense-Infrastructure
This project demonstrates a fully functional Security Information and Event Management (SIEM) infrastructure built from the ground up, featuring centralized log management, real-time threat detection, and security analytics across multiple operating systems and network segments.
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

## 🎯 Overview

This project showcases a fully functional Security Operations Center (SOC) environment built in a home lab setting. The infrastructure integrates **Wazuh Manager, Indexer, and Dashboard** with multiple endpoints and network security components to provide real-time threat detection, log analysis, and incident response capabilities.

### Project Objectives

- Deploy enterprise-grade SIEM infrastructure in a virtualized environment
- Implement comprehensive endpoint and network monitoring
- Demonstrate practical security operations and incident response workflows
- Simulate real-world attack scenarios for detection validation
- Build expertise in security monitoring, log analysis, and dashboarding

## 🏗️ Architecture

### Network Topology

```
┌─────────────────────────────────────────────────────────────┐
│                     Home Lab Network                         │
├─────────────────────────────────────────────────────────────┤
│                                                               │
│  ┌──────────────┐          ┌─────────────────┐             │
│  │   pfSense    │◄────────►│ Wazuh Manager   │             │
│  │   Firewall   │          │   (Lubuntu VM)  │             │
│  └──────────────┘          │                 │             │
│         │                  │ - Manager       │             │
│         │                  │ - Indexer       │             │
│         │                  │ - API Server    │             │
│         │                  └─────────────────┘             │
│         │                          ▲                        │
│         │                          │                        │
│    ┌────┴────────────────┬─────────┴────────┬─────────┐   │
│    │                     │                  │         │   │
│ ┌──▼───────┐      ┌──────▼─────┐    ┌──────▼────┐   │   │
│ │ Windows  │      │ Linux Mint │    │  Kali     │   │   │
│ │ 10 Agent │      │   Agent    │    │  Linux    │   │   │
│ └──────────┘      └────────────┘    │ (Attacker)│   │   │
│                                      └───────────┘   │   │
│                                                      │   │
│                   ┌─────────────────┐               │   │
│                   │  Windows 11     │               │   │
│                   │  Dashboard GUI  │◄──────────────┘   │
│                   └─────────────────┘                   │
│                                                          │
└──────────────────────────────────────────────────────────┘
```

### Core Components

| Component | Platform | Role |
|-----------|----------|------|
| **Wazuh Manager** | Lubuntu VM | Central analysis engine, rule processing, alert generation |
| **Wazuh Indexer** | Lubuntu VM | OpenSearch-based data storage and indexing |
| **Wazuh Dashboard** | Windows 11 | Web-based visualization and management interface |
| **Windows 10 Agent** | Windows 10 | Monitored endpoint with agent deployment |
| **Linux Agent** | Linux Mint | Secondary monitored endpoint |
| **Attacker Machine** | Kali Linux | Red team simulation and penetration testing |
| **pfSense Firewall** | pfSense | Network perimeter defense and traffic monitoring |

## ✨ Key Features

### Monitoring & Detection

- **File Integrity Monitoring (FIM)**: Real-time detection of unauthorized file changes
- **Log Collection & Analysis**: Centralized log ingestion from multiple sources
- **Network Traffic Monitoring**: pfSense firewall log integration
- **Intrusion Detection**: Real-time alert generation for suspicious activities
- **Endpoint Visibility**: Comprehensive agent monitoring across Windows and Linux

### Security Operations

- **SIEM Dashboard**: Custom visualizations for security event analysis
- **Alert Management**: Rule-based detection with configurable severity levels
- **Incident Response**: Workflow for investigating and responding to security events
- **Threat Simulation**: Controlled attack scenarios using Kali Linux
- **Honeypot Integration**: Deception technology for attacker tracking

## 🖥️ Lab Environment

### Hardware Requirements

- **Host Machine**: Minimum 16GB RAM, 100GB free storage
- **Virtualization**: VMware Workstation / VirtualBox / Hyper-V
- **Network**: Isolated virtual network or VLAN for lab segmentation

### Software Stack

- **Ubuntu/Lubuntu**: Wazuh Manager host (4GB RAM, 15GB disk)
- **Windows 10**: Primary monitored endpoint
- **Linux Mint**: Secondary Linux endpoint
- **Kali Linux**: Penetration testing and attack simulation
- **pfSense**: Open-source firewall appliance
- **Wazuh 4.12**: Latest stable version

## 🚀 Installation & Setup

### Phase 1: Core Wazuh Deployment

```bash
# Switch to root user
sudo -i

# Install dependencies
apt install curl default-jdk -y

# Download Wazuh installation script
curl -sO https://packages.wazuh.com/4.12/wazuh-install.sh

# Run all-in-one installation
bash wazuh-install.sh -a
```

Access the dashboard at: `https://<wazuh-server-ip>`

### Phase 2: Agent Deployment

#### Windows Agent Installation

1. Download the Windows agent installer from Wazuh Dashboard
2. Run installer with Manager IP configuration
3. Verify agent connection in Dashboard → Agents Inventory

#### Linux Agent Installation

```bash
# Import GPG key
curl -s https://packages.wazuh.com/key/GPG-KEY-WAZUH | apt-key add -

# Add repository
echo "deb https://packages.wazuh.com/4.x/apt/ stable main" | tee /etc/apt/sources.list.d/wazuh.list

# Install agent
apt-get update
apt-get install wazuh-agent

# Configure manager IP
echo "WAZUH_MANAGER='<manager-ip>'" >> /var/ossec/etc/ossec.conf

# Start agent
systemctl start wazuh-agent
```

### Phase 3: File Integrity Monitoring Configuration

Edit agent configuration (`C:\Program Files (x86)\ossec-agent\ossec.conf` on Windows):

```xml
<syscheck>
  <frequency>10</frequency>
  <directories>C:\Users\Public</directories>
  <scan_on_start>yes</scan_on_start>
  <report_changes>yes</report_changes>
</syscheck>
```

Restart agent:
```powershell
Restart-Service -Name wazuh
```

### Phase 4: pfSense Integration

1. Install pfSense in virtual environment
2. Configure syslog forwarding to Wazuh Manager
3. Enable traffic logging for monitored interfaces
4. Verify log ingestion in Wazuh Dashboard

## 🎓 Implemented Capabilities

### 1. File Integrity Monitoring (FIM)

**Configuration**: Custom monitoring of critical directories with 10-second scan frequency for rapid detection

**Validation**: Successfully detected and alerted on:
- File creation events
- File modification events
- Real-time metadata capture (path, timestamp, action type)

### 2. Centralized Log Collection

**Sources Integrated**:
- Windows Event Logs (Security, System, Application)
- Linux system logs (syslog, auth.log)
- pfSense firewall logs (traffic, block, pass rules)

**Analysis Features**:
- Event normalization and correlation
- Rule-based alert generation
- Custom dashboard visualizations

### 3. Network Perimeter Monitoring

**pfSense Capabilities**:
- Real-time traffic analysis
- Firewall rule enforcement visibility
- Intrusion detection log forwarding
- Network policy compliance monitoring

### 4. Honeypot Deployment

Implemented deception technology to:
- Attract and log attacker behavior
- Generate high-fidelity alerts
- Study attack patterns and TTPs

## 🔬 Use Cases & Demonstrations

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

## 📸 Screenshots

### Wazuh Dashboard Overview
![Dashboard](link-to-screenshot)<img width="1852" height="956" alt="dashboard wazuh - Copy" src="https://github.com/user-attachments/assets/f6de356b-84bc-4f7e-8150-9e6914cbbd4f" />


### File Integrity Monitoring Events
![FIM Events](link-to-screenshot)<img width="1918" height="1021" alt="FIM added" src="https://github.com/user-attachments/assets/e601a7c4-430f-42b5-8355-49c74c1f7897" />

### Agent Inventory

![Agent Inventory](link-to-screenshot)<img width="1919" height="1014" alt="wazuh agent to inventory" src="https://github.com/user-attachments/assets/43863220-d2fb-4b23-89c5-dabe527c38d9" />

### Security Alerts
![Alerts](link-to-screenshot)<img width="1915" height="1017" alt="wazuh event dashboard" src="https://github.com/user-attachments/assets/b19285a0-ee42-4553-b25d-c1c23984bb73" />

## 💡 Skills Demonstrated

### Technical Competencies

- **SIEM Operations**: Log analysis, alert tuning, incident investigation
- **System Administration**: Linux/Windows server management, service configuration
- **Network Security**: Firewall configuration, traffic analysis, network segmentation
- **Virtualization**: Multi-VM environment management
- **Scripting**: Configuration automation and custom rule development

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

## 🔮 Future Enhancements

- [ ] Integration with threat intelligence feeds (MISP, STIX/TAXII)
- [ ] Active Response automation (automatic blocking, containment)
- [ ] Custom detection rules for advanced persistent threats
- [ ] Integration with Shuffle SOAR for automated playbooks
- [ ] Cloud workload monitoring (AWS/Azure agent deployment)
- [ ] Vulnerability assessment integration (OpenVAS/Nessus)
- [ ] Email alerting and notification system
- [ ] Compliance reporting (PCI-DSS, HIPAA, CIS benchmarks)

## 📚 Documentation

Detailed documentation for this project is available in the following resources:

1. **[Wazuh Installation Guide](./docs/Wazuh-Log-Detection.pdf)** - Complete installation procedures, architecture overview, and dashboard setup
2. **[File Integrity Monitoring Configuration](./docs/Wazuh-FIM-Integration.pdf)** - FIM setup, testing, and validation procedures
3. **[pfSense Integration Guide](./docs/pfSense-Integration.pdf)** - Firewall deployment and log forwarding configuration

### Additional Resources

- [Official Wazuh Documentation](https://documentation.wazuh.com/)
- [Wazuh Community Forums](https://groups.google.com/g/wazuh)
- [MITRE ATT&CK Framework](https://attack.mitre.org/)

## 🤝 Acknowledgments

- **Wazuh Team** for developing the open-source SIEM platform
- **Cybersecurity Community** for shared knowledge and best practices
- **pfSense Project** for the robust firewall solution

## 📧 Contact

**Sufiyan Sageer**

For questions, suggestions, or collaboration opportunities, feel free to reach out!

---

⭐ If you found this project helpful, please consider giving it a star!

**Disclaimer**: This lab environment is designed for educational and testing purposes only. Do not use these techniques on systems you do not own or have explicit permission to test.
