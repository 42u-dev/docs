# Networking Equipment

## Firewall

- Unifi UDM Pro Max

This is the main firewall and handles all routing.

## Data Switches

- 2x Aruba 6200F L3 1Gb 48-port switches

These switches handle redundant management and VM traffic.

## Storage Switches

- Mikrotik CRS312 - 10Gb iSCSI
- D-Link DXS-1100-10TS - 10Gb iSCSI

These switches handle redundant iSCSI connections between the compute hosts, SAN, and backup storage.

- Mikrotik CRS504 - 100Gb vSAN

This switch handles isolated vSAN traffic between each compute node.