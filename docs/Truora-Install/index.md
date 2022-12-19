

# 安装部署

**<font color=#00BB00>！(*首先确认区块链底层对应的版本，并切换到对应的文档和代码分支进行操作*)！</font>**

** >> 面向区块链底层 FISCO-BCOS 2.6.0 +**

* 区块链底层对应 [FISCO-BCOS 2.6.0 +](https://github.com/FISCO-BCOS/FISCO-BCOS/releases/tag/v2.6.0) 
* 文档连接[v2stable](https://truora.readthedocs.io/zh_CN/v2stable/)	 
* Truora-Service 稳定代码分支[v2stable](https://github.com/WeBankBlockchain/Truora-Service/tree/v2stable)
* 一键安装，Truora docker和web相关模块参见[v2stable](https://truora.readthedocs.io/zh_CN/v2stable/)文档里的相关说明

** >> 面向区块链底层 FISCO-BCOS 3.1.x +**

* 区块链底层对应 [FISCO-BCOS 3.1及以上](https://github.com/FISCO-BCOS/FISCO-BCOS)
* 文档连接[Latest/v3dev分支](https://truora.readthedocs.io/zh_CN/latest/)	
*  Truora-Service 主分支[main](https://github.com/WeBankBlockchain/Truora-Service/tree/main)
*  Truora-Service 开发分支[v3dev](https://github.com/WeBankBlockchain/Truora-Service/tree/v3dev)
*  v3dev分支暂不包括一键安装、docker和web模块，欢迎参与开发贡献

**<font color=#0000BB>！(*首先确认区块链底层对应的版本，并切换到对应的文档和代码分支进行操作*)！</font>**


		
		
## 操作步骤
<!--TODO. 调整格式-->
Truora 服务的部署模式有如下两种方式：

* **一键部署**

```eval_rst
.. admonition:: 适用场景

     - 快速体验 Truora 服务
     - 预言机服务的开发和调试
```

一键部署使用一键部署脚本（`deploy-all.sh`），在部署 Truora 服务时，会自动部署依赖服务，包括：

| 服务名  | 功能  |
|---|---|
| FISCO-BCOS  |  包含 4 个 FISCO-BCOS 节点的区块链底层服务 |
|  WeBASE-Front | 提供 Solidity 合约的可视化开发，编译，部署和调试功能  |
|  Truora-Service | Truora 的 **后** 端服务  |
| Truora-Web  | Truora 的 **前** 端页面  |
| MySQL  |  数据库服务，存储请求历史记录， 预言机相关合约的地址 |




* **独立部署**
```eval_rst
.. admonition:: 适用场景

     - 已有 FISCO-BCOS 底层节点
```

独立部署使用脚本工具（`deploy-single.sh`），仅仅部署 Truora 的 **两个** 核心服务：

| 服务名  | 功能  |
|---|---|
|  Truora-Service | Truora 的 **后** 端服务  |
| Truora-Web  | Truora 的 **前** 端页面  |


```eval_rst
.. toctree::
   :maxdepth: 1

   ./docker-all.md
   ./docker-single.md
   ./appendix.md
```

