# 简介
sshcp 命令使您可以通过一个简短的命令就把把本地指定目录中的所有文件和目录 上传到 服务器的指定目录中，或者 直接登录并转到服务器的相应目录；

[主页](https://github.com/GuoBinyong/sshcp)

# 语法
```
sshcp [-tp|--type type] [-c|--conf conf] [-pw|--password password] [-h|--host host] [-p|--prot prot] [-s|--sour sour] [-t|--targ targ] [-d|--dele dele]
```

# 选项

sshcp 命令支持以下选项

* -tp|--type  : 配置文件的类型，指定该参数，会优先匹配带有以该类型作为后缀名的配置文件；
* -c|--conf  : 配置文件的路径
* -n|--name  : ssh登录时的用户名
* -pw|--password  : 用户名对应的密码
* -h|--host : 服务器的地址
* -p|--prot : 服务器的端口
* -s|--sour  : 源目录；默认值：当前目录；
* -t|--targ  : 服器上的目标目录
* -d|--dele : 在上传sour目录中的文件之前，是否删除 targ 目录中的所有文件； 0 : 不删除；1 : 删除； 默认值: 1 ;
* -help|--help|help  : 输出 sshcp 命令的帮助信息



# 配置文件
配置文件是作为 tcl 脚本被优先执行的，所以配置文件中可以书写 tcl 代码来实现更加细粒度的控制和配置；

## 配置文件可存放在两个地方
- 用户级配置文件：存放在~/.sshcp/ 目录下，文件名为 sshcprc；后缀名为 type 选项和变量设置的字符串，如果没有配置 type 选项和变量，则没有后缀名
- 项目级配置文件：存放在当前目录下，文件名为 conf 选项和变量设置的字符串，如果没有配置conf选项和变量，则默认以 sshcprc 开头；

## 配置文件可分为两类
- 私有配置文件：带有由 type 选项和变量指定的字符串作为后缀名的配置文件；该配置文件，只有设置了 type 选项或变量时，才会被加载
- 公共配置文件：不带有由 type 选项和变量指定的字符串作为后缀名的配置文件；该配置文件只要存在，就会被加载；

## 配置文件的加载顺序和优先级
选项和变量配置文件的加载顺序和优先级如下（优先级从底到高排列）:
1. 用户级的公共配置文件: ~/.sshcp/sshcprc
2. 用户级的私有配置文件：~/.sshcp/sshcprc.[type]
3. 项目级的公共配置文件: [PWD]/[conf]
4. 项目级的私有配置文件: [PWD]/[conf].[type]
5. 命令行选项

**备注：** 
方括号 [] 表示取相应变量的值


# 可配置变量

配置文件中可设置的变量及其语法示例如下

## type
配置文件的类型 该类型会作为配置文件的后缀名；
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
set name "root"
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
源目录；默认值：当前目录；
```
set sour "dist"
```

## targ
服器上的目标目录
```
set targ "/data/mc/website/guo.binyong.com/www/webapp"
```

## dele
在上传sour目录中的文件之前，是否删除 targ 目录中的所有文件； 0 : 不删除；1 : 删除； 默认值: 1 ;
```
set dele 1
```