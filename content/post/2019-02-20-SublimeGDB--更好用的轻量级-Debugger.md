---
title: "SublimeGDB: 更好用的轻量级 Debugger"
author: William
date: 2019-02-20
lastmod: 2019-02-20
categories: [Programming]
tags: [Sublime,GNU,GDB,Debugger,gcc,c,c++,调试器]
description:
draft: false
ToC: true
---

`SublimeGDB` 是一款可以在 `Sublime` 编辑器内运行的代码调试器，即调用系统的 `GNU Debugger` 套件。通过一定的设置，我们便可以把 `Sublime` 改造成一款轻量级的 `IDE`，从而可以直接在编辑器对源文件^[source code]进行调试，并且提供了了多个代码调试状态，比直接在终端进行调试要强大的多，也十分方便。

<!--more-->

# 安装

直接使用 `shif+ctrl+p` 调用 `install packages` 来查找 `SublimeGDB`。安装完成后，我们还需要根据个人的使用习惯，进行定制改造。

# 配置

## 布局

`Preferences -> Package Settings -> SublimeGDB -> Settings-User`

```bash
{
    "workingdir":"${folder:${file}}",
    "commandline":"gcc -ggdb3 -std=c11 ${file} -o ${file_base_name} && gdb --interpreter=mi --args ./${file_base_name}",
    // 可以直接调试 cmake 下的 bin/run 可执行文件
    //"commandline":"gdb --interpreter=mi --args ../bin/run",
    "env": {"DISPLAY": ":100"},
    "close_views": true,

    // Define debugging window layout (window split)
    // first define column/row separators, then refer to them to define cells
    "layout":
    {
        "cols": [0.0, 0.5, 1.0],
        "rows": [0.0, 0.65, 1.0],
        "cells":
        [ // c1 r1 c2 r2
            [0, 0, 1, 1], // -> (0.0, 0.0), (0.5, 0.65)
            [1, 0, 2, 1], // -> (0.5, 0.0), (0.65, 1.0)
            [0, 1, 1, 2], // -> (0.0, 0.65), (1.0, 0.5)
            [1, 1, 2, 2]  // -> (0.5, 0.65), (1.0, 1.0)
        ]
        // 布局结构
        //
        //         | c0:0          | c1:0.5        | c2:1.0
        // r0:0.0  | ------------- | ------------- |
        // ------- | c0:r0          c1:r0
        // ------- |     =0#             =1#
        // ------- |          c1:r1            c2:r1
        // r1:0.65 | ------------- | ------------- |
        // ------  | c0:r1          c1:r1
        // ------  |     =2#             =3#
        // ------- |          c1:r2            c2:r2
        // r2:1.0  | ------------- | ------------- |
    },

    // The group used for opening files
    "file_group": 0,

    "console_group": 1,
    "console_open": true,

    "session_group": 2,
    "session_open": true,

    "variables_group": 2,
    "variables_open": true,

    "callstack_group": 3,
    "callstack_open": true,

    "registers_group": 3,
    "registers_open": false,

    "disassembly_group": 3,
    "disassembly_open": true,
    // Set to "intel" for intel disassembly flavor. All other
    // values default to using "att" flavor.
    "disassembly_flavor": "intel",

    "threads_group": 3,
    "threads_open": true,

    "breakpoints_group": 3,
    "breakpoints_open": true,

}
```

说明：

-   `"workingdir":"${folder:${file}}"`：设置为在当前源文件进行调试
-   `"commandline":"gcc -ggdb3 -std=c11 ${file} -o ${file_base_name} && gdb --interpreter=mi --args ./${file_base_name}"`：需要使用的调试器参数，比例我这里使用 `gcc` 进行编译，并以 `-std=c11` 的标准。需要注意的是，由于我们编译完源文件后进行调试，因此需要添加参数 `-g` 表示生成可调式执行文件。
-   `"env": {"DISPLAY": ":100"}`：全屏显示
-   `"close_views": true`：当退出调试模式后，把相关的窗口都关闭掉。
-   `"layout"`：用来控制页面格式，可以根据使用习惯进行定制。使用 `cells` 来标记窗口号，然后将不同的窗口放置在标记的布局里。

## 快捷键

`Preferences -> Package Settings -> SublimeGDB -> Key Bindings-User`

```bash
// SublimeGDB ==============================================================
{
    "keys": ["ctrl+p"],
    "command": "gdb_toggle_breakpoint"
},
{
    "keys": ["ctrl+g"],
    "command": "gdb_launch",
    "context": [{"key": "gdb_running", "operator": "equal", "operand": false}]   
},
{
    "keys": ["ctrl+g"],
    "command": "gdb_exit",
    "context": [{"key": "gdb_running", "operator": "equal", "operand": true}]
},
{
    "keys": ["ctrl+enter"],
    "command": "gdb_continue",
    "context": [{"key": "gdb_running", "operator": "equal", "operand": true}]
},
{
    "command": "gdb_step_over",
    "context":
    [
        {"key": "gdb_running", "operator": "equal", "operand": true},
        {"key": "gdb_disassembly_view", "operand": false}
    ],
    "keys": ["ctrl+n"]
},
{
    "command": "gdb_next_instruction",
    "context":
    [
        {"key": "gdb_running", "operator": "equal", "operand": true},
        {"key": "gdb_disassembly_view", "operand": true}
    ],
    "keys": ["ctrl+n"]
},
// SublimeGDB ==============================================================
```

快捷键使用指南：

-   `ctrl+n`：触发断点
-   `ctrl+g`：载入 `GDB` 调试模式，其中 `context` 表示在何种情况下运行这个命令。

# 使用

![](/images/2019-02-20-SublimeGDB--更好用的轻量级-Debugger/hello.png)

