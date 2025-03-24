# Active Directory Certificate Services

Owner: padowla
Created time: February 28, 2024 7:12 PM

**Active Directory Certificate Services (AD CS)** enables use of Public Key Infrastructure (PKI) in Active Directory forest.

AD CS helps in authenticating users and machines, encrypting and signing documents, filesystem, emails and more.
AD CS is the Server Role that allows you to build a public key infrastructure (PKI) and provide public key cryptography, digital certificates, and digital signature capabilities for your organization.

# Terminology

- **CA**: ****The certification authority that issues certificates. The server with AD CS role (DC or separate) is the CA.
- **Certificate**: Issued to a user or machine and can be used for authentication, encryption, signing etc.
- **CSR**: Certificate Signing Request made by a client to the CA to request a certificate.
- **Certificate Template**: Defines settings for a certificate. Contains information like enrollment permissions, EKUs, expiry etc.
- **EKU OIDs**: Extended Key Usages Object Identifiers. These dictate the use of a certificate template (Client/Server authentication, Smart Card Logon, Encrypting File System, SubCA etc.)

# Certificate Enrollment

![Untitled](Active%20Directory%20Certificate%20Services%20b263775168354905a04a2863262b9ac8/Untitled.png)

# Parts of certificate and templates

- **Subject**: The owner of the certificate.
- **Public Key**: Associates the Subject with a private key stored separately.
- **NotBefore and NotAfter dates**: Define the duration that the certificate is valid.
- **Serial Number**: An identifier for the certificate assigned by the CA.
- **Issuer**: Identifies who issued the certificate (commonly a CA).
- **SubjectAlternativeName**: Defines one or more alternate names that the Subject may go by. In the case of public certificates they are tied to the FQDN while in the case of Active Directory they indicate alternative user names. Can be dangerous when combined with certificates that allow domain authentication and are misconfigured like in this example:
    
    ![With this misconfiguration we can impersonate the Administrator user](Active%20Directory%20Certificate%20Services%20b263775168354905a04a2863262b9ac8/Untitled%201.png)
    
    With this misconfiguration we can impersonate the Administrator user
    
- **Extended Key Usages (EKUs)**: Object identifiers (OIDs) that describe how the certificate will be used.
    
    ![Untitled](Active%20Directory%20Certificate%20Services%20b263775168354905a04a2863262b9ac8/Untitled%202.png)
    
    Common EKU OIDs include:
    
    - Code Signing (OID 1.3.6.1.5.5.7.3.3) - The certificate is for signing executable code. Typically used in combination with **Windows Defender Application Control and AppLocker**.
    - Encrypting File System (OID 1.3.6.1.4.1.311.10.3.4) - The certificate is for encrypting file systems.
    - Secure Email (1.3.6.1.5.5.7.3.4) - The certificate is for encrypting email.
    - Client Authentication (OID 1.3.6.1.5.5.7.3.2) - The certificate is for authentication to another server (e.g., to AD).
    - Smart Card Logon (OID 1.3.6.1.4.1.311.20.2.2) - The certificate is for use in smart card authentication.
    - Server Authentication (OID 1.3.6.1.5.5.7.3.1) - The certificate is for identifying servers (e.g., HTTPS certificates).
- **Signature Algorithm**: Specifies the algorithm used to sign the certificate.
- **Signature**: The signature of the certificates body made using the issuer’s (e.g., a CA’s) private key.