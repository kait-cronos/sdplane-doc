[Top](../../README.en.md) > [User Guide](README.md) > Configuration Guides > Neighbor Table (ARP/ND)

# Neighbor Table (ARP/ND)

**Language:** **English** | [日本語](../ja/neighbor.md)

The neighbor table manages the mapping between IP addresses and MAC addresses resolved through IPv4 ARP (Address Resolution Protocol) and IPv6 ND (Neighbor Discovery). It is used for destination MAC address resolution during L3 packet forwarding.

## Data Structure

![Neighbor Data Structure](../ja/images/neighbor-structure.png)

![Neighbor Table Structure](../ja/images/neighbor-table.png)

## Command List

- [`show_neighbor`](#show_neighbor) - Display neighbor table

## Command Details

### show_neighbor

Display neighbor table
```
show neighbor (|ipv4|ipv6) (|<WORD>)
```

Displays the neighbor table (ARP table / ND table).

**Parameters:**
- `ipv4` - Display IPv4 ARP table
- `ipv6` - Display IPv6 ND table
- `<WORD>` - Filter by interface name
- No parameters - Display all neighbor entries

**Examples:**
```bash
show neighbor
show neighbor ipv4
show neighbor ipv6
show neighbor ipv4 tap0
```

## Neighbor Resolution Mechanism

### Control Packet Handling

The router processing threads (`router` / `srv6-router`) identify the following as control packets and forward them to the router interface (TAP device):

- ARP
- IPv6 NS/NA (Neighbor Solicitation / Neighbor Advertisement)
- OSPF (IPv4/IPv6)
- Packets whose destination matches the router interface's IP/MAC address

Neighbor resolution information (ARP/ND) is reflected in the neighbor table via internal messages.

### Neighbor Entry Structure

The neighbor table consists of two types of tables (ARP / ND) but shares a common data structure. There are `master_neigh_tables` managed thread-locally by `neigh_manager`, and `rib_info->neigh_tables` managed by `rib_manager` within `rib_info`.

## Related Workers

### neigh-manager

An lthread worker that manages ARP/ND tables. It is responsible for synchronizing Linux kernel neighbor information with sdplane's internal tables.

```bash
# Configure neigh-manager worker
set worker lthread neigh-manager
```

## Verification

```bash
# Check all neighbor entries
show neighbor

# IPv4 neighbors only
show neighbor ipv4

# Neighbors for a specific interface
show neighbor ipv4 rif1
```

## Source Location

These commands are defined in:
- `sdplane/rib.c` - Neighbor table related commands

## See Also

- [RIB & Routing](routing.md) - RIB and routing functions
- [Virtual Switch (vswitch)](vswitch.md) - Virtual switch configuration and management
- [FDB (Forwarding Database)](fdb.md) - MAC address learning table
- [lthread Management](lthread-management.md) - lthread management including neigh-manager
- [Configuring a Router: Static Routes Only](scenario-static-router.md) - Static route router scenario guide
