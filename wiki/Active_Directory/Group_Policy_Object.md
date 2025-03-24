# Group Policy Object

Owner: padowla
Created time: February 20, 2024 11:50 AM

**Group Policy** provides the ability to manage configuration and changes easily and centrally in Active Directory.

Allows configuration of :

- Security settings
- Registry-based policy settings
- Group policy preferences like startup/shutdown/log-on/logoff scripts settings
- Software installation

GPOs have two levels of settings application:

- **Computer**: settings that apply to the computer
- **Users**: settings that apply to the user

In addition, for each of these two categories we have a further classification:

- **Policies**: settings that **CANNOT** be changed by the user
- **Preferences**: settings that can be changed by the user

![Untitled](Group%20Policy%20Object%20e8d6951027cd4f7289db1cd535346eac/Untitled.png)

# Refresh interval

According to Microsoft, by default, **computer Group Policy is updated in the background every 90 minutes**, with a random offset of 0 to 30 minutes, which forces the application of specific settings to any machines in an OU/site where the GPO is linked.

# Restricted Groups

Let's imagine being system administrators of a company and having installed a new server on which the application system administrator will have to access with his own domain credentials and complete the configuration of the management system.

To avoid having to give Domain Admin permissions or having to create a local account with Local Admin permissions on one or more machines **manually**, it is possible to manage the local groups of individual machines through **Restricted Groups**.

![Untitled](Group%20Policy%20Object%20e8d6951027cd4f7289db1cd535346eac/Untitled%201.png)

The Restricted Groups policy is located in `Computer Configuration/Windows Settings/Security Settings/Restricted Groups`:

![Untitled](Group%20Policy%20Object%20e8d6951027cd4f7289db1cd535346eac/Untitled%202.png)

This policy thus defined on the machines belonging to the OU to which the GPO applies governs the **members of the local Administrators group:**

![Untitled](Group%20Policy%20Object%20e8d6951027cd4f7289db1cd535346eac/Untitled%203.png)

1. **exclusive configuration**, that is, when the machine boots up and gets the GPO from the Domain Controller, any other configuration present is **reset**. Therefore, in this example, the only users who will be part of the local Administrators group will be the `Domain Admins` and the `pufferfish` user.
2. **inclusive configuration** in which defined users are **added** to existing users within the Local Administrators group.
