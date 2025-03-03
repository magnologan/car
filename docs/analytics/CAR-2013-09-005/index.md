---
title: "CAR-2013-09-005: Service Outlier Executables"
layout: analytic
submission_date: 2013/09/23
information_domain: Host
subtypes: Process
analytic_type: Detection
contributors: MITRE
---

New executables that are started as a service are suspicious. This analytic looks for anomalous service executables.

## ATT&CK Detection

|Technique |Tactic |Level of Coverage |
|---|---|---|
|[Modify Existing Service](https://attack.mitre.org/techniques/T1031/)|[Persistence](https://attack.mitre.org/tactics/TA0003/)|Moderate|
|[New Service](https://attack.mitre.org/techniques/T1050/)|[Persistence](https://attack.mitre.org/tactics/TA0003/), [Privilege Escalation](https://attack.mitre.org/tactics/TA0004/)|Moderate|

## Data Model References

|Object|Action|Field|
|---|---|---|
|[process](/data_model/process) | [create](/data_model/process#create) | [parent_image_path](/data_model/process#parent_image_path) |


## Implementations

### Pseudocode

Create a baseline of services seen over the last 30 days and a list of services seen today. Remove services in the baseline from services seen today, leaving a list of new services.


```
processes = search Process:Create
services = filter processes where (parent_image_path == "C:\Windows\System32\services.exe")
historic_services = filter services (where timestamp < now - 1 day AND timestamp > now - 1 day)
current_services = filter services (where timestamp >= now - 1 day)
new_services = historic_services - current_services
output new_services
```


