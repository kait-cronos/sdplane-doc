[User Guide](README.md) > Tips

# Tips

**Language:** **English** | [日本語](../ja/tips.md)

Tips for configuring and operating sdplane-oss.

### IOMMU is required when using vfio-pci as NIC driver

- Intel: Intel VT-d
- AMD: AMD IOMMU / AMD-V

These need to be enabled in BIOS settings.
GRUB configuration may also need to be changed:

```conf
# /etc/default/grub
GRUB_CMDLINE_LINUX="iommu=pt intel_iommu=on"
```

```bash
sudo update-grub
sudo reboot
```

### Configuration to permanently load vfio-pci Linux kernel module

```conf
#/etc/modules-load.d/vfio-pci.conf
vfio-pci
```

### For Mellanox ConnectX Series

Driver installation is required from the following link:

https://network.nvidia.com/products/ethernet-drivers/linux/mlnx_en/

During installation, run `./install --dpdk`.
**The option `--dpdk` is mandatory.**

Comment out the following settings in sdplane.conf as they are not needed:

```conf
#set device {pcie-id} driver unbound
#set device {pcie-id} driver {driver-name} driver_override
#set device {pcie-id} driver {driver-name} bind
```

For Mellanox NICs, you need to run the `update port status` command to refresh port information.

### How to check PCIe bus numbers

You can use the dpdk-devbind.py command in DPDK to check the PCIe bus numbers of NICs:

```bash
> dpdk-devbind.py -s

Network devices using kernel driver
===================================
0000:04:00.0 'NetXtreme BCM5720 Gigabit Ethernet PCIe 165f' numa_node=0 if=eno8303 drv=tg3 unused= *Active*
0000:b1:00.0 'MT27800 Family [ConnectX-5] 1017' numa_node=1 if=enp177s0np0 drv=mlx5_core unused= *Active*
```

### Worker thread ordering in configuration file

If configuring `rib-manager`, `neigh-manager`, and `netlink-thread` workers, they must be configured in this order if they are used.

### DPDK initialization

Only one command that calls `rte_eal_init()` should be invoked from the configuration file. The `rte_eal_init()` function is called by commands such as `rte_eal_init`, `pktgen init`, `l2fwd init`, and `l3fwd init`.
