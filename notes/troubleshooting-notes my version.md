# Troubleshooting Notes
## 1. Overview

This document records the environment checks, startup warnings, module configuration, and operational observations encountered while performing SMB share enumeration against Metasploitable 2.

The lab was performed using Ubuntu WSL, VMware Workstation, Metasploitable 2, and Metasploit Framework.

## 2. Target Network Verification

### Observation

The Metasploitable 2 network configuration was checked using:

`ifconfig`

The `eth0` interface reported:

- IPv4 address: `192.168.174.129`
- Broadcast address: `192.168.174.255`
- Subnet mask: `255.255.255.0`
- Interface state: UP

### Validation

From Ubuntu WSL:

`ping -c 4 192.168.174.129`

### Result

- Four packets transmitted.
- Four packets received.
- Zero percent packet loss.
- Average response time: 1.250 ms.

### Conclusion

Network connectivity was working. No network troubleshooting or VMware adapter changes were required during this exercise.

## 3. Metasploit Startup Warnings

### Observation

When `msfconsole` was launched, Ruby displayed warnings about unresolved or ambiguous gem specifications.

The affected gems were:

- `base64`
- `logger`

Multiple installed versions were listed.

### Result

Despite the warnings:

- Metasploit started successfully.
- The console displayed version `6.5.12-dev-`.
- The existing database was found and startup was attempted.
- PostgreSQL connectivity was confirmed.
- The SMB share enumeration module loaded.
- The scanner completed successfully.

### Conclusion

The Ruby warnings were non-blocking during this lab.

No gem cleanup, package modification, or Ruby environment changes were required.

**Troubleshooting lesson:** Do not modify installed gems merely because a warning appears. First determine whether it causes an actual operational problem.

## 4. Database Connectivity

### Observation

During startup, Metasploit displayed:

`Existing database found, attempting to start it`

### Validation

The database connection was checked using:

`db_status`

### Result

`Connected to msf. Connection type: postgresql.`

### Conclusion

The PostgreSQL database was available and connected to Metasploit.

No database reinitialization or repair was required.

## 5. SMB Share Enumeration Module

### Observation

The following module was selected:

`auxiliary/scanner/smb/smb_enumshares`

The module information was reviewed using:

`info`

The description indicated that the module identifies SMB shares and can collect additional information about share types, directories, files, and timestamps.

### Conclusion

The module was appropriate for the intended share enumeration exercise.

## 6. Module Configuration

### Observation

The initial options showed:

- `RHOSTS` was empty.
- `SpiderShares` was false.
- `ShowFiles` was false.
- `SpiderProfiles` was true.
- `SMBUser` and `SMBPass` were empty.
- `THREADS` was set to 1.

### Actions

The target was configured:

`set RHOSTS 192.168.174.129`

Recursive share inspection was disabled:

`set SpiderShares false`

Detailed file display was disabled:

`set ShowFiles false`

User-profile spidering was disabled:

`set SpiderProfiles false`

### Result

A subsequent `show options` confirmed that:

- RHOSTS contained `192.168.174.129`.
- SpiderShares was false.
- ShowFiles was false.
- SpiderProfiles was false.
- No SMB username or password was configured.

### Conclusion

The configuration matched the intended scope of share identification without recursive directory or file inspection.

## 7. Scanner Execution

### Observation

The module was executed using:

`run`

### Result

The scanner:

- Completed against one target.
- Returned results through TCP port 139.
- Identified five SMB shares.
- Reported DISK and IPC share types.
- Returned share descriptions.
- Returned `Unix (Unknown)` as host identification.

### Conclusion

The scanner executed successfully. No module execution failure was reported.

## 8. Returned Share Information

The scanner identified:

| Share | Type | Description |
|---|---|---|
| print$ | DISK | Printer Drivers |
| tmp | DISK | oh noes! |
| opt | DISK | No description displayed |
| IPC$ | IPC | IPC Service (metasploitable server (Samba 3.0.20-Debian)) |
| ADMIN$ | IPC | IPC Service (metasploitable server (Samba 3.0.20-Debian)) |

### Interpretation

The output confirmed that the server disclosed the five share names and their reported types.

The Samba 3.0.20-Debian text appeared in the IPC share descriptions. It should be recorded as returned descriptive information rather than treated as an independently verified software-version result.

The scanner output supplied for this lab did not explicitly confirm read or write permissions.

## 9. Issues That Did Not Occur

The following problems were not encountered:

- Target connectivity failure.
- Metasploit console startup failure.
- PostgreSQL connection failure.
- Module loading failure.
- Missing target configuration after setup.
- Scanner execution failure.

