<!--
 * @Description: In User Settings Edit
 * @Author: your name
 * @Date: 2019-09-15 22:58:56
 * @LastEditTime: 2019-09-18 10:39:54
 * @LastEditors: Please set LastEditors
 -->
# u-boot移植
## 1 u-boot
1. uboot是系统运行之前的一段引导启动代码
2. uboot的主要功能是平台初始化(只是做十分基础的初始化，比如系统时钟，不会把全部外设都初始化，部分外设的初始化交给设备驱动)
3. 在开发板上电之后，首先要运行uboot代码，对开发板进行初始化，uboot运行完成之后，引导加载kernel、rootfs，跳转执行kernel、rootfs。
4. uboot在引导完内核之后，uboot消失，CPU开始执行内核代码。
5. uboot引导内核的过程是一个不可逆的过程。
6. 不同厂家芯片的u-boot程序不一样，因此其不具备通用性。

## 2 u-boot移植
1. 复制源码包`u-boot-nanopi.tar.bz2`至linux下并解压(压缩包可以删除)；
2. 进入u-boot目录，进行以下操作：
   ```
    make   clean					//清理中间文件
    make   distclean				//清除配置文件
    make   rk3399_linux_defconfig	//把uboot配置为rk3399开发板使用
   ```
3. 修改u-boot启动延时：
   ```
    vim  include/configs/rk33plat.h  +202
     --> #define CONFIG_BOOTDELAY      1                //1为延时时间
   ```
4. 进入menuconfig对u-boot进行配置（本质就是裁剪u-boot）,在RK3399中不需裁剪
5. 编译，生成镜像：
   ```
    //ARCHV         :       选择芯片架构
    //CROSS_COMPILE ：      选择编译器
    //-j16          :       加快编译速度
    make ARCHV=arm64 CROSS_COMPILE=aarch64-linux-gnu- -j16

   ```
6. 把目录下生成的rk3399_loader_v1.12.109.bin、uboot.img、trust.img复制到windows下，用专门的烧录工具烧录。

## 3 u-boot源码分析
1. __普通文件__
   ```
    config.mk	 ：Makefile的配置文件
    Kbuild 	 ：编译配置文件（被Makefile调用）
    Kconfig 	 ：生成配置菜单选项
    Makefile	 ：管理整个uboot源码工程
    README	 ：源码说明书

   ```
2. __目录文件__
   ```
    api/		：uboot提供的少量接口函数，供uboot开发使用
    arch/	    ：当前uboot能够支持的CPU架构
                  arch/arm/cpu/armv8/rk33xx/
    board/  	：当前uboot能够支持的开发板
                  board/rockchip/rk33xx/
    common/	    ：uboot提供的命令的源代码
    configs/	：每一种开发板的uboot默认配置
                  configs/rk3399_linux_defconfig
    disk/		：存储设备接口
    drivers/	：uboot中实现的裸机驱动
    fs/		    ：当前uboot支持的文件系统（uboot上可直接挂在的文件系统类型）
    include/	：uboot源码中的通用头文件
                  include/rk33plat.h	 --> 一款开发板的默认配置参数
    lib/		：uboot源码中的通用库文件
    net/		：uboot支持的网络协议栈
    scripts/	：uboot源码包中通用脚本
   ```


