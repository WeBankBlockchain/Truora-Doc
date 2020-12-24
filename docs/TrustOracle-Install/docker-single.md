# 独立部署
```eval_rst
.. admonition:: 提示

     - 由于 Docker 的网络限制，使用独立安装 TrustOracle 服务时，**仅支持 Linux 操作系统！！**
```

## 安装介绍
Docker 独立安装，是指使用 `deploy.sh` 工具，只部署 TrustOracle-Service 服务。

Docker 独立安装适合以下场景：

* 用户自行部署底层 FISCO-BCOS 节点，并且自行安装 MySQL 数据库
* Oracle 服务的开发和调试



```eval_rst
.. important:: 

     - CentOS 的 yum 仓库的 OpenJDK 缺少 JCE(Java Cryptography Extension)，导致 Web3SDK 无法正常连接区块链节点，推荐自行从 `OpenJDK网站 <https://jdk.java.net/java-se-ri/11>`_ 自行下载后安装。
     
```

