# uboot分析
## 1.bootloader基本流程
1. 初始化硬件：关看门狗、设置时钟、设置SDRAM、NAND FLASH
2. 如果bootloader比较大，需要把它重定位到SDRAM
3. 把内核从NAND FLASH读到SDRAM
4. 设置要传给内核的参数
5. 跳转执行内核

## 2.u-boot官方执行流程
> u-boot目录(..\arch\arm\cpu\arm920t)中的Start.S文件，这是s3c2440的单板启动文件。
1. 设置成管理员权限：set the cpu to SVC32 mode
2. 关看门狗：turn off the watchdog
3. 屏蔽中断:mask all IRQs by setting all bits in the INTMR
4. 设置时钟分频比例
5. 设置内存控制器（内存相关操作）
6. cpu_init_crit部分初始化--lowlevel_init（内存控制器的初始化）
7. 设置栈(0x3000,0f80),并调用C函数board_init_f
   1. 调用函数数组init_sequence中的各个函数--完成具体的硬件操作
      1. 设置系统时钟、GPIO、定时器初始化等
      2. 对栈区的内存分布进行分配：即具体的栈内存地址，各分配多少的内存(如uboot存放大小、bss段大小等)
   2. 重定位代码(relocate_code)
      1. 把代码从NOR_FLASH复制到SDRAM中\[1\]
      2. 程序的链接地址是0，访问全局变量、静态数据、函数地址都是使用“基于地址0编译得到的地址”，但现在代码被复制到SDRAM中，需要通过重定位，使得这些地址是基于SDRAM的地址--如代码中的全局变量，其实是基于地址0的，但是通过重定位后，这些全局变量的地址，应该是基于SDRAM的
      3. 程序里面有些地址是不确定的（如库函数的地址），要到运行前才能确定，在relocate_code中，执行代码：fixabs
   3. 调用C函数board_init_r--第2阶段的单板初始化

## 3. 优化后的u-boot启动流程
1. 设置成管理员权限：set the cpu to SVC32 mode
2. 关看门狗：turn off the watchdog
3. 屏蔽中断:mask all IRQs by setting all bits in the INTMR
4. 设置系统时钟
5. cpu_init_crit部分初始化--lowlevel_init（内存控制器的初始化）
6. 设置栈(0x3000,0f80)
7. 初始化nand flash，使其支持nand启动(nand_init_ll)
8. 把nand里的代码复制到sdram里(copy_code_to_sdram)
   1. 如果是nor启动，因为nor flash和sdram的接口通用，所以把nor flash代码直接赋值到sdram的代码地址即可；
   2. 如果是nand启动，由于nand flash和sdram接口不通用，需要通过命令把nand flash的内容读出来，再发送给sdram[2]
9. 分配栈的排布(board_init_f)，即哪个地址对应哪个功能区域
10. 第二阶段初始化(board_init_r)--也是其他外设的初始化
11. 如果board_init_r没有打断u-boot的话，则成功进入了内核
12. 若有其他附加功能，则需要在板载文件：../include/configs/smdk2440.h添加对应的宏，从而使得makefile把对应功能添加到u-boot.bin中，从而使其增加新的功能
    1.  韦东山视频中是增加了u-boot支持Nor Flash, Nand Flash, DM9000网卡，其基本操作就是在该头文件中添加响相应得宏，使得该u-boot也支持该功能；
    2.  假如想把DM9000加入到u-boot中，在存储网卡文件目录下的Makefile中，查看要编译对应的.c文件需要定义什么宏，然后在smdk2440.h中添加对应的宏即可。
13. board_init_r成功执行，则可以成功进入内核。

## 备注：
\[1\]:如果uboot是存放于SD卡或者NandFlash，应该会有相同的操作，最终目的是使得uboot代码存放于SDRAM中。  
问题：如果其他单板没有这种类型的内存，该如何处理？
\[2\]:在韦东山的处理方式中，直接把代码送到特定地址中，因此不存在代码重定位的问题，因此函数board_init_f取消了relocate_code函数的调用

1. `norflash`的接口和`SDRAM`是一样的(`DATA线和ADDR线分离开`)，所以程序可以直接在`norflash`中直接运行(`norflash的地址也和ram的一样`);而`nandflash`的`DATA线和ADDR线是分时复用`的，因此接口和RAM并不一致，因此程序不能在`nandflash`上运行，在`nandflash`上的程序想要运行的话，需要先把程序复制到`SDRAM`上，才可以继续运行。
2. `FCLK`:内核时钟，`HCLK`:内存时钟，`PCLK`:外设时钟
3. 如何判断内存正常与否，可以先往内存里面某个地址写数据，再读该内存地址，结果是否与写进去的一致，是则说明内存正常；具体哪个地址是内存，可以查看芯片规格书与电路图，判断对应的内存芯片所在的地址段。
4. 如果要求单板支持`uboot`可以`nand flash`启动的时候，重定位的方式需要做些修改。
   1. 对于`nor`启动时，`uboot`的重定位方式是把`uboot`里面的地址值`加上一个偏移量`，使其重定位到SDRAM时，程序运行的时候可以找到`全局变量`、`静态变量`、`函数的地址`，同时对于库函数的地址，要求编译时增加`-pie`选项，这样可以在库运行前可以获取到库函数的入口地址；
   2. 对于`nand`启动，由于`nand`的接口和`ram`的`接口不一致`，代码不能在`nand`中运行，因此需要在`uboot`中，直接`定死uboot的变量地址`，从而当程序运行时，把uboot拷到sdram上，程序可以找到`全局变量`、`静态数据`、`函数入口地址`。
5. 在汇编语言中，如果跳转去某个函数，其中函数参数与寄存器的对应关系是：第一个参数对应r0，第二个参数对应r1，以此类推。