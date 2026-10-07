# Runbook: Slow Computer

## Symptoms

- "My computer is so slow" — apps take forever to open, typing lags
- Fan running loud constantly, machine hot
- Slow only at certain times (e.g., right after login, or during Teams calls)

## Quick checks (do these first)

1. **Reboot** — Ask when they last restarted. "3 weeks ago" explains a lot.
2. **How full is the disk?** — Right-click C: → Properties. Under ~10% free and Windows chokes.
3. **Is it slow everywhere or in one app?** — One app = app issue. Everything = system issue.

## Step-by-step fix

### 1. Find what's hogging resources
Open Task Manager (`Ctrl + Shift + Esc`) → **Processes** tab, sort by CPU / Memory / Disk:
- **100% disk** on a spinning HDD → the machine needs an SSD (note it for hardware refresh); short-term, disable Superfetch/SysMain.
- One app eating CPU constantly → end it, check if it recurs after reboot.
- **Startup** tab → disable anything non-essential (Spotify, updaters, chat apps they don't use). This is the highest-ROI 2 minutes in all of helpdesk.

### 2. Free disk space
1. Disk Cleanup (`cleanmgr`) — temp files, recycle bin, Windows Update cleanup.
2. Check for the usual space hogs: Downloads folder, old installers, Teams cache
   (`%appdata%\Microsoft\Teams` can balloon to multiple GB).
3. If C: is still nearly full after cleanup, escalate — don't start deleting things you're unsure about.

### 3. Malware check
1. Confirm Windows Security / the corporate EDR (CrowdStrike, SentinelOne) is running and definitions are current.
2. Run a **full** scan, not quick. If the EDR console shows the agent as unhealthy/disconnected, that's a tier-2/security ticket.
3. Multiple detections or reinfection after cleaning → escalate to security; the machine may need reimaging.

### 4. When it's just old
If Task Manager shows constant 90%+ RAM with normal apps open and the machine has
4 GB RAM and a spinning disk, no amount of cleanup fixes it — document the specs
and recommend a hardware refresh through the proper channel.

## Escalate when

- Malware found that the EDR can't clean, or reinfection after cleanup
- Failing drive symptoms (clicking, SMART warnings in Event Viewer → Windows Logs → System, source `disk`)
- Hardware refresh needed (old specs, confirmed by Task Manager data)
- Slowness affects multiple machines after a patch rollout (possible bad update — tier 2)

## Ticket note template

```
Issue: Slow performance.
Troubleshooting: Last reboot [date]. Disk [X]% free. Task Manager: [CPU/MEM/DISK]
highest consumer was [process] at [Y]%.
Actions: [Disabled N startup items / ran Disk Cleanup, freed X GB / ran full
malware scan — clean / ended runaway process].
Verification: User confirms noticeably faster at [time].
Follow-up: [None / recommended SSD/RAM upgrade — specs: ...].
```
