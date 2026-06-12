# Enterprise SIEM & NIDS Home Lab: Wazuh & Suricata Deployment

## Project Overview
The objective of this project was to architect and deploy a centralized Security Information and Event Management (SIEM) pipeline capable of ingesting, correlating, and analyzing telemetry from a multi-OS environment. By integrating Wazuh with Suricata, this lab demonstrates practical skills in host-based intrusion detection (HIDS), network-based intrusion detection (NIDS), log aggregation, and alert triage.

## Logical Architecture
- SIEM Manager: Wazuh 4.x (Deployed locally via Docker Compose)
- Windows 11 Endpoint: Wazuh Agent installed for native OS log collection, Sysmon integration, and authentication monitoring.
- Kali Linux Endpoint (VirtualBox): Wazuh Agent installed for Linux system logging, coupled with a local Suricata instance for network traffic analysis.

---

## Step-by-Step Execution Guide

### Phase 1: Provisioning the SIEM Manager
To ensure an isolated and easily reproducible environment, the Wazuh manager was deployed utilizing Docker containers.
1. Cloned the Wazuh Docker repository.
2. Provisioned the cluster using the command: docker-compose up -d
3. Verified container health and accessed the central Wazuh dashboard via the local interface.

### Phase 2: Endpoint Agent Enrollment
Agents were deployed to both endpoints to establish a secure, encrypted log-shipping pipeline to the central manager.

- Windows Deployment: Deployed the MSI package via PowerShell, defining the Wazuh manager's IP address, and started the Wazuh service via services.msc.
- Linux Deployment: Deployed the agent to the Kali virtual machine via APT package manager using the official repository keys, then enabled and started the wazuh-agent service.

Result: Both agents successfully reported to the manager as active.

<img width="1900" height="730" alt="ACTIVE-AGENTS" src="https://github.com/user-attachments/assets/e09638a0-4c43-4304-ae3e-d1705379a48c" />


### Phase 3: Suricata NIDS Integration
To monitor network traffic for malicious signatures, Suricata was installed on the Kali Linux endpoint. 
1. Installed Suricata using the package manager.
2. Configured the Wazuh agent to read Suricata's eve.json output file by adding the file location and setting the log format to JSON inside the local ossec.conf configuration file.
3. Restarted the Wazuh agent to apply the configuration.

### Phase 4: Attack Simulation & Telemetry Generation
To validate the detection engineering pipeline, I executed controlled attacks against the Kali Linux endpoint.

1. Network Intrusion Simulation:
Triggered a known Suricata test rule by simulating a malicious network payload using a curl command against testmynids.org.
Expected Output: Suricata flags the traffic and writes a GPL ATTACK_RESPONSE alert to eve.json, which the Wazuh agent immediately forwards to the manager.

2. Host-Level Authentication Attack:
Simulated a brute-force or unauthorized access attempt by forcing failed user impersonations using the command: su - invaliduser
Expected Output: The Linux OS logs the failed authentication, and Wazuh parses the syslog to generate a Rule Level validation.


<img width="1920" height="922" alt="LINUX-ATTACKS" src="https://github.com/user-attachments/assets/9bb84431-8aec-4c99-8e17-58bff7755463" />

---

## Alert Triage & Incident Response Validation
The final phase of the lab involved pivoting to the Wazuh Threat Hunting dashboard to correlate the generated telemetry. 

The SIEM successfully aggregated the multi-platform alerts. In the logs captured below, the system successfully tracked and mapped the simulated attacks to the MITRE ATT&CK Framework:
- Captured high-severity Logon failure alerts from the Windows host (Mapped to Tactics: T1078 Valid Accounts & T1531 Account Access Removal).
- Captured the GPL ATTACK_RESPONSE Suricata alert triggered by the malicious network interface traffic on the Kali Linux machine.

<img width="1892" height="852" alt="WAZUH-ALERTS" src="https://github.com/user-attachments/assets/1fefe883-6977-4fed-97f8-09141b4783fe" />


## Conclusion
This deployment validates the ability to engineer a comprehensive security monitoring environment. It successfully demonstrates the configuration of secure log pipelines, the integration of third-party NIDS sensors into a SIEM, and the practical application of the MITRE ATT&CK framework for analyzing multi-OS security events.
