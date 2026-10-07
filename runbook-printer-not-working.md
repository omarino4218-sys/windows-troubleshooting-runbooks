# Runbook: Printer Not Working

## Symptoms

- "My printer isn't printing" / print jobs disappear
- Documents stuck in the print queue
- "Printer offline" status even though it's powered on

## Quick checks (do these first)

1. **Is it powered on and on the right network?** — Network printers need the same network as the PC (not guest Wi-Fi).
2. **Is it just them or everyone?** — Everyone = printer/server issue, not the user's machine.
3. **Paper and toner** — Check the printer's display panel for actual error messages before touching the PC.

## Step-by-step fix

### A. Stuck print queue (most common)
1. Open `services.msc` → find **Print Spooler** → Stop it.
2. Delete everything in `C:\Windows\System32\spool\PRINTERS\`
3. Start the Print Spooler service again.
4. Have the user reprint. Quick PowerShell version:
   ```powershell
   Stop-Service Spooler -Force
   Remove-Item C:\Windows\System32\spool\PRINTERS\* -Force
   Start-Service Spooler
   ```

### B. "Printer offline"
1. Settings → Bluetooth & devices → Printers → select the printer → make sure
   **"Use printer offline" is NOT checked**.
2. Remove the printer and re-add it:
   - Network printer: add by IP/hostname (`\\printserver\PrinterName` or via TCP/IP port with the printer's IP from its config page).
   - USB printer: unplug, wait 10 seconds, plug into a different USB port.

### C. Driver issues
1. Device Manager or Print Management (`printmanagement.msc`) → check for warning icons.
2. Download the driver from the **manufacturer's site** (HP, Canon, Brother) — not a random driver site. Match the exact model.
3. For recurring driver corruption across machines, ask tier 2 about pushing the driver via print server / Group Policy.

### D. Network printer unreachable
```cmd
ping <printer-IP>
```
- Ping fails → printer lost its DHCP lease or is on the wrong VLAN. Power-cycle the printer; if it persists, escalate to netops (possible static-IP conflict).
- Ping works but won't print → try the printer's web interface at `http://<printer-IP>` to check its own error state.

## Escalate when

- Print server itself is down or the queue keeps jamming for multiple users
- Printer needs a static IP reservation / VLAN change (netops)
- Hardware failure: fuser errors, paper jam sensors, anything requiring a technician visit — log it with the vendor if under contract

## Ticket note template

```
Issue: Printer [model] [not printing / stuck queue / showing offline].
Troubleshooting: Printer powered on, [paper/toner OK]. Queue had [N] stuck jobs.
Actions: [Cleared spooler + restarted service / removed and re-added printer via
IP / reinstalled driver from manufacturer site].
Verification: Test page printed successfully at [time].
Follow-up: [None / monitoring — 3rd queue jam this month, may need print server review].
```
