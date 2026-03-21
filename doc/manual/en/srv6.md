[Top](../../README.en.md) > [User Guide](README.md) > Configuration Guides > SRv6 (Segment Routing over IPv6)

# SRv6 (Segment Routing over IPv6)

**Language:** **English** | [日本語](../ja/srv6.md)

sdplane implements SRv6 (Segment Routing over IPv6) Transit Node functionality. With SRv6, it processes IPv6 packets' Segment Routing Headers (SRH) and forwards packets along specified segment lists.

## SRv6 Forwarding

![SRv6 Forwarding](../ja/images/srv6-forwarding.png)

## Command List

- [`set_srv6_local_sid`](#set_srv6_local_sid) - Set SRv6 Local SID
- [`show_srv6_local_sid`](#show_srv6_local_sid) - Display SRv6 Local SID

## Supported Features

- Processes SRH type 4 (`RTE_IPV6_SRCRT_TYPE_4`)
- End-equivalent basic operation (segments_left decrement + next SID reflection to IPv6 destination)
- Up to 16 local-sids (0-15) configurable
- Used with the `srv6-router` worker type

## Command Details

### set_srv6_local_sid

Set SRv6 Local SID
```
set srv6 local-sid <WORD> (|<0-15>)
```

Sets an SRv6 Local SID.

**Parameters:**
- `<WORD>` - IPv6 address (SID)
- `<0-15>` - Local SID index (default: 0)

**Examples:**
```bash
set srv6 local-sid 2001:db8::1
set srv6 local-sid 2001:db8::2 1
```

### show_srv6_local_sid

Display SRv6 Local SID
```
show srv6 local-sid
```

Displays configured SRv6 Local SIDs.

**Examples:**
```bash
show srv6 local-sid
```

## Related Workers

### srv6-router

An lcore worker that performs routing using SRv6 (Segment Routing over IPv6). It implements Transit Node functionality and performs SRH processing. It shares most of its codebase with the router worker, adding SRH processing to IPv6 forwarding.

```bash
# Configure srv6-router worker
set worker lcore 1 srv6-router
```

## Source Location

These commands are defined in:
- `sdplane/rib.c` - SRv6 related commands

## See Also

- [RIB & Routing](routing.md) - RIB and routing functions
- [Virtual Switch (vswitch)](vswitch.md) - Virtual switch configuration and management
- [Worker & lcore Management](worker-lcore-thread-management.md) - srv6-router worker management
