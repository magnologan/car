---
title: "CAR-2013-05-003: SMB Write Request"
layout: analytic
submission_date: 2013/05/13
information_domain: Host, Network
subtypes: Network, Netflow, PCAP
analytic_type: Situational Awareness, TTP
contributors: MITRE
---

As described in [CAR-2013-01-003](CAR-2013-01-003), SMB provides a means of remotely managing a file system. Adversaries often use SMB to move laterally to a host. SMB is commonly used to upload files. It may be used for staging in [Exfiltration](https://attack.mitre.org/tactics/TA0010) or as a [Lateral Movement](https://attack.mitre.org/tactics/TA0008) technique. Unlike SMB Reads, SMB Write requests typically require an additional level of access, resulting in less activity. Focusing on SMB Write activity narrows the field to find techniques that actively change remote hosts, instead of passively reading files.

## ATT&CK Detection

|Technique |Tactic |Level of Coverage |
|---|---|---|
|[Remote File Copy](https://attack.mitre.org/techniques/T1105/)|[Lateral Movement](https://attack.mitre.org/tactics/TA0008/)|High|
|[Windows Admin Shares](https://attack.mitre.org/techniques/T1077/)|[Lateral Movement](https://attack.mitre.org/tactics/TA0008/)|Moderate|
|[Valid Accounts](https://attack.mitre.org/techniques/T1078/)|[Defense Evasion](https://attack.mitre.org/tactics/TA0005/)|Moderate|

## Data Model References

|Object|Action|Field|
|---|---|---|
|[flow](/data_model/flow) | [message](/data_model/flow#message) | [proto_info](/data_model/flow#proto_info) |
|[flow](/data_model/flow) | [message](/data_model/flow#message) | [dest_port](/data_model/flow#dest_port) |


## Implementations

### Pseudocode


```
flow = search Flow:Message
smb_write = filter flow where (dest_port == "445" and protocol == "smb.write")
smb_write.file_name = smb_write.proto_info.file_name
output smb_write
```


