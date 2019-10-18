---
title: "SublimeREPL: 定制与改造"
author: William
date: 2019-02-18
lastmod: 2019-02-18
categories: [Programming]
tags: [sublime,REPL,IDE,python,R,改造,编辑器]
description: 
draft: false
ToC: true
---

`Sublime` 是一款提供了可自定义的强大**编辑器**。我们可以根据个人的使用习惯，通过修改相应的参数配置，就可以搭建一个得心应手的编程环境。

<!--more-->

对于现代（c以后）的解释型编程语言，基本上都是支持 `REPL` 的，即 `read-evalue-print-loop`^[[wiki:read-evalue-print-loop](https://www.wikiwand.com/en/Read–eval–print_loop)，这些语言包括：APL、BASIC、Clojure、F#、Haskell、J、Julia、Perl、PHP、Prolog、Python、R、Ruby、Scala、Smalltalk、Standard ML、Swift、Tcl、Javascript、Java这样的编程语言所拥有的类似的编程环境。]，简单来说，就是可以在源文件与解释器之间进行实时的交互。这一点尤其是对于数据分析工作意义重大。一般而言，我们使用 `R` 或则 `python` 対数据进行探索性分析时，往往需要在程序与结果之间进行多次的交互分析，通过程序来查看结果，同时又通过结果来修改程序。很难想象如果没有 `REPL` 的支持，仅凭借编译语言，每次修改程序后都需要重新编译、执行，工作量是多么的巨大。

而做为一款现代的、优秀的编辑器，`Sublime Text` 当然夜提供了支持 `REPL` 的功能了。通过安装相关的插件，并设置一定的参数，我们便可以把 `sublime` 改造成为一个称心如意的数据分析套件了。

# 安装插件

可以通过调用 `shift+ctrl+p` 来安装需要的插件：

-   `SublimeREPl`：支持 `REPL` 交互
-   `SendCode`：发送代码到 `SublimeREPL`

安装完成后，我们可以在 `sublime` 的菜单中，打开 `Preferences -> Browse Packages` 查看已安装的插件，这也是后面改造插件的配置文件所在。 

# 改造

下面，我们一步步地完成対 `SublimeREPL` 进行改造。

## 执行终端显示代码

可以在执行的终端显示已发送的代码。打开 `Preference -> Package Setting`，然后找到 `SublimeREPL`，打开 `Settigns-User`：

```bash
{
	"show_transferred_text": true,
}
```

这样，我们便可以在终端看到已经执行了哪些代码。

## 修改快捷键

可以配置快捷键，用于打开特定的编程环境，如 `R`、`python`。同时，我们还可以通过 `ssh` 直接连接到远程服务器，实现在本地编辑器修改源文件、在远程服务器执行代码。

打开 `Preference -> Key Bindings`

### R 编程

-   local machine

    ```bash
    // 使用 F5 打开 本地R
      { "keys": ["f5"],
      "caption": "SublimeREPL: R",
      "command":"repl_open","args":
      {
      "type": "subprocess",
      "external_id": "r",
      "additional_scopes": ["tex.latex.knitr"],
      "encoding": {
      "windows": "$win_cmd_encoding",
      "linux": "utf8",
      "osx": "utf8"
      },
      "soft_quit": "\nquit(save=\"no\")\n",
      "cmd": {
            "linux": ["R", "--interactive", "--no-readline"],
            "osx":   ["R", "--interactive", "--no-readline"],
            "windows": ["Rterm.exe", "--ess", "--encoding=$win_cmd_encoding"]
            },
      "cwd": "$file_path",
      "extend_env": {"osx": {"PATH": "{PATH}:/usr/local/bin"},
      "linux": {"PATH": "{PATH}:/usr/local/bin"},
      "windows": {}},
      "cmd_postfix": "\n",
      "suppress_echo": {"osx": true,
      "linux": true,
      "windows": false},
      "syntax": "Packages/R/R Console.tmLanguage"
      }
      },
    ```

    

-   remote server

    ```bash
    // 使用 F6 打开 远程R
      { "keys": ["f6"],
      "caption": "SublimeREPL: Rssh135",
      "command":"repl_open","args":
      {
      "type": "subprocess",
      "external_id": "r",
      "additional_scopes": ["tex.latex.knitr"],
      "encoding": {
      "windows": "$win_cmd_encoding",
      "linux": "utf8",
      "osx": "utf8"
      },
      "soft_quit": "\nquit(save=\"no\")\n",
      "cmd": {
        "linux": ["ssh","fl@192.168.1.135","-p22","R","--interactive", "--no-readline"],
        "osx":   ["ssh","fl@gczhang.imwork.net", "-p58873","R","--interactive", "--no-readline"]
            },
      "cwd": "$file_path",
      "extend_env": {"osx": {"PATH": "{PATH}:/usr/local/bin"},
      "linux": {"PATH": "{PATH}:/usr/local/bin"},
      "windows": {}},
      "cmd_postfix": "\n",
      "suppress_echo": {"osx": true,
      "linux": true,
      "windows": false},
      "syntax": "Packages/R/R Console.tmLanguage"
      }
      },
    ```

    

### python 编程

-   `python2`

    ```bash
    // 使用 F2 打开 本地python2
      { "keys": ["f2"],
      "caption": "SublimeREPL: Python2",
      "command":"repl_open","args":
      {
      "type": "subprocess",
      "encoding": "utf8",
      //"cmd": ["python", "-i", "-u"],
      "cmd": ["/home/william/anaconda2/bin/python", "-i", "-u"],
      "cwd": "$file_path",
      "syntax": "Packages/Python/Python.tmLanguage",
      "external_id": "python",
      "extend_env": {"PYTHONIOENCODING": "utf-8"}
      }
      },
    ```

    

-   `python3`

    ```bash
    // 使用 F3 打开 本地python3
      { "keys": ["f3"],
      "caption": "SublimeREPL: Python3",
      "command":"repl_open","args":
      {
      "type": "subprocess",
      "encoding": "utf8",
      //"cmd": ["python", "-i", "-u"],
      "cmd": ["/home/william/anaconda3/bin/python", "-i", "-u"],
      "cwd": "$file_path",
      "syntax": "Packages/Python/Python.tmLanguage",
      "external_id": "python",
      "extend_env": {"PYTHONIOENCODING": "utf-8"}
      }
      },
    ```

- 远程 `python`

    ```bash
    // 使用 F4 打开 远程python2
      { "keys": ["f4"],
      "caption": "SublimeREPL: PySSH166",
      "command":"repl_open","args":
      {
      "type": "subprocess",
      "encoding": "utf8",
      "cmd": {"linux": ["ssh","fl@192.168.1.166","-p22","python2", "-i", "-u"]},
      "cwd": "$file_path",
      "syntax": "Packages/Python/Python.tmLanguage",
      "suppress_echo": false,
      "external_id": "python",
      "extend_env": {"PYTHONIOENCODING": "utf-8"},
      "cmd_postfix": "\n",
      "suppress_echo": {"osx": true,
      "linux": true,
      "windows": false}
      }
      },
    ```

### repl-restart

对于一个完整的分析项目，我们有时候想重启终端，以便查看修改后的程序是否依然能完整的运行。这个可以通过调用 `shift-ctrl-p` 来实现

![](/images/2019-02-18-SublimeREPL--定制与改造/repl-restart.gif)

当然，这个略显笨拙，尤其是对于一个需要多次重启的过程，十分的耗费时间与精力。做为一个「懒惰」的程序员，当然需要一键搞定全过程了。

-   我们可以找到 `~/.config/sublime-text-3/Packages/SublimeREPL`，查看 `repl:restart` 是由 `Context.sublime-menu` 控制

    ```bash
    vim ~/.config/sublime-text-3/Packages/SublimeREPL/Context.sublime-menu
    
    [
    	{"caption": "-"},
        {"command": "repl_kill", "caption": "Kill"},
    	{"command": "repl_restart", "caption": "Restart"},
    	{"command": "subprocess_repl_send_signal", "caption": "Send other SIGNAL"}
    ]
    ```

- 既然这样，我们就可以设置一个快捷键来映射这个命令

    ```bash
    // 使用 F12 实现 repl:restart 功能
      { "keys": ["f12"],
      "caption": "SublimeREPL: Restart",
      "command": "repl_restart", "caption": "Restart"
      },
    ```

- 同时，我们还观察到每次重启终端后，都会有窗口询问是否确定需要关闭。这个其实也是冗余的操作，同样可以去掉。找到 `~/.config/sublime-text-3/Packages/SublimeREPL/sublimerepl.py`，这是控制整个插件的核心功能模块。找到

    ```bash
    def restart(self, view, edit):
        repl_restart_args = view.settings().get("repl_restart_args")
        if not repl_restart_args:
            sublime.message_dialog("No restart parameters found")
            return False
        ## ---------------------------------------------------------------------
        rv = self.repl_view(view)
        if rv:
            if rv.repl and rv.repl.is_alive() and not sublime.ok_cancel_dialog("Still running. Really restart?"):
                return False
            rv.on_close()  # yes on_close, delete rv from
        ## ---------------------------------------------------------------------
    
        view.insert(edit, view.size(), RESTART_MSG)
        repl_restart_args["view_id"] = view.id()
        self.open(view.window(), **repl_restart_args)
        return True
    ```

    可以看到，其实询问的窗口是一个 `rv = self.repl_view(view)` 的对象。我们直接把这个注释掉，以后每次重启终端就不会再有提示了。

    ```bash
    def restart(self, view, edit):
        repl_restart_args = view.settings().get("repl_restart_args")
        if not repl_restart_args:
            sublime.message_dialog("No restart parameters found")
            return False
        ## ---------------------------------------------------------------------
        # rv = self.repl_view(view)
        # if rv:
        #     if rv.repl and rv.repl.is_alive() and not sublime.ok_cancel_dialog("Still running. Really restart?"):
        #         return False
        #     rv.on_close()  # yes on_close, delete rv from
        ## ---------------------------------------------------------------------

        view.insert(edit, view.size(), RESTART_MSG)
        repl_restart_args["view_id"] = view.id()
        self.open(view.window(), **repl_restart_args)
        return True
    ```

    再次运行 `F12`，确实是没有提示了，直接重启终端。

    ![](/images/2019-02-18-SublimeREPL--定制与改造/repl-f12.gif)

