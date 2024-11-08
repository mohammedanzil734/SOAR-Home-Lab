
---

# My Home Lab SOAR Setup for Detecting and Responding to Mimikatz Activity

This repository documents my home lab SOAR setup focused on detecting and responding to Mimikatz activity. I wanted to simulate a SOC (Security Operations Center) environment with tools for log collection, automated alerts, incident tracking, and response automation. Below is a detailed walkthrough of the process I followed to set this up.

---

### Project Overview
The goal of this project was to create a small-scale SOAR lab using open-source tools. My focus was on detecting credential-based attacks, like those performed with Mimikatz, and automating the response. Here’s what I used:

- **Wazuh** for SIEM and log collection.
- **Shuffle** as the SOAR platform for automating workflows.
- **TheHive** for case management, backed by **Cassandra** and **Elasticsearch**.
- **Sysmon** on a Windows VM to capture detailed logs.

---

## Lab Architecture

I set up the lab using VirtualBox to host multiple virtual machines, with the following architecture:

- **VM 1**: Wazuh (SIEM)
- **VM 2**: Shuffle (SOAR Platform)
- **VM 3**: Windows Client with Sysmon (for log generation and Mimikatz testing)
- **VM 4**: TheHive with Cassandra and Elasticsearch (Case Management)

---

## Setup Guide

1. **Setting Up Virtual Machines**
   - I used VirtualBox to create separate VMs for each component. This kept everything modular and allowed me to manage resources for each VM separately.

2. **Installing Wazuh (SIEM)**
   - I installed Wazuh to serve as the SIEM. This was set up on a Linux-based VM to handle log collection and alerting.
   - I configured the Wazuh agent on the Windows VM so that it would forward logs to the Wazuh manager, where the detection rules were applied.

3. **Configuring Sysmon on the Windows Client**
   - I installed Sysmon on my Windows VM to capture detailed events like process creation and network connections.
   - The Sysmon logs were forwarded to Wazuh for analysis, which allowed Wazuh to detect suspicious activities.

4. **Setting Up Shuffle (SOAR Platform)**
   - Shuffle was installed on a separate Linux VM to act as the SOAR platform. I configured it to automate responses based on specific triggers.
   - I set up integrations with Wazuh (for alerting) and TheHive (for case creation) to enable automated workflows.

5. **Installing TheHive with Cassandra and Elasticsearch (Case Management)**
   - I installed TheHive on another Linux VM, backed by Cassandra (for data storage) and Elasticsearch (for searching and indexing cases).
   - I configured TheHive to receive alerts from Shuffle, so cases would be created automatically when specific incidents (like Mimikatz detections) were identified.

---

## Configuration Details

### Wazuh Configuration
- I enabled Sysmon event logging in Wazuh and created custom rules to detect Mimikatz activity.
- Specific rules were configured to monitor events like suspicious access to `lsass.exe` or unusual process creations that are typically associated with Mimikatz.

### Shuffle Workflows
- **Mimikatz Detection Workflow**
  - Triggered by an alert from Wazuh when Mimikatz activity was detected on the Windows VM.
  - The workflow in Shuffle enriched the alert with VirusTotal for any hash values, sent an email alert, and created a case in TheHive.

### TheHive, Cassandra, and Elasticsearch Configuration
- I configured TheHive to use **Cassandra** for data storage and **Elasticsearch** for indexing and searching.
- The integration between Shuffle and TheHive allowed cases to be created automatically, with incident details structured for easy tracking and investigation.

---

## Workflow

1. **Detection**
   - Wazuh monitored logs from the Windows VM, generating alerts whenever suspicious Mimikatz-like behavior was detected.

2. **Alert Enrichment and Case Creation**
   - Shuffle received alerts from Wazuh and automatically enriched them using VirusTotal for hash lookups.
   - Shuffle then sent an email notification to the analyst and created a new case in TheHive with the relevant details.

3. **Incident Tracking and Response**
   - TheHive managed the case, allowing me to track the investigation steps, document findings, and coordinate response actions.

---

## Testing and Simulation

1. **Simulating Mimikatz Execution**
   - I ran Mimikatz on the Windows VM to simulate a credential dump attack.
   - Wazuh generated an alert, which was forwarded to Shuffle, triggering the configured workflows.

2. **Workflow Validation**
   - I verified that Shuffle enriched the alert, sent an email, and created a case in TheHive.
   - TheHive logged the case with all details intact, making it easy to track the response steps and review them later.

---

## Future Improvements

- **Additional Automation**: I plan to add more automation steps, such as isolating affected endpoints or resetting credentials automatically.
- **Expanded Integrations**: I'm considering adding Splunk or other data sources to increase monitoring coverage.
- **Advanced Threat Intelligence**: Adding more enrichment sources to provide better context for each alert.

---

## References

- [Wazuh Documentation](https://documentation.wazuh.com/current/)
- [Sysmon Installation and Configuration](https://docs.microsoft.com/en-us/sysinternals/downloads/sysmon)
- [Shuffle Documentation](https://shuffler.io/docs)
- [TheHive Project](https://thehive-project.org)

---

This setup has given me hands-on experience with a functional SOAR environment. It allows me to practice real-world incident detection and response using automation, enrichment, and case management—all in a controlled home lab environment.
