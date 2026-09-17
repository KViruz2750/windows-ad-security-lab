# Phase 1: Foundation and Networking

## Objective

Build the virtualized infrastructure and establish reliable communication between the domain controller and future client workstation.

## Environment

* Virtualization platform: Oracle VirtualBox
* Server: Windows Server 2025 Evaluation with Desktop Experience
* Server hostname: `DC01`
* Internet network: NAT
* Private lab network: `VERMACORP-LAB`

## Network Design

The server uses two network adapters:

| Adapter   | Network         | Purpose                      | Address         |
| --------- | --------------- | ---------------------------- | --------------- |
| Adapter 1 | NAT             | Internet access and updates  | `10.0.2.10`     |
| Adapter 2 | `VERMACORP-LAB` | Private domain communication | `192.168.50.10` |

The internal adapter uses the `192.168.50.0/24` subnet and intentionally has no default gateway. The NAT adapter handles external connectivity.

## Configuration

The internal adapter was configured with:

```text
IP address: 192.168.50.10
Subnet mask: 255.255.255.0
Default gateway: None
DNS server: 192.168.50.10
```

The following commands were used to validate and configure networking:

```powershell
ipconfig
ping 10.0.2.2
ping 8.8.8.8
nslookup google.com
```

The external DNS test successfully resolved `google.com` through the VirtualBox NAT DNS service.

The private adapter was configured with:

```powershell
New-NetIPAddress -InterfaceAlias "Ethernet 2" -IPAddress 192.168.50.10 -PrefixLength 24
Set-DnsClientServerAddress -InterfaceAlias "Ethernet 2" -ServerAddresses 192.168.50.10
```

## Outcome

The server had working internet access through NAT and a separate isolated network for Active Directory communication.

## Skills Practiced

* IPv4 addressing
* Subnet masks
* NAT versus internal networking
* DNS resolution
* PowerShell network configuration
* Connectivity troubleshooting

