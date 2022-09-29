# linux配置环境变量

> 参考：[Linux创建连接命令 ln -s创建软连接](https://blog.csdn.net/jialibang/article/details/108247033?spm=1001.2101.3001.6661.1&utm_medium=distribute.pc_relevant_t0.none-task-blog-2%7Edefault%7ECTRLIST%7ERate-1-108247033-blog-122507328.pc_relevant_multi_platform_whitelistv3&depth_1-utm_source=distribute.pc_relevant_t0.none-task-blog-2%7Edefault%7ECTRLIST%7ERate-1-108247033-blog-122507328.pc_relevant_multi_platform_whitelistv3&utm_relevant_index=1)

## 读取环境变量

**export 命令** 读取当前系统定义的所有环境变

```bash
当在不同目录使用相同文件时，可以使用ln命令链接，避免了重复占用磁盘空间。
例如：ln -s /bin/less /usr/local/bin/less
 
需要注意：第一，ln命令会保持每一处链接文件的同步性，也就是说，不论你改动了哪一处，其它的文件都会发生相同的变化；
第二，ln的链接分软链接和硬链接
软链接： ln -s ** **,它只会在你选定的位置上生成一个文件的镜像，不会占用磁盘空间
硬链接： ln ** **,没有参数-s, 它会在你选定的位置上生成一个和源文件大小相同的文件
 
无论是软链接还是硬链接，文件都保持同步变化
```

【硬连接】

硬连接的作用是允许一个文件拥有多个有效路径名，这样用户就可以建立硬连接到重要文件，以防止“误删”的功能。

【[软连接](https://so.csdn.net/so/search?q=软连接&spm=1001.2101.3001.7020)】

软链接文件有类似于Windows的快捷方式。包含的有另一文件的位置信息。

## 1. 创建软链接

具体用法是：ln -s  [源文件]  [软链接文件]

```bash
pwd  #查看当前路径
ll  #查看所有文件
 
#首先我们先来创建两个文件
mkdir test_chk
touch test_chk/test.txt
touch test_chk/val.txt    
vim test_chk/test.txt #sudo gedit test_chk/test.txt(这一步随便在这个test.txt里写点东东即可)
 
#下面我们来创建test_chk目录 的软链接
ln -s test_chk test_chk_ln
ll #ls -l查看
 
#修改软链接
#ln –snf [新的源文件或目录] [软链接文件]
 
#删除软链接
#rm –rf   ./软链接名称
#rm -rf ./软链接名称/ (这样就会把软链接以及软链接指向下的内容删除)
 
#正确的删除方式（删除软链接，但不删除实际数据）
rm -rf  ./test_chk_ln
#为了防止误删，可以询问 rm -ri  ./test_chk_ln  或者改用mv 命令操作
 
#错误的删除方式
rm -rf ./test_chk_ln/ (这样就会把原来test_chk下的内容删除)
```

##  2.“rm -rf /” 与 “rm -rf /\*”的强大威力,瘫痪系统,推荐使用mv代替rm

​    -f  强制删除文件或目录  -i 删除已有文件或目录之前先询问用户  -r 递归处理， 将指定目录下的所有文件与子目录一并处理

​    /  在Linux中表示根目录  * 所有文件   /* 根目录下的所有文件

   ~/ 当前登录用户的用户目录      ./  表示当前目录    pwd  查看当前所在路径

## 3.Linux建立软硬链接文件(ln命令)

> 参考：[Linux建立软硬链接文件](https://zhuanlan.zhihu.com/p/511763885)

如果要想说清楚 ln 命令，则必须先解释下 ext 文件系统（Linux 文件系统）是如何工作的。我们在前面讲解了分区的格式化就是写入文件系统，而我们的 Linux 目前使用的是 ext4 文件系统。如果用一张示意图来描述 ext4 文件系统，则可以参考图 1。



![img](https://pic2.zhimg.com/80/v2-47b166cec5f4f72ae6603ba3f9ea0e99_720w.webp)

图 1 ext4 文件系统示意图

ext4 文件系统会把分区主要分为两大部分（暂时不提超级块）：小部分用于保存文件的 inode (i 节点）信息；剩余的大部分用于保存 block 信息。

inode 的默认大小为 128 Byte，用来记录文件的权限（r、w、x）、文件的所有者和属组、文件的大小、文件的状态改变时间（ctime）、文件的最近一次读取时间（atime）、文件的最近一次修改时间（mtime）、文件的数据真正保存的 block 编号。每个文件需要占用一个 inode。大家如果仔细查看，就会发现 inode 中是不记录文件名的，那是因为文件名记录在文件所在目录的 block 中。

block 的大小可以是 1KB、2KB、4KB，默认为 4KB。block 用于实际的数据存储，如果一个 block 放不下数据，则可以占用多个 block。例如，有一个 10KB 的文件需要存储，则会占用 3 个 block，虽然最后一个 block 不能占满，但也不能再放入其他文件的数据。这 3 个 block 有可能是连续的，也有可能是分散的。

由此，我们可以知道以下 2 个重要的信息：

1. 每个文件都独自占用一个 inode，文件内容由 inode 的记录来指向；
2. 如果想要读取文件内容，就必须借助目录中记录的文件名找到该文件的 inode，才能成功找到文件内容所在的 block 块；

了解了 Linux 系统底层文件的存储状态后，接下来学习 ln 命令。

ln 命令用于给文件创建链接，根据 Linux 系统存储文件的特点，链接的方式分为以下 2 种：

- 软链接：类似于 Windows 系统中给文件创建快捷方式，即产生一个特殊的文件，该文件用来指向另一个文件，此链接方式同样适用于目录。
- 硬链接：我们知道，文件的基本信息都存储在 inode 中，而硬链接指的就是给一个文件的 inode 分配多个文件名，通过任何一个文件名，都可以找到此文件的 inode，从而读取该文件的数据信息。

ln 命令的基本格式如下：

```text
[root@localhost ~]# ln [选项] 源文件 目标文件
```

选项：

- -s：建立软链接文件。如果不加 "-s" 选项，则建立硬链接文件；
- -f：强制。如果目标文件已经存在，则删除目标文件后再建立链接文件；

【例 1】创建硬链接：

```text
[root@localhost ~]# touch cangls
[root@localhost ~]# ln /root/cangls /tmp
\#建立硬链接文件，目标文件没有写文件名，会和原名一致
\#也就是/tmp/cangls 是硬链接文件
```

【例 2】创建软链接：

```text
[root@localhost ~]# touch bols
[root@localhost ~]# In -s /root/bols /tmp
\#建立软链接文件
```

这里需要注意的是，软链接文件的源文件必须写成绝对路径，而不能写成相对路径（硬链接没有这样的要求）；否则软链接文件会报错。这是初学者非常容易犯的错误。