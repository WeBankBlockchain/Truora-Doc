# 附录

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
    deploy_util.sh [-t cdn|docker] [-m] [-w] [f] [-M 3306] [-W 5002] [-B 5000] [-S 5012] [-d] [-g] [-i fiscoorg] [-h]
    -t        Source of docker images, cdn or Docker hub, default cdn.

    -m        Deploy a MySQL instance with Docker, default use an external MySQL service.
    -w        Deploy a WeBASE-Front service, default no.
    -f        Deploy a 4 nodes FISCO-BCOS service, default no.

    -M        Listen port of MySQL, default 3306.
    -W        Listen port of WeBASE-Front, default 5002.
    -B        Listen port of TrustOracle-Web, default 5000.
    -S        Listen port of TrustOracle-Service, default 5012.

    -d        Install dependencies during deployment, default no.
    -g        Use guomi, default no.

    -i        Organization of docker images, default fiscoorg.

    -h        Show help info.
```


| 参数 | 说明  | 参数是否需要值  |  备注 |
|---|---|:---:|:---|
| -t  |  镜像来源，只有 `cdn` 和 `docker` 两个选项  | 需要 | 不使用该参数时，默认：`cdn`  |
| -m  |  部署 MySQL  | 不 |  不使用该参数时，**不部署** MySQL。<br />需要在部署时输入 MySQL 信息  |
| -w  |  部署 WeBASE-Front    | 不 |  不使用该参数时，**不部署** WeBASE-Front  |
| -f  |  部署 FISCO-BCOS | 不 |  不使用该参数时，**不部署** FISCO-BCOS  |
| -M  |  设置 MySQL 端口 | 需要 |  不使用该参数时，默认：`3306` |
| -W  |  设置 WeBASE-Front  端口 | 需要 | 不使用该参数时，默认：`5002` |
| -B  |  设置 TrustOracle-Web 端口 | 需要 | 不使用该参数时，默认：`5000` |
| -S  |  设置 TrustOracle-Service 端口 | 需要 | 不使用该参数时，默认：`5012` |
| -d  | 安装系统依赖 | 不  |    不使用该参数时，则不安装 |
| -g  |  启用**国密**   | 不 |  不使用该参数时，使用 `ECDSA`  |
| -i  | 指定 Docker 镜像的组织名称 |需要  |  **仅开发 TrustOracle 服务时使用** <br /> 默认 `fiscoorg`  |
| -h  | 显示帮助文档 | 不  | 无  |    

```eval_rst
.. admonition:: 提示

     - `-d` 参数安装的系统依赖包括：`OpenSSL`，`curl`，`wget`，`Docker`，`Docker Compose`。
     - `-i` 参数指定 Docker 镜像的组织名称。例如指定为: `testorg`，Docker 的拉取镜像命令就变成：`docker pull testorg/trustoracle-service:${version}` 
```