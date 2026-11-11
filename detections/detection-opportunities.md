# Detection Opportunities

Where a SOC could have broken this chain, organized by stage. Summarized from
[The DFIR Report](https://thedfirreport.com/2026/06/29/from-bing-search-to-ransomware-bumblebee-and-adaptixc2-deliver-akira-3/) (June 2026).
Treat these as detection ideas to validate in your own environment, not as
drop-in production rules.

## 1. DLL side-loading (Bumblebee)
- `consent.exe` executing from `AppData` or `%TEMP%` (Sigma: *System File Execution Location Anomaly*).
- `msimg32.dll` loading from a user-writable path.
- Mismatched PE metadata in `msimg32.dll`.

## 2. Process injection (AdaptixC2)
- `WAB.exe` / `AdgNsy.exe` with unbacked RWX memory regions.
- Sysmon Event ID 10 showing `consent.exe` accessing the injected process.
- `WmiPrvSE.exe` spawning unusual child processes.

## 3. Rogue account creation and group changes
- `net user <name> ... /add /dom`.
- `net group "enterprise admins" <name> /add`.
- Reactivation of the built-in Administrator account.

## 4. Credential theft
- `wbadmin` command lines referencing `ntds.dit` and the SYSTEM / SECURITY hives.
- `psql` executed against the VeeamBackup database.
- `comsvcs.dll` MiniDump invoked via `rundll32`.
- Remote execution through SMB, WMI, scheduled tasks, and DCOM.

## 5. Tunneling and remote access
- `ssh -R` originating from a domain controller.
- RDP logons (Logon Type 10 or 2) sourced from loopback.
- RustDesk launched with `--tray` or `--cm`.

## 6. Exfiltration
- FileZilla SSH client string `FileZilla_3.68.1` connecting outbound to port 22.
- Windows Event ID 5145 showing SYSVOL and credential-store path enumeration.

## 7. Ransomware and anti-recovery
- `Win32_Shadowcopy` deletion via WMI.
- Akira `locker.exe` executed with `-p` / `-n` flags.

## 8. Network
- Emerging Threats rules for Bumblebee CnC, RustDesk DNS, and SMB/DCERPC lateral movement.
- Download gateways hosted on common VPS providers (two-tier `/Get?q=` delivery pattern).
