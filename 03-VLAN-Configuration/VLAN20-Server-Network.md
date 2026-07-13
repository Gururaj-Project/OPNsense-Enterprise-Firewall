# VLAN 20 - Server Network Configuration (Static IP)

## Overview

This section describes the configuration of **VLAN 20**, which is dedicated to server infrastructure. Unlike the user network, this VLAN does not use DHCP for automatic IP address assignment. All servers are configured with manually assigned static IP addresses to provide consistent network addressing and simplify infrastructure management.

---

## Objectives

- Create a dedicated VLAN for server infrastructure.
- Isolate servers from end-user devices.
- Configure OPNsense as the default gateway.
- Use static IP addressing for all servers.
- Prepare the network for firewall policies and NAT configuration.

---

## Creating VLAN 20

The VLAN creation process is similar to the user VLAN configuration. The only difference is the VLAN Tag and the intended purpose of the network.

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
| VLAN Tag | 20 |
| Priority | Default |
| Description | VLAN20_SERVERS |

Click **Save** and **Apply Changes**.

> **Important:** Every VLAN must have a unique VLAN Tag. The same VLAN Tag (**20**) must also be configured on the connected managed or smart switch. If the switch VLAN configuration does not match the OPNsense configuration, devices connected to this VLAN will not communicate correctly.

---

## Assigning the VLAN Interface

Navigate to:

```
Interfaces
    └── Assignments
```

Select the newly created VLAN interface and click **+ Add**.

Rename the interface to **VLAN20**.

---

## Configuring the VLAN Interface

Navigate to:

```
Interfaces
    └── VLAN20
```

Configure:

| Option | Value |
|---------|-------|
| Enable Interface | Yes |
| Description | VLAN20 |
| IPv4 Configuration | Static IPv4 |
| IPv4 Address | 192.168.20.1/24 |
| IPv6 | None (Optional) |

Click **Save** and **Apply Changes**.

The configured IP address becomes the default gateway for all devices connected to VLAN 20.

Unlike VLAN 10, **DHCP is intentionally not configured** for this network because all servers use manually assigned static IP addresses.

---


## Why Static IP Addressing?

Unlike end-user devices, servers require predictable and consistent IP addresses because multiple services and infrastructure components depend on them.

For this reason, DHCP was intentionally **not enabled** on VLAN 20.

Each server is assigned a manually configured static IP address.

This approach provides several advantages:

- Predictable IP addresses for critical services.
- Easier server documentation and inventory management.
- Simplifies firewall rule creation.
- Simplifies NAT and Port Forwarding configuration.
- Makes troubleshooting faster.
- Prevents unexpected IP address changes after reboot or lease renewal.

For example:

| Server | Static IP |
|---------|-----------|
| Application Server | 192.168.20.10 |
| Database Server | 192.168.20.20 |
| Docker Host | 192.168.20.30 |
| Backup Server | 192.168.20.40 |

Maintaining a documented IP addressing plan makes it easier to identify servers and reduces the risk of configuration mistakes.

---

## Why DHCP Was Not Enabled

Since this VLAN hosts production services and infrastructure components, automatic IP address assignment was not required.

Static addressing ensures that every server always uses the same IP address. This is particularly important for services such as:

- Web Applications
- Database Servers
- Docker Hosts
- Monitoring Servers
- Authentication Services
- Backup Servers

Many infrastructure services reference server IP addresses directly. Keeping these addresses fixed simplifies administration and improves overall reliability.

---

## Future Integration

Using static IP addresses also simplifies future configurations such as:

- One-to-One NAT
- Port Forwarding
- Reverse Proxy
- Firewall Policies
- Monitoring Systems
- Backup Solutions
- DNS Records

Since each server always uses the same IP address, these configurations remain consistent over time and require minimal maintenance.