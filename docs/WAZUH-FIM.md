# WAZUH FILE INTEGRATION MONITORING(FIM),LOG COLLECTION AND PFSENSE INSTALLATION AND INTEGRATION

## INTRODUCTION
This document serves as a continuation of the previous Wazuh Log Detection & Monitoring Report – Installation & Dashboards, where we successfully deployed the core Wazuh components — Wazuh Manager, Indexer, and Dashboard — and integrated the Wazuh Agent for endpoint monitoring. Having established a functional Wazuh environment in that phase, this next stage focuses on expanding its capabilities to achieve comprehensive security visibility and control.

In this documentation, we move beyond installation to explore three critical areas of operational monitoring:
- File Integrity Monitoring (FIM) — Configuring and testing Wazuh’s FIM module to detect unauthorized file changes, monitor system directories, and ensure the integrity of sensitive data across agents.
- Log Collection and Analysis — Setting up centralized log ingestion from multiple endpoints and services, followed by verification of event normalization, rule matching, and alert generation within the Wazuh dashboard.
- pfSense Firewall Integration — Installing and configuring pfSense as a network perimeter defense component, and integrating its logs into Wazuh for real-time network traffic monitoring, intrusion detection, and policy enforcement visibility.
This document continues in the same hands-on, step-by-step approach as the previous report, maintaining a consistent lab topology and methodology. The goal is to provide practical, verifiable guidance that security analysts can follow to extend Wazuh’s detection coverage across both endpoint and network layers.

By the end of this phase, the lab environment will evolve into a more complete SOC-style monitoring setup, with Wazuh acting as the unified platform for file integrity validation, log correlation, and network event analysis — laying the groundwork for proactive threat detection and response.

## FILE INTEGRITY MONITORING (FIM) CONFIGURATION AND VALIDATION
After completing the installation and agent integration process from the previous documentation, the next objective was to enable and test File Integrity Monitoring (FIM) using the Wazuh Windows agent. This section explains, in detail, the steps I followed to configure, implement, and verify file monitoring functionality between my Windows endpoint and the Wazuh Manager running on Lubuntu

### Step 1: Establishing the Connection Between the Agent and Dashboard
With the Wazuh Manager already installed on my Lubuntu machine and the Windows agent successfully integrated, I proceeded to connect the agent to the Wazuh Dashboard.
From the Dashboard’s Endpoint Security section, I navigated to the Agents Inventory panel and confirmed that my Windows agent was listed and actively communicating with the manager. This verification step ensured that the agent was properly registered and ready to receive configuration updates.

### Step 2: Locating and Editing the Agent Configuration File
Once the agent connection was verified, I moved to my Windows system to configure the File Integrity Monitoring settings.
The configuration file for the Wazuh agent is located at:C:\Program Files (x86)\ossec-agent\ossec.conf
I opened this file using a text editor with administrative privileges (such as Notepad run as Administrator). Within the file, I located the <File integrity monitoring> section, which controls the behavior of the File Integrity Monitoring module.

### Step 3: Modifying the File Integrity Monitoring Settings
Inside the <syscheck> section, I made a few important changes to customize how the agent performs file monitoring:


#### 1.Changed the Frequency:
By default, the agent scans monitored directories every 12 hours. To test 	FIM responsiveness more efficiently in a lab setting, I modified the 	frequency to 10 seconds.
<frequency>10</frequency>
This change allowed the system to check for file modifications almost immediately, ensuring quicker validation during testing.

#### 2.Added a New Directory for Monitoring:
I then added a new directory entry to the configuration, pointing to the C:\Users\Public folder. This folder was chosen because it is easily accessible and suitable for test files.
	
	<directories>C:\Users\Public</directories>
	


#### 3.Kept Other Default Settings:
Other sections, such as scan_on_start and report_changes, were left at their default values to maintain the standard monitoring behavior while focusing on the frequency and directory parameters.

### Step 4: Restarting the Wazuh Agent
After saving the configuration file, I restarted the Wazuh agent service to apply the new settings. This can be done through either the Windows Services console or by running the following command in PowerShell:
Restart-Service -Name wazuh

Restarting the service triggers a fresh configuration load, allowing the agent to begin its new file monitoring cycle immediately.

### Step 5: Performing a File Integrity Test
To verify that the configuration was working correctly, I performed a simple test in the monitored directory.
- 1.I created a new text file inside the monitored path:
C:\Users\Public\Pictures\fim_test.txt
- 2.After confirming the file appeared in the directory, I opened it and added some text to simulate a modification event.
For example, I added:File Integration
- 3.Later, I edited the same file again to generate a second modification event. Each of these changes was meant to create detectable FIM events that Wazuh could log and display.

### Step 6: Monitoring and Analyzing Logs in the Wazuh Dashboard
Back in the Wazuh Dashboard, I navigated to Endpoint Security → Events → File Integrity Monitoring.
Within this section, I observed the real-time alerts generated by the agent:
  - The first alert corresponded to the file creation event for file integration.txt.
  - The subsequent alerts indicated file modification events, showing metadata such as the file path, event type, and timestamp.
These results confirmed that my configuration changes were successful and that Wazuh was accurately detecting and reporting file-level activities from the Windows system.

### Step 7: Analysis and Observations
The File Integrity Monitoring module worked as expected. By adjusting the frequency to 10 seconds, the detection became almost instantaneous, making it suitable for testing and demonstration purposes.
The alerts generated provided detailed information about:
  - The file path involved in each event.
  - The type of action (creation or modification).
  - The agent name and operating system.
  - The timestamp of the event.
This validation proved that Wazuh’s FIM component, when configured properly, is capable of continuous monitoring of file activity and can serve as an effective detection layer against unauthorized changes.



### Step 8: Summary
Through this process, I successfully implemented and validated File Integrity Monitoring in Wazuh for a Windows endpoint.
The configuration allowed the system to:
  - Detect and alert on file creation and modification activities in real-time.
  -Display corresponding events clearly on the Wazuh Dashboard.
  -Establish a baseline understanding of how FIM operates within a SOC environment.
This hands-on implementation enhanced my understanding of how Wazuh agents monitor file systems, generate security events, and forward them to the central manager for analysis. It also provided practical insights into fine-tuning the configuration parameters such as frequency, directory selection, and recursion levels.

VISUAL WALKTHROUGH OF FILE INTEGRATIONS
