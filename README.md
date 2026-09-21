# azure-monitoring-lab
Built a monitoring pipeline in Microsoft Azure: Linux VM → Azure Monitor Agent → Log Analytics workspace → KQL queries → automated email alerts.

## Why
Practising the monitoring and incident-detection workflow used in day-to-day
IT support and cloud operations.

## Architecture
Ubuntu 22.04 VM (Standard_B2ats, South Africa North)
  → Azure Monitor Agent, configured by a Data Collection Rule
  → Log Analytics workspace (30-day retention, free tier)
  → Metric alert rule + Activity Log alert rule
  → Action group (email notification)

## What I built
- Log Analytics workspace collecting Syslog (auth, authpriv, syslog, daemon)
  and performance counters at 60s sampling
- Data Collection Rule scoping exactly what the agent gathers
- KQL queries for agent heartbeat, CPU trend, disk free space, and failed
  SSH authentication attempts
- Metric alert firing at >80% CPU over a 5-minute window
- Activity Log alert on VM deletion
- Shared dashboard with pinned query tiles

## Testing
Generated synthetic CPU load with `stress-ng` and confirmed the alert fired
and the notification email was delivered within 10 minutes.
| | |
|---|---|
| Testing `stress-ng`| ![<img width="1365" height="718" alt="Screenshot 2026-09-18 204421" src="https://github.com/user-attachments/assets/6210f671-ccff-40eb-b78c-446e35392082"/> <img width="1361" height="712" alt="Screenshot 2026-09-19 140909" src="https://github.com/user-attachments/assets/df41b5fc-4ba0-4942-bb3d-c8c87d10e056"/>](screenshots/Testing`stress-ng`.png)|

## What I found
Within 11 hours the VM logged more then 750 failed SSH login attempts from external IP
addresses — a reminder of why exposed management ports need restricting.

## Cost control
Free-tier resources only, a ZAR 20 monthly budget with alerts at 50/80/100%,
subscription spending limit left enabled, and full teardown after testing.
Total spend: $2,98\R48,38.

## Screenshots
| | |
|---|---|
| Log Analytics query results | ![<img width="1365" height="640" alt="Screenshot 2026-09-19 152731" src="https://github.com/user-attachments/assets/15f3583f-0b95-44e8-a8fa-b4bf3ebce447" />
<img width="1365" height="696" alt="Screenshot 2026-09-19 110847" src="https://github.com/user-attachments/assets/523640be-b43c-4971-ac99-0fa7ede1df4d" />](screenshots/kql-cpu.png) |
| Alert email received | ![<img width="1360" height="639" alt="Screenshot 2026-09-19 140809" src="https://github.com/user-attachments/assets/7d1d9277-9719-4016-8c0a-332f24e40a0d" />
<img width="1365" height="640" alt="Screenshot 2026-09-19 140825" src="https://github.com/user-attachments/assets/938a5fed-530b-419a-8a1f-543d74bc3a78" />](screenshots/alert-email.png) |
| Dashboard | ![<img width="1326" height="694" alt="Screenshot 2026-09-18 185733" src="https://github.com/user-attachments/assets/63b73b2a-bdbd-4d2e-8fd2-1853c86732f6" />
<img width="1355" height="649" alt="Screenshot 2026-09-19 150921" src="https://github.com/user-attachments/assets/8a6a5d7e-a045-4817-b396-35553be5b9cd" />](screenshots/dashboard.png) |
## What I'd do differently
- Restrict the NSG rule for SSH to my own IP instead of Any
- Use a log search alert on failed logins (small monthly cost per rule)
- Automate the whole deployment with Bicep
