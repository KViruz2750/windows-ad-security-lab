# Phase 2: Active Directory and DNS

## Objective

Convert `DC01` into a domain controller and establish the core identity and name-resolution services for the lab.

## Active Directory Deployment

Active Directory Domain Services was installed through Server Manager. The server was then promoted by creating a new forest:

```text
Domain: vermacorp.local
NetBIOS name: VERMACORP
Domain controller: DC01
```

The DNS delegation option was left unchecked because this was a new forest without an existing authoritative parent DNS zone.

All prerequisite checks passed before installation, and the server restarted automatically after promotion.

## Core Services

The following services were verified as running:

* Active Directory Domain Services
* DNS Server
* Netlogon
* DFS Replication

The domain controller also provided the following shares:

```text
SYSVOL
NETLOGON
```

These shares are required for Group Policy and domain logon processing.

They were verified with:

```powershell
net share
Test-Path C:\Windows\SYSVOL\sysvol\vermacorp.local
```

## DNS Validation

The domain controller was tested with:

```powershell
nslookup dc01.vermacorp.local 192.168.50.10
nltest /dsgetdc:vermacorp.local
```

The domain controller was successfully discovered through the private lab network.

Because `DC01` has both NAT and internal interfaces, DNS registration required additional troubleshooting to ensure the client resolved the domain controller through:

```text
192.168.50.10
```

## Outcome

`DC01` became the operational domain controller and DNS server for `vermacorp.local`.

## Skills Practiced

* Active Directory Domain Services
* Forest and domain creation
* DNS delegation concepts
* Domain-controller discovery
* SYSVOL and NETLOGON
* Windows Server service validation
* DNS troubleshooting

## Evidence

### DNS infrastructure

![DNS Manager](../../screenshots/phase-02/DNS%20Manager.png)

*DNS Manager shows the `vermacorp.local` and `_msdcs.vermacorp.local` forward lookup zones, along with DC01 host records.*

### Active Directory service health

![Active Directory services running](../../screenshots/phase-02/AD%20services%20running%20phase%202.png)

*The final PowerShell output confirms that DFSR, DNS, Netlogon, and NTDS are running on DC01.*

### SYSVOL and NETLOGON validation

![SYSVOL and NETLOGON validation](../../screenshots/phase-02/SYSVOL%20and%20NETLOGON%20validation.png)

*The `net share` output confirms that SYSVOL and NETLOGON are available, and the SYSVOL path test returned `True`.*

### Organizational units and account membership

![Organizational units and account membership](../../screenshots/phase-02/Organizational%20Units%20and%20Account%20Membership.png)

*Active Directory Users and Computers shows the Lab-Users, Lab-Admins, and Lab-Workstations OUs. The administrative account is a member of Domain Admins and Domain Users.*

### DNS name resolution

![DC01 DNS lookup](../../screenshots/phase-02/correct%20ip%20address%281%29.png)

*The DNS server at 192.168.50.10 successfully resolves `dc01.vermacorp.local` to 192.168.50.10. The additional addresses shown reflect DC01’s multi-homed configuration.*

## Evidence Notes

These screenshots document the deployment and validation of Active Directory Domain Services and DNS on DC01. The evidence confirms the domain zones, required services, SYSVOL and NETLOGON availability, organizational structure, account privileges, and DNS name resolution.

****
