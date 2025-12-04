Splunk SSH Log Analysis
Objective

This project focuses on analyzing SSH authentication logs using Splunk Enterprise.
The goal was to ingest the provided SSH log dataset, identify failed login attempts, detect potential brute-force behavior, and determine next steps from a SOC analyst perspective.

Data Acquisition & Ingestion

Downloaded the SSH log file: ssh_logs.json
(Right-click → Save As)

Uploaded the file into Splunk via Add Data → Upload

Configured:

Source type: _json

Index: main

Verified successful ingestion with:

index="main" sourcetype="_json"

Analysis
Action 1 — Identify Top 10 Endpoints With Failed SSH Login Attempts
index="main" sourcetype="_json" auth_success="false"
| stats count by "id.orig_h"
| sort -count
| head 10


Purpose:
To identify whether any IPs are repeatedly attempting and failing authentication — a common indicator of brute-force attempts.

Action 2 — Determine Total SSH Connections
index="main" sourcetype="_json"
| stats count as total_ssh_connections


Result:
Approximately 1200 SSH connection events were logged.

Action 3 — Identify All SSH Event Types
index="main" sourcetype="_json"
| stats count by event_type


Result:
Event categories included:

Successful logins

Failed logins

Multiple failed attempts

Connections without authentication

This provides a high-level view of overall SSH authentication behavior.

Action 4 — Investigate Top Failed Source IPs

As a SOC analyst, next steps include:

Reviewing the top failed-login source IPs to determine whether they indicate:

Brute-force attempts

Malicious scanning behavior

Determining whether the source IP is internal or external

Internal IP:

Possible compromised account

Potential insider threat

Investigate host and reset credentials

Validate whether SSH access is required

External IP:

Likely brute-force or unauthorized probing

Checking for “failure → success” sequences from the same IP
(This is a strong indicator of account compromise.)

Recommended defensive actions:

Restrict SSH (port 22) or disable it if not required

Enforce key-based authentication / MFA

Implement auto-ban or rate-limiting for repeated authentication failures

Apply IP allow-listing for administrative access

Conclusion

This project demonstrated how to ingest and analyze SSH authentication logs within Splunk.
I identified failed login sources, evaluated overall SSH activity, categorized event types, and outlined appropriate SOC response actions.
This type of analysis is essential for detecting brute-force attempts, unauthorized access, and abnormal login activity in an environment.

Enforce strong SSH security controls (key-based auth, MFA, IP allow-lists)

Conclusion

This project demonstrated how to ingest and analyze SSH authentication logs within Splunk.
I identified failed login sources, analyzed total event volume, categorized authentication event types, and outlined appropriate SOC response actions.
This type of analysis is critical for detecting brute-force attempts, unauthorized access, and abnormal login activity within an environment.
