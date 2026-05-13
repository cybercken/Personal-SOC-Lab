# **HOME SOC LAB**

Deployed the Wazuh SIEM on an Ubuntu VM to monitor a Windows endpoint to ingest security logs, implement custom detection rules, map them to the MITRE ATT&CK framework, and configure automated responses.

Basic architecture:

  Ubuntu VM (Wazuh Manager)  <------->  Windows Client (Wazuh Client)


## **1) Detection Engineering**

Focused on improving visibility and detection capabilities across the environment:

Enabled Sysmon on the Windows endpoint to generate detailed event logs
Configured TShark on the Ubuntu VM to monitor network traffic across the subnet
Enabled Wazuh File Integrity Monitoring (FIM) for real-time monitoring of sensitive directories
Integrated VirusTotal for malware reputation checks and malicious file analysis

Developed custom detection rules for common attack patterns, including:

-Multiple failed login attempts from the same IP within a short time window

-Successful authentication following repeated failed login attempts

-High volumes of TCP SYN or ICMP traffic, indicative of reconnaissance activity such as port scans or ping sweeps

## **2) Automated Responses**

Configured active response actions for specific detections, including:

- Temporarily blocking source IPs associated with brute-force or network discovery activity
  
- Automatically quarantining files flagged as malicious by VirusTotal

## **3) Walkthroughs**

### **Scenario 1: Brute Force Detection**

Used CrackMapExec from a separate Kali Linux machine to simulate brute-force login attempts against the Windows endpoint.

Custom Wazuh rules detected the activity and extracted details such as:

- Source IP address
  
- Logon type (Type 2, type 3, type 10, etc)
  
- Failed authentication count

Once the threshold was reached, Wazuh triggered an active response script to temporarily block the attacking IP.

In a real environment, additional investigation would still be required. For example, repeated failed logins could originate from a legitimate remote employee who mistyped credentials. Analysts would validate the source, review authentication activity, and either unblock the IP or escalate the incident with actions such as credential resets or longer-term blocking.

### **Scenario 2: Malware Detection with VirusTotal Integration**

Downloaded the EICAR test file from eicar.org to validate malware detection and response workflows.

VirusTotal flagged the file as malicious, triggering Wazuh active response scripts to quarantine it automatically.

The quarantine environment was configured to:

- Restrict access to Administrator and SYSTEM accounts only
  
- Prevent accidental execution
  
- Isolate suspicious files from the primary filesystem

A separate partition was created for the quarantine directory to provide an isolated environment for controlled malware analysis if needed.

If a file is determined to be a false positive, it can be restored safely. Otherwise, it can be retained for further analysis and investigation.

### **Scenario 3: Network Discovery Detection**

Configured TShark as a background service on the Ubuntu system in promiscuous mode to capture subnet traffic and write logs to a custom output file.

TShark display filters were tuned to capture traffic commonly associated with reconnaissance activity, including:

- ICMP Echo Requests (Type 8), commonly used in ping sweeps
  
- TCP SYN packets with SYN=1 and ACK=0, commonly associated with port scanning

The raw TShark logs were parsed and decoded into structured fields to simplify custom Wazuh rule creation and alert correlation.

Using a separate Kali machine, I launched both Nmap scans and ICMP ping sweeps to test detection logic.

In a real-world environment, some TCP and ICMP traffic is expected. However, unusually high volumes matching the configured thresholds are treated as suspicious activity.

When triggered, the detection rules automatically initiate an active response to temporarily block the source IP pending further investigation. If determined to be legitimate traffic, the IP can be unblocked. Otherwise, the activity can be escalated as potential reconnaissance.
