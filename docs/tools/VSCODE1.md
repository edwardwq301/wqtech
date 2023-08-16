---
title: editor
time: 2022-4-5
author: WQ
tag: 
 - tools
categories: 
    - backend
---


### C++
众所周知，可以用devC++，Visio Studio，vscode等等写C++，但是vscode可能会让人折腾半天。下面给出一个拿来就用的办法
!!! 注意

    前提：需要安装好`vscode`，`mingw64`

1. 配置文件夹

2. 在`.vscode`文件夹下创建以下三个文件
      1. `c_cpp_properties.json`
      2. `launch.json`
      3. `tasks.json`
3. 复制粘贴以下

    ===  "c_cpp_properties.json"
       
        ```json
        {
        "configurations": [
            {
                "name": "Win32",
                "includePath": [
                    "${workspaceFolder}/**"
                ],
                "defines": [
                    "_DEBUG",
                    "UNICODE",
                    "_UNICODE"
                ],
                "compilerPath": "D:/C/vscode/gcc/mingw64/bin/g++.exe",//你的g++位置
                "cStandard": "gnu17",
                "cppStandard": "gnu++14",
                "intelliSenseMode": "windows-gcc-x64"
            }
            ],
        "version": 4
        }
        ```

    ===  "launch.json"  
        
        ```json
        {
        "version": "0.2.0",
        "configurations": [
            {
                "name": "(gdb) 内部终端启动",
                "type": "cppdbg",
                "request": "launch",
                "program": "${workspaceFolder}\\exe\\${fileBasenameNoExtension}.exe",//更多信息请查看gcc/g++的手册
                "args": [],
                "stopAtEntry": true,
                "cwd": "${fileDirname}",
                "environment": [],
                "externalConsole": false,
                
                "MIMode": "gdb",
                "miDebuggerPath": "D:\\C\\vscode\\gcc\\mingw64\\bin\\gdb.exe",
                //你的gdb位置
                "setupCommands": [
                    {
                        "description": "为 gdb 启用整齐打印",
                        "text": "-enable-pretty-printing",
                        
                        "ignoreFailures": true
                    },
                    {
                        "description": "将反汇编风格设置为 Intel",
                        "text": "-gdb-set disassembly-flavor intel",
                        "ignoreFailures": true
                    }
                ]
            },
            ]
        }
        ```

    ===  "task.json"

        ```json
        {
        "tasks": [
            
            {
                "type": "cppbuild",
                "label": "g++.exe ",
                "command": "D:/C/vscode/gcc/mingw64/bin/g++.exe",//对应你的g++位置
                "args": [
                    "-fdiagnostics-color=always",
                    "-g",
                    "${file}",
                    "-o",
                    "${workspaceFolder}\\exe\\${fileBasenameNoExtension}.exe"
                ],
                "options": {
                    "cwd": "D:/C/vscode/gcc/mingw64/bin"
                },
                "problemMatcher": [
                    "$gcc"
                ],
                "group": {
                    "kind": "build",
                    "isDefault": true
                },
                "detail": "调试器生成的任务。"
            }
            ],
            "version": "2.0.0"
        }
        ```


4. 在`sourcecode`文件夹写源代码即可

### C

今天好好的弄了一下`fork`。
- 在父进程中，fork的返回值是子进程的pid
- 在子进程中，fork的返回值是0
- 出错了，返回的是-1
```C
#include "student.h"

int main()
{
    int pid = fork();
    if (pid == 0)
    {
        printf("son:i am %d,my father is %d\n", getpid(), getppid());
        printf("  %d\n", pid);
    }
    else
    {
        printf("father:i am %d\n", getpid());
        printf("  %d\n", pid);
    }

    return 0;
}
```

### markdown preview enhanced
- 自定义字体（代码字体需要改官方代码）  [教程](https://zhuanlan.zhihu.com/p/458010679)
- 打印背景（让代码块背景为灰色）    [教程](https://blog.csdn.net/RP123123123/article/details/118113026)

### 更换vscode的代码颜色
1. 找到对应的文件夹
   默认主题 
   ` D:\C\vscode\Microsoft VS Code\resources\app\extensions\theme-defaults\themes`


2. 打开json文件，主要修改`"tokenColors"`这一项
   可以用github dark的对应修改


## vim

相当于cheetsheet

|操作|命令|
|:---|:---|
|复制|y|
|选中后复制到系统剪贴板|"+y|
|粘贴|p|
|撤销undo|u|
|重做redo|Ctrl-r|
|visio block后进入insert|Shift-i|


---

参考资料

[vim和系统剪贴板交互](https://www.zhihu.com/question/19863631/answer/89354508)
