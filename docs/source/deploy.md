
# 编译和安装部署

## 安装介绍

Truora-Service本身是个典型的SpringBoot工程，只要熟悉Java以及相关的知识，则可以按照一般Java项目的开发部署模式，完成全流程的操作。

**本技术文档主要适用于 FISCO BCOS 3.1+**

如面向FISCO BCOS2.6.x+，切换到文档连接[v2stable](https://truora.readthedocs.io/zh_CN/v2stable/)	 

**分支版本说明参见 [连接](version.md)**

**当前版本聚焦于代码级的开发编译部署。未提供docker/web等工具，如有需要可自行开发适配。**

## 前置依赖

在使用本组件前，请确认系统环境已安装相关依赖软件，清单如下：

| 依赖软件 | 说明 |
| --- | --- | 
| FISCO-BCOS | >= 2.6.0 or >=3.1.0 | 
| MySQL | >= mysql-community-server[5.7] | 
| Java | JDK[1.8] | 
| Git | 下载的源码使用 Git | 

请参考：[附录](../appendix.md) 检查系统是否已经安装相关依赖软件。

* 运行前应已经安装FISCO BCOS的底层并把链运行起来。FISCO BCOS底层的安装部署参见其[操作文档](https://fisco-bcos-doc.readthedocs.io/zh_CN/latest/)

* 数据库应事先安装。可采用Mysql/MariaDB，首先要先手工建库`truora`，并保证`application.yml`里的数据库连接配置正确，以连接到数据库。建表脚本为项目文件路径里的

```dbscripts/V2022.10__v1.0.0_init_table.sql```

*WeBASE等中间件平台，按需自行安装。参见[操作文档](https://webasedoc.readthedocs.io/zh_CN/latest/)


## 拉取代码


github:
```
git clone https://github.com/WeBankBlockchain/Truora-Service.git
```
gitee:
```
git clone https://gitee.com/WeBankBlockchain/Truora-Service.git
```

***
注意：

如区块链底层平台为FISCO BCOS 3.1.0+,使用Truora-Service的master分支。3.0.0版本底层未支持。

如区块链底层平台为FISCO BCOS 2.6.0+(且低于3.0.0)，则使用Truora-Service的v2stabe分支。低于2.6.0版本底层未支持。
***





## gradle编译

方式一：如果服务器已安装Gradle，且版本为 Gradle-5.60 +

```shell
gradle build -x test
```

方式二：如果服务器未安装 Gradle，或者版本低于 Gradle-4.10，使用 gradlew 编译

```shell
chmod +x ./gradlew && ./gradlew build -x test
```

构建完成后，会在根目录 Truora-Service 下生成已编译的代码目录 dist。



## 获取证书
FISCO-BCOS 链有两种类型： **非国密（ECDSA）** 和 **国密（SM2）** 。


```eval_rst
.. important::

    - 不同的连接方式，需要拷贝的证书不同
    - 使用 **非国密** 的方式连接节点，需要拷贝 `sdk/` 目录下 `ca.crt`、`node.crt` 和 `node.key` 文件
    - 使用 **国密** 的方式连接节点，需要拷贝 `sdk/gm` 目录下 `gm` 开头的所有文件
```

将链证书复制到编译后的dist/conf/sdk目录下。



## 配置数据库
修改配置 `conf/application.yml` 文件


* 修改数据库 `IP` 地址，用户名和密码。 
   
```yaml
 datasource:
    driver-class-name: com.mysql.cj.jdbc.Driver
    url: jdbc:mysql://127.0.0.1:3306/truora?serverTimezone=GMT%2B8&useUnicode=true&characterEncoding=utf-8&zeroDateTimeBehavior=convertToNull&useSSL=false
    username: "defaultAccount"
    password: "defaultPassword"
```  
*数据库应事先安装。可采用Mysql/MariaDB，首先要先手工建库truora，并保证application.yml里的数据库连接配置正确，以连接到数据库。建表脚本为项目文件路径里的

```dbscripts/V2022.10__v1.0.0_init_table.sql```



## 配置连接


Truora-Service 支持同时连接多条链，以及连接同一条链中的多个群组。


* 如果连接FISCO BCOS 2.x版本的底层，配置`application-fiscobcos2.yml`。可参见文档的stable分支版本里的详细说明。本文档注重讲解面向FISCO  BCOS 3.x版本的配置。

* 如果连接FISCO BCOS 3.x版本的底层，配置`application-fiscobcos3.yml`，配置链的连接参数以及`bcos3sdk_config.toml`，如果是连接多条链，则应有多个不同的`bcos3sdk_config_[链id].toml`文件。

* application.yml里的spring.profiles段，预置了几种配置组合模式，名字配置在这里的，相应的spring配置文件(如application-fiscobcos3.yml）才会生效。

* 如:实例仅面向FISCO BCOS3.1.x+,则只需要激活fiscobcos3的profile即可。面向fiscobcos2同理

* 名为dapps的profile对应一些示例。参见[接口里的示例段](https://truora.readthedocs.io/zh_CN/v3dev/Truora-Service/interface.html#dapp)

```
spring:
  profiles:
	include: dapps,fiscobcos3
	#include: dapps,fiscobcos2,fiscobcos3
	#include: fiscobcos2
	#include: fiscobcos3
```

配置文件有模板和注释，可参照修改。注意证书路径等细节要和实际位置匹配。如连接多条链，则证书文件的名字应有不同，且在对应的`cos3sdk_config_[链id].toml`里一一对应。

举例：如连接FISCO BCOS 3.x的链，链id为`chain0`，则

1） 将conf目录下的`bcos3sdk_config-template.toml`复制一份到`bcos3sdk_config_chain0.toml`

2） 将链的证书复制到conf/sdk目录下，为每个证书改名为 chain0_[原文件名]，如chain0_ca.crt,chain0_sdk.crt,chain0_sdk.key....，国密证书同理，总之是保证文件名不重复

3） 将证书名一一配置到`bcos3sdk_config_chain0.toml`里，确保一一对应，如
```
[cryptoMaterial]

useSMCrypto = "false" # 注意，这一个参数按实际是否适用国密来配置，true表示连接的是国密链

caCert = "sdk/chain0_ca.crt"     

sslCert = "sdk/chain0_sdk.crt"   

sslKey = "sdk/chain0_sdk.key" 
```


**虽然项目支持一个Truora-Service连接多条链，但从工程实操上讲，不建议这么运行，因为这样在配置复杂度，可用性等方面都会带来一些成本**

**建议是每个Truora-Service实例连接一条底层链，清晰解耦，以便维护。**



## 服务启停

返回到 `dist` 目录执行：

* 启动

```Bash
# 采用非国密连接（ECDSA）
bash start.sh

```

* 停止
```shell
bash stop.sh
```

* 检查

```shell
bash status.sh
```
**备注**：服务进程起来后，需通过日志确认是否正常启动，出现以下内容表示正常；如果服务出现异常，确认修改配置后，重启提示服务进程在运行，则先执行 `stop.sh`，再执行 `start.sh`。

```Bash
Application() - main run success...
```



## 查看日志

在 dist 目录查看：

```Bash
# 前置服务日志：
tail -f log/truora-service.log
```
