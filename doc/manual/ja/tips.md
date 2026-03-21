[ユーザーガイド](README.md) > Tips

# Tips

**Language:** [English](../en/tips.md) | **日本語**

sdplane-ossの設定・運用に関するヒント集です。

### vfio-pciをNICドライバーに使用する場合はIOMMUが必須

- Intel：Intel VT-d
- AMD：AMD IOMMU / AMD-V

上記をBIOSで有効にする必要があります。
また、GRUBの設定変更が必要な場合があります。

```conf
# /etc/default/grub
GRUB_CMDLINE_LINUX="iommu=pt intel_iommu=on"
```

```bash
sudo update-grub
sudo reboot
```

### vfio-pci Linux Kernel Moduleを永続的にロードする設定

```conf
#/etc/modules-load.d/vfio-pci.conf
vfio-pci
```

### Mellanox ConnectXシリーズの場合

以下のリンクからドライバーのインストールが必要です。

https://network.nvidia.com/products/ethernet-drivers/linux/mlnx_en/

インストール時には、`./install --dpdk` を実行してください。
**オプション `--dpdk` が必須です。**

以下の設定はsdplane.confでは不要なため、コメントアウトしてください。

```conf
#set device {pcie-id} driver unbound
#set device {pcie-id} driver {driver名} driver_override
#set device {pcie-id} driver {driver名} bind
```

メラノックスNICの場合は、ポート情報を更新するために、`update port status` コマンドを実行する必要があります。

### PCIeバス番号の確認方法

DPDKでは、dpdk-devbind.pyコマンドを使用してNICのPCIeバス番号を確認できます。

```bash
> dpdk-devbind.py -s

Network devices using kernel driver
===================================
0000:04:00.0 'NetXtreme BCM5720 Gigabit Ethernet PCIe 165f' numa_node=0 if=eno8303 drv=tg3 unused= *Active*
0000:b1:00.0 'MT27800 Family [ConnectX-5] 1017' numa_node=1 if=enp177s0np0 drv=mlx5_core unused= *Active*
```

### 設定ファイル中のワーカースレッドの順序

`rib-manager`、`neigh-manager`、`netlink-thread` のワーカーを設定する場合、もし使用されるのであれば、この順序で設定されなければいけません。

### DPDK初期化に関して

`rte_eal_init()` を呼ぶコマンドは、設定ファイルからはどれか一つのみ呼び出すのが正しいです。`rte_eal_init()` 関数は、`rte_eal_init`、`pktgen init`、`l2fwd init`、`l3fwd init` などのコマンドから呼ばれます。
