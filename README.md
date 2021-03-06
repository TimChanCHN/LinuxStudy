<!--
 * @Description: In User Settings Edit
 * @Author: your name
 * @Date: 2019-09-15 22:58:56
 * @LastEditTime: 2019-10-22 09:53:19
 * @LastEditors: Please set LastEditors
 -->
# LinuxStudy （开发平台RK3399）
## 1 Linux Application  

1. [Linux简要概述](https://github.com/TimChanCHN/LinuxStudy/blob/master/1LinuxApplication/Linux%E7%AE%80%E8%A6%81%E6%A6%82%E8%BF%B0.md)  
2. [Linux基本命令](https://github.com/TimChanCHN/LinuxStudy/blob/master/1LinuxApplication/Linux%E5%9F%BA%E6%9C%AC%E5%91%BD%E4%BB%A4.md) 
3. [shell脚本](https://github.com/TimChanCHN/LinuxStudy/blob/master/1LinuxApplication/shell%E8%84%9A%E6%9C%AC.md) 
4. [库制作](https://github.com/TimChanCHN/LinuxStudy/blob/master/1LinuxApplication/%E5%BA%93%E5%88%B6%E4%BD%9C.md) 
5. [Makefile](https://github.com/TimChanCHN/LinuxStudy/blob/master/1LinuxApplication/Makefile.md) 
6. [标准IO](https://github.com/TimChanCHN/LinuxStudy/blob/master/1LinuxApplication/%E6%A0%87%E5%87%86IO.md) 
7. [文件IO](https://github.com/TimChanCHN/LinuxStudy/blob/master/1LinuxApplication/%E6%96%87%E4%BB%B6IO.md) 
8. [进程](https://github.com/TimChanCHN/LinuxStudy/blob/master/1LinuxApplication/%E8%BF%9B%E7%A8%8B.md) 
9. [信号](https://github.com/TimChanCHN/LinuxStudy/blob/master/1LinuxApplication/%E4%BF%A1%E5%8F%B7.md) 
10. [线程](https://github.com/TimChanCHN/LinuxStudy/blob/master/1LinuxApplication/%E7%BA%BF%E7%A8%8B.md) 
11. [网络编程](https://github.com/TimChanCHN/LinuxStudy/blob/master/1LinuxApplication/%E7%BD%91%E7%BB%9C%E7%BC%96%E7%A8%8B.md) 
12. [其他（时间获取）](https://github.com/TimChanCHN/LinuxStudy/blob/master/1LinuxApplication/%E5%85%B6%E4%BB%96%EF%BC%88%E6%97%B6%E9%97%B4%E8%8E%B7%E5%8F%96)

## 2 Linux Driver
### 2.1 系统移植

1. [uboot移植](https://github.com/TimChanCHN/LinuxStudy/blob/master/2LinuxDriver/1%E7%B3%BB%E7%BB%9F%E7%A7%BB%E6%A4%8D/1uboot%E7%A7%BB%E6%A4%8D.md) 
1. [kernel移植](https://github.com/TimChanCHN/LinuxStudy/blob/master/2LinuxDriver/1%E7%B3%BB%E7%BB%9F%E7%A7%BB%E6%A4%8D/2kernel%E7%A7%BB%E6%A4%8D.md) 
1. [文件系统搭建](https://github.com/TimChanCHN/LinuxStudy/blob/master/2LinuxDriver/1%E7%B3%BB%E7%BB%9F%E7%A7%BB%E6%A4%8D/3%E6%96%87%E4%BB%B6%E7%B3%BB%E7%BB%9F%E6%90%AD%E5%BB%BA.md) 
2. [menuconfig菜单介绍](https://github.com/TimChanCHN/LinuxStudy/blob/master/2LinuxDriver/1%E7%B3%BB%E7%BB%9F%E7%A7%BB%E6%A4%8D/4menuconfig%E4%BB%8B%E7%BB%8D.md) 

###  2.2 内核开发--模块以及设备驱动
1. [模块以及内核开发方式](https://github.com/TimChanCHN/LinuxStudy/blob/master/2LinuxDriver/2%E8%AE%BE%E5%A4%87%E9%A9%B1%E5%8A%A8%E7%BC%96%E7%A8%8B/1%E6%A8%A1%E5%9D%97%E4%BB%A5%E5%8F%8A%E5%86%85%E6%A0%B8%E5%BC%80%E5%8F%91%E6%96%B9%E5%BC%8F.md) 
2. [字符设备](https://github.com/TimChanCHN/LinuxStudy/blob/master/2LinuxDriver/2%E8%AE%BE%E5%A4%87%E9%A9%B1%E5%8A%A8%E7%BC%96%E7%A8%8B/2%E5%AD%97%E7%AC%A6%E8%AE%BE%E5%A4%87.md) 
3. [杂项字符设备](https://github.com/TimChanCHN/LinuxStudy/blob/master/2LinuxDriver/2%E8%AE%BE%E5%A4%87%E9%A9%B1%E5%8A%A8%E7%BC%96%E7%A8%8B/3%E6%9D%82%E9%A1%B9%E5%AD%97%E7%AC%A6%E8%AE%BE%E5%A4%87.md) 
4. [早期字符字符设备](https://github.com/TimChanCHN/LinuxStudy/blob/master/2LinuxDriver/2%E8%AE%BE%E5%A4%87%E9%A9%B1%E5%8A%A8%E7%BC%96%E7%A8%8B/4%E6%97%A9%E6%9C%9F%E5%AD%97%E7%AC%A6%E8%AE%BE%E5%A4%87.md) 
5. [标准字符设备](https://github.com/TimChanCHN/LinuxStudy/blob/master/2LinuxDriver/2%E8%AE%BE%E5%A4%87%E9%A9%B1%E5%8A%A8%E7%BC%96%E7%A8%8B/5%E6%A0%87%E5%87%86%E5%AD%97%E7%AC%A6%E8%AE%BE%E5%A4%87.md) 

### 2.3 Linux内核机制
1. [外部中断开发](https://github.com/TimChanCHN/LinuxStudy/blob/master/2LinuxDriver/3%E5%86%85%E6%A0%B8%E6%9C%BA%E5%88%B6/1%E5%A4%96%E9%83%A8%E4%B8%AD%E6%96%AD.md)
2. [内核定时器](https://github.com/TimChanCHN/LinuxStudy/blob/master/2LinuxDriver/3%E5%86%85%E6%A0%B8%E6%9C%BA%E5%88%B6/2%E5%86%85%E6%A0%B8%E5%AE%9A%E6%97%B6%E5%99%A8.md)
3. [中断底半部](https://github.com/TimChanCHN/LinuxStudy/blob/master/2LinuxDriver/3%E5%86%85%E6%A0%B8%E6%9C%BA%E5%88%B6/3%E4%B8%AD%E6%96%AD%E5%BA%95%E5%8D%8A%E9%83%A8.md)
4. [poll机制](https://github.com/TimChanCHN/LinuxStudy/blob/master/2LinuxDriver/3%E5%86%85%E6%A0%B8%E6%9C%BA%E5%88%B6/4poll%E8%BD%AE%E8%AF%A2.md)
5. [fasync](https://github.com/TimChanCHN/LinuxStudy/blob/master/2LinuxDriver/3%E5%86%85%E6%A0%B8%E6%9C%BA%E5%88%B6/5fasync%E6%9C%BA%E5%88%B6.md)

### 2.4 内核总线、子系统
1. [平台设备驱动](https://github.com/TimChanCHN/LinuxStudy/blob/master/2LinuxDriver/4%E5%86%85%E6%A0%B8%E6%80%BB%E7%BA%BF%E3%80%81%E5%AD%90%E7%B3%BB%E7%BB%9F/1%E5%B9%B3%E5%8F%B0%E8%AE%BE%E5%A4%87%E9%A9%B1%E5%8A%A8.md)
2. [输入子系统](https://github.com/TimChanCHN/LinuxStudy/blob/master/2LinuxDriver/4%E5%86%85%E6%A0%B8%E6%80%BB%E7%BA%BF%E3%80%81%E5%AD%90%E7%B3%BB%E7%BB%9F/2%E8%BE%93%E5%85%A5%E5%AD%90%E7%B3%BB%E7%BB%9F.md)
3. [IIC子系统](https://github.com/TimChanCHN/LinuxStudy/blob/master/2LinuxDriver/4%E5%86%85%E6%A0%B8%E6%80%BB%E7%BA%BF%E3%80%81%E5%AD%90%E7%B3%BB%E7%BB%9F/3IIC%E5%AD%90%E7%B3%BB%E7%BB%9F.md)
4. [LCD子系统](https://github.com/TimChanCHN/LinuxStudy/blob/master/2LinuxDriver/4%E5%86%85%E6%A0%B8%E6%80%BB%E7%BA%BF%E3%80%81%E5%AD%90%E7%B3%BB%E7%BB%9F/4LCD%E9%A9%B1%E5%8A%A8.md)

### 2.5 设备树开发
1. [设备树](https://github.com/TimChanCHN/LinuxStudy/blob/master/2LinuxDriver/5%E8%AE%BE%E5%A4%87%E6%A0%91%E5%BC%80%E5%8F%91%E3%80%81%E9%A9%B1%E5%8A%A8%E7%A7%BB%E6%A4%8D%E3%80%81%E4%BA%A7%E5%93%81%E5%8F%91%E5%B8%83%E3%80%81%E9%A1%B9%E7%9B%AE%E5%BC%80%E5%8F%91/1%E8%AE%BE%E5%A4%87%E6%A0%91.md)

# 附录
1. [SourceInsight使用技巧__](https://github.com/TimChanCHN/LinuxStudy/blob/master/%E9%99%84%E5%BD%95/SourceInsight%E4%BD%BF%E7%94%A8%E6%8A%80%E5%B7%A7.md)
