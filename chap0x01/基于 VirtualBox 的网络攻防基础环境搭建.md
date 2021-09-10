





#  基于 VirtualBox 的网络攻防基础环境搭建

## 实验目的

- 掌握 VirtualBox 虚拟机的安装与使用；
- 掌握 VirtualBox 的虚拟网络类型和按需配置；
- 掌握 VirtualBox 的虚拟硬盘多重加载；

## 实验环境

以下是本次实验需要使用的网络节点说明和主要软件举例：

- VirtualBox 虚拟机
- 攻击者主机（Attacker）：Kali-Linux-2021.2
- 网关（Gateway, GW）：Debian 10
- 靶机（Victim）：Debian10 / xp-sp3-base / Kali-Linux-2021.2

## 实验要求

- 虚拟硬盘配置成多重加载

- 搭建满足如下拓扑图所示的虚拟机网络拓扑；

![img](https://c4pr1c3.github.io/cuc-ns/chap0x01/attach/chap0x01/media/vb-exp-layout.png)

> 根据实验宿主机的性能条件，可以适度精简靶机数量

- 完成以下网络连通性测试：

  靶机可以直接访问攻击者主机

  攻击者主机无法直接访问靶机

  网关可以直接访问攻击者主机和靶机

  靶机的所有对外上下行流量必须经过网关

  所有节点均可以访问互联网

## 实验步骤

1. 虚拟机配置多重加载

   用老师的链接下载需要的系统镜像。

   在虚拟介质管理器里面选中盘片之后选择多重加载，再新建的时候直接使用多重加载的镜像文件

   ![多重加载](img\多重加载.png)

   ![多重加载1](img\多重加载1.png)

2. 根据要求创建所有需要的虚拟机

   ![虚拟机安装完成](img\虚拟机安装完成.png)

3. 按照实验要求拓扑图配置虚拟机网络

   - 网关配置： 

     - 配置四块网卡分别为"NAT网络"“Host Only”“内部网络（intnet1）”和“内部网络（intnet2）”

     ![gateway-network-card](img\gateway-network-card.png)

     内部网络详细配置

     ![intnet1-details](img\intnet1-details.png)

     Host-Only中DHCP服务器详细配置

     ![host-only-DHCP](img\host-only-DHCP.png)

     开启作为网关的虚拟机，查看各个网卡地址

     ![gateway-network-address](img\gateway-network-address.png)

   - 靶机和攻击者主机网络配置

     按照实验要求网络拓扑配置winXP-Victim-1和kali-Victim-1网络为内部网络“intnet1”

     ![winXP-Victim-1-network](img\winXP-Victim-1-network.png)

     ![kali-victim-1-network](img\kali-victim-1-network.png)

     配置winXP-Victim-2和debian-Victim-1网络为内部网络“intnet2”

     ![winXP-victim-2-network](img\winXP-victim-2-network.png)

     ![debian-victim-2-network](img\debian-victim-2-network.png)

     攻击者主机kali-Attacker为NAT网络

     ![kali-attacker-network](img\kali-attacker-network.png)

     开启各个虚拟机并查看网络地址

     ![winXP-victim-1-network-address](img\winXP-victim-1-network-address.png)

     ![kali-victim-1-network-address](img\kali-victim-1-network-address.png)

     ![winXP-victim-2-network-address](img\winXP-victim-2-network-address.png)

     ![debian-victim-2-network-address](img\debian-victim-2-network-address.png)

     ![kali-attacker-network-address](img\kali-attacker-network-address.png)

     整理出各个虚拟机对应的IP地址如下：

     | 虚拟机名称      | IP地址                                |
     | --------------- | ------------------------------------- |
     | Kali-Attacker   | 10.0.2.15/24（NATNetwork）            |
     | Kali-Victim-1   | 172.16.111.106/24（内部网络 intnet1） |
     | winXP-Victim-1  | 172.16.111.122/24（内部网络 intnet1） |
     | winXP-Victim-2  | 172.16.222.106/24（内部网络 intnet2） |
     | Debian-Victim-2 | 172.16.222.137/24（内部网络 intnet2） |
     | Debian-Gateway  | 10.0.2.5/24 （NATNetwork）            |
     |                 | 192.168.56.113/24 （Host Only）       |
     |                 | 172.16.111.1/24（内部网络 intnet1）   |
     |                 | 172.16.222.1/24（内部网络 intnet2）   |

4. 进行连通性测试

需要注意：将winXP系统的防火墙关掉，否则不能ping通

- [x] 靶机可以直接访问攻击者主机

![connect1](img\connect1.png)

- [x] 攻击者主机无法直接访问靶机

  ![connect2](img\connect2.png)

- [x] 网关可以直接访问攻击者主机和靶机

  ![connect3](img\connect3.png)

- [x] 靶机的所有对外上下行流量必须经过网关

  在网关上安装`tcpdump`，并对对应网卡进行监控。在各个节点上访问互联网，观察捕获到了上下行的所有包。

  ```
  apt insatll tcpdump
  /usr/sbin/tcpdump -i enp0s9 # etc
  ```

![connect4](img\connect4.png)

- [x] 所有节点均可以访问互联网

![connect5](img\connect5.png)

## 参考链接

[2021 秋教学日历 - 传媒信安教学 Wiki (gitee.io)](https://c4pr1c3.gitee.io/cuc-wiki/ns/2021/calendar.html)

[2020-ns-public-LyuLumos/基于 VirtualBox 的网络攻防基础环境搭建.md at ch0x01 · CUCCS/2020-ns-public-LyuLumos (github.com)](https://github.com/CUCCS/2020-ns-public-LyuLumos/blob/ch0x01/ch0x01/基于 VirtualBox 的网络攻防基础环境搭建.md)

[第一章课后实验详解](https://www.bilibili.com/video/BV1CL41147vX?p=12)

