# 多链操作手册

## 部署物料包
```
$ git clone https://github.com/ywy2090/multi-chain.git
$ cd multi-chain
```
多链物料包结构如下所示，相关操作会在对应命令中进行解释
```
|-- conf
|-- data
|-- format.sh
|-- log
|-- main.py
|-- new.md
|-- pys
|-- README.md
|-- release_note.txt
|-- scripts
`-- tpl
```


## 查询命令 -h
```
python main.py -h
```
可以查询到相关的操作命令
```
usage: main.py [-h] [--version] [--init_ansible]
               [--build ./config.conf or ./conf/ fisco_path]
               [--publish chain_id:version eg. [chain_id_1:version_1 chain_id_2:version_1 chain_id_3:version_2.etc. ...]]
               [--check all or chain_id or [chain_id:host_ip ...]]
               [--stop all or chain_id or [chain_id:host_ip ...]]
               [--start all or chain_id or [chain_id:host_ip ...]]
               [--monitor all or chain_id or [chain_id:host_ip ...]]
               [--pkg_list all or chain_id or [chain_id_1 chain_id_2 ...]]
               [--pub_list all or chain_id or [chain_id_1 chain_id_2 ...]]
               [--telnet all or host_ip or [host_ip1 host_ip2 ...]]
               [--env_check all or host_ip [all or host_ip ...]]
               [--cmd_push all:"cmd_1 cmd_2" or chain_id:"cmd_1 cmd_2" or [hostip:"cmd_1 cmd_2" ...]]
               [--file_push all:scr_path:dest_path or chain_id:scr_path:dest_path or [chain_id:scr_path:dest_path or host_ip:scr_path:dest_path ...]]
               [--chainca ./dir_chain_caSET)]
               [--agencyca ./dir_agency_ca(SET) ./chain_ca_dir The Agency Name]
               [--sdkca ./dir_sdk_ca(SET ./dir_agency_ca]
```
以上是物料包支持的相关操作，解释如下。

## 查看多链物料包版本 --version命令
本命令用于查看当前多链物料包版本
```
python main.py --version
```
会得到如下提示
```
INFO | v1.0.0
```

## 初始化ansible hosts --init_ansible命令
用户在配置conf/hosts时，需要用到本命令。

本命令需要sudo权限对ansible的配置进行修改，用户每次修改hosts.conf都需要运行本命令。

```
python main.py --init_ansible
```
会得到如下提示
```
INFO | ansible init success
```
**注意，用户如果需要修改主机配置，则每次修改完./conf/hosts.conf后都需要执行init_ansible命令**


## 检查目标服务器环境依赖 --env_check命令
本命令可以检测目标服务器环境依赖是否满足

```
python main.py --env_check all  or host_ip ...
```

用户可以指定参数all查询配置的所有服务器，或是指定某些服务器ip查询对应服务器依赖。

## 生成多链安装包 --build命令
本命令是解析用户输入的conf文件，生成相应安装包的命令。用户使用前需要确保运维服务器可以启动1.3版本的fisco-bcos，并且对应服务器的环境可以启动fisco-bcos

### 生成单条链
命令有两个input参数，分别为conf配置和fisco-bcos路径，示例如下
```
$ python main.py --build ./conf/sample_12345_v1.0.conf $PATH/fisco-bcos
```
如果要生成某一条的链安装包执行第一个命令，如果在该目录下有多个安装包的conf文件，则执行第二条命令。build命令第二项参数为fisco-bcos的路径

mchain.conf为相关的部署目录和机构名称，用户根据需要修改。

用户使用时，首先更改./conf 目录下的sample_12345_v1.0.conf文件，如果需要部署多条链，需要拷贝多个conf文件。

执行
```
$ python main.py --build ./conf $PATH/fisco-bcos
```
可以生成conf文件夹下配置的多条链

## 网络环境测试 --telnet命令
telnet命令用来测试运维服务器是否可以与配置好的所有服务器进行ansible通信，操作如下
```
python main.py --telnet all or host_ip or [host_ip1 host_ip2 ...]
```
检测ansible配置的所有服务器是否可以进行通信
示例：
### 测试所有配置服务器
```
python main.py --telnet all
```
正确会得到以下提示
```
INFO |  telnet begin.
INFO |  ansible telnet success, host is all.
INFO |  telnet end.
```
### 测试单个服务器
```
python main.py --telnet 127.0.0.1
```
正确会得到以下提示
```
INFO |  telnet  begin.
INFO |  ansible telnet success, host is 127.0.0.1.
INFO |  telnet end.
```
### 测试多个服务器
```
python main.py --telnet 127.0.0.1 127.0.0.2 127.0.0.3
```
正确会得到以下提示
```
INFO |  telnet begin.
INFO |  ansible telnet success, host is 127.0.0.1.
INFO |  ansible telnet success, host is 127.0.0.2.
INFO |  ansible telnet success, host is 127.0.0.3.
INFO |  telnet end.
```



## 发布多链安装包 --publish命令
publish为多链的发布命令，用户需要制定链id和版本号，中间用":"隔开
```
$ python main.py --publish chain_id_1:version_1 chain_id_2:version_2 ...
```
chain_id_n:version_n 为chain的id和版本号，中间用":"隔开
示例：
用户需要推上面生成的链版本号为12345,12346,12347的链。

执行:
```
$ python main.py --publish 12345:v1.0.0 12346:v1.0.0 12347:v1.0.0
```
执行完成后可以看到
```
INFO |  publish begin.
****
****
INFO |  publish end.
```

## 启动多链节点 --start命令
start命令允许用户在运维服务器上启动所有节点，或者某条链的节点，或者某条链对应某个服务器的节点。
```
$ python main.py --start [all or chain_id or chain_id:host_ip]
```

三种参数分别对应启动部署的所有链的节点，部署的对应chain_id链的节点，和对应chain_id的host_ip的服务器的节点
### 启动部署所有链所有节点
```
$ python main.py --start all
```
执行完成后可以看到
```
INFO |  start begin.
INFO |  ansible start success, host is 127.0.0.1.
INFO |  ansible start success, host is 127.0.0.2.
***
***
INFO |  start end.
```
上述命令启动了部署完成的所有链的全部节点
### 启动多条链节点
```
$ python main.py --start 12345 12346 12347
```
执行完成后可以看到
```
INFO |  start begin.
INFO |  ansible start success, host is 127.0.0.1.
INFO |  ansible start success, host is 127.0.0.2.
***
***
INFO |  start end.
```
上述命令启动了链id为12345 12346 12347三条链的节点

### 启动多条链对应服务器节点
```
$ python main.py --start 12345:127.0.0.1 12346:127.0.0.2 12347:127.0.0.3
```
执行完成后可以看到
```
INFO |  start begin.
INFO |  ansible start success, host is 127.0.0.1.
INFO |  ansible start success, host is 127.0.0.2.
***
***
INFO |  start end.
```
上述命令启动了链12345在127.0.0.1服务器上的3个节点， 链12346在127.0.0.2上的3个节点， 12347在127.0.0.3上的2个节点。

## 停止多链节点 --stop命令
```
$ python main.py --stop [all or chain_id or chain_id:host_ip]
```
stop命令的原理与start命令类似，示例如下：
### 停止部署所有链所有节点
```
$ python main.py --stop all
```

上述命令停止了部署完成的所有链的全部节点
### 停止多条链节点
```
$ python main.py --stop 12345 12346 12347
```

上述命令启动了链id为12345 12346 12347三条链的节点

### 停止多条链对应服务器节点
```
$ python main.py --stop 12345:127.0.0.1 12346:127.0.0.2 12347:127.0.0.3
```
上述命令停止了链12345在127.0.0.1服务器上的3个节点， 链12346在127.0.0.2上的3个节点， 12347在127.0.0.3上的2个节点。

## 检查多链节点启动情况 --check命令
```
$ python main.py --check [all or chain_id or chain_id:host_ip]
```
check命令的原理与start命令类似
示例如下：
### 检查部署所有链所有节点
```
$ python main.py --check all
```

### 检查多条链节点
```
$ python main.py --check 12345 12346 12347
```

### 检查多条链对应服务器节点
```
$ python main.py --check 12345:127.0.0.1 12346:127.0.0.2 12347:127.0.0.3
```

## 检查多链节点运行情况 --monitor命令
```
$ python main.py --monitor [all or chain_id or chain_id:host_ip]
```
monitor命令的原理与start命令类似
示例如下：
### 检查部署所有链所有节点
```
$ python main.py --monitor all
```

### 检查多条链节点
```
$ python main.py --monitor 12345 12346 12347
```

### 检查多条链对应服务器节点
```
$ python main.py --monitor 12345:127.0.0.1 12346:127.0.0.2 12347:127.0.0.3
```

**请注意 monitor命令是检查节点的运行状况，check命令是检查节点的启动情况**

## 列出生成安装包节点 --pkg_list命令
```
$ python main.py --pkg_list [all or chain_id]
```
pkg_list命令的原理与start命令类似
示例如下：
### 列出所有节点
```
$ python main.py --pkg_list all
```

### 列出多条链节点
```
$ python main.py --pkg_list 12345 12346 12347
```


## 列出发布安装包节点 --pub_list命令
```
$ python main.py --pub_list [all or chain_id]
```
ppub_list命令的原理与start命令类似
示例如下：
### 列出所有节点
```
$ python main.py --pub_list all
```

### 列出多条链节点
```
$ python main.py --pub_list 12345 12346 12347
```

## 执行shell --cmd_push命令
cmd_push命令允许用户批量在对应服务器上执行本地的脚本，或是在对应服务器上直接执行一条shell命令
```
$ python main.py --cmd_push  all:"cmd_1 cmd_2" or chain_id:"cmd_1 cmd_2" or all:"./test.sh"
```

执行时命令会检测本地是否有对应路径下的脚本文件，如果没有会去对应服务器执行

## 传输单独文件 --file_push命令
file_push命令允许用户批量传输某个文件到对应服务器。
```
$ python main.py --file_push all:scr_path:dest_path or chain_id:scr_path:dest_path or [chain_id:scr_path:dest_path or host_ip:scr_path:dest_path ...]]
```

命令中第一项是对应服务器或链参数

第二项为本地文件路径

第三项为对应服务器路径

注意本地路径和对应服务器路径格式一致，如都为绝对路径或相对路径

## ca证书相关操作 
多链物料包允许用户生成相应证书，fisco bcos的证书相关介绍请参考-[证书介绍](https://fisco-bcos-documentation.readthedocs.io/zh_CN/feature-multichain/docs/usage/cert_permission.html)，操作如下

### 生成根证书 --chainca命令
用户可以指定目录，生成根证书
```
python main.py --chainca ./dir_chain_ca(SET)
```
执行完成后用户可以在指定文件夹下看到根证书ca.crt 和私钥ca.key 。
### 生成机构证书 --agencyca命令
用户可以指定机构证书目录，链证书存放目录和机构名称，生成机构证书
```
python main.py --agencyca ./dir_agency_ca(SET) ./chain_ca_dir The_Agency_Name
```
执行完成后可以在./dir_agency_ca(SET)路径下生成名为The_Agency_Name的文件夹，包含相应的机构证书

### 生成sdk证书 --sdkca
用户可以指定sdk存放目录，机构证书存放目录，生成sdk证书
sdkca ./dir_sdk_ca(SET) ./dir_agency_ca
执行完成后可以在./dir_sdk_ca(SET)路径下生成名为sdk的文件夹，包含相应的sdk证书


