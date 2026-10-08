# Case 001 — Slow Computer (SIMULATED)

> This is a **simulated** ticket note — a filled-in example showing what a
> completed ticket note looks like when you follow
> [runbook-slow-computer.md](../runbook-slow-computer.md). No real user,
> machine, or data is involved.

## Ticket note

```
Ticket: HD-1042 — "My computer is so slow"
Opened: 2026-10-01 09:14 | Tech: simulated

Issue: Slow performance — apps take minutes to open, typing lags.
Troubleshooting: Last reboot 2026-09-10 (3 weeks ago). Disk 6% free on C:.
  Task Manager: DISK at 100%, highest consumer was "Service Host: SysMain"
  with no single app dominating; Startup tab showed 11 enabled items.
Actions: [Rebooted first — no change / Disabled 6 non-essential startup items
  (Spotify, Adobe updater, etc.) / Ran Disk Cleanup, freed 14.2 GB /
  Did NOT disable SysMain — no authorization, evidence pointed at disk space
  and startup bloat instead].
Verification: User confirms noticeably faster at 10:02 — apps open in seconds.
Follow-up: Monitoring — machine has 4 GB RAM + spinning HDD; recommended
  SSD/RAM upgrade through hardware refresh channel.
```

## What this demonstrates

- Quick checks first (reboot date, disk space) before deeper steps.
- Evidence-driven decisions: SysMain was left alone because the data pointed
  elsewhere and there was no authorization to change services.
- Verification with the user, not just "looks fine to me".
- Follow-up captures the real root cause (aging hardware) for refresh planning.
