# NandFlash设备驱动
## 1.硬件介绍
问题引入：
1. NandFlash和ARM之间只有数据线相连，如何传输地址？
   1. 在DATA0~DATA7之间既传输地址也传输数据，由引脚ALE控制，当ALE为高电平时，传输地址
2. 操作NandFlash前需要先发命令，如何传入命令？
   1. 在DATA0~DATA7之间既传输地址也传输数据还传输命令，引脚ALE为高电平时传输地址，引脚CLE为高电平时传输命令，引脚ALE,CLE为低电平时传输数据
3. NandFlash的数据线同时还和SARAM，NorFlash等器件公用，如何区分？
   1. 片选信号CS
4. NandFlash如何判断命令、数据、地址的烧写？
   1. 通过状态引脚RnB来判断，低电平是繁忙，高电平是空闲。
5. 操作NandFlash的步骤
   1. 发送命令
   2. 发送地址
   3. 读/写数据
6. 对于没有专门的NandFlash控制器的芯片而言，可以通过GPIO模拟，把数据送到NandFlash中，而对于有NandFlash控制器的芯片，可以对其对应的寄存器操作，就可以实现对NandFlash的操作
   1. 发命令--寄存器NFCMMD
   2. 发地址--寄存器NFADDR
   3. 发数据--寄存器NFDATA
   4. 读数据--val=NFDATA
   5. 片选信号--NFCONT,bit1 

## 2.NandFlash驱动框架以及编码步骤
1. 见同目录下的文件：flash程序驱动框架
2. 程序编写流程
   1. 分配nand_chip结构体
   2. 设置nand_chip结构体内容
      1. 设置片选
      2. 设置cmd_ctl函数
      3. 设置IO_R/W对应的虚拟地址
      4. 设置空闲信号：dev_ready
      5. 设置软件ECC校验：ecc.mode  
      > 对于linux3.4而言，nand_chip结构体内部的函数指针需要重新设置，而不能使用默认的nand_chip本身函数
    3. 硬件相关操作
       1. 设置nand时钟
       2. 设置nfconf：根据nand flash手册分配nand时间参数
    4. 使用nand_chip
       1. 分配结构体mtd_info，并绑定nand_chip
       2. 识别nand_flash并构造mtd_info，函数`nand_scan_ident`和`nand_scan_tail`共同完成，在lnux2.6，可以由函数`nand_scan`完成
    5. 设置分区
          1. 使用函数`mtd_device_parse_register`
 3. 利用nandflash.ko挂载文件系统
    1. make menuconfig去掉NAND驱动，并重新烧写新内核
    2. insmod nandflash.ko
    3. 格式化
       1. flash_eraseall /dev/mtd3      //本身就是yaffs文件系统
       2. flash_erase                   // 清除扇区
    4. 挂接
       1. mount -t yaffs /dev/mtdblock3 /mnt
    5. 进入/mnt即可以编辑文件，文件会存放到nandflash中
    6. 取消挂接
       1. umount /mnt
 4. flash_erase等命令需要从本目录下的安装包中获得
    1. 复制安装包到linux虚拟机中，解压并进入目录util
    2. 修改Makefile，把交叉编译器取消注释
    3. make
    4. 复制当前目录的flash_eraseall/flash_erase到文件系统目录中即可

## 3. 其他
1. NandFlash的优势是成本低，但是有着其固有的缺点，位反转。位反转是指当读出一个page的数据时，里面的某些bit数据可能会发生反转。针对这个问题，NandFlash在每一页数据后都加一个64bit的空间(OOB:out of blank)，用于存储每一页数据的ECC校验码。通过计算每一页数据的ECC校验码，和OOB区域的ECC校验码进行比较，从而可以判断出该页数据是否发生位反转。
2. NandFlash最根本的使用过程：
   1. 写：
      1. 写page
      2. 生成ECC
      3. 把ECC写入OOB中
   2. 读
      1. 读page
      2. 读OOB的ECC
      3. 计算page的ECC码
      4. 比较2和3的ECC码