# 使用qemu调试openwrt固件

本文章使用Ubuntu focal 20.04 LTS作为宿主机qemu版本为5.2.0，以x86_64构架openwrt为例。

## 直接启动固件

因为宿主机也是x86_64构架且支持硬件虚拟化，所以这里启动kvm加速，openwrt-x86-64-generic-squashfs-combined.img是编译好的固件。

```shell
qemu-system-x86_64 --enable-kvm openwrt-x86-64-generic-squashfs-combined.img
```

可以看见openwrt成功运行并打印log。

```shell
   Booting `OpenWrt' Booting `OpenWrt

'

[    0.000000] Linux version 5.4.52 (fw867@koolshare.cn) (gcc version 8.4.0 (OpenWrt GCC 8.4.0 r13620-533ee970da)) #0 SMP Thu Jul 23 12:16:45 2020
[    0.000000] Command line: BOOT_IMAGE=/boot/vmlinuz root=PARTUUID=09cf104a-02 rootwait console=tty0 console=ttyS0,115200n8 noinitrd
[    0.000000] x86/fpu: x87 FPU will use FXSAVE
[    0.000000] BIOS-provided physical RAM map:
[    0.000000] BIOS-e820: [mem 0x0000000000000000-0x000000000009fbff] usable
[    0.000000] BIOS-e820: [mem 0x000000000009fc00-0x000000000009ffff] reserved
[    0.000000] BIOS-e820: [mem 0x00000000000f0000-0x00000000000fffff] reserved
[    0.000000] BIOS-e820: [mem 0x0000000000100000-0x0000000007fdffff] usable
[    0.000000] BIOS-e820: [mem 0x0000000007fe0000-0x0000000007ffffff] reserved
[    0.000000] BIOS-e820: [mem 0x00000000feffc000-0x00000000feffffff] reserved
[    0.000000] BIOS-e820: [mem 0x00000000fffc0000-0x00000000ffffffff] reserved
[    0.000000] NX (Execute Disable) protection: active
[    0.000000] SMBIOS 2.8 present.
[    0.000000] DMI: QEMU Standard PC (i440FX + PIIX, 1996), BIOS rel-1.14.0-0-g155821a1990b-prebuilt.qemu.org 04/01/2014
[    0.000000] Hypervisor detected: KVM
[    0.000000] kvm-clock: Using msrs 4b564d01 and 4b564d00
[    0.000000] kvm-clock: cpu 0, msr 21ea001, primary cpu clock
[    0.000000] kvm-clock: using sched offset of 1486715385 cycles
[    0.000003] clocksource: kvm-clock: mask: 0xffffffffffffffff max_cycles: 0x1cd42e4dffb, max_idle_ns: 881590591483 ns
[    0.000006] tsc: Detected 3693.044 MHz processor
[    0.001576] last_pfn = 0x7fe0 max_arch_pfn = 0x400000000
[    0.001634] x86/PAT: Configuration [0-7]: WB  WC  UC- UC  WB  WP  UC- WT  
[    0.003922] found SMP MP-table at [mem 0x000f5ab0-0x000f5abf]
[    0.004170] ACPI: Early table checksum verification disabled
[    0.004174] ACPI: RSDP 0x00000000000F58D0 000014 (v00 BOCHS )
[    0.004189] ACPI: RSDT 0x0000000007FE1550 000034 (v01 BOCHS  BXPCRSDT 00000001 BXPC 00000001)
[    0.004193] ACPI: FACP 0x0000000007FE1404 000074 (v01 BOCHS  BXPCFACP 00000001 BXPC 00000001)
[    0.004199] ACPI: DSDT 0x0000000007FE0040 0013C4 (v01 BOCHS  BXPCDSDT 00000001 BXPC 00000001)
[    0.004201] ACPI: FACS 0x0000000007FE0000 000040
[    0.004204] ACPI: APIC 0x0000000007FE1478 000078 (v01 BOCHS  BXPCAPIC 00000001 BXPC 00000001)
[    0.004206] ACPI: HPET 0x0000000007FE14F0 000038 (v01 BOCHS  BXPCHPET 00000001 BXPC 00000001)
[    0.004209] ACPI: WAET 0x0000000007FE1528 000028 (v01 BOCHS  BXPCWAET 00000001 BXPC 00000001)
[    0.004420] Zone ranges:
[    0.004423]   DMA      [mem 0x0000000000001000-0x0000000000ffffff]
[    0.004425]   DMA32    [mem 0x0000000001000000-0x0000000007fdffff]
[    0.004426]   Normal   empty
[    0.004427] Movable zone start for each node
[    0.004428] Early memory node ranges
[    0.004429]   node   0: [mem 0x0000000000001000-0x000000000009efff]
[    0.004431]   node   0: [mem 0x0000000000100000-0x0000000007fdffff]
[    0.004434] Zeroed struct page in unavailable ranges: 130 pages
[    0.004435] Initmem setup node 0 [mem 0x0000000000001000-0x0000000007fdffff]
[    0.005180] ACPI: PM-Timer IO Port: 0x608
[    0.005193] ACPI: LAPIC_NMI (acpi_id[0xff] dfl dfl lint[0x1])
[    0.005229] IOAPIC[0]: apic_id 0, version 17, address 0xfec00000, GSI 0-23
[    0.005232] ACPI: INT_SRC_OVR (bus 0 bus_irq 0 global_irq 2 dfl dfl)
[    0.005234] ACPI: INT_SRC_OVR (bus 0 bus_irq 5 global_irq 5 high level)
[    0.005235] ACPI: INT_SRC_OVR (bus 0 bus_irq 9 global_irq 9 high level)
[    0.005236] ACPI: INT_SRC_OVR (bus 0 bus_irq 10 global_irq 10 high level)
[    0.005237] ACPI: INT_SRC_OVR (bus 0 bus_irq 11 global_irq 11 high level)
.........
[    8.104817] NET: Registered protocol family 24
[    8.106891] PPTP driver version 0.8.5
[    8.109690] usbcore: registered new interface driver r8152
[    8.114840] sky2: driver version 1.30
[    8.116913] tulip: Linux Tulip driver version 1.1.15-NAPI (Feb 27, 2007)
[    8.118984] usbcore: registered new interface driver ums-alauda
[    8.121039] usbcore: registered new interface driver ums-cypress
[    8.122997] usbcore: registered new interface driver ums-datafab
[    8.124969] usbcore: registered new interface driver ums-freecom
[    8.126806] usbcore: registered new interface driver ums-isd200
[    8.128691] usbcore: registered new interface driver ums-jumpshot
[    8.130555] usbcore: registered new interface driver ums-karma
[    8.132395] usbcore: registered new interface driver ums-sddr09
[    8.134289] usbcore: registered new interface driver ums-sddr55
[    8.136075] usbcore: registered new interface driver ums-usbat
[    8.141246] usbcore: registered new interface driver usblp
[    8.146016] wireguard: WireGuard 1.0.20200712 loaded. See www.wireguard.com for information.
[    8.147770] wireguard: Copyright (C) 2015-2019 Jason A. Donenfeld <Jason@zx2c4.com>. All Rights Reserved.
[    8.212805] xt_ndpi v1.2 ndpi 3.3.0 IPv6=YES debug_message=no
[    8.212805]  BT: hash_size 0k, hash_expiation 0 sec, log_size 128kb
[    8.212805]  sizeof hash_ip4p_node=44 id_struct=256
[    8.212805]  flow_struct=2264 packet_struct=1416
[    8.212805]    flow_tcp_struct=96 flow_udp_struct=36 int_one_line_struct=16
[    8.212805]  ndpi_ip_addr_t=16 ndpi_protocol=4 nf_ct_ext_ndpi=176
[    8.212805]  flow_info=112 spinlock_t=4  NF_EXT_ID 7
[    8.222766] xt_ndpi: MAX_PROTOCOLS 320 LAST_PROTOCOL 253
[    8.224190] xt_ndpi: flow acctounting OFF
[    8.258639] xt_time: kernel timezone is -0000
[    8.261218] usbcore: registered new interface driver asix
[    8.263491] usbcore: registered new interface driver ax88179_178a
[    8.295036] l2tp_ppp: PPPoL2TP kernel driver, V2.0
[    8.297720] PCLMULQDQ-NI instructions are not detected.
[    8.299826] kmodloader: 3 modules could not be probed
[    8.301340] kmodloader: - crc32-pclmul - 0
[    8.302581] kmodloader: - crct10dif-pclmul - 0
[    8.303918] kmodloader: - ghash-clmulni-intel - 0
[    9.462693] ip_local_port_range: prefer different parity for start/end values.
[   10.091483] xt_FULLCONENAT: RFC3489 Full Cone NAT module
[   10.091483] xt_FULLCONENAT: Copyright (C) 2018 Chion Tang <tech@chionlab.moe>
[   10.671267] 8021q: adding VLAN 0 to HW filter on device eth0
[   10.704309] br-lan: port 1(eth0) entered blocking state
[   10.705797] br-lan: port 1(eth0) entered disabled state
[   10.759109] e1000: eth0 NIC Link is Up 1000 Mbps Full Duplex, Flow Control: RX
[   10.805954] br-lan: port 1(eth0) entered blocking state
[   10.807458] br-lan: port 1(eth0) entered forwarding state
[   10.847259] IPv6: ADDRCONF(NETDEV_CHANGE): br-lan: link becomes ready



BusyBox v1.31.1 () built-in shell (ash)

  _______                     ________        __
 |       |.-----.-----.-----.|  |  |  |.----.|  |_
 |   -   ||  _  |  -__|     ||  |  |  ||   _||   _|
 |_______||   __|_____|__|__||________||__|  |____|
          |__| W I R E L E S S   F R E E D O M
 -----------------------------------------------------
 Openwrt Koolshare mod V2.36, r14941-67f6fa0a30 by fw867
 -----------------------------------------------------

root@Openwrt:/# 
```

## 网桥配置

openwrt经常需要调试和网络相关的功能，所以需要虚拟机和物理机之间网络可以互通而qemu默认的网络配置虚拟机和物理机之间无法之间互通，这时应该配置网桥来使虚拟机和物理机的网络能够互相联通。

**注意**：不同发行版的Linux网桥配置方法有可能不一样，这里仅介绍Ubuntu focal 20.04 LTS。

Ubuntu focal 20.04 LTS使用netplan来管理网络，编辑/etc/netplan/xxx.yaml即可配置网络，这里贴出本人物理机上的网桥配置：

```shell
# Let NetworkManager manage all devices on this system
network:
        ethernets:
                eno1:
                        dhcp4: true
        bridges:
                br0:
                        interfaces: [eno1]
                        dhcp4: false
                        addresses: [192.168.1.192/24]
                        gateway4: 192.168.1.2
                        nameservers:
                                addresses: [192.168.1.2,8.8.8.8]
        version: 2
```

eno1是物理机上的物理网卡，br0为创建的网桥，配置完成后使用`sudo netplan apply`命令可以应用配置，以下是应用后我的物理机`ifconfig`输出的log:

```shell
br0: flags=4163<UP,BROADCAST,RUNNING,MULTICAST>  mtu 1500
        inet 192.168.1.192  netmask 255.255.255.0  broadcast 192.168.1.255
        inet6 fdd5:36d5:eb21:0:b62e:99ff:feea:18d2  prefixlen 64  scopeid 0x0<global>
        inet6 fe80::b62e:99ff:feea:18d2  prefixlen 64  scopeid 0x20<link>
        ether b4:2e:99:ea:18:d2  txqueuelen 1000  (Ethernet)
        RX packets 1493623  bytes 2479701090 (2.4 GB)
        RX errors 0  dropped 0  overruns 0  frame 0
        TX packets 1013030  bytes 91124015 (91.1 MB)
        TX errors 0  dropped 0 overruns 0  carrier 0  collisions 0

eno1: flags=4163<UP,BROADCAST,RUNNING,MULTICAST>  mtu 1500
        ether b4:2e:99:ea:18:d2  txqueuelen 1000  (Ethernet)
        RX packets 1925874  bytes 2579666834 (2.5 GB)
        RX errors 0  dropped 0  overruns 0  frame 0
        TX packets 1122528  bytes 107627799 (107.6 MB)
        TX errors 0  dropped 0 overruns 0  carrier 0  collisions 0

lo: flags=73<UP,LOOPBACK,RUNNING>  mtu 65536
        inet 127.0.0.1  netmask 255.0.0.0
        inet6 ::1  prefixlen 128  scopeid 0x10<host>
        loop  txqueuelen 1000  (Local Loopback)
        RX packets 30575  bytes 3431746 (3.4 MB)
        RX errors 0  dropped 0  overruns 0  frame 0
        TX packets 30575  bytes 3431746 (3.4 MB)
        TX errors 0  dropped 0 overruns 0  carrier 0  collisions 0
```

可以看见有个br0网桥出现。

### 在qemu上使用网桥

```shell
sudo qemu-system-x86_64 --enable-kvm openwrt-x86-64-generic-squashfs-combined.img -device e1000,netdev=brdev0 -netdev bridge,id=brdev0,br=br0
```

这条命令模拟了一个e1000网卡桥接到物理机的br0，br0相当于一个交换机。

在openwrt的/etc/config/network文件中配置好ip然后重启虚拟机即可实现物理机与虚拟机的网络互通。