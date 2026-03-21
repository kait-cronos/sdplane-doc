[Top](../../README.en.md) > [User Guide](README.md) > Configuration Guides > FDB (Forwarding Database)

# FDB (Forwarding Database)

**Language:** **English** | [日本語](../ja/fdb.md)

The FDB (Forwarding Database) is an L2 forwarding table built through MAC address learning. It learns source MAC addresses from received packets and determines output ports based on destination MAC addresses.

## Command List

- [`show_fdb`](#show_fdb) - Display FDB entries
- [`show_fib_route`](#show_fib_route) - Display FIB table

## Command Details

### show_fdb

Display FDB entries
```
show fdb
```

Displays FDB (Forwarding Database) entries.

**Display Fields:**
- Source MAC address
- Output port ID
- VLAN ID
- Entry status (Active / None)
- Last seen timestamp

**Examples:**
```bash
show fdb
```

The FDB holds up to 1024 entries with a default aging time of 7200 seconds (2 hours).

### show_fib_route

Display FIB Table
```
show fib (ipv4|ipv6) route
```

Display IPv4 or IPv6 FIB (Forwarding Information Base) table. The FIB contains the best routes selected from the RIB and is used for actual packet forwarding.

**Parameters:**
- `ipv4` - Display IPv4 FIB table
- `ipv6` - Display IPv6 FIB table

**Examples:**
```bash
show fib ipv4 route
show fib ipv6 route
```

## How FDB Works

### MAC Learning

The virtual switch (vswitch) automatically learns source MAC addresses from received packets and registers them in the FDB. Learned entries are associated with the receiving port and VLAN ID.

### Flooding

When the destination MAC address does not exist in the FDB (unlearned), the packet is flooded to all ports within the same VLAN. Broadcast frames are also flooded.

- **Unicast**: If the destination MAC exists in the FDB, forward to a single port
- **Unicast (unlearned)**: If the destination MAC does not exist in the FDB, flood within the VLAN
- **Broadcast/Multicast**: Flood within the VLAN

### Aging

FDB entries are automatically removed after a period of inactivity (default 7200 seconds). This allows the FDB to adapt to network topology changes.

## Verification

```bash
# Check FDB entries
show fdb

# Check together with virtual switch configuration
show vswitch
show rib vswitch-link
```

## Source Location

These commands are defined in:
- `sdplane/rib.c` - FDB related commands

## See Also

- [Virtual Switch (vswitch)](vswitch.md) - Virtual switch configuration and management
- [Neighbor Table](neighbor.md) - ARP/ND table management
- [RIB & Routing](routing.md) - RIB and routing functions
- [System Information & Monitoring](system-monitoring.md) - FDB monitoring
