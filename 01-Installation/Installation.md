# OPNsense Installation
## Overview

This project uses OPNsense 26.x, an open-source firewall and routing platform based on FreeBSD. The firewall was deployed to provide secure network access, VLAN segmentation, VPN connectivity, Multi-WAN failover, NAT, and centralized traffic management for a small office and home lab environment.

The installation was performed on a dedicated machine and later configured to support multiple internal networks, remote VPN access, and internet failover using two different ISPs.

## Why I Chose OPNsense

I selected OPNsense because it is a free and open-source firewall that provides many enterprise-grade networking and security features without requiring expensive licensing. It offers a modern web interface, regular security updates, and an active community.

The platform is suitable for home labs, educational environments, and small to medium-sized organizations that require reliable firewall, routing, VPN, and network security features.

### Some of the major features that influenced my decision include:
- VLAN support for network segmentation
- Stateful firewall
- Multi-WAN and failover support
- OpenVPN and WireGuard support
- DHCP and DNS services
- NAT and Port Forwarding
- Traffic monitoring and reporting
- High extensibility through plugins

## Hardware Requirements
One of the advantages of OPNsense is that it can run on modest hardware while still providing excellent performance for small and medium-sized environments.

### Minimum Recommended
| Component | Specification |
|-----------|---------------|
| CPU | Dual Core (64-bit) |
| RAM | 4 GB |
| Storage | 20 GB SSD |
| Network Interfaces | Minimum 2 Ethernet Ports |

For my implementation, I used a dedicated system with at least two physical network interfaces:

- One interface for WAN connectivity
- One interface for the internal LAN

Since this project also implements Multi-WAN failover, additional network interfaces were required to connect the second ISP.
### Recommended for Multi-WAN Deployment

| Component | Specification |
|-----------|---------------|
| CPU | Quad Core 64-bit processor |
| RAM | 8 GB or more |
| Storage | SSD storage |
| Network Interfaces | 3 or more Ethernet ports depending on ISP requirements |

## Downloading OPNsense

The latest OPNsense installer image can be downloaded from the official OPNsense website.

The downloaded image is provided as a compressed .bz2 archive. After downloading, extract the archive to obtain the bootable ISO image.

The ISO can then be written to a USB drive using tools such as Balena Etcher, Rufus, or the Linux dd command.

## Installing OPNsense
- Boot the system using the prepared USB installation media.
- Select the installer from the boot menu.
- Log in using the default installer credentials:
``` 
Username : installer 
Password : opnsense 
```
- Follow the installation wizard.
- Select the target disk for installation.
- Choose ZFS as the filesystem for improved data integrity and snapshot support.
- Continue with the default installation options unless your environment requires custom  settings.
- Complete the installation and reboot the system.
- Remove the installation media after the reboot.

After installation, the firewall boots into the OPNsense console menu.

## Initial Login

After the installation is complete, connect a system to the LAN interface.

By default, OPNsense assigns the LAN interface the IP address:

``` 192.168.1.1 ```

Open a web browser and navigate to:

``` https://192.168.1.1 ```

Login using the default administrator credentials:
```
 Username : root
 Password : opnsense
```
##### Important: 
Change the default administrator password immediately after the first login to prevent unauthorized access.

## Interface Assignment

Interfaces can be assigned using either the console menu or the web interface.

### Using the Console
- Log in as root.
- From the console menu, select:
``` 
1) Assign Interfaces
 ```
- OPNsense automatically detects all available network adapters.

Example:
```
igc0
igc1
re0
re1
em0
vtnet0
```
The interface names depend on the hardware or virtualization platform being used.

- Assign the interface connected to the ISP as WAN.
- Assign the interface connected to the internal network as LAN.
- Press Enter when there are no additional interfaces to assign.
- Confirm the changes.

The firewall reloads the interface configuration and displays the updated interface assignments.

### Using the Web Interface

#### Navigate to:

``` Interfaces → Assignments ```

If additional network interfaces are detected but not yet assigned, they can be added from this page and configured as WAN, LAN, or OPT interfaces based on the network design.

## Initial System Configuration

After the initial login, complete the basic system configuration before deploying the firewall into the network.

### Recommended tasks include:

- Change the administrator password.
- Configure the hostname.
- Set the correct timezone.
- Configure DNS servers if required.
- Verify interface assignments.
- Review the default firewall configuration.

## Updating the Firewall
Keeping the firewall updated ensures that the latest security patches, bug fixes, and new features are available.

#### Navigate to:

``` System → Firmware → Updates ```

Check for available updates and install them before continuing with the remaining configuration.

## Backing Up the Configuration
Once the initial installation and configuration are complete, create a backup of the firewall configuration.

#### Navigate to:

``` System → Configuration → Backups ```

Download the configuration file and store it securely. Regular backups make it easier to recover the firewall in case of hardware failure or accidental configuration changes.

## Best Practices
- Change all default credentials immediately.
- Keep the firewall updated with the latest firmware.
- Use strong administrator passwords.
- Take configuration backups after major changes.
- Document interface assignments before configuring VLANs and firewall rules.
- Restrict administrative access to trusted networks only.

## Verification
#### The installation can be verified by confirming that: 

- The firewall boots successfully.
- WAN and LAN interfaces are correctly assigned.
- The web interface is accessible.
- Internet connectivity is available through the WAN interface.
- The administrator can log in successfully.
- The latest firmware updates are installed.