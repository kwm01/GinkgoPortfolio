# ##项目疑问

checked：作为一个毕论文，它的内容是什么？

> 吴：它是一个SDN理论的实际运用。是对floodlight控制器的二次开发。主要是路由模块。

Openflow 提供了和of交换机相匹配的接口，以进行SDN的开发；




# ##理解项目

SDN 软件定义网络 Software Defined Network
QoS服务质量  Quality of Service
QoE 服务体验  Quality of Experience



多媒体网络资源的带宽、时延、抖动、丢包率等性能指标的要求越来越高。传统网络体系架构越来越多的缺陷和僵化， 不能解决特定业务性能的实际问题。

下一代网络架构必须能满足例如虚拟化的云计算、大数据应用、数据中心服务以及丰富的多媒体业务等服务要求2。

![image-20201104100807914](R&D.asset/image-20201104100807914.png)



## 实时业务处理模块

### 主要模块

主要功能：处理并且维护组播组的信息。（组播是一种实时流量的优化方式，通过交换机复制流，再进行多路转发。）

1. 当终端加入组播组，更新组播信息表，添加终端信息。
2. 当终端离开组播组，更新组播信息表，并删除终端信息。
3. 每次更新后，生成具体的流表项（OpenFlow 定义在交换机中的表）下发到转发的交换机中。

### 实时业务设计

![image-20201104195243650](R&D.asset/image-20201104195243650.png)

用户请求收看直播时，用户向网络中发送IGMP：join 命令来加入视频的组
播组。

1. 由于交换机没有匹配的流表项，join 命令通过Packet-in 上交给控制器。
2. 控制器收到join 命令，将终端加入组播组，并且动态地计算组播树和要修改
的路由信息，并通过Flowmod 消息配置流表项。。
3. 当流表项生效后，从节目源来的实时直播流量就依据组播树进行转发。
4. 用户停止收看节目时，用户发送leave 消息。
10
5. 由于交换机没有匹配的流表项，leave 命令通过Packet-in 上交给控制器。
6. 控制器将用户从组播组中移除，并通过Flowmod 消息配置流表项，中断组播
源到该用户流量的转发。

# ##Agile

## User story

- Independent (of all others),
-  Negotiable (not a specific contract for futures),
-  Valuable (It’s all about the end-user. If you can’t describe the value a customer is going to get out of your story, it’s not a good story),
-  Estimatable (to a good approximation),
- Small (so as to fit within an iteration) and
-  Testable (in principle even if there isn’t a test for it yet).

# 项目日志

## 实验环境

### Floodlight 控制器

#### 基本概念

![img](https://img2018.cnblogs.com/blog/1165691/201903/1165691-20190301202444156-636118144.png)

Floodlight是Apache授权并且基于JAVA开发的企业级OpenFlow控制器，开源软件。有可视化管理界面网页（类似tomcat服务器）。

Floodlight提供的API接口主要有：ACL、防火墙、静态流表、虚拟网。



具体来说， 基于ｆｌｏ ｏ ｄ ｌ ｉ ｇｈｔ 控制器的路由模块可以根据实时的全局网络的状态信息， 计算每个流的多条路径和可用带宽， 从而提供最佳的路由策略。

#### Floodlight安装



#### Floodlight启动

Floodlight 的安装基于linux系统或者是mac。那Floodlight就不是一个简单的一个jar包，随便就能用的。它的底层实现或许依赖linux系统的相关部分接口，因此需要在linux上配置安装，以及启动服务。

~~~java
启动floodlight
java -jar target/floodlight.jar 

游览器访问
http://localhost:8080/ui/index.html 
~~~



### Mininet

#### 基本概念

Mininet是由斯坦福大学基于Linux Container架构开发的一个进程虚拟化网络仿真工具，可以创建一个包含主机，交换机，控制器和链路的虚拟网络，其交换机支持OpenFlow，具备高度灵活的自定义软件定义网络。

**Mininet可以做什么**

- 为OpenFlow应用程序提供一个简单，便宜的网络测试平台；
- 启用复杂的拓扑测试，无需连接物理网络；
- 具备拓扑感知和OpenFlow感知的CLI，用于调试或运行网络范围的测试；
- 支持任意自定义拓扑，主机数可达4096，并包括一组基本的参数化拓扑；
- 提供用户网络创建和实验的可拓展Python API。



### 环境搭建

安装mininet,git

#### 安装jdk8，修改环境变量:　　

```bash
sudo vi ~/.bashrc

#set oracle jdk environment
export JAVA_HOME=/usr/lib/jvm/jdk1.8.0_191  ## 这里要注意目录要换成自己解压的jdk 目录
export JRE_HOME=${JAVA_HOME}/jre  
export CLASSPATH=.:${JAVA_HOME}/lib:${JRE_HOME}/lib  
export PATH=${JAVA_HOME}/bin:$PATH 
```

#### 安装ant

二. 手动安装

  \1. 到Apache官网下载最新版本的ant：http://ant.apache.org/bindownload.cgi

  \2. 解压下载下来的`.tar.gz文件： tar -xf apache-ant-1.8.2-bin.tar.gz (可能会要求输入密码)`

```
 3.将解压出来的文件移动到/opt/下：sudo mv apache-ant-1.8.2 /opt/ （sudo 不能省，否则没有权限）
 4.配置环境变量：sudo gedit /etc/profile，在原来基础上添加以下蓝体字：
```

   <u>export ANT_HOME=/opt/apache-ant-1.8.2</u>
   export JAVA_HOME=/usr/lib/jvm/java-6-openjdk
   <u>export PATH=$JAVA_HOME/bin:$ANT_HOME/bin:$PATH</u>
   export CLASSPATH=.:$JAVA_HOME/lib/dt.jar:$JAVA_HOME/lib/tools.jar

  5.验证是否安装成功： ant -version

  Apache Ant(TM) version 1.8.2 compiled on December 20 2010
  如此字样，则表示安装成功！

具体的之用方法，使用 ant --help 即可一一学到。

#### 安装floodlight

从Github下载并编译Floodlight：git clone git://github.com/floodlight/floodlight.git

git check v1.2. // master 分支在出现问题，目前v1.1 可以成功ant。

**启动**

java -jar target/floodlight.jar

停止：jps； kill -9 pid// kill pid



#### ant eclipse

~~~
ant eclipse
注：必须编译才能在eclipse导入，否则不能进行导入，出现no projects are found to import!

第三步打开eclipse进行导入


File --> Import --> General --> Existing Projects into Workspace --> Select root direction 找到clone出来的floodlight文件夹，选中 --> finish
然后就是运行配置设置


右键floodlight项目 --> Run As --> Run Configuration --> 右键Java Application --> new --> 名字自己命名，Project选择floodlight，Main选择net.floodlightcontroller.core.Main，最后点击 Apply
~~~

报错：setup***.sh  permission denied.

chmod +x setup...sh



#### 安装simplejson

~~~bash
 pip install simplejson
~~~



## Start-up

#### Mininet

sudo mn –controller remote,ip=192.168.152.20,port=6633

#### eclipse read only 的问题

解决方法：在本地var/lib 中创建一个floodlight文件夹

> chmod -R 777 floodlight
>
> chown -R root ./floodlight

#### 结果：

在floodlight页面中确实显示出了设备。



## 创建一个网络拓扑图

sudo mn --custom /home/workspace/Test04.py --topo mytopo --controller=remote,ip=192.168.123.208,port=6653 --switch ovsk,protocols=OpenFlow13

sudo mn --custom /home/workspace/fattree.py --topo mytopo --controller=remote,ip=192.168.123.208,port=6653 --switch ovsk,protocols=OpenFlow13

![image-20201223144126792](R&D/image-20201223144126792.png)

点击edit

![image-20210218145940299](R&D/image-20210218145940299.png)

![image-20201223144314622](R&D/image-20201223144314622.png)

Iperf 发包：

![image-20201223150519980](R&D/image-20201223150519980.png)

### Idea 启动Floodlight

port: 6653

url: http://192.168.33.1:8080/ui/pages/index.html



### 组播

sudo mn --custom /home/workspace/Topo07.py --topo mytopo --link tc --controller=remote,ip=192.168.33.1,port=6653 --switch ovsk,protocols=OpenFlow13

route add -net 224.0.0.0 netmask 224.0.0.0 h8-eth0

Source: iperf -c 224.0.0.3 -u --ttl 5 -t 3600 -b10M

user: iperf -s -u -B 224.0.0.3 -i 1



**更新mininet**

3/19笔记





ip route show

vlc-wrapper &



rtp://@224.0.0.3:5004/



main分支：

![image-20210319230200249](R&D/image-20210319230200249.png)



###  图

sudo mn --custom Test04.py --topo mytopo --link tc  --controller=remote,ip=192.168.123.208,port=6653 --switch ovsk,protocols=OpenFlow13



## Development

路由计算的类调用顺序

![image-20210130174542540](R&D/image-20210130174542540.png)



### 重要概念

| item             | note                                                         |
| ---------------- | ------------------------------------------------------------ |
| Service & Module | Module and service: module 是一个独立的子系统，为了和service区分，可以理解为module没有提供get方法自顾自的子系统，而service开放了一个服务供其他模块调用。 |
| U64              | getValue()，单位bit；U64.getValue() 返回一个long的raw        |
|                  |                                                              |



### 几个重要的接口

| 接口名             | note                                                         |
| ------------------ | ------------------------------------------------------------ |
| IFloodlightService | 内容为空                                                     |
|                    |                                                              |
| IOFMessageListener | isCallback() 方法确定消息处理的前后顺序；继承父接口IListener,并有几个并列的子Listener |
|                    |                                                              |



### 组播树

加入组播树解决冗余问题；



### 资源监视模块 QoSResourceService dev

#### MonitorDelayServiceImpl

1、定义一个LinkEntry类表示一条link。

2、使用FL service获取linkInfo以得到延迟方法


```java
Map<Link, LinkInfo> linksMap = linkDiscoveryService.getLinks();
```

有两种get延迟的方法

```java
System.out.println("链路时延:"+node.getKey().getLatency().getValue()+"ms");
System.out.println("当前时延："+node.getValue().getCurrentLatency().getValue()+"ms");
```

3、返回，private Map<LinkEntry<NodePortTuple, NodePortTuple>, Integer> linkDelaySecMap;



####  MonitorPkLossServiceImpl 

模拟Floodlight StatisticsCollector 进行数据收集，在返回的交换机响应中，获取丢包计数器数字；

模仿带宽获取，选取两个时间戳内的数据，并处理overflow，计算周期内的丢弃包和全部流量包之比为丢包率。

#### Future 类的使用

```java
ListenableFuture<?> future;
future = sw.writeStatsRequest(req); 
values = (List<OFStatsReply>) future.get(portStatsInterval*1000 / 2, TimeUnit.MILLISECONDS);
```

### 路由算法

#### 组播算法

使用最短路径开发，使用多路径合并，通过比较得到一个rp集合，并维护一个HashMap <hostMartch,Path> 

**预计需要：**RP_port  --  links

Path 返回一个NodePortTuple, 顺序是从用户节点开始的src 到dst （从零开始的list)





### 组播实验设计（查看备忘录）

>sudo mn --custom /home/medic/FloodlightProject/Mininet_Topology/Topo03C.py --topo mytopo --link tc --controller=remote,ip=192.168.33.1,port=6653 --switch ovsk,protocols=OpenFlow13
>
>route add -net 224.0.0.0 netmask 224.0.0.0 h8-eth0
>
>Source: iperf -c 224.0.0.3 -u --ttl 5 -t 3600 -b10M
>
>user: iperf -s -u -B 224.0.0.3 -i 1
>
>

#### sFlow Trend 

ovs-vsctl -- --id=@sflow create sFlow agent=ens33 target=\"192.168.33.1:6343\"  header=128  sampling=64 polling=1 -- set bridge s6 sflow=@sflow



ovs-vsctl -- --id=@sflow create sFlow agent=ens33 target=\"192.168.56.101:6343\"  header=128  sampling=64 polling=1 -- set bridge s6 sflow=@sflow



**访问网页**: 192.168.33.1:8087 

(然后 访问 本机的8087端口或者 设置https后访问8443端口 便可以查看到)

ovs-vsctl list sflow

ovs-vsctl remove bridge s5 sflow 778929ac-f591-4b34-8213-de251c3b4ea9





#### sFlow-RT

下载链接：https://inmon.com/products/sFlow-RT/sflow-rt.tar.gz

相关记录：https://www.sdnlab.com/15090.html

相关记录：

https://blog.csdn.net/yinxingsha/article/details/52276587

https://www.cnblogs.com/cyquen/p/13520501.html



./start.sh

./get-app.sh sflow-rt browse-metrics





## Deploy

use idea to build a jar 					此方法运行时没有一个SyncDB

or maven install the floodlight.jar   此方法生成的jar运行后如下

<u>run as:</u> java -jar floodlight.jar  -cf default.proxxxyyssfd;

![image-20210424142606265](R&D/image-20210424142606265.png)



# ---------------------------

# ##工具包

## Screen

前言

　　刚用xshell工具时，发现关闭xshell会话后软件也会随之关闭。后来了解到可以使用screen控制软件后台运行，并且能够便利地随时唤出程序。

1.安装

　　终端输入screen，如果出现Command not found表明当前设备未安装screen

　　输入 sudo apt install screen 安装

　　安装后输入screen,一般如下图所示

![img](https://img2018.cnblogs.com/blog/1594067/201910/1594067-20191010164018785-1827226015.png)

 

 2.新建窗口

　　screen -S name

　　name自行命名，后期可通过name唤出当前程序

3.执行软件

　　窗口创建后直接执行软件就行，和普通终端运行一样的。

4.查看当前screen运行的程序

　　screen -ls

5.唤出某个程序

　　screen -r name 或 screen -d -r id

## Iperf

~~~bash
xterm
~~~

~~~bash
//h1 iperf -s
//h2 iperf -c h1-ip

~~~

测得延时、流量、丢包率等参数

~~~bash
iperf -s -u -i 1
~~~

~~~
iperf -c 10.0.0.1 -u -b 100M
~~~

## ovs-ofctl 

查看流表

~~~bash
ovs-ofctl dump-flows s1

//安装 apt-get install openvswitch-common
sudo ovs-ofctl -O OpenFlow13 dump-groups s2
~~~

https://www.cnblogs.com/legendbaby/p/5056923.html



## chmod 

chmod +x setup...sh  //可执行



## mininet 

mn -c 清除