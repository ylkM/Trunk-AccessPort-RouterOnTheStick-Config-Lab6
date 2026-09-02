# Port-by-Port Connection Map

## SW1 access ports → end devices

| SW1 Port | Connected to | VLAN |
|----------|---------------|------|
| Fa0/1 | PC1 (10.0.0.1)   | 10 |
| Fa0/2 | PC2 (10.0.0.2)   | 10 |
| Fa0/3 | PC6 (10.0.0.129) | 30 |
| Fa0/4 | PC7 (10.0.0.130) | 30 |

## SW1 trunk port

| SW1 Port | Connects to | Mode  | Allowed VLANs | Native VLAN |
|----------|-------------|-------|----------------|--------------|
| Gig0/1   | SW2 Gig0/1  | trunk | 10, 20, 30     | 1001 (unused) |

## SW2 access ports → end devices

| SW2 Port | Connected to | VLAN |
|----------|---------------|------|
| Fa0/3 | PC3 (10.0.0.3)  | 10 |
| Fa0/2 | PC4 (10.0.0.4)  | 10 |
| Fa0/1 | PC5 (10.0.0.65) | 20 |

## SW2 trunk ports

| SW2 Port | Connects to    | Mode  | Allowed VLANs | Native VLAN |
|----------|----------------|-------|----------------|--------------|
| Gig0/1   | SW1 Gig0/1     | trunk | 10, 20, 30     | 1001 (unused) |
| Gig0/2   | R1 Gig0/0      | trunk | 10, 20, 30     | 1001 (unused) |

## R1 (router-on-a-stick)

| R1 Interface | Connects to | Role |
|---------------|--------------|------|
| Gig0/0        | SW2 Gig0/2   | Trunk parent — carries all 3 VLANs via subinterfaces |
| Gig0/0.10     | (logical)    | Gateway for VLAN 10 |
| Gig0/0.20     | (logical)    | Gateway for VLAN 20 |
| Gig0/0.30     | (logical)    | Gateway for VLAN 30 |

## Why VLAN 10 exists on both switches

VLAN 10 has devices on **both** SW1 (PC1, PC2) and SW2 (PC3, PC4) — that's exactly what the
SW1↔SW2 trunk is for: it lets a single VLAN/broadcast domain span multiple physical
switches. Traffic between PC1 (SW1) and PC3 (SW2) stays inside VLAN 10 and is switched, not
routed, even though it crosses the trunk.

## Why the trunk restricts allowed VLANs + uses an unused native VLAN

- Only VLANs 10/20/30 actually need to cross either trunk — allowing "all" VLANs is
  unnecessary attack surface and wastes broadcast/flood traffic across the link.
- VLAN 999 is created but has no access ports anywhere — using it as the native VLAN means
  any untagged or double-tagged frame lands in a dead-end VLAN instead of a real one
  (mitigates VLAN hopping via native VLAN mismatch/spoofing).
