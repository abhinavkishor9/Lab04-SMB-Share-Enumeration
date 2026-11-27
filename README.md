# Lab 04 – SMB Share Enumeration
## Overview

This lab focuses on identifying SMB shares exposed by a Metasploitable 2 system using the Metasploit Framework.

In Lab 03, SMB service enumeration identified SMB version 1, returned Unix as the host operating-system identification, and reported that SMB signing was not required.

Building on those findings, Lab 04 investigates the shared resources exposed through the target's SMB service.

The Metasploit auxiliary module `auxiliary/scanner/smb/smb_enumshares` was used to identify share names, share types, and descriptions returned by the server.

The investigation was limited to share enumeration. Recursive directory inspection, file access, file modification, and exploitation were not performed.

## Lab Environment

| Component | Details |
|---|---|
| Attacker environment | Ubuntu 24.04 under WSL |
| Target | Metasploitable 2 |
| Target IP | 192.168.174.129 |
| Target network interface | eth0 |
| Framework | Metasploit Framework |
| Metasploit version | 6.5.12-dev- |
| Database | PostgreSQL |
| Auxiliary module | auxiliary/scanner/smb/smb_enumshares |
| Target port observed | TCP 139 |

## Objectives

- Understand SMB shares and their purpose.
- Identify shared resources exposed by an SMB server.
- Use Metasploit's SMB share enumeration module.
- Review share names, types, and descriptions.
- Understand the difference between share discovery and resource access.
- Record findings without assuming file permissions or exploitability.

## Activities Performed

1. Verified the Metasploitable 2 network configuration.
2. Confirmed the target IP address as `192.168.174.129`.
3. Tested connectivity from Ubuntu WSL using `ping`.
4. Launched Metasploit Framework.
5. Verified PostgreSQL connectivity using `db_status`.
6. Selected `auxiliary/scanner/smb/smb_enumshares`.
7. Reviewed module information using `info`.
8. Examined available module options using `show options`.
9. Configured `RHOSTS` with the target IP.
10. Disabled recursive share inspection and file-display options.
11. Executed the SMB share enumeration scanner.
12. Reviewed and documented the returned shares.

## Enumeration Configuration

| Option | Configured value |
|---|---|
| RHOSTS | 192.168.174.129 |
| SpiderShares | false |
| ShowFiles | false |
| SpiderProfiles | false |
| SMBUser | Not configured |
| SMBPass | Not configured |
| THREADS | 1 |

The module contacted TCP port 139 during the scan.

## Key Findings

The scanner identified five SMB shares:

| Share | Type | Description |
|---|---|---|
| print$ | DISK | Printer Drivers |
| tmp | DISK | oh noes! |
| opt | DISK | No description displayed |
| IPC$ | IPC | IPC Service (metasploitable server (Samba 3.0.20-Debian)) |
| ADMIN$ | IPC | IPC Service (metasploitable server (Samba 3.0.20-Debian)) |

The scanner also returned:

`Unix (Unknown)`

The Samba 3.0.20-Debian text was disclosed in the IPC share descriptions. It is recorded as returned server-description information rather than an independently validated software-version result.

## Analysis

The results confirm that the target disclosed five SMB shares through the enumeration request.

Three shares were identified as DISK resources:

- print$
- tmp
- opt

Two shares were identified as IPC resources:

- IPC$
- ADMIN$

The descriptions provide additional context about the exposed resources. The IPC share descriptions also disclose the text `Samba 3.0.20-Debian`.

The scanner output did not explicitly establish whether the identified shares were readable or writable. Share discovery must therefore be distinguished from successful access to share contents.

## Scope and Limitations

The lab was restricted to SMB share enumeration.

The following activities were not performed:

- Recursive directory inspection.
- File enumeration or downloading.
- File modification or deletion.
- Password guessing or brute-force attacks.
- Credential attacks.
- Exploitation.
- Payload execution.
- Session creation.
- Post-exploitation.

The findings represent information returned by the scanner during this exercise. They do not constitute a complete assessment of SMB permissions or share contents.

## Conclusion

The Metasploit SMB share enumeration module successfully identified five shares on the Metasploitable 2 target through TCP port 139.

The results included three DISK shares and two IPC shares. The returned descriptions also disclosed the text `Samba 3.0.20-Debian`.

The exercise demonstrated how SMB share enumeration can reveal network resources and service information. However, the results did not establish successful file access or read/write permissions.

The investigation remained limited to reconnaissance and enumeration.

## Tools Used

- Metasploit Framework
- Ubuntu 24.04 under WSL
- Metasploitable 2
- VMware Workstation
- PostgreSQL database

## Key Learning

SMB share enumeration helps identify resources exposed by a network file-sharing service. A discovered share is not automatically accessible, and its presence does not prove that files can be read or modified. Findings must be interpreted according to the actual scanner output.
