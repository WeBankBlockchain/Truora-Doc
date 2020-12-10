# Docker 一键安装

```eval_rst
.. admonition:: 提示

     - 使用一键安装 TrustOracle 服务时，**仅支持 Linux 操作系统！！**
```

## 安装介绍
TrustOracle 服务支持使用 `Docker` + `Docker-Compose` 进行部署。

同时，在部署 TrustOracle 服务时，会同时部署一个 WeBASE-Front（Solidity 合约的开发和调试环境）服务，作为 Oracle 的开发和调试环境。

TrustOracle 使用 Docker 一键安装后的结构如下：

![TrustOracle-Docker-deploy](../../../images/TrustOracle-Docker-Deploy.png)


* **WeBASE-Front**

    WeBASE-Front 是 [WeBASE](https://webasedoc.readthedocs.io/zh_CN/latest/docs/WeBASE-Front/README.html) 的一个子系统服务，针对 FISCO-BCOS 区块链服务提供 Solidity 合约的可视化开发，编译，部署和调试功能。
    在进行 TrustOracle 相关业务的合约开发和调试时，可以使用 WeBASE-Front 中的合约 IDE，方便合约的开发和调试，大大提高开发效率。

* **TrustOracle-Web**
    
    TrustOracle-Web 是 TrustOracle 服务的前端 Web，主要包含四个功能：
    * 分页查询 Oracle 请求历史记录
    * 单个查询 Oracle 请求明细（状态，响应结果，错误信息等）
    * TrustOracle-Service 内置合约地址查询
    * 查询所有 TrustOracle-Service 服务列表

* **TrustOracle-Service**

    TrustOracle-Service 是 TrustOracle 的服务端：
    * 监听 FISCO-BCOS 链上 TrustOracle 合约的事件
    * 接收链上事件，调用 Http API 接口或 VRF 随机数生成库，获取结果
    * 结果上链，供用户合约查询
    
    具体，关于 TrustOracle 原理，请参考：[Oracle-Service 原理介绍](../README.md#原理简介)


## 前置要求
### 系统要求

| 操作系统         |  版本最低要求     |  
| ------------- |:-------|
| CentOS/RHEL |7.3（kernel >= 3.10.0-514）|
|Debian|Stretch 9  |
|Ubuntu|Xenial 16.04 (LTS)|


### 硬件配置

|配置项      | 推荐配置 |
|----- | -----|
| CPU | 2 核|
| 内存 | 4 GB |
| 存储 | 40 GB |

## 脚本说明
TrustOracle Docker 一键安装工具使用 `deploy.sh` 脚本：

* 自动安装 `OpenSSL`, `curl`, `wget`, `Docker`, `Docker Compose` 等依赖服务
* 调用 FISCO-BCOS 一键部署脚本 `build_chain.sh`，部署 `4` 个区块链底层节点
* 部署 WeBASE-Front 服务
* 部署 WeBASE-Oracle 服务
* 支持国密选项

关于脚本详细参数列表，请参考：[脚本参数](./docker.html#shell_script_param)

## 获取部署脚本
部署脚本的获取方式有两种：
* 从 Release 页面直接下载安装包
* 使用 Git 从仓库拉取

### 下载安装包
在[版本列表中](https://github.com/WeBankBlockchain/TrustOracle-Service/releases) 选择相应版本的 `docker-deploy.zip` 文件下载。

或者直接直接代码，下载最新安装包：

```Bash
## 下载安装包
wget "https://github.com/WeBankBlockchain/TrustOracle-Service/releases/xxx/xxx/docker-deploy.zip"

## 解压安装包
unzip docker-deploy.zip

## 进入安装脚本目录
cd docker-deploy
```

### 仓库拉取
也可以从 线上

```Bash
# 初始化本地目录
git init TrustOracle-Service && cd TrustOracle-Service;

# 设置检出子目录 docker/deploy
git config core.sparsecheckout true ;
echo "docker/deploy" >> .git/info/sparse-checkout ;

# 设置仓库地址，拉取部署工具
git remote add origin "https://github.com/WeBankBlockchain/TrustOracle-Service.git";
git pull origin master;

# 进入安装脚本目录
cd docker/deploy
```

## 部署

进入 `deploy.sh` 脚本所在目录，执行命令：

```Bash
# 自动安装依赖服务，从 CDN 拉取 Docker 镜像
bash deploy.sh -d

# 自动安装依赖服务，从 Docker Hub 官方仓库拉取 Docker 镜像
bash deploy.sh -d -t docker
```

* `-d`：自动安装依赖服务
* `-t`：镜像来源，`docker` 表示从 Docker Hub 官方仓库 `https://hub.docker.com/` 拉取 Docker 镜像。

```eval_rst
.. admonition:: 提示

     - 如果不使用 `-t` 参数或者使用 `-t cdn`，部署工具会从 CDN 拉取 Docker 镜像
     - 由于 Docker Hub 的网络原因，部署脚本拉取镜像的速度较慢，耗时过长，容易拉取镜像失败，推荐 **从 CDN 拉取 Docker 镜像**。
```  

## 服务启停
如果一键脚本 `deploy.sh` 执行成功后显示 `Deploy TrustOracle service SUCCESS!!` ，表示部署成功。

使用 `bash start.sh` 脚本启动 TrustOracle 服务。

// TODO. 脚本自动检测服务启动结果，如果失败，则tail -n  xxx 日志

停止服务使用：`bash stop.sh`

**备注**：服务进程起来后，需通过日志确认是否正常启动，出现以下内容表示正常；如果服务出现异常，确认修改配置后，重启提示服务进程在运行，则先执行 `stop.sh`，再执行 `start.sh`。

```
	Application() - main run success...
```

## 查看日志

在 `log/server` 目录查看：

```
前置服务日志：tail -f log/server/Oracle-Service.log
```

<span id="shell_script_param"/>

## 脚本参数
查看一键脚本 `deploy.sh` 的帮助文档：`bash deploy.sh -h`

```Bash
[root@host]# bash deploy.sh -h

Usage:
    deploy.sh [-g] [-t cdn|docker] [-d] [-w v1.4.2] [-f v2.6.0] [-o v0.5] [-i fiscoorg] [-h]
    -g        Use guomi, default no.
    -t        Where to get docker images, cdn or Docker hub, default cdn.
    -d        Install dependencies during deployment, default no.

    -w        WeBASE-Front version, default v1.4.2
    -f        FISCO-BCOS version, default v2.6.0.
    -o        TrustOracle version, default v0.5.
    -i        Organization of docker images, default fiscoorg.
    -h        Show help info.
```


| 参数 | 说明  | 是否需要参数值  |  备注 |
|---|---|:---:|:---|
| -g  |  启用**国密**   | 不 |  不使用该参数，使用 `ECDSA`  |
| -d  | 安装 `OpenSSL`，`curl`，`wget`，<br />`Docker`，`Docker Compose` | 不  |    不使用该参数，则不安装 |
| -t  |  镜像来源，只有 `cdn` 和 `docker` 两个选项  | 需要 | 不使用该参数，默认：`cdn`  |
| -w  | WeBASE-Front 的 Docker 镜像版本  |需要 | 默认 v1.4.2 |    
| -f  | FISCO-BCOS 的 Docker 镜像版本  |需要  | 默认 v2.6.0  |    
| -o  | Oracle-Service 的 Docker 镜像版本  |需要 |默认 v0.5  |    
| -h  | 显示帮助文档 | 不  | 无  |    
| -i  | 指定 Docker 镜像的组织名称 |需要  |  **仅开发 TrustOracle 服务时使用** <br /> 默认 `fiscoorg`  |

```eval_rst
.. admonition:: 提示

     - `-i` 参数指定 Docker 镜像的组织名称。例如指定为: `testorg`，Docker 的拉取镜像命令就变成：`docker pull testorg/trustoracle-service:${version}` 
```