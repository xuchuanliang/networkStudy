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

### 2.3 收发数据
- MTU：一个网络包的最大长度，以太网中一般为 1500 字节
- MSS：除去头部之后，一个网络包所能容纳的 TCP 数据的最大长度
- TCP的实际工作过程：
>首先客户端在连接时需要计算出从客户端到服务器方向通信相关的序号初始值，并将这个值发送给服务器。接下来，服务器会通过
这个初始值计算出ACK号并返回给客户端。初始值有可能在通信过程中丢失，因此当服务器收到初始值后需要返回ACK号作为确认。同时，
服务器也需要计算出与从服务器到客户端方向通信相关的序号初始值，并将这个值发送给客户端。接下来像刚才一样，客户端也需要
根据服务器发来的初始值计算出ACK号并返回给服务器。到这里，序号和ACK号都已经准备完成了，接下来可以进入数据收发阶段
了。数据收发操作本身是可以双向同时进行的，但Web中是先由客户端向服务器发送请求，序号也会跟随数据一起发送。然后服务器
收到数据后再返回ACK号。从服务器向客户端发送数据的过程刚好相反。
TCP采用这样的方式确认对方是否收到了数据，在得到对方确认之前，发送过的包都会保存在发送缓冲区中。如果对方没有返回某些
包对应的ACK号，那么就重新发送这些包。
- 通过序号和ACK号可以确认接受方是否收到了网络包。
>TCP采用了动态调整等待时间的方法，这个等待时间是根据ACK号返回所需的时间来判断。具体来说，TCP会在发送数据的过程中
持续测量ACK号的返回时间，如果ACK号返回变慢，则相应延长等待时间；相对的，如果ACK号马上就能返回，则相应缩短等待时间。
- 使用窗口有效管理ACK号
- ACK号标识的是已收到的数据量，也就是说，它是告诉发送方目前已接收数据的最后位置在哪里，因此当需要连续发送ACK号时，
只要发送最后一个ACK号就可以了，中间的可以全部省略。
>总结
>> 首先，协议栈会检查收到的数据块和 TCP 头
   部的内容， 判断是否有数据丢失， 如果没有问题则返回 ACK 号。 然后，
   协议栈将数据块暂存到接收缓冲区中， 并将数据块按顺序连接起来还原出
   原始的数据， 最后将数据交给应用程序。 具体来说， 协议栈会将接收到的
   数据复制到应用程序指定的内存地址中， 然后将控制流程交回应用程序。
   将数据交给应用程序之后， 协议栈还需要找到合适的时机向发送方发送窗
   口更新

### 2.4 从服务器断开并删除套接字
>数据收发的整体流程，参见图 TCP的整体流程.jpg
>> 数据收发操作的第一步是创建套接字。 一般来说， 服务器一方的应用
   程序在启动时就会创建好套接字并进入等待连接的状态。 客户端则一般是
   在用户触发特定动作， 需要访问服务器的时候创建套接字。 在这个阶段，
   还没有开始传输网络包。
   创建套接字之后， 客户端会向服务器发起连接操作。 首先， 客户端会
   生成一个 SYN 为 1 的 TCP 包并发送给服务器（图 2.13 ①）。 这个 TCP 包
   的头部还包含了客户端向服务器发送数据时使用的初始序号， 以及服务器
   向客户端发送数据时需要用到的窗口大小 A。 当这个包到达服务器之后， 服
   务器会返回一个 SYN 为 1 的 TCP 包（图 2.13 ②）。 和图 2.13 ①一样， 这个
   包的头部中也包含了序号和窗口大小， 此外还包含表示确认已收到包①的
   ACK 号 B。 当这个包到达客户端时，客户端会向服务器返回一个包含表示确
   认的 ACK 号的 TCP 包（图 2.13 ③）。 到这里， 连接操作就完成了， 双方进
   入数据收发阶段。
   数据收发阶段的操作根据应用程序的不同而有一些差异， 以 Web 为
   例， 首先客户端会向服务器发送请求消息。 TCP 会将请求消息切分成一定大
   小的块， 并在每一块前面加上 TCP 头部， 然后发送给服务器（图 2.13 ④）。
   TCP 头部中包含序号， 它表示当前发送的是第几个字节的数据。 当服务器
   收到数据时， 会向客户端返回 ACK 号（图 2.13 ⑤）。 在最初的阶段， 服务
   器只是不断接收数据， 随着数据收发的进行， 数据不断传递给应用程序，
   接收缓冲区就会被逐步释放。 这时， 服务器需要将新的窗口大小告知客户
   端。 当服务器收到客户端的请求消息后， 会向客户端返回响应消息， 这个
   过程和刚才的过程正好相反（图 2.13 ⑥⑦）。
   服务器的响应消息发送完毕之后， 数据收发操作就结束了， 这时就会
   开始执行断开操作。 以 Web 为例，服务器会先发起断开过程 A。 在这个过程
   中， 服务器先发送一个 FIN 为 1 的 TCP 包（图 2.13 ⑧）， 然后客户端返回一
   个表示确认收到的 ACK 号（图 2.13 ⑨）。 接下来， 双方还会交换一组方向相
   反的 FIN 为 1 的 TCP 包（图 2.13 ⑩）和包含 ACK 号的 TCP 包（图 2.13k）。
   最后， 在等待一段时间后， 套接字会被删除。

### 2.5 IP与以太网的包收发操作
- IP模块负责添加如下两个头部：1.MAC头部：以太网用的头部，包含MAC地址；2.IP头部：IP用的头部，包含IP地址
- 无论要收发的包是控制包还是数据包，IP对各种类型的包的收发操作都是相同的。
- IP头部的接收方IP地址填写通信对象的IP地址。
- 发送方IP地址需要判断发送所使用的网卡，并填写该网卡的IP地址。
- IP第十实际上并不是分配给计算机的，而是分配给网卡的，因此当计算机上存在多块网卡时，每一块网卡都会有自己的IP地址。
- 子网掩码：用来判断IP地址中的网络号和主机号分界线的值
- 在CMD中使用route print来显示路由表，如图所示
- IP模块根据路由表Gateway栏的内容判断应该把包发送给谁
- ARP：address resolution protocol，地址解析协议
- ARP是利用广播对所有设备提问：“×× 这个 IP 地址是谁的？ 请把你的 MAC 地址告诉我。” 然后就会有人回答：
“这个 IP 地址是我的， 我的 MAC 地址是××××。”，参见图用ARP查询MAC地址
- 查询MAC地址需要使用ARP
- IP模块负责整个打包的过程，首先是TCP协议附上TCP头交给IP模块，IP模块附上IP头和MAC头部，交给网卡发送，网卡只负责发送
- 以太网的三个性质：1.将包发送到MAC头部的接收方MAC地址代表目的地；2.用发送方MAC地址识别发送方；3.用以太类型识别包的内容。
>网卡的ROM中保存着全世界唯一的MAC地址，这是在生产网卡时写入的，将这个值读出来之后就可以对MAC模块进行设置，MAC模块
就知道自己对应的MAC地址了。也有一些特殊方法，比如从命令或者配置文件中读取MAC地址并分配给MAC模块。这种情况下，网卡
会忽略ROM中的MAC地址。有人认为在网卡通电后，ROM中的MAC地址就自动生效了，其实不然，真生生效的是网卡驱动进行初始化在
MAC模块中设置的那个MAC地址。在操作系统启动并完成这些初始化操作后，网卡就等待来自IP的委托了。
- 网卡的ROM中保存着全世界唯一的MAC地址，这是在生产网卡是写入的
- 网卡中保存的MAC地址会由网卡驱动程序读取并分配给MAC模块
- 网卡将包转换成电信号并发送到网线中。
- 网卡驱动从IP模块获取包之后，会将其复制到网卡内的缓冲区中，然后向MAC模块发送发送包的命令。接下来轮到MAC模块进行工作
首先MAC模块会将包从缓冲区中取出，并在开头加上报头和起始帧分界符，在末尾加上用于检测错误的帧校验序列。
- 网卡的MAC模块生成通用信号，然后由PHY（MAU）模块转换成可在网线中传输的格式，并通过网线发送出去。
- 半双工和全双工

- 集线器：当信号到达集线器后，会被广播到整个网络中，以太网的基本架构就是将包发送到所有的设备，然后由设备根据接收方
MAC地址来判断应该接收哪些包，而集线器就是这一架构的忠实体现，它就是负责按照以太网的基本架构将信号广播出去。
- 集线器将信号发送给所有连接在它上面的线路
- 交换机内部有一张MAC地址与网线端口的对应表。当接收到包时，会将相应的端口号码和发送方MAC地址写入表中，这样就可以根据地址
判断出该设备连接在那个端口上了。交换机就是根据这些信息判断应该把包转发到哪里的。
- 交换机端口的MAC模块不具有MAC地址。
- 交换机根据MAC地址表查找MAC地址，然后将信号发送到相应的端口
- 交换机的全双工模式可以同时发送和接收信号
> 交换机只将包转发到具有特定MAC地址的设备连接的端口，其他端口都是空闲的，如图所示，当包从最上面的端口发送到
最下面的端口时，其他端口都处于空闲状态，这些端口可以传输其他的包，因此交换机可以同时转发多个包。相对的，
集线器会将输入的信号广播到所有的端口，如果同时输入多个信号就会发生碰撞，无法同时传输多路信号，因此从设备
整体的转发能力来看，交换机要高于集线器。

### 路由器的包转发操作
- 路由器是基于IP设计的，而交换机是基于以太网设计的。
- 路由器包括转发模块和端口模块两部分，其中转发模块负责判断包的转发目的地，端口模块负责包的收发操作，换句话说
路由器转发模块和端口模块的关系，就相当于协议栈的IP模块和网卡之间的关系。因此，可以将路由器的转发模块想象成
IP模块，将端口模块想象成网卡。
- 路由器的各个端口都具有MAC地址和IP地址。
- 交换机是通过MAC头部中的接收方MAC地址来判断转发目标的，而路由器是根据IP头部中的IP地址来判断
- 路由器根据IP地址判断转发目标
- 路由器最左侧的目标地址列记录的是接收方的信息，实际上这里的IP地址只包含子网的网络号部分的比特值，而表示主机号部分的比特值
全部为0.
- 路由器会忽略主机号，只匹配网络号
- 路由聚合，多个子网会被合并成一个子网，子网掩码会发生变化，同时，目标地址列也会改成聚合后的地址。
- 路由表的子网掩码列只表示在匹配网络目标地址时需要对比比特数量
- 交换机中对MAC地址表的维护是包转发操作中的一个步骤，而路由器中对路由表的维护是与包转发操作相互独立的。对路由表进行
维护的方法有两种：1.由人手动维护路由记录；2.根据路由协议机制，通过路由器之间的信息交换由路由器进行维护路由表的记录。
- 路由器的端口都具有MAC地址，只接收与自身地址匹配的包，遇到不匹配的包则直接丢弃。
- 通过路由器转发的网络包，其接收方MAC地址为路由器端口的MAC地址。
- 路由表中子网掩码为0.0.0.0的记录表示默认路由
- 路由器在把包发出去之前，更新IP头部中的TTL（生存时间）字段,TTL字段表示包的有效期，包每经过一个路由器的转发，这个
值就会减1，当这个值变成0时，就表示超过了有效期，这个包就会被丢弃。
- 路由器判断下一个转发目标的方法如下：1.如果路由器表的网关列内容为IP地址，则该地址就是下一个转发目标；2.如果路由表
的网关列内容为空，则IP头部中的接收方IP地址就是下一个转发目标
- 路由器也会使用ARP来查询下一个转发目标的MAC地址。
- 路由器在转发网络包时，都需要在前面加上MAC头部。路由器是基于IP设计的，而交换机是基于以太网设计的，因此IP与以太网
的关系也就是路由器与交换机的关系
- IP（路由器）负责将包送达通信对象这一整体过程，而其中将包传输到下一个路由器的过程则是由以太网（交换机）来负责。
- 内网中的私有地址本身没有特别的结构，只不过是将共有地址中没分配的一部分拿出来规定只能在内网使用他们而已，内网中
可用作自由地址的范围包含：10.0.0.0-10.255.255.255;172.16.0.0-172.31.255.255;192.168.0.0-192.168.255.255
- 当公司内网和互联网连接时，，将公司内网分成两个部分，一部分是对互联网开放的部分分配公有地址，可以和互联网直接进行
通信。相对的，内网部分则分配私有地址，内网中的设备不能和互联网直接收发网络包，而是通过地址转换进行连接。
- 地址转换的基本原理是在转发网络包时对IP头部中的IP地址和端口号进行改写。
- 路由器的包过滤功能

## 第四章 通过接入网进入互联网内部
- ADSL接入网
- 所谓接入网，就是指连接互联网与家庭、公司网络的通信线路。一般家用的接入网方式包括ADSL、FTTH、CATV、电话线、ISDN等，
公司则还可能使用专线。
- 互联网接入路由器会在网络包前面加上MAC头部、PPPoE头部、PPP头部总共3中头部，然后发送给ADSL Modem
- ADSL Modem将包拆分成信元，并转换成电信号发送给分离器
## 第五章 服务器端的局域网中有什么玄机
- 包过滤方式的防火墙可根据接收方IP地址、发送方IP地址、接收方端口号、发送方端口号、控制位等信息来判断是否允许某个包通过。
- 使用描述符来指代套接字的原因：1.等待连接的套接字中没有客户端IP地址和端口号；2.使用描述符这一种信息比较简单
- 网卡的MAC模块将网络包从信号还原为数字信号，校验FCS并存入缓冲区
- 网卡驱动会根据MAC头部判断协议类型，并将包交给相应的协议栈
- 协议栈的IP模块会检查IP头部：1.判断是不是发给自己的；2.判断网络包是否经过分片；3.将包转交给TCP模块或UDP模块
- 如果收到的是发起连接的包，则TCP模块会：1.确认TCP头部的控制位SYN；2.检查接收方端口号；3.为相应的等待连接套接字
复制一个新的副本；4.记录发送方IP地址和端口号等信息
- 收到数据包时，TCP模块会：1.根据收到的包的发送方IP地址、发送方端口号、接收方IP地址，接收方端口号找到相对应的套接字、
发送方端口号、接收方IP地址、接收方端口号找到相对应的套接字；2.将数据块拼接起来并保存在接收缓冲区中；3.向客户端反馈ACK。



2019年6月20日 13:15:50 123/362
2019年7月2日 19:06:23 142/362
2019年7月3日 12:32:42 149/362
2019年7月5日 12:20:54 162/362
2019年7月8日 12:32:24 176/362
2019年7月8日 15:37:38 187/362
2019年7月8日 17:53:24 293/362
2019年7月8日 18:31:50 基本结束



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