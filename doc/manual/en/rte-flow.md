[Top](../../README.en.md) > [User Guide](README.md) > Configuration Guides > rte-flow

# rte-flow

**Language:** **English** | [日本語](../ja/rte-flow.md)

Commands for configuring hardware flow classification using DPDK's rte_flow. By leveraging NIC hardware capabilities, you can set up packet pattern matching and actions (such as forwarding to a specific queue or dropping).

## Command List

- [`set_rte_flow_pattern`](#set_rte_flow_pattern) - Define flow patterns
- [`set_rte_flow_action`](#set_rte_flow_action) - Define flow actions
- [`set_rte_flow`](#set_rte_flow) - Apply flow rules
- [`show_rte_flow_pattern`](#show_rte_flow_pattern) - Display flow patterns
- [`show_rte_flow_action`](#show_rte_flow_action) - Display flow actions

## Overview

![rte-flow overview](images/rte-flow-overview.png)

rte-flow configuration is performed in three steps:

1. **Define patterns** (`set rte-flow-pattern`) - Define conditions for matching packets
2. **Define actions** (`set rte-flow action`) - Define operations to perform on matched packets
3. **Apply flow rules** (`set rte-flow port`) - Associate patterns and actions with a port and apply them

## Command Details

### set_rte_flow_pattern

Define flow patterns
```
set rte-flow-pattern <ID> index <INDEX> (ether|vlan|ipv4|end) ...
```

Defines packet match conditions (patterns). Each pattern consists of multiple indexes, specifying match conditions per layer. The pattern must be terminated with `end`.

**Important:** Individual field setting commands such as `src`, `dst`, `proto`, `type`, and `id` do not zero-initialize the spec/mask structures. Always initialize with `any` or `zero` before setting individual fields on the same index. Without initialization, undefined values may remain in other mask fields, resulting in unintended match conditions.

**Ethernet layer:**
```bash
# Match all Ethernet frames (zero-initialize spec/mask)
set rte-flow-pattern 1 index 0 ether any

# Match by source MAC address (initialize with any first)
set rte-flow-pattern 1 index 0 ether any
set rte-flow-pattern 1 index 0 ether src 5e:3d:bb:8d:fe:52

# Match by EtherType (initialize with any first)
set rte-flow-pattern 1 index 0 ether any
set rte-flow-pattern 1 index 0 ether type 0x86dd
```

**VLAN layer:**
```bash
# Zero-initialize VLAN fields (wildcard)
set rte-flow-pattern 1 index 1 vlan zero

# Match by specific VLAN ID (initialize with zero first)
set rte-flow-pattern 1 index 1 vlan zero
set rte-flow-pattern 1 index 1 vlan id 100
```

**IPv4 layer:**
```bash
# Zero-initialize IPv4 fields (wildcard)
set rte-flow-pattern 1 index 2 ipv4 zero

# Match by source IP address (initialize with zero first)
set rte-flow-pattern 1 index 2 ipv4 zero
set rte-flow-pattern 1 index 2 ipv4 src 100.100.100.100

# Match by destination IP address (initialize with zero first)
set rte-flow-pattern 1 index 2 ipv4 zero
set rte-flow-pattern 1 index 2 ipv4 dst 200.200.200.200

# Match by protocol number (initialize with zero first)
set rte-flow-pattern 1 index 2 ipv4 zero
set rte-flow-pattern 1 index 2 ipv4 proto 89
```

**Pattern termination:**
```bash
set rte-flow-pattern 1 index 3 end
```

### set_rte_flow_action

Define flow actions
```
set rte-flow action <ID> index <INDEX> (queue|drop|end) ...
```

Defines actions to perform on matched packets. The action list must be terminated with `end`.

```bash
# Forward to a specific queue
set rte-flow action 1 index 0 queue 1

# Terminate the action list
set rte-flow action 1 index 1 end
```

Packets forwarded to a queue are delivered to the DPDK thread assigned to that queue via the `set thread <lcore> port <port> queue <queue>` command.

### set_rte_flow

Apply flow rules
```
set rte-flow port <PORT> pattern <ID> action <ID>
```

Associates previously defined patterns and actions with a DPDK port and applies the flow rule.

**Parameters:**
- `<PORT>` - DPDK port ID
- `pattern <ID>` - Pattern ID (defined with `set rte-flow-pattern`)
- `action <ID>` - Action ID (defined with `set rte-flow action`)

**Example:**
```bash
set rte-flow port 0 pattern 1 action 1
```

### show_rte_flow_pattern

Display flow patterns
```
show rte-flow pattern (|<0-15>)
```

Displays the contents of configured flow patterns. If a pattern ID is specified, only that pattern is displayed. If omitted, all configured patterns are displayed.

**Examples:**
```bash
# Display all patterns
show rte-flow pattern

# Display only pattern 1
show rte-flow pattern 1
```

### show_rte_flow_action

Display flow actions
```
show rte-flow action
```

Displays the contents of configured flow actions.

**Example:**
```bash
show rte-flow action
```

## Configuration Examples

### Route OSPF Packets to a Specific Queue

```bash
# Pattern: Ethernet -> IPv4 -> protocol 89 (OSPF)
set rte-flow-pattern 1 index 0 ether any
set rte-flow-pattern 1 index 1 ipv4 zero
set rte-flow-pattern 1 index 1 ipv4 proto 89
set rte-flow-pattern 1 index 2 end

# Action: forward to queue 1
set rte-flow action 1 index 0 queue 1
set rte-flow action 1 index 1 end

# Apply to port 0
set rte-flow port 0 pattern 1 action 1
```

### Route Traffic with a Specific VLAN ID

```bash
# Pattern: Ethernet -> VLAN 100
set rte-flow-pattern 2 index 0 ether any
set rte-flow-pattern 2 index 1 vlan zero
set rte-flow-pattern 2 index 1 vlan id 100
set rte-flow-pattern 2 index 2 end

# Action: forward to queue 1
set rte-flow action 2 index 0 queue 1
set rte-flow action 2 index 1 end

# Apply to port 0
set rte-flow port 0 pattern 2 action 2
```

## Notes

- rte-flow depends on NIC hardware capabilities. The NIC must support the rte_flow API.
- Pattern and action index numbers should be specified sequentially starting from 0 and terminated with `end`.
- **Initialization before setting fields is mandatory.** Individual field setting commands such as `src`, `dst`, `proto`, `type`, and `id` do not initialize the internal spec/mask structures. Always initialize with `any` or `zero` before setting individual fields on the same index. Skipping initialization may leave undefined values in the mask, causing unintended match conditions.

## See Also

- [Queue Configuration](queue-configuration.md) - DPDK queue configuration
- [Worker & lcore Management](worker-lcore-thread-management.md) - Worker and queue assignment
- [Port Management & Statistics](port-management.md) - DPDK port management
