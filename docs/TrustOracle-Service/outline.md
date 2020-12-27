# 概要介绍

[![License](https://img.shields.io/badge/license-Apache%202-4EB1BA.svg)](https://www.apache.org/licenses/LICENSE-2.0.html)    


```eval_rst
.. admonition:: **简介**

    `TrustOracle-Service <https://github.com/WeBankBlockchain/TrustOracle-Service>`_ 是 `TrustOracle` 的后台服务，主要分为链上部分和链下部分。链上部分主要是 `oracle` 相关合约，链下部分主要是 `Java` 服务，负责连接节点，监听合约的事件，采集结果并回写到 `oracle` 合约。

```

TrustOracle-Service 目前主要支持获取链下API,后续会陆续开源VRF随机数，去中心化预言机功能。

获取链下API原理图:
     
![api](../../images/oracle-yhb.png)   

  用户发起调用后，`FiscoOracleClient` 会调用 `OracleCore` 合约，此时会发起一个事件。`TrustOracle-Service` 监听到事件后， 会从事件信息中取出以下几个信息字段：  
  
   - 请求编号（唯一）
   - 请求地址和数据解析格式（URL）
   - 倍数（防止小数,solidity 不支持浮点数）
     
  `TrstOracle-Service` 获取到数据后，对 URL 发起一个 HTTP 调用，获取到数据，对数据安装解析格式进行处理，然后再调用 `OracleCore` 合约，根据 请求编号将结果上传到链上，提供给其他合约来获取。  

        

