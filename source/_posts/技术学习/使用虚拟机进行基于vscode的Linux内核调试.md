---
title: 使用虚拟机进行基于qemu和gdb的Linux内核调试
tags:
  - Linux
  - C/C++
category:
  - 技术学习
lang: zh-CN
copy_from: >-
  https://wfloveiu.github.io/2023/11/20/%E9%85%8D%E7%BD%AELinux%E5%86%85%E6%A0%B8%E7%BC%96%E8%AF%911/
abbrlink: 6c03d805
date: 2023-11-18 00:00:00
---

文章介绍了在虚拟机中配置和编译Linux内核的步骤。涵盖了虚拟机配置要求、开启SSH服务、内核编译的准备工作、解决编译中的报错、安装Qemu以及编译busybox等步骤。最后，通过构建initramfs根文件系统和使用Qemu启动内核进行调试，详细说明了连接和调试的步骤。文章还提供了丰富的命令行示例和截图，为读者提供了一份详尽的指南。

<!--more-->

## 虚拟机配置

至少分8个核心（不然编译速度很慢，亲测）

磁盘大小分50G（编译后的内核大小有20多个G！）

## 打开SSH

虚拟机中安装的是ubuntu22.04版本，默认没有安装和启用SSH服务

**更新软件源**

```sh
sudo apt update && sudo apt upgrade -y
```

**安装SSH(OpenSSH)**

```sh
sudo apt install openssh-server -y
```

**启动SSH服务**

```sh
sudo systemctl enable --now ssh
```

**检查是否启动成功**

```sh
sudo systemctl status ssh
```

## 内核编译

### 准备工作

```sh
# 安装相关依赖
sudo apt-get install libncurses5-dev libssl-dev bison flex libelf-dev gcc g++ make openssl libc6-dev

# 安装gdb，这里使用apt安装（多次尝试的结果）
sudo apt-get install gdb
gdb --version # gdb版本为12.1

#这里选择清华源，国内速度会快很多
wget https://mirrors.tuna.tsinghua.edu.cn/kernel/v5.x/linux-5.14.tar.gz
# 解压
tar -xvf linux-5.14.tar.gz

#配置编译选项
make menuconfig
```

然后会在此文件夹下生成 **./config**文件

**进入该文件，并做以下2处修改**

```sh
vim ./.config
```

![image-20231117212013850](https://raw.githubusercontent.com/wfloveiu/blogImage/main/img/202311201128412.png)

**安装dwarves软件包（编译报错得出结论）**

```sh
sudo apt-get install dwarves
```

**BTF报错解决**

如果仅仅只进行了上边的配置，会报如下错误（我个人是这样）

```sh
  KSYMS   .tmp_vmlinux.kallsyms1.S
  AS      .tmp_vmlinux.kallsyms1.S
  LD      .tmp_vmlinux.kallsyms2
  KSYMS   .tmp_vmlinux.kallsyms2.S
  AS      .tmp_vmlinux.kallsyms2.S
  LD      vmlinux
  BTFIDS  vmlinux
FAILED: load BTF from vmlinux: Invalid argument
make: *** [Makefile:1187: vmlinux] Error 255
make: *** Deleting file 'vmlinux'
```

查阅资料后，有三种解决方案：https://devkernel.io/posts/pahole-error/

我使用的是**第二种方法**，对pahole软件包进行降级 :

查看pahole的版本，是1.25

```sh
pahole --version  
```

查看pahole的所有可用安装版本

```sh
sudo apt-cache policy pahole
```

截图如下

![1](https://raw.githubusercontent.com/wfloveiu/blogImage/main/img/202311201127209.png)

我们发现只有两个版本，因此只能降级为 1.22-8

```sh
sudo apt install pahole=1.22-8
```

### 编译

```sh
make -j8      #8个线程并行编译，
```

然后可能会弹出一个选择（1，2，3），直接选择1即可。等待一段时间，30分钟左右

### 是否成功

编译完成后，目录下会生成以下,那么就编译成功了

> ./vmLinux
>
> ./arch/x86/boot/bzImage
>
> 其中vmLinux为GDB所需的调试Map文件，bzImage为大内核文件

## 安装Qemu

qemu是一款完全软件模拟(Binary translation)的虚拟化软件，在虚拟化的实现中性能相对较差。但利用它在测试环境中gdb调试Linux内核代码，是熟悉Linux内核代码的一个好方法。

```sh
#安装qemu
sudo apt-get install qemu
```

## 安装编译busybox

安装busybox的目的是：借助BusyBox构建极简initramfs，提供基本的用户态可执行程序。

```sh
wget https://busybox.net/downloads/busybox-1.36.1.tar.bz2  # 去官网找最新版
tar -xvf busybox-1.36.1.tar.bz2
cd busybox-1.36.1/
make menuconfig
```

在编译busybox之前，我们需要对其进行设置，执行`make menuconfig`，如下

![image-20231120113536905](https://raw.githubusercontent.com/wfloveiu/blogImage/main/img/202311201135953.png)

![image-20231120113608696](https://raw.githubusercontent.com/wfloveiu/blogImage/main/img/202311201136740.png)

这里一定要选择**静态编译**，编译好的可执行文件`busybox`不依赖动态链接库，可以独立运行，方便构建initramfs。

```sh
make -j 8
make && make install # 安装完成后生成的相关文件会在 _install 目录下
```

## 构建initramfs根文件系统

```sh
# 在busybox压缩包的下载目录下,创建的该文件夹，该文件夹下有
# wufang@wufang:~/linux_kernel/kernel_compile$ ls
# busybox-1.36.1   busybox-1.36.1.tar.bz2   linux-5.14  linux-5.14.tar.gz

mkdir initramfs
cd initramfs
cp ../busybox-1.29.0/_install/* -rf ./ #将_install文件夹下的所有文件复制到initramfs文件夹下
mkdir dev proc sys
sudo cp -a /dev/{null,console,tty,tty1,tty2,tty3,tty4} dev/
rm -f linuxrc
vim init
```

添加如下代码

```sh
#!/bin/busybox sh
echo "{==DBG==} INIT SCRIPT"
mount -t proc none /proc
mount -t sysfs none /sys

echo -e "{==DBG==} Boot took $(cut -d' ' -f1 /proc/uptime) seconds"
exec /sbin/init
```

```sh
chmod a+x init 修改文件权限
# 完成后，initrams下有如下文件
# bin   dev   init  proc  sbin  sys   usr
```

**打包initramfs**

```sh
find . -print0 | cpio --null -ov --format=newc | gzip -9 > ../initramfs.cpio.gz
# 此时在busybox压缩包的下载目录下，有如下文件
# busybox-1.36.1          initramfs               linux-5.14
# busybox-1.36.1.tar.bz2  initramfs.cpio.gz       linux-5.14.tar.gz
```

## 启动Qemu调试内核

上述完成之后，就可以启动Qemu来调试内核了,启动代码如下（是一个指令）

```sh
qemu-system-x86_64 -kernel ./arch/x86/boot/bzImage -initrd ../initramfs.cpio.gz -append "nokaslr console=ttyS0" -s -S -nographic
```

- `qemu-system-x86_64`：指定是x86，64位;
- `-kernel ./arch/x86/boot/bzImage`：指定启用的内核镜像；
- `-initrd ../initramfs.cpio.gz`：指定启动的内存文件系统
- `-append "nokaslr console=ttyS0"` ：附加参数，其中 `nokaslr` 参数**必须添加进来**，防止内核起始地址随机化，这样会导致 gdb 断点不能命中；
- `-s` ：监听在 gdb 1234 端口；
- `-S` ：表示启动后就挂起，等待 gdb 连接；
- `-nographic`：不启动图形界面

开启另一个命令行窗口，输入**gdb**，即可开启调试

```sh
(gdb) target remote localhost:1234  #连接qemu监听的端口
(gdb) break start_kernel      #在start_kernel打断点
(gdb) break  rest_init        #在res_init打断点
(gdb) c                       #运行到断点处
```

