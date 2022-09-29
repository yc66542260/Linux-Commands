# linux配置环境变量

> 参考：[linux配置环境变量](https://blog.csdn.net/huanby/article/details/123103191)

在配置 Linux 系统服务器的时候，我们常常需要设置系统[环境变量](https://so.csdn.net/so/search?q=环境变量&spm=1001.2101.3001.7020)，这篇文章就是总结几种常见的配置环境变量的方式。

## 读取环境变量

**export 命令** 读取当前系统定义的所有环境变

```bash
[root@localhost ~] export
declare -x DISPLAY="localhost:10.0"
declare -x HISTCONTROL="ignoredups"
declare -x HISTSIZE="1000"
declare -x HOME="/root"
declare -x HOSTNAME="localhost.localdomain"
declare -x LANG="zh_CN.UTF-8"
declare -x LD_LIBRARY_PATH="/usr/lib/oracle/18.5/client64/lib"
declare -x LESSOPEN="||/usr/bin/lesspipe.sh %s"
declare -x LOGNAME="root"
```

**echo $PATH 命令**  输出当前的 PATH 环境变量的值

```bash
echo $PATH
/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/root/bin
```

注意：PATH变量定义了指定路径，以冒号 : 分割不同的多个路径

## 配置环境变量

### **方式一：export PATH**

**export 命令**用于显示或设置环境变量，语法：**export [变量名称]=[变量设置值]。**

```bash
export ADDPATH=/root/bin
echo $ADDPATH
/root/bin
```

需要注意如果添加的环境变量已经存在，需要在设置环境变量值时加上原值：**$变量名称 + ":" + 变量值**，环境变量用冒号 **:** 分隔不同的路径

```bash
export PATH=/root/bin:$PATH
echo $PATH
/root/bin:/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/root/bin
```

**echo是一个非常简单、直接的linux命令：   \*将argument送出至标准输出（STDOUT），通常就是在显示器（monitor）上输出。**

- 生效时间：立即生效
- 生效期限：仅当前终端有效，关闭终端后失效
- 生效范围：仅对当前用户有效

### **方式二：vim ~/.bash_profile**

设置当前登录用户环境变量，在最后一行添加 **export [变量名称]=[变量设置值]。**

```bash
vim ~/.bash_profile
# 在最后一行添加
export CUSTOM_PATH="$CUSTOM_PATH:~/.bash_profile"
```

- 生效时间：当前用户打开新终端生效，或者执行 source ~/.bash_profile 生效
- 生效期限：永久有效
- 生效范围：仅对当前用户有效

### **方式三：vim ~/.bashrc**

设置当前登录用户环境变量

```bash
vim ~/.bashrc
 
# 在最后一行添加
export CUSTOM_PATH="$CUSTOM_PATH:~/.bashrc"
```

- 生效时间：打开新终端生效，或者执行 source /etc/profile 生效
- 生效期限：永久有效
- 生效范围：对所有用户有效

### **方式五：vim /etc/environment**

系统变量，需要管理员权限或者对配置文件的写入权限

```bash
vim /etc/environment
 
# 在最后一行添加
export CUSTOM_PATH="$CUSTOM_PATH:/etc/environment"
```

### 方式六：vim /etc/profile.d/*.sh

在 /etc/profile.d 文件夹新建 *.sh 文件配置环境变量，所有的环境变量都在 /etc/profile 中配置会导致该文件中的配置过多，不利于之后的修改操作，使用这个方式可以对变量按照功能配置，不需要该变量的时候只需要删除指定 *.sh 文件就可以了，并且它与 /etc/profile 是同级的变量，效果一致。

新建 sh 文件需要管理员权限或者对配置文件的写入权限

```bash
# 添加 /etc/profile/test.sh 文件
vim /etc/profile.d/test.sh
 
# 在最后一行添加
export CUSTOM_PATH="$CUSTOM_PATH:/etc/profile.d/test.sh"
```

- 生效时间：打开新终端生效，或者执行 source /etc/profile.d/*.sh 生效
- 生效期限：永久有效
- 生效范围：对所有用户有效

### **方式七：vim /etc/bashrc**

系统变量，需要管理员权限或者对配置文件的写入权限

```bash
vim /etc/bashrc
 
# 在最后一行添加
export CUSTOM_PATH="$CUSTOM_PATH:/etc/bashrc"
```

- 生效时间：打开新终端生效，或者执行 source /etc/bashrc 生效

- 生效期限：永久有效

- 生效范围：对所有用户有效

### 总结：环境变量的分类

**Linux 环境变量可以分为用户环境变量与系统环境变量**

**用户环境变量**：~/.bashrc、~/.bash_profile

**系统环境变量**：/etc/profile、/etc/environment、/etc/profile.d/*.sh、/etc/bashrc

### 注意事项

配置的环境变量中要加上原来的配置，即 $PATH 部分，避免覆盖之前配置。

使用修改文件配置的方式对于环境变量的修改是永久有效的，只有 export 命令行方式配置的环境变量只在当前终端有效。
