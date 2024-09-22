# ThingsHackersLikeinAD
Some Things that hackers like in an AD environment

## About:
This is a brief description about some security flaws in MS Active Directory.
The goal is so wake up admins, that aren't aware of these to make their environments more robust and increasing overall IT-security.
For more detailed information please search the internet or use any provided link.
You can also find more about this topic on youtube.
I recommend this channel:
- https://www.youtube.com/channel/UCpoyhjwNIWZmsiKNKpsMAQQ/videos

## Checking:
To check your environment yourself, you might use tools like:
- mimikatz (https://www.varonis.com/blog/what-is-mimikatz/)
- kerbrute (https://github.com/ropnop/kerbrute)
- impacket (https://www.secureauth.com/labs/open-source-tools/impacket)
- evil-winrm (https://kalilinuxtutorials.com/evil-winrm-hacking-pentesting/)

## Actual weaknesses

### standard Domain Users can join Computers to AD
By default (at least the last 20 years) a standard domain user, with no special privileges, is allowed to join up to ten computers to your Active Directory.
This is still the default setting in Domains that are freshly setup and can lead to untrusted to be computers joined to your environment.
The good news is, this can be prevented by adjusting your "Default Domain"-policy:
__Computer Configuration -> Windows Settings -> Security Settings -> Local Policies -> User Rights Assignment  -> find the entry named "Add workstations to the domain"__
and/or by adjusting the value "ms-DS-MachineAccountQuota" of your domain using the ADSI-Editor

Links:
- https://community.spiceworks.com/topic/130925-by-default-ad-ds-users-can-join-up-to-10-computers-to-the-domain
- https://www.der-windows-papst.de/2019/01/01/berechtigung-zum-hinzufuegen-von-computern-zum-ad-entfernen/

### Using high privileged Domain Accounts on Client systems
You should never, ever use any high privileged Domain Account (like e.g. YOURDOMAIN\Administrator) to do things on clients, as your credentials can be stolen by local system accounts that are member of the "Administrators"-group.

### AD-Administrative groups
You might know that members of "Domain Admins"-group are very powerful, as they can change everything in your AD-Domain, but did you know there are plenty more groups that have rights that are very powerful as well? e.g.: "Enterprise Admins", "Backup Operators", "Print Operators".
You should really keep track of who is member of this groups and don't overlook any nested groups.
- https://docs.microsoft.com/de-de/windows-server/identity/ad-ds/plan/security-best-practices/appendix-b--privileged-accounts-and-groups-in-active-directory
- https://github.com/lazywinadmin/Monitor-ADGroupMembership
- https://github.com/derco0n/PSHTML-AD-Report

### Change your krbtgt-accounts password frequently. really. do this.
In most environments the password for the built-in account "krbtgt" never ever gets changed. As this account is used to sign kerberos-tokens it is a high value target for attackers. If an attacker gets his hands on the NTLM-Hash of this account's-password, he/she is able to craft golden tickets, which allow access to everything for a timeperiod of up to 10 years! Therefore, you really should change the password for this account frequently and do it twice (i suggest one day after another) as the last two passwords are cached.
- https://www.msxfaq.de/windows/kerberos/krbtgt_keyrollover.htm

### Accounts without Kerberos-PreAuth
Any Account that has Kerberos-PreAuthentication disabled is a serious security risk. You should avoid them whenever possible. Check that noone is member of the "Pre-Windows-2000-compatibility" group and that no account has the flag set.
- https://social.technet.microsoft.com/wiki/contents/articles/23559.kerberos-pre-authentication-why-it-should-not-be-disabled.aspx
- https://www.scip.ch/?labs.20181011

### User accounts with SPNs defined
Computer accounts change their passwords every 30 days to a new random, 128 character long string.
Different to that, the most user accounts and accounts an admin creates for a specific job, have a password which is significantly weaker than this and won't change that often.
If a user-account has a service principle name (SPN) defined, this weakness can be abused for a so called "kerberoast"-attack allowing an attacker to gain valid credentials for this account and therefore get an intial foothold into your environment.
- https://pentestlab.blog/2018/06/12/kerberoast/

### local administrator passwords
Not really an AD topic but it will affect your AD security. You should avoid to use the same password for client-local administrative accounts on different machines, as the NTLM-hashes will be the same. An attacker who might gain credentials on one client, can effectively use them on other machines as well.
This way attackers can make their way through your environment.
To prevent this you might want to use Microsoft LAPS
- https://www.msxfaq.de/windows/endpointsecurity/laps.htm
- https://docs.microsoft.com/en-us/previous-versions/mt227395(v=msdn.10)?redirectedfrom=MSDN

### Smartcard/Certificate-based privilege escalation
misconfigured certificate-templates can be abused by users with local administrative permissions, to gain domain administrator.

An attacker could possibly make a certificate request with an alternative name (e.g. "administrator@yourdomain.com).
If the certificate authority issues an certificate, this can be abused within a virtual smartcard which then can be used to authenticate against server.
The server will trust the smartcard, as it is signed by a trusted CA.

Commands to be used on attacker side:
TpmVscMgr create /name MyVSC /pin default /adminkey random /generate
    
certutil -csp “Microsoft Base Smart Card Crypto Provider” -importpfx C:\_INSTALL\WSCert.pfx
    
runas /user:lab.administrator@yourdomain.com /smartcard cmd
PIN = 12345678

### From DNS-Admin to Domain Takeover
You must be aware that DNS-Administrators (or anyone who has write-access to the DNS-Server-object) is able to load .dll's with the DNS-service.
As the DNS-service is running as SYSTEM, this allows for a privilege escalation to domain administrator.
More on that topic can be found [here](https://medium.com/@esnesenon/feature-not-bug-dnsadmin-to-dc-compromise-in-one-line-a0f779b8dc83)

Example code: [google](https://google.com/search?q=dns-exe-persistance)
