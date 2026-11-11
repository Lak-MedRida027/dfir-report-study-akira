# From a Bing Search to Full-Domain Ransomware in 44 Hours

A blue-team breakdown of a 2026 intrusion where **SEO poisoning** delivered the
**Bumblebee** loader, which handed the attacker an **AdaptixC2** beacon and ended
in **Akira** ransomware across an entire Active Directory.

Part of my **Learning in Public** series: I work through notable public DFIR
reports and rebuild each one as a detection-focused study, with the MITRE ATT&CK
mapping and the detection opportunity for every stage.

> **Source and credit.** This repository is a study and summary of the public
> report **"From Bing Search to Ransomware: Bumblebee and AdaptixC2 Deliver Akira"**
> by **The DFIR Report** (June 2026). All technical findings, indicators, and the
> intrusion timeline are theirs. Everything here is written in my own words for
> learning purposes. Read the original and full report here:
> https://thedfirreport.com/2026/06/29/from-bing-search-to-ransomware-bumblebee-and-adaptixc2-deliver-akira-3/

---

## TL;DR

| Metric | Value |
|---|---|
| Initial access | SEO poisoning (fake ManageEngine OpManager download) |
| Loader | Bumblebee (DLL side-loading via `consent.exe`) |
| C2 framework | AdaptixC2 (HTTP beacon, default profile) |
| Ransomware | Akira (`locker.exe`) |
| Time to ransomware | ~44 hours |
| Attacker dwell time | ~5 days |
| Data exfiltrated | ~77 GB (SFTP via FileZilla) |
| Privilege escalation | None needed; identity theft to Enterprise Admin |

**One takeaway:** prevention failed at the search bar, but detection had at least
six clean chances afterward. Almost every post-access stage was visible to a SOC.

---

## The attack chain, stage by stage

### 1. Initial access — poisoned search results
An IT administrator searched Bing for "ManageEngine OpManager" and reached a
lookalike site (`opmanager[.]pro`) that redirected to a fake download center. The
trojanized MSI installed the legitimate OpManager as a decoy while dropping the
Bumblebee loader. The installer was even moved to a network share before being run.

### 2. The loader — Bumblebee behind a trusted binary
`consent.exe` was side-loaded with a malicious `msimg32.dll` from `%TEMP%`. The
loader checked the machine locale against CIS-region locales, then beaconed to
algorithm-generated (DGA) domains.

### 3. Command and control — AdaptixC2
About 5 hours in, a renamed `WAB.exe` (dropped as `AdgNsy.exe`) was launched
through WMI, so it spawned under `WmiPrvSE.exe`. Shellcode was injected, giving an
AdaptixC2 HTTP beacon. Discovery followed with `systeminfo`, `nltest`, `whoami`,
`ping`, and a network scan.

### 4. Credential access — straight to Enterprise Admin
Two rogue domain accounts were created (`backup_DA`, `backup_EA`), and `backup_EA`
was added to Enterprise Admins. `ntds.dit` plus the SYSTEM and SECURITY hives were
copied with `wbadmin`. Veeam credentials were dumped via `psql` and DPAPI, and
LSASS was dumped remotely on three hosts with `lsassy`. No exploit, no privilege
escalation: the attacker held domain admin from the start of lateral movement.

### 5. Lateral movement and persistence
RDP was used across nine accounts, including through a reverse SSH tunnel
(`ssh -R`). RustDesk was installed as a service for durable remote access.
SoftPerfect Netscan and `Invoke-ShareFinder` mapped hosts and shares.

### 6. Exfiltration and impact
Around 77 GB was exfiltrated over SFTP using FileZilla. Akira (`locker.exe`) was
deployed on the backup server first, then the root domain, then the child domain
controller two days later. Volume shadow copies were deleted via WMI, and
FileZilla was uninstalled to remove evidence.

---

## Repository contents

| Path | What's inside |
|---|---|
| [`mitre/attack-mapping.md`](mitre/attack-mapping.md) | Full MITRE ATT&CK technique mapping by tactic |
| [`detections/detection-opportunities.md`](detections/detection-opportunities.md) | Where a SOC could break the chain, per stage |
| [`iocs/iocs.md`](iocs/iocs.md) | Indicators (defanged) with a verification note |
| [`assets/DFIR_Akira_Carousel_Rida.pdf`](assets/DFIR_Akira_Carousel_Rida.pdf) | 10-slide LinkedIn carousel summary |

---

## Why this one

My recent work has centered on C2 beaconing detection (Zeus with RITA), in-memory
attack detection with Sysmon, and detection engineering. This intrusion is a live
AdaptixC2 beacon, DLL side-loading, and process injection end to end, which made
it a natural next study.

---

## Author

**Mohammed Rida Lakhdari** — Information Security Engineering student, Purple Team instructor.

- Portfolio: https://rlak.vercel.app
- GitHub: https://github.com/Lak-MedRida027

*Learning in Public. Analysis and summary of a report by [The DFIR Report](https://thedfirreport.com/). All credit for the original research is theirs.*
