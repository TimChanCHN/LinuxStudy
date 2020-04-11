<!--
 * @Description: In User Settings Edit
 * @Author: your name
 * @Date: 2019-09-15 22:58:56
 * @LastEditTime: 2019-10-21 20:20:20
 * @LastEditors: Please set LastEditors
 -->
# Linux基本命令

## man
## ls
## cd
## pwd
## useradd/passwd
1. 增加用户，密码
   
## su
## mkdir
## rmdir
## touch
## gedit
## cp
## rm
## mv
## tar
## cat
1. 作用：打印文件内容

## more
## clear
1. 作用：清除当前页面

## ln


## tree
1. 查看当前文件架构

## chmod
1. 格式：chmod [权限值] [filename]
2. 作用：修改权限值
3. 权限值说明：
   1. 3位8进制值，修改文件的读/写/可执行权限
   2. +/-(w/r/x)，修改文件的写/读/可执行权限
   
## umask


## gcc
1. 备注：对于不同平台，该编译器命令不一致

## ps
1. 格式：ps option
2. 作用：查看进程相关信息
3. 说明：
   1. -ef:  查看当前进程的父进程等信息
   2. -aux: 查看当前进程详细信息

## find
1. 格式：   find path option  {}
2. 说明：
   1. path: 查找路径
   2. option:
      1. -name: 查找名字，{}跟的是文件名称
      2. -size: 查找文件大小，{}跟的是文件大小
      3. -ctime: 查找最近更新的文件，{}是指最近的更新时间 
   
## grep
1. 格式：grep -r "string" path / grep "string" * -nR
2. 作用：在路径path中，查找含有"string"的文件

## date
1. 查看系统当前时间
2. 修改系统时间 date -s "2020-2-12 9:59:55"

## 补丁制作以及打补丁/卸载补丁
1. 制作补丁--diff
   ```bash
   diff -Naur  old/ new/ >test.patch
   #new是软件的最新版本， old是软件的老版本，通过该命令可以生成*.patch文件
   ```

2. 打补丁--patch
   > 当n=0时，从当前目录查找目的文件（夹）(直接使用补丁文件里面指定的路径)
   > 当n=1时，忽略掉第一层目录，从当前目录查找(去掉补丁文件指定路径最左的第1个’/’及前面所有内容)
   ```bash
   patch -p(n) < [补丁包路径] patch_name        #安装补丁
   patch -p(n) -R < [补丁包路径] patch_name     #卸载补丁
   ```
