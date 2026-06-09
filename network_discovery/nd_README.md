## **Overview**

Configured TShark as a background service on the Ubuntu system in promiscuous mode to capture subnet traffic and write logs to a custom output file.

TShark display filters were tuned to capture traffic commonly associated with reconnaissance activity, including:

- ICMP Echo Requests (Type 8), commonly used in ping sweeps
  
- TCP SYN packets with SYN=1 and ACK=0, commonly associated with port scanning

The raw TShark logs were parsed and decoded into structured fields to simplify custom Wazuh rule creation and alert correlation.

## **Tests & Response**

Using a separate Kali machine, I launched both Nmap scans and ICMP ping sweeps to test detection logic.

In a real-world environment, some TCP and ICMP traffic is expected. However, unusually high volumes matching the configured thresholds are treated as suspicious activity.

When triggered, the detection rules automatically initiate an active response to temporarily block the source IP pending further investigation. If determined to be legitimate traffic, the IP can be unblocked. Otherwise, the activity can be escalated as potential reconnaissance.

