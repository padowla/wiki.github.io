# ms-DS-MachineAccountQuota

# What is this?

`MachineAccountQuota` **(MAQ) is a domain level attribute that by default permits unprivileged users to attach up to 10 computers to an Active Directory (AD) domain.**

Open `dsa.msc` (Active Directory Users and Computers). If not already enabled, **enable** `Advanced Features`. Next open the properties of your domain (right click), click on Attribute editor and navigate to the attribute `ms-DS-MachineAccountQuota`. Are you surprised? **Every user (Domain User) can add up to 10 Computers by default.**

From Powershell and machine AD joined:

```powershell
Get-ADObject ((Get-ADDomain).distinguishedname) -Properties ms-DS-MachineAccountQuota
```

```powershell
Get-ADDomain | Select-Object -ExpandProperty DistinguishedName | Get-ADObject -Properties 'ms-DS-MachineAccountQuota'
```

From non joined machine (Linux):

```bash
nxc ldap 172.17.0.100 -d domain.local -u 'username' -p 'password' -M maq
```

**The creator account’s SID is stored in the machine account’s ms-DS-CreatorSID attribute**. AD populates this attribute only when the creator is not an administrator, or has not been delegated the privilege to add machine accounts.

# **Changing the default value**

A value of 0 means that domain users are are not allowed to add computer accounts.

Open the properties of the domain and double click `ms-DS-MachineAccountQuota`. Modify the value. The number represents the number of computers that you want users to be able to add to the domain. **I recommend changing it to 0.**

# ⚠️ **NOTE** ⚠️

Testers need to be aware that the MAQ attribute set to a non-zero value doesn't necessarily mean the users can create machine accounts. The right to add workstations to a domain can in fact be changed in the Group Policies. `Group Policy Management Console (gpmc.msc) > Domain Controllers OU > Domain Controllers Policy > Computer Configuration > Policies > Windows Settings > Security Settings > Local Policies > User Rights Assigments > Add workstations to domain`

# References

- [https://sid-500.com/2017/09/09/securing-active-directory-who-can-add-computers-to-the-domain-only-the-domain-admin-are-you-sure/](https://sid-500.com/2017/09/09/securing-active-directory-who-can-add-computers-to-the-domain-only-the-domain-admin-are-you-sure/)
- [https://www.thehacker.recipes/ad/movement/builtins/machineaccountquota](https://www.thehacker.recipes/ad/movement/builtins/machineaccountquota)