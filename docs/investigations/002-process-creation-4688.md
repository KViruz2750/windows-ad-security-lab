# Investigation 002 — PowerShell Process Creation

## Investigation status

**Closed — Authorized activity**

## Summary

A Windows Security Event ID 4688 was generated on CLIENT01 after an administrator launched a controlled PowerShell command. The event was investigated to determine which account created the process, what executable was launched, how it was started, whether it was elevated, and what command it executed.

The activity was confirmed as an authorized test used to validate the advanced process-auditing controls configured during [Phase 5 — Advanced Process Creation Auditing](../phases/phase-05-advanced-process-auditing.md).

## Investigation details

| Field | Value |
|---|---|
| Date | September 26, 2026 |
| Host | `CLIENT01.vermacorp.local` |
| Log | Windows Security |
| Event ID | `4688` |
| Event description | A new process has been created |
| Account | `VERMACORP\karan.admin` |
| New process | `powershell.exe` |
| Parent process | `powershell.exe` |
| Elevation | Full administrative token |
| Disposition | Benign, authorized test |

## Investigation trigger

A controlled process was generated with the following command:

```powershell
powershell.exe -NoProfile -Command "Get-Date | Out-File C:\Users\Public\VERMACORP_PHASE5_4688_TEST.txt"
```

The command launched a new PowerShell process and wrote the current date to a harmless text file in `C:\Users\Public`.

The distinctive `VERMACORP_PHASE5_4688_TEST` marker was included so that the corresponding security event could be separated from normal operating-system process activity.

## Event retrieval

The matching event was retrieved from CLIENT01’s Security log with:

```powershell
Get-WinEvent -FilterHashtable @{LogName='Security'; Id=4688; StartTime=(Get-Date).AddMinutes(-5)} |
Where-Object { $_.Message -match 'VERMACORP_PHASE5_4688_TEST' } |
Select-Object -First 1 -ExpandProperty Message
```

This query limited the results to recent Event 4688 records and selected the event containing the unique test marker.

## Observed event data

The event contained the following process information:

| Event field | Observed value |
|---|---|
| Creator account | `karan.admin` |
| Creator domain | `VERMACORP` |
| New process ID | `0x894` |
| New process name | `C:\Windows\System32\WindowsPowerShell\v1.0\powershell.exe` |
| Creator process ID | `0x1a24` |
| Creator process name | `C:\Windows\System32\WindowsPowerShell\v1.0\powershell.exe` |
| Token elevation type | `TokenElevationTypeFull (2)` |
| Mandatory label | `S-1-16-12288` |
| Command-line marker | `VERMACORP_PHASE5_4688_TEST` |

The complete command line was recorded because command-line process auditing had been enabled through Group Policy.

## Analysis

### Account context

The process was created by `VERMACORP\karan.admin`, the authorized domain-administrator account used for the lab configuration.

The account information matched the user who intentionally executed the test command.

### Parent-child relationship

The new `powershell.exe` process was launched by another `powershell.exe` process. This relationship was expected because the test command explicitly started a child PowerShell instance from an existing PowerShell session.

In a production investigation, PowerShell launched by an unusual parent process—such as an Office application, web browser, or script host—would require additional examination.

### Command-line behavior

The recorded command line used:

- `powershell.exe`
- The `-NoProfile` option
- The `Get-Date` cmdlet
- `Out-File`
- A text file under `C:\Users\Public`

The `-NoProfile` option can appear in both legitimate administration and malicious execution. In this case, the remainder of the command showed that it only retrieved the current date and wrote it to a controlled test file.

No encoded command, remote download, credential access, persistence mechanism, or destructive action was observed.

### Elevation

`TokenElevationTypeFull (2)` and mandatory label `S-1-16-12288` indicate that the process ran with an elevated administrative token and high-integrity context.

This matched the administrator PowerShell session used to generate the event.

### MITRE ATT&CK relevance

PowerShell execution is associated with MITRE ATT&CK technique **T1059.001 — PowerShell**.

This mapping identifies the type of behavior that defenders may monitor; it does not by itself mean that the activity was malicious. The surrounding account, parent-process, command-line, and testing context confirmed that this event was authorized.

## Findings

- Event ID 4688 process auditing operated correctly.
- The complete command line was recorded.
- The creator account matched the authorized lab administrator.
- The parent-child process relationship matched the test procedure.
- The process executed with an elevated administrative token.
- The command performed only the expected file-writing test.
- No indicators of malicious activity were identified.

## Disposition

**Benign — authorized security-control validation.**

The event was created intentionally to verify that CLIENT01 records detailed process-creation telemetry. No containment, account reset, or remediation was required.

## Recommended monitoring

Future SIEM detections should examine Event ID 4688 records for:

- Encoded or heavily obfuscated PowerShell commands
- PowerShell launched by unusual parent processes
- Commands downloading content from external locations
- Credential-access or security-tool-disabling commands
- Execution from temporary or user-writable directories
- Unexpected elevated processes
- Administrative activity outside approved time periods
- Repeated command execution across multiple systems

## Evidence

### Security log filtered for Event 4688

![Event 4688 Security log](../../screenshots/phase-05/event%204688%20security%20log.png)

*CLIENT01’s Security log contains successful Event ID 4688 process-creation records.*

### Creator account and process details

![Event 4688 creator and process details](../../screenshots/phase-05/testing%204688%20part1.png)

*The controlled event identifies `karan.admin` as the creator and PowerShell as the newly created process.*

### Complete process command line

![Event 4688 command-line details](../../screenshots/phase-05/testing%204688%20part2.png)

*The event records the complete PowerShell command line, including the distinctive Phase 5 test marker.*

## Conclusion

The investigation confirmed that the observed PowerShell process was authorized and benign. More importantly, the event demonstrated that CLIENT01 now provides the process, account, parent-process, elevation, and command-line telemetry required for host-based investigation and future SIEM analysis.
