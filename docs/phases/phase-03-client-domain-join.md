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

