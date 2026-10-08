# Runbook: Email Not Syncing (Outlook / Teams)

## Symptoms

- "My emails aren't coming in" / Outlook stuck on "Trying to connect" or "Disconnected"
- New emails arrive on phone but not in desktop Outlook
- Teams shows old messages or won't send

## Quick checks (do these first)

1. **Is it just email or all internet?** — If nothing loads, use the [no-internet runbook](runbook-no-internet.md) instead.
2. **Check the Outlook status bar** (bottom-right): "Connected to Microsoft Exchange" = healthy. "Disconnected", "Trying to connect", or "Need password" tells you where to look.
3. **Ask about password changes** — a recent password change breaks Outlook/Teams auth silently until re-authenticated.

## Step-by-step fix

### A. "Need password" / auth prompt looping
1. Close Outlook completely (check the system tray — Outlook loves to hide there).
2. Windows Settings → Accounts → **Access work or school** → select the account → Disconnect, then re-add it.
3. Reopen Outlook and sign in fresh — this clears the stale token. If MFA is involved, have the phone with the authenticator app ready.

### B. Stuck on "Trying to connect"
1. Confirm they're on the right network/VPN — Exchange Online needs internet, on-prem Exchange may need VPN.
2. Restart Outlook. If still stuck, start Outlook in safe mode to rule out add-ins:
   ```cmd
   outlook.exe /safe
   ```
   Works in safe mode → disable add-ins one by one (File → Options → Add-ins → COM Add-ins → Go).

### C. Mailbox not updating (send/receive works, data is stale)
The local OST cache is likely corrupt. Rebuild it:
1. Close Outlook.
2. Control Panel → Mail → Data Files → note the location of the `.ost`, then delete (or rename) it.
3. Reopen Outlook — it rebuilds the OST from the server. Warn the user this takes a while on large mailboxes; don't do it 5 minutes before a meeting.
4. Alternative for shared-mailbox weirdness: File → Account Settings → uncheck "Download shared folders" for the affected mailbox.

### D. Teams-specific
1. Sign out of Teams fully (profile pic → Sign out), clear the cache:
   `%appdata%\Microsoft\Teams` → delete contents, restart Teams.
2. "New Teams" toggle issues: if the user was migrated, make sure they're launching the new client, not the classic one.

## Escalate when

- Mailbox quota exceeded and the user needs retention/archive policy changes (messaging admin)
- Multiple users can't connect (Exchange Online outage or on-prem server issue — check the Microsoft 365 Service Health dashboard first)
- Suspected compromised mailbox (unusual sent items, forwarding rules the user didn't create — loop in security immediately)
- On-prem Exchange: any server-side issue is tier 2 / messaging team

## Ticket note template

```
Issue: Email not syncing in [Outlook/Teams].
Troubleshooting: Status bar showed [Connected/Disconnected/Need password].
Internet otherwise [working/down]. Recent password change: [yes/no].
Actions: [Re-authenticated work account / rebuilt OST / cleared Teams cache /
disabled faulty add-in: name].
Verification: New test email sent and received at [time]; Teams messages current.
Follow-up: [None / advised user on VPN-before-Outlook if remote].
```
