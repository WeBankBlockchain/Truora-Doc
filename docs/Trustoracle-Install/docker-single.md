# 独立部署
```eval_rst
.. admonition:: 提示

    - 由于 Docker 的网络限制，使用独立部署 Trustoracle 服务时，**仅支持 Linux 操作系统！！**
```

```eval_rst
.. important::

    - 使用独立部署功能的用户，需要对 MySQL 和 FISCO-BCOS sdk 证书有一定了解。
```
## 安装介绍

独立部署是基于 `Docker`，`Docker-Compose` 和 `Bash Shell` 封装的一个部署工具，提供一键部署 Trustoracle 服务，并连接到已有 FISCO-BCOS 链。

适合以下场景：

* 已有 FISCO-BCOS 底层节点

使用独立部署工具，部署 Trustoracle 服务时，会部署 Trustoracle-Service 和 Trustoracle-Web 服务，此外，可以选择是否部署一个 MySQL 服务。

```eval_rst
.. important::

    - 使用独立部署时，Trustoracle-Service 需要链接到 FISCO-BCOS 节点。需要手动提供链接节点的 sdk 相关文件。
    - 使用独立部署时，如果选择 **不部署** MySQL，在部署时，需要提供 MySQL 的链接信息，包括：IP，端口，用户名和密码。
```

部署的服务包括：
* Trustoracle-Web
* Trustoracle-Service 
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
| 内存 | 4 GB      | 8 GB      |
| 核心 | 2 核      | 4 核      |
| 带宽 | 1 Mb      | 10 Mb     |

## 脚本说明
Trustoracle 独立部署工具特性：

* 提供自动安装依赖服务功能，包括：`OpenSSL`, `curl`, `wget`, `Docker`, `Docker Compose` 等
* 部署 Trustoracle-Service, Trustoracle-Web 服务
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
wget "https://github.com/WeBankBlockchain/Trustoracle-Service/releases/download/v1.0.0/docker-deploy.zip"

## 解压部署包
unzip docker-deploy.zip

## 进入部署脚本目录
cd docker-deploy
```

如果需要下载指定版本，在 [版本列表中](https://github.com/WeBankBlockchain/Trustoracle-Service/releases) 选择相应版本下载。

```eval_rst
.. admonition:: 提示

     	- 由于网络原因，如果遇到打不开 Github 页面，或者无法从 GitHub 下载，可以从 CDN 下载。关于 CDN 说明，请参考：`CDN -- 部署工具包 <./appendix.html#cdn_instruction>`_
```

如果需要从代码仓库，拉取部署包源码，请参考：[部署工具源码](./appendix.html#pull_deploy_code)

## 部署

### 执行部署脚本
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

```eval_rst
.. important::

    - 如果使用 `-g` 参数部署 Trustoracle 国密版本时，需要确保 **链类型：国密**，**连接类型：国密**。关于链类型，国密类型，请参考：`加密类型 <../Trustoracle-Service/deploy.html#encrypt_type>`_
```

### 配置证书目录

```eval_rst
.. important::

    - 部署脚本 **不会检查 SDK 文件和需要连接的链是否匹配**，只会检查必须的证书文件是否存在。
```

#### 非国密 Trustoracle（ECDSA）

提示输入 SDK 目录（目录需要存在对应链的 `ca.crt`，`node.crt`，`node.key` 文件），输入目录后，按 **回车** 确认：

```Bash
.......
============================================================================================
[INFO] Deploy services ...
[INFO] Enter certifications info.

[INFO] Enter sdk path:

# 提示输入 SDK 目录，回车确认
e.g:[ /root/webank/deploy/deploy/fiscobcos/nodes/127.0.0.1/sdk ]:

.......
```

脚本会自动检测输入的 SDK 目录中是否存在 `ca.crt`，`node.crt`，`node.key` 文件。



#### 国密 Trustoracle（SM2）

提示输入 `gm` SDK 目录（目录需要存在对应链的 `gmca.crt`，`gmensdk.crt`，`gmensdk.key`，`gmsdk.crt`，`gmsdk.key` 文件），输入目录后，按 **回车** 确认：

```Bash
============================================================================================
[INFO] Deploy services ...
[INFO] Enter certifications info.

[INFO] Enter sdk path:

# 提示输入 gm SDK 目录，回车确认
e.g:[ /root/webank/deploy/deploy/fiscobcos/nodes/127.0.0.1/sdk/gm ]:

.......
```

脚本会自动检测输入的 `gm` SDK 目录中，是否存在 `gmca.crt`，`gmensdk.crt`，`gmensdk.key`，`gmsdk.crt`，`gmsdk.key` 文件。

### 配置 MySQL
如果未使用 `-m` 参数，会提示输入 MySQL 的连接信息。直接 **回车** 使用默认值：

```Bash
.......
[INFO] User external MySQL.

Enter MySQL IP, default: 127.0.0.1 ? 127.0.0.1

Enter MySQL port, default: 3306 ?

Enter MySQL user, default: trustoracle ?

Enter MySQL password, default: defaultPassword ? user

.......
```

在 MySQL 中创建一个 `trustoracle` 数据库，供 Trustoracle-Service 服务使用。

```SQL
# 连接 MySQL
mysql -u root -p 

# 创建 trustoracle 数据库
CREATE DATABASE `trustoracle` /*!40100 DEFAULT CHARACTER SET utf8mb4 */;
```

### 配置节点连接

修改 FISCO-BCOS 节点连接信息，编辑 `trustoracle/docker-compose.yml` 文件：

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

### 多链配置
使用独立部署的 Trustoracle 服务，支持同时连接多链，以及一条链的多个群组。

#### 挂载证书
在添加多链时，需要先将新链的证书目录挂载到 Docker 容器中。

在部署时生成的挂载文件，根据加密方式不同，生成的 Docker Compose 文件不同：

```Bash
# 非国密 Trustoracle（ECDSA）
trustoracle/deploy/docker-compose-ecdsa.yml

# 国密 Trustoracle（SM2）
trustoracle/deploy/docker-compose-sm2.yml
```
   
修改对应的 `trustoracle/deploy/docker-compose-xxxx.yml` 文件

```yml
version: '3.7'
services:
  trustoracle-server:
    volumes:
      .......
      .......
      #- /sdk_path_of_chain_2/:/dist/conf/cert/2
```

在 `volumes` 配置中添加一行。

冒号 `:` 前表示新链的证书目录。  

冒号 `:` 后表示 **容器中的证书路径**，Trustoracle-Service 服务启动时，会从容器中的此目录加载证书。

#### 修改连接配置

在部署时生成的连接配置文件，根据加密方式不同，生成的 **连接配置** 文件不同：

```Bash
# 非国密 Trustoracle（ECDSA）
trustoracle/deploy/trustoracle-ecdsa.yml

# 国密 Trustoracle（SM2）
trustoracle/deploy/trustoracle-sm2.yml
```
   
修改对应的 `trustoracle/deploy/trustoracle-xxxx.yml` 文件。

**注意：** 在添加新链的证书配置时，`classpath` 冒号 `:` 的路径是 **容器中的证书路径** 相对 `/dist/conf` 的相对路径。

```yml
# 配置连接
group-channel-connections-configs:
  configs:
    ........
    ........
   ## 第二条链的连接信息，证书，群组列表以及对应的 IP:Port
    - chainId: 2
      caCert: classpath:cert/2/ca.crt
      sslCert: classpath:cert/2/node.crt
      sslKey: classpath:cert/2/node.key
      all-channel-connections:
        - group-id: 1
          connections-str:
            - 127.0.0.1:20200

# 启用新链和群组
event:
  eventRegisters:
   ........
   ........
   - {chainId: 2, group: 1}
   #- {chainId: 2, group: 2}
```

详细配置修改，请参考：[修改配置](../Trustoracle-Service/deploy.html#modify_service_config)

```eval_rst
.. admonition:: 提示

    - `trustoracle.yml` 配置文件中的 `${TRUSTORACLE_SERVICE_PORT:5021}` 表示 Trustoracle-Service 在启动时，会优先从系统环境变量中读取 `TRUSTORACLE_SERVICE_PORT` 的值。如果 `TRUSTORACLE_SERVICE_PORT` 环境变量 **没有设置** 或者 **值为空**，则使用默认值 `5021`。
```


## 服务启停
如果一键部署脚本 `deploy_all.sh` 执行成功后显示 `Deploy Trustoracle service SUCCESS!!` ，表示部署成功。

* 使用 `bash start.sh` 启动 Trustoracle 服务。

* 使用 `bash stop.sh`  停止服务。

在启动时，脚本会依次启动服务，并检测服务启动结果。

如果提示 `Trustoracle service start up SUCCESS !!`，表示 Trustoracle 服务启动成功。

如果启动失败，根据命令行的提示，检查启动失败服务的日志。关于查看服务的日志，请参考：[日志查看](./appendix.html#check_log)

## 访问服务
    
Trustoracle 服务启动成功后，打开浏览器，输入 `http://[IP]:5020`，比如：`http://127.0.0.1:5020`，访问 Trustoracle-Web 服务，请参考：[Trustoracle-Web 服务介绍](../Trustoracle-Web/outline.html)


```eval_rst
.. admonition:: 提示

     - 注意替换服务器的 `IP` 地址
```

关于 Trustoracle 服务的 **开发教程**，请参考：[Trustoracle 开发教程](../develop/quick-start.html)