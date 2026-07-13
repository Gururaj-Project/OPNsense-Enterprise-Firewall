# Network Design

## Overview

The network was designed using OPNsense as the central firewall and routing device. The main objective of this design was to separate user devices and server infrastructure into different network segments to improve security, simplify traffic management, and provide better control over communication between networks.

Instead of keeping all devices in a single flat network, separate VLANs were created based on device type and purpose.

---

## Network Segmentation Design

The network consists of two primary VLANs:

| VLAN | Purpose | Address Assignment |
|------|---------|-------------------|
| VLAN 10 | User network for laptops, desktops, WiFi devices, and employee systems | DHCP |
| VLAN 20 | Server network for applications, databases, Docker workloads, and infrastructure services | Static IP |

---

## VLAN 10 - User Network

VLAN 10 is dedicated for end-user devices.

This network includes:

- User laptops
- Desktop systems
- WiFi clients
- Other end-user devices

The network uses DHCP-based IP assignment because user devices frequently join and leave the network. Automatic IP assignment simplifies device management and reduces manual configuration effort.

---

## VLAN 20 - Server Network

VLAN 20 is dedicated for server infrastructure and internal services.

This network includes:

- Web applications
- Database servers
- Docker containers
- Internal infrastructure services

Static IP addressing is used for servers because applications and services require predictable IP addresses for communication, monitoring, backup, and access control.

---

## Reason for Network Separation

The main reason for separating users and servers into different VLANs was security and better traffic control.

In a flat network, user devices and servers share the same broadcast domain, which increases security risks and makes traffic management difficult.

By separating the networks:

- User devices cannot directly access servers without firewall policies.
- Server access can be controlled and monitored.
- Security rules can be applied independently for each network.
- Network troubleshooting becomes easier.
- Broadcast traffic is reduced.

All communication between VLANs is controlled through OPNsense firewall rules.

---

## High-Level Network Flow

![Network Design](images/network-diagram-light.svg)