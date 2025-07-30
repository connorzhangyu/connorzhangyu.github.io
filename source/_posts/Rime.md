---
title: Rime
categories:
  - 软件
tags:
  - 笔记
cover: https://image.runtimes.cc/202404051532600.png
description: Rime教程
abbrlink: 31017
date: 2023-07-02 22:37:34
updated: 2023-07-07 15:31:42
---

# 基本概念

Rime 是一个输入法框架，并不是狭义上的“输入法”，而是将各种输入法的共性抽象出来的算法框架。通过不同的配置文件，Rime 可以支持多种输入方案（Schema），这个所谓的输入方案就是我们狭义上的“输入法”。比如朙月拼音输入法就是 Rime 自带的一种输入方案，另外还有比如四叶草输入法（https://github.com/fkxxyz/rime-cloverpinyin）等等。鼠须管、小狼毫、中州韵分别是 Rime 在不同操作系统下的实现程序。Rime 的配置、词库文件均使用文本方式，便于修改。所有文件均要求以 UTF-8 编码。在配置文件中，以 # 号开头表示注释。

# 配置文件所在的目录

Rime 有两个重要的配置目录：

## 共享配置目录

- 【中州韻】 `/usr/share/rime-data/`
- 【小狼毫】 `"安裝目錄\data"`
- 【鼠鬚管】 `"/Library/Input Methods/Squirrel.app/Contents/SharedSupport/"`

## 用户配置目录

- 【中州韻】 `~/.config/ibus/rime/` （0.9.1 以下版本爲 `~/.ibus/rime/`）
- 【小狼毫】 `%APPDATA%\Rime`
- 【鼠鬚管】 `~/Library/Rime/`

共享目录下放置的是 Rime 的预设配置，在软件版本更新时候，也会自动更新该目录下的文件。所以请不要修改该目录下的文件。

用户目录则放置用户自定义的配置文件。我们要做的修改都放在用户目录下。

对于鼠须管而言，用户目录初始时只有如下几个文件。

* `installion.yaml` 文件记录的是当前 Rime 程序的版本信息。其中有一个字段 `installation_id` 用来在同步用户词典时唯一标记当前 Rime 程序。

* `user.yaml` 文件记录用户的使用状态。比如上次“重新部署”的时间戳，上次选择的输入方案等。

* `build` 目录下放的是每次“重新部署”后生成的文件。包括字典文件编译后生成的「.bin」文件，包括与自定义配置合并后生成的各种 yaml 配置文件。

* `xxx.userdb` 目录下放的是对应输入方案的用户词典。即用户在使用时候选择的词组、词频等动态信息，这个目录是实时更新的。

* `sync` 目录是用来做用户数据同步的。每个 `sync/installation_id` 目录对应不同电脑上的 Rime 程序的用户数据。（如果你由多台电脑安装了 Rime，并设置了同步。）按照作者的说法，Rime 的用户词典同步原理是：

* 手工从其他电脑复制或者从网盘自动同步 ⇒ `sync/*/*.userdb.txt` ⇒ 合并到本地 `*.userdb` ⇒ 导出到 `sync/<installation_id>/*.userdb.txt`。

## 关于调试

Rime 的日志目录放在如下为止：

- 【中州韻】 `/tmp/rime.ibus.*`
- 【小狼毫】 `%TEMP%\rime.weasel.*`
- 【鼠鬚管】 `$TMPDIR/rime.squirrel.*`
- 早期版本 `用户配置目录/rime.log`

# 修改配置

如果想要修改配置，请不要直接修改原有的 `xxx.yaml` 文件，而是应该新建一份 `xxx.custom.yaml` 文件，其中 `xxx` 与原文件名相同。

在 `.custom.yaml` 文件中对于要修改的配置项，都需要放在 `patch` 根节点下面。

每次修改配置，都需要在鼠须管的菜单中选择“重新部署”后才能生效。

## 修改候选词个数

Rime 默认每次出现的候选词个数为 5 个，我们可以将其修改为 1~9 之间的任意数。

在用户目录下新建一个 `default.custom.yaml` 文件（`default.yaml` 文件可以在共享配置目录下找到），写入如下内容：

```yaml
patch:
  "menu/page_size": 9   # 字段名加不加双引号都可以
  
# 或者是這樣

patch:
  menu: 
    page_size: 8
# 但是注意这种写法是覆盖整个 menu 字段。好在默认情况下，menu 下一级也只有 page_size 字段，如果是有多个下级字段，请不要这么写。 
```

上面的 `default` 文件是修改所有输入方案的候选词个数，如果只想针对某个输入方案做调整，比如对于朙月输入方案，那么只需要在用户目录下建立 `luna_pinyin.custom.yaml` 文件并写入如上内容，再重新部署即可。（注意，对输入方案定义文件 `xxx.schema.yaml` 的修改，新建的文件名只需要是 `xxx.custom.yaml`，并不需要加上 `schema`，写成 `xxx.schema.custom.yaml` 这样。）



使用，使用快捷键`F4`，然后像拼音打字选候选字一样，选择就好

输入方案的可切换状态，请参考后续的 `switches` 章节

## 主题

鼠须管的外观配置文件是 `squirrel.yaml`（小狼毫的外观配置文件是 `weasel.yaml`）。所以我们需要在用户配置目录下新建一个 `squirrel.custom.yaml`，参考或者拷贝开源项目的写法，通常有很多个主题，我们可以通过 `style/color_scheme` 来选择一个主题

[开源项目的主题文件](https://github.dev/ssnhd/rime/blob/25462b6e887a3c3ff9b77d193a4cf3d18c334c7f/配置文件/luna_pinyin_simp.custom.yaml)

> 参考:
>
> [鼠须管输入法配置 - 哈呜.王 (hawu.me)](https://www.hawu.me/others/2666)
>
> [Rime Squirrel 鼠须管输入法配置详解 - 知乎 (zhihu.com)](https://zhuanlan.zhihu.com/p/474994157)



