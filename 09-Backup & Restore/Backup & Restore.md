# Backup and Restore

> **OPNsense Version:** 26.x

---

# Overview

Regular configuration backups are an essential part of firewall administration. A backup allows the entire OPNsense configuration to be restored in the event of hardware failure, accidental configuration changes, system corruption, or migration to a new firewall.

Since all firewall settings are stored in a single configuration file, maintaining regular backups significantly reduces recovery time and simplifies disaster recovery.

---

# Why Configuration Backups Are Important

Configuration backups help to:

- Recover from hardware failures.
- Restore accidental configuration changes.
- Migrate OPNsense to new hardware or virtual machines.
- Reduce downtime during disaster recovery.
- Maintain a copy of firewall configurations before performing upgrades.

---

# Backuping Up the Configuration

Navigate to:

```
System
    └── Configuration
            └── Backups
```

Under the **Backup** section, click **Download Configuration**.

The firewall downloads an XML configuration file containing all firewall settings.

The downloaded configuration includes:

- Interface configuration
- VLAN configuration
- Firewall rules
- NAT configuration
- VPN configuration
- Gateway configuration
- DHCP configuration
- DNS configuration
- User accounts
- Certificates
- System settings

Store the backup file in a secure location.

---

# Best Practices Before Taking a Backup

Before downloading a configuration backup:

- Verify that all recent configuration changes have been saved.
- Take a backup before firmware upgrades.
- Take a backup before making major configuration changes.
- Keep multiple backup versions.
- Store backups securely.

---

# Restoring a Configuration

Navigate to:

```
System
    └── Configuration
            └── Backups
```

Under the **Restore Configuration** section:

- Browse and select the previously downloaded XML configuration file.
- Upload the configuration.
- Confirm the restore operation.

After restoring the configuration, OPNsense automatically applies the settings and may reboot depending on the restored configuration.

---

# Migrating to Another Firewall

The configuration backup can also be used to migrate an existing OPNsense deployment to another firewall.

Typical migration process:

1. Install the same or a compatible OPNsense version.
2. Complete the initial installation.
3. Access the Web GUI.
4. Restore the saved configuration file.
5. Reboot the firewall if required.
6. Verify interface assignments and connectivity.

If the new hardware uses different network interface names, interface assignments may need to be updated after the restore.

---

# Verification

After restoring the configuration, verify the following:

| Component | Verification |
|------------|--------------|
| Interfaces | ✅ Correctly Assigned |
| VLANs | ✅ Available |
| Firewall Rules | ✅ Loaded |
| NAT Rules | ✅ Working |
| VPN | ✅ Operational |
| DHCP | ✅ Assigning Addresses |
| DNS | ✅ Resolving Domains |
| Gateway Groups | ✅ Operational |
| Internet Connectivity | ✅ Verified |

---

# Best Practices

- Keep regular backups after major configuration changes.
- Store backups on a secure external location.
- Encrypt backup files if they contain sensitive information.
- Test restoration procedures periodically.
- Maintain versioned backups for rollback if required.

---

# Lessons Learned

During this project, I learned that configuration backups are one of the simplest yet most important administrative tasks.

Taking a backup before implementing major features such as VLANs, OpenVPN, NAT, or Multi-WAN makes it much easier to recover from unexpected configuration issues.

I also learned that a configuration backup significantly reduces recovery time when migrating to new hardware or restoring a failed firewall.

---

> **Version Note**

This documentation is based on my implementation using **OPNsense 26.x**. Menu locations and available options may change in future releases.