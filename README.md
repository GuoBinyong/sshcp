[API帮助文档]: ./API帮助文档.md

[赞赏码]: ./赞赏码.JPG

[sshcp仓库]: https://github.com/GuoBinyong/sshcp
[Homebrew]: https://brew.sh

[issues]: https://github.com/GuoBinyong/sshcp/issues

<!-- TOC -->

- [1. 简介](#1-简介)
- [2. sshcp解决的问题](#2-sshcp解决的问题)
- [3. 特性](#3-特性)
- [4. 依赖](#4-依赖)
- [5. 安装方式](#5-安装方式)
    - [方式1:直接安装](#方式1直接安装)
    - [方式2:通过brew安装](#方式2通过brew安装)
    - [方式3:通过自带的下载命令安装](#方式3通过自带的下载命令安装)
- [6. 语法](#6-语法)
- [7. 选项](#7-选项)
- [8. 配置文件](#8-配置文件)
    - [8.1. 配置文件可存放在两个地方](#81-配置文件可存放在两个地方)
    - [8.2. 配置文件可分为两类](#82-配置文件可分为两类)
    - [8.3. 配置文件的加载顺序和优先级](#83-配置文件的加载顺序和优先级)
    - [8.4. 配置文件查找规则](#84-配置文件查找规则)
- [9. 可配置变量](#9-可配置变量)
    - [type](#type)
    - [conf](#conf)
    - [name](#name)
    - [password](#password)
    - [host](#host)
    - [prot](#prot)
    - [sour](#sour)
    - [targ](#targ)
    - [dele](#dele)
    - [prompt](#prompt)
- [10. 配置文件示例](#10-配置文件示例)

<!-- /TOC -->


# 1. 简介
sshcp 命令使您可以通过一个简短的命令就实现如下功能：
* 把本地指定目录或文件 上传到 服务器的指定的目标目录中，上传之前也可配置是否要清空服务器的目标目录；
* 自动登录服务器，并转到指定的目标目录中；

主页：<https://github.com/GuoBinyong/sshcp>

**如果您在使用的过程中遇到了问题，或者有好的建议和想法，您都可以通过以下方式联系我，期待与您的交流：**  
- 给该仓库提交 [issues][]
- 给我 Pull requests
- 邮箱：<guobinyong@qq.com>
- QQ：guobinyong@qq.com
- 微信：keyanzhe


# 2. sshcp解决的问题
工作中经常需要将一些文件上传到服务器，而通常的步骤如下：
1. 通过 ssh 命令登录到服务器，如：`ssh -l root -p 22  192.168.90.33`
2. 输入登录服务器的密码；
3. 通过 cd 命令转到指定目录，如：`cd /data/mc/website/guo.binyong.com/www/webapp`
4. 通过 rm 命令删除掉所有的文件，如：`rm -rf *`
5. 退出 ssh 登录；
6. 通过 scp 命令将 本地某个目录里的所有文件 上传 到服务器刚才删除的目录内，如：`scp -r  dist/*  root@192.168.90.33:/data/mc/website/guo.binyong.com/www/webapp`
7. 输入登录服务器的密码；

每次上传，都要经过这几步，对于经常上传的人来说，这简直是灾难；

能不能直接写个 shell 脚本，自动执行呢？不能，因为 ssh 命令 和 scp 命令都没有提供密码选项，只能以交互的方式运行，即：只能手动输入密码；

那怎么办呢？

为了解决这个问题，我专门研究了 expect 和 tcl 脚本语言，最终，sshcp 工具诞生了；




# 3. 特性
- 一键上传指定的文件或文件夹到服务器的指定目录内；
- 一键登录并转到服务器的相应目录；
- 所有配置均可通过 命令行参数的形式 或 配置文件的形式提供；
- 支持多级配置文件，局部配置文件的配置会覆盖全局配置文件的配置；
- 支持多种运行模式，通过 type 选项，自动会加载对应类型的配置文件，方便随时切换不同的配置；



# 4. 依赖
sshcp 依赖于 expect；所以运行些脚本之前，需要保证环境中能访问到 expect 命令；

**注意：Mac系统自带 expect，无需额外安装其它东西**



# 5. 安装方式

## 方式1:直接安装
直接将 [sshcp仓库][] 中的 sshcp 文件放入命令行的 PATH 目录中，如：`/usr/local/bin/`；

**注意：**  
- 如果在你电脑中提示 sshcp 不是可执行文件，则可以对 sshcp 文件执行以下命令 `chmod a+x sshcp` 或 `chmod u+x sshcp`；



## 方式2:通过brew安装
如果你的电脑上已经安装了包管理工具 [brew][Homebrew]，则可以通过 brew 来安装，命令如下：
```
brew install sshcp
```


## 方式3:通过自带的下载命令安装
```
curl https://raw.githubusercontent.com/GuoBinyong/sshcp/master/sshcp -o /usr/local/bin/sshcp && chmod a+x /usr/local/bin/sshcp
```









# 6. 语法
```
sshcp [-tp|--type type] [-c|--conf conf] [-pw|--password password] [-h|--host host] [-p|--prot prot] [-s|--sour sour] [-t|--targ targ] [-d|--dele dele] [-pmt|--prompt prompt]

sshcp [-help|--help|help]
```

# 7. 选项

sshcp 命令支持以下选项

* -help|--help|help  : 输出 sshcp 命令的帮助信息
* -tp|--type  : 配置文件的类型，指定该参数，会优先匹配带有以该类型作为中缀名的配置文件；
* -c|--conf  : 配置文件的路径
* -n|--name  : ssh登录时的用户名
* -pw|--password  : 用户名对应的密码
* -h|--host : 服务器的地址
* -p|--prot : 服务器的端口
* -s|--sour  : 源目录；
   * 如果没有给该参数配置有效的值，则会进入交互模式，即：登录服务器并跳转到目标目录后，自动将控制权交给用户；
   * 如果该参数的值是以 / 结尾的目录路径，则会将该目录下的所有文件 和 文件夹 都上传到服务器的目标目录下；
   * 如果该参数的值不是以 / 结尾的 目录或文件，则会将 该目录 或 文件 上传到服务器的目标目录下；
* -t|--targ  : 服器上的目标目录
* -d|--dele : 在上传sour目录中的文件之前，是否删除 targ 目录中的所有文件； 0 : 不删除；1 : 删除； 默认值: 0 ;
* -pmt|--prompt  : 能匹配服务器的命令行提示符的字符串 或 正则表达式； 默认值：\S，即表示：包含非空字符的字符串；
   * 该参数只有在交互模式下才有效；
   * 该参数有助于精准识别 ssh 登录成功；
   * 由于该参数保存的是正则表达式，所以为了避免 tcl 正则的特殊字符 当作 tcl 的特殊字符去展开，建义在使用配置文件配置该参数时，将配置的正则表达式用 花括号 {} 包裹着；



# 8. 配置文件
配置文件是作为 tcl 脚本被优先执行的，所以配置文件中可以书写 tcl 代码来实现更加细粒度的控制和配置；

配置文件的全名（完整名称）由三部分组成：配置文件名、中缀名、后缀名，由 点 `.` 分隔，格式如下：
```
配置文件名.type.tcl
```
- 配置文件名 是 由 `-c|--conf` 选项指定的文件名
- 中缀名 是 由 `-tp|--type` 选项指定的类型字符串
- 后缀名 是 `tcl`
- 中缀名 和 后缀名 是 可选的；


## 8.1. 配置文件可存放在两个地方
- 用户级配置文件：存放在~/.sshcp/ 目录下，文件前缀名为 sshcprc；
- 项目级配置文件：存放在当前目录下，文件的前缀名为 conf 选项和变量设置的字符串，如果没有配置conf选项和变量，则默认的前缀名为 `sshcprc`；

## 8.2. 配置文件可分为两类
- 私有配置文件：带有由 type 选项和变量指定的字符串作为中缀名的配置文件；该配置文件，只有设置了 type 选项或变量时，才会被加载
- 公共配置文件：不带有由 type 选项和变量指定的字符串作为中缀名的配置文件；该配置文件只要存在，就会被加载；

## 8.3. 配置文件的加载顺序和优先级
选项和变量配置文件的加载顺序和优先级如下（优先级从底到高排列）:
1. 用户级的公共配置文件: ~/.sshcp/sshcprc[.tcl]
2. 用户级的私有配置文件：~/.sshcp/sshcprc.<type>[.tcl]
3. 项目级的公共配置文件: <PWD>/<conf>[.tcl]
4. 项目级的私有配置文件: <PWD>/<conf>.<type>[.tcl]
5. 命令行选项

**备注：** 
- 尖括号 `<>` 表示取相应变量的值
- 方括号 `[]` 表示是可选的


## 8.4. 配置文件查找规则
脚本会按照下面的顺序查找配置文件，如果找到，就会加载该配置文件并停止查找：
1. 查找带相应的带 `tcl` 后缀名的配置文件；
2. 查找不相应的不带 `tcl` 后缀名的配置文件；

脚本会先查找带 `tcl` 后缀名的配置文件，如果找到了，则会加载带 `tcl` 后缀名的配置文件，并且会忽略相应的不带 `tcl` 后缀名的配置文件；如果没有找到，则会继续查找不带 `tcl` 后缀名的配置文件；


# 9. 可配置变量

配置文件中可设置的变量及其语法示例如下

**注意：以下所有配置字段的值均是字符串，字符串可以用双引号 " 或 花括号 {} 包裹着，也可以不用包裹，当字符串中包含空格等能引起不达预期效果的字符时，可以通过将该字符串包裹在 双引号 或 花括号 {} 中来解决这类问题**

## type
配置文件的类型 该类型会作为配置文件的中缀名；
```
set type "dev"
```

## conf
配置文件的路径
```
set conf "sshcprc"
```

## name
ssh登录时的用户名
```
set name "keyanzhe"
```

## password
用户名对应的密码
```
set password "123456789"
```

## host
服务器的地址
```
set host "192.168.90.33"
```

## prot
服务器的端口
```
set prot "9033"
```

## sour
源目录；
* 如果没有给该参数配置有效的值，则会进入交互模式，即：登录服务器并跳转到目标目录后，自动将控制权交给用户；
* 如果该参数的值是以 / 结尾的目录路径，则会将该目录下的所有文件 和 文件夹 都上传到服务器的目标目录下；
* 如果该参数的值不是以 / 结尾的 目录或文件，则会将 该目录 或 文件 上传到服务器的目标目录下；
```
set sour "dist/"
```

## targ
服器上的目标目录
```
set targ "/data/mc/website/guo.binyong.com/www/webapp"
```

## dele
在上传sour目录中的文件之前，是否删除 targ 目录中的所有文件； 0 : 不删除；1 : 删除； 默认值: 0 ;
```
set dele 0
```

## prompt
能匹配服务器的命令行提示符的字符串 或 正则表达式； 默认值：\S，即表示：包含非空字符的字符串；
* 该参数只有在交互模式下才有效；
* 该参数有助于精准识别 ssh 登录成功；
* 由于该参数保存的是正则表达式，所以为了避免 tcl 正则的特殊字符 当作 tcl 的特殊字符去展开，建义在使用配置文件配置该参数时，将配置的正则表达式用 花括号 {} 包裹着；
```
set prompt {\S}
```

# 10. 配置文件示例
sshcprc.tcl
```
set name keyanzhe
set password 123456789
set host 192.168.90.33
set prot 9033
set sour dist/
set targ "/data/mc/website/guo.binyong.com/www/webapp"
set prompt {#}
```




--------------------

> 有您的支持，我会在开源的道路上，越走越远

![赞赏码][]