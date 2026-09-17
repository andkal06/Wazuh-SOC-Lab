# Wazuh SOC Lab

A hands-on Security Operations Center (SOC) lab built with Wazuh to practice security monitoring, threat detection, alert investigation, and File Integrity Monitoring (FIM) in a controlled virtual environment.

## Overview

This project focuses on building a small SOC environment using Wazuh as the central security monitoring platform. The lab was designed to understand how security events are collected from endpoints, analyzed by Wazuh, and presented as alerts for further investigation.

The project includes controlled attack simulations and system modifications to generate security events. These events are then analyzed through the Wazuh dashboard to understand the detection process and investigate the resulting alerts.

## Objectives

* Deploy and configure a Wazuh-based SOC environment.
* Monitor endpoint security events using Wazuh agents.
* Analyze security alerts generated from simulated activities.
* Investigate authentication-related security events.
* Implement and analyze File Integrity Monitoring (FIM).
* Understand how file modifications can be detected before they lead to further system impact.
* Practice basic SOC analyst workflows such as alert triage, investigation, and documentation.

## Lab Environment

| Component          | Role                                                 |
| ------------------ | ---------------------------------------------------- |
| Wazuh Manager      | Centralized security monitoring and alert management |
| Wazuh Dashboard    | Alert visualization and investigation                |
| Wazuh Agent        | Endpoint monitoring and log collection               |
| Linux / Windows VM | Monitored endpoint                                   |
| Kali Linux         | Controlled attack simulation                         |

The entire environment is deployed in a controlled virtual lab for educational and testing purposes.

## Architecture

```text
                    +----------------------+
                    |      Wazuh Server    |
                    |----------------------|
                    | Wazuh Manager        |
                    | Wazuh Indexer        |
                    | Wazuh Dashboard      |
                    +----------+-----------+
                               |
                               | Security Events
                               |
                    +----------v-----------+
                    |    Wazuh Agent       |
                    |----------------------|
                    | Endpoint Monitoring  |
                    | Log Collection        |
                    | File Integrity        |
                    +----------+-----------+
                               ^
                               |
                    +----------+-----------+
                    |   Controlled Events  |
                    |----------------------|
                    | Authentication Tests |
                    | File Modifications   |
                    | Attack Simulation    |
                    +----------------------+
```

## Wazuh Agent Monitoring

The Wazuh agent is installed on the monitored endpoint to collect security-related events and system information.

The monitoring process can be summarized as:

```text
Endpoint Activity
       |
       v
Wazuh Agent
       |
       v
Wazuh Manager
       |
       v
Analysis & Detection Rules
       |
       v
Security Alert
       |
       v
Wazuh Dashboard
       |
       v
Investigation
```

This workflow demonstrates the basic process used in a SOC environment to collect telemetry, detect suspicious activity, and investigate security events.

## File Integrity Monitoring

One of the main components of this lab is File Integrity Monitoring (FIM).

FIM monitors selected files and directories for changes such as:

* File creation
* File modification
* File deletion
* Permission changes
* Ownership changes

During the lab, controlled modifications were made to monitored files to generate Wazuh alerts.

The detection process can be represented as:

```text
Monitored File
      |
      v
File Modification
      |
      v
Wazuh FIM Detection
      |
      v
Alert Generated
      |
      v
SOC Investigation
```

The key concept demonstrated by this exercise is that FIM can identify unauthorized or unexpected file changes at the moment the change occurs. This provides visibility before a modified file is executed or causes further impact.

For example, in a real environment, similar monitoring could help detect suspicious modifications to system configuration files or the introduction of unauthorized scripts into protected directories.

## Security Event Investigation

After an alert is generated, the event is investigated through the Wazuh Dashboard.

The investigation process includes:

1. Reviewing the alert severity.
2. Identifying the affected endpoint.
3. Examining the timestamp of the event.
4. Identifying the file, process, or user associated with the event.
5. Reviewing the event details and available metadata.
6. Determining whether the activity was expected or suspicious.
7. Documenting the findings.

This process provides hands-on practice with the basic workflow of a SOC analyst.

## Attack Simulation

Controlled security activities were performed to generate realistic events inside the isolated lab environment.

The simulations were used to understand how Wazuh detects and reports suspicious activity rather than targeting real-world systems.

The generated events were then analyzed through the Wazuh dashboard to examine:

* Event timestamps
* Source information
* Target endpoint
* Authentication activity
* File changes
* Alert severity
* Detection rules

## Key Findings

The lab demonstrated several important concepts in security monitoring:

* Endpoint activity can be centralized through Wazuh agents.
* Wazuh can generate alerts based on detected security events.
* FIM can detect changes to monitored files and directories.
* Alert details provide useful information for investigation.
* Security events can be correlated with the activity that generated them.
* Detection is only the first stage of the SOC workflow and should be followed by investigation and appropriate response.

## Skills Demonstrated

### Security Operations

* SOC Monitoring
* Alert Triage
* Security Event Investigation
* Log Analysis
* Incident Investigation
* File Integrity Monitoring

### Wazuh

* Wazuh Manager
* Wazuh Agent
* Wazuh Dashboard
* Detection Rules
* FIM Configuration
* Alert Analysis

### Systems & Networking

* Linux Administration
* Virtual Machine Networking
* Endpoint Monitoring
* System Log Analysis

## Project Structure

```text
Wazuh-SOC-Lab/
│
├── README.md
│
├── setup/
│   └── Wazuh installation and configuration
│
├── attack-simulation/
│   └── Controlled security event simulations
│
├── fim/
│   └── File Integrity Monitoring investigation
│
├── screenshots/
│   └── Wazuh dashboard and alert evidence
│
└── documentation/
    └── Investigation notes and findings
```

## Learning Outcomes

Through this project, I gained practical experience in setting up a Wazuh monitoring environment and understanding how endpoint telemetry is transformed into security alerts.

The lab also helped me understand the difference between generating an alert and actually investigating it. Instead of only looking at whether an event was detected, the investigation process requires examining the event context, identifying the affected system, and determining whether the activity is expected or potentially suspicious.

## Future Improvements

Possible improvements for this lab include:

* Adding Windows endpoint monitoring with Sysmon.
* Creating custom Wazuh detection rules.
* Integrating additional network security telemetry.
* Adding MITRE ATT&CK mapping to detected activities.
* Implementing automated Active Response.
* Expanding the lab with additional attack scenarios.
* Building investigation playbooks for common SOC alerts.

## Disclaimer

This project was created for educational and portfolio purposes. All security testing and attack simulations were performed within an isolated and controlled lab environment. No unauthorized systems were targeted.

