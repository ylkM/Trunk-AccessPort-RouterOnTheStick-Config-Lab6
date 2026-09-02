# IP Addressing Table

Subnet mask for all VLANs: **/26** → `255.255.255.192` (62 usable hosts each)

Gateway rule: gateway = **last usable address** in the subnet, assigned to R1's subinterface.

| VLAN | Name    | Network        | Usable range            | Broadcast   | Gateway (last usable) |
|------|---------|----------------|---------------------------|-------------|--------------------------|
| 10   | VLAN10  | 10.0.0.0/26    | 10.0.0.1 – 10.0.0.62     | 10.0.0.63   | **10.0.0.62**             |
| 20   | VLAN20  | 10.0.0.64/26   | 10.0.0.65 – 10.0.0.126   | 10.0.0.127  | **10.0.0.126**            |
| 30   | VLAN30  | 10.0.0.128/26  | 10.0.0.129 – 10.0.0.190  | 10.0.0.191  | **10.0.0.190**            |
| 999  | NATIVE  | (unused — no hosts, trunk native VLAN only) |

## PC assignments (from diagram)

| PC | Switch | Port | VLAN | IP Address | Subnet Mask     | Default Gateway |
|----|--------|------|------|------------|------------------|------------------|
| PC1 | SW1 | Fa0/1 | 10 | 10.0.0.1  | 255.255.255.192 | 10.0.0.62  |
| PC2 | SW1 | Fa0/2 | 10 | 10.0.0.2  | 255.255.255.192 | 10.0.0.62  |
| PC3 | SW2 | Fa0/3 | 10 | 10.0.0.3  | 255.255.255.192 | 10.0.0.62  |
| PC4 | SW2 | Fa0/2 | 10 | 10.0.0.4  | 255.255.255.192 | 10.0.0.62  |
| PC5 | SW2 | Fa0/1 | 20 | 10.0.0.65 | 255.255.255.192 | 10.0.0.126 |
| PC6 | SW1 | Fa0/3 | 30 | 10.0.0.129| 255.255.255.192 | 10.0.0.190 |
| PC7 | SW1 | Fa0/4 | 30 | 10.0.0.130| 255.255.255.192 | 10.0.0.190 |

## R1 subinterface addressing (router-on-a-stick)

| Subinterface   | VLAN | Encapsulation   | IP Address | Subnet Mask     |
|-----------------|------|------------------|------------|------------------|
| Gig0/0.10       | 10   | dot1Q 10         | 10.0.0.62  | 255.255.255.192 |
| Gig0/0.20       | 20   | dot1Q 20         | 10.0.0.126 | 255.255.255.192 |
| Gig0/0.30       | 30   | dot1Q 30         | 10.0.0.190 | 255.255.255.192 |
| Gig0/0 (parent) | —    | no IP, no shut   | —          | —                 |

## Broadcast addresses (optional broadcast/flood testing)

| VLAN | Broadcast address |
|------|----------------------|
| 10   | 10.0.0.63   |
| 20   | 10.0.0.127  |
| 30   | 10.0.0.191  |
