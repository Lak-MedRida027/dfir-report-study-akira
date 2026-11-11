# MITRE ATT&CK Mapping

Techniques observed in the intrusion, grouped by tactic. Summarized from
[The DFIR Report](https://thedfirreport.com/2026/06/29/from-bing-search-to-ransomware-bumblebee-and-adaptixc2-deliver-akira-3/) (June 2026).

## Initial Access
| Technique | ID | Notes |
|---|---|---|
| Drive-by Compromise | T1189 | SEO-poisoned search result to a lookalike download site |
| User Execution: Malicious File | T1204.002 | Trojanized OpManager MSI run by the admin |

## Execution
| Technique | ID | Notes |
|---|---|---|
| Command and Scripting Interpreter: PowerShell | T1059.001 | Post-exploitation scripting |
| Windows Management Instrumentation | T1047 | Launched the renamed `WAB.exe` under `WmiPrvSE.exe` |

## Persistence
| Technique | ID | Notes |
|---|---|---|
| Create Account: Domain Account | T1136.002 | `backup_DA`, `backup_EA` |
| Create or Modify System Process: Windows Service | T1543.003 | RustDesk installed as a service |

## Privilege Escalation / Defense Evasion
| Technique | ID | Notes |
|---|---|---|
| Hijack Execution Flow: DLL Side-Loading | T1574.001 | `consent.exe` + malicious `msimg32.dll` |
| Process Injection | T1055 | Shellcode injected into the renamed `WAB.exe` |
| Masquerading | T1036 | `WAB.exe` renamed to `AdgNsy.exe` |
| Obfuscated Files or Information: Command Obfuscation | T1027.010 | Mixed-case command lines |
| Indicator Removal: File Deletion | T1070.004 | FileZilla uninstalled to remove evidence |

## Credential Access
| Technique | ID | Notes |
|---|---|---|
| OS Credential Dumping: LSASS Memory | T1003.001 | `lsassy` on three hosts; `comsvcs.dll` MiniDump |
| OS Credential Dumping: NTDS | T1003.003 | `ntds.dit` copied with `wbadmin` |
| Credentials from Password Stores | T1555 | Veeam credentials via `psql` + DPAPI; password-manager dirs |

## Discovery
| Technique | ID | Notes |
|---|---|---|
| Account Discovery: Domain Account | T1087.002 | `whoami`, AD enumeration |
| Permission Groups Discovery: Domain Groups | T1069.002 | `nltest` |
| Domain Trust Discovery | T1482 | AD / child-domain enumeration |
| Remote System Discovery | T1018 | SoftPerfect Netscan |
| Network Share Discovery | T1135 | `Invoke-ShareFinder` |
| Network Service Discovery | T1046 | Network scan after beacon |

## Lateral Movement
| Technique | ID | Notes |
|---|---|---|
| Remote Services: RDP | T1021.001 | Across nine accounts, incl. via reverse SSH tunnel |
| Remote Services: DCOM | T1021.003 | Remote execution vector for LSASS dumping |
| System Services: Service Execution | T1569.002 | Service-based remote execution |

## Command and Control
| Technique | ID | Notes |
|---|---|---|
| Application Layer Protocol: Web Protocols | T1071.001 | AdaptixC2 HTTP beacon |
| Proxy | T1090 | Reverse SSH tunnel proxying RDP |
| Remote Access Software | T1219 | RustDesk |
| Dynamic Resolution: DGA | T1568.002 | Bumblebee DGA domains |

## Exfiltration
| Technique | ID | Notes |
|---|---|---|
| Exfiltration Over C2 Channel | T1041 | ~2.5 GB from a DC over the C2 / SSH |
| Exfiltration Over Symmetric Encrypted Non-C2 Protocol | T1048.001 | ~77 GB over SFTP via FileZilla |

## Impact
| Technique | ID | Notes |
|---|---|---|
| Data Encrypted for Impact | T1486 | Akira `locker.exe` |
| Inhibit System Recovery | T1490 | Volume shadow copies deleted via WMI |
