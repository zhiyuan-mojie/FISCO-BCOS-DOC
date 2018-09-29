# ansible 配置文档
请注意
**只需要在运维服务器上部署ansible即可**

部署多链时需要用到ansible进行多服务器的数据流传输。本文是多链物料包ansible的配置文档。

## 安装
centOS系统
```
配置EPEL
yum install https://dl.fedoraproject.org/pub/epel/epel-release-latest-7.noarch.rpm
sudo yum install ansible
```
ubuntu系统
```
$ sudo apt-get install software-properties-common
$ sudo apt-add-repository ppa:ansible/ansible
$ sudo apt-get update
$ sudo apt-get install ansible
```

## 配置
配置前需要确保与对应主机可以进行ssh通信,物料包使用的ansible通信方式自动公钥认证方式，因此用户需要执行以下命令配置ssh密钥。

```
$ ssh-keygen -t rsa
$ cp id_rsa  id_rsa.pub ~/.ssh/
```
用户在OWMC/conf文件夹下修改hosts.conf格式如下
```
user 127.0.0.1 22 123
user 127.0.0.2 80 123
user 127.0.0.3 36000 123
```
第一项为用户名，第二项为ip 第三项为端口号，第四项为密码

需要在sshd_config文件中开启key认证
```
$ vim /etc/ssh/sshd_config
PubkeyAuthentication yes  //将该项改为yes 
```
修改完成后，通过/etc/init.d/sshd restart 重启ssh服务重新加载配置。
之后在多链物料包文件夹下执行
```
python main.py --init_ansible
```
会得到如下提示
```
INFO | ansible init success
```
**注意，用户如果需要修改主机配置，则每次修改完./conf/hosts.conf后都需要执行init_ansible命令**

## 检测
配置完成之后，使用下述命令进行测试
```
$ ansible all -m ping
```

如果正确会输出下述标准输出

```
127.0.0.1 | SUCCESS => {
    "changed": false, 
    "ping": "pong"
}
127.0.0.2 | SUCCESS => {
    "changed": false, 
    "ping": "pong"
}
127.0.0.3 | SUCCESS => {
    "changed": false, 
    "ping": "pong"
}
127.0.0.4 | SUCCESS => {
    "changed": false, 
    "ping": "pong"
}
```