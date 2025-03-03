---
title: "CAR-2013-10-002: DLL Injection via Load Library"
layout: analytic
submission_date: 2013/10/07
information_domain: Host
subtypes: Process DLL
analytic_type: TTP
contributors: MITRE
---

Microsoft Windows allows for processes to remotely create threads within other processes of the same privilege level. This functionality is provided via the Windows API [CreateRemoteThread](https://msdn.microsoft.com/en-us/library/windows/desktop/ms682437.aspx). Both Windows and third-party software use this ability for legitimate purposes. For example, the Windows process [csrss.exe](https://en.wikipedia.org/wiki/Client/Server_Runtime_Subsystem) creates threads in programs to send signals to registered callback routines. Both adversaries and host-based security software use this functionality to [inject DLLs](https://attack.mitre.org/techniques/T1055), but for very different purposes. An adversary is likely to inject into a program to [evade defenses](https://attack.mitre.org/tactics/TA0005) or [bypass User Account Control](https://attack.mitre.org/techniques/T1088), but a security program might do this to gain increased monitoring of API calls. One of the most common methods of [DLL Injection](https://attack.mitre.org/techniques/T1055) is through the Windows API [LoadLibrary](https://msdn.microsoft.com/en-us/library/windows/desktop/ms684175.aspx).

-   Allocate memory in the target program with [VirtualAllocEx](https://msdn.microsoft.com/en-us/library/windows/desktop/aa366890.aspx)
-   Write the name of the DLL to inject into this program with [WriteProcessMemory](https://msdn.microsoft.com/en-us/library/windows/desktop/ms681674.aspx)
-   Create a new thread and set its entry point to [LoadLibrary](https://msdn.microsoft.com/en-us/library/windows/desktop/ms684175.aspx) using the API [CreateRemoteThread](https://msdn.microsoft.com/en-us/library/windows/desktop/ms682437.aspx).

This behavior can be detected by looking for thread creations across processes, and resolving the entry point to determine the function name. If the function is `LoadLibraryA` or `LoadLibraryW`, then the intent of the remote thread is clearly to inject a DLL. When this is the case, the source process must be examined so that it can be ignored when it is both expected and a trusted process.

## ATT&CK Detection

|Technique |Tactic |Level of Coverage |
|---|---|---|
|[Process Injection](https://attack.mitre.org/techniques/T1055/)|[Defense Evasion](https://attack.mitre.org/tactics/TA0005/)|Moderate|
|[Bypass User Account Control](https://attack.mitre.org/techniques/T1088/)|[Privilege Escalation](https://attack.mitre.org/tactics/TA0004/)|Moderate|

## Data Model References

|Object|Action|Field|
|---|---|---|
|[thread](/data_model/thread) | [remote_create](/data_model/thread#remote_create) | [src_pid](/data_model/thread#src_pid) |
|[thread](/data_model/thread) | [remote_create](/data_model/thread#remote_create) | [start_function](/data_model/thread#start_function) |


## Implementations

### Pseudocode

Search for remote thread creations that start at LoadLibraryA or LoadLibraryW. Depending on the tool, it may provide additional information about the DLL string that is an argument to the function. If there is any security software that legitimately injects DLLs, it must be carefully whitelisted. 


```
remote_thread = search Thread:RemoteCreate
remote_thread = filter (start_function == "LoadLibraryA" or start_function == "LoadLibraryW")
remote_thread = filter (src_image_path != "C:\Path\To\TrustedProgram.exe")

output remote_thread
```


