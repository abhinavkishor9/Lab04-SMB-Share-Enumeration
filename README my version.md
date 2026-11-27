# Lab04-SMB-Share-Enumeration
## Overview
### 1. Introduction to SMB

Server Message Block (SMB) is a network protocol that allows computers to share resources over a network.

These resources may include:

- Files and directories.
- Network storage.
- Printers.
- Inter-process communication resources.

SMB is commonly used in Windows environments. Linux systems can also provide SMB services through implementations such as Samba.

SMB servers make resources available through named shares. Other systems can discover these shares and request access according to the server's authentication and authorization configuration.

### 2. What Is an SMB Share?

An SMB share is a named resource exposed by an SMB server.

A server may provide multiple shares, each serving a different purpose.

Common share types include:

| Share Type | Description |
|---|---|
| DISK | A shared directory or storage resource |
| IPC | An inter-process communication resource |
| PRINTER | A shared printer resource |

Examples of commonly encountered share names include:

- `print$` – commonly associated with printer-driver resources.
- `IPC$` – a special share used for inter-process communication.
- `ADMIN$` – commonly associated with administrative access to a Windows system.
- `Public` – a possible general-purpose shared directory.

These names are examples. Their actual purpose and permissions must be determined from the target's configuration and enumeration results.

### 3. What Is SMB Share Enumeration?

SMB share enumeration is the process of identifying the shared resources exposed by an SMB server.

An enumeration tool communicates with the SMB service and requests information about available shares.

Depending on the server response and the capabilities of the tool, the collected information may include:

- Share names.
- Share types.
- Share descriptions.
- Access-related information.
- Directory and file details when additional inspection is performed.

Enumeration helps a security analyst understand which resources are disclosed through the network service.

### 4. Metasploit SMB Share Enumeration

Metasploit provides an auxiliary scanner for SMB share identification:

`auxiliary/scanner/smb/smb_enumshares`

The module attempts to determine which shares are provided by the SMB service.

According to its module description, it can also determine which shares are readable or writable and collect additional information such as directories, files, and timestamps.

Its default share enumeration mechanism uses a RubySMB `net_share_enum_all` request through the Server Service Remote Protocol (SRVSVC).

The module can perform additional recursive inspection when its spidering options are enabled.

For this lab, recursive inspection is disabled so that the assessment remains focused on share identification.

### 5. Understanding Important Module Options

| Option | Purpose |
|---|---|
| RHOSTS | Specifies the target host or hosts |
| Share | Restricts enumeration to a specified share |
| SpiderShares | Enables recursive inspection of shares |
| ShowFiles | Controls detailed file information during spidering |
| SpiderProfiles | Enables user-profile spidering in supported scenarios |
| MaxDepth | Controls the maximum directory depth during spidering |
| SMBUser | Specifies an SMB username |
| SMBPass | Specifies an SMB password |
| SMBDomain | Specifies the SMB authentication domain |
| THREADS | Controls scanning concurrency |

The actual settings should always be checked using `show options` before executing the module.

### 6. Understanding SMB Share Access

Identifying a share and accessing its contents are different activities.

A server may disclose a share name without allowing an unauthenticated user to browse its contents.

Share access generally depends on:

- Authentication requirements.
- Share-level permissions.
- Underlying filesystem permissions.
- Server configuration.
- Network access restrictions.

The possible access levels include:

#### Read access

Allows an authorized user to view or retrieve permitted resource contents.

#### Write access

Allows an authorized user to create or modify permitted resources.

#### No access

The user or client cannot access the resource under the attempted authentication and permission context.

An enumeration result should not be interpreted as proof of read or write access unless the tool explicitly reports that result or separate validation establishes it.

### 7. Why SMB Share Enumeration Matters

SMB share enumeration is useful during network reconnaissance and security assessments because exposed shares may disclose information about a system's resources.

Security analysts use the findings to investigate:

- Unexpectedly exposed shared directories.
- Administrative or special-purpose resources.
- Potentially excessive access permissions.
- Shares that may contain sensitive information.
- Resources that should be restricted to specific users or systems.

The presence of a share alone does not demonstrate a security vulnerability. Its permissions, contents, intended purpose, and exposure must be considered.

### 8. Application to This Lab

During Lab 03, the Metasploit SMB version detection module identified SMB version 1 on the Metasploitable 2 target.

It also returned Unix as the host operating-system identification and reported that SMB signing was not required.

Lab 04 extends the investigation by identifying the resources exposed through the SMB service.

The following module was used:

`auxiliary/scanner/smb/smb_enumshares`

The scanner identified five shares:

| Share | Type |
|---|---|
| print$ | DISK |
| tmp | DISK |
| opt | DISK |
| IPC$ | IPC |
| ADMIN$ | IPC |

The descriptions of the IPC shares included the text `Samba 3.0.20-Debian`.

The scanner also returned `Unix (Unknown)` as its host identification.

Recursive share inspection, detailed file display, and user-profile spidering were disabled during the exercise.

The supplied output did not explicitly establish read or write access to the identified shares.

### 9. Conclusion

SMB share enumeration provides visibility into the shared resources disclosed by an SMB server.

Metasploit's `smb_enumshares` auxiliary scanner can identify share names, types, and descriptions and provides additional capabilities for assessing share access and inspecting resources.

The important analytical principle is to distinguish between share discovery, access permissions, and actual access to resource contents.

Each finding must be documented according to the evidence returned by the scanner and the activities actually performed.


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

## Lab Objectives

### Primary Objective

To identify shared resources exposed by an SMB server and understand how share enumeration contributes to network reconnaissance and security assessment.

### Technical Objectives

1. Understand the purpose of SMB shares in network communication.
2. Differentiate between DISK, IPC, and PRINTER share types.
3. Understand how named shares expose resources through an SMB server.
4. Navigate the Metasploit SMB auxiliary scanner module.
5. Examine the configuration options available in `smb_enumshares`.
6. Configure a target system for controlled share enumeration.
7. Understand the role of recursive share inspection and file-display settings.
8. Execute an SMB share enumeration scan.
9. Identify share names, types, and descriptions returned by the server.
10. Recognize information disclosed through SMB share descriptions.

### Analytical Objectives

- Distinguish share discovery from successful resource access.
- Understand the difference between read permissions and write permissions.
- Recognize the limitations of unauthenticated enumeration.
- Interpret share information without assuming its security impact.
- Identify resources that may require access-control review.
- Preserve exact scanner output as investigation evidence.
- Separate confirmed findings from potential security concerns and unestablished claims.

### Investigation Outcome

Complete a controlled SMB share enumeration exercise against Metasploitable 2.

Produce an evidence-based record of the shared resources identified, their reported types, and their descriptions.

The assessment must remain limited to share identification. Recursive file browsing, file downloading, file modification, credential attacks, and exploitation are not included.

## Lab Scenario

### Background

During an earlier network reconnaissance exercise, a Metasploitable 2 virtual machine was found to expose SMB-related services over the network.

A subsequent SMB service enumeration exercise identified SMB version 1, returned Unix as the host operating-system identification, and reported that SMB signing was not required.

Although these findings provided information about the SMB service, they did not identify the shared resources made available by the target.

### Investigation Scenario

As part of a controlled security assessment, a security analyst is tasked with identifying the resources exposed through the target's SMB service.

The analyst uses Ubuntu WSL to communicate with the Metasploitable 2 virtual machine hosted in VMware.

After verifying network connectivity, the analyst launches Metasploit and selects its SMB share enumeration auxiliary module.

The investigation focuses on identifying the names and types of exposed shares and collecting descriptions returned by the server.

Particular attention is given to understanding the difference between a share being discovered and the ability to access its contents.

### Investigation Tasks

The analyst will:

- Identify the SMB shares disclosed by the target.
- Record their reported share types.
- Examine available share descriptions.
- Review information returned by the scanner about the target.
- Identify any observations that may warrant further security review.
- Document limitations where access permissions have not been established.

Recursive share inspection and detailed file enumeration will remain disabled.

### Assessment Boundaries

The exercise is restricted to SMB share identification and basic enumeration.

No password guessing, credential attacks, recursive directory browsing, file downloading, file modification, or exploitation will be performed.

All conclusions must be supported by the actual scanner output. The discovery of an SMB share must not be treated as proof that its contents are accessible.

### Expected Outcome

The analyst is expected to produce an investigation record containing the identified SMB shares, their reported types, and any descriptions disclosed by the target.

The final assessment should distinguish between confirmed share information, potential access-control concerns, and permissions or security impacts that remain unverified.

This exercise provides practical experience in SMB resource discovery and evidence-based network security assessment.

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

