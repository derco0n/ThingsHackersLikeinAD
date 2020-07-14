# ThinksHackerLikeinAD
Some Things that hackers like in an AD environment

## About:
This is a brief description about some security flaws in MS Active Directory.
For more detailed information please search the internet or use any provided link.

### standard Domain Users can join Computers to AD
By default (at least the last 20 years) a standard domain user, with no special privileges, is allowed to join up to ten computers to your Active Directory.
This is still the default setting in Domains that are freshly setup and can lead to untrusted to be computers joined to your environment.
The good news is, this can be prevented by adjusting your "Default Domain Controllers"-policy using the ADSI-editor:
Set the value "ms-DS-MachineAccountQuota" to 0 instead of the default value "10".

Link:
https://community.spiceworks.com/topic/130925-by-default-ad-ds-users-can-join-up-to-10-computers-to-the-domain
