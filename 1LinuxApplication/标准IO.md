<!--
 * @Description: In User Settings Edit
 * @Author: your name
 * @Date: 2019-09-15 22:58:56
 * @LastEditTime: 2019-10-21 23:08:28
 * @LastEditors: Please set LastEditors
 -->
# 标准IO
## 1 概念
1. 标准IO本上是一组C库函数的API接口；
2. 它只能操作普通文件；
3. 利用标准IO打开文件，操作系统会产生一个结构体指针，该指针包含着文件信息，被称之为指针流。
4. 在调用标准IO时，操作系统会在用户态来内核态中来回切换，无疑会增大系统开销，为避免这种情况，设立了缓冲机制。
   > 利用fflush函数可以刷新缓冲区内容。

## 2 标准IO对文件操作的相关函数
1. fopen-->打开文件
2. fclose-->关闭文件
3. fread-->按块读文件
4. fwrite-->按块写文件
5. fgetc-->按字节读
6. fputc-->按字节写
7. fgets-->按行读
8. fputs-->按行写
9. fprintf-->格式化到输入文件中（写字符串到文件中）
10. fscanf-->从文件中读出数据
11. rewind-->把文件指示器复位（文件指示器，指示在文件中的当前位置，类似于鼠标光标）
12. fseek-->文件定位
13. ftell-->指示在文件的当前位置
14. stdin/stdout/stderr-->标准输入/输出/错误
    > 每打开一个文件，该三个指针流即会出现  
    > stdin:获取键盘输出
    > stdout:类似于一个buff










