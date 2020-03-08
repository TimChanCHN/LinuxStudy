# USB驱动
## 1. 相关概念
1. USB是主从结构
   1. 所有的USB传输，都是从USB主机发起；
2. USB的传输类型
   1. 控制传输：可靠，时间没有保证，如：USB设备识别过程；
   2. 批量传输：可靠，时间没有保证，如：U盘
   3. 中断传输：可靠，实时，如：USB鼠标
   4. 实时传输：不可靠，实时，比如USB摄像头
3. USB传输对象--端点
   1. 如给U盘读数据时，是往端点1读数据，写数据时，是往端点2写数据；
   2. 每个端点只支持只支持一个方向的数据传输，端点0除外，端点0既能输入也能输出
4. 每一个端点都有传输类型，传输方向
5. 在USB通讯过程中，输入输出是对于USB主机而言
6. USB驱动程序作用：
   1. 识别USB设备；
   2. 查找并安装对应的设备驱动程序
   3. 提供USB读写函数
7. USB驱动程序框架：
app:  
--------------------------------  
        USB设备驱动程序  
内核  --------------------------   
        USB总线驱动程序  
--------------------------------  
         USB主机控制器  
       UHCI  OHCI  EHCI  
硬件    ----------------  
            USB设备  

## 2. USB总线驱动程序说明
1. 识别USB设备
2. 查找并安装对应的设备驱动程序
3. 提供USB读写函数
   [USB总线驱动程序说明]()

## 3. USB设备驱动编写过程
1. 把USB设备和输入子系统关联起来
   1. 目的：让应用层可以获取USB数据，如果不加入输入子系统，USB设备驱动依然可以工作，但是获得的数据只能在内核层中传输，无意义；
   2. 分配一个input_dev;
   3. 把输入设备设置成何种输入类型，何种输入事件；
   4. 注册到输入子系统总线上。
2. 硬件操作--先对数据传输三要素初始化           
   1. 获得端点：usb_rcvintpipe(dev, endpoint->bEndpointAddress);
      1. 端点的获取需要另外一些结构体的支持：
        ```c
        struct usb_host_interface *interface;
    	struct usb_endpoint_descriptor *endpoint;

        interface = intf->cur_altsetting;
        endpoint = &interface->endpoint[0].desc;
        ```
    2. 通过endpoint获取数据包长度
       1. 	len = endpoint->wMaxPacketSize;
    3. 把物理地址和虚拟地址关联起来
       1. 	buffer = usb_alloc_coherent(dev, len, GFP_ATOMIC, &usb_buffer_phys);
3. 利用“三要素”构造出urb(usb request block)，一个类似于缓存区的东西
   1. 分配struct urb*：`usb_alloc_urb`
   2. 初始化urb_init:`usb_fill_int_urb`,同时该结构体中的元素transfer_dma/transfer_flags也需要初始化
   3. 使用`usb_submit_urb`--该函数具有可以触发urb中断的功能，urb中断在init函数中实现
   4. 在urb中断函数中，每执行完一次中断，需要调用一次`usb_submit_urb`，否则无法再次触发urb中断函数

## 引入：
1. 以windows为例，没有“驱动程序”，为何知道是“android phone”？
   1. windows已经有了USB的总线驱动程序，接入USB设备后，是总线驱动程序识别到设备时“android phone”,提示用户安装设备驱动程序；
   2. USB总线驱动程序负责识别USB设备，并给USB设备找到对应的驱动程序；
2. USB种类繁多，为什么一接入电脑就能识别出来？
   1. PC和USB设备都得遵守一些规则：USB总线驱动程序发送某些命令给USB设备想获取设备信息（描述符），而USB则必须返回描述符给USB总线驱动程序（PC）。
3. USB接有多个USB设备，如何分辨他们？
   1. 每一个USB设备接入PC时，USB总线驱动程序都会给它分配一个编号，从而介在USB总线上的每一个USB设备都会有自己的编号（地址）；
   2. PC想访问某个USB设备时，发出的命令都含有对应的编号（地址）
4. USB设备刚接入PC时，还没有编号，PC时如何和USB设备通信？
   1. 新接入的USB设备编号都是0，在未分配序号时，PC和USB设备都是靠该编号进行通信。
5. 为什么一接入USB设备，PC就能发现？
   1. USB接口的硬件结构时VCC,D+,D-,GND，在主机内部，D+,D-分别接有15k的下拉电阻，而USB设备中D+/D-接有一个1.5K的上拉电阻，当USB一接入，PC就可以识别到USB设备；
   2. D+接上拉电阻时，USB设备是普通设备，D-接上拉电阻时，USB设备是高速设备。