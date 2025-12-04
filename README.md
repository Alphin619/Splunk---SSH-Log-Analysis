# Splunk SSH Log Analysis
## Objective
This project focuses on analyzing SSH authentication logs using Splunk Enterprise.
The goal was to ingest the provided SSH log dataset, identify failed login attempts, detect potential brute-force behavior, and determine next steps from a SOC analyst perspective.

---
## Data Acquisition & Ingestion

 [Download SSH Log file](https://raw.githubusercontent.com/0xrajneesh/30-Days-SOC-Challenge-Beginner/refs/heads/main/ssh_logs.json)**
 (Right-click -> Save As)

<img width="1820" height="706" alt="Screenshot From 2025-12-02 16-21-21" src="https://github.com/user-attachments/assets/8b5753bf-1e44-4f3c-8d7c-513a9f8551bd" />


Uploaded the file into Splunk via Add Data -> Upload

Configured:

Source type: _json

Index: main

Verified successful ingestion with:

[index="main" sourcetype="_json"]
<img width="1839" height="779" alt="Screenshot From 2025-12-02 16-24-13" src="https://github.com/user-attachments/assets/a527289a-b074-47ce-8bc8-d94f243f7b13" />


## Action 1 - Analysis  
---
Identify Top 10 Endpoints With Failed SSH Login Attempts

[index="main" sourcetype="_json" auth_success="false"
| stats count by "id.orig_h"
| sort -count
| head 10]
<img width="1839" height="600" alt="Screenshot From 2025-12-02 16-29-35" src="https://github.com/user-attachments/assets/2613a4e1-264f-4917-b4c1-f6ce37f8c2d1" />


Purpose:
To identify whether any IPs are repeatedly attempting and failing authentication - a common indicator of brute-force attempts.

## Action 2 - Determine Total SSH Connections
---
[index="main" sourcetype="_json"
| stats count as total_ssh_connections]
<img width="1838" height="329" alt="Screenshot From 2025-12-02 16-39-25" src="https://github.com/user-attachments/assets/3ff268eb-258f-4328-b13d-5c6f419ff163" />

Result:
Approximately 1200 SSH connection events were logged.

## Action 3 - Identify All SSH Event Types
---
[index="main" sourcetype="_json"
| stats count by event_type]
<img width="1844" height="416" alt="Screenshot From 2025-12-02 16-42-15" src="https://github.com/user-attachments/assets/593a5fe4-7ee6-4204-8dba-e5db14c3d79a" />

Result:
Event categories included:

Successful logins

Failed logins

Multiple failed attempts

Connections without authentication

This provides a high-level view of overall SSH authentication behavior.

## Action 4 - Investigate Top Failed Source IPs
---
As a SOC analyst, next steps include:

Reviewing the top failed-login source IPs to determine whether they indicate:

Brute-force attempts

Malicious scanning behavior

Determining whether the source IP is internal or external

---

Internal IP:

Possible compromised account

Potential insider threat

Investigate host and reset credentials

Validate whether SSH access is required

---

External IP:

Likely brute-force or unauthorized probing

Checking for “failure -> success” sequences from the same IP
(This is a strong indicator of account compromise.)

Recommended defensive actions:

Restrict SSH (port 22) or disable it if not required

Enforce key-based authentication / MFA

Implement auto-ban or rate-limiting for repeated authentication failures

Apply IP allow-listing for administrative access

## Conclusion

---

This project demonstrated how to ingest and analyze SSH authentication logs within Splunk.
I identified failed login sources, evaluated overall SSH activity, categorized event types, and recommended appropriate SOC response actions.
