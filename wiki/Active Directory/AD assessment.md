# AD assessment

# Index

1. **Authentication protocol deprecated (NTLMv1)**
2. **LAPS disabled**
3. **Weak Password Policy** 
4. **Windows System Update Service (WSUS) configured with HTTP not secure**

# **Authentication protocol deprecated (NTLMv1)**

`Network security: LAN Manager authentication level` is the GPO responsible for re-introducing NTLMv1, disabled by default since Windows Server 2008 but administrators may re-enable it for backwards compatibility. The most common vulnerable configuration would look like this from the GPO:

![image.png](AD%20Assessment/image.png)

Enabling the policy creates the registry key:

                          `HKLM\System\CurrentControlSet\Control\Lsa\LmCompatibilityLevel`

0, 1, and 2 are the values that result in NTLMv1 being enabled. 

**If we have access to a domain-joined machine** we can check whether the domain is vulnerable by querying the key:

```
reg query HKLM\System\CurrentControlSet\Control\Lsa /v LmCompatibilityLevel
```

Otherwise we launch responder with the SMB server enabled, then either wait until a host connects to us as a result of the LLMNR/NBT-NS poisoning or, if we have domain credentials, fire up [coercer](https://github.com/p0dalirius/Coercer).

***TO DO…***

## References

- [https://medium.com/@offsecdeer/ntlmv1-domain-compromise-9bd8dd7e9891](https://medium.com/@offsecdeer/ntlmv1-domain-compromise-9bd8dd7e9891)

---

# LAPS disabled

Extract from Neo4J console the table with all computers that have LAPS disabled:

```sql
MATCH (c:Computer {haslaps:False}) 
RETURN c.name AS Computer_Name, 
       c.domain AS Domain, 
       c.operatingsystem AS OS, 
       c.enabled AS Enabled, 
       c.lastlogon AS Last_Logon
ORDER BY Computer_Name
```

![image.png](AD%20Assessment/image%201.png)

---

# Weak Password Policy

[polenum | Portcullis Labs](https://labs.portcullis.co.uk/tools/polenum/)

`polenum` is a python script which can be used to get the password policy from a Windows machine.

It uses the [Impacket](http://oss.coresecurity.com/projects/impacket.html) library from CORE Security Technologies to extract the password policy information from a Windows machine. This allows a non-Windows (Linux, Mac OSX, BSD etc..) user to query the password policy of a remote Windows box without the need to have access to a Windows machine to perform the query.

## Key features

- Can extract password and associated information from a windows machine
- Will connect over a NULL or authenticated share
- Supports encrypted/signed sessions

### Usage

We can also specify a different domain if exists a trust cross-domain or cross-forest.

```bash
polenum --domain <domain> --username <username> --password '<password>'
```

---

# Windows System Update Service (WSUS) configured with HTTP not secure

it is necessary to enumerate via BloodHound (LDAP) the GUID of the GPO associated with the WSUS configuration and the WSUS server IP or FQDN.

![image.png](AD%20Assessment/image%202.png)

Once the GUID is obtained, it is possible to access the SYSVOL share on the Domain Controller to download the Registry.pol file containing the registry settings related to the WSUS server configuration:

```bash
smbclient //<DOMAIN>/SYSVOL -U 'DOMAIN\username'%'password'
```

![image.png](AD%20Assessment/image%203.png)

```bash
get Registry.pol
```

<aside>
⚠️

`Registry.pol` files store Group Policy registry settings in a **binary format**, so you can't just open them with a text editor. Instead, you need to **decode them**.

</aside>

Microsoft provides the **Local Group Policy Object Utility (`LGPO.exe`)** to convert `Registry.pol` into a readable format:

[https://www.microsoft.com/en-us/download/details.aspx?id=55319](https://www.microsoft.com/en-us/download/details.aspx?id=55319) → **download the ZIP file type**

[LGPO.zip](AD%20Assessment/LGPO.zip)

Run the following command to export the contents for machine group policy (`/m`):

```bash
LGPO.exe /parse /m 'path\to\Registry.pol'

```

<aside>
ℹ️

To extract User settings use `/u` instead

</aside>

![image.png](AD%20Assessment/image%204.png)

---

# Check MachineAccountQuota (MAQ)

See [ms-DS-MachineAccountQuota](Interesting%20attributes/ms-DS-MachineAccountQuota.md)

---