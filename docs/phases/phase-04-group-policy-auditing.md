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

