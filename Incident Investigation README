# Incident 001: Malware Execution Analysis

## 📋 Incident Summary
- **Date**: June 2nd 2025
- **Analyst**: Christian Bartolillo
- **Severity**: HIGH
- **Status**: CONFIRMED MALWARE
- **Affected Systems**: 1 host (Windows-PC)
- **Investigation Time**: 15 minutes

## 🎯 Executive Summary
A malicious executable disguised as a PDF document was downloaded and executed on a Windows workstation. The malware established a reverse shell connection and performed system reconnaissance. Immediate containment recommended.

## 🔍 Investigation Timeline

### Initial Alert (T+0 minutes)
**Alert**: Suspicious executable downloaded and executed
**File**: Resume.pdf.exe
**Host**: 192.168.20.10

### Triage Phase (T+0 to T+5 minutes)
```splunk
index=endpoint "Resume.pdf.exe" | head 10
