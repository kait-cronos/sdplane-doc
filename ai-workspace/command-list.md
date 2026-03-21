# sdplane-oss CLI コマンド一覧（調査結果）

注意: module/ 配下 (l2fwd, l3fwd, pktgen, common) は全て除外

## コマンド登録パターン
- マクロ: `DEFINE_COMMAND`, `CLI_COMMAND2`, `ALIAS_COMMAND`
- 登録: `INSTALL_COMMAND2(cmdset, cmdname)`
- 定義: `lib/sdplane/command.h`
- 初期化: `sdplane_cmd_init()` in `sdplane/sdplane.c`

## ソースファイル別コマンド一覧

### DPDK lcore 管理 (dpdk_lcore_cmd.c)
- `(set|reset|start|restart) worker lcore <0-16>`
- `(start|stop|reset|restart) worker lcore (<0-16>|all)`
- `show worker`
- `set mempool`
- `set rte_eal argv`
- `rte_eal_init`
- `rte_eal_init argv-list <0-7>`

### DPDK ポート管理 (dpdk_port_cmd.c)
- `(start|stop|reset) port (|<0-16>|all)`
- `show port (|<0-16>|all)`
- `show port <0-16> get-info`
- `show port statistics (|pps|total|bps|Bps|total-bytes)`
- `show port (<0-16>|all) promiscuous`
- `show port (<0-16>|all) flowcontrol`
- `set port (<0-16>|all) promiscuous (enable|disable)`
- `set port (<0-16>|all) dev-configure <0-64> <0-64>`
- `set port (<0-16>|all) (nrxdesc|ntxdesc) <0-16384>`
- `set port (<0-16>|all) (nrxq|ntxq) <0-256>`
- `set port (<0-16>|all) mtu <0-16384>`
- `set port (<0-16>|all) link (up|down)`
- `set port (<0-16>|all) flowcontrol (rx|tx|autoneg|send-xon|fwd-mac-ctrl) (on|off)`

### RIB・ルーティング・スイッチング (rib.c) ※ソースコード検証済み
- `show rib`
- `show rib (ipv4|ipv6) route`
- `show fib (ipv4|ipv6) route`
- `show rib capture-if`
- `show rib router-if`
- `show rib vswitch` （エイリアス: `show vswitch`）
- `show rib vswitch-link`
- `show rib nexthop-group`
- `show rib nexthop-pool`
- `show fdb`
- `show neighbor (|ipv4|ipv6) (|<WORD>)`
- `set vswitch <1-4094> vlan <1-4094>`
- `set vswitch <1-4094> capture-if <WORD>`
- `set vswitch <1-4094> router-if <WORD>`
- `set vswitch <1-4094> router-if <WORD> vlan-id <0-4094>`
- `set vswitch <1-4094> router-if <WORD> hwaddr <WORD>`
- `set vswitch <1-4094> port <0-7> (tagged|untag)`
- `set vswitch <1-4094> port <0-7> tag swap <1-4094>`
- `no set vswitch <1-4094>`
- `no set vswitch <1-4094> port <0-7>`
- `no set router-if <WORD>`
- `no set capture-if <WORD>`

### RTE Flow ハードウェアフロールール (rte_flow_support.c)
- `set rte-flow port <0-127> pattern <0-15> action <0-15>`
- `set rte-flow-pattern <0-15> index <0-15> end`
- `set rte-flow-pattern <0-15> index <0-15> ether (any|zero)`
- `set rte-flow-pattern <0-15> index <0-15> ipv4 (any|zero)`
- `set rte-flow-pattern <0-15> index <0-15> ipv4 (src|dst) A.B.C.D`
- `set rte-flow-pattern <0-15> index <0-15> ipv4 proto <0-255>`
- `set rte-flow-pattern <0-15> index <0-15> vlan (any|zero)`
- `set rte-flow-pattern <0-15> index <0-15> vlan id <1-4094>`
- `set rte-flow action <0-15> index <0-15> (drop|end)`
- `set rte-flow action <0-15> index <0-15> queue <0-127>`
- `show rte-flow pattern (|<0-15>)`
- `show rte-flow action`

### NetTLP リモートDMA (nettlp.c)
- `show nettlp`
- `show nettlp psmem (|<WORD>)`
- `set nettlp ether (local-addr|remote-addr) <WORD>`
- `set nettlp ipv4 (local-addr|remote-addr) A.B.C.D`
- `set nettlp udp (src-port|dst-port) <0-65535>`
- `set nettlp pci-tag <0-255>`
- `set nettlp tx-portid <0-128>`
- `set nettlp bus-number <0-65535> device-number <0-65535>`
- `set nettlp memory-address <WORD>`
- `set nettlp payload-size <0-4096>`
- `set nettlp max-payload-size <0-4096>`
- `set nettlp payload-string <LINE>`
- `set nettlp psmem-address <WORD>`
- `set nettlp psmem-size (<WORD>|256M)`
- `nettlp-send (dma-write|dma-read)`

### SRv6 (srv6_cmd.c)
- `set srv6 local-sid <WORD> (|<0-15>)`
- `show srv6 local-sid`

### TAP/キャプチャ (tap_cmd.c)
- `set tap capture ifname <WORD>`
- `(set|no|unset) tap capture persistent`

### Netlink Hook (netlink_hook.c) ※ソースコード検証済み
- `set netlink-hook <0-3> (ipv4|ipv6) ifaddr (new|del) ifname <WORD> argv-list <0-7>`
- `show netlink-hook (|<0-3>)`

### ワーカースレッド (lthread_main.c)
- `set worker lthread stat-collector`
- `set worker lthread rib-manager`
- `set worker lthread netlink-thread`
- `set worker lthread neigh-manager`
- `set worker lthread dhcp-server`
- `set worker lthread l3-tap-handler`

### スレッド情報 (thread_info.c)
- `show thread`
- `show thread counter`
- `set thread <0-128> port <0-128> queue <0-128>`

### キュー設定 (queue_config.c) ※ソースコード検証済み
- `update port status`
- `set thread <0-128> port <0-128> queue <0-128>`
- `show thread qconf`

### デバイス管理 (dpdk_devbind.c) ※ソースコード検証済み
- `show devices`
- `set device <WORD> driver (ixgbe|igb|igc|uio_pci_generic|igb_uio|vfio-pci|unbound) (|bind|driver_override)`

### その他 (sdplane.c 等)
- `show version`
- `show loop-count (console|vty-shell|l2fwd) (pps|total)`
- `show rcu`
- `show mempool`
- `set locale (C|C.utf8|en_US.utf8|POSIX)`
- `sleep <0-300>`
- `show argv-list (|<0-7>)`
- `set argv-list <0-7> <WORD>`
- `show debugging sdplane`
- `update port link-status`

## 除外対象
- module/ 配下全て: l2fwd, l3fwd, pktgen, common
