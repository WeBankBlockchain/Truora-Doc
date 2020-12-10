
# 源码安装

```eval_rst
.. important:: 

     - CentOS 的 yum 仓库的 OpenJDK 缺少 JCE(Java Cryptography Extension)，导致 Web3SDK 无法正常连接区块链节点，推荐自行从 `OpenJDK网站 <https://jdk.java.net/java-se-ri/11>`_ 自行下载后安装。
     
```

## 前置依赖

在使用本组件前，请确认系统环境已安装相关依赖软件，清单如下：

| 依赖软件 | 说明 |
| --- | --- | 
| FISCO-BCOS | >= 2.6.0 | 
| MySQL | >= mysql-community-server[5.7] | 
| Java | JDK[1.8] | 
| Git | 下载的源码使用Git | 


```eval_rst
.. important:: 

     - CentOS 的 yum 仓库的 OpenJDK 缺少 JCE(Java Cryptography Extension)，导致 Web3SDK 无法正常连接区块链节点，推荐自行从 `OpenJDK网站 <https://jdk.java.net/java-se-ri/11>`_ 自行下载后安装。
     
```

如果您还未安装这些依赖，请参考[附录](../appendix.md)


## 拉取代码
执行命令：
```
git clone https://github.com/WeBankBlockchain/TrustOracle-Service.git
```

进入目录：

```
cd TrustOracle-Service
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

构建完成后，会在根目录 TrustOracle-Service 下生成已编译的代码目录 dist。

## 修改配置

（1）进入dist目录

```
cd dist
```

dist目录提供了一份配置模板conf：


（2）进入conf目录：

```shell
cd conf

# 拷贝 SDK 连接证书
cp  /${PATH_TO_SDK}/node.* .
cp  /${PATH_TO_SDK}/ca.crt .
```

```eval_rst
.. important::

    - TrustOracle-Service 服务需要连接 FISCO-BCOS 节点，拷贝节点所在目录 `nodes/${ip}/sdk` 下的 `ca.crt`、`node.crt` 和 `node.key` 文件拷贝到当前 `conf` 目录。

```


（3）修改配置 application.yml 文件（根据实际情况修改）：

  * 修改数据库 `IP` 地址，用户名和密码。 
   
```yaml
 datasource:
    driver-class-name: com.mysql.cj.jdbc.Driver
    url: jdbc:mysql://127.0.0.1:3306/trustoracle?serverTimezone=GMT%2B8&useUnicode=true&characterEncoding=utf-8&zeroDateTimeBehavior=convertToNull&useSSL=false
    username: "defaultAccount"
    password: "defaultPassword"
```  
  
  
  * 多链多群组配置：**不同链之间相互独立，没有关联**


```eval_rst
.. important:: 

    - TrustOracle-Service 在配置多链时，所有链版本必须在 `v2.6.0` + 
    - 不同链的证书目录不同
    - 同一个 TrustOracle-Service 连接的多条链，必须同为 **ECDSA(默认，非国密)**，或者同为 **国密**
```

```yaml 
########################################################################
# 配置 TrustOracle 连接的链和群组信息（证书和地址）:
#   1. 同一条链可以配置多个群组
#   2. 可以配置多条链，要求同为 ECDSA（默认，非国密） 或者 国密
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
             - 127.0.0.1:20200
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

  * 国密配置：如果链类型是国密，配置 `encryptType` = 1
  
```yaml
sdk:
  encryptType: 0 #0:standard, 1:guomi
```

  * 多链多群组监听配置
  
```eval_rst
.. admonition:: 提示

     - 配置多链多群组监听时，配置的链ID和群组ID，必须在 `group-channel-connections-configs` 中配置过
     - `group-channel-connections-configs` 表示 TrustOracle-Service 会连接到哪些链和群组
     - `eventRegisters` 表示启用哪些链和群组
     
```

  
```yaml
########################################################################
# 配置事件监听：
#   1. 配置 TrustOracle 需要监听的链（ChainId）和群组（groupId）
#   2. 配置的 chainId 和 groupId 需要在 group-channel-connections-configs 存在
########################################################################
event:
  eventRegisters:
   - {chainId: 1, group: 1, operator: "operator1", url: "http://localhost:5012/TrustOracle/"}
   #- {chainId: 1, group: 2, operator: "operator2", url: "http://localhost:5012/TrustOracle/"}
   #- {chainId: 2, group: 1, operator: "operator3", url: "http://IP2:5012/TrustOracle/"}
   #- {chainId: 2, group: 2, operator: "operator4", url: "http://IP2:5012/TrustOracle/"}
```  

## 服务启停

返回到dist目录执行：
```shell
启动: bash start.sh
停止: bash stop.sh
检查: bash status.sh
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
