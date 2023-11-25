---
title: 使用Vscode调试Linux内核
tag:
  - Linux
  - C/C++
category:
  - 技术学习
lang: zh-CN
copy_from: >-
  https://wfloveiu.github.io/2023/11/20/Vscode%E8%B0%83%E8%AF%95Linux%E5%86%85%E6%A0%B8/
abbrlink: c45db453
date: 2023-11-19 00:00:00
---

文章介绍了在Vscode中配置和使用远程SSH插件进行Linux内核的调试，以替代之前使用Qemu和gdb的方式。通过安装远程SSH插件和C/C++插件，配置调试环境，并展示了在Vscode中设置断点、进行调试的步骤。文章还解决了在代码中出现红色标记的问题，提供了生成`compile_commands.json`文件的命令和在Vscode中配置的步骤，使读者能够顺利进行Vscode调试。

<!--more-->

上一篇博客我们在虚拟机中编译了Linux内核，并且使用Qemu和gdb进行调试，但是gdb的指令我还不熟练，还是想用vscode来调试，这样也更加方便

## Vscode插件安装

### remote-ssh

![image-20231120124509960](https://raw.githubusercontent.com/wfloveiu/blogImage/main/img/202311201245007.png)

安装完成后右边工具栏会多出一个功能

![image-20231120124653194](https://raw.githubusercontent.com/wfloveiu/blogImage/main/img/202311201246229.png)

按F1呼出对话框，输入`remote-ssh`，选择open ssh configuration file。

![image-20231120124748850](https://raw.githubusercontent.com/wfloveiu/blogImage/main/img/202311201247886.png)

选择第一个配置文件

![image-20231120124824202](https://raw.githubusercontent.com/wfloveiu/blogImage/main/img/202311201248231.png)

```
Host 自定义连接名称
    HostName 服务器IP地址
    User 用户名
```

### C/C++

安装C/C++插件

![image-20231120125110014](https://raw.githubusercontent.com/wfloveiu/blogImage/main/img/202311201251048.png)

依次点击【运行】->【打开配置】，将以下配置复制到launch.json中。

**该代码不需要更改，直接粘贴**

```json
{
    // 使用 IntelliSense 了解相关属性。 
    // 悬停以查看现有属性的描述。
    // 欲了解更多信息，请访问: https://go.microsoft.com/fwlink/?linkid=830387
    "version": "0.2.0",
    "configurations": [
        {
            "name": "kernel-debug",   //随便起名
            "type": "cppdbg",
            "request": "launch",
            "miDebuggerServerAddress": "127.0.0.1:1234",  //远端调试地址，1234为qemu的监视端口
            "program": "${workspaceFolder}/vmlinux",     //当前目录下的vmlinux
            "args": [],
            "stopAtEntry": false,
            "cwd": "${workspaceFolder}",
            "environment": [],
            "externalConsole": false,
            "logging": {
                "engineLogging": false
            },
            "MIMode": "gdb",
        }
    ]
}
```

## Vscode调试

### 在虚拟机中启动qemu

在**Linux内核文件夹下**运行此命令

```sh
qemu-system-x86_64 -kernel ./arch/x86/boot/bzImage -initrd ../initramfs.cpio.gz -append "nokaslr console=ttyS0" -s -S -nographic
```

### 打断点

打开init/main.c，我打了如下的断点

![image-20231120130037370](https://raw.githubusercontent.com/wfloveiu/blogImage/main/img/202311201300424.png)

### 调试

![image-20231120130522009](https://raw.githubusercontent.com/wfloveiu/blogImage/main/img/202311201305050.png)

然后在vscode中就可以看到调试结果了

## 代码中标红的问题

代码标红是缺少compile_commands.json文件

我在B站上学习的时候，是跟着这位up主来的，我的解决方案如下：

在终端键入命令

```sh
./scripts/clang-tools/gen_compile_commands.py
```

在源码目录下就生成了`compile_commands.json`文件

在vscode中：`ctrl+shipt+p`选择C/C++：Edit Coonfigurations,

在c_cpp_properties.json

```json
{
    "configurations": [
        {
            "name": "Linux",
            "includePath": [
                "${workspaceFolder}/**"
            ],
            "defines": [],
            "compilerPath": "/usr/bin/gcc",
            "cStandard": "c17",
            "cppStandard": "gnu++17",
            "intelliSenseMode": "linux-gcc-x64",
            "compileCommands": "${workspaceFolder}/compile_commands.json"
        }
    ],
    "version": 4
}
```

此后，main.c中的代码就不标红了

