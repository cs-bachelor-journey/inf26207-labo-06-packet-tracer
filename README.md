# Packet Tracer Lab: Dual Stack IPv4/IPv6 Addressing Configuration

## Overview
This lab focuses on planning and implementing a dual-stack IPv4/IPv6 network environment using Cisco Packet Tracer. You will practice VLSM subnetting, configure both IPv4 and IPv6 addressing on routers and hosts, and verify end-to-end connectivity across multiple network segments.

## Learning Objectives
- Plan IPv4 addressing using VLSM from a larger network block (`172.17.0.0/16`)
- Configure IPv4 and IPv6 addresses on router interfaces and end hosts
- Implement a simple dual-stack network architecture
- Assign correct IPv4 and IPv6 default gateways
- Verify local and inter-network connectivity using ping and web browsing tests

## Network Topology
<img width="1050" height="582" alt="image" src="https://github.com/user-attachments/assets/b10465e4-d9c8-4a53-9847-8ed0f6bd739a" />


## Prerequisites
- **Cisco Packet Tracer** (latest compatible version)
- Basic understanding of:
  - IPv4 subnetting and VLSM
  - IPv6 addressing fundamentals
  - Router interface configuration
  - Host network settings

## IPv4 Address Planning (VLSM)

### Constraints
- **Base Network:** `172.17.0.0/16`
- Use **VLSM** to efficiently allocate subnets
- Allocate subnets **from largest to smallest** host requirements
- Use the **first available subnets**
- Assign the **first usable address** to the router interface
- Assign host addresses in **ascending order**

### Subnet Planning Table
| Network | Host Requirements | CIDR Prefix | Subnet Mask | Network Address | Valid Host Range | Broadcast |
|---------|------------------|-------------|-------------|-----------------|------------------|-----------|
| Network A (S1) | *[Fill based on needs]* | | | | | |
| Network B (S2) | *[Fill based on needs]* | | | | | |
| Network C (Admin) | 1 | | | | | |

> 💡 **Tip:** Calculate the minimum prefix length needed: `2^n - 2 ≥ required_hosts`

## Addressing Table (Dual Stack)

| Device | Interface | IPv4/Prefix | IPv4 Gateway | IPv6/Prefix | IPv6 Gateway |
|--------|-----------|-------------|--------------|-------------|--------------|
| **R1** | G0/0 | *[Your VLSM result]* | N/A | `2001:DB8:1:1::1/64` | N/A |
| **R1** | G0/1 | *[Your VLSM result]* | N/A | `2001:DB8:1:2::1/64` | N/A |
| **R1** | G0/2 | *[Your VLSM result]* | N/A | `2001:DB8:1:3::1/64` | N/A |
| **R1** | Link-Local | N/A | N/A | `FE80::1` | N/A |
| **Accounting** | NIC | *[Your VLSM result]* | *[R1 G0/0 IPv4]* | `2001:DB8:1:1::4/64` | `FE80::1` |
| **Billing** | NIC | *[Your VLSM result]* | *[R1 G0/0 IPv4]* | `2001:DB8:1:1::3/64` | `FE80::1` |
| **Sales** | NIC | *[Your VLSM result]* | *[R1 G0/0 IPv4]* | `2001:DB8:1:1::2/64` | `FE80::1` |
| **CAD** | NIC | *[Your VLSM result]* | *[R1 G0/1 IPv4]* | `2001:DB8:1:2::4/64` | `FE80::1` |
| **Engineering** | NIC | *[Your VLSM result]* | *[R1 G0/1 IPv4]* | `2001:DB8:1:2::3/64` | `FE80::1` |
| **Design** | NIC | *[Your VLSM result]* | *[R1 G0/1 IPv4]* | `2001:DB8:1:2::2/64` | `FE80::1` |
| **Admin** | NIC | *[Your VLSM result]* | *[R1 G0/2 IPv4]* | `2001:DB8:1:3::2/64` | `FE80::1` |

## Configuration Steps

### Part 1: Router Configuration (R1)

#### Enable IPv6 Routing
```cisco
R1> enable
R1# configure terminal
R1(config)# ipv6 unicast-routing
```

#### Configure Interface G0/0 (Network A)
```cisco
R1(config)# interface GigabitEthernet0/0
R1(config-if)# ip address <YOUR_IPV4> <YOUR_SUBNET_MASK>
R1(config-if)# ipv6 enable
R1(config-if)# ipv6 address 2001:DB8:1:1::1/64
R1(config-if)# ipv6 address FE80::1 link-local
R1(config-if)# no shutdown
R1(config-if)# exit
```

#### Configure Interfaces G0/1 and G0/2
Repeat the above steps for `GigabitEthernet0/1` and `GigabitEthernet0/2`, using the respective IPv4 addresses from your VLSM plan and IPv6 prefixes:
- G0/1: `2001:DB8:1:2::1/64`
- G0/2: `2001:DB8:1:3::1/64`

### Part 2: Host Configuration

#### For Each Host (GUI Method in Packet Tracer)
1. Click on the device → **Desktop** tab → **IP Configuration**
2. Set **IPv4 Address** and **Subnet Mask** per your VLSM table
3. Set **Default Gateway** to the router interface IPv4 address for that segment
4. Set **IPv6 Address** per the addressing table above (e.g., `2001:DB8:1:1::2`)
5. Set **IPv6 Prefix** to `/64`
6. Set **IPv6 Default Gateway** to `FE80::1` (link-local)

> 🔁 Repeat for all hosts: Sales, Billing, Accounting, Design, Engineering, CAD, and Admin.

### Part 3: Verification & Testing

#### Test Web Connectivity
1. On a client (e.g., Sales), open **Web Browser** from Desktop tab
2. Navigate to IPv6 address of Accounting server: `http://[2001:DB8:1:1::4]`
3. Navigate to IPv6 address of CAD server: `http://[2001:DB8:1:2::4]`
4. Repeat from other clients to verify cross-segment access

#### Test ICMP Connectivity
```bash
# From Sales PC Command Prompt:
PC> ping <IPv4_of_Billing_PC>
PC> ping 2001:DB8:1:1::3          # IPv6 ping to Billing
PC> ping 2001:DB8:1:2::4          # IPv6 ping to CAD (cross-segment)
```

#### Verify Router Configuration
```cisco
R1# show ipv6 interface brief
R1# show ip interface brief
R1# show running-config | section GigabitEthernet
```

## Troubleshooting Tips
- ✅ Ensure `ipv6 unicast-routing` is enabled on R1
- ✅ Verify `no shutdown` is applied to all interfaces
- ✅ Confirm link-local address `FE80::1` is configured correctly
- ✅ Double-check that host gateways match the router interface addresses
- ✅ Use `ping` and `tracert`/`traceroute` to isolate connectivity issues

## Repository Structure (Suggested)
```
.
├── docs/
│   ├── addressing-plan.md      # VLSM calculations & final table
│   ├── topology.png            # Packet Tracer topology screenshot
│   └── verification-log.txt    # Ping/web test results
├── packet-tracer/
│   └── INF26207_DualStack_Lab.pkt  # Saved Packet Tracer file
├── scripts/
│   └── router-config.rtf       # Copy-paste ready router commands
└── README.md
```

## Deliverables Checklist
- [ ] Completed VLSM subnetting table
- [ ] Fully populated dual-stack addressing table
- [ ] Packet Tracer file (.pkt) with working configuration
- [ ] Screenshots of:
  - Router `show` command outputs
  - Successful IPv4 and IPv6 pings
  - Web browser access to servers via IPv6
- [ ] Brief reflection on challenges encountered and how dual-stack supports network transition

---

> 📝 **Note:** This lab simulates a real-world scenario where organizations gradually migrate from IPv4 to IPv6. Dual-stack deployment allows both protocols to coexist during the transition period, ensuring backward compatibility while enabling future-proofing.
