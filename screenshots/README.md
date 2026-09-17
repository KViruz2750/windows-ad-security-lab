# Screenshot Evidence

Screenshots are organized by the phase of the lab they support. Original filenames are retained; the folder and descriptions provide the context.

## `phase-01/`

Contains evidence of the initial server and network configuration:

* Windows Server installation and desktop
* VirtualBox NAT adapter
* VirtualBox internal-network adapter
* Static IP configuration
* `ipconfig` output
* Successful gateway and internet pings
* Successful DNS lookup for `google.com`

## `phase-02/`

Contains evidence of the domain-controller deployment:

* Active Directory Domain Services wizard
* DNS options and delegation warning
* Successful prerequisite checks
* Server Manager showing AD DS and DNS
* DNS Manager and domain records
* `SYSVOL` and `NETLOGON` shares
* Domain-controller discovery results

## `phase-03/`

Contains evidence of identity and client configuration:

* Active Directory organizational units
* Standard and administrative accounts
* Administrative group membership
* Windows client virtual-machine settings
* Client network configuration
* Domain-join process
* `CLIENT01` inside the `Lab-Workstations` OU
* Successful domain-account logins

## `phase-04/`

Contains evidence of Group Policy and authentication auditing:

* GPO linked to `Lab-Workstations`
* Logon auditing configuration
* User-account auditing configuration
* Successful `gpupdate /force`
* `gpresult` showing the GPO was applied
* Event Viewer filtered for Events `4624` and `4625`
* Detailed failed-logon event
* Detailed successful-logon event



