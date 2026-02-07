# Networking Architecture

## Redundancy

As mentioned in [Networking](../Hardware/networking.md), two Aruba switches are utilized for management and data. Each switch has an uplink to the UDM firewall, orchestrated via Aruba SmartLink.

Each virtualization host has two management connections: one connection to each switch.

The 10Gb network is architected with the same redundancy. Each virtualization host has two 10Gb connections: one to each 10Gb switch.

The PowerVault SAN also has its iSCSI connections hooked up redundantly. This allows for iSCSI multipathing in addition to the redundancy.

## Segmentation

My network consists of several VLANs to handle traffic segmentation.

All management (iDRAC, ESXi, etc.) is in its own VLAN.

All VM traffic is separated into 3 categories: standard, production, and lab.

This allows for critical production workloads to be isolated from the other VLANs.

This also allows for IoT and guest WiFi traffic segmentation and only allows communication where necessary (example: Home Assistant -> IoT device).

The zero-trust principle is followed. All traffic is blocked between VLANs by default (implicit deny) unless explicitly allowed.

## Subnetting

Each data subnet is a /24 and uses a specific class.

Each 10Gb subnet is a /24 and uses a specific class.

Each WireGuard tunnel network is a /29 and uses a specific class.

Each subnet has its VLAN ID present in the network address.

Together, these practices keep things consistent across networks and allow for immediate identification of "what a subent is used for."

??? example

    - IoT VLAN: `20`
    - IoT Subnet: `192.168.20.0/24`
    - Management VLAN: `10`
    - Management Subnet: `192.168.10.0/24`
    - 10Gb Subnet: `10.10.10.0/24`
    - 100Gb Subnet: `10.10.100.0/24`
    - WireGuard Tunnel Subnet: `172.22.22.0/29`

## Visualization

``` mermaid
flowchart TB
    A["ISP ONT"] --> n1["UDM Firewall"]
    n1 --> n2["Aruba Switch 1"] & n3["Aruba Switch 2"]
    n2 --> n4["Server 1 Port 1"] & n6["Server 2 Port 1"] & n8["Server 3 Port 1"]
    n3 --> n5["Server 1 Port 2"] & n7["Server 2 Port 2"] & n9["Server 3 Port 2"]
```
