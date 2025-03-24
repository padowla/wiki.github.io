# Introduction

Owner: padowla
Created time: February 17, 2024 10:44 AM

![Untitled](Introduction%200c9e96314e5a4ee794c813354a04cdcf/Untitled.png)

The **schema** defines the attributes of all objects defined and used in Active Directory.
The diagram indicates that as in the figure, all AD users must have the attributes shown:

![Untitled](Introduction%200c9e96314e5a4ee794c813354a04cdcf/Untitled%201.png)

When you extend the schema, such as when you install Exchange Server On-prem, you must have Schema Admin or Enterprise Admin permissions to be able to add additional attributes to Active Directory objects.

The **security boundary of an Active Directory** environment is not actually the domain but the **forest**.

**Sites** are useful for facilitating and managing a geo-distributed infrastructure.

![Untitled](Introduction%200c9e96314e5a4ee794c813354a04cdcf/Untitled%202.png)

# Authorization Overview

![Untitled](Introduction%200c9e96314e5a4ee794c813354a04cdcf/Untitled%203.png)

# **Active Directory Partitions**

To search objects in AD, first we need to understand the basic structure of the AD database and find out where we should search from. Active Directory can support tens of millions of objects and to scale up those objects, the **AD database is divided up into partitions** (aka **naming context**) for replication and administration. 

Each logical partition replicates its changes separately among domain controllers in the forest. See the typical structure below:

<aside>
💡

Download [ADExplorer](https://learn.microsoft.com/en-us/sysinternals/downloads/adexplorer) to view this structure

</aside>

![](https://dev-2null.github.io/assets/img/adenum/adpartitions.png)

- **Domain Partition**: `DC=privatelab,DC=local`
    
    It stores information about directory objects found in the given domain. (E.g. Users/Groups/Computers/OUs.)
    
- **Configuration Partition**: `CN=Configuration,DC=privatelab,DC=local`
    
    It stores information about the directory structure such as available domains/sites and domain controllers in the forest.
    
- **Schema Partition**: `CN=Schema,CN=Configuration,DC=privatelab,DC=local`
    
    It stores definition of all objects, along with their properties.
    
- **Application Partition**:
`DC=DomainDnsZones,DC=privatelab,DC=local` & `DC=ForestDnsZones,DC=privatelab,DC=local`
    
    It is optional, and it stores information about a specific application, it cannot be used to store security principal objects. (E.g. Adding the DNS role will add `DomainDnsZone`/`ForestDnsZone`)
    

Most of the time, objects we want to enumerate are under the default naming context (domain partition).