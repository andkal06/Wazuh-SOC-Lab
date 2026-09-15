# Setting up the Wazuh agent on Windows

## Initial install

<img width="500" height="200" alt="image" src="https://github.com/user-attachments/assets/d57d9996-a244-40e7-9c34-36fef6914777" />

```powershell
Invoke-WebRequest -Uri https://packages.wazuh.com/4.x/windows/wazuh-agent-4.14.7-1.msi -OutFile $env:tmp\wazuh-agent.msi

msiexec.exe /i $env:tmp\wazuh-agent.msi /q `
  WAZUH_MANAGER='127.0.0.1' `
  WAZUH_REGISTRATION_SERVER='127.0.0.1' `
  WAZUH_AGENT_NAME='windows-host'

NET START WazuhSvc
```

## Verification

After that, the agent finally shows up as **active**, both from the CLI and the dashboard:

<img width="500" height="200" alt="image" src="https://github.com/user-attachments/assets/8bbaaf50-88ea-40f0-a5b4-576a9b16eb11" />

```bash
sudo docker exec -it single-node-wazuh.manager-1 /var/ossec/bin/agent_control -l
# ID: 001, Name: windows-host, IP: any, Active
```


