# Trusts

Owner: padowla
Created time: February 20, 2024 5:13 PM

In an AD environment, **trust** is a relationship between two domains or forests which allows users of one domain or forest to access resources in the other domain or forest.

So the trust configuration then allows the permissions to be applied through ACL. So between different domains or forests, it is the combination of **Trust + [ACM](https://www.notion.so/ACM-ACL-DACL-SACL-be5782955ca24173b9d3078fd6e1b6d5?pvs=21)** that allows the correct access of resources.

Trust can be:

- **automatic** (parent-child, same forest etc.)
- **established** (forest, external).

The object created after the trust relationship establishment is called **Trusted Domain Objects (TDOs),** is stored in Active Directory so that the information can be retrieved when required

![Untitled](Trusts%20589d3dddc9284091b38927fa5621ba5a/Untitled.png)

# Trust Direction

## One-way Trust

![Untitled](Trusts%20589d3dddc9284091b38927fa5621ba5a/Untitled%201.png)

<aside>
⚠️ The trust, as mentioned before, allows users of the Trusted Domain to access resources in the Trusting Domain, provided that the necessary **ACLs** are configured.

</aside>

## Two-way Trust

![Untitled](Trusts%20589d3dddc9284091b38927fa5621ba5a/Untitled%202.png)

# Trust Transitivity

A **transitive trust** is one in which the trust relationship that is extended to one domain is automatically extended to all other domains that trust that domain

![Untitled](Trusts%20589d3dddc9284091b38927fa5621ba5a/Untitled%203.png)

- **Transitive**: Can be extended to establish trust relationships with other domains. All the default intra-forest trust relationships (Tree-root, ParentChild) between domains within a same forest are transitive two-way trusts.
Domains in same forest have an implicit two-way trust with other domains. There is a trust key between the parent and child domains.

- **Non Transitive**: Cannot be extended to other domains in the forest. Can be two-way or one-way.

![Untitled](Trusts%20589d3dddc9284091b38927fa5621ba5a/Untitled%204.png)

This is the default trust (called **external** trust) between two domains in different forests when forests do not have a trust relationship

# Type of Trusts

**Parent-child trust**: It is created automatically between the new domain and the domain that precedes it in the namespace hierarchy, whenever a new domain is added in a tree. For example, `prod.cybercorp.lab` is a child of `cybercorp.lab`, in this case the trust is automatically two-way transitive.

![Untitled](Trusts%20589d3dddc9284091b38927fa5621ba5a/Untitled%205.png)

**Tree-root trust**: It is created automatically between whenever a new domain tree is added to a forest root. For example, `cybercorp.lab` and `techcorp.lab`, both are root domains. This trust is automatically two-way transitive.

![Untitled](Trusts%20589d3dddc9284091b38927fa5621ba5a/Untitled%206.png)

**Shortcut trust**: Are manually created one-way, transitive trusts. They can only exist **within a forest**. They are created to optimize the authentication process shortening the trust path. These trusts are created when one domain needs to trust another domain by bypassing the hierarchy of trusts such as parent-child trust and Tree-root trusts.

![Untitled](Trusts%20589d3dddc9284091b38927fa5621ba5a/Untitled%207.png)

**External Trusts**: Is a one-way non-transitive trust. These trusts are manually established. An external trust is established with an external domain outside the forest of the trusting domain.

![Untitled](Trusts%20589d3dddc9284091b38927fa5621ba5a/Untitled%208.png)

**Forest Trusts**: Between forest root domain. Cannot be extended to a third forest (no implicit trust). Can be one-way or two-way and transitive or non transitive.
Forest trust cannot be extended to other forests, for example:
if `Forest1.com` trusts `Forest2.com`, and another forest trust is created between `Forest2.com` and `Forest3.com`, `Forest1.com` does not have an implied trust. If a trust is required, one must be manually created.

![Untitled](Trusts%20589d3dddc9284091b38927fa5621ba5a/Untitled%209.png)

# SID Filtering

For a newly set up **trust between two domains** **or two forests**, the **SID Filtering is activated by default**. 

The filter removes all foreign SIDs from the user’s Access Token while accessing a resource via a trust in a trusting domain. An example for a foreign SID would be the SID-history of a migrated user-account. The SID-history of user accounts and groups enables access to resources in the trusting domain – in case the filtering is deactivated.

![The picture shows how the **SID-History** (from the source domain) is deleted from the Token while accessing via the trust (with an activated SID filter). Access is not possible.](Trusts%20589d3dddc9284091b38927fa5621ba5a/Untitled%2010.png)

The picture shows how the **SID-History** (from the source domain) is deleted from the Token while accessing via the trust (with an activated SID filter). Access is not possible.

During an **Active Directory migration**, the SID-history is used for migrated user accounts in the trusted domain (target) to gain access to resources in the trusting domain (source). **With activated SID Filtering this is impossible**.

To get the current status of SID filtering run this command:

```powershell
netdom trust source.domain /domain:target.domain /quarantine
```

## **Deactivate SID Filter for Domain Trust**

```powershell
Netdom trust <TrustingDomainName> /domain:<TrustedDomainName> /quarantine:No

```

> **TrustingDomainName:** the domain with the resource to be accessed. For migrations this usually is the source domain.
> 
> 
> **TrustedDomainName**: the domain with the accessing user-account. For migrations this usually is the target domain.
> 
> **quarantine**: “no” deactivates the SID Filter, “yes” activates SID Filter.
> 

## **Deactivate SID Filter for Forest Trust**

To enable using a SID-history via a Forest Trust, another parameter has to be employed:

```powershell
Netdom trust <TrustingDomainName> /domain:<TrustedDomainName> /enablesidhistory:Yes
```

> **enablesidhistory**: “yes” deactivates the SID-Filter, “no” activates it.
>