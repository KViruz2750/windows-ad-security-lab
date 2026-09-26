# Troubleshooting Windows Time and Group Policy

## Summary

CLIENT01 initially failed to apply the updated computer-side Group Policy because its clock was significantly different from DC01. Windows Time synchronization was corrected, the client’s domain session was refreshed, and Group Policy then applied successfully.

## Symptoms

Running the following command on CLIENT01 produced a computer-policy failure:

```powershell
gpupdate /force
```

The first error stated that Windows could not determine whether new Group Policy settings should be enforced because the computer clock was not synchronized with a domain controller.

The machines displayed different dates:

- CLIENT01: Tuesday, September 22, 2026
- DC01: Wednesday, September 16, 2026

This amount of time skew prevented normal domain authentication and computer-policy processing.

## Root cause

DC01 and CLIENT01 had been resumed with significantly different system dates. Active Directory authentication relies on Kerberos, which requires domain-member clocks to remain closely synchronized with the domain controller.

The confirmed root cause was time skew between CLIENT01 and DC01.

## Resolution

### Correct DC01 time synchronization

DC01 was configured with the correct time zone and an external Windows Time source:

```powershell
Set-TimeZone -Id "Pacific Standard Time"

w32tm /config /manualpeerlist:"time.windows.com,0x8" /syncfromflags:manual /reliable:yes /update

Restart-Service w32time

w32tm /resync /force

Get-Date
w32tm /query /source
```

DC01’s date and time were corrected before attempting to synchronize the client.

### Resynchronize CLIENT01 with the domain

CLIENT01 was configured to obtain its time through the Active Directory domain hierarchy:

```powershell
Set-TimeZone -Id "Pacific Standard Time"

w32tm /config /syncfromflags:domhier /update

Restart-Service w32time

w32tm /resync /rediscover

Get-Date
w32tm /query /source
```

CLIENT01 then reported the following source:

```text
DC01.vermacorp.local
```

### Refresh the domain session

After time synchronization, another Group Policy attempt reported that Windows could not resolve the computer or user name. This was caused by stale domain-session and authentication information remaining from before the time correction.

CLIENT01 was restarted and the following checks were performed:

```powershell
whoami
w32tm /query /source
$env:LOGONSERVER
Test-ComputerSecureChannel -Verbose
```

The validation returned:

```text
vermacorp\karan.admin
DC01.vermacorp.local
\\DC01
True
```

The DNS resolver cache was then cleared and Group Policy was applied again:

```powershell
ipconfig /flushdns
gpupdate /force
```

Both Computer Policy and User Policy completed successfully.

## Validation

The resolution confirmed that:

- CLIENT01 authenticated with the domain account.
- CLIENT01 used DC01 as its Windows Time source.
- DC01 served as the active logon server.
- The computer-domain secure channel was healthy.
- Computer and user Group Policy settings applied successfully.
- The advanced process-auditing settings became active on CLIENT01.

## Lessons learned

- Correct time synchronization is essential for Kerberos authentication.
- DC01 should be corrected before synchronizing domain clients.
- Domain clients should use the Active Directory hierarchy for time synchronization.
- A restart may be necessary after correcting substantial time skew.
- Group Policy success must be verified on the endpoint rather than assumed from the server-side configuration.
- `Test-ComputerSecureChannel` is useful for separating time problems from broken domain trust.

## Evidence

### CLIENT01 time mismatch

![CLIENT01 time mismatch](../../screenshots/troubleshoots/c01%20time%20stuck%20on%20tues.png)

*CLIENT01 displayed an incorrect date before domain time synchronization was repaired.*

### DC01 time mismatch

![DC01 time mismatch](../../screenshots/troubleshoots/dc01%20time%20stuck%20on%20wed.png)

*DC01 displayed a different date, confirming significant time skew between the domain controller and client.*

### DC01 time correction

![DC01 time corrected](../../screenshots/troubleshoots/tiem%20change%20succesful%20on%20dc01.png)

*DC01’s time configuration was corrected before synchronizing the domain client.*

### CLIENT01 time synchronization

![CLIENT01 time synchronized](../../screenshots/troubleshoots/time%20succesfull%20on%20c01.png)

*CLIENT01 synchronized with `DC01.vermacorp.local` through the domain hierarchy.*

### Post-synchronization Group Policy error

![Post-synchronization Group Policy error](../../screenshots/troubleshoots/another%20error%20after%20time%20sync.png)

*The remaining name-resolution error indicated that CLIENT01 still held stale domain-session information.*

### Successful Group Policy application

![Successful Group Policy application](../../screenshots/phase-05/successful%20gp%20update.png)

*After restarting CLIENT01 and validating its secure channel, both Computer Policy and User Policy applied successfully.*

## Resolution status

**Resolved.** Windows Time synchronization, domain communication, and Group Policy processing are functioning correctly.
