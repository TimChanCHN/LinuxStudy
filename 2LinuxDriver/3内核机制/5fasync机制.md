<!--
 * @Description: In User Settings Edit
 * @Author: your name
 * @Date: 2019-09-20 19:14:30
 * @LastEditTime: 2019-10-22 14:27:58
 * @LastEditors: Please set LastEditors
 -->
# fasync机制

## 1 fasync机制介绍
1. 一种设备驱动和进程之间的异步机制； 
2. 本质上驱动到进程的单向通信，只能是设备驱动发信号、进程接收信号；
3. 设备驱动只能给进程发送唯一的信号：SIGIO；
4. 工作机理：
   >进程独立执行自己的操作、不需要关心设备驱动的情况。设备驱动中使用外部中断检测外部触发，当检测到外部触发之后，设备驱动把触发以信号的方式发送给进程。这时进程对该信号进行处理，处理完之后、继续执行主程序。（示意图请见下图）
   ![fasync机制示意图](https://github.com/TimChanCHN/pictures/raw/master/Linux/fasync%E5%8E%9F%E7%90%86.png)

## 2 fasync机制的实现
1. 在驱动层中定义fasync函数，该函数作用是给应用层发送命令，而应用层也调用fasync接口函数，用以实现fasync机制；
2. 驱动要发送命令给进程，需要获取进程PID--通过fasync接口函数把PID传递给设备驱动；
3. 设备驱动通过调用函数`kill_fasync`把信号发送给进程

## 3 设备驱动实现fasync机制
1. 如同其他机制，需要定义并实现fasync文件操作函数-->目的是获取进程PID；
   ```c
    //fasync文件操作函数中，调用下函数即可以获得PID
    int  fasync_helper(int fd, struct file * filp, int on, struct fasync_struct **fapp);
    // fd   :   文件描述符
    // filp :   文件结构体
    // on   :   使能异步机制的条件
    // fapp :   异步机制结构体（若有多个进程，则这里应该时一个结构体数组，所以需要用二重指针）
    return:
    //  1   :   添加了一个进程到队列中
    //  0   ：  没有执行任何操作
    //  其他：  错误码
   ```

2. 当条件满足时，调用信号发送函数，给进程发送`SIGIO`信号。
   ```c
    //调用函数kill_fasync发送信号
    void  kill_fasync(struct fasync_struct **fp,  int sig,  int band);
    //  fp  :   异步机制队列
    //  sig :   发送的信号，SIGIO
    //  band:   信号类型
    //   POLL_IN:输入事件
    //   POLL_OUT:输出事件
    //   POLL_ERR:异常事件
   ```

## 4 应用程序的 fasync机制
1. 把进程PID传递给设备驱动
   > 不同于其他read/write/open等函数，在应用层中，fasync并没有对应的接口函数，若要把进程PID送给设备驱动，需要用上对文件描述符操作的接口函数`fcntl`
    ```c
     int  fcntl(int fd, int cmd, ... /* arg */ );
     // fd  :   文件描述符
     // cmd :   要执行的操作
     //     F_GETFL(void)   :   获得当前文件描述符当前设置的操作方式
     //     F_SETFL(int)    :   重新设置文件描述符中的操作方式
     //     F_SETOWN(int)   :   把进程PID设置到文件描述符中
     // arg :   可变参数,用于给cmd传参
     example:

     int  fd = open(“/dev/key_fasync”,  O_RDWR);
    fcntl(fd,  F_SETOWN,  getpid());	    	//设置进程PID到文件描述符中
    int  old = fcntl(fd,  F_GETFL);		        //获取原来的操作方式,必须要有该步操作，否则会覆盖file结构体的相关操作
    fcntl(fd,  F_SETFL,  old | FASYNC);     	//在操作方式上添加FASYNC，将自动调用设备驱动中的fasync()函数

    ```

2. 接收信号，并处理对应的函数
   ```c
    //信号捕捉函数
    sighandler_t signal(int signum, sighandler_t handler);
    //  signum  :   信号类型
    //  handler :   操作函数
   ```





