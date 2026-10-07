# Runbook: User Can't Log In

## Symptoms

- "My password isn't working" / "I can't log into my computer"
- "The sign-in method you're trying to use isn't allowed" or account locked messages
- User can log into email on their phone but not the laptop (or vice versa)

## Quick checks (do these first)

1. **Caps Lock** — seriously. Check it before anything else.
2. **Which password?** — Ask if they recently changed it. People try the old one out of habit.
3. **Is it just one machine or everywhere?** — If phone/email works but the laptop doesn't, it's likely cached credentials (see below).
4. **Check the account status in AD** before resetting anything:
   - Open Active Directory Users and Computers → find the user → check for the red "locked out" indicator, or run:
   ```powershell
   Search-ADAccount -LockedOut | Select-Object Name, SamAccountName
   ```

## Step-by-step fix

### A. Account is locked out
```powershell
# Unlock the account
Unlock-ADAccount -Identity jdoe

# Confirm
Get-ADUser jdoe -Properties LockedOut | Select-Object Name, LockedOut
```
Then have the user try again. If it locks again immediately, something is hammering
it with the old password — usually a phone with saved Wi-Fi/Exchange credentials
or a mapped drive. Ask the user to update the password on their phone too.

### B. Password reset (forgotten or expired)
```powershell
# Reset and force change at next logon
Set-ADAccountPassword -Identity jdoe -Reset -NewPassword (ConvertTo-SecureString "Temp#2026!" -AsPlainText -Force)
Set-ADUser jdoe -ChangePasswordAtLogon $true
```
Give the user the temp password over a verified channel (call them back at their
known number — never email a password). Confirm they can log in and change it.

### C. Cached credentials (works on phone, not on laptop off-network)
Laptops cache the last successful logon. If the user changed their password
elsewhere and the laptop is off the corporate network/VPN, the laptop still
expects the OLD password.
1. Connect to VPN or plug into the office network.
2. Lock the screen (`Win + L`) and log in with the NEW password.
3. If VPN-before-logon is needed: on the Windows logon screen, use the network
   icon to connect first (or `Ctrl+Alt+Del` → network sign-in option).

### D. MFA issues (Microsoft Authenticator / Okta)
1. Have the user check for the push notification — expired pushes are the #1 cause.
2. If they got a new phone: they need an MFA reset from an admin — verify
   identity first (employee ID + manager confirmation per your org's policy).
3. After reset, walk them through re-enrollment at `aka.ms/mfasetup`.

## Escalate when

- Account locks repeatedly with no misbehaving device found (possible brute-force — loop in security)
- The user is a VIP/executive and policy requires manager approval for resets
- You suspect the account is compromised (logons from unusual locations in the sign-in logs)
- MFA reset is needed and your role doesn't have admin rights for it

## Ticket note template

```
Issue: User unable to log in (locked out / forgotten password / cached creds).
Troubleshooting: Verified Caps Lock off; checked AD — account [locked / active].
Actions: [Unlocked account / reset password + forced change at logon / had user
connect VPN and log in with new password].
Verification: User logged in successfully at [time].
Follow-up: Advised user to update saved password on mobile devices.
```
