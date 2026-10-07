# Windows Troubleshooting Runbooks

Tier-1 knowledge-base articles for the five tickets every helpdesk tech sees
weekly. Each runbook follows the same structure a real internal KB uses:
symptoms → quick checks → step-by-step fix → when to escalate → how to write
the ticket note.

## Runbooks

| Runbook | Covers |
|---|---|
| [`runbook-cant-log-in.md`](runbook-cant-log-in.md) | Password resets, account lockouts, cached credentials, MFA issues |
| [`runbook-no-internet.md`](runbook-no-internet.md) | Layered diagnosis: physical → `ipconfig` → gateway/DNS pings → drivers |
| [`runbook-printer-not-working.md`](runbook-printer-not-working.md) | Print spooler, stuck queues, driver reinstalls, network printers |
| [`runbook-slow-computer.md`](runbook-slow-computer.md) | Task Manager triage, startup apps, disk space, malware scan |
| [`runbook-email-not-syncing.md`](runbook-email-not-syncing.md) | Outlook/Teams connectivity, OST rebuild, MFA re-auth |

## How to use these

1. Match the user's symptom to a runbook.
2. Run the **quick checks** first — they resolve ~60% of these tickets in under 5 minutes.
3. If quick checks fail, work the **step-by-step** section top to bottom.
4. Hit an **escalation criterion**? Stop, document what you tried, escalate with the ticket note template filled in.

## Skills demonstrated

- Structured tier-1 troubleshooting (CompTIA 6-step methodology in practice)
- Windows admin tools: Event Viewer, Services, Device Manager, Task Manager
- Command line: `ipconfig`, `ping`, `nslookup`, `netstat`, PowerShell basics
- Ticket documentation — every runbook ends with a note template because
  "fixed it" is not a ticket note
