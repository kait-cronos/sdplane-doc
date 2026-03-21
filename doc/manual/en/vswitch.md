[Top](../../README.en.md) > [User Guide](README.md) > Configuration Guides > Virtual Switch (vswitch)

# Virtual Switch (vswitch)

**Language:** **English** | [日本語](../ja/vswitch.md)

The virtual switch (vswitch) provides VLAN-based L2 switching between DPDK ports. It manages multiple VLAN domains with independent forwarding tables and integrates with the Linux kernel stack through router interfaces and capture interfaces.

## vswitch Data Structures

![vswitch Data Structures](../ja/images/vswitch-structures.png)

## vswitch Configuration Diagram

![vswitch Configuration Diagram](../ja/images/vswitch-configuration.png)

## vswitch_link Deletion Process

![vswitch_link Deletion Process](../ja/images/vswitch-link-deletion.png)

## vswitch Forwarding

![vswitch Forwarding](../ja/images/vswitch-forwarding.png)

## Enhanced Repeater Architecture

![Enhanced Repeater Architecture](../ja/images/enhanced-repeater-vswitch.png)

## Command List

### Show Commands

- [`show_rib_vswitch`](#show_rib_vswitch) - Display virtual switch information
- [`show_rib_vswitch_link`](#show_rib_vswitch_link) - Display virtual switch link information
- [`show_rib_router_if`](#show_rib_router_if) - Display router interface information
- [`show_rib_capture_if`](#show_rib_capture_if) - Display capture interface information

### Configuration Commands

- [`set_vswitch`](#set_vswitch) - Create virtual switch
- [`set_vswitch_port`](#set_vswitch_port) - Add port to virtual switch
- [`set_vswitch_port_tag_swap`](#set_vswitch_port_tag_swap) - VLAN tag conversion
- [`set_router_if`](#set_router_if) - Create router interface
- [`set_router_if_vlan`](#set_router_if_vlan) - Set router interface VLAN
- [`set_router_if_hwaddr`](#set_router_if_hwaddr) - Set router interface MAC address
- [`set_capture_if`](#set_capture_if) - Create capture interface

### Delete Commands

- [`no_set_vswitch`](#no_set_vswitch) - Delete virtual switch
- [`no_set_vswitch_port`](#no_set_vswitch_port) - Remove port from virtual switch
- [`no_set_router_if`](#no_set_router_if) - Delete router interface
- [`no_set_capture_if`](#no_set_capture_if) - Delete capture interface

## Show Commands

### show_rib_vswitch

Display virtual switch information
```
show rib vswitch
```

Displays the virtual switch configuration stored in the RIB. `show vswitch` is an alias for this command.

**Examples:**
```bash
show rib vswitch
show vswitch
```

### show_rib_vswitch_link

Display virtual switch link information
```
show rib vswitch-link
```

Displays port link information (port-to-VLAN associations) for virtual switches.

**Examples:**
```bash
show rib vswitch-link
```

### show_rib_router_if

Display router interface information
```
show rib router-if
```

Displays router interface (TAP interface) information registered in the RIB.

**Examples:**
```bash
show rib router-if
```

### show_rib_capture_if

Display capture interface information
```
show rib capture-if
```

Displays capture interface information registered in the RIB.

**Examples:**
```bash
show rib capture-if
```

## Configuration Commands

### set_vswitch

Create virtual switch
```
set vswitch <1-4094> vlan <1-4094>
```

Creates a virtual switch and assigns a native VLAN ID.

**Parameters:**
- `<1-4094>` (1st) - Virtual switch ID
- `<1-4094>` (2nd) - VLAN ID

**Examples:**
```bash
# Create virtual switch 10 with VLAN 10
set vswitch 10 vlan 10

# Create virtual switch 2031 with VLAN 2031
set vswitch 2031 vlan 2031
```

### set_vswitch_port

Add port to virtual switch
```
set vswitch <1-4094> port <0-7> (tagged|untag)
```

Connects a DPDK port to a virtual switch.

**Parameters:**
- `<1-4094>` - Virtual switch ID
- `<0-7>` - DPDK port ID
- `tagged` - Connect with VLAN tagging (trunk port)
- `untag` - Connect without VLAN tagging (access port)

**Examples:**
```bash
# Connect port 0 as an access port
set vswitch 10 port 0 untag

# Connect port 0 as a trunk port
set vswitch 2031 port 0 tagged
```

### set_vswitch_port_tag_swap

VLAN tag conversion
```
set vswitch <1-4094> port <0-7> tag swap <1-4094>
```

Performs VLAN tag swapping on a port. Rewrites the input packet's VLAN tag to the specified VLAN ID.

**Parameters:**
- `<1-4094>` (1st) - Virtual switch ID
- `<0-7>` - DPDK port ID
- `<1-4094>` (2nd) - Target VLAN ID

**Examples:**
```bash
# Swap VLAN tag to VLAN 100 on port 0
set vswitch 10 port 0 tag swap 100
```

### set_router_if

Create router interface
```
set vswitch <1-4094> router-if <WORD>
```

Creates a router interface (TAP interface) on a virtual switch. Functions as an L3 routing gateway.

**Parameters:**
- `<1-4094>` - Virtual switch ID
- `<WORD>` - TAP interface name

**Examples:**
```bash
# Create router interface rif1 on virtual switch 1
set vswitch 1 router-if rif1
```

The created TAP interface appears on Linux and can be assigned an IP address using `ip addr add`.

### set_router_if_vlan

Set router interface VLAN
```
set vswitch <1-4094> router-if <WORD> vlan-id <0-4094>
```

Assigns a VLAN ID to a router interface.

**Parameters:**
- `<1-4094>` - Virtual switch ID
- `<WORD>` - TAP interface name
- `<0-4094>` - VLAN ID

**Examples:**
```bash
set vswitch 10 router-if rif10 vlan-id 10
```

### set_router_if_hwaddr

Set router interface MAC address
```
set vswitch <1-4094> router-if <WORD> hwaddr <WORD>
```

Sets the MAC address for a router interface.

**Parameters:**
- `<1-4094>` - Virtual switch ID
- `<WORD>` (1st) - TAP interface name
- `<WORD>` (2nd) - MAC address (e.g., `a8:b8:e0:05:9b:e1`)

**Examples:**
```bash
set vswitch 10 router-if rif10 hwaddr a8:b8:e0:05:9b:e1
```

### set_capture_if

Create capture interface
```
set vswitch <1-4094> capture-if <WORD>
```

Creates a capture interface (TAP interface) on a virtual switch. Used for packet capture and monitoring.

**Parameters:**
- `<1-4094>` - Virtual switch ID
- `<WORD>` - TAP interface name

**Examples:**
```bash
# Create capture interface cif1 on virtual switch 1
set vswitch 1 capture-if cif1
```

## Delete Commands

### no_set_vswitch

Delete virtual switch
```
no set vswitch <1-4094>
```

Deletes a virtual switch.

**Parameters:**
- `<1-4094>` - Virtual switch ID

**Examples:**
```bash
no set vswitch 10
```

### no_set_vswitch_port

Remove port from virtual switch
```
no set vswitch <1-4094> port <0-7>
```

Removes a port from a virtual switch.

**Parameters:**
- `<1-4094>` - Virtual switch ID
- `<0-7>` - DPDK port ID

**Examples:**
```bash
no set vswitch 10 port 0
```

### no_set_router_if

Delete router interface
```
no set router-if <WORD>
```

Deletes a router interface.

**Parameters:**
- `<WORD>` - TAP interface name

**Examples:**
```bash
no set router-if rif1
```

### no_set_capture_if

Delete capture interface
```
no set capture-if <WORD>
```

Deletes a capture interface.

**Parameters:**
- `<WORD>` - TAP interface name

**Examples:**
```bash
no set capture-if cif1
```

## Architecture

### Virtual Switch Framework

The virtual switch provides:

- **Multiple VLANs**: Support for multiple VLAN domains (1-4094)
- **Port Aggregation**: Multiple physical ports per virtual switch
- **Isolated Forwarding**: Independent forwarding domains per VLAN
- **Flexible Tagging**: Per-port native, tagged, and conversion modes

### TAP Interface Integration

- **Router Interface**: Kernel network stack integration for L3 processing
- **Capture Interface**: Packet monitoring and analysis capabilities
- **Ring Buffer**: Efficient packet transfer between data plane and kernel
- **Bidirectional**: Packet processing in both input and output directions

### VLAN Processing

- **VLAN Tagging**: Adding 802.1Q headers to untagged frames
- **VLAN Untagging**: Removing 802.1Q headers from tagged frames
- **VLAN Translation**: Changing VLAN IDs between input and output
- **Native VLAN**: Handling untagged traffic on trunk ports

### Packet Flow

1. **Input Processing**: Packet reception at DPDK port → VLAN determination based on tag or native settings → Destination virtual switch lookup
2. **Virtual Switch Lookup**: MAC address learning and lookup → Output port determination within VLAN domain → Unknown unicast/broadcast flooding
3. **Output Processing**: VLAN header manipulation per port configuration → Packet copying for multiple destinations → TAP interface integration
4. **TAP Interface Processing**: Router interface (kernel L3 processing) / Capture interface (monitoring and analysis)

## Configuration Example

### Basic VLAN Switching

```bash
# Create virtual switches
set vswitch 2031 vlan 2031
set vswitch 2032 vlan 2032

# Connect DPDK ports to virtual switches
set vswitch 2031 port 0 tagged
set vswitch 2032 port 0 tagged

# Create router interfaces
set vswitch 2031 router-if rif2031
set vswitch 2032 router-if rif2032

# Create capture interfaces
set vswitch 2031 capture-if cif2031
set vswitch 2032 capture-if cif2032
```

### Verification

```bash
# Check virtual switch configuration
show vswitch
show rib vswitch-link
show rib router-if
show rib capture-if
```

## Source Location

These commands are defined in:
- `sdplane/rib.c` - Virtual switch related commands

## See Also

- [RIB & Routing](routing.md) - RIB and routing functions
- [FDB (Forwarding Database)](fdb.md) - MAC address learning table
- [Neighbor Table](neighbor.md) - ARP/ND table management
- [Using a Switch](scenario-switch.md) - L2 switching scenario guide
- [Configuring a Router: Static Routes Only](scenario-static-router.md) - Static route router scenario guide
- [Enhanced Repeater Application](enhanced-repeater-application.md) - Enhanced repeater feature details
