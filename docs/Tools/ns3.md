---
sidebar_position: 1
---


# NS3使用笔记

NS3 是一个离散事件模拟器，你可以理解为是一个 人为模拟现实中时间来实现网络中发生 的各种事件 ，什么意思呢？NS3 就是一个 C++程序，但是它自己有个时钟（并非现实中的 时钟），它会根据设定好的时间来运行事件（函数），从而能够达到我们现实世界中，发生各 种事情所需要耗费的时间。



学习仓库：

使用到的 HPCC 开源源代码地址： https://github.com/alibaba-edu/High-Precision-Congestion-Control

# 一、目录结构

```Plain Text
AUTHORS       examples       src               utils.pyc  wutils.py
bindings      LICENSE        test.py           VERSION    wutils.pyc
build         Makefile       testpy.supp       waf
CHANGES.html  README         twoflow-1-0.pcap  waf.bat
contrib       RELEASE_NOTES  utils             waf-tools
doc           scratch        utils.py          wscript
```

- **waf**
  基于Python开发的编译工具，ns-3系统本身和我们写的仿真代码都是由waf负责编译运行的。

- **scratch**
  该目录一般存放用户脚本文件，也可以把要运行的例子拷贝到此目录下，该目录是ns-3默认的脚本存放目录，使用waf编译运行脚本文件时，可以不加目录scratch，如果脚本文件在其他目录下需要在文件名前加目录。

- **examples**
  是由ns-3提供的关于如何使用ns-3的例子，包含了许多模块的使用，如能量、路由、无线网络等，入门ns-3这个目录下例子很重要。其中，tutorial目录下的例子很适合入门者学习。

- **doc**
  帮助文档

- **build**
  ns-3编译结果存放目录，包含编译文件时使用的共享库和头文件

- **src**
  ns-3源代码目录。src下面的子目录：

    ```Plain Text
  antenna           dsdv           netanim                test
  aodv              dsr            network                topology-read
  applications      energy         nix-vector-routing     traffic-control
  bridge            fd-net-device  olsr                   uan
  brite             flow-monitor   openflow               virtual-net-device
  buildings         internet       point-to-point         visualizer
  click             internet-apps  point-to-point-layout  wave
  config-store      lr-wpan        propagation            wifi
  core              lte            sixlowpan              wimax
  create-module.py  mesh           spectrum               wscript
  csma              mobility       stats
  csma-layout       mpi            tap-bridge
  
    ```

  每个子目录的结构都如下：

    ```C++
  bindings  doc  examples  helper  model  test  wscript
    ```

  其中，wscript文件结构是固定的，用来注册模块中包含的源代码和使用其他模块情况；模块代码的.cc和.h文件包含在model目录下；helper目录用来存放模块对应的helper类代码的源文件；test目录包含的是模块设计者编写的模块测试代码；examples目录存放的是应用该模块的示例代码；doc是帮助文档；bindings目录是模块用来绑定Python语言的。

  我们也可以添加和修改模块达到我们的模拟目的。







# 二、模块

1. 网络模块

    2.

2.



# 三、模拟流程

1. 选择仿真模块

2. 编写仿真脚本

3.



# 四、开发仿真

## 1.关键概念

    1. Node 节点
    
        2. 基本计算设备（主机）的抽象。
    
        3. Node类提供了用于管理计算设备的各种方法。
    
    4. channel 信道
    
        5. 基本的通信子网的抽象。
    
        6. Channel类提供了管理通信子网对象和把节点连接至信道的各种方法。
    
        7. CsmaChannel,PointToPointChannel和WifiChannel。举例来说，CsmaChannel信道模拟了用于一个可以实现载波侦听多路访问的信道，这个信道具有和以太网相似的功能。
    
    8. 网络设备
    
        9. 硬件设备和软件驱动的总和的抽象。
    
        10. NetDevice类提供了管理连接其他节点和信道对象的各种方法，并且允许开发者以面向对象的方法来自定义。
    
        11. CsmaNetDevice,PointToPointNetDevice, 和WifiNetDevice。正如以太网卡被设计成在以太网中工作一样，CsmaNetDevice被设计成在csma信道中工作，而PointToPointNetDevice在PointToPoint信道中工作，WifiNetNevice在wifi信道中工作。
    
    12. 协议层？
    
    13. 应用程序
    
        14. 需要被仿真的用户程序的抽象。（应用程序在节点上运行来驱动模拟过程。）
    
        15. Application类提供了管理仿真过程中用户层应用的各种方法。
    
        16. UdpEchoClientApplication和UdpEchoServerApplication。这些应用程序包含了一个client应用和一个server应用来发送和回应仿真网络中的数据包。

## 2.





# 五、example解读

example-first.cc文件

1. include声明

2. 命名空间：using namespace ns3;

3. 日志：NS_LOG_COMPONENT_DEFINE ("FirstScriptExample");

4. main函数

    5. 使两个日志组件生效的。它们被内建在Echo Client 和EchoServer 应用中：LogComponentEnable("UdpEchoClientApplication",LOG_LEVEL_INFO);
       LogComponentEnable("UdpEchoServerApplication",LOG_LEVEL_INFO);

    6. 创建ns-3节点对象：NodeContainer nodes;
       nodes.Create (2);

    7. 创建一个PointToPointNetDevice对象时配置仿真器：使用“5Mbps"来作为数据速率。使用"2ms"（2毫秒）作为每一个被创建的点到点信道传输延时值。

    8. 使用一个NetDeviceContainer对象来存放需要所有被创建的NetDevice对象。

    9. PointToPointHelper的Install()方法以一个NodeContainer对象作为一个参数。在Install()方法内，一个NetDeviceContainer被创建了。对于在NodeContainer对象中的每一个节点（对于一个点到点链路必须明确有两个节点)，都将有一个PointToPointNetDevice被创建和保存在设备容器内，有一个PointToPointChannel对象被创建，两个PointToPointNetDevices与之连接。当PointToPointHelper对象创建时，那些在helper中被预先设置的属性被用来初始化对象对应的属性值。

    10. InternetStackHelper是一个辅助安装网络协议栈的helper类。其中Install()方法以NodeContainer对象作为参数，当它被执行后，它会为节点容器中的每一个节点安装一个网络协议栈（TCP,UDP,IP等）。

    11. 使用Ipv4AddressHelper类为节点上的设备管理IP地址的分配。

        12. 声明了一个helper对象，并且告诉它应该开始从10.1.1.0开始以子网掩码为255.255.255.0分配地址。地址分配默认是从1开始并单调的增长，所以在这个基础上第一个分配的地址会是10.1.1.1，紧跟着是10.1.1.2等等。

    12. 使用Ipv4InterfaceContainer对象将一个IP地址同一个网络设备关联起来。

    13. Application：UdpEchoServerHelper、UdpEchoClientHelper

        15. echoServer.Install将会在管理节点的NodeContainer容器索引号为1的机节点上安装一个UdpEchoServerApplication。安装会返回一个容器，这个容器中包含了指向所有被helper对象创建的应用指针。

        16. 应用程序对象需要一个时间参数来“开始”产生数据通信并且可能在一个可选的时间点“停止”。时间点用ApplicationContainer的方法Start和Stop来设置。

        17. 创建了一个UdpEchoClientHelper的对象，并告诉它设置客户端的远端地址为服务器节点的IP地址。我们同样告诉它准备发送数据包到端口9。

        18. “MaxPackets”属性告诉客户端我们所允许它在模拟期间所能发送的最大数据包个数。

        19. “Interval”属性告诉客户端在两个数据包之间要等待多长时间。

        20. “PacketSize”属性告诉客户端它的数据包应该承载多少数据。

    14. Simulator

        22. 运行模拟器：使用全局函数Simulator::Run.来做到：Simulator::Run ();

        23. 当Simulator::Run被调用时，系统会开始遍历预设事件的列表并执行。

        24. 通过调用全局函数Simulator::Destroy来完成清理工作。当该方法被执行后，模拟器中所有创建的对象将被销毁。





# 学习笔记

1. 环境搭建笔记

   [NS-3学习笔记（一）：NS-3的运行环境搭建](https://rainsia.github.io/2018/03/30/ns3-001/#more)


2. 官方文档

   [1. Quick Start — Tutorial](https://www.nsnam.org/docs/release/3.39/tutorial/html/quick-start.html)


3. 编写逻辑学习笔记

   [NS3快速入门（使用VScode查看、编译代码）_51CTO博客_vscode快速生成代码](https://blog.51cto.com/u_15127602/3853549)





