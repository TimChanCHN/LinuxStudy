<!--
 * @Description: In User Settings Edit
 * @Author: your name
 * @Date: 2019-09-25 21:35:46
 * @LastEditTime: 2019-09-29 16:20:20
 * @LastEditors: Please set LastEditors
 -->
# IIC子系统

## 1 IIC通讯原理
> [IIC教程](https://blog.csdn.net/lingdongtianxia/article/details/81135456)
1. IIC:
   > IIC通讯有两根信号线：SDA/SCL，其中SDA是双向通信的信号线，SCL是时钟信号，IIC总线上可以挂载多个设备，每个设备都有独自的地址，当主机和从机的地址匹配成功时，二者即可以进行通信。控制SCL的设备即为主机。

2. IIC的地址一般为8位
   > 以24ATCxx为例，其地址格式为1010A0A1A2R/W, 1010为固件地址，A0A1A2为可编程寻址，R/W为读写控制位

3. IIC初始化、起始、停止控制
   >初始化： SDA/SCL均为高电平  
   >起始控制位： SCL为高电平，SDA为下降沿  
   >停止控制位： SCL为高电平，SDA为上升沿

4. 读写操作
   > SCL=0, 写操作  
   > SCL=1, 读操作

5. 相应信号
   > 当数据发送结束后，主机会从输出变成输入，用于检测回应信号。若从机接收到信号后，会发出一个低电平，主机则检测该回应信号（此时SCL应该为高电平）。

6. IIC对芯片的读操作
   >  1. 输入写命令后，输入待读设备的地址，并等待回应；  
   >  2. 输入读命令后，等待接收IIC总线上的数据  
   ![IIC对芯片读操作](https://github.com/TimChanCHN/pictures/raw/master/Linux/IIC%E8%AF%BB%E6%93%8D%E4%BD%9C.png)

7. IIC对芯片的写操作
   > 1. 输入读命令后，输入待写设备的地址，并等待回应；
   > 2. 输入待写数据，等待回应信号  
   ![IIC对芯片写操作](https://github.com/TimChanCHN/pictures/raw/master/Linux/IIC%E5%86%99%E6%93%8D%E4%BD%9C.png)

备注： IIC只能操作8位数据。


## 2 IIC子系统
1. IIC子系统用于管理内核中所有IIC总线以及IIC从设备；
2. 在芯片中每个IIC模块在内核中被描述成一条总线，也称之为适配器；
3. IIC子系统管理IIC从设备时，会把它管理成一个IIC客户端并注册到对应的适配器上；
4. 在IIC子系统中已经实现了标准的IIC标准协议并封装了标准的IIC操作函数，若在内核中对IIC从设备操作，只需要调用对应的IIC函数即可，内核会自动会指定的适配器进行操作。
5. IIC的客户端和驱动端如同平台设备总线一样，都是使用name元素匹配链接。
   ![内核IIC子系统示意图](https://github.com/TimChanCHN/pictures/raw/master/Linux/IIC%E5%AD%90%E7%B3%BB%E7%BB%9F%E7%BB%93%E6%9E%84.png)

## 3 IIC客户端

1. IIC客户端作用
   > 1. 提供客户端名称、用来和驱动端比较匹配  
   > 2. 提供i2c从设备的器件地址及所属总线(adapter)

2. 相关结构体
   1. IIC客户端结构体 struct  i2c_client
   ```c
    struct i2c_client {
        unsigned short 	flags;		        //i2c从设备的地址模式(8位 或 10位) 	<默认8位>
        unsigned short 	addr;		        //i2c从设备的器件地址
        char  name[I2C_NAME_SIZE];      	//i2c设备名称 -- 用来和驱动端比较匹配
        struct i2c_adapter*	 adapter;		//适配器 -- i2c从设备挂载的i2c总线对应的适配器
        struct device 	 dev;				//设备结构体 -- 描述一个设备的参数信息（可选）
        int	 irq;					    	//i2c从设备的中断引脚对应的中断号（可选）
    };
   ```
    2. 从设备设备信息结构体：struct i2c_board_info
    ```c
    struct i2c_board_info {
        char		type[I2C_NAME_SIZE];		//i2c设备名称 -- 传递给i2c客户端结构体的name成员
        unsigned short	flags;			//i2c从设备的地址模式 -- 传递给i2c客户端结构体的flags成员
        unsigned short	addr;			//i2c从设备的器件地址 -- 传递给i2c客户端结构体的addr成员
        void*	platform_data;			//i2c从设备的私有数据（可以传递任何数据）
        struct dev_archdata*  archdata;		//i2c从设备的架构数据
        struct device_node*   of_node;		//设备树节点展开得到的节点结构体
        struct fwnode_handle *fwnode;		//平台设备固件对应的设备节点信息
        int		irq;				//i2c从设备的中断引脚对应的中断号 -- 传递给i2c客户端结构体的irq成员
    };
    ```
    3. 获取适配器：i2c_get_adapter
    ```c
    struct i2c_adapter*  i2c_get_adapter(int nr);
    // nr   :   i2c总线编号
    return:
    // i2c总线对应的适配器结构体首地址

    ```
    > 备注：  
    > 1. 为了节省CPU资源，在注册完i2c客户端之后，利用函数`i2c_put_adapter`释放该适配器的索引;
    > 2. 客户端结构体不需要手动赋值，由注册函数获得。
    
    4. 客户端注册方法
       1. 函数`i2c_register_board_info()`
           > 特性：该函数可以一次性注册多个客户端。在内核刚启动时该函数即启动（i2c总线尚未注册），该函数把要注册的这些客户端信息存放在一个静态列表中。  
           > 当i2c总线注册成功、得到对应的适配器之后，适配器将自动遍历静态列表，把列表中的i2c设备信息注册为对应的客户端。

        2. 函数`i2c_new_device()`
           > 往指定的适配器上注册一个i2c客户端,该函数在注册i2c客户端时，会对提供的器件地址进行校验。
            ```c
            struct i2c_client*  i2c_new_device(struct i2c_adapter *adap, struct i2c_board_info const *info);
            ```
            
        3. 函数`i2c_new_probed_device()`
            > 往指定的适配器上注册一个i2c客户端。 如果i2c从设备的参数信息了解不充分，不知道i2c从设备的器件地址。这时可以提供一个可能的器件地址列表，调用该函数，从器件地址列表中校验每一个器件地址，直到找到第一个有效地址。

        4. 利用命令注册i2c客户端
            ```
            例子：echo  eeprom  0x50  >  /sys/bus/i2c/devices/i2c-3/new_device
            ```
            
         注销函数：
        ```c
        void  i2c_unregister_device(struct i2c_client *client);
        ```
    > 备注：  
    > 当客户端注册成功后，设备信息结构体`i2c_board_info`的信息会被赋值到客户端结构体`i2c_client`中。  
    > 其中数据对应关系如下，在应用层中可以直接调用
    > i2c_board_info.type           -->  i2c_client.name  
    > i2c_board_info.addr           -->  i2c_client.addr
    > i2c_board_info.platform_data  -->  i2c_client.dev.platform_data


## 4 IIC驱动端
1. 作用
   1. 提供一个客户端名称匹配列表，和客户端进行比较匹配；
   2. 客户端和驱动端匹配成功，合成完整的i2c设备驱动，在完整的设备驱动中对匹配成功的i2c客户端读写

2. 驱动端结构体
   ```c
    struct i2c_driver {
        unsigned int class;			//要匹配的i2c设备类型

        /* 当有客户端和驱动端匹配成功，自动调用该函数。该函数作为设备驱动的入口函数 */
        int  (*probe)(struct i2c_client *, const struct i2c_device_id *);		//i2c设备驱动真正的入口函数
        /* 当客户端和驱动端分离，自动调用该函数。在该函数中可以进行资源释放、注销 */
        int  (*remove)(struct i2c_client *);							//i2c设备驱动真正的出口函数

        int (*command)(struct i2c_client *client, unsigned int cmd, void *arg);	//把一些i2c操作封装为cmd
        struct device_driver   driver;						//设备驱动结构体 -- 用来描述一个设备驱动
        const struct i2c_device_id*  id_table;					//客户端名称匹配列表
        
        int (*detect)(struct i2c_client *, struct i2c_board_info *);	//i2c热插拔
            const unsigned short*   address_list;					//器件地址列表（自动检测的器件地址列表）
    };

   ```

   3. 驱动端注册函数： `i2c_register_driver`
   ```c
    int  i2c_register_driver(struct module* owner,  struct i2c_driver* driver)
   ```
    注销函数：  `i2c_del_driver` 
    ```c
    void  i2c_del_driver(struct i2c_driver *driver);
    
    ```


4. 相关的读写接口函数
   ```c
    //快速读写：
    i2c_smbus_read_byte(const struct i2c_client* client);
    i2c_smbus_write_byte(const struct i2c_client* client,  u8 value);
    
    //从某一客户端的指定地址空间中读取数据：
    i2c_smbus_read_byte_data();
    i2c_smbus_read_word_data();
    i2c_smbus_read_block_data();
    
    //往某一客户端的指定地址空间中写入数据：
    i2c_smbus_write_byte_data();
    i2c_smbus_write_word_data();
    i2c_smbus_write_block_data();
   ```

    `i2c_smbus_read_byte_data`-->从指定的客户端的指定地址空间中读取一个字节数据  
    `i2c_smbus_write_byte_data`-->往指定的客户端的指定地址空间中写入一个字节的数据，写入的数据是value  
    `i2c_smbus_xfer` -->所有的i2c读写函数都是在该函数基础上封装得到的  
    `i2c_transfer`-->用来实现连续读操作（以msg数组的方式连续读）

备注：  
当i2c的客户端和驱动端成功匹配后（如同平台设备驱动），会自动产生对应的模块，进入iic的probe函数中。而对iic设备的读写，  
可以在probe函数中进行。            
