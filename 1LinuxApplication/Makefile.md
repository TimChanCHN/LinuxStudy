<!--
 * @Description: In User Settings Edit
 * @Author: your name
 * @Date: 2019-09-15 22:58:56
 * @LastEditTime: 2019-10-21 22:02:30
 * @LastEditors: Please set LastEditors
 -->
# Makefile

## 1 makefile概述
1. makefile文件描述了整个工程的编译，链接等规则
2. Make是一个命令工具，它解释makefile中的指令

## 2 makefile规则
  基本规则   
   目标文件：依赖文件  
   (tab)命令  
   ...  

## 3 变量的使用
1. 赋值符
   =    ：  递归赋值  
   +=   :   追加赋值（在当前文件后面增加右边内容）
   ：=  :   直接赋值  
   ?=   :   如果变量为空就赋值，否则无效    

2. 取值：$(val)

## 4 命令行选项
-C  :   读入指定命令的makefile  
-I  :   指定头文件目录

## 5 预定义变量
$@  :   全部目标文件  
$^  :   全部依赖文件  
$<  :   第一个依赖文件

## 6 Makefile中的规则
1. .o文件会自动依赖.c/.cc文件
2. 通配符：     %.o:%.c     --> 把目录下全部的.c文件转化成.o文件  

## 7 函数
1. 获取匹配模式文件名函数— wildcard
   $(wildcard PATTERN...)-->把当前目录中符合PATTERN模式的文件取出  

2. 模式替换函数— patsubst  
   $(patsubst PATTERN,REPLACEMENT,TEXT)  
   --> 搜索“ TEXT”中以空格分开的单词，将否符合模式“ TATTERN”替换为“ REPLACEMENT”  

## 7 内核Makefile规则
在内核编程中，有一个特殊命令：
obj-$(OPERATION) += name.o      
--> 当OPERATION为y时，name.o直接被编译进去内核  
--> 当OPERATION为m时，name.o直接被编译成模块.ko，被外部调用  

该命令为指定该文件的编译方式，实际上完成编译的动作由该命令完成：  
`make -C $(KDIR) M=$(PWD) modules  `
KDIR:顶层Makefile所在的路径  
PWD:当前模块文件所在的路径  
modules: 顶层Makefile中关于编译模块的命令  
备注：在内核Makefile中，名称必须为Makefile，不能是makefile

## makefile和shell的区别
1. 通配符不一致：   *           %  
2. 引用变量不同：   ${}         $()
3. makefile的目标里面才能执行shell脚本
4. 赋值：        不允许有空格   允许有空格
5. 在管理项目时，makefile更加方便，语句更加简介（.o文件自动寻找.c，当一个找不到一个文件时，会向下逐次递归寻找）
6. 执行方式不一致： shell-->三种执行方式，并且把脚本里面全部命令都执行  
                   makefile->每次只执行一个目标
