# Connectivity Verification (Task 4)

All 7 PCs should reach each other — same-VLAN traffic is switched (possibly across the
SW1↔SW2 trunk), cross-VLAN traffic is routed through R1's subinterfaces.

## 1. Same-VLAN tests (switched, no routing — some cross the SW1↔SW2 trunk)

| From | To | Path |
|------|----|------|
| PC1 (10.0.0.1, SW1) | PC2 (10.0.0.2, SW1) | Local switching on SW1 |
| PC1 (10.0.0.1, SW1) | PC3 (10.0.0.3, SW2) | Switched across SW1↔SW2 trunk (both VLAN10) |
| PC3 (10.0.0.3, SW2) | PC4 (10.0.0.4, SW2) | Local switching on SW2 |
| PC6 (10.0.0.129, SW1) | PC7 (10.0.0.130, SW1) | Local switching on SW1 |

## 2. Cross-VLAN tests (routed through R1)

| From | To | Path |
|------|----|------|
| PC1 (VLAN10) | PC5 (10.0.0.65, VLAN20) | SW1 → trunk → SW2 → trunk → R1 (Gig0/0.10 → Gig0/0.20) → back |
| PC1 (VLAN10) | PC6 (10.0.0.129, VLAN30) | SW1 → trunk → SW2 → trunk → R1 (Gig0/0.10 → Gig0/0.30) → back to SW1 |
| PC5 (VLAN20) | PC6 (VLAN30) | SW2 → R1 (Gig0/0.20 → Gig0/0.30) → trunk → SW1 |
| Every PC | its own gateway (.62 / .126 / .190) | Should always succeed |

## 3. What to check if a ping fails

- **PC config** — correct IP/mask/gateway per `docs/ip-addressing-table.md`.
- **Access port VLAN** — `show vlan brief` on SW1/SW2; PC's port must be in the right VLAN.
- **Trunk allowed VLANs** — `show interfaces trunk`; VLANs 10/20/30 must show as allowed
  and *not* pruned on both Gig0/1 (SW1↔SW2) and Gig0/2 (SW2↔R1).
- **Native VLAN match** — both ends of each trunk must agree on native VLAN 999, or you'll
  see a native VLAN mismatch warning/log message.
- **R1 subinterfaces** — `show ip interface brief`; each Gig0/0.x must be up/up with the
  correct encapsulation (`show vlans` on R1) and IP address.
- **R1 parent interface** — Gig0/0 itself must be `no shutdown` even with no IP address, or
  none of the subinterfaces will come up.

## 4. Optional: confirm VLAN isolation with a broadcast ping

In Simulation Mode, ping each VLAN's broadcast address (10.0.0.63 / 10.0.0.127 / 10.0.0.191)
from a PC in that VLAN and confirm only same-VLAN devices (and R1's matching subinterface)
receive it — PCs in other VLANs should not.
