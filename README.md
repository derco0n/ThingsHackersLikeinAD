# ThingsHackersLikeinAD
Some Things that hackers like in an AD environment

## About:
This is a brief description about some security flaws in MS Active Directory.
For more detailed information please search the internet or use any provided link.
You can also find more about this topic on youtube. I suggest this channel: https://www.youtube.com/channel/UCpoyhjwNIWZmsiKNKpsMAQQ/videos

### standard Domain Users can join Computers to AD
By default (at least the last 20 years) a standard domain user, with no special privileges, is allowed to join up to ten computers to your Active Directory.
This is still the default setting in Domains that are freshly setup and can lead to untrusted to be computers joined to your environment.
The good news is, this can be prevented by adjusting your "Default Domain"-policy:
Computer Configuration -> Windows Settings -> Security Settings -> Local Policies -> User Rights Assignment  -> find the entry named "Add workstations to the domain"
and/or by adjusting the value "ms-DS-MachineAccountQuota" of your domain using the ADSI-Editor

Links:
https://community.spiceworks.com/topic/130925-by-default-ad-ds-users-can-join-up-to-10-computers-to-the-domain
https://www.der-windows-papst.de/2019/01/01/berechtigung-zum-hinzufuegen-von-computern-zum-ad-entfernen/

### Using high privileged Domain Accounts on Client systems
You should never, ever use any high privileged Domain Account (like e.g. YOURDOMAIN\Administrator) to do things on clients, as your credentials can be stolen by local system accounts that are member of the "Administrators"-group.

### AD-Administrative groups
You might know that members of "Domain Admins"-group are very powerful, as they can change everything in your AD-Domain, but did you know there are plenty more groups that have rights that are very powerful as well? e.g.: "Enterprise Admins", "Backup Operators", "Print Operators".
You should really keep track of who is member of this groups and don't overlook any nested groups.
https://docs.microsoft.com/de-de/windows-server/identity/ad-ds/plan/security-best-practices/appendix-b--privileged-accounts-and-groups-in-active-directory

### Change your krbtgt-accounts password frequently. really. do this.
In most environments the password for the built-in account "krbtgt" never ever gets changed. As this account is used to sign kerberos-tokens it is a high vlaue target for attackers. If an attacker gets his hands on the NTLM-Hash of this account's-password, he/she is able to craft golden tickets, which allow access to everything for a timeperiod of up to 10 years! Therefore, you really should change the password for this account frequently and do it twice (i suggest one day after another) as the last two passwords are cached.
https://www.msxfaq.de/windows/kerberos/krbtgt_keyrollover.htm

### Accounts without Kerberos-PreAuth
Any Account that has Kerberos-PreAuthentication disabled is a serious security risk. You should avoid them whenever possible. Check that noone is member of the "Pre-Windows-2000-compatibility" group and that no account has the flag set.
https://social.technet.microsoft.com/wiki/contents/articles/23559.kerberos-pre-authentication-why-it-should-not-be-disabled.aspx


