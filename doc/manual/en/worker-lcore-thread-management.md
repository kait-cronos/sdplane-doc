[Top](../../README.en.md) > [User Guide](README.md) > Configuration Guides > Worker & lcore Management

# Worker & lcore Management & Thread Information

**Language:** **English** | [日本語](../ja/worker-lcore-thread-management.md)

Commands for managing and monitoring DPDK worker threads, lcores, and thread information.

## Command List

- [`set_worker`](#set_worker) - Worker type configuration
- [`reset_worker`](#reset_worker) - Worker reset
- [`start_worker`](#start_worker) - Start worker
- [`stop_worker`](#stop_worker) - Stop worker
- [`restart_worker`](#restart_worker) - Restart worker
- [`show_worker`](#show_worker) - Display worker information
- [`show_thread`](#show_thread) - Display thread information
- [`show_thread_counter`](#show_thread_counter) - Display thread counter
- [`set_mempool`](#set_mempool) - Configure memory pool
- [`set_rte_eal_argv`](#set_rte_eal_argv) - Configure RTE EAL command line arguments
- [`rte_eal_init`](#rte_eal_init) - RTE EAL initialization
- [`rte_eal_init_argv_list`](#rte_eal_init_argv_list) - RTE EAL initialization using argv-list

## Worker Management Commands

### set_worker

Worker Type Configuration
```
set worker lcore <0-16> (|none|l2fwd|l3fwd|l3fwd-lpm|tap-handler|l3-tap-handler|l2-repeater|vlan-switch|enhanced-repeater|l2-switch|router|srv6-router|nettlp-thread|pktgen|dhcp-server|rib-manager|netlink-thread)
```

Sets the worker type for the specified lcore.

**Worker Types:**
- `none` - No worker
- `l2fwd` - Layer 2 forwarding
- `l3fwd` - Layer 3 forwarding
- `l3fwd-lpm` - Layer 3 forwarding (LPM)
- `tap-handler` - TAP interface handler
- `l3-tap-handler` - L3 TAP interface handler (packet processing via router interface)
- `l2-repeater` - Layer 2 repeater
- `vlan-switch` - VLAN switch
- `enhanced-repeater` - Enhanced repeater with VLAN switching and TAP interface
- `l2-switch` - Layer 2 switch
- `router` - Virtual switch (vswitch) based L3 router
- `srv6-router` - SRv6 router
- `nettlp-thread` - NetTLP thread
- `pktgen` - Packet generator
- `dhcp-server` - DHCP server
- `rib-manager` - RIB manager
- `netlink-thread` - Netlink thread

**Examples:**
```bash
# Set L2 forwarding worker to lcore 1
set worker lcore 1 l2fwd

# Set enhanced repeater worker to lcore 1
set worker lcore 1 enhanced-repeater

# Set router worker to lcore 1, L3 TAP handler to lcore 2
set worker lcore 1 router
set worker lcore 2 l3-tap-handler

# Set no worker to lcore 2
set worker lcore 2 none
```

### reset_worker

Reset Worker
```
reset worker lcore (<0-16>|all)
```

Resets the worker for the specified lcore or all lcores.

### start_worker

Start Worker
```
start worker lcore (<0-16>|all)
```

Starts the worker for the specified lcore or all lcores.

### stop_worker

Stop Worker
```
stop worker lcore (<0-16>|all)
```

Stops the worker for the specified lcore or all lcores.

### restart_worker

Restart Worker
```
restart worker lcore (<0-16>|all)
```

Restarts the worker for the specified lcore or all lcores.

**Examples:**
```bash
# Start worker for lcore 1
start worker lcore 1

# Stop all workers
stop worker lcore all

# Restart worker for lcore 3
restart worker lcore 3
```

### show_worker

Display Worker Information
```
show worker
```

Displays the current worker status and configuration.

## Thread Information Commands

### show_thread

Display Thread Information
```
show thread
```

Displays current thread status and information.

This command displays the following information:
- Thread ID
- Thread status
- Running tasks
- CPU usage
- Memory usage

### show_thread_counter

Display Thread Counter
```
show thread counter
```

Displays thread counter information.

This command displays the following information:
- Processed packet count
- Execution count
- Error count
- Processing time statistics

## System Initialization Commands

### set_mempool

Configure Memory Pool
```
set mempool
```

Configures DPDK memory pool.

### set_rte_eal_argv

Configure RTE EAL Command Line Arguments
```
set rte_eal argv <WORD> <WORD> <WORD> <WORD> <WORD> <WORD>
```

Configures command line arguments used for RTE EAL (Environment Abstraction Layer) initialization.

**Examples:**
```bash
# Configure EAL arguments
set rte_eal argv -c 0x1 -n 4 --socket-mem 1024,1024 --huge-dir /mnt/huge
```

### rte_eal_init

RTE EAL Initialization
```
rte_eal_init
```

Initializes RTE EAL (Environment Abstraction Layer). Uses the arguments configured with `set rte_eal argv` for initialization.

### rte_eal_init_argv_list

RTE EAL Initialization Using argv-list
```
rte_eal_init argv-list <0-7>
```

Initializes RTE EAL using the arguments stored in the specified argv-list. The arguments must be configured in advance using the `set argv-list` command.

**Examples:**
```bash
# Configure arguments in argv-list 0, then initialize
set argv-list 0 "-c 0x7 -n 4"
rte_eal_init argv-list 0
```

## Worker Types and Thread Architecture

### Worker Type Details

#### L2 Forwarding (l2fwd)
Worker that performs packet forwarding at Layer 2 level. Forwards packets based on MAC addresses.

#### L3 Forwarding (l3fwd/l3fwd-lpm)
Worker that performs packet forwarding at Layer 3 level. Routes packets based on IP addresses. LPM uses Longest Prefix Matching.

#### TAP Handler (tap-handler)
Worker that performs packet transfer between TAP interfaces and DPDK ports.

#### L3 TAP Handler (l3-tap-handler)
Worker that processes packet sending and receiving via router interfaces (TAP interfaces). Exchanges packets with the Linux kernel stack.

#### Enhanced Repeater (enhanced-repeater)
High-functionality repeater worker with VLAN switching and TAP interface capabilities.

#### L2 Switch (l2-switch)
Worker that performs Layer 2 switching. Forwards packets based on MAC address learning and FDB.

#### Router (router)
Worker that performs L3 routing based on virtual switch (vswitch). Forwards IP packets using RIB and FIB.

#### SRv6 Router (srv6-router)
Worker that performs routing using SRv6 (Segment Routing over IPv6).

#### Packet Generator (pktgen)
Worker that generates packets for testing purposes.

### sdplane Thread Architecture

![sdplane Thread Architecture](images/thread-architecture.png)

sdplane operates with the following thread types in a cooperative multithreading model:

#### 1. Worker Threads (running on lcores)
- **L2/L3 Forwarding** - Packet forwarding processing
- **TAP Handler** - TAP interface processing
- **PKTGEN** - Packet generation

#### 2. Management Threads (lthreads)
- **RIB Manager** - Routing information management
- **Statistics Collector** - Statistics information collection
- **Netlink Thread** - Netlink communication processing

#### 3. System Threads
- **VTY Server** - VTY connection processing
- **Console** - Console input/output

### Relationship with lthread
sdplane uses a cooperative multithreading model:
- **lthread** - Lightweight thread implementation
- **Cooperative Scheduling** - Control through explicit yields
- **High Efficiency** - Reduces context switching overhead

## Understanding Thread Information

### Basic Display Items
- **Thread ID** - Thread identifier
- **Name** - Thread name
- **State** - Thread state (Running/Ready/Blocked/Terminated)
- **lcore** - Running CPU core
- **Type** - Thread type

### Counter Information
- **Packets** - Processed packet count
- **Loops** - Loop execution count
- **Errors** - Error count
- **CPU Time** - CPU usage time

## Usage Examples and Workflows

### Basic Configuration and Worker Startup
```bash
# 1. Configure EAL initialization
set rte_eal argv -c 0xf -n 4

# 2. Execute EAL initialization
rte_eal_init

# 3. Configure memory pool
set mempool

# 4. Configure workers
set worker lcore 1 l2fwd
set worker lcore 2 l3fwd

# 5. Start workers
start worker lcore all

# 6. Check status
show worker
show thread
```

### Monitoring and Troubleshooting

#### Regular Monitoring
```bash
# Check worker and thread status
show worker
show thread
show thread counter
```

#### Performance Analysis
```bash
# Performance-related information
show thread counter
```

#### Troubleshooting Procedures

**When threads are unresponsive**
```bash
# 1. Check thread status
show thread

# 2. Check worker status
show worker

# 3. Restart if necessary
restart worker lcore 1
```

**When performance degrades**
```bash
# 1. Check counter information
show thread counter

# 2. Check error count
show thread counter

# 3. Restart workers
restart worker lcore all
```

## Optimization Points

### CPU Affinity Configuration
- Place workers on appropriate lcores
- Consider NUMA node placement
- Balance CPU usage

### Memory Efficiency Improvement
- Appropriate memory pool size
- Prevent memory leaks
- Improve cache efficiency

### Utilizing Statistics
- Performance monitoring
- Capacity planning
- Anomaly detection

## Definition Location

These commands are defined in the following files:
- `sdplane/dpdk_lcore_cmd.c` - Worker management commands
- `sdplane/thread_info.c` - Thread information commands

## Related Topics

- [Port Management & Statistics](port-management.md)
- [lthread Management](lthread-management.md)
- [System Information & Monitoring](system-monitoring.md)
- [Debug & Logging](debug-logging.md)