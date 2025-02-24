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

