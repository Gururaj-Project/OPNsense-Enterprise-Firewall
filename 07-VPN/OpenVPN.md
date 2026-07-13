# OpenVPN Configuration

> **OPNsense Version:** 26.x

---

# Overview

OpenVPN was implemented to provide secure remote access to the internal network. The objective was to allow authorized users to securely connect from external networks and access internal resources hosted in both the User VLAN (VLAN 10) and Server VLAN (VLAN 20).

The VPN tunnel uses SSL/TLS certificates for authentication and encryption, ensuring secure communication between remote clients and the firewall.

---

# Objectives

- Configure a secure Remote Access VPN.
- Allow authenticated users to access internal VLANs.
- Encrypt all VPN communication.
- Authenticate users using certificates and credentials.
- Provide secure access to internal applications.

---

# VPN Architecture

```
                Remote User
                     │
               Internet
                     │
             Public WAN IP
                     │
              OPNsense Firewall
                     │
             OpenVPN Tunnel
                     │
         -------------------------
         │                       │
     VLAN 10                 VLAN 20
   User Network          Server Network
```

---

# Why OpenVPN?

OpenVPN was selected because it provides:

- Secure SSL/TLS encrypted communication
- Certificate-based authentication
- Cross-platform client support
- Fine-grained access control
- Integration with OPNsense

---

# Certificate Authority (CA)

Before configuring OpenVPN, a Certificate Authority (CA) must be created.

Navigate to:

```
System
    └── Trust
            └── Authorities
```

Create a new Internal Certificate Authority.

Example:

| Option | Example |
|----------|---------|
| Method | Create Internal Certificate Authority |
| Description | OpenVPN_CA |

Save the configuration.

This Certificate Authority is later used to sign both the VPN server certificate and client certificates.

---

# Creating the Server Certificate

Navigate to:

```
System
    └── Trust
            └── Certificates
```

Create a new certificate.

Example:

| Option | Example |
|----------|---------|
| Method | Create Internal Certificate |
| Issuer | OpenVPN_CA |
| Certificate Type | Server Certificate |
| Key Type | RSA |
| Digest Algorithm | SHA256 |
| Lifetime | As per organization policy |

Save the certificate.

This certificate is used by the OpenVPN server during SSL/TLS negotiation.

---

# Configuring the OpenVPN Server

Navigate to:

```
VPN
    └── OpenVPN
            └── Instances
```

Create a new OpenVPN Server Instance.

Example configuration:

| Option | Example |
|----------|---------|
| Role | Server |
| Bind Address | WAN Public IP |
| Device Type | TUN |
| Server Certificate | OpenVPN Server Certificate |
| Local Networks | VLAN10, VLAN20 Networks |
| Authentication | Local Database |

The Local Networks field should include all internal networks that VPN users need to access.

Example:

```
192.168.10.0/24
192.168.20.0/24
```

This allows VPN clients to communicate with both the user network and server network.

---

# Creating VPN Users

Navigate to:

```
System
    └── Access
            └── Users
```

Create VPN users with usernames and passwords.

Initially, I assumed that creating only user accounts would be sufficient.

However, during testing, VPN authentication was unsuccessful because each VPN user also requires a dedicated client certificate.

---

# Creating User Certificates

Navigate to:

```
System
    └── Trust
            └── Certificates
```

Create a new certificate for each VPN user.

Example:

| Option | Example |
|----------|---------|
| Method | Create Internal Certificate |
| Issuer | OpenVPN_CA |
| Certificate Type | User Certificate |
| Common Name | Username |

Associate the certificate with the corresponding VPN user.

---

# Exporting the VPN Client Configuration

Initially, I attempted to configure the VPN client manually by importing certificates.

Although the certificates were valid, the VPN connection was unsuccessful.

After further troubleshooting, I used the OpenVPN Client Export utility.

Navigate to:

```
VPN
    └── OpenVPN
            └── Client Export
```

Select the required user and export the client configuration.

The exported configuration includes:

- CA Certificate
- User Certificate
- Private Key
- OpenVPN Configuration

Importing the exported configuration into the OpenVPN client resolved the certificate-related issues.

---

# Configuring Outbound NAT

After successfully establishing the VPN tunnel, VPN users were still unable to access external networks.

To achieve the required routing behaviour for VPN clients, additional Outbound NAT rules were configured.

Navigate to:

```
Firewall
    └── NAT
            └── Outbound
```

Configure the required NAT rules according to your VPN network design.

After applying the Outbound NAT rules, VPN users were able to access external networks successfully.

> **Note:** The exact Outbound NAT configuration depends on the VPN topology and routing requirements. This project uses Outbound NAT to achieve the desired connectivity for remote users.

---

# Configuring Firewall Rules

Navigate to:

```
Firewall
    └── Rules
            └── OpenVPN
```

Allow the required traffic for VPN users.

Example:

| Field | Value |
|----------|---------|
| Action | Pass |
| Interface | OpenVPN |
| Source | OpenVPN Net |
| Destination | Any |
| Description | Allow VPN Users |

This allows authenticated VPN users to communicate with the configured internal networks.

---

# Accessing Published Applications

After VPN connectivity was established, users were able to access the internal VLANs.

However, applications published through Destination NAT were initially inaccessible from VPN clients.

Additional NAT configuration was performed to allow VPN clients to access the published internal services successfully.

---

# Verification

The following tests were completed after configuration:

- VPN tunnel established successfully.
- User authentication successful.
- VPN client received tunnel IP.
- Access to VLAN10 successful.
- Access to VLAN20 successful.
- Internet access verified.
- Internal applications accessible.

---

# Troubleshooting

## Issue 1

VPN connection failed.

### Cause

User certificates were not created.

### Resolution

Created dedicated client certificates for each VPN user.

---

## Issue 2

Manual client configuration failed.

### Resolution

Used the OpenVPN Client Export utility to generate the complete client profile.

---

## Issue 3

VPN connected but internet was unavailable.

### Resolution

Configured the required Outbound NAT rules.

---

## Issue 4

VPN users could access VLAN gateways but were unable to access published applications.

### Resolution

Updated the NAT configuration to allow VPN clients to reach internally published services.

---

# Best Practices

- Use a dedicated Certificate Authority for VPN.
- Create separate certificates for every VPN user.
- Never share client certificates.
- Restrict VPN access using firewall rules.
- Export client profiles using the OpenVPN Client Export utility.
- Regularly revoke unused certificates.

---

# Lessons Learned

Implementing OpenVPN provided a deeper understanding of certificate-based authentication and VPN routing.

During deployment, I learned that:

- Creating a Certificate Authority alone is not sufficient.
- Every VPN user requires an individual client certificate.
- The OpenVPN Client Export utility simplifies client configuration and reduces certificate-related issues.
- VPN routing, firewall policies, and NAT configuration must work together to provide complete connectivity.
- Testing each configuration step individually makes troubleshooting much easier.

---

> **Version Note**

This documentation is based on my implementation using **OPNsense 26.x**. Menu locations and available options may change in future releases.