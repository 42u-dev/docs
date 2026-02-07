# Storage Architecture

## Redundancy

As mentioned in [Storage](../Hardware/storage.md), a PowerVault SAN is used to provide the compute cluster with shared storage.

The SAN's main storage pool contains 5x 1.6TB SAS SSD drives in a RAID6. This allows for 2 drives to fail before any data loss is possible.

!!! info "Reminder"

    This is a friendly reminder that RAID is **not a backup!**


In addition to regular VM backups, ZFS replication in TrueNAS is utilized to copy important datasets to the opposite system. This, along with ZFS snapshots, ensure that data exists on more than one physical device.

Important data is also uploaded to Backblaze B2 buckets for an extra offsite copy.
