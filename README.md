# azure-monitoring-lab
Built a monitoring pipeline in Microsoft Azure: Linux VM → Azure Monitor Agent → Log Analytics workspace → KQL queries → automated email alerts.

## Why
Practising the monitoring and incident-detection workflow used in day-to-day
IT support and cloud operations.

## Architecture
Ubuntu 22.04 VM (Standard_B1s, South Africa North)
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

## What I found
Within 11 hours the VM logged more then 750 failed SSH login attempts from external IP
addresses — a reminder of why exposed management ports need restricting.

## Cost control
Free-tier resources only, a ZAR 20 monthly budget with alerts at 50/80/100%,
subscription spending limit left enabled, and full teardown after testing.
Total spend: $2,98\R48,38.

## Screenshots

Log Analytics query results | ![<img width="1365" height="640" alt="Screenshot 2026-09-19 152731" src="https://github.com/user-attachments/assets/c2303df2-44ef-42f6-9f44-fb795a3dcaff" />
<img width="1365" height="696" alt="Screenshot 2026-09-19 110847" src="https://github.com/user-attachments/assets/3b8e2dc5-1775-4582-ae4d-1468c2d0e982" />
](screenshots/kql-cpu.png) |
Alert email received | ![<img width="1360" height="639" alt="Screenshot 2026-09-19 140809" src="https://github.com/user-attachments/assets/035a1239-200f-4170-b6de-762842548ed2" /> <img width="1365" height="640" alt="Screenshot 2026-09-19 140825" src="https://github.com/user-attachments/assets/ff834c04-ebfa-41d8-8a86-acd5927d4502" />
](screenshots/alert-email.png) |
Dashboard | ![<img width="1326" height="694" alt="Screenshot 2026-09-18 185733" src="https://github.com/user-attachments/assets/6ce858dc-f445-4926-9c72-e97ed7462e3c" /> <img width="1355" height="649" alt="Screenshot 2026-09-19 150921" src="https://github.com/user-attachments/assets/e388ae01-3324-4ebb-95be-cf45f0287751" />
](screenshots/dashboard.png) |

## What I'd do differently
- Restrict the NSG rule for SSH to my own IP instead of Any
- Use a log search alert on failed logins (small monthly cost per rule)
- Automate the whole deployment with Bicep
