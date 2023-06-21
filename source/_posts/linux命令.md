---
title: linux命令
date: 2022-10-14 16:06:11
tags: 
- Linux
categories:
- Linux
cover: https://bu.dusays.com/2022/11/16/637452ba0ed0b.png
---

#### 1、cd

```bash
cd /home 　  进入根目录下面的home目录
cd home 　  进入当前目录下的home目录
cd ..　　　  返回上一层目录
cd ../.. 　　 返回上两级目录
cd /　　　　　返回跟目录
cd - 　　　　返回上次所在的目录 
```

#### 2、ls命令

```bash
ls 　　　查看目录中的文件 
ls -a　　列出全部的文件，连同隐藏文件（开头为.的文件）一起列出来
ls -l 　 显示文件和目录的详细资料 
```

#### 3、mkdir

```bash
mkdir dir1 创建一个叫做 'dir1' 的文件夹' 
mkdir dir1 dir2 同时创建两个文件夹 
```

#### 4、rm命令

```bash
rm -f file1　　 删除一个叫做 'file1' 的文件' 
rmdir dir1 　　删除一个叫做 'dir1' 的目录' （空目录才能删除）
rm -rf dir1 　　删除一个叫做 'dir1' 的目录并同时删除其内容 
rm -rf dir1 dir2　　 同时删除两个目录及它们的内容 
```

#### 5、mv

```bash
mv file1 file2   把文件file1重命名为file2  

mv file1 file2 dir   把文件file1、file2移动到目录dir中
```

#### 6、cp

```bash
cp  -a file1 file2  连同文件的所有特性把文件file1复制成文件file2
cp dir/* .  复制一个目录下的所有文件到当前工作目录 
cp -a /tmp/dir1 .  复制一个目录到当前工作目录 
cp -a dir1 dir2  复制一个目录 
```

#### 7、find

```bash
find / -name file1 从 '/' 开始进入根文件系统搜索文件和目录 
find / -user user1 搜索属于用户 'user1' 的文件和目录 
find /home/user1 -name \*.bin 在目录 '/ home/user1' 中搜索带有'.bin' 结尾的文件 
find /usr/bin -type f -atime +100 搜索在过去100天内未被使用过的执行文件 
find /usr/bin -type f -mtime -10 搜索在10天内被创建或者修改过的文件 
```

#### 8、ps

```bash
-A ：所有的进程均显示出来
-a ：不与terminal有关的所有进程
-u ：有效用户的相关进程
-x ：一般与a参数一起使用，可列出较完整的信息
-l ：较长，较详细地将PID的信息列出
其实我们只要记住ps一般使用的命令参数搭配即可，它们并不多，如下：

ps aux   查看系统所有的进程数据
ps ax   查看不与terminal有关的所有进程
ps -lA   查看系统所有的进程数据
ps axjf   查看连同一部分进程树状态
```

#### 9、kill

```bash
kill -signal PID
signal的常用参数如下：注：最前面的数字为信号的代号，使用时可以用代号代替相应的信号。

1：SIGHUP，启动被终止的进程
2：SIGINT，相当于输入ctrl+c，中断一个程序的进行
9：SIGKILL，强制中断一个进程的进行
15：SIGTERM，以正常的结束进程方式来终止进程
17：SIGSTOP，相当于输入ctrl+z，暂停一个进程的进行
```

#### 10、tar

```bash
压缩：tar -jcv -f filename.tar.bz2 要被处理的文件或目录名称  
查询：tar -jtv -f filename.tar.bz2  
解压：tar -jxv -f filename.tar.bz2 -C 欲解压缩的目录  
tar -zxvf 包名 // 解压tar文件
unzip 包名 // 解压zip文件
unzip -o 包名 // 解压文件全部替换不提示
```

#### 11、chmod

```bash
chmod -R 777      chmod -R 777 意思就是将当前目录及目录下所有文件都给予777权限（所有权限）
```

