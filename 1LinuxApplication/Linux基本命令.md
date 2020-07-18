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
2. 作用：查找文件
3. 说明：
   1. path: 查找路径
   2. option:
      1. -name: 查找名字，{}跟的是文件名称
      2. -size: 查找文件大小，{}跟的是文件大小
      3. -ctime: 查找最近更新的文件，{}是指最近的更新时间,如最近1天，{}为-1 
   
## grep
1. 格式：grep -r "string" path / grep "string" * -nR
2. 作用：在文件中查找字符串
3. 作用：在路径path中，查找含有"string"的文件
4. 可以在某些查看指令后，通过grep来筛选需要查看的文档
   1. ps -aux | grep bzl_robot

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

## dpkg
1. 和.deb安装包相关操作
   1. -i : 安装
   2. -l : 查看已经安装的包
   3. -r : 卸载

## top
1. 查看内存情况以及CPU使用情况
   1. 在顶层的KiB Mem中，total是内存总量，free是空闲内存，used是已使用内存，buff/cache是缓存

## file
1. file+文件名：查看文件类型以及编码格式

## du
1. du+文件：查看文件大小，以MB为单位,-sh则会显示单位

## top
1. 查看系统的运行情况：CPU使用率、内存使用率等等。

## df
1. df -h > 查看磁盘使用情况
2. df -i > 查看inode占用硬盘内存情况
3. 备注：通过这两个命令可以查看内存使用情况，如果内存占用达到100%会导致很多命令都使用不了。有可能是系统产生大量的log，把占用空间的log查找出来并且清除后系统才可以正常运行。