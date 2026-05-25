# OpenWRT-CI 固件精简记录

> 本配置文件用于编译定制固件，针对 JDCloud RE-SS-01 (IPQ60XX) 设备优化。

## 精简原则

1. 够用就好，不需要的就不编译
2. 保留核心功能：WiFi、USB存储、SMB共享、科学上网、DNS
3. 删除占空间但不常用的组件

---

## 已移除的组件 (2026-05-25)

### Docker 相关 (~25个包)
```bash
# Docker 引擎及相关
CONFIG_PACKAGE_dockerd=n
CONFIG_PACKAGE_docker=n
CONFIG_PACKAGE_docker-compose=n
CONFIG_PACKAGE_luci-app-dockerman=n
CONFIG_PACKAGE_luci-lib-docker=n

# Docker 依赖的内核模块
CONFIG_PACKAGE_kmod-cgroup=n
CONFIG_PACKAGE_kmod-cgroup-swap=n
CONFIG_PACKAGE_kmod-cgroup-pids=n
CONFIG_PACKAGE_kmod-cgroup-freezer=n
CONFIG_PACKAGE_kmod-cgroup-net-prio=n
CONFIG_PACKAGE_kmod-cgroup-bpf=n
CONFIG_PACKAGE_kmod-ikconfig=n
CONFIG_PACKAGE_kmod-ikconfig-procfs=n
CONFIG_PACKAGE_kmod-nf-ipvs=n
CONFIG_PACKAGE_kmod-nf-conntrack-netlink=n
CONFIG_PACKAGE_kmod-ipt-conntrack=n
CONFIG_PACKAGE_kmod-ipt-extra=n
CONFIG_PACKAGE_kmod-ipt-raw=n
CONFIG_PACKAGE_kmod-macvlan=n
CONFIG_PACKAGE_kmod-md-mod=n
CONFIG_PACKAGE_kmod-md-raid0=n
CONFIG_PACKAGE_kmod-md-raid1=n
CONFIG_PACKAGE_kmod-md-raid10=n
CONFIG_PACKAGE_kmod-md-raid456=n
```
**原因**: 路由器只有 920MB 内存，跑 Docker 太吃力。pansou 已改为 standalone 二进制运行。

### 磁盘工具 (6个)
```bash
CONFIG_PACKAGE_cfdisk=n    # 磁盘分区
CONFIG_PACKAGE_cgdisk=n    # 磁盘分区
CONFIG_PACKAGE_fdisk=n     # 磁盘分区
CONFIG_PACKAGE_gdisk=n     # 磁盘分区
CONFIG_PACKAGE_sfdisk=n    # 磁盘分区
CONFIG_PACKAGE_sgdisk=n    # 磁盘分区
```
**原因**: 小众工具，U盘/硬盘在电脑上先分好再插路由

### 存储/设备工具
```bash
CONFIG_PACKAGE_coremark=n       # CPU跑分，编译完无用
CONFIG_PACKAGE_libimobiledevice=n  # iOS设备管理，路由器用不上
CONFIG_PACKAGE_usb-modeswitch=n   # 3G/4G USB猫，暂时不需要
CONFIG_PACKAGE_mmc-utils=n         # EMMC工具，路由器用不上
CONFIG_PACKAGE_nand-utils=n         # NAND工具，路由器用不上
```

### 网络/测试工具
```bash
CONFIG_PACKAGE_iperf3=n          # 网速测试，普通人用不上
CONFIG_PACKAGE_kmod-usb-audio=n   # USB声卡，路由器用不上
CONFIG_PACKAGE_kmod-usb-ohci=n    # USB1.1，旧设备用不上
```

### 主题
```bash
# argon                      # 删掉
# kucat                       # 删掉
# kucat-config               # 删掉
```
**保留**: aurora + aurora-config

### Packages.sh 中的冗余插件
```bash
# momo         # 删，代理重复
# nikki         # 删，代理重复
# openclash     # 删，代理重复
# passwall      # 删，代理重复
# passwall2     # 删，代理重复
# diskman       # 删，小众
# fancontrol    # 删，JDCloud设备用不上
# gecoosac      # 删，不认识
# mosdns        # 删，有smartdns了
# netspeedtest  # 删，用不上
# openlist2     # 删，不认识
# qbittorrent   # 删，Docker没了没法跑
# qmodem        # 删，不认识
# viking        # 删，不认识
```

---

## 保留的核心功能

### 网络
- WiFi (ath11k)
- USB 网络 (kmod-usb-net-ipheth) - iPhone 网络共享
- USB 存储 (kmod-usb-storage)
- WireGuard (kmod-wireguard)
- HomeProxy (sing-box) - 科学上网

### 插件
- luci-app-samba4 - SMB文件共享
- luci-app-homeproxy - 代理
- luci-app-smartdns - 智能DNS
- luci-app-autoreboot - 自动重启
- luci-app-partexp - 文件传输
- luci-app-upnp - UPnP

### 系统
- cpufreq (CPU频率调节)
- openssh-sftp-server
- curl, openssl-util

---

## 可选保留（根据需要）
```bash
CONFIG_PACKAGE_usbmuxd=y           # iPhone USB共享需要
CONFIG_PACKAGE_luci-app-tailscale=y  # 内网穿透（如果需要）
CONFIG_PACKAGE_ddns-go=y              # 动态DNS（如果需要）
CONFIG_PACKAGE_easytier=y             # 内网穿透（如果需要）
CONFIG_PACKAGE_vnt=y                  # 内网穿透（如果需要）
```

---

## 下次编译前检查清单

- [ ] WiFi 是否正常
- [ ] USB 存储挂载是否正常
- [ ] SMB 共享是否正常
- [ ] 科学上网是否正常
- [ ] DNS 是否正常
- [ ] 文件传输是否正常
- [ ] iPhone USB 网络共享是否正常（如果需要）

---

## 编译命令

```bash
# 手动触发编译
gh workflow run QCA-ALL.yml --repo nak1li/OpenWRT-CI

# 查看编译状态
gh run list --repo nak1li/OpenWRT-CI
```