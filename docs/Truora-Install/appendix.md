# 附录

<span id="pull_deploy_code"/>

## 部署工具源码

拉取部署工具远吗，需要使用 Git 工具。

```Bash
# 初始化本地目录
git init Truora-Service && cd Truora-Service;

# 设置检出子目录 docker/deploy
git config core.sparsecheckout true ;
echo "docker/deploy" >> .git/info/sparse-checkout ;

# 设置仓库地址，拉取部署工具
git remote add origin "https://github.com/WeBankBlockchain/Truora-Service.git";
git fetch --depth 1 && git checkout main

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
docker logs truora-mysql
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

### Truora-Service
Truora-Service 的日志位于相对目录 `truora/deploy/log/server/` 中。

查看 Truora-Service 日志：

```Bash
# 服务启动日志
cat truora/deploy/log/server/Oracle-Service.log

# 服务错误日志
cat truora/deploy/log/server/Oracle-Service-error.log
```

### Truora-Web( Nginx )
Truora-Web 部署在一个 Nginx 的 Docker 容器中。

查看 Nginx 的日志：

```
docker logs truora-web
```

Truora-Web 的访问日志位于相对目录 `truora/deploy/log/nginx/oracle-access.log` 中。
Truora-Web 的错误日志位于相对目录 `truora/deploy/log/nginx/oracle-error.log` 中。

<span id="shell_script_param"/>

## 脚本参数
**一键部署** 脚本 `deploy_all.sh` 和 **独立部署** 脚本 `deploy_single.sh` 本质上都是通过调用 `util/deploy_util.sh` 脚本来完成部署。

```eval_rst
.. admonition:: 区别

    - `deploy_all.sh` 脚本执行时，自带 `-m`，`-w`，`-f` 参数。
```


```Bash
# 查看部署脚本参数
bash util/deploy_util.sh -h

# 参数明细
Usage:
    deploy_util.sh [-k] [-m] [-w] [-f] [-M 3306] [-W 5002] [-B 5020] [-S 5021] [-d] [-g] [-i fiscoorg] [-t] [-p] [-D] [-h]
    -k        Pull images from Docker hub.

    -m        Deploy a MySQL instance with Docker.
    -w        Deploy a WeBASE-Front service.
    -f        Deploy a 4 nodes FISCO-BCOS service.

    -M        Listen port of MySQL, default 3306.
    -W        Listen port of WeBASE-Front, default 5002.
    -B        Listen port of Truora-Web, default 5020.
    -S        Listen port of Truora-Service, default 5021.

    -d        Install dependencies during deployment.
    -g        Use guomi.

    -i        Organization of docker images, default fiscoorg.
    -t        Use [dev] tag for images of Truora-Service and Truora-Web. Only for test.
    -p        Pull [dev] latest for images of Truora-Service and Truora-Web. Only works when option [-t] is on.
    -D        Set log level of Truora to [ DEBUG ], default [ INFO ].

    -h        Show help info.
```


| 参数 | 说明  | 参数是否需要值  |  备注 |
|---|---|:---:|:---|
| -k  |  从 Docker Hub 拉取镜像  | 不 | 不使用该参数时，默认从 **CDN** 拉取|
| -m  |  部署 MySQL  | 不 |  不使用该参数时，**不部署** MySQL。<br />需要在部署时输入 MySQL 信息  |
| -w  |  部署 WeBASE-Front    | 不 |  不使用该参数时，**不部署** WeBASE-Front  |
| -f  |  部署 FISCO-BCOS | 不 |  不使用该参数时，**不部署** FISCO-BCOS  |
| -M  |  设置 MySQL 端口 | 需要 |  不使用该参数时，默认：`3306` |
| -W  |  设置 WeBASE-Front  端口 | 需要 | 不使用该参数时，默认：`5002` |
| -B  |  设置 Truora-Web 端口 | 需要 | 不使用该参数时，默认：`5020` |
| -S  |  设置 Truora-Service 端口 | 需要 | 不使用该参数时，默认：`5021` |
| -d  | 安装系统依赖 | 不  |    不使用该参数时，则不安装 |
| -g  |  启用**国密**   | 不 |  不使用该参数时，使用 `ECDSA`  |
| -i  | 指定 Docker 镜像的组织名称 |需要  |  **仅开发 Truora 服务时使用** <br /> 默认 `fiscoorg`  |
| -t  | truora-web 和 truora-service <br /> 镜像使用 `dev` 版本，<br />**仅测试使用**|不  | 不使用该参数时，<br />Truora-Web 和 Truora-Service <br/>**默认使用 Release 版本（v1.x.x）** |
| -p  | 拉取 truora-web 和 truora-service <br /> 镜像 `dev` 版本的最新版，<br />**仅开启 `-t` 参数后有效** |不  |  不使用该参数时，默认不拉取 |
| -D  | 设置 Truora-Service 服务 <br />日志级别为 `DEBUG` |不  |  不使用该参数时，默认 `INFO` |
| -h  | 显示帮助文档 | 不  | 无  |    

```eval_rst
.. admonition:: 提示

     - `-d` 参数安装的系统依赖包括：`OpenSSL`，`curl`，`wget`，`Docker`，`Docker Compose`
     - `-i` 参数指定 Docker 镜像的组织名称。例如指定为: `testorg`，Docker 的拉取镜像命令就变成：`docker pull testorg/truora-service:${version}`
     - `-p` 参数只有在启用 `-t` 参数后才有效
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
部署工具包用来部署 Truora 服务，同时支持：**一键部署** 和 **独立部署** 两种场景。


```Bash
# 下载指定版本时替换 {VERSION} 版本号
wget "https://osp-1257653870.cos.ap-guangzhou.myqcloud.com/WeBankBlockchain/Truora/deploy/docker-deploy-{VERSION}.zip" -O docker-deploy.zip
# 解压部署包
unzip docker-deploy.zip
```

版本号，从 [https://github.com/WeBankBlockchain/Truora-Service/releases](https://github.com/WeBankBlockchain/Truora-Service/releases) 获取。

比如，下载 v1.0.0 版：

```Bash
wget "https://osp-1257653870.cos.ap-guangzhou.myqcloud.com/WeBankBlockchain/Truora/deploy/docker-deploy-v1.0.0.zip" -O docker-deploy.zip
# 解压部署包
unzip docker-deploy.zip
```

### Docker 镜像

Docker 镜像下载成功后，使用命令进行解压：

```Bash
# 解压镜像包
docker load -i xxxx.tar

# 查看镜像版本
docker images -a
```

#### Truora

Truora 镜像包含两个服务的镜像：Truora-Service 和 Truora-Web。

```Bash
## 下载指定版本时替换 {VERSION} 版本号

# 下载 Truora-Service
wget "https://osp-1257653870.cos.ap-guangzhou.myqcloud.com/WeBankBlockchain/Truora/docker/truora/truora-service-{VERSION}.tar" -O truora-service.tar
# 加载镜像
docker load -i truora-service.tar

# 下载 Truora-Web
wget "https://osp-1257653870.cos.ap-guangzhou.myqcloud.com/WeBankBlockchain/Truora/docker/truora/truora-web-{VERSION}.tar" -O truora-web.tar
# 加载镜像
docker load -i truora-web.tar
```

版本号，从 [https://github.com/WeBankBlockchain/Truora-Service/releases](https://github.com/WeBankBlockchain/Truora-Service/releases) 获取。

比如，下载 v1.0.0 版：

```Bash
# 下载 Truora-Service
wget "https://osp-1257653870.cos.ap-guangzhou.myqcloud.com/WeBankBlockchain/Truora/docker/truora/truora-service-v1.0.0.tar" -O truora-service.tar
# 加载镜像
docker load -i truora-service.tar


# 下载 Truora-Web
wget "https://osp-1257653870.cos.ap-guangzhou.myqcloud.com/WeBankBlockchain/Truora/docker/truora/truora-web-v1.0.0.tar" -O truora-web.tar
# 加载镜像
docker load -i truora-web.tar
```


#### FISCO-BCOS
FISCO-BCOS 镜像是指 FISCO-BCOS 底层节点镜像，当前仅包含 `v2.6.0` 版本

```Bash
# 下载 FISCO-BCOS v2.6.0 镜像
wget "https://osp-1257653870.cos.ap-guangzhou.myqcloud.com/WeBankBlockchain/Truora/docker/FISCO-BCOS/fiscobcos-v2.6.0.tar" -O fiscobcos.tar
# 加载镜像
docker load -i fiscobcos.tar
```


#### WeBASE-Front
WeBASE-Front 镜像是指 WeBASE 中间件中的子服务 WeBASE-Front 的镜像，当前仅包含 `v1.4.2` 版本

```Bash
# 下载 WeBASE-Front v1.4.2 镜像
wget "https://osp-1257653870.cos.ap-guangzhou.myqcloud.com/WeBankBlockchain/Truora/docker/WeBASE/webase-front-v1.4.2.tar" -O webase-front.tar
# 加载镜像
docker load -i webase-front.tar
```
#### MySQL
MySQL 镜像是 Docker Hub 仓库中的官方 MySQL 镜像，当前仅包含 `5.7` 版本

```Bash
# 下载 MySQL 5.7 镜像
wget "https://osp-1257653870.cos.ap-guangzhou.myqcloud.com/WeBankBlockchain/Truora/docker/official/mysql-5.7.tar" -O mysql.tar
# 加载镜像
docker load -i mysql.tar
```

#### Docker-Compose

Docker-Compose 镜像是 Docker Hub 仓库中的官方 Docker Compose 镜像，当前仅包含 `1.27.4` 版本

```Bash
# 下载 Docker Compose 1.27.4 镜像
wget "https://osp-1257653870.cos.ap-guangzhou.myqcloud.com/WeBankBlockchain/Truora/docker/official/docker-compose-1.27.4.tar" -O docker-compose.tar
# 加载镜像
docker load -i docker-compose.tar
```