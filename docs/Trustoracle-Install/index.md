# 安装部署

<!--TODO. 调整格式-->
Trustoracle 服务的部署模式有如下两种方式：

* **一键部署**

```eval_rst
.. admonition:: 适用场景

     - 快速体验 Trustoracle 服务
     - 预言机服务的开发和调试
```

一键部署使用一键部署脚本（`deploy-all.sh`），在部署 Trustoracle 服务时，会自动部署依赖服务，包括：

| 服务名  | 功能  |
|---|---|
| FISCO-BCOS  |  包含 4 个 FISCO-BCOS 节点的区块链底层服务 |
|  WeBASE-Front | 提供 Solidity 合约的可视化开发，编译，部署和调试功能  |
|  Trustoracle-Service | Trustoracle 的 **后** 端服务  |
| Trustoracle-Web  | Trustoracle 的 **前** 端页面  |
| MySQL  |  数据库服务，存储请求历史记录， 预言机相关合约的地址 |




* **独立部署**
```eval_rst
.. admonition:: 适用场景

     - 已有 FISCO-BCOS 底层节点
```

独立部署使用脚本工具（`deploy-single.sh`），仅仅部署 Trustoracle 的 **两个** 核心服务：

| 服务名  | 功能  |
|---|---|
|  Trustoracle-Service | Trustoracle 的 **后** 端服务  |
| Trustoracle-Web  | Trustoracle 的 **前** 端页面  |


```eval_rst
.. toctree::
   :maxdepth: 1

   ./docker-all.md
   ./docker-single.md
   ./appendix.md
```

