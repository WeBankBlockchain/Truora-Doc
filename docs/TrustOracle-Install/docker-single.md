# 独立部署
```eval_rst
.. admonition:: 提示

    - 由于 Docker 的网络限制，使用独立部署 TrustOracle 服务时，**仅支持 Linux 操作系统！！**
```

```eval_rst
.. important::

    - 使用独立部署功能的用户，需要对 MySQL 和 FISCO-BCOS sdk 证书有一定了解。
```
## 安装介绍

独立部署是基于 `Docker`，`Docker-Compose` 和 `Bash Shell` 封装的一个部署工具，提供一键部署 TrustOracle 服务，并连接到已有 FISCO-BCOS 链。

适合以下场景：

* 已有 FISCO-BCOS 底层节点

使用独立部署工具，部署 TrustOracle 服务时，会部署 TrustOracle-Service 和 TrustOracle-Web 服务，此外，可以选择是否部署一个 MySQL 服务。

```eval_rst
.. important::

    - 使用独立部署时，TrustOracle-Service 需要链接到 FISCO-BCOS 节点。需要手动提供链接节点的 sdk 相关文件。
    - 使用独立部署时，如果选择 **不部署** MySQL，在部署时，需要提供 MySQL 的链接信息，包括：IP，端口，用户名和密码。
```

部署的服务包括：
* TrustOracle-Web
* TrustOracle-Service 
* MySQL（**可选**）


## 前置要求


### 系统要求

| 操作系统         |  版本最低要求     |  
| ------------- |:-------|
| CentOS/RHEL |7.3（kernel >= 3.10.0-514）|
|Debian|Stretch 9  |
|Ubuntu|Xenial 16.04 (LTS)|


### 硬件配置

| 配置 | 最低配置 | 推荐配置 |
| ---- | -------- | -------- |
| CPU  | 1.5 GHz   | 2.4 GHz   |
| 内存 | 2 GB      | 8 GB      |
| 核心 | 1 核      | 4 核      |
| 带宽 | 1 Mb      | 10 Mb     |

## 脚本说明
TrustOracle 独立部署工具特性：

* 提供自动安装依赖服务功能，包括：`OpenSSL`, `curl`, `wget`, `Docker`, `Docker Compose` 等
* 部署 TrustOracle-Service, TrustOracle-Web 服务
* 部署 MySQL 服务（可选）
* 支持国密选项

关于脚本详细参数列表，请参考：[脚本参数](./appendix.html#shell_script_param)

## 获取部署脚本
部署脚本的获取方式包括：

* 下载部署包（**推荐使用**）
* Github 仓库拉取源码

<!-- TODO add latest release-->
```Bash
## 从 GitHub 下载最新部署包
wget "https://github.com/WeBankBlockchain/TrustOracle-Service/releases/v1.0.0/docker-deploy.zip"

## 解压部署包
unzip docker-deploy.zip

## 进入部署脚本目录
cd docker-deploy
```

如果需要下载指定版本，在 [版本列表中](https://github.com/WeBankBlockchain/TrustOracle-Service/releases) 选择相应版本下载。

```eval_rst
.. admonition:: 提示

     	- 由于网络原因，如果遇到打不开 Github 页面，或者无法从 GitHub 下载，可以从 CDN 下载。关于 CDN 说明，请参考：`CDN -- 部署工具包 <./appendix.html#cdn_instruction>`_
```

如果需要从代码仓库，拉取部署包源码，请参考：[部署工具源码](./appendix.html#pull_deploy_code)

## 部署

* 进入部署脚本（`deploy_single.sh`）所在目录，执行命令：

```Bash
# 自动安装依赖服务，默认从 CDN 拉取 Docker 镜像
# Docker Hub 官方仓库拉取镜像时，不仅速度比较慢，同时成功率也相对较低
# 
# -d : 自动安装系统依赖
# -g : 使用国密
# -m : 部署 MySQL 服务
# -k : 从 Docker Hub 官方仓库拉取 Docker 镜像
bash deploy_single.sh -d
```

关于脚本详细参数列表，请参考：[脚本参数](./appendix.html#shell_script_param)

* 出现提示输入 SDK 目录，输入目录后，按 **回车** 确认：

```Bash
.......
============================================================================================
[INFO] Deploy services ...
[INFO] Enter certifications info.

# 提示输入 SDK 目录，
Enter SDK path, e.g:[ /root/webank/deploy/deploy/fiscobcos/nodes/127.0.0.1/sdk ] 

.......
```

脚本会自动检测输入的 SDK 目录中，是否存在 `ca.crt`，`node.crt`，`node.key` 文件

```eval_rst
.. important::

    - 脚本 **不会检查 SDK 文件和链是否匹配**。
```

* 如果未使用 `-m` 参数，会提示输入 MySQL 的连接信息。直接 **回车** 使用默认值：

```Bash
.......
[INFO] User external MySQL.

Enter MySQL IP, default: 127.0.0.1 ? 10.10.1.1

Enter MySQL port, default: 3306 ?

Enter MySQL user, default: trustoracle ?

Enter MySQL password, default: defaultPassword ? user

.......
```

在 MySQL 中创建一个 `trustoracle` 数据库，供 TrustOracle-Service 服务使用。

```SQL
# 连接 MySQL
mysql -u root -p 

# 创建 trustoracle 数据库
CREATE DATABASE `trustoracle` /*!40100 DEFAULT CHARACTER SET utf8mb4 */;
```

* 修改 FISCO-BCOS 节点连接信息，编辑 `trustoracle/docker-compose.yml` 文件：

```yml
.....
      # FISCO-BCOS 节点 IP，默认：127.0.0.1
      - "FISCO_BCOS_IP=127.0.0.1"
      # FISCO-BCOS 节点端口，默认：20200
      - "FISCO_BCOS_PORT=20200"
      # FISCO-BCOS 连接群组，默认：1
      - "FISCO_BCOS_GROUP=1"
.....
```

* 修改 TrustOracle-Service 配置，请参考：[修改配置](../TrustOracle-Service/deploy/install.html#modify_service_config)

```eval_rst
.. admonition:: 提示

    - `trustoracle.yml` 配置文件中的 `${TRUSTORACLE_SERVICE_PORT:5021}` 表示 TrustOracle-Service 在启动时，会优先从系统环境变量中读取 `TRUSTORACLE_SERVICE_PORT` 的值。如果 `TRUSTORACLE_SERVICE_PORT` 环境变量 **没有设置** 或者 **值为空**，则使用默认值 `5021`。
```

## 服务启停
如果一键部署脚本 `deploy_all.sh` 执行成功后显示 `Deploy TrustOracle service SUCCESS!!` ，表示部署成功。

* 使用 `bash start.sh` 启动 TrustOracle 服务。

* 使用 `bash stop.sh`  停止服务。

在启动时，脚本会依次启动服务，并检测服务启动结果。

如果提示 `TrustOracle service start up SUCCESS !!`，表示 TrustOracle 服务启动成功。

如果启动失败，根据命令行的提示，检查启动失败服务的日志。关于查看服务的日志，请参考：[日志查看](./appendix.html#check_log)

启动后