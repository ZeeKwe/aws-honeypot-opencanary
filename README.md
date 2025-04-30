# aws-honeypot-opencanary
Lightweight AWS honeypot using OpenCanary with auto-tagging and detection via CloudWatch, SNS, Lambda, and GuardDuty
🧠 Project Summary

Goal: Detect malicious port scans and auto-respond within AWS using native services.

Honeypot Tool: OpenCanary running on EC2 (Ubuntu 22.04)

Detection: AWS CloudWatch Logs + CloudWatch Metric Filters

Alerting: SNS (email notification)

Response: Lambda function auto-tags EC2 as Compromised=true

Validation: GuardDuty independently detected the probe

Components:

EC2 Honeypot (Ubuntu 22.04):

Runs OpenCanary simulating fake services: SSH (2222), FTP, HTTP, Telnet

CloudWatch Logs:

Captures log output from OpenCanary

CloudWatch Alarm:

Detects suspicious log entries (e.g., port 2222 activity)

SNS Topic:

Sends email alert when alarm is triggered

Lambda Function:

Auto-tags EC2 instance with Compromised=true

GuardDuty:

Independently detects suspicious activity as a second-layer validation

🛠️ Implementation Steps

EC2 Setup

Launched Ubuntu 22.04 instance with ports 22 and 2222 open

Installed OpenCanary and configured it to fake SSH/FTP services

CloudWatch Logs

Installed and configured the CloudWatch Agent to push logs from EC2

Metric Filter + Alarm

Created a custom metric filter to watch for log entries indicating a connection attempt on port 2222

Alarm sends to SNS and logs to Log Group

SNS + Lambda Trigger

SNS sends email alert

Lambda triggered via log group trigger (not alarm directly) to tag EC2 instance

Lambda Detection Logic (Simplified)

if "connection attempt" in log and "port 2222" in log:
    trigger_alarm()
    tag_instance("Compromised=true")

GuardDuty Detection

Validated that AWS GuardDuty flagged the SSH probe from an attacker IP as a threat

🧪 Challenges & Fixes

OpenCanary conflicts with ports: Resolved by manually killing PID using port 21

CloudWatch Alarm didn’t trigger Lambda: Realised I needed a Log Group trigger, not an alarm action

CloudWatch Agent config: Installed .deb manually after apt version failed

✅ Result

Simulated an attacker probing a fake SSH service

Triggered CloudWatch log detection → SNS email → Lambda tagging

Instance was auto-tagged as compromised

GuardDuty confirmed external detection

📎 Summary for CV/GitHub

Deployed a honeypot in AWS using OpenCanary on EC2. Integrated CloudWatch Logs, Metric Filters, SNS, and Lambda to detect SSH probes and auto-tag the instance as compromised. Detection validated by GuardDuty. Demonstrates layered security and real-time cloud-based detection engineering.

🧠 Lessons Learned

Detection engineering involves more than spotting logs — you must build a responsive system

AWS-native tools (like CloudWatch + Lambda) can create powerful, automated defence layers

GuardDuty is valuable as a validation layer, even for custom traps

