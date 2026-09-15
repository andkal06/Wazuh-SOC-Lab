# Architecture

## Components
This lab has 4 main pieces componens : 
1. **Wazuh Manager** : Analyzes logs, matches rules, generates alerts. 
2. **Wazuh Indexer** : Stores alerts in an OpenSearch index. 
3. **Wazuh Dashboard** : Web UI for visualization and investigatio.
4. **Wazuh Agent** : Collects Windows Event Logs and forwards them to the manager.
Wazuh Manager, Indexer, and Dashboard runs on Docker container (Ubuntu VM), while Wazuh Agent runs on Windows 11 (host machine). Due to Ubuntu Server runs as a separate VirtualBox VM, not directly on the Windows host, traffic between the Windows machine and the VM needs explicit port forwarding (more on that below).

## Data Flow Pipeline 
Quick rundown of each stage:
1. **Windows Event Log** :  the Wazuh agent reads events from the `Security`, `System`, and `Application` channels.
2. **Agent → Manager (TCP 1514)** :  events get sent encrypted to the manager, but only after the agent has enrolled (registered its key through port 1515 first).
3. **Manager (analysisd)** :  Evaluates every incoming event against the ruleset and MITRE ATT&CK framework. If a match is found, it triggers an alert and writes it to
4.  `alerts.json`.
5. **Filebeat → Indexer (HTTPS 9200)** : filebeat reads `alerts.json` and ships it to the indexer, authenticated with internal credentials (in this lab, `admin` / `kalya`).
6. **Indexer (OpenSearch)** : stores the alert documents inside an index called `wazuh-alerts-4.x-*`.
7. **Dashboard** : queries and visualizes everything from the indexer, accessed through the browser at `https://localhost:8443`.

## Network / port forwarding
<img width="300" height="150" alt="image" src="https://github.com/user-attachments/assets/3692a178-6dc7-4278-abff-fef668215ad8" />

Since Ubuntu Server runs inside VirtualBox with NAT, these ports need to be forwarded from the Windows host to the VM so the agent and browser can actually reach the Wazuh stack:
| Name | Protocol | Host port | Guest port | Purpose |
|---|---|---|---|---|
| SSH | TCP | 2222 | 22 | SSH access into the VM |
| https | TCP | 8443 | 443 | Dashboard access |
| Agent-enroll | TCP | 1515 | 1515 | New agent registration/enrollment |
| Agent-event | TCP | 1514 | 1514 | Regular event traffic from agent to manager |

