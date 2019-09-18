<!--
 * @Description: In User Settings Edit
 * @Author: your name
 * @Date: 2019-09-15 22:58:56
 * @LastEditTime: 2019-09-18 15:41:55
 * @LastEditors: Please set LastEditors
 -->
# Kernel移植
## 1 内核说明
1. Linux内核 是一款Linux系统所有功能的集合！  
1. linux内核经过裁剪、移植之后，挂载文件系统，得到各种版本的发行版Linux系统。
1. linux内核是Linux系统的核心，全球只有唯一的一款Linux内核。

## 2 内核移植
1. 必备库  
   ```
     git 
     lzop  
     libc6:i386  
     libssl-dev
     libncurses5-dev
     build-essential
     liblz4-tool

   ```
2. 复制内核包至linux中并解压，进入kernel目录
3. 执行以下命令：
   ```
    make   clean      
    make   distclean       
    make   nanopi4_linux_defconfig    ARCH=arm64  
    make   menuconfig
   ```

4. 配置menuconfig:
   ```
    General setup  --->
            Cross-compiler tool prefix	[aarch64-linux-gnu-]
            Local version - append to kernel release 	[name]

    Networking support  --->
        Networking options  --->
            [*]  IP: kernel level autoconfiguration	

    File systems  --->
        Network File Systems  --->
            [*]  Root file system on NFS (NEW)

    Device Drivers  --->
        Generic Driver Options  --->
            [*]  Fallback user-helper invocation for firmware loading
   ```
5. 编译
   ```
    make ARCH=arm64 CROSS_COMLILE=aarch64-linux-gnu- <bordname> -j16
   ```
6. 编译结果kernel.img   resource.img，送至windows中烧录

## 3 内核源码分析
1. 普通文件
   ```
    Kbuild	：内核编译配置文件
    Kconfig	：用来生成配置菜单的选项
    Makefile	：管理整个源码工程
    README	：源码说明书

   ```

2. 目录文件
   ```
    arch/	        ：内核源码能够支持的CPU架构
                        arch/arm64/boot/dts/rockchip/rk3399-nanopi4-common.dtsi	    --> 板级参数
                        arch/arm64/configs/nanopi4_linux_defconfig	                -->  内核裁剪的默认配置参数
    block/	        ：块设备通信协议(接口)
    crypto/	        ：内核通用算法
    Documentation/	：内核提供的帮助文档
    drivers/		：内核支持的通用的设备驱动
    firmware/	    ：固件库
    fs/			    ：内核支持的文件系统
    include/		：内核层的通用头文件
    lib/			：内核层的通用库文件
    init/			：内核启动初始化代码
    ipc/			：IPC通信机制的实现代码
    kernel/		    ：内核的核心功能代码
    mm/			    ：内存管理的功能代码
    net/			：内核支持的网络通信协议栈
    scripts/		：内核中的通用脚本、被Makefile调用
    security/		：安全加密代码
    sound/	    	：音频协议/接口
    usr/			：内核和文件系统的接口（内核层和用户层的交互接口）
    virt/			：虚拟终端/虚拟功能

   ```
3. 内核源码组织架构  
    &emsp;内核源码包中，每一级目录下均包含以下三种文件：
    > Makefile  :   制定当前目录下的编译规则  
    > Kconfig   :   传递变量给makefile并生成meniconfig配置菜单的选项  
    > 功能源码   :   实现某项功能的内核源码（.c,.h）  
    &emsp;备注：内核源码中，管理整个内核的编译不是靠一个目录下的makefile完成编译，  
    而是靠顶层Makefile，通过层层调用各个目录下的makefile，从而完成整个系统的编译。  
    1. 内核makefile编写规则  
        &emsp;`obj-y += test.o`--> 把test.o编译进内核镜像中，即编译后的内核镜像包含该功能   
        &emsp;`obj-m += test.o`--> 把test.o编译成模块
        &emsp;`obj-n += test.o`--> 不编译  
        内核makefile的规则如下：
        ```
            obj-$(CONFIG_LED) += test.o         //CONFIG_LED的值由Kconfig传递过来，其中CONFIG_为固定格式，LED为Kconfig的格式
        ```
    2. 内核Kconfig编写规则  
       &emsp;定义变量    
         > config LED         //对应的menuconfig菜单栏中无下级菜单  
         > menuconfig LED     //对应的menuconfig菜单栏中有下级菜单  

       &emsp;设置变量类型以及菜单栏名称
         > bool		“ledtest”			//变量对应的菜单选项名称为“xyd led test”，变量有两种值"[y/n]"  
         > tristate	“xyd beep test”		//变量对应的菜单选项名称为“xyd beep   test”，变量有三种值<y/m/n>    

       &emsp;设置默认选项
         > default  	y               //初始值为y

       &emsp;选择依赖选项  
         > depends on    XYD_TEST 

       &emsp;选择跟随选项
         > select   XYD_TEST

       &emsp;选择帮助选项
         > help 
         > "帮助信息"



