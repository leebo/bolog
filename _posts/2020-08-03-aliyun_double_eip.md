---
layout: post
title: aliyun绑定弹性网卡实现双IP
date: 2020-07-29
summary: aliyun eip ecs
categories: work
---

# 阿里云绑定弹性 ip 实现双 ip

1. 建立弹性网卡
   网络与安全里面选择弹性网卡
   ![image.png](https://i.loli.net/2020/05/29/7Mdbq3RfVnYyJP6.png)
   选择默认的交换机 安全组
   ![image.png](https://i.loli.net/2020/05/29/XTObtaiZroGcmqQ.png)
   这里就是建立好的辅助网卡 还有公网 ip
   ![image.png](https://i.loli.net/2020/05/29/N4iMJZDVdXQae2l.png)
2. 建立弹性公网 ip
   ![image.png](https://i.loli.net/2020/05/29/D5lvWNaweKjL3VX.png)
   选择按流量计费 选择最大带宽（这里每个月费用是 14.4 公网 IP 使用费+公网流量费 0.8 元/G） 可以买共享流量包 0.74/G +闲时 0.55/G
   ![image.png](https://i.loli.net/2020/05/29/IXs2ECh4y1JbveG.png)
3. ecs 服务器绑定弹性网卡
   ![image.png](https://i.loli.net/2020/05/29/iOXRoWHMDJYsrhl.png)
   ![image.png](https://i.loli.net/2020/05/29/Y4jXOBzfrqLQtwb.png)
   选择你刚才新建的弹性网卡 绑定

4. 弹性网卡绑定弹性公网 ip
   ![image.png](https://i.loli.net/2020/05/29/Ft8AvwlDJZrzVuo.png)
   绑定弹性公网 ip

5. 进入服务器配置 eth1 网卡 dhcp
   ssh 登录服务器

```
ifconfig -a
```

查看网卡 找到 eth1
![image.png](https://i.loli.net/2020/05/29/VpuxRK5QE26IMOn.png)
里面有网卡 mac 地址
ether 00:16:3e:0a:e0:0b
打开配置文件

```
sudo vim /etc/netplan/50-cloud-init.yaml
```

eth0 后加入 eth1 的配置

```
# This file is generated from information provided by
# the datasource.  Changes to it will not persist across an instance.
# To disable cloud-init's network configuration capabilities, write a file
# /etc/cloud/cloud.cfg.d/99-disable-network-config.cfg with the following:
# network: {config: disabled}
network:
    version: 2
    ethernets:
        eth0:
            dhcp4: true
            match:
                macaddress: 00:16:3e:08:c1:6d
            set-name: eth0
        eth1:
            dhcp4: true
            match:
                macaddress: 00:16:3e:0a:e0:0b
            set-name: eth1
```

使配置生效

```
 sudo netplan apply
```

现在你服务器就有两个 ip 地址了 ping 一下试试吧
