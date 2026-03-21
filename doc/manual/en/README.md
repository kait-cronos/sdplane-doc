[Top](../../README.en.md) > User Guide

# sdplane-oss User's Guide

**Language:** **English** | [日本語](../ja/README.md)

sdplane-oss is a high-performance DPDK-based software router. This user guide describes all commands and features of sdplane.

## Configuration Guides

1. [Port Management & Statistics](port-management.md) (10 commands) - DPDK port management and statistics
2. [Worker & lcore Management & Thread Information](worker-lcore-thread-management.md) (6 commands) - Worker threads, lcore, and thread information management

3. [Debug & Logging](debug-logging.md) (2 commands) - Debug and logging functions
4. [VTY & Shell Management](vty-shell.md) (4 commands) - VTY and shell management
5. [System Information & Monitoring](system-monitoring.md) (10 commands) - System information and monitoring
6. [RIB & Routing](routing.md) (1 command) - RIB and routing functions
   - [Virtual Switch (vswitch)](vswitch.md) (16 commands) - Virtual switch configuration and management
   - [Neighbor Table (ARP/ND)](neighbor.md) (1 command) - Neighbor table display
   - [FDB (Forwarding Database)](fdb.md) (1 command) - FDB display
   - [SRv6](srv6.md) (2 commands) - SRv6 Local SID configuration and display
7. [Queue Configuration](queue-configuration.md) (3 commands) - Queue configuration and management
8. [Packet Generation](packet-generation.md) (3 commands) - Packet generation using PKTGEN

9. [TAP Interface](tap-interface.md) (2 commands) - TAP interface management
10. [lthread Management](lthread-management.md) (3 commands) - lthread management
11. [Device Management](device-management.md) (2 commands) - Device and driver management

## Basic Usage

### Connection

To connect to sdplane:

```bash
# Start sdplane
sudo ./sdplane/sdplane

# Connect to CLI from another terminal
telnet localhost 9882
```

### Help

You can use `?` to display help for any command:

```
sdplane# ?
sdplane# show ?
sdplane# set ?
```

### Basic Commands

- `show version` - Display version information
- `show port` - Display port information
- `show worker` - Display worker information
- `exit` - Exit from CLI

## License

This project is licensed under the MIT License. See the [LICENSE](../../../LICENSE) file for license details.

For detailed usage instructions, please refer to the documentation for each category.