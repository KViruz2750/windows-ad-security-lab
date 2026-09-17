# Phase 4: Group Policy and Authentication Auditing

## Objective

Apply centralized security settings to the domain-joined workstation and verify that the policies are processed successfully.

## Group Policy Object

The following GPO was created:

```text
GPO-Lab-Workstation-Security
```

It was linked to:

```text
vermacorp.local/Lab-Workstations
```

This ensures that the policies apply to computers placed in the `Lab-Workstations` organizational unit.

## Audit Policies

The GPO was configured with:

```text
Audit Logon: Success and Failure
Audit User Account Management: Success and Failure
```

These settings allow the workstation to record authentication activity and local account-management events.

## Policy Validation

The client initially failed to process the GPO because of a time-synchronization problem. After correcting the time source, the policy was successfully applied with:

```powershell
gpupdate /force
```

The resulting policy was verified with:

```powershell
gpresult /r
```

The report confirmed:

```text
GPO-Lab-Workstation-Security
```

was applied to:

```text
CN=CLIENT01,OU=Lab-Workstations,DC=vermacorp,DC=local
```

## Security Events

The policy generated authentication events in the Security log, including:

* Event `4624`: Successful logon
* Event `4625`: Failed logon

## Outcome

Centralized auditing was successfully deployed to `CLIENT01`, providing the evidence needed for future SOC-style investigations.

## Future Improvements

* Enable process-creation auditing
* Audit account creation on the domain controller
* Audit privileged-group changes
* Configure password and account-lockout policies

## Evidence

### GPO link and scope

![GPO link and scope](../../screenshots/phase%204/gpo.png)

*Group Policy Management shows `GPO-Lab-Workstation-Security` linked to the `Lab-Workstations` organizational unit. The link is enabled and applies to Authenticated Users.*

### Audit Logon configuration

![Audit Logon policy](../../screenshots/phase%2004/audit%20logon%281%29.png)

*The Group Policy audit configuration enables Audit Logon for both Success and Failure events.*

### Audit User Account Management configuration

![Audit User Account Management policy](../../screenshots/phase%2004/audit%20user%20account%20management%281%29.png)

*The Group Policy audit configuration enables Audit User Account Management for both Success and Failure events.*

### Successful policy update

![Successful policy update](../../screenshots/phase%2004/successful%20policy%20update.png)

*The `gpupdate /force` command completed successfully for both computer and user policy.*

### Applied policy verification

![Applied policy verification](../../screenshots/phase%2004/applied%20policy%20verification.png)

*The `gpresult /r` output confirms that `GPO-Lab-Workstation-Security` and `Default Domain Policy` were applied to CLIENT01.*

### Security event log review

![Security event log list](../../screenshots/phase%2004/security%20log%20event%20list.png)

*The Security log is filtered for Event IDs 4624 and 4625, displaying both successful and failed logon events.*

### Failed logon investigation

![Failed logon Event 4625](../../screenshots/phase%2004/failed%20logon%20investigation.png)

*Event 4625 records a failed logon attempt for `VERMACORP\karan.admin` on CLIENT01. The failure reason is an unknown username or bad password.*

### Successful logon validation

![Successful logon Event 4624](../../screenshots/phase%2004/successful%20logon.png)

*Event 4624 records a successful logon for `VERMACORP\karan.admin` on CLIENT01.*

## Evidence Notes

These screenshots document the Phase 4 workflow: GPO scope, audit policy configuration, policy refresh, applied-policy verification, Security log filtering, and investigation of successful and failed logon events.

The current evidence proves that User Account Management auditing is configured. A future Event ID 4720 or 4726 screenshot could be added if account creation or deletion auditing is tested.
