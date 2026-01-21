# WAZUH SOC LAB WITH PFSENSE. FULL BUILD & INTEGRATION GUIDE

## OBJECTIVE
Build a full SOC lab where:
  - pfSense acts as a firewall + router
  - Kali is the attacker
  - Windows & Ubuntu are victims
  - Wazuh collects logs and shows alerts in a dashboard
All traffic flows through pfSense and all logs are monitored by Wazuh.

## VIRTUAL MACHINES USED

## VM	PURPOSE

| VM | Purpose |
|----|--------|
| **pfSense** | Firewall, router, DHCP server, and NAT gateway |
| **Ubuntu (Wazuh Server)** | SIEM server hosting Wazuh Manager, Indexer, and Dashboard |
| **Lubuntu (Agent)** | Linux monitored host with Wazuh agent installed |
| **Windows 10** | Windows monitored endpoint with Wazuh agent |
| **Kali Linux** | Attacker machine for penetration testing and attack simulation |

## NETWORK ARCHITECTURE

| VM | Network Adapter |
|----|----------------|
| **pfSense** | Adapter 1 → NAT (WAN)<br>Adapter 2 → Host-Only (LAN) |
| **All Other VMs** | Adapter 1 → Host-Only |

## VIRTUALBOX HOST-ONLY NETWORK

  - Open: VirtualBox → File → Tools → Host Network Manager
  - Create a Host-Only network
  - Set: IPv4: 192.168.56.1
    - Mask: 255.255.255.0
    - DHCP: Disabled
pfSense will provide DHCP instead

## PFSENSE CONFIGURATION
### INTERFACES
  - WAN → NAT
  - LAN → Host-Only

### ENABLE DHCP

### pfSense Web UI: 
  - Services → DHCP Server → LAN

### Enable:
  - Range: 192.168.56.50 – 192.168.56.150
  - Gateway: 192.168.56.1
  - DNS: 8.8.8.8

### NAT
- Firewall → NAT → Outbound → Automatic

## UBUNTU WAZUH SERVER IP

Set static IP:192.168.56.X
```
Netplan:

network:
  version: 2
  renderer: NetworkManager
  ethernets:
    enp0s3:
      dhcp4: no
      addresses:
        - 192.168.56.10/24
      routes:
        - to: default
          via: 192.168.56.1
      nameservers:
        addresses:
          - 8.8.8.8                                                         
```

## WAZUH INSTALLATION

On Wazuh Server:
  - Wazuh Manager
  - Wazuh Indexer
  - Wazuh Dashboard

## AGENT REGISTRATION

On Wazuh Server: sudo /var/ossec/bin/manage_agents
Add: 
  - windows
  - Ubuntu

### EXTRACT KEYS
- On Ubuntu Agent
  - sudo /var/ossec/bin/manage_agents
  - I → paste key → Q
  - sudo systemctl restart wazuh-agent

- ON WINDOWS AGENT
Run as Admin:
  - manage_agents.exe
  - I → paste key → Q
  - Restart Wazuh Agent service
## VERIFY AGENTS

On Wazuh Server: ```/var/ossec/bin/agent_control -lc ```

Expected:
  - windows → Active
  - ubuntu → Active



## LOG FLOW TEST

### Linux
  - su root
  - Enter wrong password.

### DASHBOARD
  - Wazuh → Security Events → Authentication failure








