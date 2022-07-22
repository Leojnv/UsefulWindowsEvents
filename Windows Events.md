# General Events you should look closely in general

- 1102 – Event log cleared – Often the attackers try to conceal themselves by clearing the log entries
- 4610,4611,4614,4622 – Local Security Authority modification – Attackers might want to modify LSA to ensure persistence, escalation or credential theft
- 4648 – Explicit credential logon – when a logged on user provides different credentials to access a resource (smb login with different credentials)
- 4661 – A handle to an object was requested – SAM / DSA access
- 4672 – Special privileges assigned to new logon – If the logged on user gets administrative rights assigned- useful to monitor administrative logons
- 4723 – Account password change attempted – if a known password change then all good but if not.. good luck
- 4964 – Custom Special Group logon tracking – Create registry key with groups of interest (Domain Admin, Enterprise Admin, Schema admin etc) and log if any of the members logs in
- 7045,4697 – New Service was installed – attackers might want to install additional services for persistence and for privilege escalation also psexec uses the same method
- 4698,4702 – Scheduled task creation/modification - attackers might want to add additional scheduled tasks for persistence or modify existing ones for privilege escalation
- 4719 – System audit policy was changed – Attacker might want to adjust the audit policy to remain hidden
- 4732 – member was added to a local group – attackers often create new local accounts and add them to the administrators group
- 4720 – local user account created – Attackers often create additional accounts for persistence
- 3065,3066 – LSASS auditing for code integrity – Monitors LSA drivers and plugins
- 3033,3063 – LSA protection – Monitors LSA drivers and plugins and blocks those which are not properly signed
- 4798 – users local group membership was enumerated- potential recon activity- analyze normal activity to spot malicious one
- 4735,4737,4755 – security groups changed – attackers often modify security groups to assign themselves more permissions
- 4728,4732,4756 – user added to security group – attackers might add themselves to different groups to assign more privileges
- 4624 – successful logon – information of an account successfully logging on and how it logged on

| Logon Type | Name | Description | Credentials on Disk | Credentials in Memory |
| -- | -- | -- | -- | -- |
| 0 | System | Rare logon type but might hint malicious activity | yes | yes |
| 2 | Interactive | Console logon or RunAs | no | yes |
| 3 | Network | Access of file shares, printers, powershell remoting | No | No |
| 4 | Batch | Scheduled tasks | Yes | Yes |
| 5 | Service | Services | Yes | Yes |
| 7 | Unlock | Unlocking the system | No | Yes |
| 8 | Network Clear Text | Network logon with IIS basic auth | Might | Yes |
| 9 | New Credentials | RunAs /NetOnly execution with different credentials than logon credentials | No | Yes |
| 10 | Remote Interactive | RDP, Remote assistance | Might | Yes |
| 11 | Cached Interactive | Logon with cached credentials (DC unavailable) | Yes | Yes | 

- 4625 – account failed to log on – important are the reasons why the user failed to logon 

| Status / substatus code | Description |
| -- | -- |
| 0XC000005E | There are currently no logon servers available to service the logon request. |
| 0xC0000064 | User logon with misspelled or bad user account |
| 0xC000006A | User logon with misspelled or bad password |
| 0XC000006D | The cause is either a bad username or authentication information |
| 0XC000006E |Indicates a referenced user name and authentication information are valid, but some user account restriction has prevented successful authentication (such as time-of-day restrictions). |
| 0xC000006F | User logon outside authorized hours |
| 0xC0000070 | User logon from unauthorized workstation |
| 0xC0000071 | User logon with expired password |
| 0xC0000072 | User logon to account disabled by administrator |
| 0XC00000DC | Indicates the Sam Server was in the wrong state to perform the desired operation. |
| 0XC0000133 | Clocks between DC and other computer too far out of sync |
| 0XC000015B | The user has not been granted the requested logon type (also called the logon right) at this machine |
| 0XC000018C | The logon request failed because the trust relationship between the primary domain and the trusted domain failed. |
| 0XC0000192 | An attempt was made to logon, but the Netlogon service was not started. |
| 0xC0000193 | User logon with expired account |
| 0XC0000224 | User is required to change password at next logon |
| 0XC0000225 | Evidently a bug in Windows and not a risk |
| 0xC0000234 | User logon with account locked |
| 0XC00002EE | Failure Reason: An Error occurred during Logon |
| 0XC0000413 | Logon Failure: The machine you are logging on to is protected by an authentication firewall. The specified account is not allowed to authenticate to the machine. |
| 0x0 | Status OK. |
- 4634 – an account logged off
- 4647 – user initiated log off
- 4688 – process created – attackers tend to often use some commands in a tight timeframe that are rarely used by regular users 
	- commands group 1: tasklist, ver, ipconfig, systeminfo, net time, netstat, whoami, net start, qprocess, query
	- commands group 2: dir, net view, ping, net use, type, net user, net localgroup, net group, net config, net share, at, reg, wmic, wusa, netsh advfirewall, sc, rundll32
# Events specifically for the **DC**
- 4768 – Kerberos auth ticket was requested – helpful in tracking users-workstations
- 4769 – User requests a Kerberos service ticket – helpful in identifying silver ticket attacks
- 4964 – SpecialGroup logon – someone got assigned privileges from the specialgroup 
- 4625,4771 – logon failure
- 4765,4766 – SID history added to an account – if its not domain migration then it is probably malicious 
- 4794 – DSRM account password change attempt – if not intentional then malicious
- 4780 – ACLs set on admin accounts - if not intentional then malicious
- 4739 – Domain Policy changed - if not intentional then malicious
- 4713 – Kerberos policy changed - if not intentional then malicious
- 4724 – password reset attempt – keep an eye of important users (aadministrators)
- 4735 – local security group changed – keep an eye on important groups
- 4737 – global security group changed – keep an eye on important groups

# BONUS: Stuff to audit
- Account Logon (Success, Failure)
	- Account credential validation 
	- Audit kerberos authentication service
	- Audit kerberos service ticket operations
- Account management (Success, Failure)
	- Audit computer account management
	- Audit other account management events
	- Audit security group management
	- Audit user account management
- Detailed tracking (Success, Failure)
	- Audit process creation
- DS access (Success, Failure)
	- Audit directory service access
	- Audit directory service changes
- Logon and logoff
	- Audit account lockout (Success)
	- Audit logoff (Success)
	- Audit logon (Success, Failure)
	- Audit special logon (Success, Failure)
- Object Access (Success, Failure)
	- Audit file share
- System (Success, Failure)
	- Audit security state change
	- Audit security system extension
	- Audit system integrity

# Tools for parsing logs
- ELK stack
- Splunk
- Powershell
- [DeepBlueCLI](https://github.com/sans-blue-team/DeepBlueCLI)
- [What2Log](https://what2log.com/)
