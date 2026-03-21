[Top](../../README.en.md) > [User Guide](README.md) > Configuration Guides > lthread Management

# lthread Management

**Language:** **English** | [日本語](../ja/lthread-management.md)

Commands for managing lthread (lightweight threads).

## Command List

- [`set_worker_lthread_stat_collector`](#set_worker_lthread_stat_collector) - Set lthread Statistics Collector
- [`set_worker_lthread_rib_manager`](#set_worker_lthread_rib_manager) - Set lthread RIB Manager
- [`set_worker_lthread_netlink_thread`](#set_worker_lthread_netlink_thread) - Set lthread Netlink Thread
- [`set_worker_lthread_neigh_manager`](#set_worker_lthread_neigh_manager) - Set lthread Neighbor Manager
- [`set_worker_lthread_l3_tap_handler`](#set_worker_lthread_l3_tap_handler) - Set lthread L3 TAP Handler
- [`set_worker_lthread_dhcp_server`](#set_worker_lthread_dhcp_server) - Set lthread DHCP Server

## Command List

### set_worker_lthread_stat_collector

Set lthread Statistics Collector
```
set worker lthread stat-collector
```

Configure lthread worker that collects statistics information.

**Examples:**
```bash
set worker lthread stat-collector
```

### set_worker_lthread_rib_manager

Set lthread RIB Manager
```
set worker lthread rib-manager
```

Configure lthread worker that manages RIB (Routing Information Base).

**Examples:**
```bash
set worker lthread rib-manager
```

### set_worker_lthread_netlink_thread

Set lthread Netlink Thread
```
set worker lthread netlink-thread
```

Configure lthread worker that processes Netlink communication.

**Examples:**
```bash
set worker lthread netlink-thread
```

### set_worker_lthread_neigh_manager

Set lthread Neighbor Manager
```
set worker lthread neigh-manager
```

Configure lthread worker that manages the neighbor table (ARP/ND table).

**Examples:**
```bash
set worker lthread neigh-manager
```

### set_worker_lthread_l3_tap_handler

Set lthread L3 TAP Handler
```
set worker lthread l3-tap-handler
```

Configure lthread worker that processes packets on L3 TAP interfaces. Handles packet transmission and reception via router interfaces.

**Examples:**
```bash
set worker lthread l3-tap-handler
```

### set_worker_lthread_dhcp_server

Set lthread DHCP Server
```
set worker lthread dhcp-server
```

Configure lthread worker that provides DHCP server functionality.

**Examples:**
```bash
set worker lthread dhcp-server
```

## lthread Overview

### What is lthread
lthread (lightweight thread) is a lightweight thread implementation that realizes cooperative multithreading.

### Main Features
- **Lightweight** - Low memory overhead
- **High Speed** - Fast context switching
- **Cooperative** - Control through explicit yield
- **Scalability** - Efficient management of large numbers of threads

### Differences from Traditional Threads
- **Preemptive vs Cooperative** - Explicit control transfer
- **Kernel Thread vs User Thread** - Execution without kernel intervention
- **Heavy vs Light** - Small overhead for creation and switching

## lthread Worker Types

### Statistics Collector (stat-collector)
Worker that periodically collects system statistics.

**Functions:**
- Port statistics collection
- Thread statistics collection
- System statistics collection
- Statistics data aggregation

**Examples:**
```bash
# Configure statistics collector
set worker lthread stat-collector

# Check statistics
show port statistics
show thread counter
```

### RIB Manager (rib-manager)
Worker that manages RIB (Routing Information Base).

**Functions:**
- Routing table management
- Route addition, deletion, and updates
- Route information distribution
- Routing status monitoring

**Examples:**
```bash
# Configure RIB manager
set worker lthread rib-manager

# Check RIB information
show rib
```

### Netlink Thread (netlink-thread)
Worker that processes Netlink communication.

**Functions:**
- Netlink communication with kernel
- Network configuration monitoring
- Interface status monitoring
- Routing information reception

**Examples:**
```bash
# Configure Netlink thread
set worker lthread netlink-thread

# Check network status
show port
show vswitch
```

### Neighbor Manager (neigh-manager)
Worker that manages the neighbor table (ARP/ND table).

**Functions:**
- ARP table (IPv4) management
- ND table (IPv6) management
- Neighbor entry addition, deletion, and updates
- Neighbor state transition processing

**Examples:**
```bash
# Configure neighbor manager
set worker lthread neigh-manager

# Check neighbor table
show neighbor
show neighbor ipv4
```

### L3 TAP Handler (l3-tap-handler)
Worker that processes packets on L3 TAP interfaces.

**Functions:**
- Packet transmission and reception via router interfaces
- Packet exchange with the Linux kernel stack
- TAP interface ring buffer management

**Examples:**
```bash
# Configure L3 TAP handler
set worker lthread l3-tap-handler

# Check router interfaces
show rib router-if
```

### DHCP Server (dhcp-server)
Worker that provides DHCP (Dynamic Host Configuration Protocol) server functionality.

**Examples:**
```bash
# Configure DHCP server
set worker lthread dhcp-server
```

## lthread Configuration

### Basic Configuration Steps
1. **Configure Required lthread Workers**
```bash
# Configure statistics collector
set worker lthread stat-collector

# Configure RIB manager
set worker lthread rib-manager

# Configure Netlink thread
set worker lthread netlink-thread
```

2. **Verify Configuration**
```bash
# Check worker status
show worker

# Check thread information
show thread
```

### Recommended Configuration
For general use, the following combination is recommended:
```bash
# Basic lthread worker configuration
set worker lthread stat-collector
set worker lthread rib-manager
set worker lthread netlink-thread
```

When using routing functionality, add the following as well:
```bash
# Routing lthread worker configuration
set worker lthread neigh-manager
set worker lthread l3-tap-handler
```

## Usage Examples

### Basic Configuration
```bash
# Configure lthread workers
set worker lthread stat-collector
set worker lthread rib-manager
set worker lthread netlink-thread

# Verify configuration
show worker
show thread
```

### Statistics Monitoring Configuration
```bash
# Configure statistics collector
set worker lthread stat-collector

# Periodically check statistics
show port statistics
show thread counter
show mempool
```

### Routing Management Configuration
```bash
# Configure RIB manager
set worker lthread rib-manager

# Check routing information
show rib
show vswitch
```

## Monitoring and Management

### lthread Status Check
```bash
# Overall status check
show worker
show thread

# Check specific statistics
show thread counter
```

### Performance Monitoring
```bash
# Check lthread performance
show thread counter

# Check overall system performance
show port statistics pps
show mempool
```

## Troubleshooting

### When lthread Does Not Operate
1. Check worker configuration
```bash
show worker
```

2. Check thread status
```bash
show thread
```

3. Check system status
```bash
show rcu
show mempool
```

### When Statistics Information Is Not Updated
1. Check statistics collector status
```bash
show worker
show thread
```

2. Manually check statistics
```bash
show port statistics
show thread counter
```

### When RIB Is Not Updated
1. Check RIB manager status
```bash
show worker
show thread
```

2. Check RIB information
```bash
show rib
```

3. Check Netlink thread status
```bash
show worker
```

## Advanced Features

### Cooperative Operation of lthread
Since lthread operates cooperatively, the following points require attention:

- **Explicit Yield** - Long-running processes require explicit yield
- **Deadlock Avoidance** - Cooperative design avoids deadlocks
- **Fairness** - All threads execute appropriately

### Performance Optimization
- **Appropriate Worker Placement** - Consider affinity with CPU cores
- **Memory Efficiency** - Set appropriate memory pool sizes
- **Load Distribution** - Load balancing with multiple workers

## Definition Location

These commands are defined in the following file:
- `sdplane/lthread_main.c`

## Related Topics

- [Worker & lcore Management](worker-lcore-thread-management.md)
- [Thread Information](worker-lcore-thread-management.md)
- [System Information & Monitoring](system-monitoring.md)
- [RIB & Routing](routing.md)