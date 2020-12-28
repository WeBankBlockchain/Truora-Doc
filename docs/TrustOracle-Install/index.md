# 安装部署

**硬件要求：** 

| 配置 | 最低配置 | 推荐配置 |
| ---- | -------- | -------- |
| CPU  | 1.5GHz   | 2.4GHz   |
| 内存 | 2GB      | 8 GB      |
| 核心 | 1核      | 4核      |
| 带宽 | 1Mb      | 10Mb     |

## 部署方式

### 一键部署

```eval_rst
.. admonition:: 适用场景

     - 快速体验
     - 开发调试 
```

一键部署使用一键部署脚本（`deploy-all.sh`），在部署 TrustOracle 服务时，会自动部署依赖服务，包括：

| 服务名  | 功能  |
|---|---|
| FISCO-BCOS  |  包含 4 个 FISCO-BCOS 节点的区块链底层服务 |
|  WeBASE-Front | 提供 Solidity 合约的可视化开发，编译，部署和调试功能  |
|  TrustOracle-Service | TrustOracle 的 **后** 端服务  |
| TrustOracle-Web  | TrustOracle 的 **前** 端页面  |
| MySQL  |  数据库服务，存储请求历史记录， Oracle 相关合约的地址 |


关于一键部署，请参考：[TrustOracle 一键部署](./docker-all.md)

### 独立部署
```eval_rst
.. admonition:: 适用场景

     - 已有 FISCO-BCOS 底层节点
```

独立部署是指仅仅部署 TrustOracle 的 **两个** 核心服务：

| 服务名  | 功能  |
|---|---|
|  TrustOracle-Service | TrustOracle 的 **后** 端服务  |
| TrustOracle-Web  | TrustOracle 的 **前** 端页面  |

针对独立部署，在使用 **源码编译** 部署的基础上，额外提供了一个 **脚本工具**，机遇 Docker 服务，快速部署 TrustOracle 的核心服务。

* **脚本工具**
    * 关于脚本工具进行独立部署，请参考：[独立部署](./docker-single.md)
    
* **源码编译**
    * [TrustOracle-Service](../TrustOracle-Service/deploy/install.md)
    * [TrustOracle-Web](../TrustOracle-Web/deploy/install.md)

