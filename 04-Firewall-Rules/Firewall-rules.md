# Firewall Rules

## Overview

After configuring the VLAN interfaces, firewall rules were created to control communication between the user network, server network, and the internet.

The objective of these firewall rules was to:

- Allow users to access the internet.
- Control communication between VLANs.
- Protect the server network from unnecessary access.
- Permit only the required traffic between network segments.

All firewall rules were configured using the OPNsense firewall and applied on the respective VLAN interfaces.

---

# Firewall Rule Processing

OPNsense processes firewall rules from **top to bottom**.

The first rule that matches the traffic is applied, and the remaining rules are ignored.

For this reason, the order of firewall rules is important. More specific rules should be placed above general rules.

---

# VLAN 10 (User Network) Rules

Navigate to:

```
Firewall
    └── Rules
            └── VLAN10
```

The following firewall rules were configured for the user network.

## Rule 1 - Allow Internet Access

| Field | Value |
|---------|---------|
| Action | Pass |
| Interface | VLAN10 |
| Source | VLAN10 Net |
| Destination | Any |
| Description | Allow internet access for users |

This rule allows client devices connected to VLAN 10 to access external networks.

---

## Rule 2 - Allow Communication to Server Network

| Field | Value |
|---------|---------|
| Action | Pass |
| Interface | VLAN10 |
| Source | VLAN10 Net |
| Destination | VLAN20 Net |
| Description | Allow users to access server network |

This rule allows user devices to communicate with servers located in VLAN 20.

---

## Recommended Security Practice

Although allowing the entire VLAN 10 network to communicate with the server VLAN is suitable for testing and small environments, a more secure approach is to allow access only from specific devices that require communication with the servers.

Example:

| Source | Destination |
|---------|-------------|
| 192.168.10.25 | 192.168.20.10 |

Instead of:

```
Source : VLAN10 Net
Destination : VLAN20 Net
```

Use:

```
Source : 192.168.10.25
Destination : 192.168.20.10
```

This follows the **Principle of Least Privilege**, allowing only the required systems to communicate with the server network.

---

# VLAN 20 (Server Network) Rules

Navigate to:

```
Firewall
    └── Rules
            └── VLAN20
```

The following firewall rules were configured for the server network.

## Rule 1 - Allow Internet Access

| Field | Value |
|---------|---------|
| Action | Pass |
| Interface | VLAN20 |
| Source | VLAN20 Net |
| Destination | Any |
| Description | Allow server internet access |

This rule allows servers to download software updates, install packages, synchronize time, and access external services when required.

---

## Rule 2 - Allow Communication to User Network

| Field | Value |
|---------|---------|
| Action | Pass |
| Interface | VLAN20 |
| Source | VLAN20 Net |
| Destination | VLAN10 Net |
| Description | Allow communication with user network |

This rule allows communication from servers to the user network when required.

Depending on your environment, this rule may not always be necessary. It should be enabled only if server applications need to initiate communication with client devices.

---

# Inter-VLAN Communication

Communication between VLANs is controlled entirely by OPNsense firewall rules.

Without explicit firewall rules, communication between VLANs is restricted according to the configured security policy.

By defining firewall rules on both VLAN interfaces, communication can be permitted only for the required traffic.

---

# Verification

The following tests were performed after configuring the firewall rules:

- Verified internet access from VLAN 10.
- Verified internet access from VLAN 20.
- Verified communication between VLAN 10 and VLAN 20.
- Verified access to internal applications hosted in the server network.
- Confirmed that traffic flowed only through the configured firewall policies.

---

# Best Practices

- Follow the Principle of Least Privilege.
- Allow only the traffic required by the application.
- Avoid using **Any → Any** rules whenever possible.
- Use descriptive names for firewall rules.
- Place more specific rules above general rules.
- Review and remove unused firewall rules regularly.

---

# Lessons Learned

Initially, communication between VLANs was not possible until the appropriate firewall rules were configured on the respective interfaces.

After defining the required firewall policies, users in VLAN 10 were able to access the necessary services hosted in VLAN 20 while maintaining logical separation between both networks.

For better security, I recommend allowing communication only from specific client IP addresses instead of permitting the entire user network to access all servers whenever possible.

---

> **Version Note**

This documentation is based on my implementation using **OPNsense 26.x**. Menu locations and available options may change in future releases.