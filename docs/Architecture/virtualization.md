# Virtualization Architecture

## Redundancy

In addition to the host-level redundancies (networking, power, etc.), my compute cluster has HA (high availability) and DRS (distributed resource scheduler).

Since all virtual machines are located on shared storage (either the SAN or vSAN), they can be migrated (using vMotion) between hosts if one host is resource constrained. DRS automatically balances the workload and running VMs across the hosts within the cluster.

In the event of a host failure, the virtual machines on that host are automatically brought back up on one of the other hosts within the cluster.

## EVC

Since one of my hosts has a newer generation Xeon CPU, EVC (enhanced vMotion compatibility) must be enabled in my cluster.

EVC locks all hosts in the cluster to a common set of CPU instructions/features. This allows running VMs to be moved between hosts that have different generation CPUs.

## Virtual Switching

vSphere distributed switches (vDS) are used within my cluster. There is one vDS for 1Gb, one for 10Gb, and one for 100Gb.

Management port groups are ephemeral, which removes the dependency on the vCenter appliance. This allows for easier recovery on the ESXi hosts if the vCenter appliance goes down.

Each host has two uplink ports per vDS. These uplinks map to the physical NIC ports on each host.

Each VLAN used within the cluster has its own distributed port group (DPG). DPGs apply to all hosts, so the network can be defined once and propagate to all hosts automatically.
