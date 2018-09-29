################################################################################
Over-Wafer-Multi-Chain(OWMC)多链搭建工具
################################################################################


.. 什么是多链搭建工具

FOver-Wafer-Multi-Chain 多链搭建是针对机构内同时部署多条链的物料包。操作者可以在单台运维服务器上通过配置ansible与多台服务器进行交互，从而快速在多台服务器上部署多条区块链。

列如：在一台服务器上，配置好与其他服务器的ssh密钥，生成三条链，每条链在三台服务器上有三个节点，将安装包推给对应服务器，启动节点，组成三条区块链网络。

版本支持

当前多链物料包版本号为v1.0.0

执行

```
git clone https://github.com/HaoXuan40404/Over-Wafer-Multi-Chain.git
```

```
git checkout dev
```

多链物料包的相关流程如下:


.. toctree::
   :maxdepth: 1

   envcheck.md
   javacheck.md
   pythoncheck.md
   ansiblecheck.md
   operator.md
   sample.md
   FAQ.md

