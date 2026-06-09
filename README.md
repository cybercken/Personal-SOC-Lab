# **HOME SOC LAB**
The goal of this project is to gain a deeper understanding of security analysis detection engineering, and incident response.

Deployed the Wazuh SIEM on an Ubuntu VM to monitor a Windows endpoint to ingest security logs, implement custom detection rules, map them to the MITRE ATT&CK framework, simulate security scenarios and configure automated responses.

Basic architecture:

  Ubuntu VM (Wazuh Manager)  <------->  Windows Client (Wazuh Client)


## **Detection Layer**

Focused on improving visibility and detection capabilities across the environment, with the following primary log sources:

- Sysmon on the Windows endpoint to generate detailed event logs
- TShark on the Ubuntu VM to monitor network traffic across the subnet
- File Integrity Monitoring (FIM) for real-time monitoring of sensitive directories

## **Summary**
The bulk of this project revolves around the following:

- Brute force attack detection
- Malware detection & response
- Network discovery detection


