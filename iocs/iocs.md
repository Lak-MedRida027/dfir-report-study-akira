# Indicators of Compromise (defanged)

Summarized from [The DFIR Report](https://thedfirreport.com/2026/06/29/from-bing-search-to-ransomware-bumblebee-and-adaptixc2-deliver-akira-3/) (June 2026).

> **Verify before blocking.** Indicators are reproduced here for study and are
> defanged. The original report notes a small inconsistency in one DGA-style
> string (`2rxyt8yrhq0bgj` vs `2rxyt9urhq0bgj`), so confirm every indicator
> against the source report before acting on it. Always validate in your own
> environment first.

## Domains
| Indicator | Role |
|---|---|
| `opmanager[.]pro` | Lookalike initial-access site |
| `download-center[.]online` | Fake download center (redirect target) |
| `ip-scanner[.]org` | Related two-tier delivery (Swisscom-observed variant) |

## IP addresses
| Indicator | Role |
|---|---|
| `188.40.187[.]145` | Bumblebee C2 |
| `109.205.195[.]211` | Bumblebee C2 |
| `172.96.137[.]160` | AdaptixC2 beacon |
| `193.242.184[.]150` | Reverse SSH tunnel / RDP relay |
| `185.174.100[.]203` | SFTP exfiltration (Zeek-observed) |

## Files and tooling
| Indicator | Role |
|---|---|
| `msimg32.dll` | Malicious side-loaded DLL (Bumblebee) |
| `consent.exe` | Abused trusted Windows binary (side-loading host) |
| `AdgNsy.exe` | Renamed `WAB.exe` used for AdaptixC2 |
| `locker.exe` | Akira ransomware binary |
| `backup_DA`, `backup_EA` | Rogue domain accounts |

## Notable commands
| Command | Context |
|---|---|
| `locker.exe -p=G:\ -n=15` | Akira encryption run |
| `win.exe -n=2 netonly` | Akira in the Swisscom-observed variant |

## Legitimate tools abused
RustDesk, FileZilla, lsassy / Impacket, `wbadmin`, `psql`, SoftPerfect Netscan,
`Invoke-ShareFinder`, OpenSSH.
