#Splunk Dashboards

EventCode 1 shows what processes were started.

Files like Resume.pdf.exe appear as documents but are actually executable malware.

This search helps find cases where a user might have double-clicked a malicious file thinking it was a PDF.
![image](https://github.com/user-attachments/assets/5b168b48-5c92-4f22-9a95-605c7a9afcf4)

Created detection rules query for common C2 communication patterns which shows established connection to Kali linux machine
![image](https://github.com/user-attachments/assets/2db7705b-bc7e-4f04-93b2-d3df71e6b654)

Threat hunting query designed to try find executables disguised as PDF files.
![image](https://github.com/user-attachments/assets/82144861-9a1e-4ed9-94a2-ba20d8fffd18)

