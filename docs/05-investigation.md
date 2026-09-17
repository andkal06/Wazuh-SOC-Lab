# Attack Scenario Investigation

I ran 3 different attack scenarios to test how well Wazuh could detection them. Here is the scenario : 

## Scenario 1: Unauthorized account creation + privilege escalation
### Attack simulation

<img width="600" height="70" alt="image" src="https://github.com/user-attachments/assets/4f51b2bb-2f61-45ba-8dff-ef51301c3597" />


```powershell
net user soc_temp_intruder P@ssw0rd2026! /add
net localgroup administrators soc_temp_intruder /add
```

### Detection
<img width="781" height="20" alt="image" src="https://github.com/user-attachments/assets/5f2642e1-32b6-4b8f-b406-27f781d06284" />

An alert titled **"Administrators Group Changed"** showed up in Threat Hunting almost immediately, rule level **12** (High severity).

### Analysis 
A member was added to a security enabled local group  at 2026-09-11T21:23:12.9071611Z. The subject account, `kalya`, made the change, adding a new member into the `Administrators` group on the `kalya` host. Wazuh flagged it as rule level 12, which is high severity because the Administrators group has full control over the system any addition to it is essentially a privilege escalation event. MITRE ATT&CK mapped this to T1484 (Domain Policy Modification), under the Defense Evasion and Privilege Escalation tactics.

### Cleanup
Once the account was confirmed as a test artifact, it was removed using
```powershell
net user soc_temp_intruder /delete
```

## Scenario 2: Brute force logon attempts
### Attack simulation
```powershell
for ($i = 1; $i -le 10; $i++) {
    net use \\127.0.0.1\IPC$ /user:fake_intruder WrongPass$i 2>$null
    Start-Sleep -Milliseconds 200
}
```
### Detection
<img width="913" height="53" alt="image" src="https://github.com/user-attachments/assets/d3edbc35-584d-45ef-bf6d-38af88746da2" />
Logon Failure, unknown user or bad password alerts fired repeatedly within a short window.

### Analysis
An event ID 4625 detect that there are an account failed to log on at 2026-09-11T21:36:13.316Z, with the attempt made against the username `fake_intruder` from source address `127.0.0.1`. This corresponds to logon type `3`, which means a network logon rather than an interactive one, someone trying to authenticate remotely, not sitting in front of the machine. The failure reason returned was `%%2313`, unknown user name or bad password. What stands out is that this same failure repeated ten times in a row, roughly 200 milliseconds apart, each with a different source port. That timing pattern doesn't look like a person typing a wrong password by hand. Wazuh rated it rule level `5` (medium), and mapped it to T1110 (Brute Force) under the Credential Access tactic.

# Scenario 3: File Integrity Monitoring (FIM) tampering
### Attack simulation
```powershell
Set-Content -Path "C:\Windows\System32\drivers\etc\test_fim.txt" -Value "Initial Secure System Config"
Start-Sleep -Seconds 3
Add-Content -Path "C:\Windows\System32\drivers\etc\test_fim.txt" -Value "`n[TAMPERED] Unauthorized payload injected at $(Get-Date)"
```
### Detection
<img width="820" height="32" alt="image" src="https://github.com/user-attachments/assets/977213db-a156-4de4-8758-178901d09220" />

The FIM module picked up the change to the file and to a related registry key, logging it as Registry/File Integrity Checksum Changed.

### Analysis
Wazuh's syscheck module detected a checksum change on a file inside `C:\Windows\System32\drivers\etc`, along with a related change to the registry key `SecureTimeLimits` registry key. These changes triggered rule IDs 594 and 750, both classified as level 5 (medium). This detection is related to File Integrity Monitoring (FIM), which works by comparing the current hash of files or registry entries with a previously recorded baseline. Therefore, even a small unauthorized modification, such as adding a single line to a text file, can be detected by Wazuh.

### Cleanup
The tampered file was removed and the directory was checked to confirm no other unexpected changes were left behind.

