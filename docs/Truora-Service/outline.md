# 概要介绍

[![License](https://img.shields.io/badge/license-Apache%202-4EB1BA.svg)](https://www.apache.org/licenses/LICENSE-2.0.html)    


```eval_rst
.. admonition:: **简介**

    `Truora-Service <https://github.com/WeBankBlockchain/Truora-Service>`_ 是 Truora 的后台服务，主要分为链上部分和链下部分。  
    链上部分主要是 `oracle` 相关合约，链下部分主要是 `Java` 服务，负责连接节点，监听合约的事件，采集结果并回写到 `oracle` 合约。  

    `Truora-Service` 支持多集群部署(监听同一条链并共用同一个数据库)。

```

Truora-Service 目前主要支持获取链下API,链上可验证随机数（VRF），后续会陆续开源去中心化预言机功能。

## 获取链下API原理图:
     
![api](../../images/oracle-yhb.png)   

  用户发起调用后，`FiscoOracleClient` 会调用 `OracleCore` 合约，此时会发起一个事件。`Truora-Service` 监听到事件后， 会从事件信息中取出以下几个信息字段：
  
   - 请求编号（唯一）
   - 请求地址和数据解析格式（URL）
   - 倍数（防止小数,solidity 不支持浮点数）
   - 返回值类型（支持 string，int256, bytes三种）
     
  `Truora-Service` 获取到数据后，对 URL 发起一个 HTTP 调用，获取到数据，对数据按照解析格式进行处理，然后再调用 `OracleCore` 合约，根据请求编号将结果上传到链上，提供给其他合约来获取。

## 获取链上可验证随机数（VRF):
![vrf](../../images/VRF.png)  
 
 `VRF`原理可以参考文档[VRF原理](./VRF.md)   
  用户发起调用后，需要传入随机数种子，`VRFClient` 会调用 `VRFCore` 合约，此时会发起一个事件。`Truora-Service` 监听到事件后,会从事件信息中取出以下几个信息字段：
   - 请求编号（唯一）
   - 用户输入随机数种子
   - 预言机公钥哈希
   - 实际参与VRF计算的随机数种子  
   - 块高    
 
   `Truora-Service` 获取到数据后，使用服务私钥和实际随机数种子调用`vrf`函数生成随机数和`proof`，然后再调用 `OracleCore` 合约，`OracleCore`合约会验证`proof`的准确性，通过后，则根据请求编号将随机数结果回写到用户合约。

```eval_rst 
.. important::
   - 随机数种子要保持足够随机性，为了安全，实际参与VRF运算的随机数种子是由用户随机数种子，用户合约地址，预言机公钥哈希，用户请求次数四个变量哈希处理后的值做为随机数种子。
   - 本方案采用的是 `SECP256K1_SHA256_TAI`加密套件，参考实现 `ECVRF <https://tools.ietf.org/html/draft-irtf-cfrg-vrf-06#section-5/>`_。

```    
  
       

