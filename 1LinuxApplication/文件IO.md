<!--
 * @Description: In User Settings Edit
 * @Author: your name
 * @Date: 2019-09-15 22:58:56
 * @LastEditTime: 2019-10-21 23:45:34
 * @LastEditors: Please set LastEditors
 -->
# 文件IO
## 1 概述
1. 本质是一组接口函数，属于系统直接调用，在应用层中没有缓存区一说
2. linux中的其中基本文件均能打开
3. 打开文件，会返回一个数字，被称之为文件描述符，在对该文件进行读写等操作时，该int数字会被传进内核，内核会找到相应的文件结构体，从而实现对文件的操作；

# 2 常用函数
1. open--> 文件打开、创建
2. close--> 文件关闭
3. read--> 文件读操作
4. write--> 文件写操作
5. turncate-->改变文件大小【在服务器上传下载文件会用到】
6. lseek-->改变文件的读写位置
7. opendir-->打开目录文件
8. readdir-->读取目录文件内容
9. closedir-->关闭目录文件
10. mkdir-->创建目录文件
