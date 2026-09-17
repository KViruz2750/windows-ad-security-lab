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
****
