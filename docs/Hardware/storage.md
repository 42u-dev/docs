# Storage Hardware

## Hypervisor Shared Storage

- Dell PowerVault ME4024
    - Dual iSCSI 4x 10Gb Base-T controllers
    - 5x 1.6TB SAS SSD drives - RAID6
    - 10x 1.2TB SAS HDD - RAID10

## Backup Storage

- Dell R240
    - TrueNAS serves NFS share for secondary Veeam backup copies
- Custom built Fractal Node 304 NAS
    - TrueNAS serves NFS share for primary Veeam copies, vCenter DB backups, etc.
    - Datasets other than Veeam are replicated via ZFS replication to the R240.