# VLAN 10 - User Network Configuration (DHCP)

## Overview

This section describes the configuration of the **User Network (VLAN 10)** in OPNsense. The objective of creating a dedicated user VLAN was to separate end-user devices from the server infrastructure, improving security, network organization, and traffic management.

All user devices such as desktops, laptops, and WiFi clients obtain their IP addresses automatically through the OPNsense DHCP server.

---

## Objectives

- Create a dedicated VLAN for end-user devices.
- Separate users from the server network.
- Configure OPNsense as the default gateway.
- Enable automatic IP address assignment using DHCP.
- Prepare the VLAN for communication through a managed switch.

---

# Creating VLAN 10

Navigate to:

```
Interfaces
    └── Devices
            └── VLAN
```

Click **+ Add** and configure the following:

| Option | Value |
|---------|-------|
| Parent Interface | LAN Interface (Example: em1 / igc1) |
| VLAN Tag | 10 |
| Priority | Default |
| Description | VLAN10_USERS |

Click **Save** and **Apply Changes**.

This creates a new VLAN interface on the selected LAN interface.

---

# Assigning the VLAN Interface

Navigate to:

```
Interfaces
    └── Assignments
```

Under **New Interface**, select the newly created VLAN interface and click **+ Add**.

The interface will appear as an available interface (for example **OPT1**).

Rename the interface to **VLAN10** for easier identification.

---

# Configuring VLAN 10 Interface

Navigate to:

```
Interfaces
    └── VLAN10
```

Configure the interface as follows:

| Option | Value |
|---------|-------|
| Enable Interface | Yes |
| Description | VLAN10 |
| IPv4 Configuration Type | Static IPv4 |
| IPv4 Address | 192.168.10.1/24 |
| IPv6 Configuration | None (Optional) |

Click **Save** and **Apply Changes**.

After assigning the IP address, the VLAN interface becomes the default gateway for all devices connected to VLAN 10.

Communication between VLAN 10 and other networks will be controlled using firewall rules, which will be configured in a later section.

---

# Configuring the DHCP Server

Since VLAN 10 is intended for end-user devices, DHCP is enabled to automatically assign IP addresses.

In OPNsense 26.x, navigate to:

```
Services
    └── Kea DHCP
            └── Kea DHCPv4
```

Select the **VLAN10** interface.

Configure the DHCP settings according to your network requirements.

Example configuration:

| Option | Example Value |
|---------|---------------|
| Interface | VLAN10 |
| Subnet | 192.168.10.0/24 |
| IP Pool | 192.168.10.100 - 192.168.10.200 |
| Gateway | 192.168.10.1 |
| DNS Server | 192.168.10.1 |
| NTP Server | 192.168.10.1 |

> **Note:** Depending on your environment, some options may already be inherited from the interface configuration. Configure them according to your network design.

### IP Address Planning

For this implementation, I intentionally configured the DHCP address pool to start from **192.168.10.100** instead of **192.168.10.2**.

The IP address range **192.168.10.2 - 192.168.10.99** is reserved for devices that require manually assigned static IP addresses. Keeping these addresses outside the DHCP pool prevents IP conflicts and provides predictable addressing for network infrastructure devices.

Examples of devices that can use these reserved IP addresses include:

- Managed Switches
- Wireless Access Points
- Network Printers
- IP Cameras
- NAS Devices
- Other Infrastructure Devices

Client devices such as laptops, desktops, and WiFi users automatically receive IP addresses from the configured DHCP pool (**192.168.10.100 - 192.168.10.200**).

> **Note:** Reserving a portion of the subnet for infrastructure devices is considered a good network design practice because it keeps static and dynamic IP addresses organized and avoids accidental address conflicts.


Save the configuration and restart or apply the DHCP service if required.

---

# Verification

After connecting a client device to VLAN 10, verify the following:

- Client receives an IP address from the configured DHCP pool.
- Client receives the correct subnet mask.
- Client receives the default gateway.
- Client receives the configured DNS server.
- Client can ping the VLAN gateway (192.168.10.1).
- Client can access the internet.
- DNS name resolution works correctly.

---

# Troubleshooting

### Issue

Clients successfully received an IP address but were unable to access the internet.

### Cause

The DHCP configuration was missing the required network information for clients.

### Resolution

Configured the following DHCP options:

- Gateway : 192.168.10.1
- DNS Server : 192.168.10.1
- NTP Server : 192.168.10.1

After renewing the DHCP lease on the client devices, internet connectivity was restored successfully.

---

# Best Practices

- Use meaningful VLAN descriptions.
- Keep DHCP pools separate from static IP ranges.
- Reserve lower IP addresses for infrastructure devices.
- Document VLAN IDs and IP addressing.
- Verify DHCP leases after every configuration change.
- Test connectivity before deploying users to the network.

---
