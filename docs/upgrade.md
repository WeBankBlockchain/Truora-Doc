# 版本升级

**此文档仅适用于v2stable分支，不包括适配FISCO BCOS3.1.x+的分支**

参见[版本分支说明](https://truora.readthedocs.io/zh_CN/v2stable/docs/introduction.html#id1)

Truora 在进行升级操作时，不同的部署方式，有不同的操作流程。



Truora 的升级的主要流程如下：
1. 数据库修改
2. Truora 版本更新
3. 更新 Truora 配置

```eval_rst
.. admonition:: 提示

    - 由于 **一键部署** 的目的主要是体验，开发和调试，所以 **不推荐** 对一键部署的预言机服务进行升级操作，推荐重新部署。
    - 该升级教程，仅适用于采用 **独立部署** 的部署方式部署的 Truora 服务。
```

如果需要重新部署，请参考：[一键部署](./Truora-Install/docker-all.html)

## 数据库

### 获取数据库脚本

在进行数据库升级时，需要优先确定当前的 Truora 版本号，根据版本号来判断需要执行的数据库脚本列表。

比如，当前 Truora 版本号为 `v1.0.0`，需要升级到版本 `v1.1.0`，只需要执行 `V2021.03.08__v1.1.0.sql` 脚本即可。

```eval_rst
.. admonition:: 提示

    - 需要执行的 SQL 是 **当前版本** 和需要升级到的 **目标版本** 之间所有的数据库脚本。
```

```eval_rst
.. important::

    - 执行脚本时，需要要按照 **版本顺序依次** 执行。
```

关于如何获取当前版本，请参考：[获取 Truora 服务版本](./Truora-Service/interface.html#server_version)

获取数据库的脚本地址：

* [gitee 仓库](https://gitee.com/WeBankBlockchain/Truora-Service/tree/main/src/main/resources/db/migration)
* [GitHub 仓库](https://github.com/WeBankBlockchain/Truora-Service/tree/main/src/main/resources/db/migration)

推荐使用 `gitee` 仓库。

### 执行数据库脚本

```eval_rst
.. important::

    - 如果采用 **一键部署** 的方式部署，建议使用新版本的部署脚本，重新部署最新版本的服务。
```

采用独立部署的方式时，Truora 连接的数据库是开发者自行提供的数据库。

在获取数据库的的更新脚本后，需要用户连上 MySQL 数据库，根据版本号对比，依次执行数据库脚本中的 SQL 语句。

下面以从 `v1.0.0` 版本更新到 `v1.1.0` 版本为例：

* 下载数据库脚本

```
## 下载 v1.1.0 数据库更新脚本

# 从 gitee 下载
wget "https://gitee.com/WeBankBlockchain/Truora-Service/raw/main/src/main/resources/db/migration/V2021.03.08__v1.1.0.sql"

# 从 GitHub 下载
wget "https://raw.githubusercontent.com/WeBankBlockchain/Truora-Service/dev/src/main/resources/db/migration/V2021.03.08__v1.1.0.sql"
```

* 连接到 MySQL
 
```Bash
# 连接 MySQL
$ mysql -u root -p 
......

# 登陆成功后，切换到 Truora 数据库
mysql> use truora

# 执行数据库脚本
mysql> source V2021.03.08__v1.1.0.sql

......
Query OK, 0 rows affected (0.07 sec)
Records: 0  Duplicates: 0  Warnings: 0

Query OK, 0 rows affected (0.00 sec)
Rows matched: 0  Changed: 0  Warnings: 0

Query OK, 0 rows affected (0.06 sec)
Records: 0  Duplicates: 0  Warnings: 0

Query OK, 0 rows affected (0.10 sec)
Records: 0  Duplicates: 0  Warnings: 0
......
```

如果都是 `Query OK`，表示数据库脚本执行成功。

```eval_rst
.. admonition:: 提示

    - 如果升级时的版本跨越多个版本，请按照版本号，依次执行数据库脚本。
```

如果使用使用其它的工具，只要能连接到数据，然后依次数据数据库变更脚本即可。

## Truora 服务

在更新完数据库后，需要更新 Truora-Service 和 Truora-Web 的 Docker 镜像版本。


### 修改 Truora 版本
* 从 CDN 获取 Truora 服务镜像，此处以升级到 `v1.1.0` 版本作为例：
    * 关于从 CDN 下载其它版本，请参考：[下载 Truora CDN 镜像](./Truora-Install/appendix.html#truora)

```Bash
# 此处以升级到 v1.1.0 为示范

## 下载 Truora-Service
wget "https://osp-1257653870.cos.ap-guangzhou.myqcloud.com/WeBankBlockchain/Truora/docker/truora/truora-service-v1.1.0.tar" -O truora-service.tar
# 加载镜像
docker load -i truora-service.tar

## 下载 Truora-Web
wget "https://osp-1257653870.cos.ap-guangzhou.myqcloud.com/WeBankBlockchain/Truora/docker/truora/truora-web-v1.1.0.tar" -O truora-web.tar
# 加载镜像
docker load -i truora-web.tar
```


* 打开 Truora 服务的 docker-compose.yml 文件

```Bash
# 进入 Truora 部署工具目录
$ cd /xxx/xxx/deploy

# 编辑 Truora 的 docker-compose.yml 文件
$ vim truora/deploy/docker-compose.yml
```

*  Truora 版本号

更新 docker-compose.yml 文件中下面两行的版本为 `v1.1.0`

```Yaml
......

    image: fiscoorg/truora-web:v1.1.0

......

    image: fiscoorg/truora-service:v1.1.0
    
......
```

### 修改 Truora 配置

* 打开 Truora 配置文件 truora.yml

```Bash
# 进入 Truora 部署工具目录
$ cd /xxx/xxx/deploy

# 编辑 Truora 的 truora.yml 配置文件
$ vim truora/deploy/truora.yml
```

* 关闭 `generate-ddl` 功能

修改 truora.yml 文件中的配置为 `false`


```Yaml
#server config
......

spring:
......
  jpa:
    generate-ddl: false
......
```

* 重启 Truora 服务

```Bash
# 进入 Truora 部署工具目录
$ cd /xxx/xxx/deploy

# 重启
$ bash stop.sh && bash start.sh
=============================================================
Root dir: [/xxx/xxx/deploy]
String Truora..
Creating truora-service ... done
Creating truora-web     ... done
[INFO] Wait for Truora-Service start up on port:[5021]...
[INFO] Truora-Service start success.

[INFO] Wait for Truora-Web start up on port:[5020]...
[INFO] Truora-Web start SUCCESS.

Truora service start up SUCCESS !!
```

看到 `Truora service start up SUCCESS` 提示，表示重启成功，升级完成。
