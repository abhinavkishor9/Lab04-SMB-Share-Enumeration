
# Timeline – Lab 04: SMB Share Enumeration

## Investigation Timeline

| Sequence | Activity | Result |
|---|---|---|
| 1 | Started Metasploitable 2 in VMware | Target VM running |
| 2 | Executed `ifconfig` on Metasploitable | Confirmed target IP `192.168.174.129` |
| 3 | Executed `ping -c 4 192.168.174.129` from Ubuntu WSL | Four responses received; 0% packet loss |
| 4 | Launched `msfconsole` | Metasploit console started |
| 5 | Checked database using `db_status` | PostgreSQL connection confirmed |
| 6 | Selected `auxiliary/scanner/smb/smb_enumshares` | SMB Share Enumeration module loaded |
| 7 | Reviewed module information using `info` | Module purpose and capabilities examined |
| 8 | Reviewed configuration using `show options` | Available module settings examined |
| 9 | Configured `RHOSTS` | Target set to `192.168.174.129` |
| 10 | Disabled `SpiderShares` | Recursive share inspection disabled |
| 11 | Disabled `ShowFiles` | Detailed file display disabled |
| 12 | Disabled `SpiderProfiles` | User-profile spidering disabled |
| 13 | Reviewed configuration again | Target and scope settings verified |
| 14 | Executed `run` | SMB share enumeration completed |
| 15 | Reviewed scanner output | Five SMB shares identified |
| 16 | Analyzed share information | Three DISK shares and two IPC shares documented |
| 17 | Recorded investigation findings | Observations and limitations documented |

## Identified SMB Shares

| Share | Type | Description |
|---|---|---|
| print$ | DISK | Printer Drivers |
| tmp | DISK | oh noes! |
| opt | DISK | No description displayed |
| IPC$ | IPC | IPC Service (metasploitable server (Samba 3.0.20-Debian)) |
| ADMIN$ | IPC | IPC Service (metasploitable server (Samba 3.0.20-Debian)) |

## Key Observations

- Target connectivity from Ubuntu WSL was successful.
- Metasploit started despite non-blocking Ruby gem warnings.
- PostgreSQL database connectivity was confirmed.
- The SMB share enumeration scanner contacted TCP port 139.
- Five SMB shares were identified.
- Three shares were classified as DISK resources.
- Two shares were classified as IPC resources.
- IPC share descriptions disclosed the text `Samba 3.0.20-Debian`.
- Host identification was returned as `Unix (Unknown)`.
- Recursive share, file-display, and profile inspection options were disabled.
- Read/write access was not established by the supplied output.

## Final Status

**Lab status:** Completed

**Assessment type:** SMB share enumeration

**Shares identified:** 5

**DISK shares:** 3

**IPC shares:** 2

**Exploitation performed:** No

**File access performed:** No

**Post-exploitation performed:** No

**Conclusion:** SMB share enumeration successfully identified five resources exposed by the target. Their names, types, and returned descriptions were recorded. Actual resource access and permissions remain unestablished.
