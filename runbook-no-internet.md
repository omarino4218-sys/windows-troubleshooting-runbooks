# Runbook: No Internet / Can't Connect to Network

## Symptoms

- "The internet is down" / "I can't get to any websites"
- Wi-Fi shows connected but nothing loads; or no network icon at all
- Works on phone but not on laptop (or vice versa)

## Quick checks (do these first)

1. **Is it just them?** — Ask if coworkers nearby are affected. One user = their machine. Everyone = network outage, check with netops and skip the rest of this runbook.
2. **Physical layer** — Cable plugged in? Wi-Fi actually connected to the right SSID (not the guest network, not their phone hotspot)?
3. **Airplane mode** — Check the quick settings panel. It happens more than anyone admits.

## Step-by-step fix (work bottom-up through the layers)

### 1. Check the IP configuration
```cmd
ipconfig
```
- **169.254.x.x (APIPA)?** → The machine couldn't reach DHCP. Skip to step 3.
- **No IP at all?** → Adapter disabled or driver issue. Skip to step 4.
- **Valid IP?** → Move to step 2.

### 2. Test the layers with ping
```cmd
ping <default-gateway>
ping 8.8.8.8
ping google.com
```
- Get the default gateway from the `ipconfig` output in step 1. The 8.8.8.8
  ping tests internet routing while bypassing DNS; the google.com ping tests
  DNS resolution.
- **A failed ping is inconclusive, not a diagnosis.** ICMP is commonly
  filtered by host and network firewalls — a failed ping with otherwise
  working traffic just means ping is blocked. Confirm with a TCP test instead:
  ```powershell
  Test-NetConnection google.com -Port 443
  ```
- Gateway fails → local network problem (cable, switch port, Wi-Fi AP).
- Gateway OK, 8.8.8.8 fails → routing/firewall issue past the local network.
- 8.8.8.8 OK, google.com fails → **DNS issue** (most common "connected but no internet"):
  ```cmd
  ipconfig /flushdns
  ```
  Then try again. If it persists, check what DNS servers are assigned (`ipconfig /all`) — they should be your internal DNS or a known public one.

### 3. Renew the DHCP lease
> **Warning:** `ipconfig /release` drops the machine's IP immediately. Over a
> remote session (RDP/Quick Assist) you will lose the session the moment you
> run it — only do this with the user on the phone or with another way back in.
```cmd
ipconfig /release
ipconfig /renew
```
If renew fails or times out, the DHCP server isn't reachable — verify the machine
is on the right VLAN/network and the DHCP scope isn't exhausted (escalate to netops).

### 4. Driver / adapter issues
> **Warning:** disabling the network adapter drops the connection instantly.
> Over a remote session, have the user (or a back-channel) ready to re-enable
> it — otherwise you lock yourself out.
1. Device Manager → Network adapters → look for yellow warnings.
2. Right-click → Disable, wait 5 seconds → Enable.
3. Still broken? Right-click → Uninstall device (check "delete driver" only if you
   have the driver ready), then Scan for hardware changes.
4. As a last resort before escalation:
   ```cmd
   netsh winsock reset
   netsh int ip reset
   ```
   Then reboot.

### 5. Wi-Fi specific
- Forget the network and rejoin (clears bad saved profiles).
- Check they're not on a captive portal network (hotel/guest) without completing the login page.
- 2.4 GHz vs 5 GHz: if the 5 GHz signal is weak at their desk, force 2.4 GHz in adapter properties → Preferred Band.

## Escalate when

- Multiple users affected (likely switch, AP, or ISP issue — netops owns it)
- DHCP scope exhausted or rogue DHCP server suspected
- Problem follows the user across machines (possible account/network policy issue)
- `netsh` resets + reboot didn't help and hardware looks fine (possible OS corruption — tier 2)

## Ticket note template

```
Issue: No network connectivity.
Scope: [Single user / multiple users].
Troubleshooting: ipconfig showed [valid IP / APIPA / no adapter]. Ping results:
gateway [OK/fail], 8.8.8.8 [OK/fail], google.com [OK/fail].
Actions: [flushdns / renew / driver reinstall / rejoined Wi-Fi / netsh reset + reboot].
Verification: User browsing and email confirmed working at [time].
Follow-up: [None / monitoring — second occurrence this week].
```

## Related automation

- [`Test-NetworkConnectivity.ps1`](https://github.com/omarino4218-sys/powershell-helpdesk-toolkit/blob/main/Test-NetworkConnectivity.ps1) — layered gateway/DNS/internet/TCP check
