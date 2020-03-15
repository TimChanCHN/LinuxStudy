# NorFlash驱动设备操作
## 1. nor flash基本介绍
1. 与nand flash不同，nor flash是数据线和地址线分开，因此数据线只用于传输数据/命令
2. norflash的地址线与CPU的连接方式视nor flash的位宽而不一致。对于JZ2440而言，其板端的nor flash位宽是16bit,而CPU是一个地址对应一个字节。如果两者接线时一一对应的话，对于nor flash而言，则浪费了一半的空间；因此则需要CPU的地址接线与nor flash有一位的偏移（CPU的A1，对应nor flash的L0）。即CPU地址是xxxxxxx0、xxxxxxx1对于nor flash都被认为是xxxxxxx，从而可以达成既省空间，又可以提高软件运行效率的目的
3. nand flash具有容量大，管脚少，便宜的特点，但是其容易发生位反转，软件需要做ECC校验处理；对于nor flash而言，其容量少，管脚多，价格贵，但是不会发生位反转，可以可靠存储数据，适宜用于存储重要数据。

## 2. NorFlash驱动程序编写流程
1. 分配`map_info`结构体
2. 设置`map_info`结构体
   1. 理基地址(phys), 大小(size), 位宽(bankwidth), 虚拟基地址(virt)
   2. 利用`simple_map_init`进行简单的初始化工作
3. 调用nor flash协议提供的函数来识别nor flash：`do_map_probe`
4. 设置分区：`add_mtd_partitions`

## 3. NorFlash驱动使用步骤
1. insmod norflash.ko
2. 格式化文件系统
   1. flash_eraseall -j /dev/mtd1           // 对于norflash，一般使用jffs文件系统
3. 挂载文件系统
   1. mount -t jffs2 /dev/mtdblk1 /mnt
4. 进入文件系统/mnt进行编辑

## 4.小结
1. 对于块设备驱动，其对象是flash(nand/nor)，硬盘，内存等；
2. linux内核中已经包含了nor flash, nand flash的具体实现函数，而对应的驱动函数工作只是提供一些硬件层面的信息，通过调用对应协议提供的函数，可以把硬件参数和mtd_info结构体结合起来，其他工作则交给内核完成；
3. 块设备驱动的基本工作流程是把操作安排到队列中，内核块设备驱动通过调用对应的优化算法，实现对块设备进行高效率的读写操作
4. 当对块设备驱动加载并挂接文件系统成功后，进入挂接目录，即是对文件系统的操作。
5. CFI和JEDEC的区别
   1. CFI为公共Flash接口[Common Flash Interface]，用来帮助程序从Flash芯片中获取操作方式信息(发送命令，从nor flash的芯片里获取器件的各种参数，换芯片时，不需修改内核代码)
   2. JEDEC用来帮助程序读取Flash的制造商ID和设备ID（在内核里有这个nor flash 的所有信息，换另外一款芯片时，若内核里没有这款芯片的描述，需手动添加该器件的各种参数），以确定Flash的大小和算法
   3. 如果芯片不支持CFI，则要使用JEDEC
6. JZ2440的nor flash型号是MX29LV800BT/BB，在Linux3.4下，可能无法正常响应，cfi模式可能是官方bug，在`drivers/mtd/chips/cfi_uti.c， line63`有说明；而jedec模式则是列表中没有型号这个型号的`drivers/mtd/chips/jedec_probe.c  line294`
