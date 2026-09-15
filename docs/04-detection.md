# Detection dashboard

Once the agent was active and data was flowing properly, I used Wazuh's built in dashboard modules for monitoring.

## Overview

This dashboard provides active agent status, alongside a comprehensive severity breakdown of all security alerts triggered within the last 24 hours.

<img width="700" height="320" alt="image" src="https://github.com/user-attachments/assets/49b78236-30d4-4c26-a31b-3a601376f9ad" />


## MITRE ATT&CK mapping

To better understand the threat landscape, Wazuh automatically enriches the log data by mapping triggered rules directly to specific MITRE ATT&CK tactics and techniques. This context makes threat hunting and incident response much more intuitive.

<img width="700" height="320" alt="image" src="https://github.com/user-attachments/assets/195c4311-1cfc-4045-a6fc-a1ecce328257" />

## File Integrity Monitoring (FIM)

The FIM module actively tracks critical file and registry modifications on the Windows endpoint. It provides detailed visibility into what specific changes were made, exactly when they occurred, and which user initiated them.

<img width="700" height="320" alt="image" src="https://github.com/user-attachments/assets/2ff15299-9005-4d35-8234-15bdfd49434b" />


## Endpoint detail view

This dashboard provides active agent status, alongside a comprehensive severity breakdown of all security alerts triggered within the last 24 hours.

<img width="700" height="320" alt="image" src="https://github.com/user-attachments/assets/4b735463-6698-4289-a92b-b4ba0e0a9ebe" />


