[Top](../../README.en.md) > [User Guide](README.md) > Configuration Guides > RIB & Routing

# RIB & Routing

**Language:** **English** | [日本語](../ja/routing.md)

Commands for managing RIB (Routing Information Base) and system resource information.

## Command List

- [`show_rib`](#show_rib) - Display RIB information
- [`show_rib_route`](#show_rib_route) - Display routing table
- [`show_rib_nexthop_group`](#show_rib_nexthop_group) - Display nexthop group
- [`show_rib_nexthop_pool`](#show_rib_nexthop_pool) - Display nexthop information pool
- [`set_netlink_hook`](#set_netlink_hook) - Set netlink hook
- [`show_netlink_hook`](#show_netlink_hook) - Display netlink hook

## Command Details

### show_rib

Display RIB Information
```
show rib
```

Display RIB (Routing Information Base) information.

**Examples:**
```bash
show rib
```

This command displays the following information:
- RIB version and memory pointer
- Virtual switch configurations and VLAN assignments
- DPDK port status and queue configurations
- lcore-to-port queue assignments
- Neighbor tables for L2/L3 forwarding

### show_rib_route

Display Routing Table
```
show rib (ipv4|ipv6) route
```

Display IPv4 or IPv6 routing table.

**Parameters:**
- `ipv4` - Display IPv4 routing table
- `ipv6` - Display IPv6 routing table

**Examples:**
```bash
show rib ipv4 route
show rib ipv6 route
```

### show_rib_nexthop_group

Display Nexthop Group
```
show rib nexthop-group
```

Display the nexthop group table. `show rib nexthop-group` shows the logical structure of the groups.

**Display Fields:**

| Field | Description |
|-------|-------------|
| ID | sdplane internal nexthop group ID |
| KernelID | Linux kernel nhid (shown only for separated type) |
| RefCnt | Number of route entries or other groups referencing this group |
| Nexthop Count | Number of member nexthops |
| Depends | sdplane IDs of child groups referenced by this group |
| Dependents | sdplane IDs of parent groups referencing this group |

**Examples:**
```bash
show rib nexthop-group
```

**Output Example:**
```
ID: 13
KernelID: 10
RefCnt: 2
Nexthop Count: 1
Dependents: (15)
via 172.15.0.10, rif1 (14), IPv4, weight 0

ID: 15
KernelID: 100
RefCnt: 1
Nexthop Count: 2
Depends: (13) (14)
via 172.15.0.10, rif1 (14), IPv4, weight 1
via 172.16.0.10, rif2 (16), IPv4, weight 1
```

### show_rib_nexthop_pool

Display Nexthop Information Pool
```
show rib nexthop-pool
```

Display the nexthop information pool. `show rib nexthop-pool` shows the actual nexthop information (IP addresses and interfaces).

**Display Fields:**

| Field | Description |
|-------|-------------|
| IDX | Index within the pool |
| RefCnt | Number of nexthop group members referencing this entry |
| Family | IPv4 / IPv6 |
| Type | gateway (via gateway) or connected (directly connected) |
| Gateway | Gateway IP address |
| Interface | Output interface name and interface number |

**Examples:**
```bash
show rib nexthop-pool
```

**Output Example:**
```
IDX    RefCnt  Family  Type     Gateway         Interface
--------------------------------------------------------------------------------
27     2       IPv4    gateway  172.15.0.10     rif1 (14)
29     2       IPv4    gateway  172.16.0.10     rif2 (16)
```

## Configuration Commands

For virtual switch related configuration commands, see [Virtual Switch (vswitch)](vswitch.md).

For FDB and FIB command details, see [FDB (Forwarding Database)](fdb.md).

### set_netlink_hook

Set Netlink Hook
```
set netlink-hook <0-3> (ipv4|ipv6) ifaddr (new|del) ifname <WORD> argv-list <0-7>
```

Monitor Linux kernel network events (IP address additions/deletions) and configure hooks that execute command lists when events occur.

**Parameters:**
- `<0-3>` - Hook index (up to 4)
- `ipv4|ipv6` - Address family
- `new|del` - Event type (address addition / deletion)
- `<WORD>` - Interface name to monitor
- `<0-7>` - argv-list index to execute

**Examples:**
```bash
# Execute argv-list 0 when an IPv4 address is added to rif1
set netlink-hook 0 ipv4 ifaddr new ifname rif1 argv-list 0
```

### show_netlink_hook

Display Netlink Hook
```
show netlink-hook (|<0-3>)
```

Display configured netlink hook information.

**Parameters:**
- `<0-3>` - Specify a particular hook index (omit to display all)

**Examples:**
```bash
show netlink-hook
show netlink-hook 0
```

## Static Route Configuration

sdplane imports routes configured in the Linux kernel via netlink. Static routes set by iproute2 or FRRouting can be used.

### Static Route Configuration with iproute2

sdplane supports the **non-separated type** (traditional method) where Route Objects and Nexthop Objects are not separated, and the **separated type** where both are separated using the `ip nexthop` command. Multipath routes using Nexthop Groups are also supported.

#### 1) Non-separated Type (Traditional Method)

```bash
# Single path
ip route add 10.10.10.0/24 via 172.15.0.10 dev rif1

# Multipath
ip route add 10.20.20.0/24 \
  nexthop via 172.15.0.10 dev rif1 \
  nexthop via 172.16.0.10 dev rif2
```

#### 2) Separated Type (Route Object / Nexthop Object Separation)

sdplane manages Linux kernel nhids by mapping them to internal IDs.

```bash
# Single path
ip nexthop add id 10 via 172.15.0.10 dev rif1
ip route add 10.30.30.0/24 nhid 10

# Multipath (Nexthop Group)
ip nexthop add id 10 via 172.15.0.10 dev rif1
ip nexthop add id 11 via 172.16.0.10 dev rif2
ip nexthop add id 100 group 10/11
ip route add 10.40.40.0/24 nhid 100
```

#### Route Verification

The following is an example output when single-path and multipath routes are configured using the separated type.

```
console> show rib ipv4 route
Destination      Nexthop                       Interface
[snip]
[2] 10.30.30.0/24    172.15.0.10    13    rif1 (14)
[3] 10.40.40.0/24    172.15.0.10    15    rif1 (14)
                      172.16.0.10          rif2 (16)
```

### Static Route Configuration with FRRouting

When static routes are configured via FRR's `vtysh`, they are injected into the kernel via staticd → zebra, and sdplane imports them via netlink.

**Verified Version:** FRR 10.5.2

```bash
vtysh
router# configure terminal

# Single path
router(config)# ip route 10.10.10.0/24 172.15.0.10

# Multipath
router(config)# ip route 10.20.20.0/24 172.15.0.10
router(config)# ip route 10.20.20.0/24 172.16.0.10
```

#### Route Verification

```bash
# FRR side
router# show ip route static
router# show nexthop-group rib

# sdplane side
console> show rib ipv4 route
console> show rib nexthop-group
console> show rib nexthop-pool
```

## Dynamic Routing (FRRouting / Bird Integration)

sdplane passes control packets (ARP/ND/OSPF, etc.) to router interfaces (TAP devices) and can perform dynamic routing in cooperation with FRRouting or Bird.

### Control Packet Handling

The router processing threads (`router` / `srv6-router`) classify the following as control packets and send them to router interfaces (TAP devices):

- ARP
- IPv6 NS/NA (Neighbor Solicitation / Neighbor Advertisement)
- OSPF (IPv4/IPv6)
- Packets whose destination matches the IP/MAC of the local router interface

Neighbor resolution information (ARP/ND) is reflected in the neighbor table via internal messages.

## SRv6

sdplane implements SRv6 (Segment Routing over IPv6) Transit Node functionality.

### Supported Features

- Processes SRH type 4 (`RTE_IPV6_SRCRT_TYPE_4`)
- End-equivalent basic operation (segments_left decrement + next SID reflection to IPv6 destination)
- Up to 16 local-sids (0-15) configurable
- Used with the `srv6-router` worker type

For SRv6 command details, see [SRv6](srv6.md).

## RIB Overview

### What is RIB
RIB (Routing Information Base) is a centralized database that stores system resource and networking information. In sdplane, it manages the following information:

- **Virtual Switch Configuration** - VLAN switching and port assignments
- **DPDK Port Information** - Link status, queue configuration, and capabilities
- **lcore Queue Configuration** - Packet processing assignments per CPU core
- **Neighbor Tables** - L2/L3 forwarding database entries

### RIB Structure
The RIB consists of two main structures:

```c
struct rib {
    struct rib_info *rib_info;  // Pointer to actual data
};

struct rib_info {
    uint32_t ver;                                    // Version number
    uint8_t vswitch_size;                           // Number of virtual switches
    uint8_t port_size;                              // Number of DPDK ports
    uint8_t lcore_size;                             // Number of lcores
    struct vswitch_conf vswitch[MAX_VSWITCH];       // Virtual switch config
    struct vswitch_link vswitch_link[MAX_VSWITCH_LINK]; // VLAN port links
    struct port_conf port[MAX_ETH_PORTS];           // DPDK port config
    struct lcore_qconf lcore_qconf[RTE_MAX_LCORE];  // lcore queue assignments
    struct neigh_table neigh_tables[NEIGH_NR_TABLES]; // Neighbor/forwarding tables
};
```

## Reading RIB Information

### Basic Display Items
- **Destination** - Destination network address
- **Netmask** - Netmask
- **Gateway** - Gateway (next hop)
- **Interface** - Output interface
- **Metric** - Route metric value
- **Status** - Route status

### Route Status
- **Active** - Active route
- **Inactive** - Inactive route
- **Pending** - Route being configured
- **Invalid** - Invalid route

## Usage Examples

### Basic Usage
```bash
# Display RIB information
show rib
```

### Output Example Interpretation
```
rib information version: 21 (0x55555dd42010)
vswitches:
dpdk ports:
  dpdk_port[0]:
    link: speed=1000Mbps duplex=full autoneg=on status=up
    nb_rxd=1024 nb_txd=1024
    queues: nrxq=1 ntxq=4
  dpdk_port[1]:
    link: speed=0Mbps duplex=half autoneg=on status=down
    nb_rxd=1024 nb_txd=1024
    queues: nrxq=1 ntxq=4
  dpdk_port[2]:
    link: speed=0Mbps duplex=half autoneg=off status=down
    nb_rxd=1024 nb_txd=1024
    queues: nrxq=1 ntxq=4
lcores:
  lcore[0]: nrxq=0
  lcore[1]: nrxq=1
    rxq[0]: dpdk_port[0], queue_id=0
  lcore[2]: nrxq=1
    rxq[0]: dpdk_port[1], queue_id=0
  lcore[3]: nrxq=1
    rxq[0]: dpdk_port[2], queue_id=0
  lcore[4]: nrxq=0
  lcore[5]: nrxq=0
  lcore[6]: nrxq=0
  lcore[7]: nrxq=0
```

In this example:
- RIB version 21 shows current system state
- DPDK port 0 is active (up) with 1Gbps link speed
- DPDK ports 1 and 2 are inactive (down) with no link
- lcore 1, 2, 3 are assigned to handle packets from ports 0, 1, 2 respectively
- Each port uses 1 RX queue and 4 TX queues
- RX/TX descriptor rings are configured with 1024 entries each

## RIB Management

### Automatic Updates
RIB is automatically updated at the following times:
- Interface status changes
- Network configuration changes
- Routing protocol information received

### Manual Updates
To manually check RIB information:
```bash
show rib
```

## Troubleshooting

### When Routing Is Not Working Properly
1. Check RIB information
```bash
show rib
```

2. Check interface status
```bash
show port
```

3. Check worker status
```bash
show worker
```

### When Routes Are Not Shown in RIB
- Verify network configuration
- Check interface status
- Verify RIB manager operation

## Advanced Features

### RIB Manager
The RIB manager operates as an independent thread and provides the following functions:
- Automatic routing information updates
- Route validity checking
- Network status monitoring

### Related Workers
- **rib-manager** - Worker that manages RIB updates and synchronization
- **neigh-manager** - ARP/ND table management
- **netlink-thread** - Netlink communication with the Linux kernel (route/address synchronization)
- **router** - vswitch-based L3 router
- **srv6-router** - SRv6 router
- **l3-tap-handler** - Packet processing via router interfaces

## Definition Location

These commands are defined in the following files:
- `sdplane/rib.c` - RIB, virtual switch, FDB, and neighbor table related commands
- `sdplane/netlink_hook.c` - Netlink hook related commands

## Related Topics

- [Virtual Switch (vswitch)](vswitch.md) - Virtual switch configuration and management
- [Neighbor Table (ARP/ND)](neighbor.md) - ARP/ND table management
- [FDB (Forwarding Database)](fdb.md) - MAC address learning table
- [SRv6](srv6.md) - Segment Routing over IPv6
- [Using as a Switch](scenario-switch.md) - L2 switching scenario guide
- [Setting Up a Router: Static Routes Only](scenario-static-router.md) - Static route router scenario guide
- [Worker & lcore Management](worker-lcore-thread-management.md)
- [lthread Management](lthread-management.md)
- [System Information & Monitoring](system-monitoring.md)
