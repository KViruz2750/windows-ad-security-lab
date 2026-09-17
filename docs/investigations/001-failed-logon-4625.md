# Investigation 001: Failed Interactive Logon

## Summary

A controlled failed authentication attempt was generated and investigated on `CLIENT01` to validate the authentication-auditing configuration.

## Event Details

| Field                  | Finding                           |
| ---------------------- | --------------------------------- |
| Event ID               | `4625`                            |
| Host                   | `CLIENT01.vermacorp.local`        |
| Account                | `VERMACORP\karan.admin`           |
| Time                   | September 16, 2026, 4:55:50 PM    |
| Logon type             | `2` — Interactive                 |
| Failure reason         | Unknown username or bad password  |
| Status                 | `0xC000006D`                      |
| Substatus              | `0xC000006A` — Incorrect password |
| Source address         | `127.0.0.1`                       |
| Authentication package | Negotiate                         |

## Related Event

A successful `4624` event was recorded shortly afterward for the same account. The event showed Logon Type `11`, which represents cached interactive authentication.

## Analysis

The failed authentication occurred locally on `CLIENT01`. The source address `127.0.0.1` indicates that the attempt was made directly on the workstation rather than remotely.

The failure was caused by an incorrect password during authorized testing.

## Assessment

* Severity: Low
* Classification: Expected lab activity
* Evidence of remote attack: None
* Evidence of account compromise: None

## Recommended Response

No containment action was required. In a production environment, the analyst should verify whether the user initiated the failed attempt and monitor for repeated failures from the same account or workstation.

## Skills Demonstrated

* Windows Event Viewer
* Event ID analysis
* Authentication triage
* Logon-type interpretation
* Timeline correlation


## Evidence

### Applied audit policy

![Applied policy verification](../../screenshots/phase-04/applied%20policy%20verification.png)

*The applied-policy results confirm that the auditing GPO was successfully applied to CLIENT01.*

### Filtered Security log

![Security event log list](../../screenshots/phase-04/security%20log%20event%20list.png)

*The Security log is filtered for Event IDs 4624 and 4625, providing the event context for the investigation.*

### Failed logon event

![Failed logon Event 4625](../../screenshots/phase-04/failed%20logon%20investigation.png)

*Event 4625 records a failed logon attempt for `VERMACORP\karan.admin` on CLIENT01. The failure reason indicates an unknown username or bad password.*

### Successful logon comparison

![Successful logon Event 4624](../../screenshots/phase-04/successful%20logon.png)

*Event 4624 provides a comparison showing a successful logon for `VERMACORP\karan.admin` on CLIENT01.*

## Evidence Notes

The evidence confirms that the auditing policy was applied, the relevant Security events were located, and the failed logon was examined in detail alongside a successful logon event.

* Security-event documentation

