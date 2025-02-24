# (WIP) Network Performance Degradation Investigation

## Preparation
**Goal:** Set up the hunt by defining what you're looking for.

The server team has noticed a significant network performance degradation on some of their older devices attached to the network in the `10.0.0.0/16` network. After ruling out external DDoS attacks, the security team suspects something might be going on internally.

**Activity:** Develop a hypothesis based on threat intelligence and security gaps (e.g., “Could there be lateral movement in the network?”).

All traffic originating from within the local network is by default allowed by all hosts. There is also unrestricted use of PowerShell and other applications in the environment. It’s possible someone is either downloading large files or doing some kind of port scanning against hosts in the local network.

## Data Collection
EDR-test-vm was found failing several connection request against itself:
![image](https://github.com/user-attachments/assets/206e8956-acec-4378-8bf8-a1349808e8ac)

After observing failed connection requests from our suspected host(10.0.0.166) in chronological order, I noticed a port scan was taking place due to the sequential order of the ports. There were several port scans being conducted:
![image](https://github.com/user-attachments/assets/52f7a914-6f56-4b1f-a94b-6571fb28eaa2)

I pivoted to the DeviceProcessEvents table to see if we could see anything that was suspicious around the time the port scan started. We noticed a PowerShell script named portscan.ps1 launch at 2025-02-24T15:50:55.7880015Z
![image](https://github.com/user-attachments/assets/86001c15-a225-41d8-84b2-38d582e6eb2c)

I logged into the suspected computer and observed the PowerShell script that was used to conduct the port scan.
![image](https://github.com/user-attachments/assets/8b1e88f7-f237-42d7-8498-7904e3672463)


We observed the port scan script was launched by the SYSTEM account, this is not expected behavior and is not something that was setup by admins, so I isolated the device and ran a malware scan. Malware scan came back with zero results so we will have the IT team re-image the device.
![image](https://github.com/user-attachments/assets/ddb69dd2-f9e4-4e52-9f2a-0d73ab0a6d45)

---

## Documentation
Based on the details provided, the following Tactics, Techniques, and Procedures (TTPs) from the MITRE ATT&CK framework are relevant:

- **Discovery**:
  - Network Service Scanning (T1046)

- **Execution**:
  - Command and Scripting Interpreter: PowerShell (T1059.001)

- **Lateral Movement**:
  - Remote Services (T1021)

- **Defense Evasion**:
  - Masquerading (T1036)

These TTPs highlight the adversary's efforts to discover network services, execute scripts, move laterally, and evade defenses.
