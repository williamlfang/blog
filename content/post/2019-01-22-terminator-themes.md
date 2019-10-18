---
title: "terminator themes"
author: William
date: 2019-01-22
lastmod: 2019-01-22
categories: [Programming]
tags: [terminator,theme,zenburn]
description: 
draft: false
ToC: true
---

配置 `terminator` 主题颜色，以插件的形式提供了灵活的配置方式。[The biggest collection of Terminator themes](https://github.com/EliverLara/terminator-themes)。

<!--more-->

# 获取配置

这个一定要在系统自带的 `terminal` 里安装，否则会无法载入。[Can't find the plugin in the preferences &gt; Plugins](https://github.com/EliverLara/terminator-themes/issues/14)

> Had the same problem, make sure to close any open instance of terminator b4 trying, solved it for me
>
> 需要关闭所有的 terminator, 然后在系统自带的 terminal 下面安装。

```bash
sudo apt install python-requests
mkdir -p $HOME/.config/terminator/plugins

## For terminator >= 1.9
wget https://git.io/v5Zww -O $HOME"/.config/terminator/plugins/terminator-themes.py"

## For terminator < 1.9
wget https://git.io/v5Zwz -O $HOME"/.config/terminator/plugins/terminator-themes.py"
```

# 安装插件

现在，打开 `terminator`，鼠标右键点击选择 `Preference -> Plugins`，然后选中 `TerminatorThemes` 按钮。

![激活](/images/2019-01-22-terminator-themes/preference.gif)

# 安装主题

内置了非常丰富的主题配色，可以在 `terminator` 操作界面，使用鼠标右键选择 `themes` 来安装和选择喜欢的配色。

![主题](/images/2019-01-22-terminator-themes/themes.gif)

# 设置默认
可以在启动 `terminator` 的时候，启用默认的配色方案。

打开 `~/.config/terminator/config`，把 `[plugins] -> [profiles] -> [[default]]` 的设置替换为默认启动的方案即可。

```bash
## 这个是原来的一个默认配置
[plugins]
[profiles]
  [[default]]
    background_color = "#002b36"
    background_darkness = 0.92
    background_type = transparent
    copy_on_selection = True
    cursor_color = "#aaaaaa"
    foreground_color = "#839496"
    palette = "#073642:#dc322f:#859900:#b58900:#268bd2:#d33682:#2aa198:#eee8d5:#002b36:#cb4b16:#586e75:#657b83:#839496:#6c71c4:#93a1a1:#fdf6e3"
    show_titlebar = False
    use_theme_colors = True
```

比如，我用 `Zenburn` 配置

```bash
[plugins]
[profiles]
  [[default]]
    background_color = "#3f3f3f"
    background_darkness = 0.92
    background_type = transparent
    copy_on_selection = True
    cursor_color = "#dcdcdc"
    foreground_color = "#dcdcdc"
    palette = "#3f3f3f:#cc9393:#7f9f7f:#e3ceab:#dfaf8f:#cc9393:#8cd0d3:#dcdccc:#3f3f3f:#cc9393:#7f9f7f:#e3ceab:#dfaf8f:#cc9393:#8cd0d3:#dcdccc"
    show_titlebar = False
  [[Zenburn]]
    background_color = "#3f3f3f"
    background_type = transparent
    copy_on_selection = True
    cursor_color = "#73635a"
    foreground_color = "#dcdccc"
    palette = "#4d4d4d:#705050:#60b48a:#f0dfaf:#506070:#dc8cc3:#8cd0d3:#dcdccc:#709080:#dca3a3:#c3bf9f:#e0cf9f:#94bff3:#ec93d3:#93e0e3:#ffffff"
    show_titlebar = False
```
