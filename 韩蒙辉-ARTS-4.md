### ARTS

本篇为ARTS第四篇

Algorithm、Review、Technique、Share

----

#### Algorithm

题目 [Lowest Common Ancestor of a Binary Search Tree](https://leetcode.com/problems/lowest-common-ancestor-of-a-binary-search-tree/description/)

Given a binary search tree (BST), find the lowest common ancestor (LCA) of two given nodes in the BST.

According to the [definition of LCA on Wikipedia](https://en.wikipedia.org/wiki/Lowest_common_ancestor): “The lowest common ancestor is defined between two nodes p and q as the lowest node in T that has both p and q as descendants (where we allow **a node to be a descendant of itself**).”

```
# Definition for a binary tree node.
# class TreeNode(object):
#     def __init__(self, x):
#         self.val = x
#         self.left = None
#         self.right = None

class Solution(object):
 
    def findParent(self, root, p, q):
        if (root.val<p):
            return self.findParent(root.right, p, q)
        elif(root.val> q ):
            return self.findParent(root.left,p,q)
        else:
            return root

    def lowestCommonAncestor(self, root, p, q):
        maxN=max(p.val,q.val);
        minN=min(p.val,q.val)
        return  self.findParent(root,minN,maxN)
```

---



#### Review####

文章：Java NIO  by Ron Hitchens 

《Java NI0》 这是Ron Hitchens所著的一本关于NIO的书，我阅读的英文原本，目前只看了前几张，并对设计I/0的地方进行了查阅总结。

有关计算机组成原理的书开头一句往往是计算机系统由“硬件”和“软件”两大部分组成。硬件是计算机的实体部分，我们通常也是通过计算机的五大组成部分展开讨论的：算术计算单元，逻辑控制单元，输入设备，输出设备，存储单元。计算机软件有可以分为两大类：系统软件和应用软件。应用软件如浏览器，社交软件等；系统软件如操作系统用于协调应用软件分时运行并管理硬件资源。

##### I/O 设备组成

通常I/O设备由机械部分和电子部分两部分组成，所谓机械部分指设备本身，而电子部分是我们熟悉的设备控制器或者适配器，电子部分的主要工作是是地址译码：按照主机和设备之间约点的格式和过程接受计算机发来的数据或者控制信号，或者向主机发送数据和状态信号。另外，电子部分还负责将发送来的电子信号(1,0 通常用高低电位表示)转换成机械部分能识别的模拟信号(高低电压，频率等)。同时电子部分的另一项重要工作是提供相应的缓冲区。

![img](https://wx4.sinaimg.cn/mw690/4c774c9dgy1ftk73akfj0j20aa04jt8u.jpg)

通常，一个接口是由多个可寻址的寄存器组成，人们将这些能够被CPU寻址，并且能读或写的寄存器成为“端口”。CPU访问接口时，寻址的是其内部的某一端口，而不是笼统地指接口或外设。根据CPU与外设交换信息的需要一个接口可能包含多个端口（例如，存放数据信息的数据端口，存放控制信息的控制端口和存放状态信息的状态端口），也可能只包含一个端口。其中，数据端口用来接收CPU和内存送往外设的数据或者外设送外CPU和内存的数据。在CPU和外设的数据传送中起缓冲作用。态端口用来存放外设或接口本身的状态。 控制或命令端口用来存放CPU发出的控制信息，以控制接口和外设按要求动作。 

###### 输入输出系统组成

输入输出系统由I/O软件和I/O硬件两部分组成。

I/0软件：输入输出系统软件的主要任务是：1.如果将用户编制的程序（或数据）输入至主机内。2.如果将运算结果输送给用户。3.如果实现I/O系统与主机工作的协调等。

I/O指令：I/O指令是机器指令的一类，其格式与其他指令既有相似之处也有不同点。I/O指令可以与其他指令字长相等，但它还应反应CPU与i/o设备交换信息的各种特点，如它必须反应对多台I/O设备的选择选择，以及在完成信息交换过程中，对不同设备应作哪些具体操作等。

|操作码|命令码|设备码|
||||
||

操作码：可做I/O指令与其他类指令（如访存指令，算逻指令）等的判别码；命令码：用来体现I/O的具体操作设备码：是对多态I/O设备的选择码。

I/O指令命令码：

1. 数据从I/O设备输入主机。例如将缓冲寄存器中的数据读至CPU的某个寄存器。
2. 将数据输出到I/O设备。例如将CPU中某个寄存器中的数据写入到某台设备的接口电路中的缓冲寄存器内；
3. 状态测试。利用命令码检测设备的状态（busy，ready）
4. 形成某些操作。例如读扇区，写扇区，找磁道。

###### I/O设备与主机的联系方式

I/O编址方式：

通常将I/O设备码视为地址码，对IO地址码的编址可采用两种方式：统一编址和不统一编址。

统一编址就是讲IO地址看做是寄存器地址的一部分。如在64k地址的存贮空间中，划出8k地址作为I/O地址，凡是在这8k地址范围内的访问，就是对I/O的访问。所用指令与访存指令相似。把所有的I/O接口的端口都当作是存储器的一个单元对待，每个接口芯片都安排一个或几个与存储器统一编号的地址号。也不设专门的输入/输出指令，所有传送和访问存储器的指令都可用来对I/O接口操作。不统一编址就是I/O地址和存储器地址是分开的，所有对I/O访问必须有专用的I/O指令。

![Memory-Mapped I/O (1)                           Memory Address Space                  I/O Address Space  (a) Separate I/O ...](https://image.slidesharecdn.com/accessingio-130308111901-phpapp01/95/accessing-io-devices-12-638.jpg?cb=1362741728)

##### I/O 与主机信息传送的控制方式

I/O 设备与主机交换信息时，共有五种控制方式：程序查询方式、程序中断方式、直接存储器存取方式（DMA)、I/O通道方式、I/O处理及方式。

###### 程序查询方式

程序查询方式是由CPU通过程序不断查询I/O设备是否已做好准备，从而控制I/O主机交换信息。这种方式使CPU与I/O处于串行工作状态，CPU的工作效率不高。

###### 程序中断方式

CPU启动I/0后仍然执行原程序，在N条指令执行接收后，CPU响应I/O的请求。这大大提高了CPU的动作效率。

![img](https://wx2.sinaimg.cn/mw690/4c774c9dgy1ftk9mqdlx0j20kc0svtbw.jpg)

采用程序中断式，CPU和I/O接口不仅在硬件方面需增加相应电路，而且在程序方面还必须编制终端服务程序。

###### DMA方式

在DMA方式中，主存与I/O设备之间有一条数据通路，主存与I/O设备交换信息时，无需处理中断服务程序。

``` 
Direct memory access (DMA) is a feature of computer systems that allows certain hardware subsystems to access main system memory (Random-access memory), independent of the central processing unit (CPU). 

Without DMA, when the CPU is using programmed input/output, it is typically fully occupied for the entire duration of the read or write operation, and is thus unavailable to perform other work. With DMA, the CPU first initiates the transfer, then it does other operations while the transfer is in progress, and it finally receives an interrupt from the DMA controller when the operation is done. This feature is useful at any time that the CPU cannot keep up with the rate of data transfer, or when the CPU needs to perform work while waiting for a relatively slow I/O data transfer. Many hardware systems use DMA, including disk drive controllers, graphics cards, network cards and sound cards.

```

[wikipedia](https://en.wikipedia.org/wiki/Direct_memory_access)

DMA是计算机系统中硬件设备访问主存的一种方式，可以独立CPU单独执行。

```
Many devices can temporarily take control of the bus and perform data transfers to (and from) main memory or other devices. Because the device is doing the work without the help of the CPU, this type of data transfer is known as direct memory access (DMA). DMA transfers can be performed between two devices, between a device and memory, or between memory and memory. This chapter explains transfers between a device and memory only.
```

 https://docs.oracle.com/cd/E19683-01/806-5222/dma-29901/index.html

![img](https://upload.wikimedia.org/wikipedia/commons/thumb/6/68/Computer_system_bus.svg/400px-Computer_system_bus.svg.png)

##### 字符设备、块设备、和其他设备

根据外设与系统之间交换数据的方式，可以将设备分为几种类别。有些设备非常适合于面向字符的数据交换，因为数据传输量很低。其它的设备则更适合于处理包含固定数目的数据块。内核会区分字符设备和块设备。前一类包括串行接口和文本终端，而后一类则包括硬盘，光驱设备等。

```
meitner> ls -l /dev/ sd{ a, b} /dev/ ttyS{ 0, 1} 
brw- r----- 1 root disk 8, 0 2008- 02- 21 21: 06 /dev/ sda 
brw- r----- 1 root disk 8, 16 2008- 02- 21 21: 06 /dev/ sdb 
crw- rw---- 1 root uucp 4, 64 2007- 09- 21 21: 12 ttyS0 
crw- rw---- 1 root uucp 4, 65 2007- 09- 21 21: 12 ttyS1
```

访问权限之前的字母是b或 c，分别表示块设备和字符设备。

网卡没有设备文件。 相反，用户程序必须使用套接字与网卡通信。套接字是一个抽象层，对所有网卡提供了一个 抽象视图。 标准库的网络相关函数调用 socketcall 系统调用与内核通信交互， 进而访问网卡。

块设备与字符设备在3个主要方面有根本的不同。 

* 可以在数据中的任何位置进行访问。对字符设备来说，这是可能的，但不是必然的。 
* 数据总是以**固定长度的块进行传输**。 即使只请求一个字节的数据，设备驱动程序也会从设备取出一个完全块 的数据。 相比之下，字符设备能够返回单个 字节。
* 对块设备的访问有大规模的缓存， 即已经读取的数据保存 在内存中。 如果再次需要， 则直接从内存获得。 写入操作也使用了缓存， 以便延迟处理。 这对字符设备没有意义（ 如 键盘）。 因为，字符设备的每次读请求都必须真正与设备交互才能完成。

        块（block）和扇区（sector）。块是一个特定长度的字节序列，用于保存在内核和设备之间传输的数据。块的长度可通过软件方法修改。扇区是一个固定的硬件单位，指定了某个设备最少能够传输的数据量。块不过是连续扇区的序列而已。因此，块长度总是扇区长度的整数倍。由于扇区是特定于硬件的常数，它也用来指定设备上某个数据块的位置。内核将每个块设备都视为一个线性表，由按整数编号的扇区或块组成。当前几乎所有常见块设备的扇区长度都是512字节，块长度则有512、1024、2048、4096字节等。但应该注意到，块的最大长度，会受到特定体系结构的内存页长度的限制。IA-32系统支持的块长度为4096字节，因为其内存页长度是4096字节。另一方面，IA-64和Alpha系统能够处理8192字节的块。
        文件系统会将硬盘划分为不同长度的块，以便在处理许多小文件或少数大文件时分别优化性能。因为文件系统能够将传输的块长度与自身块长度匹配，所以实现起来要容易得多。
    	块设备层不仅负责寻址块设备，也负责执行其他任务，以提高系统中所有块设备的性能。此类任务包括预读算法的实现，在内核判断应用程序稍后将需要使用某数据时，会使用预读算法从块设备预先将数据读入内存。
    	如果预读的数据不是立即需要，那么块设备层必须提供缓冲区/缓存来保存这些数据。这种缓冲区/缓存不仅用于保存预读数据，也用于临时保存经常用到的块设备数据。
    

From:莫尔勒(Wolfgang Mauerer). 深入Linux内核架构

##### 文件系统

```
In computing, a file system or filesystem controls how data is stored and retrieved. Without a file system, information placed in a storage medium would be one large body of data with no way to tell where one piece of information stops and the next begins. By separating the data into pieces and giving each piece a name, the information is easily isolated and identified. Taking its name from the way paper-based information systems are named, each group of data is called a "file". The structure and logic rules used to manage the groups of information and their names is called a "file system".
```

如上是Wikipedia中关于[文件系统](https://en.wikipedia.org/wiki/File_system)的定义，简而言之，文件系统是组织文件如何高效存取的一个系统软件。

每种操作系统都至少有一种“标准文件系统“，提供了或好或差的一些功能，可以可靠而高效地执行所需的任务。Linux附带的Ext2/3文件系统是一种标准文件系统。为支持各种本机文件系统，且在同时允许访问其他操作系统的 文件， Linux 内核在用户进程（ 或C标准库）和文件系统实现之间引入了 一个 抽象 层。该抽象层称之为虚拟文件 系统（ Virtual File System）， 简称 VFS。

VFS用来提供一种操作文件、目录及其他对象的统一方法。另一方面，它必须能够与各种方法给出的具体文件系统的实现达成妥协。

###### 文件系统类型

1.基于磁盘的文件系统。如Ext2/3 ,FAT.所有这些文件系统都使用面向块的介质。从文件系统的角度来看，底层设备无非是存储块组成的一个列表，文件系统相当于对该列表是一个适当的组织方案。

2.虚拟文件系统。

3.网络文件系统

###### 磁盘： 

磁道：磁盘在格式化时被分成许多同心圆，这些同心圆轨迹叫做磁道。磁道从外向内从0开始编号，硬盘的每一个页面有300-1024个磁道。这些同心圆不是连续记录数据，而是被划分成一段段的圆弧，这些圆弧的角速度一样，由于径向长度不一样，所以线速度也不一样，外圈的线速度较内圈的线速度大，即同样的转速度下，外圈在同样时间段里，划过的圆弧长度要比内圈划过的圆弧长度大。 

扇区：每段圆弧叫做一个扇区，扇区从1开始编号，每个扇区中的数据作为一个单元同时读出或写入。

柱面：所有盘面上的同一磁道构成一个圆柱，通常称做柱面。

每个圆柱上的磁头由上而下从0开始编号，数据的读/写按柱面进行，即磁头读/写数据时首先在同一柱面内从0磁头开始进行操作，依次向下在同一柱面的不同盘面即磁头上进行操作，只有在同一柱面所有的磁头全部读/写完毕后磁头才转移到下一柱面（同心圆再往里的柱面），因为选取磁头只需要通过电子切换即可，而选取柱面则必须机械切换，电子切换相当快，比在机械上的磁头向邻近磁道移动快得多。

所以，数据的读/写按柱面进行，而不按盘面进行，也就是说，一个磁道写满数据后，就在同一柱面的下一个盘面来写，一个柱面写满后，才移到下一个扇区开始写数据，读数据也按照这种方式进行，这样就提高了硬盘的读/写效率。

访盘请求完成过程

1）确定磁盘地址（柱面号，磁头号，扇区号），内存地址（源/目）

当需要从磁盘读取数据的时候，系统会将数据的逻辑地址传递个磁盘，磁盘的控制电路按照寻址逻辑将逻辑地址翻译成物理地址，即确定要读的数据在哪个磁道，哪个扇区

2）为了读取这个扇区的数据，需要将磁头放到这个扇区上方，为了实现这一点：

- A. 首先必须找到柱面，即磁头需要移动对准相应磁道，这个过程叫做寻道，所耗费时间叫做寻道时间。
- B. 然后目标扇区旋转到磁头下，即磁盘旋转将目标扇区旋转到磁头下，这个过程耗费的时间叫做旋转时间。

3）即一次访盘请求（读/写）完成过程由三个动作组成：

- A. 寻道（时间）：磁头移动定位到指定磁道。
- B. 旋转延迟（时间）：等待指定扇区从磁头下旋转经过。
- C. 数据传输（时间）：数据在磁盘与内存之间的实际传输。

减少I/O的预读原理： 

由于存储介质的特性，磁盘本身存取就比主存慢很多，再加上机械运动耗费的时间，磁盘的存取速度往往是主存的几百分之一，因此为了提高效率，要尽量减少磁盘的I/O，磁盘往往不是严格地按需读取，而是每次都会预读，即使只需要一个字节，磁盘也会从这个位置开始，顺序向后读取一定长度的数据放入内存，这样做的理论依据是计算机科学中著名的局部性原理：

1. 当一个数据被用到时，其附近的数据一般来说也会被马上使用。
2. 程序运行期间所需要的数据通常比较集中。
3. 由于磁盘顺序读取的效率很高（不需要寻道时间，只需要很少的旋转时间），因此对于具有局部性的程序来说，预读可以提高I/O效率。

预读的长度一般为页（Page）的整数倍。页是计算机管理存储器的逻辑块，硬件及操作系统往往将主存和磁盘存储分割为连续的大小相等的块，每个存储块称为一页（在许多操作系统中，页的大小通常为4k），**主存和磁盘以页为单位交换数据 **，当程序要读取的数据不在主存中时，会触发一个缺页异常，此时系统会向磁盘发出读盘信息，磁盘会找到数据的起始位置并向后连续读取一页或几页的数据载入内存中，然后异常返回，程序继续运行。

#### Share

JNI详解 

关于JNI之前知之甚少，因为工作中从未涉及到JNI相关的编程也就从未对其有过过多了解，可以说除了知其为Java Native Interface缩写外再无过多认识。这是在我阅读https://www.ibm.com/developerworks/library/j-nativememory-linux/ 一文中看到关于JNI的零星知识，就顺便查阅了资料，这篇文章详细讲解了JNI以及JNI编程：

http://vdisk.weibo.com/s/tyWGcNVs40iW