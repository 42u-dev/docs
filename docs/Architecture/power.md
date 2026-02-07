# Power Architecture

## Redundancy

As mentioned in [Power](../Hardware/power.md), each Eaton UPS is fed by a separate 120V 20A breaker.

Each UPS feeds a separate APC PDU in the rack.

Each piece of equipment that has redundant power supplies has one PSU hooked up to one PDU, other to the other PDU.

This allows for complete power redundancy (minus devices that only have one PSU).

## Visualization

``` mermaid
flowchart TB
    A["Utility Power"] --> n1["Main Panel"]
    n1 --> n2["Breaker 1"] & n3["Breaker 2"]
    n2 --> n4["UPS 1"]
    n3 --> n5["UPS 2"]
    n4 --> n6["PDU 1"]
    n5 --> n7["PDU 2"]
    n6 --> n8["Server PSU 1"]
    n7 --> n9["Server PSU 2"]
```
