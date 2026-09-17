# Windows Active Directory Security Lab

A hands-on virtualized lab for building practical experience in Windows administration, Active Directory, DNS, Group Policy, authentication auditing, and defensive security operations.

## Project Overview

I built a two-machine Windows domain environment in Oracle VirtualBox:

| System     | Role                                                 |
| ---------- | ---------------------------------------------------- |
| `DC01`     | Windows Server 2025 domain controller and DNS server |
| `CLIENT01` | Windows 11 Pro domain-joined workstation             |

The machines communicate through an isolated `VERMACORP-LAB` internal network using the domain:

```text
vermacorp.local
```

## What I’ve Built so far
* Configured static IPv4 networking and DNS
* Deployed Active Directory Domain Services
* Created organizational units for users, administrators, and workstations
* Created separate standard and administrative domain accounts
* Joined `CLIENT01` to the domain
* Deployed `GPO-Lab-Workstation-Security`
* Enabled logon and user-account auditing
* Verified policy application with `gpupdate` and `gpresult`
* Investigated failed and successful authentication events in Event Viewer

## Investigation Highlight

I analyzed Event ID `4625`, identifying a failed interactive logon caused by an incorrect password. The event was generated locally on `CLIENT01` and correlated with a subsequent successful authentication event.

## Key Lessons

This project has included troubleshooting real configuration issues involving:

* Multi-adapter DNS registration
* Domain-controller connectivity
* SYSVOL and NETLOGON access
* Windows Time synchronization
* Group Policy processing error `0x576`

## Roadmap

* [ ] Investigate process-creation events (`4688`)
* [ ] Audit account and privileged-group changes
* [ ] Configure password and account-lockout policies
* [ ] Test file permissions and least privilege
* [ ] Perform authorized Nmap and Wireshark analysis
* [ ] Integrate logs with a SIEM
* [ ] Publish additional SOC investigation reports

Detailed setup notes, evidence, diagrams, and investigations are available in the [`docs/`](docs/) directory.

> This is a personal project to develop and hone my Cybersecurity/IT skills.

