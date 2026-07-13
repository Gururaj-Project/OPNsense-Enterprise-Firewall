# Multi-WAN Failover

> **OPNsense Version:** 26.x

---

# Overview

Multi-WAN Failover was implemented to improve internet availability and reduce service interruptions caused by ISP outages.

The organization hosts multiple internally deployed applications that must remain accessible to users and external clients. Since both Users and hosted services depend on continuous internet connectivity, relying on a single Internet Service Provider (ISP) creates a single point of failure.

To improve network availability, two independent ISP connections were configured in OPNsense using Gateway Groups with automatic failover.

---

# Objectives

- Eliminate internet downtime caused by ISP failures.
- Increase application availability.
- Maintain uninterrupted connectivity for users.
- Automatically switch internet traffic to the secondary ISP during failures.
- Restore traffic back to the primary ISP after recovery.

---

# Why Multi-WAN?

Using a single ISP introduces a single point of failure.

Typical issues include:

- ISP maintenance
- Fiber cuts
- Network congestion
- Hardware failures
- Long restoration times

These outages directly affect:

- Users productivity
- Internal business applications
- Public-facing services
- Remote VPN users

To minimize these risks, a Multi-WAN failover solution was implemented.

---

# Network Architecture

```
                 ISP 1
                   │
               WAN (Tier 1)
                   │
          ---------------------
          |                   |
          |   OPNsense        |
          |                   |
          ---------------------
                   │
            Gateway Group
                   │
               Automatic
               Failover
                   │
               WAN2 (Tier 2)
                   │
                 ISP 2
```

---

# Prerequisites

Before configuring Multi-WAN, the following requirements should be completed:

- OPNsense installation completed.
- Initial WAN configured.
- Additional physical Ethernet interface available.
- Second ISP connection available.
- Interface assigned for WAN2.
- Firewall functioning correctly with a single WAN.
