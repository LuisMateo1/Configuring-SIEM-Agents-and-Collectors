# Configuring SIEM Agents and Collectors
<h3>Objective</h3>

- Analyze data as part of security monitoring activities

A security information and event management (SIEM) system assists security monitoring and incident response by aggregating and correlating log and network traffic data within a single management and reporting interface. 
In this lab, I'll be configuring the SIEM and the security appliances that will send logs to the SIEM.
#
<h3>Configuring a Sensor Interface</h3>

The SIEM1 VM running Security Onion has two interfaces. eth0 is configured with the IP address 10.1.0.246 and is used as a management interface. eth1 has no IP address and is used only to sniff traffic from the local network.
All the VMs are connected to the vLOCAL switch implemented in Hyper-V. To enable eth1 to sniff traffic, it is configured as a port mirroring destination interface.
- The goal is to configure the source interfaces and test that the sensor can sniff traffic.

![image](https://github.com/user-attachments/assets/c17804c4-61b0-42f5-8a25-95215ecc5577)

The first thing to do is to run a script on the host virtual machine to enable port mirroring.
![image](https://github.com/user-attachments/assets/ab454e40-0e99-43bd-b60a-725a569c8482)

Now I'll log into the VM running the SIEM to sniff the traffic off the eth1 interface to test if port mirroring is working.
![image](https://github.com/user-attachments/assets/189a866b-d314-4758-81ea-6fa78a72bdad)
- This traffic is monitored by Zeek, a passive network sniffer. Zeek's ruleset will only send events considered 'interesting' to the SIEM.

Here I can see the status of the security tools being used.
![image](https://github.com/user-attachments/assets/ed26a77b-19d2-4592-8687-a3f333d8939b)
- Security Onion is the SIEM, and the Host Intrusion Prevention System (HIDS) is the SIEM client.
- siem-eth1 is this VM interface that is sniffing traffic from the network.
- Bro (Zeek) is the passive sniffer, that is monitoring the eth1 interface
- The Elastic Stack is how events get written to the SIEM, it powers the SIEM logging engine.


On the Kibana dashboard, and selecting 'connections', under 'Bro Hunting', and scroll down to verify that hosts from the 10.1.0.0/24 network are present.
- Kibana is the visualization tool in ElasticStack and is used to configure dashboards for different categories, showing data in graph and table formats.

![image](https://github.com/user-attachments/assets/2e700e37-fa8e-4ac4-a6fc-b487db76dbce)
![image](https://github.com/user-attachments/assets/6321c933-eb53-4e3c-8c2d-68a87ed51da4)

On another VM I'll use Zenmap, to run an intensive scan against 10.1.0.1. Then go back to Kibana to check if any alerts were generated.
![image](https://github.com/user-attachments/assets/aca7831f-4c86-46bf-b364-05f8d88c7c00)

Under Alert Data > Bro Notices, here I see that the scan was detected by Bro (Zeek).
![(1)11a](https://github.com/user-attachments/assets/f2ec207f-3d9b-47a0-a947-80f437d2fcd9)

