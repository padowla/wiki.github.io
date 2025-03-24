# LAPS

Owner: padowla
Created time: February 27, 2024 11:28 AM

Microsoft **Local Administrator Password Solution** **(LAPS)** provides automated local administrator account management for computers in Active Directory. A client-side component installed on computers generates a **random password**, updates the **LAPS password attribute** on the associated AD computer account and sets the password locally.

![Untitled](LAPS%20d4856ce793764ba79337b0e3288a4447/Untitled.png)

The LAPS configuration can be managed through Group Policies providing the password complexity, length, change frequency and local account name. LAPS attempts to remove **Pass The Hash / Password Spray / Credential Replay attacks** in environments where users can log on to computers without domain credentials.

LAPS, which is a Group Policy component, **automatically checks for password expiry** of accounts it is managing and rotates the expired password.

Passwords of local "administrators" are stored in special attributes (`ms-mcs-AdmPwd`) of computer objects in Active Directory and access to them is controlled by ACLs (**only Domain Admins and authorized users can access them**).

**The passwords are stored in clear text** but encrypted in transmission (Kerberos).

# How it works

![Untitled](LAPS%20d4856ce793764ba79337b0e3288a4447/Untitled%201.png)

1. The initial install provides **schema extensions** that are added to computer objects as attributes:
    1. `ms-mcs-AdmPwd`: Clear-text password
    2. `ms-mcs-AdmPwdExpirationTime`: Passwords expiration date. When is reached LAPS client is forced to reset the password.
2. Deployment of LAPS client to Computers in order to manage their local Administrator password account
3. Delegate computers self write access on their own computer accounts to update the newly added attributes
4. Delegate LAPS computer attributes to a group of users to view or force a change in LAPS passwords
5. Create a new GPO to enable & configure LAPS management.
*E.g.: in the picture, the "Support staff" group has been authorized to read the "plain text" attributes of user passwords*
    
    ![Untitled](LAPS%20d4856ce793764ba79337b0e3288a4447/Untitled%202.png)