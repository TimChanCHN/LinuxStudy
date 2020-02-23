# LCD驱动
## 1.说明
1. LCD种类繁多，但是linux内核提供了一个对LCD通用操作(读写)的代码：fbmem.c
2. LCD的通用操作依然需要具体的LCD参数，则通过LCD驱动程序提供：s3c2410fb.c

## 2.LCD通用操作
1. fbmem_init：注册设备号以及文件操作集合（读写操作在文件操作集合中实现）
   1. register_chrdev(FB_MAJOR,"fb",&fb_fops)
   2. 创建类：class_create(THIS_MODULE, "graphics")
2. 在对fbmem操作之前，先注册设备：register_framebuffer(struct fb_info *fb_info)
3. fb_info从LCD驱动程序中得来

## 3.LCD驱动程序
1. LCD驱动程序的基本配置过程([LCD驱动设备编写示范](https://github.com/TimChanCHN/LinuxProgram/blob/master/LinuxDriver/JZ2440/LCD/lcd.c))：
   1. 分配fb_info空间
   2. LCD固有参数的相关设置
      1. 查看查LCD手册，配置LCD的基本参数，如x轴像素点，y轴像素点、分辨率、RGB数据格式等；
   3. 硬件相关操作（GPIO,频率等）
      1. 通过查看原理图，配置GPIO的输出、复用功能（设置为LCD专用引脚）；
      2. 通过查看芯片手册的时序图和LCD数据手册的时序图，把HSYNC,VSYNC相关的时间参数设置好，
         目的是设置LCD的控制寄存器；
      3. 设置显存配置：把显存的地址数据发送到LCD控制寄存器；
      4. 设置虚拟调色板（专门合成RGB数据的功能代码）
   4. 设备注册
> 先从该驱动程序的init函数入手，里面会有一个注册函数，之后重点关心里面的probe函数，它是LCD设配匹配上后的后续操作。

## 4. 硬件原理
1. 对于ARM而言，它本身内置一个LCD控制器，用于控制LCD怎么亮、哪里亮以及换行、换页的设置等。
2. JZ2440还有一个显存，显存等价于PC的显卡，专门用于控制输出的图像数据以及图像数据格式，把固定数据格式的图像数据发送给LCD控制器，实现图片的显示。
>s3c2440支持的数据格式是RGB565(16bits)

## 5.LCD子系统分析
1. 驱动端
   1. 参考s3c2410fb.c，该文件是LCD的驱动端。当设备端和驱动端匹配成功后，触发probe函数，在probe函数中正式执行生成lcd模块的程序；
   2. 驱动程序会接受来自设备端的设备结构体`platform_device`，该结构体提供了LCD的设备信息，其中，相关的设备参数也同样在设备端设置成功；
   3. probe函数中实现LCD驱动的配置基本上和第3节内容所述一样，因此不再赘述；

2. 设备端
   1. 已知linux驱动设备其实是由设备端和驱动端匹配成功才会产生probe函数，而两者是靠匹配设备名称来match的，LCD的设备名称在文件
      `/arch/arm/plat-samsung`的`struct platform_device s3c_device_lcd`中有所定义；
   2. 而设备端的硬件信息、参数配置文件则在另外一个文件`/arch/arm/mach-s3c24xx`中，在函数`smdk2440_machine_init`中通过使能函数       `s3c24xx_fb_set_platdata`把硬件参数添加结构体`platform_device`中；
   3. *_set_platdata函数中只有参数`struct s3c2410fb_mach_info`，因此当需要添加新的LCD设备时，则重新定义一个`struct s3c2410fb_mach_info`
      结构体即可,若需要更改LCD名称，则在第一点文件中更改即可；
   4. 结构体`struct s3c2410fb_mach_info`包含了GPIO设置、LCD显示参数配置（由结构体`struct s3c2410fb_display`确定）等
   5. 通过函数`platform_add_devices`把设备挂载到设备总线上，等待和驱动端匹配。

## 备注：
   LCD子系统的基本工作流程和平台设备驱动基本类似，都是由设备端和驱动端共同组成。

