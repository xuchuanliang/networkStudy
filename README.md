# 《网络是怎样连接的》
## 第一章 浏览器生成消息
### 1.1 生成HTTP请求消息
- 浏览器的第一步工作是对URL进行解析，具体参见图
- 1条请求消息中只能写1个URI。如果需要获取多个文件，必须对每个文件单独发送1条请求。
### 1.2 向DNS服务器查询Web服务器的IP地址
- 在浏览器对URL进行解析完毕后，区分出来了协议，服务器地址，访问的文件位置；
- 如果URL中服务器地址是使用的域名，则需要找寻DNS服务器解析成对应的IP地址；
- 尽管浏览器能够解析网址并生成HTTP消息，但它本身并不具备将消息发送到网络中的功能，因此这一功能需要委托
操作系统来实现。
- 在网络中， 所有的设备都会被分配一个地址。 这个地址就相当于现实 中某条路上的“×× 号 ×× 室”。 
其中“号”对应的号码是分配给整个子网的【也称为网段】， 而“室”对应的号码是分配给子网中的计算机的， 
这就是网络中的 地址。“号”对应的号码称为网络号，“室”对应的号码称为主机号， 这个地址的整体称为 IP 地址
- 实际的 IP 地址是一串32 比特的数字， 按照 8 比特（1 字节）为一组分成 4 组， 分别用十进制表示
然后再用圆点隔开。 这就是我们平常经常见到的 IP 地址格式， 但仅凭这一 串数字我们无法区分哪部分是网络号，、
哪部分是主机号。 在 IP 地址的规则 中，网络号和主机号连起来总共是 32 比特， 但这两部分的具体结构是不固
定的。 在组建网络时， 用户可以自行决定它们之间的分配关系， 因此，我们还需要另外的附加信息来表示 IP 
地址的内部结构--子网掩码
- 子网掩码的作用就是为了将ip地址区分出网段和主机号，因为网段和主机号的具体结构不是固定的，只是最终组成了32位
的IP地址
- 子网掩码的格式是一串与 IP 地址长度相同的 32 比特数字， 其左边一半都是 1， 右边一半都是0。其中，
子网掩码为 1 的部分表示网络号， 子网掩码为 0 的部分表示主机号。将子网掩码按照和 IP 
地址一样的方式以每 8 比特为单位用圆点分组后写在 IP 地址的右侧， 这就是图 1.9（b）的方法。 
这种写法太长， 我们也可以把 1 的部分的比特数用十进制表示并写在 IP 地址的右侧， 如图 1.9（c）
所示。 这两种方式只是写法上的区别， 含义是完全一样的。
- 主机号部分的比特全部为 0 或者全部为 1 时代表两种特殊的含义。主机号部分全部为 0 代表整个子网而不是子网中的
某台设备。此外，主机号部分全部为 1 代表向子网上所有设备发送包， 即广播。
- 域名和IP地址并用的理由：域名方便人类记忆；IP地址是固定长度且相对于域名更小，只有32个比特，也就是4个字节
这样处理速度比较快。
- 向 DNS 服务器发出查询， 也就是向 DNS 服务器发送查询消息， 并接收服务器返回的响应消息。
换句话说，对于 DNS 服务器，我们的计算机上一定有相应的 DNS 客户端，而相当于DNS客户端的部
分称为DNS解析器，或者简称解析器。通过 DNS 查询 IP 地址的操作称为域名解析，因此负责执行
解析（resolution）这一操作的就叫解析器（resolver）了。解析器实际上是一段程序，
它包含在操作系统的 Socket 库中。
- 根据域名查询 IP 地址时，浏览器会使用 Socket 库中的解析器。

### 1.3 全世界DNS服务器大接力
- DNS服务器来自客户端的查询消息主要包括3中信息：1.域名（服务器、 邮件服务器（邮件地址中 @ 后面的部分）的名称）；
2.Class（在最早设计 DNS 方案时， DNS 在互联网以外的其他网络中的应用也被考虑到了，而 Class 就是用来识别网
络的信息。 不过， 如今除了互联网并没有其他的网络了，因此 Class 的值永远是代表互联网的 IN）；
3.记录类型（表示域名对应何种类型的记录。 例如， 当类型为 A 时， 表示域名对应的是 IP 地址； 当类型为 
MX 时， 表示域名对应的是邮件服务器。 对于不同的记录类型， 服务器向客户端返回的信息也会不同）；
- DNS 服务器会从域名与 IP 地址的对照表中查找相应的记录，并返回 IP 地址。
- 在域名中， 越靠右的位置表示其层级越高，比如www.lab.glasscom.com这个域名如果按照公司里的组织结构来说，
大概就是“com 事业集团 glasscom 部 lab 科的 www”这样。 其中，相当于一个层级的部分称为域。 
因此， com 域的下一层是glasscom 域， 再下一层是 lab 域， 再下面才是 www 这个名字。
- 首先， 将负责管理下级域的 DNS 服务器的 IP 地址注册到它们的上级 DNS 服务器中， 然后上级 DNS 服务器的 
IP 地址再注册到更上一级的 DNS 服务器中， 以此类推。 也就是说， 负责管理 lab.glasscom.com 这个域的 DNS 
服务器的 IP 地址需要注册到 glasscom.com 域的 DNS服务器中， 而 glasscom.com 域的 DNS 服务器的 IP 地址
又需要注册到 com域的 DNS 服务器中。 这样， 我们就可以通过上级 DNS 服务器查询出下级DNS 服务器的 IP 地址， 
也就可以向下级 DNS 服务器发送查询请求了。在互联网中， com 和 jp 的上面还有一级域， 称为根域。 根域不像 
com、 jp 那样有自己的名字， 因此在一般书写域名时经常被省略， 如果要明确表示根域， 应该像 www.lab.glasscom.com. 
这样在域名的最后再加上一个句点， 而这个最后的句点就代表根域。 不过， 一般都不写最后那个句点， 因此根域的存在
往往被忽略， 但根域毕竟是真实存在的， 根域的 DNS 服务器中保管着com、 jp 等的 DNS 服务器的信息。除此之外还需
要完成另一项工作， 那就是将根域的 DNS 服务器信息保存在互联网中所有的 DNS 服务器中。 这样一来， 任何 DNS 服务
器就都可以找到并访问根域 DNS 服务器了。

### 1.4 委托协议栈发送消息
- 进行收发数据操作之前， 双方需要先建立起这条管道才行。 建立管道的关键在于管道两端的  数据出入口， 
这些出入口称为套接字。 我们需要先创建套接字， 然后再将  套接字连接起来形成管道。 
- 收发数据的操作总结为4步：1.创建套接字；2.将管道连接到服务器端的套接字上；3.收发数据；4.断开管道并删除套接字
- 上面四步操作都是由协议栈来代劳
- 发送网络请求的步骤：1.浏览器输入地址；2.浏览器解析URL，解析成协议、服务器名和文件的路径名；3.如果服务器名是
域名，那么使用操作系统的DNS解析器访问DNS服务器，将域名解析成IP地址；4.委托操作系统的协议栈发送消息；5.协议栈调用
操作系统Socket库的socket程序组件创建套接字，套接字创建完成后，协议栈会返回一个描述符，应用程序会将收到的描述符
存放到内存中；6.委托协议栈将客户端创建的套接字与服务器那边的套接字链接起来，调用Socket库的connect，需要指定描述符、
服务器IP地址和端口号；7.传递消息，只要将数据送入套接字，数据就会被发送到对方的套接字中；8.断开阶段：收发数据结束。

## 第二章 用电信号传输TCP/IP数据
### 2.1 创建套接字
- 网络控制软件（协议栈）和网络硬件（网卡）
- 解析器用来向DNS服务器发出查询
- 浏览器、邮件等一般应用程序收发数据时用 TCP；DNS 查询等收发较短的控制数据时用 UDP。
- 套接字：在协议栈内部有一块用于存放控制信息的内存空间，这里记录了用于控制通信操作的控制信息，例如通信对象的
IP地址、端口号、通信操作的进行状态等，我们可以说这些控制信息就是套接字的实体。协议栈在执行操作时需要参阅这些控制信息。
- 套接字中记录了用于控制通信操作的各种控制信息，协议栈则需要根据这些信息判断下一步的行动，这就是套接字的作用。
- windows系统可以使用netstat命令显示套接字内容。其中如果本地IP地址和远程IP地址都是0.0.0.0，表示通信还没开始，IP地址不确定。
- netstat -ano,具体参见显示套接字内容.jpg
- 浏览器通过Socket库委托协议栈使用TCP协议来收发数据，首先是创建套接字阶段，协议栈首先会分配用于存放一个套接字所需的
内存空间，往里面写入控制信息；接下来，需要将标识这个套接字的描述符告知应用程序，描述符相当于用来区分协议栈的多个
套接字的号码牌。应用程序在向协议栈进行收发数据委托时就需要提供这个描述符。

### 2.2 连接服务器
- 连接实际上是通信双方交换控制信息，在套接字中记录这些必要信息并准备数据收发的一连串操作。所谓控制信息，就是用来控制
数据收发操作所需的一些信息，IP地址和端口号就是典型的例子。当执行数据收发操作时，我们还需要一块用来临时存放要收发数据的
内存空间，这块内存控制称为缓冲区。
- 通信操作中使用的控制信息分为两类：1.头部中记录的信息；2.套接字（协议栈中的内存信息）中记录的信息
- 连接操作的第一步是在TCP模块处创建表示连接控制信息的头部
- 通过TCP头部中的发送方和接收方端口号可以找到要连接的套接字
- 链接操作的实际过程：
>这一过程是从应用程序调用Socket库的connect开始的，调用提供了服务器的IP地址和端口号，这些信息会传递给协议栈
的TCP模块。然后，TCP模块会与该IP地址对应的对象，也就是与服务器的TCP模块交换控制信息，这一交互过程包括下面
几个步骤：
>>首先，客户端先创建一个包含开始数据收发操作的控制信息的头部。头部包含很多字段，这里要关注的重点是发送方和
接收方的端口号。到这里，客户端（发送方）的套接字就准确找到了服务器（接收方）的套接字，也就是搞清楚了我应该
连接哪个套接字。然后，我们将头部中的控制位的SYN比特设置为1，大家可以认为它表示链接。此外还需要设置适当的
序号和窗口大小。当TCP头部创建好之后，接下来TCP模块会将信息传递给IP模块并委托它进行发送。IP模块执行网络包发送
操作后，网络包就会通过网络到达服务器，然后服务器上的IP模块会将接收到的数据传递给TCP模块，服务器的TCP模块根据
TCP头部中的信息找到对应端口号的套接字，也就是说，从处于等待连接状态的套接字中找到了与TCP头部中记录的端口号
相同的套接字就可以了。当找到对应的套接字之后，套接字中会写入相应的信息，并将状态改为正在连接。上述操作完成后，
服务器的TCP模块会返回响应，这个过程和客户端一样，需要在TCP头部中设置发送方和接收方端口号以及SYN比特。此外，在返回
响应时还需要将ACK控制位设置为1，这表示已经接收到相应的网络包。网络中经常发生错误，网络包也会发生丢失，因此双方
在通信时必须相互确认网络包是否已经送达，而设置ACK比特就是用来进行这一确认的。接下来，服务器TCP模块会将TCP头部传递
给IP模块，并委托IP模块向客户端返回响应。
然后，网络包就会返回到客户端，通过IP模块到达TCP模块，并通过TCP头部的信息确认连接服务器的操作是否成功。如果SYN为1
则表示连接成功，这时会向套接字中写入服务器的IP地址、端口号等信息，同时还会将状态改为连接完毕。到这里，客户端的操作
就已经完成，但其实还剩下最后一个步骤。刚才服务器返回响应时将ACK比特设置为1，相应的，客户端也需要将ACK比特设置成1并
发回服务器，告诉服务器刚才的响应包已经收到。当这个服务器收到这个返回包之后，连接操作才算全部完成。
建立连接之后，协议栈的连接操作就结束了，也就是说connect已经执行完毕，控制流程被交回应用程序。
2019年6月13日 18:14:44 75/362


### 2.3 收发数据
### 2.4 从服务器断开并删除套接字
### 2.5 IP与以太网的包收发操作
### 2.6 UDP协议的收发操作

## 第三章 从网线到网络设备
### 3.1 信号在网线和集线器中传输
### 3.2 交换机的包转发操作
### 3.3 路由器的包转发操作
### 3.4 路由器的附加功能

## 第四章 通过接入网进入互联网内部
### 4.1 ADSL接入网的结构和工作方式
### 4.2 光纤接入网（FTTH）
### 4.3 接入网中使用的PPP和隧道
### 4.4 网络运营商的内部
### 4.5 跨越运营商的网络包

## 第五章 服务器端的局域网中有什么玄机
### 5.1 web服务器的部署地点
### 5.2 防火墙的结构和原理
### 5.3 通过将请求平均分配给多台服务器来平衡负载
### 5.4 使用缓存服务器分担负载
### 5.5 内容分发服务

## 第六章 请求到达web服务器，响应返回浏览器
### 6.1 服务器概览
### 6.2 服务器的接收操作
### 6.3 Web服务器程序解释请求消息并作出相应
### 6.4 浏览器接收相应消息并显示内容

# 《图解HTTP》