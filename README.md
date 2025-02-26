# Network Performance Degradation Investigation

### Scenario 
The server team has noticed significant network performance degradation on some older devices attached to the network in the `10.0.0.0/16` range. After ruling out external DDoS attacks, the security team suspects an internal issue.

### Activity
Develop a hypothesis based on threat intelligence and security gaps (e.g., *"Could there be lateral movement in the network?"*).

- All traffic originating from within the local network is by default allowed by all hosts.
- There is unrestricted use of PowerShell and other applications in the environment.
- It is possible someone is either downloading large files or conducting port scans against hosts in the local network.

---

## Data Collection

### Observations
**EDR-test-vm** was found failing several connection requests against itself:
![image](https://github.com/user-attachments/assets/206e8956-acec-4378-8bf8-a1349808e8ac)

After observing failed connection requests from our suspected host (`10.0.0.166`) in chronological order, I noticed a port scan was taking place due to the sequential order of the ports. Several port scans were detected:
![image](https://github.com/user-attachments/assets/52f7a914-6f56-4b1f-a94b-6571fb28eaa2)

### Investigation
I pivoted to the `DeviceProcessEvents` table to check for suspicious activity around the time the port scan started. A PowerShell script named `portscan.ps1` was launched at `2025-02-24T15:50:55.7880015Z`.
![image](https://github.com/user-attachments/assets/86001c15-a225-41d8-84b2-38d582e6eb2c)

I logged into the suspected computer and observed the PowerShell script used to conduct the port scan.
![image](https://github.com/user-attachments/assets/8b1e88f7-f237-42d7-8498-7904e3672463)

### Actions Taken
- The port scan script was executed by the `SYSTEM` account, which is unexpected behavior and was not set up by admins.
- The device was isolated and a malware scan was conducted.
- The malware scan returned **zero results**.
- The IT team will proceed with **re-imaging the device**.

![image](https://github.com/user-attachments/assets/ddb69dd2-f9e4-4e52-9f2a-0d73ab0a6d45)

---

## MITRE ATT&CK Framework Related TTPs

- **Tactic:** Reconnaissance (TA0043)
  - **Technique:** Network Service Scanning (T1046)

- **Tactic:** Execution (TA0002)
  - **Technique:** Command and Scripting Interpreter: PowerShell (T1059.001)

- **Tactic:** Privilege Escalation (TA0004)
  - **Technique:** Valid Accounts: Local Accounts (T1078.003)

- **Tactic:** Discovery (TA0007)
  - **Technique:** System Network Connections Discovery (T1049)

- **Tactic:** Lateral Movement (TA0043) *(Potential Future Phase)*
  - **Technique:** Remote Services (T1021)

These TTPs highlight the adversary's efforts to discover network services, execute scripts, move laterally, and evade defenses.
