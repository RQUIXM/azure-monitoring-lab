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


Log Analytics query results | ![](screenshots/kql-cpu.png) |
Alert email received | ![](screenshots/alert-email.png) |
Dashboard | ![](screenshots/dashboard.png) |

## What I'd do differently
- Restrict the NSG rule for SSH to my own IP instead of Any
- Use a log search alert on failed logins (small monthly cost per rule)
- Automate the whole deployment with Bicep
