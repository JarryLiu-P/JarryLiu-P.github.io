---
title: systemctl命令
date: 2019-08-07 11:30:30
tags:
    - Linux
    - systemctl
---

### 简介
`systemd` 是由红帽公司的一名叫做 `Lennart Poettering` 的员工开发，`systemd` 是 `Linux` 系统中最新的初始化系统（init）,它主要的设计目的是克服 `Sys V` 固有的缺点，提高系统的启动速度，`systemd` 和 `upstart` 是竞争对手，`ubantu` 上使用的是 `upstart` 的启动方式，`centos` `7`上使用 `systemd` 替换了 `Sys V`，`Systemd` 目录是要取代 `Unix` 时代依赖一直在使用的 init 系统，兼容 `Sys V` 和 `LSB` 的启动脚本，而且能够在进程启动中更有效地引导加载服务。
`system`：系统启动和服务器守护进程管理器，负责在系统启动或运行时，激活系统资源，服务器进程和其他进程，根据管理，字母 d 是守护进程（daemon）的缩写，`systemd` 这个名字的含义就是它要守护整个系统。
监视和控制systemd的主要命令是 `systemctl`。

### systemd 新特性
* 系统引导时实现服务并行启动
* 按需启动守护进程
* 同时采用 `socket` 式与 `D-Bus` 总线式激活服务
* 系统状态快照和恢复
* 利用 `Linux` 的 `cgroups` 监视进程
* 维护挂载点和自动挂载点
* 各服务间基于依赖关系进行精密控制

### 示例
* #### 检查systemd是否正在运行
```
ps -eaf | grep systemd
```
`systemd` 作为父守护进程运行（PID = 1）。 在上面的命令ps中使用（-e）选择所有进程，（-a）选择除会话前导之外的所有进程和（-f）选择完整格式列表（即-eaf）。

* #### 分析systemd启动过程
```
systemd-analyze
```
exp: Startup finished in 714ms (kernel) + 1.483s (initrd) + 8.458s (userspace) = 10.656s

* #### 分析每个进程在引导时花费的时间
```
systemd-analyze blame
```
exp:
2.682s network.service
2.221s kdump.service
1.773s cloud-init-local.service
1.302s tuned.service
910ms YDService.service
869ms cloud-config.service
793ms vdo.service
650ms cloud-final.service
533ms polkit.service
526ms cloud-init.service
411ms lvm2-monitor.service
405ms rhel-dmesg.service
402ms ntpd.service
399ms acpid.service
348ms systemd-journal-flush.service
337ms nginx.service
316ms dev-vda1.device


* #### 分析启动时的关键链
```
systemd-analyze critical-chain
```
exp:
The time after the unit is active or started is printed after the "@" character.
The time the unit takes to start is printed after the "+" character.
multi-user.target @7.016s
└─tuned.service @5.713s +1.302s
  └─network.target @5.708s
    └─network.service @3.025s +2.682s
      └─network-pre.target @3.024s
        └─cloud-init-local.service @1.250s +1.773s
          └─basic.target @1.243s
            └─sockets.target @1.243s
              └─dbus.socket @1.242s
                └─sysinit.target @1.242s
                  └─systemd-update-utmp.service @1.225s +15ms
                    └─auditd.service @1.012s +212ms
                      └─systemd-tmpfiles-setup.service @991ms +20ms
                        └─rhel-import-state.service @902ms +88ms
                          └─local-fs.target @897ms
                            └─local-fs-pre.target @897ms
                              └─lvm2-monitor.service @485ms +411ms
                                └─lvm2-lvmetad.service @557ms
                                  └─lvm2-lvmetad.socket @485ms
                                    └─-.slice

* #### 检查单元或者服务是否启用
```
systemctl is-enabled nginx.service
```

* #### 检查单元或服务是否正在运行
```
systemctl status nginx.service
systemctl status nginx
```

* #### 列出所有服务（包括启用和禁用）
```
systemctl list-unit-files --type=service
```

* #### 启动，重新启动，停止，重新加载服务
```
systemctl start nginx.service
systemctl restart nginx.service
systemctl stop nginx.service
systemctl reload nginx.service
```

* #### 如何在引导时激活服务并启用或禁用服务（系统引导时自动启动服务）
```
systemctl is-active nginx.service
systemctl enable nginx.service
systemctl disable nginx.service
```

* #### 如何使用systemctl命令终止服务
```
systemctl kill nginx
```

* #### 检查服务的所有配置详细信息
```
systemctl show nginx
```

* #### 分析服务的关键链（nginx）
```
systemd-analyze critical-chain nginx.service
```
exp:
The time after the unit is active or started is printed after the "@" character.
The time the unit takes to start is printed after the "+" character.

nginx.service +337ms
└─network.target @5.708s
  └─network.service @3.025s +2.682s
    └─network-pre.target @3.024s
      └─cloud-init-local.service @1.250s +1.773s
        └─basic.target @1.243s
          └─sockets.target @1.243s
            └─dbus.socket @1.242s
              └─sysinit.target @1.242s
                └─systemd-update-utmp.service @1.225s +15ms
                  └─auditd.service @1.012s +212ms
                    └─systemd-tmpfiles-setup.service @991ms +20ms
                      └─rhel-import-state.service @902ms +88ms
                        └─local-fs.target @897ms
                          └─local-fs-pre.target @897ms
                            └─lvm2-monitor.service @485ms +411ms
                              └─lvm2-lvmetad.service @557ms
                                └─lvm2-lvmetad.socket @485ms
                                  └─-.slice

* #### 获取服务的依赖项列表（nginx）
```
systemctl list-dependencies nginx.service
```
exp:
nginx.service
● ├─-.mount
● ├─system.slice
● └─basic.target
●   ├─rhel-dmesg.service
●   ├─selinux-policy-migrate-local-changes@targeted.service
●   ├─paths.target
●   ├─slices.target
●   │ ├─-.slice
●   │ └─system.slice
●   ├─sockets.target
●   │ ├─dbus.socket
●   │ ├─dm-event.socket
●   │ ├─rpcbind.socket
●   │ ├─systemd-initctl.socket
●   │ ├─systemd-journald.socket
●   │ ├─systemd-shutdownd.socket
●   │ ├─systemd-udevd-control.socket
●   │ └─systemd-udevd-kernel.socket
●   ├─sysinit.target
●   │ ├─dev-hugepages.mount
●   │ ├─dev-mqueue.mount
●   │ ├─kmod-static-nodes.service
●   │ ├─lvm2-lvmetad.socket
●   │ ├─lvm2-lvmpolld.socket
●   │ ├─lvm2-monitor.service
●   │ ├─plymouth-read-write.service
●   │ ├─plymouth-start.service
●   │ ├─proc-sys-fs-binfmt_misc.automount
●   │ ├─rhel-autorelabel.service
●   │ ├─rhel-domainname.service
●   │ ├─rhel-import-state.service
●   │ ├─rhel-loadmodules.service
●   │ ├─sys-fs-fuse-connections.mount
●   │ ├─sys-kernel-config.mount
●   │ ├─sys-kernel-debug.mount
●   │ ├─systemd-ask-password-console.path
●   │ ├─systemd-binfmt.service
●   │ ├─systemd-firstboot.service
●   │ ├─systemd-hwdb-update.service
●   │ ├─systemd-journal-catalog-update.service
●   │ ├─systemd-journal-flush.service
●   │ ├─systemd-journald.service
●   │ ├─systemd-machine-id-commit.service
●   │ ├─systemd-modules-load.service
●   │ ├─systemd-random-seed.service
●   │ ├─systemd-sysctl.service
●   │ ├─systemd-tmpfiles-setup-dev.service
●   │ ├─systemd-tmpfiles-setup.service
●   │ ├─systemd-udev-trigger.service
●   │ ├─systemd-udevd.service
●   │ ├─systemd-update-done.service
●   │ ├─systemd-update-utmp.service
●   │ ├─systemd-vconsole-setup.service
●   │ ├─cryptsetup.target
●   │ ├─local-fs.target
●   │ │ ├─-.mount
●   │ │ ├─rhel-readonly.service
●   │ │ ├─systemd-fsck-root.service
●   │ │ └─systemd-remount-fs.service
●   │ └─swap.target
●   └─timers.target
●     └─systemd-tmpfiles-clean.timer

### 参考文章
[systemctl 命令详解及使用教程](https://linux265.com/news/3385.html)
[CentOS7下Systemctl详解](https://www.jianshu.com/p/828a40ae4bdd)