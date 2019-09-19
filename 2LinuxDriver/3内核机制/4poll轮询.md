<!--
 * @Description: In User Settings Edit
 * @Author: your name
 * @Date: 2019-09-19 19:47:38
 * @LastEditTime: 2019-09-19 20:50:05
 * @LastEditors: Please set LastEditors
 -->
# poll轮询机制
## 1 poll轮询机制原理
1. 作用：使一个进程可以同时操作多个事件(设备驱动)
2. 应用层调用函数select();   poll();
3. 内核层函数接口：文件操作集合的.poll
4. 原理：
   > 当一个进程要操作多个事件时，可以把这些事件统一管理起来，一起阻塞等待；  
   > 等待其中某个或某些事件触发，这时阻塞消失，程序接着向下运行;  
   > 判断是哪些事件产生的触发即可完成工作。  
   > 等待事件触发由驱动层实现，完成判断则由应用层实现。  
   ![轮询机制示意图](https://github.com/TimChanCHN/pictures/raw/master/Linux/poll%E5%8E%9F%E7%90%86%E8%AF%B4%E6%98%8E.png)

## 2 驱动层的poll机制
1. 在驱动层中的poll机制，主要负责实现阻塞睡眠;
2. 在文件操作集合中的poll，只要有等待需求，必须要调用`poll_wait`;  
   该函数功能是把调用该函数的进程放入到wait_address等待队列中睡眠，  
   当当前设备触发 或 poll机制同步管理的其他事件触发，睡眠都将被唤醒，程序接着向下执行。
   ```c
    void  poll_wait(struct file * filp,  wait_queue_head_t * wait_address,  poll_table *p);  
    //  filp            :   文件结构体
    //  wait_address    :   等待队列
    //  p               :   poll机制同步管理的事件列表
   ```
   poll函数需要有返回值，用于提供给应用层判断：
   ```
    0	        ：  不是当前设备触发；是其他事件触发引起的当前设备唤醒
    POLLIN  	：  该触发是当前设备触发的，并且触发事件是输入事件
    POLLOUT	    ：  该触发是当前设备触发的，并且触发事件是输出事件
    POLLERR	    ：  该触发是当前设备触发的，并且触发事件是异常事件
    POLLRDNORM	：  该事件可读（不能独立使用，一般结合POLLIN使用）
    POLLWRNORM	：  该事件可写（不能独立使用，一般结合POLLOUT使用）

   ```

## 3 应用层的等待事件
1. poll()
   1. 功能： 使用事件结构体描述每一个事件。把要同步管理的事件存放在一个事件结构体数组中统一管理。  调用poll函数，阻塞等待，等待有事件触发，通过查看每一个事件结构体revents成员的值，判断触发事件。
   2. 事件结构体：
    ```c
    struct pollfd {
        int    fd;               /* 文件描述符 */
        short  events;          /* 指定的事件类型 */
        short  revents;        	/* 当有事件触发，该事件的返回值类型（接收设备驱动中poll函数的返回值） */
    };

    ```
    1. poll函数
    ```c
        int  poll(struct pollfd *fds,  nfds_t nfds,  int timeout);
        //  fds     :       事件结构体数组
        //  nfds    :       结构体数组的元素个数
        //  timeout :       阻塞等待时间。     -->      无限阻塞等待：负数

        返回值
        //  >0      :       触发事件的个数
        //  0       :       超时返回
        //  -1      :       错误
    ```
2. select
    1. 功能：使用文件描述符描述每一个事件。把要同步管理的事件存放在一个文件描述符数组中统一管理。  调用select函数，阻塞等待，等待有事件触发。有事件发生时，文件描述符的对应位会置一，  通过判断文件描述符置一位来判断触发事件类型。
    2. select函数
        ```c
            int select(int nfds, fd_set *readfds, fd_set *writefds,fd_set *exceptfds, struct timeval *timeout);
            // nfds          :       文件描述符最大值+1
            // readfs        :       读状态文件描述符数组
            // writefds     :       写状态文件描述符数组
            // exceptfds    :       异常状态文件描述符数组
            // timeout      :       阻塞等待时间
            返回值：
             //  >0      :       触发事件的个数
             //  0       :       超时返回
             //  -1      :       错误
        ```
    3. 对于由fd_set定义的文件描述符数组，若要对该数组进行增删操作，需要利用以下函数：
        ```c
        void FD_CLR(int fd, fd_set *set);    清除集合中指定fd。
        int  FD_ISSET(int fd, fd_set *set);   判断1024个FD中指定的fd是否符合*set，返        回真表示fd为1
        void FD_SET(int fd, fd_set *set);     把fd添加到集合中（本质是把第fd设置为1）
        void FD_ZERO(fd_set *set);        清空集合（本质是把集合中所的位设置为0）
        
        ```
    4. 备注：  每一次调用select后，作为函数形参的文件描述符数组会发生改变，未触发  的文件描述符会被清零。因此不能直接把定义的文件描述符直接用传参，应该先用一个中间变量  存储先定义的文件描述符，在用该中间变量用作传参。
