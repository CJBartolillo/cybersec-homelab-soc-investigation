# cybersec-homelab-soc-investigation
# SOC Analyst Investigation Lab

![image](https://github.com/user-attachments/assets/a741e1aa-68f5-4a08-8336-767efcd63b82)


## 🎯 Project Overview
This project from MyDFIR (https://www.youtube.com/watch?v=kku0fVfksrk&list=PLG6KGSNK4PuBWmX9NykU0wnWamjxdKhDJ&index=1) demonstrates practical SOC (Security Operations Center) analyst skills through a controlled cybersecurity investigation in a virtualized environment. The project simulates a real-world malware incident and shows the complete investigation workflow from initial alert to containment recommendations.

## 🔧 Technical Skills Demonstrated
- **SIEM Analysis**: Advanced Splunk query development and log analysis
- **Incident Response**: Structured investigation methodology and documentation
- **Threat Detection**: Custom rule creation and behavioral analytics
- **Network Security**: Traffic analysis and IOC identification
- **Virtualization**: Multi-VM environment setup and management
- **Endpoint Monitoring**: Sysmon configuration and log ingestion

## 🏗️ Lab Architecture
- **Virtualization Platform**: VirtualBox
- **Operating Systems**: Windows 10 (Victim), Kali Linux (Attacker)
- **SIEM**: Splunk Enterprise
- **Monitoring**: Sysmon with Olaf configuration
- **Network**: Internal /24 network (192.168.20.0/24)

## 🚨 Simulated Attack Scenario
**Incident**: Suspicious file execution with potential C2 communication
**Attack Vector**: Social engineering via malicious PDF executable
**Techniques Used**: Reconnaissance, reverse shell, system enumeration

## 📊 Key Investigations
### [Incident 001: Malware Execution Analysis](investigations/incident-001-malware-execution/)
- **Severity**: High
- **Status**: Confirmed Malware
- **Timeline**: 15-minute investigation
- **Outcome**: Host isolation recommended, IOCs extracted

## 🔍 SOC Analyst Workflow Demonstrated
1. **Alert Triage** - Initial validation and prioritization
2. **Evidence Collection** - Systematic log analysis
3. **Impact Assessment** - Scope and damage evaluation  
4. **IOC Extraction** - Threat intelligence gathering
5. **Containment Planning** - Actionable recommendations
6. **Documentation** - Incident reporting and lessons learned

## 📈 Splunk Queries Developed
- Process execution chain analysis
- Network connection monitoring
- Behavioral anomaly detection
- Threat hunting automation
- Custom dashboard creation

## 🎓 Learning Outcomes
- Hands-on experience with enterprise SIEM tools
- Understanding of attack lifecycle and detection points
- Practical incident response methodology
- Technical writing and documentation skills
- Threat hunting and proactive security monitoring

## 🚀 Getting Started
1. Review the [Lab Setup Guide](docs/lab-setup.md)
2. Follow the [Investigation Methodology](docs/investigation-methodology.md)
3. Explore the [Incident Analysis](investigations/incident-001-malware-execution/)
4. Check out the [Splunk Dashboards](dashboards/)
