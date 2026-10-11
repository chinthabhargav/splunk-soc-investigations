# 05 --- Windows Security Logs

Windows records events about logons, process creation, account changes,
services, and other activity. These logs are valuable for SOC
investigations when auditing is enabled and events are collected.

## Common fields

 | Field | Meaning |
 |:------|:------------
 |`EventCode` / `EventID` |Event identifier; exact field depends on ingestion
 | `ComputerName` / `host` |Machine associated with the event
 | `Account_Name` / `TargetUserName`|Account field; meaning varies by event
 | `Account_Domain`| Account domain, if present
 | `Logon_Type`    | Type of logon
 |`Source_Network_Address` / `IpAddress`|Source address in relevant events     
 | `Workstation_Name` / `WorkstationName`|Workstation name, if present
 | `Process_Name` / `NewProcessName`  | Process image path/name in relevant events
 | `Process_ID` / `NewProcessId`|Process identifier
 | `CommandLine` / `ProcessCommandLine` | Command line, if process command-line auditing is available|
 | `Parent_Process`|Parent process, often available through EDR/Sysmon or normalized data|
  -----------------------------------------------------------------------

## Useful event IDs

  Event ID   | Typical meaning
  :--------- |:--------------------------------------------------------
  4624       |Successful logon
  4625       |Failed logon
  4688       |Process creation
  4720       |User account created
  4728       |Member added to a security-enabled global group
  4732       |Member added to a security-enabled local group
  7045       |A service was installed (commonly from the System log)

![Screenshot](/screenshots/03_spl.png)

Event details and field names vary. Event ID alone does not establish
malicious intent.

## Example: failed logons

``` spl
index=Windowslogs.csv event_id="4625" 
| table timestamp user source_ip dest_ip 
| sort - timestamp 
```
![Screenshot](/screenshots/01_spl.png)
Replace the index and field names with those available in your
environment. Some data sets use `EventID` rather than `EventCode`.

## Example: process creation

``` spl
index=windows index=main event_id="4688" 
| table timestamp user event_name source_ip process_name command_line 
| sort - timestamp
```
![Screenshot](/screenshots/02_spl.png)

Command-line fields may be absent if the relevant auditing is not
enabled or the ingestion pipeline does not extract them.

## What to investigate

-   Unexpected account creation.
-   Changes to privileged groups.
-   Repeated failed logons.
-   Unusual process execution or command lines.
-   Unexpected services.
-   A suspicious process launched by an unusual parent process.

## Process-chain thinking
>
>A chain such as `WINWORD.EXE → powershell.exe → unknown.exe` can deserve
>investigation, but the chain must be verified with actual endpoint
>telemetry. Do not assume that every PowerShell process is malicious.
>

## Evidence checklist

-   Timestamp and timezone.
-   Hostname.
-   Account and logon context.
-   Event ID and message.
-   Process path and command line, if available.
-   Related network or authentication events.
-   Whether the activity is expected.
