# Wazuh SIEM and Suricata NIDS Lab Deployment

## Project Overview
I built this home lab to get hands-on experience deploying and configuring a Security Information and Event Management (SIEM) system across a multi-OS environment. My main objective was to set up Wazuh as the central manager, integrate Suricata for network intrusion detection, and successfully aggregate simulated attacks on a single dashboard.

## Environment Setup
* **SIEM Manager:** Wazuh (Deployed locally via Docker)
* **Windows 11 Host:** Configured the Wazuh agent for native OS log collection and authentication monitoring.
* **Kali Linux VM:** Configured the Wazuh agent for Linux system logging and integrated Suricata for network traffic analysis.
* **Tools:** VirtualBox, Docker Compose, Windows Command Line, Linux Terminal.

## Execution and Validation
After provisioning the Wazuh manager and enrolling both the Windows and Kali Linux agents, I verified that they were actively communicating and shipping logs to the central server. 

### 1. Agent Enrollment
This dashboard shows both the Windows and Linux endpoints successfully connected and reporting in real-time.

<img width="1900" height="730" alt="ACTIVE-AGENTS" src="https://github.com/user-attachments/assets/08cab149-d0df-4aa8-b1c7-30e96c6f6fc8" />


### 2. Attack Simulation
To validate the monitoring pipeline, I executed controlled attacks against the Kali Linux endpoint. I ran a simulated network attack using a test NIDS signature, and forced failed login attempts to trigger host-level authentication warnings.

<img width="1920" height="922" alt="LINUX-ATTACKS" src="https://github.com/user-attachments/assets/9e770aa9-5bd7-4001-88f5-9efa88d2ab72" />


### 3. Centralized Detections
The Wazuh dashboard successfully caught, correlated, and mapped the malicious activity. In the logs below, you can see the high-severity Windows authentication failures (mapped to MITRE ATT&CK T1078 & T1531) alongside the network attack response from the Linux machine.

<img width="1892" height="852" alt="WAZUH-ALERTS" src="https://github.com/user-attachments/assets/2884c4ac-35c0-4c03-815e-425288d56d8b" />


## Key Takeaways
* Successfully aggregated logs from completely different operating systems into one centralized server.
* Handled real-world SIEM tuning, including filtering noise and managing network IP drift during the lab setup.
* Validated that security monitoring rules are functioning by manually simulating host and network attacks.
