**有关Wireshark过滤功能的介绍**

使用过滤是非常重要的，初学者使用wireshark时，将会得到大量的冗余信息，在几千甚至几万条记录中，以至于很难找到自己需要的部分。搞得晕头转向。过滤器会帮助我们在大量的数据中迅速找到我们需要的信息。

**一、过滤功能**

过滤器有两种：

一种是显示过滤器，就是主界面上那个，用来在捕获的记录中找到所需要的记录。

一种是捕获过滤器，用来过滤捕获的封包，以免捕获太多的记录。

1.  **捕获过滤器**

捕捉前依据协议的相关信息进行过滤设置

**![UTBT4C%%P8O@Z_NN8NZ%YBE](media/38016a4409775363ae3223d291a53d4f.png)**

**字段详解：**

Protocol（协议）:

可能值: ether, fddi, ip, arp, rarp, decnet, lat, sca, moprc, mopdl, tcp and udp.

如果没指明协议类型，则默认为捕捉所有支持的协议。

注：在wireshark的HELP-Manual Pages-Wireshark Filter中查到其支持的协议。

Direction（方向）:

可能值: src, dst, src and dst, src or dst

如果没指明方向，则默认使用 “src or dst” 作为关键字。

”host 10.2.2.2″与”src or dst host 10.2.2.2″等价。

Host(s):

可能值： net, port, host, portrange.

默认使用”host”关键字，”src 10.1.1.1″与”src host 10.1.1.1″等价。

Logical Operations（逻辑运算）:

可能值：not, and, or.

其中：否(“not”)具有最高的优先级。或(“or”)和与(“and”)具有相同的优先级，运算时从左至右进行。

“not tcp port 3128 and tcp port 23″与”(not tcp port 3128) and tcp port 23″等价。

“not tcp port 3128 and tcp port 23″与”not (tcp port 3128 and tcp port
23)”不等价。

例如：捕获主机为10.88.6.225

![]C2NV6EO8C076\~K2[T5I6}M](media/eae557a2983be16a54a092c87b30fdbe.png)

**1.2 显示过滤器**

![](media/8513abfef67ad925332e475d507ee70a.png)![](media/19d3ccb2e5a3f7d3a231f4b9c0a4734c.png)

string1和string2是可选的。

依据协议过滤时，可直接通过协议来进行过滤，也能依据协议的属性值进行过滤。

按协议进行过滤：

snmp \|\| dns \|\| icmp 显示SNMP或DNS或ICMP封包。

按协议的属性值进行过滤：

ip.addr == 10.1.1.1

ip.src != 10.1.2.3 or ip.dst != 10.4.5.6

ip.src == 10.230.0.0/16 显示来自10.230网段的封包。

tcp.port == 25 显示来源或目的TCP端口号为25的封包。

tcp.dstport == 25 显示目的TCP端口号为25的封包。

http.request.method== "POST" 显示post请求方式的http封包。

http.host == "tracker.1ting.com" 显示请求的域名为tracker.1ting.com的http封包。

tcp.flags.syn == 0×02 显示包含TCP SYN标志的封包。

表示只显示Source为10.88.6.225的为：

Ip.src==10.88.6.225

![BJ}@\~JF6_F)[3@6A4ZJ_D[J](media/500af11199ea9abad505827b0eb8a845.png)

**1.3 内容过滤语法**

深度字符串匹配

contains ：Does the protocol, field or slice contain a value

示例

tcp contains "http" 显示payload中包含"http"字符串的tcp封包。

http.request.uri contains "online" 显示请求的uri包含"online"的http封包。

特定偏移处值的过滤

tcp[20:3] == 47:45:54 /\*
16进制形式，tcp头部一般是20字节，所以这个是对payload的前三个字节进行过滤 \*/

http.host[0:4] == "trac"

过滤中函数的使用（upper、lower）

upper(string-field) - converts a string field to uppercase

lower(string-field) - converts a string field to lowercase

示例

upper(http.request.uri) contains "ONLINE"

wireshark过滤支持比较运算符、逻辑运算符，内容过滤时还能使用位运算。

如果过滤器的语法是正确的，表达式的背景呈绿色。如果呈红色，说明表达式有误。

**1.4 捕获结果**

双击每一行记录，可在弹出窗口中查看详细记录

![](media/17ca6d294a21818eb4985ffa95170c94.png)

**二、表达式规则**

**2.1 协议过滤**

比如TCP，只显示TCP协议。

**2.2 IP 过滤**

比如 ip.src ==10.88.6.225 显示源地址为10.88.6.225，

ip.dst==10.88.6.225, 目标地址为10.88.6.225

（Ps:需要注意的点）

一些过滤器字段与多个协议字段匹配。例如，“ip.addr”
匹配IP报头中的IP源地址和目标地址。还有 “tcp.port”, “udp.port”, “eth.addr”
等也是如此。

还要注意：ip.addr == 10.88.6.225

等效于ip.src == 10.88.6.225 or ip.dst == 10.88.6.225

**2.3 端口过滤**

tcp.port ==80,端口为80的

tcp.srcport == 80,只显示TCP协议的愿端口为80的。

**2.4 Http模式过滤**

http.request.method=="GET", 只显示HTTP GET方法的。

**三、着色规则**

在菜单“视图-着色规则”下查看

![](media/071e808976d2fb0d13e2cbf3f25868d0.png)

数据包的大致结构

第一行：数据包整体概述，

第二行：链路层详细信息，主要的是双方的mac地址

第三行：网络层详细信息，主要的是双方的IP地址

第四行：传输层的详细信息，主要的是双方的端口号。

![](media/bf1b268847cb5058ecf5b79bc03c2ac6.png)

![](media/db21883149d4d9f2d5195303f5eb35e8.png)
