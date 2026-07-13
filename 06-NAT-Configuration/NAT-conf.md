# NAT (Network Address Translation)

---

# Overview

Network Address Translation (NAT) is a networking technique that translates one IP address into another while packets pass through the firewall.

In this project, NAT is used to allow communication between the internal private network and external public networks. It also enables selected internal services hosted in VLAN 20 to be securely accessed from the internet.

---

# Why NAT is Required

Private IP addresses such as:

- 192.168.x.x
- 172.16.x.x - 172.31.x.x
- 10.x.x.x

cannot be accessed directly from the internet.

OPNsense performs Network Address Translation by translating private addresses into public addresses before traffic leaves the firewall.

Similarly, incoming traffic destined for a public IP address can be translated and forwarded to an internal server.

Without NAT:

- Internal devices cannot communicate with external networks using private IP addresses.
- Internal applications cannot be published to the internet.

---

# Types of NAT

OPNsense supports multiple NAT methods depending on the network requirement.

## Outbound NAT

Outbound NAT translates private IP addresses into a public IP address while traffic leaves the firewall.

Example:

```
Laptop
192.168.10.100

↓

OPNsense

↓

Public IP

↓

Internet
```

This allows internal devices to access websites and external services.

For this project, the default Automatic Outbound NAT mode was sufficient during the initial deployment. No manual outbound NAT rules were required before implementing VPN and Multi-WAN.

---

## Port Forwarding (Destination NAT)

Port Forwarding is a form of Destination NAT (DNAT).

It translates traffic arriving at a public IP address and forwards it to a private server inside the network.

Example:

```
Internet

↓

Public IP : 203.x.x.x

↓

OPNsense

↓

192.168.20.10

↓

Web Application
```

This method was used in this project to publish internal applications hosted in VLAN 20.

---

## One-to-One NAT

One-to-One NAT creates a permanent mapping between one public IP address and one private IP address.

Example:

```
Public IP

↓

192.168.20.20
```

This method is commonly used when a server requires a dedicated public IP address.

This NAT type was not used in this implementation.

---

# NAT Design

The applications deployed in VLAN 20 were required to be accessible from the internet.

Instead of exposing the entire server network, only the required services were published using Destination NAT (Port Forwarding).

Traffic first reaches the OPNsense WAN interface and is then translated and forwarded to the appropriate internal server.

---

# Creating a Virtual IP

Since multiple public services were hosted behind the firewall, an additional public IP address was configured as a **Virtual IP** on the WAN interface.

Navigate to:

```
Interfacce
    └── Virtual IPs
```

Create a new Virtual IP.

Example:

| Option | Example |
|---------|----------|
| Interface | WAN |
| Type | IP Alias |
| Address | Public IP Address |
| Description | Public IP for Web Applications |

After applying the configuration, the Virtual IP becomes available for NAT rules.

---

# Configuring Port Forwarding (Destination NAT)

Navigate to:

```
Firewall
    └── NAT
            └── Port Forward
```

Create a new Port Forward rule.

Example configuration:

| Option | Example |
|----------|---------|
| Interface | WAN |
| Protocol | TCP |
| Source | Any |
| Destination | Virtual Public IP |
| Destination Port | 443 |
| Redirect Target IP | 192.168.20.10 |
| Redirect Target Port | 443 |
| Description | Publish Internal Web Application |

Save the rule and apply the changes.

This rule translates incoming HTTPS traffic from the public IP address to the internal application server located in VLAN 20.

---

# WAN Firewall Rule

After creating the Port Forward rule, allow the required traffic on the WAN interface.

Navigate to:

```
Firewall
    └── Rules
            └── WAN
```

Example:

| Field | Value |
|---------|---------|
| Action | Pass |
| Interface | WAN |
| Protocol | TCP |
| Source | Any |
| Destination | VLAN20 Server IP |
| Destination Port | 443 |
| Description | Allow HTTPS to Internal Web Server |

Only the required ports should be allowed.

Avoid exposing unnecessary services to the internet.

---

# Traffic Flow

```
Internet
      │
      ▼
Public IP (Virtual IP)
      │
      ▼
OPNsense Firewall
      │
      ▼
Destination NAT (Port Forward)
      │
      ▼
192.168.20.10
      │
      ▼
Internal Web Application
```

---
# NAT Reflection (Hairpin NAT)

After publishing internal applications using Destination NAT, external users were able to access the applications successfully using the public domain name.

However, during testing, devices located inside **VLAN 10** were unable to access the same application using the public domain name. Since the domain resolved to the firewall's public IP address, the connection was not translated back to the internal server.

To resolve this issue, **NAT Reflection (Hairpin NAT)** was enabled.

Navigate to:

```
Firewall
    └── Settings
            └── Advanced
                    └── Network Address Translation
```

Enable the following options:

- Reflection for destination NAT
- Reflection for 1:1 NAT
- Automatic outbound NAT for Reflection

These settings allow internal clients to access services hosted inside the network by using the same public domain name that external users use.

As a result:

- External users access the application using the public IP address.
- Internal users also access the application using the same domain name without requiring separate URLs.

---

# NAT Reflection vs Split DNS

There are two common approaches for allowing internal users to access internally hosted applications using a public domain name.

| NAT Reflection (Hairpin NAT) | Split DNS |
|------------------------------|-----------|
| Firewall translates traffic back to the internal server. | Internal DNS resolves the domain directly to the private IP address. |
| Simple to configure. | More scalable for larger environments. |
| Suitable for small and medium-sized deployments. | Commonly used in enterprise environments. |
| Requires additional NAT processing. | No additional NAT processing is required. |

For this implementation, **NAT Reflection** was chosen because it provided a simple solution without requiring additional internal DNS records.

> **Note:** Split DNS is another widely adopted solution for this scenario and will be discussed in detail in the **DNS Configuration** chapter.

---


# Verification

After completing the NAT configuration, verify the following:

- Public IP is reachable.
- Required port is open.
- Internal application is accessible.
- Unused ports remain blocked.
- Traffic is successfully translated to the internal server.

---

# Best Practices

- Publish only the required services.
- Use separate Virtual IP addresses when hosting multiple applications.
- Restrict exposed ports to only those required by the application.
- Keep servers inside a dedicated server VLAN.
- Protect published applications using firewall rules.
- Regularly review NAT rules and remove unused entries.

---

# Lessons Learned

Using Destination NAT made it possible to securely publish internal applications without exposing the entire server network.

Keeping all application servers inside VLAN 20 simplified NAT configuration because every published service followed the same network design.

I also found that using Virtual IP addresses provides a clean and scalable approach when multiple public-facing applications need to be hosted behind a single OPNsense firewall.

While testing published applications, I observed that external users could access the application successfully using the public domain name, but internal users located in VLAN 10 could not.

The issue occurred because internal traffic was resolving to the firewall's public IP address and was not being translated back to the internal server.

Enabling NAT Reflection (Hairpin NAT) resolved the problem and allowed both internal and external users to access the application using the same domain name.

During this implementation, I also learned that **Split DNS** is another widely used solution, especially in enterprise environments. Split DNS avoids additional NAT processing by resolving internal domain names directly to private IP addresses. Although NAT Reflection was the best fit for this deployment, understanding both approaches helps in choosing the right solution for different environments.

---

> **Version Note**

This documentation is based on my implementation using **OPNsense 26.x**. Menu locations and available options may change in future releases.