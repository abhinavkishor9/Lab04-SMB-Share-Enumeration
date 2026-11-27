# Investigation Notes
## 1. Investigation Summary

This investigation examined the SMB shares exposed by a Metasploitable 2 virtual machine using the Metasploit Framework.

The target was previously identified as running SMB version 1 during Lab 03. Lab 04 focused on identifying the shared resources exposed by the SMB service.

The Metasploit auxiliary module `auxiliary/scanner/smb/smb_enumshares` was used to collect share names, share types, and descriptions.

The investigation was restricted to share enumeration. Recursive directory inspection and file access were not performed.

## 2. Target Information

| Attribute | Observed Value |
|---|---|
| Target system | Metasploitable 2 |
| Target IP | 192.168.174.129 |
| Network interface | eth0 |
| IPv4 address | 192.168.174.129 |
| Subnet mask | 255.255.255.0 |
| Broadcast address | 192.168.174.255 |
| Attacker environment | Ubuntu WSL |

The target IP address was verified from the Metasploitable console using:

`ifconfig`

The `eth0` interface was UP and had the IPv4 address `192.168.174.129`.

## 3. Network Connectivity Validation

From Ubuntu WSL, the following command was executed:

`ping -c 4 192.168.174.129`

### Result

| Metric | Result |
|---|---|
| Packets transmitted | 4 |
| Packets received | 4 |
| Packet loss | 0% |
| Minimum RTT | 0.871 ms |
| Average RTT | 1.250 ms |
| Maximum RTT | 1.552 ms |

### Observation

All four ICMP echo requests received responses from the target.

This confirmed network-level reachability between Ubuntu WSL and Metasploitable 2 at the time of testing.

## 4. Metasploit Environment Validation

Metasploit was launched using:

`msfconsole`

The console reported:

- Metasploit version: `6.5.12-dev-`
- Existing database found during startup.
- Database startup was attempted.

The database connection was verified using:

`db_status`

### Result

`Connected to msf. Connection type: postgresql.`

### Observation

Metasploit started successfully and its PostgreSQL database connection was active.

Ruby gem warnings involving `base64` and `logger` appeared during startup. These warnings did not prevent the console from starting or the SMB enumeration module from executing.

## 5. Module Selection

The following module was selected:

`use auxiliary/scanner/smb/smb_enumshares`

Module information was reviewed using:

`info`

### Module Details

| Attribute | Value |
|---|---|
| Module name | SMB Share Enumeration |
| Module path | auxiliary/scanner/smb/smb_enumshares |
| Module category | Auxiliary scanner |
| Purpose | Identify shares provided by an SMB service |
| Module rank | Normal |
| Check supported | No |

The module description states that it determines which shares are provided by the SMB service and which ones are readable or writable. It can also collect additional information such as share types, directories, files, and timestamps.

The module documentation describes its default share enumeration request as a RubySMB `net_share_enum_all` request using SRVSVC.

## 6. Module Configuration

The available options were reviewed using:

`show options`

The target was configured using:

`set RHOSTS 192.168.174.129`

The following options were explicitly configured:

| Option | Configured Value |
|---|---|
| RHOSTS | 192.168.174.129 |
| SpiderShares | false |
| ShowFiles | false |
| SpiderProfiles | false |

Other relevant settings observed:

| Option | Value |
|---|---|
| Share | Not specified |
| SMBUser | Not configured |
| SMBPass | Not configured |
| SMBDomain | . |
| THREADS | 1 |
| LogSpider | 3 |
| MaxDepth | 999 |

The recursive share and profile inspection options were disabled to keep the activity focused on share identification.

No SMB username or password was supplied.

## 7. Scanner Execution

The scanner was executed using:

`run`

### Actual Output

```text
[*] 192.168.174.129:139 - Unix  (Unknown)
[+] 192.168.174.129:139 - print$ - (DISK) Printer Drivers
[+] 192.168.174.129:139 - tmp - (DISK) oh noes!
[+] 192.168.174.129:139 - opt - (DISK)
[+] 192.168.174.129:139 - IPC$ - (IPC) IPC Service (metasploitable server (Samba 3.0.20-Debian))
[+] 192.168.174.129:139 - ADMIN$ - (IPC) IPC Service (metasploitable server (Samba 3.0.20-Debian))
[*] 192.168.174.129: - Scanned 1 of 1 hosts (100% complete)
[*] Auxiliary module execution completed
```

## 8. Findings

### Finding 1 – SMB Share Enumeration Completed

**Status:** Confirmed

The scanner completed enumeration against one target.

The results were returned through:

`192.168.174.129:139`

### Finding 2 – DISK Shares Identified

**Status:** Confirmed

The scanner identified three DISK shares:

| Share | Description |
|---|---|
| print$ | Printer Drivers |
| tmp | oh noes! |
| opt | No description displayed |

These share names and descriptions were returned directly by the target.

### Finding 3 – IPC Shares Identified

**Status:** Confirmed

The scanner identified two IPC shares:

| Share | Description |
|---|---|
| IPC$ | IPC Service (metasploitable server (Samba 3.0.20-Debian)) |
| ADMIN$ | IPC Service (metasploitable server (Samba 3.0.20-Debian)) |

Both returned descriptions contained the text:

`Samba 3.0.20-Debian`

This is recorded as information disclosed in the share descriptions. It is not treated as an independently verified software-version assessment.

### Finding 4 – Host Identification

**Status:** Confirmed scanner output

The module reported:

`Unix (Unknown)`

This is the operating-system identification returned by the scanner. It does not establish a specific operating-system distribution or release.

### Finding 5 – Share Access Permissions

**Status:** Not established

The module description indicates that it can determine which shares are readable or writable.

However, the supplied scanner output lists share names, types, and descriptions without explicitly reporting successful read or write access.

Therefore, this investigation does not claim that any share was successfully accessed or that files were readable or writable.

## 9. Evidence-Based Assessment

| Observation | Interpretation |
|---|---|
| Five shares returned | The target disclosed five SMB shares |
| Three DISK shares | print$, tmp, and opt were identified as disk shares |
| Two IPC shares | IPC$ and ADMIN$ were identified as IPC resources |
| Samba 3.0.20-Debian text | Disclosed in the IPC share descriptions |
| Unix (Unknown) | Host identification returned by the scanner |
| TCP port 139 | Port shown for the enumeration results |
| No explicit permission results | Read/write access was not established |

### Potential Security Considerations

- The exposed shares may warrant review against the intended network access policy.
- Disk shares should be assessed for appropriate access restrictions.
- Administrative and IPC resources should be reviewed in the context of the server's intended configuration.
- The returned Samba identification string may warrant further software-version and vulnerability assessment.

### What Was Not Established

The investigation did not establish:

- Successful reading of files from any share.
- Successful modification or deletion of files.
- Unauthorized access to administrative resources.
- The contents of the identified shares.
- A specific vulnerability affecting the target.
- Successful exploitation or compromise.

