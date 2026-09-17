# Phase 3: Client Configuration and Domain Join

## Objective

Create a Windows workstation, connect it to the isolated lab network, and join it to the `vermacorp.local` domain.

## Client Configuration

A Windows 11 Pro virtual machine was created in Oracle VirtualBox.

| Setting         | Value               |
| --------------- | ------------------- |
| Virtual machine | `CLIENT01-Win11-KV` |
| Computer name   | `CLIENT01`          |
| Memory          | 4096 MB             |
| Processors      | 2                   |
| Virtual disk    | 80 GB               |
| TPM             | Version 2.0         |
| Secure Boot     | Enabled             |

The client uses two adapters:

* Adapter 1: NAT for internet access
* Adapter 2: `VERMACORP-LAB` for domain communication

## Private Network Configuration

```text
IP address: 192.168.50.20
Subnet mask: 255.255.255.0
Default gateway: None
DNS server: 192.168.50.10
```

The domain controller at `192.168.50.10` was configured as the client’s DNS server so the workstation could locate Active Directory services.

## Domain Join

The client was joined to:

```text
vermacorp.local
```

After joining the domain, the computer object was moved from the default `Computers` container into:

```text
Lab-Workstations
```

## Account Testing

The following domain accounts were tested:

```text
VERMACORP\karan.user
VERMACORP\karan.admin
```

A separate local `ClientAdmin` account was retained for initial workstation administration.

The standard account was used to test ordinary access, while the administrative account was used for Group Policy and security administration.

## Outcome

`CLIENT01` successfully joined the domain and authenticated against `DC01`.

## Skills Practiced

* Windows 11 administration
* Domain joining
* DNS client configuration
* Computer accounts
* Standard versus privileged accounts
* Active Directory organizational units
* Virtual machine networking


## Evidence

### Client virtual machine network configuration

![phase 3 client 01 configuration](../../screenshots/phase-03/phase%203%20client%2001%20configuration.png)

*CLIENT01 Adapter 2 is enabled and attached to the isolated `VERMACORP-LAB` internal network.*

### Domain computer object and OU placement

![CLIENT01 computer object in Active Directory](../../screenshots/phase-03/phase%203%20Active%20Directory%20structure%20and%20accounts.png)

*Active Directory Users and Computers shows the CLIENT01 computer object inside the `Lab-Workstations` organizational unit.*

### Client-to-domain-controller validation

![CLIENT01 domain controller connectivity](../../screenshots/phase-03/phase%203%20client-to-domain-controller%20connectivity.png)

*CLIENT01 successfully reached DC01, resolved `dc01.vermacorp.local`, and located the domain controller with `nltest /dsgetdc:vermacorp.local`.*

### LDAP connectivity validation

![CLIENT01 LDAP connectivity](../../screenshots/phase-03/phase%203%20LDAP%20connectivity.png)

*CLIENT01 successfully connected to DC01 on TCP port 389 from source address `192.168.50.20`.*

## Evidence Notes

These screenshots document CLIENT01’s connection to the isolated lab network, its domain computer object and OU placement, successful communication with DC01, and LDAP connectivity.

The Active Directory screenshot shows the CLIENT01 computer object—not user accounts—so it is labeled accordingly. The `nltest` validation provides the conclusive proof that CLIENT01 can locate the `vermacorp.local` domain controller.


