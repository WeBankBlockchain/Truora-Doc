# 附录

<span id="pull_deploy_code"/>

## 部署工具源码

拉取部署工具远吗，需要使用 Git 工具。

```Bash
# 初始化本地目录
git init TrustOracle-Service && cd TrustOracle-Service;

# 设置检出子目录 docker/deploy
git config core.sparsecheckout true ;
echo "docker/deploy" >> .git/info/sparse-checkout ;

# 设置仓库地址，拉取部署工具
git remote add origin "https://github.com/WeBankBlockchain/TrustOracle-Service.git";
git fetch --depth 1 && git checkout master

# 进入部署脚本目录
cd docker/deploy
```

<span id="check_log"/>

## 查看日志
### WeBASE-Front
WeBASE-Front 的日志位于相对目录 `webase/log/` 中。

查看 WeBASE-Front 日志：

```Bash
# 服务启动日志
cat webase/log/WeBASE-Front.log

# 服务错误日志
cat webase/log/WeBASE-Front-error.log
```
### MySQL
使用命令查看 MySQL 日志：

```Bash
docker logs trustoracle-mysql
```
### FISCO-BCOS
FISCO-BCOS 的日志位于相对目录 `fiscobcos/nodes/127.0.0.1/node0/log` 中，注意替换 `node0` 目录。

查看 FISCO-BCOS 节点日志：

```Bash
# 服务启动日志
#
# 注意替换 node0 目录
cat fiscobcos/nodes/127.0.0.1/node0/log/log_xxxxxxxxx.xx.log
```

### TrustOracle-Service
TrustOracle-Service 的日志位于相对目录 `trustoracle/log/server/` 中。

查看 TrustOracle-Service 日志：

```Bash
# 服务启动日志
cat trustoracle/log/server/Oracle-Service.log

# 服务错误日志
cat trustoracle/log/server/Oracle-Service-error.log
```

### TrustOracle-Web( Nginx )
TrustOracle-Web 部署在一个 Nginx 的 Docker 容器中。

查看 Nginx 的日志：

```
docker logs trustoracle-web
```

<span id="shell_script_param"/>

## 脚本参数
一键部署脚本 `deploy_all.sh` 和独立部署脚本 `deploy_single.sh` 脚本的**参数相同**。

```eval_rst
.. admonition:: 提示

     	- 本质上，`deploy_all.sh` 和 `deploy_single.sh` 都是通过调用 `util/deploy_util.sh` 脚本来完成部署。
     	- 在调用 `util/deploy_util.sh` 脚本时，传递不同的参数来完成部署。
```

```Bash
# 查看部署脚本参数
bash util/deploy_util.sh -h

# 参数明细
Usage:
    deploy_util.sh [-k] [-m] [-w] [f] [-M 3306] [-W 5002] [-B 5020] [-S 5021] [-d] [-g] [-i fiscoorg] [-h]
    -k        Pull images from Docker hub.

    -m        Deploy a MySQL instance with Docker, default use an external MySQL service.
    -w        Deploy a WeBASE-Front service, default no.
    -f        Deploy a 4 nodes FISCO-BCOS service, default no.

    -M        Listen port of MySQL, default 3306.
    -W        Listen port of WeBASE-Front, default 5002.
    -B        Listen port of TrustOracle-Web, default 5020.
    -S        Listen port of TrustOracle-Service, default 5021.

    -d        Install dependencies during deployment, default no.
    -g        Use guomi, default no.

    -i        Organization of docker images, default fiscoorg.

    -h        Show help info.
```


| 参数 | 说明  | 参数是否需要值  |  备注 |
|---|---|:---:|:---|
| -k  |  从 Docker Hub 拉取镜像  | 不 | 不使用该参数时，默认从 CDN 拉取|
| -m  |  部署 MySQL  | 不 |  不使用该参数时，**不部署** MySQL。<br />需要在部署时输入 MySQL 信息  |
| -w  |  部署 WeBASE-Front    | 不 |  不使用该参数时，**不部署** WeBASE-Front  |
| -f  |  部署 FISCO-BCOS | 不 |  不使用该参数时，**不部署** FISCO-BCOS  |
| -M  |  设置 MySQL 端口 | 需要 |  不使用该参数时，默认：`3306` |
| -W  |  设置 WeBASE-Front  端口 | 需要 | 不使用该参数时，默认：`5002` |
| -B  |  设置 TrustOracle-Web 端口 | 需要 | 不使用该参数时，默认：`5020` |
| -S  |  设置 TrustOracle-Service 端口 | 需要 | 不使用该参数时，默认：`5021` |
| -d  | 安装系统依赖 | 不  |    不使用该参数时，则不安装 |
| -g  |  启用**国密**   | 不 |  不使用该参数时，使用 `ECDSA`  |
| -i  | 指定 Docker 镜像的组织名称 |需要  |  **仅开发 TrustOracle 服务时使用** <br /> 默认 `fiscoorg`  |
| -h  | 显示帮助文档 | 不  | 无  |    

```eval_rst
.. admonition:: 提示

     - `-d` 参数安装的系统依赖包括：`OpenSSL`，`curl`，`wget`，`Docker`，`Docker Compose`。
     - `-i` 参数指定 Docker 镜像的组织名称。例如指定为: `testorg`，Docker 的拉取镜像命令就变成：`docker pull testorg/trustoracle-service:${version}` 
```


<span id="cdn_instruction" />

## CDN 说明
由于 Docker 的镜像的大小一般偏大，访问国外时网络很不稳定，在下载大文件或者工具时，往往下载不下来。所以，针对部分工具，提供了 CDN 的下载地址。

<span id="cdn_deploy_tool" />

```eval_rst
.. important::

    - 部署工具包是 **`zip`** 文件
    - Docker 镜像是 **`tar`** 文件
```

### 部署工具包
部署工具包用来部署 TrustOracle 服务，同时支持：**一键部署** 和 **独立部署** 两种场景。


```Bash
# 下载指定版本时替换 {VERSION} 版本号
https://osp-1257653870.cos.ap-guangzhou.myqcloud.com/WeBankBlockchain/TrustOracle/deploy/docker-deploy-{VERSION}.zip
```

版本号，从 [https://github.com/WeBankBlockchain/TrustOracle-Service/releases](https://github.com/WeBankBlockchain/TrustOracle-Service/releases) 获取。

比如，下载 v1.0.0 版：

```Bash
https://osp-1257653870.cos.ap-guangzhou.myqcloud.com/WeBankBlockchain/TrustOracle/deploy/docker-deploy-v1.0.0.zip
```

### Docker 镜像

Docker 镜像下载成功后，使用命令进行解压：

```Bash
# 解压镜像包
docker load -i xxxx.tar

# 查看镜像版本
docker images -a
```

#### TrustOracle

TrustOracle 镜像包含两个服务的镜像：TrustOracle-Service 和 TrustOracle-Web。

```Bash
## 下载指定版本时替换 {VERSION} 版本号

# 下载 TrustOracle-Service 
https://osp-1257653870.cos.ap-guangzhou.myqcloud.com/WeBankBlockchain/TrustOracle/docker/trustoracle/trustoracle-service-{VERSION}.tar

# 下载 TrustOracle-Web 
https://osp-1257653870.cos.ap-guangzhou.myqcloud.com/WeBankBlockchain/TrustOracle/docker/trustoracle/trustoracle-web-{VERSION}.tar
```

版本号，从 [https://github.com/WeBankBlockchain/TrustOracle-Service/releases](https://github.com/WeBankBlockchain/TrustOracle-Service/releases) 获取。

比如，下载 v1.0.0 版：

```Bash
# 下载 TrustOracle-Service 
https://osp-1257653870.cos.ap-guangzhou.myqcloud.com/WeBankBlockchain/TrustOracle/docker/trustoracle/trustoracle-service-v1.0.0.tar

# 下载 TrustOracle-Web 
https://osp-1257653870.cos.ap-guangzhou.myqcloud.com/WeBankBlockchain/TrustOracle/docker/trustoracle/trustoracle-web-v1.0.0.tar
```


#### FISCO-BCOS
FISCO-BCOS 镜像是指 FISCO-BCOS 底层节点镜像，当前仅包含 `v2.6.0` 版本

```Bash
# 下载 FISCO-BCOS v2.6.0 镜像
https://osp-1257653870.cos.ap-guangzhou.myqcloud.com/WeBankBlockchain/TrustOracle/docker/FISCO-BCOS/fiscobcos-v2.6.0.tar
```


#### WeBASE-Front
WeBASE-Front 镜像是指 WeBASE 中间件中的子服务 WeBASE-Front 的镜像，当前仅包含 `v1.4.2` 版本

```Bash
# 下载 WeBASE-Front v1.4.2 镜像
https://osp-1257653870.cos.ap-guangzhou.myqcloud.com/WeBankBlockchain/TrustOracle/docker/WeBASE/webase-front-v1.4.2.tar
```
#### MySQL
MySQL 镜像是 Docker Hub 仓库中的官方 MySQL 镜像，当前仅包含 `5.7` 版本

```Bash
# 下载 MySQL 5.7 镜像
https://osp-1257653870.cos.ap-guangzhou.myqcloud.com/WeBankBlockchain/TrustOracle/docker/official/mysql-5.7.tar
```

#### Docker-Compose

Docker-Compose 镜像是 Docker Hub 仓库中的官方 Docker Compose 镜像，当前仅包含 `1.27.4` 版本

```Bash
# 下载 Docker Compose 1.27.4 镜像
https://osp-1257653870.cos.ap-guangzhou.myqcloud.com/WeBankBlockchain/TrustOracle/docker/official/docker-compose-1.27.4.tar
```