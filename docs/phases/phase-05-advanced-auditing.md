# Phase 5 — Advanced Process Creation Auditing

## Status

**Complete — September 26, 2026**

## Objective

Configure advanced process-creation auditing on the domain-joined Windows 11 workstation and verify that Windows Security Event ID 4688 records newly created processes, their originating accounts, parent processes, elevation information, and complete command lines.

## Environment

| System | Role |
|---|---|
| DC01 | Active Directory domain controller, DNS server, and Group Policy administrator |
| CLIENT01 | Domain-joined Windows 11 workstation |
| Domain | `vermacorp.local` |
| Security GPO | `GPO-Lab-Workstation-Security` |
| Target OU | `Lab-Workstations` |

## Configuration

### Process-creation audit policy

The existing workstation security GPO was edited on DC01 using Group Policy Management.

The following policy was configured:

```text
Computer Configuration
→ Policies
→ Windows Settings
→ Security Settings
→ Advanced Audit Policy Configuration
→ Audit Policies
→ Detailed Tracking
→ Audit Process Creation
```

`Audit Process Creation` was configured to record successful process-creation activity.

### Command-line process logging

The following administrative-template policy was enabled:

```text
Computer Configuration
→ Policies
→ Administrative Templates
→ System
→ Audit Process Creation
→ Include command line in process creation events
```

This setting adds the complete process command line to Event ID 4688. Command-line information provides additional context for distinguishing ordinary process execution from suspicious or unauthorized activity.

## Policy application

CLIENT01 initially failed to apply the updated computer policy because its clock was not synchronized with DC01. After correcting Windows Time synchronization and restarting the client, the domain secure channel was validated and Group Policy applied successfully.

The complete resolution is documented separately in [Windows Time Synchronization and Group Policy Troubleshooting](../troubleshooting/windows-time-and-group-policy.md).

The policy was reapplied on CLIENT01 with:

```powershell
gpupdate /force
```

Both Computer Policy and User Policy completed successfully.

## Endpoint validation

The effective process-creation audit policy was verified with:

```powershell
auditpol /get /subcategory:"Process Creation"
```

The result showed:

```text
Process Creation    Success
```

Command-line logging was verified through the corresponding registry value:

```powershell
reg query "HKLM\Software\Microsoft\Windows\CurrentVersion\Policies\System\Audit" /v ProcessCreationIncludeCmdLine_Enabled
```

The value returned:

```text
ProcessCreationIncludeCmdLine_Enabled    REG_DWORD    0x1
```

These results confirmed that both settings were active on CLIENT01.

## Event generation

A safe PowerShell process was launched with a distinctive command-line marker:

```powershell
powershell.exe -NoProfile -Command "Get-Date | Out-File C:\Users\Public\VERMACORP_PHASE5_4688_TEST.txt"
```

The command created a harmless text file and generated a corresponding process-creation event.

The matching security event was retrieved with:

```powershell
Get-WinEvent -FilterHashtable @{LogName='Security'; Id=4688; StartTime=(Get-Date).AddMinutes(-5)} |
Where-Object { $_.Message -match 'VERMACORP_PHASE5_4688_TEST' } |
Select-Object -First 1 -ExpandProperty Message
```

## Event 4688 analysis

The captured event contained the following information:

- Event ID `4688`
- Creator account `VERMACORP\karan.admin`
- New process name `powershell.exe`
- Parent process information
- Process and parent-process identifiers
- Token elevation information
- The complete PowerShell command line
- The unique `VERMACORP_PHASE5_4688_TEST` marker

The full command line demonstrated that the administrative-template policy was operating correctly.

## Security significance

Event ID 4688 provides visibility into programs and commands executed on Windows systems. Including command-line information improves the ability to investigate:

- Suspicious PowerShell execution
- Unauthorized administrative tools
- Script and command interpreters
- Malware execution patterns
- Processes launched by unexpected parent applications
- Commands executed with elevated privileges

Process-creation auditing establishes an important endpoint-telemetry source for later SIEM ingestion and security investigations.

## Evidence

### Process-creation audit policy

![Audit Process Creation policy](../../screenshots/phase-05/audit%20process%20creation%20policy.png)

*The workstation security GPO is configured to audit successful process-creation activity.*

### Command-line process auditing

![Command-line process auditing](../../screenshots/phase-05/command%20line%20process%20auditing.png)

*The GPO is configured to include complete command lines in process-creation events.*

### Successful Group Policy update

![Successful Group Policy update](../../screenshots/phase-05/successful%20gp%20update%20.png)

*After restarting CLIENT01 and validating its secure channel, both Computer Policy and User Policy applied successfully.*

### Effective auditing settings

![Effective process-auditing settings](../../screenshots/phase-05/verifying%20auditing%20settings.png)

*CLIENT01 reports successful Process Creation auditing and a command-line logging registry value of `0x1`.*

### Event 4688 Security log

![Security log filtered for Event 4688](../../screenshots/phase-05/event%204688%20security%20log.png)

*Event Viewer shows CLIENT01’s Security log filtered for successful Event ID 4688 process-creation records.*

### Event 4688 creator and process details

![Event 4688 creator details](../../screenshots/phase-05/testing%204688%20part1.png)

*The test event identifies `karan.admin` as the creator and PowerShell as the newly created process.*

### Event 4688 command-line details

![Event 4688 command-line validation](../../screenshots/phase-05/testing%204688%20part2.png)

*The event contains the complete PowerShell command line and the distinctive Phase 5 test marker.*

## Outcome

Advanced process-creation auditing is active on CLIENT01. The workstation records successful Event ID 4688 events with account, process, parent-process, elevation, and command-line information. This provides detailed endpoint telemetry that can support future threat detection and SIEM investigations.
