# Project Roadmap

This roadmap tracks my development of the Windows Active Directory Security Lab. Completed work is documented separately in the `docs/` directory.

## Status 

* `[x]` Completed
* `[ ]` Planned
* `Current` Next active phase

## Phase 1: Foundation

* [x] Install Windows Server 2025
* [x] Rename server to `DC01`
* [x] Configure static networking
* [x] Create the isolated `VERMACORP-LAB` network
* [x] Verify connectivity and DNS resolution

## Phase 2: Active Directory

* [x] Install Active Directory Domain Services
* [x] Create the `vermacorp.local` domain
* [x] Configure DNS
* [x] Verify `SYSVOL` and `NETLOGON`
* [x] Verify domain-controller services

## Phase 3: Client and Domain Join

* [x] Create the Windows 11 Pro client
* [x] Name the client `CLIENT01`
* [x] Configure the private client IP
* [x] Join the client to `vermacorp.local`
* [x] Create standard and administrative accounts
* [x] Move `CLIENT01` into `Lab-Workstations`

## Phase 4: Group Policy and Auditing

* [x] Create `GPO-Lab-Workstation-Security`
* [x] Link the GPO to `Lab-Workstations`
* [x] Enable logon auditing
* [x] Enable user-account auditing
* [x] Verify policy application with `gpresult`
* [x] Investigate Event IDs `4624` and `4625`

## Phase 5: Advanced Auditing — Current

* [ ] Enable process-creation auditing
* [ ] Investigate Event ID `4688`
* [ ] Enable account-creation auditing on `DC01`
* [ ] Investigate Event ID `4720`
* [ ] Audit privileged-group changes
* [ ] Investigate Events `4728`, `4729`, and `4732`

## Phase 6: Permissions and Least Privilege

* [ ] Configure password complexity
* [ ] Configure account-lockout policies
* [ ] Create security groups
* [ ] Create a controlled file share
* [ ] Test read and modify permissions
* [ ] Compare standard-user and administrator access

## Phase 7: Network Analysis

* [ ] Perform an authorized Nmap scan
* [ ] Identify open ports and services
* [ ] Capture traffic with Wireshark
* [ ] Document normal and suspicious network behavior

## Phase 8: SIEM Investigations

* [ ] Collect Windows security logs
* [ ] Import logs into a SIEM
* [ ] Create authentication-focused searches
* [ ] Investigate repeated failures and privilege changes
* [ ] Write multiple SOC-style investigation reports

## Project Deliverables

* [ ] Complete all phase documentation
* [ ] Add sanitized screenshots and diagrams
* [ ] Add troubleshooting reports
* [ ] Add investigation reports
* [ ] Publish final architecture and lessons learned
* [ ] Create resume-ready project bullets
