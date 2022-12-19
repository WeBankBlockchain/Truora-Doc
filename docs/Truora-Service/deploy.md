
# 兼容性说明
* v2stable分支版本所述的truora支持FISCO-BCOS 2.6.0 +
	* Truora-Service 分支[v2stable](https://github.com/WeBankBlockchain/Truora-Service/tree/v2stable)
    * Truora `v1.X.0` 版本支持 [FISCO-BCOS 2.6.0 +](https://github.com/FISCO-BCOS/FISCO-BCOS/releases/tag/v2.6.0) 版本。
	* 一键安装，Truora docker和web相关模块参见文档说明。

* latest/v3dev分支所述的truora支持FISCO-BCOS 3.1.x + 
	* 支持[FISCO-BCOS 3.1及以上](https://github.com/FISCO-BCOS/FISCO-BCOS)
	* 文档连接[Latest/v3dev分支](适配FISCO BCOS 3.x+版本的文档为：[Latest/v3Dev分支](https://truora.readthedocs.io/zh_CN/latest/)	)
	*  Truora-Service 主分支[main](https://github.com/WeBankBlockchain/Truora-Service/tree/main)
	*  Truora-Service 开发分支[v3dev]()(https://github.com/WeBankBlockchain/Truora-Service/tree/v3dev)
	*  v3dev分支暂不支持一键安装、docker和web模块，欢迎参与开发贡献。


# 源码编译


## 安装介绍

## 前置依赖

在使用本组件前，请确认系统环境已安装相关依赖软件，清单如下：

| 依赖软件 | 说明 |
| --- | --- | 
| FISCO-BCOS | >= 2.6.0 | 
| MySQL | >= mysql-community-server[5.7] | 
| Java | JDK[1.8] | 
| Git | 下载的源码使用 Git | 

请参考：[附录](../appendix.md) 检查系统是否已经安装相关依赖软件。

```eval_rst
.. important:: 

     - CentOS 的 yum 仓库的 OpenJDK 缺少 JCE(Java Cryptography Extension)，导致 Web3SDK 无法正常连接区块链节点。CentOS 用户推荐参考 `CentOS 安装 Java <../appendix.html#centos_install_java>`_ 进行安装。
```

## 拉取代码

执行命令：
```Bash
# 拉取源码
git clone https://github.com/WeBankBlockchain/Truora-Service.git

# 进入目录
cd Truora-Service
```


## 编译代码

方式一：如果服务器已安装Gradle，且版本为 Gradle-4.10 +

```shell
gradle build -x test
```

方式二：如果服务器未安装 Gradle，或者版本低于 Gradle-4.10，使用 gradlew 编译

```shell
chmod +x ./gradlew && ./gradlew build -x test
```

构建完成后，会在根目录 Truora-Service 下生成已编译的代码目录 dist。

<span id="encrypt_type">

## 加密类型
FISCO-BCOS 链有两种类型： **非国密（ECDSA）** 和 **国密（SM2）** 。

在使用 SDK 连接 FISCO-BCOS 链的节点时，也有两种方式：**非国密（ECDSA）连接** 和 **国密（SM2）连接**。 

关于 **链类型** 和 **链连接** 的关系如下：

| 链类型  | 非国密链（ECDSA） |  国密链（SM2） |
|---|:---:|:---:|
| 非国密连接（ECDSA）  |  支持 | 支持  |
| 国密连接（SM2）  | 不支持  | 支持  |

```eval_rst
.. admonition:: 提示

    - **非国密** 链 **只支持** 非国密连接
    - **国密** 链 **支持** 非国密连接 和 国密连接，但是需要根据节点的 `config.ini` 文件，检查节点是否已经开启国密连接
```

在部署 Truora-Service，需要同时配置 **链类型** 和 **连接类型**。

<span id="chain_type"/>

### 链类型
在使用 `build_chain.sh` 脚本部署 FISCO-BCOS 链时，如果使用了 `-g` 参数，则链类型为 **国密链**。

<span id="connection_type"/>

### 连接类型
如果 FISCO-BCOS 版本小于或等于 `v2.4.x`，**只能使用** **非国密方式（ECDSA）** 连接链。

如果 FISCO-BCOS 版本大于或等于 `v2.5.x`，执行命令：

```Bash
# 查看 sm_crypto_channel 配置项
grep "sm_crypto_channel" nodes/127.0.0.1/node0/config.ini 
```

* 如果输出：

```ini
    sm_crypto_channel=true
```

表示节点已经启用 国密（SM2）连接，**只能使用** **国密方式（SM2）** 连接链。

* 如果没有输出，或者输出如下：

```ini
    sm_crypto_channel=false
```

表示节点未启用 国密连接，**只能使用** **非国密方式（ECDSA）** 连接链。


```eval_rst
.. important::

    - 不同的连接方式，需要拷贝的证书不同
    - 使用 **非国密** 的方式连接节点，需要拷贝 `sdk/` 目录下 `ca.crt`、`node.crt` 和 `node.key` 文件
    - 使用 **国密** 的方式连接节点，需要拷贝 `sdk/gm` 目录下 `gm` 开头的所有文件
```



## 修改配置

进入 `dist` 目录

```
cd dist
```

`dist` 目录提供了一份配置模板 `conf`

### 配置数据库
修改配置 `conf/application.yml` 文件

```Bash
# 进入 conf 目录
cd conf
```

* 修改数据库 `IP` 地址，用户名和密码。 
   
```yaml
 datasource:
    driver-class-name: com.mysql.cj.jdbc.Driver
    url: jdbc:mysql://127.0.0.1:3306/truora?serverTimezone=GMT%2B8&useUnicode=true&characterEncoding=utf-8&zeroDateTimeBehavior=convertToNull&useSSL=false
    username: "defaultAccount"
    password: "defaultPassword"
```  

### 配置链类型

如果链类型是国密，配置 `encryptType: 1`，关于链类型，请参考：[链类型](./deploy.html#chain_type)

```yaml
sdk:
  #0:standard, 1:guomi
  encryptType: 1 
```  

### 拷贝证书

在拷贝证书文件之前，需要确定使用哪种方式连接到链接点：非国密连接（ECDSA）还是 国密连接（SM2），请参考：[连接类型](./deploy.html#connection_type)

  * 非国密连接
  
```shell
# 进入 conf 目录
cd conf

# 非国密连接
cp  /${PATH_TO_SDK}/node.* .
cp  /${PATH_TO_SDK}/ca.crt .
```

```eval_rst
.. important::

    - **非国密链：** 拷贝节点所在目录 `nodes/${ip}/sdk` 下的 `ca.crt`、`node.crt` 和 `node.key` 文件拷贝到 `conf` 目录
```

  * 国密连接
  
    
```shell
# 进入 conf 目录
cd conf

# 国密连接
cp  /${PATH_TO_SDK}/gm/gm.* .
```

```eval_rst
.. important::

    - **国密链：** 拷贝节点所在目录 `nodes/${ip}/sdk/gm` 下的 `gm` 开头的所有文件拷贝到 `conf` 目录
```

<span id="modify_service_config" />

### 多链（群组）支持

#### 配置连接
Truora-Service 支持同时连接多条链，以及连接同一条链中的多个群组。

同一个 Truora-Service 连接多条链时，要求 **链类型** 都相同，同时采用 **相同的连接方式** 连接到链接点。

```eval_rst
.. important:: 

    - 不同链之间相互独立，没有关联
    - 多条链时，创建独立目录存放不同链的证书文件，同时拷贝证书文件
```

  * 如果采用 **非国密连接（ECDSA）**，修改 `application-ecdsa.yml` 文件

```yaml 
########################################################################
# 配置 Truora 连接的链和群组信息（证书和地址）:
#   1. 同一条链可以配置多个群组
#   2. 可以配置多条链
########################################################################
group-channel-connections-configs:
  configs:
    ## 第一条链的连接信息，证书，群组列表和 IP:Port
    - chainId: 1
      caCert: classpath:ca.crt
      sslCert: classpath:node.crt
      sslKey: classpath:node.key
      all-channel-connections:
         - group-id: 1
           connections-str:
             # node listen_ip:channel_listen
             - 127.0.0.1:${FISCO_BCOS_PORT:20200}
        ## 群组 2 的信息
        #- group-id: 2
        #  connections-str:
        #    - 127.0.0.1:20200

    ## 第二条链的连接信息，证书，群组列表以及对应的 IP:Port
    #- chainId: 2
    #  caCert: classpath:2/ca.crt
    #  sslCert: classpath:2/node.crt
    #  sslKey: classpath:2/node.key
    #  all-channel-connections:
    #    - group-id: 1
    #      connections-str:
    #        - 127.0.0.1:20200

```

* 如果采用 **国密连接（SM2）**，修改 `application-sm2.yml` 文件

```yaml 
########################################################################
# 配置 Truora 连接的链和群组信息（证书和地址）:
#   1. 同一条链可以配置多个群组
#   2. 可以配置多条链
########################################################################
group-channel-connections-configs:
  configs:
    ## 第一条链的连接信息，证书，群组列表和 IP:Port
    - chainId: 1
      gmCaCert: classpath:gmca.crt
      gmSslCert: classpath:gmsdk.crt
      gmSslKey: classpath:gmsdk.key
      gmEnSslCert: classpath:gmensdk.crt
      gmEnSslKey: classpath:gmensdk.key
      all-channel-connections:
         - group-id: 1
           connections-str:
             # node listen_ip:channel_listen
             - 127.0.0.1:${FISCO_BCOS_PORT:20200}
        ## 群组 2 的信息
        #- group-id: 2
        #  connections-str:
        #    - 127.0.0.1:20200

    ## 第二条链的连接信息，证书，群组列表以及对应的 IP:Port
    #- chainId: 2
    #  gmCaCert: classpath:2/gmca.crt
    #  gmSslCert: classpath:2/gmsdk.crt
    #  gmSslKey: classpath:2/gmsdk.key
    #  gmEnSslCert: classpath:2/gmensdk.crt
    #  gmEnSslKey: classpath:2/gmensdk.key
    #  all-channel-connections:
    #    - group-id: 1
    #      connections-str:
    #        - 127.0.0.1:20200

``` 

#### 启用链和群组
  
根据连接类型，修改 `application-ecdsa.yml` 或 `application-sm2.yml` 文件中 `event.eventRegisters` 配置。
  
```eval_rst
.. admonition:: 提示

     - 配置多链多群组监听时，配置的链ID和群组ID，必须在 `group-channel-connections-configs` 中配置过
     - `group-channel-connections-configs` 表示 Truora-Service 会连接到哪些链和群组
     - `eventRegisters` 表示启用哪些链和群组
```
  
```yaml
########################################################################
# 配置事件监听：
#   1. 配置 Truora 需要监听的链（ChainId）和群组（groupId）
#   2. 配置的 chainId 和 groupId 需要在 group-channel-connections-configs 存在
########################################################################
event:
  eventRegisters:
   - {chainId: 1, group: 1}
   #- {chainId: 1, group: 2}
   #- {chainId: 2, group: 1}
   #- {chainId: 2, group: 2}
```  



## 服务启停

返回到 `dist` 目录执行：

* 启动

```Bash
# 采用非国密连接（ECDSA）
bash start.sh

# 采用国密连接（SM2），添加 gm 参数
bash start.sh gm
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
tail -f log/Oracle-Service.log
```
