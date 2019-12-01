# 简介
updir 命令可 把 sour 目录中的所有文件和目录 上传到 服务器中 targ 目录中；

# 语法
```
updir [-tp|--type type] [-c|--conf conf] [-pw|--password password] [-h|--host host] [-p|--prot prot] [-s|--sour sour] [-t|--targ targ] [-d|--dele dele]
```

# 选项

updir 命令支持以下选项

* -tp|--type  : 配置文件的类型 该类型会作为- 配置文件的后缀名
* -c|--conf  : 配置文件的路径
* -n|--name  : ssh登录时的用户名
* -pw|--password  : 用户名对应的密码
* -h|--host : 服务器的地址
* -p|--prot : 服务器的端口
* -s|--sour  : 源目录
* -t|--targ  : 服器上的目标目录
* -d|--dele : 在上传sour目录中的文件之前，是否删除 targ 目录中的所有文件； 0 : 不删除；1 : 删除； 默认值: 1 ;
* -help|--help|help  : 输出 updir 命令的帮助信息



# 配置文件
配置文件是作为 tcl 脚本被优先执行的

## 配置文件可存放在两个地方
- 用户级配置文件：存放在~/.updir/ 目录下，文件名为 updirrc；后缀名为 type 选项和变量设置的字符串，如果没有配置 type 选项和变量，则没有后缀名
- 项目级配置文件：存放在当前目录下，文件名为 conf 选项和变量设置的字符串，如果没有配置conf选项和变量，则默认以 updirrc 开头；

## 配置文件可分为两类
- 私有配置文件：带有由 type 选项和变量指定的字符串作为后缀名的配置文件；该配置文件，只有设置了 type 选项或变量时，才会被加载
- 公共配置文件：不带有由 type 选项和变量指定的字符串作为后缀名的配置文件；该配置文件只要存在，就会被加载；

## 配置文件的加载顺序和优先级
选项和变量配置文件的加载顺序和优先级如下（优先级从底到高排列）:
1. 用户级的公共配置文件: ~/.updir/updirrc
2. 用户级的私有配置文件：~/.updir/updirrc.[type]
3. 项目级的公共配置文件: [PWD]/[conf]
4. 项目级的私有配置文件: [PWD]/[conf].[type]
5. 命令行选项

**备注：** 
方括号 [] 表示取相应变量的值


# 可配置变量

配置文件中可设置的变量及其语法如下

## type
配置文件的类型 该类型会作为配置文件的后缀名
```
set type ""
```

## conf
配置文件的路径
```
set conf "$env(PWD)/updirrc"
```

## name
ssh登录时的用户名
```
set name ""
```

## password
用户名对应的密码
```
set password ""
```

## host
服务器的地址
```
set host ""
```

## prot
服务器的端口
```
set prot ""
```

## sour
源目录
```
set sour "."
```

## targ
服器上的目标目录
```
set targ ""
```

## dele
在上传sour目录中的文件之前，是否删除 targ 目录中的所有文件
```
set dele 1
```