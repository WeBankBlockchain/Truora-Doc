# Truora介绍


**本技术文档主要适用于面向 FISCO BCOS 3.1+的Truora-Service**

如面向FISCO BCOS2.6.x+，切换到文档连接[v2stable](https://truora.readthedocs.io/zh_CN/v2stable/)	 

**分支版本说明参见 [连接](version.md)**

## 预言机简介
```eval_rst 
.. admonition:: **预言机简介**

  区块链是一个确定性的、封闭的系统环境，智能合约不管何时何地运行都必须是一致的结果，所以虚拟机（VM）不能让智能合约有网络调用，不然结果就是不确定的。  

  智能合约不能直接获取到链外真实世界的数据，导致区块链与现实世界是割裂的，也极大地限制了区块链的应用场景。
  而如何将区块链和现实世界连接起来，就需要引入预言机服务，通过预言机将现实世界的数据输入到区块链上，为智能合约提供与外部世界的连接性。

```

## Truora简介
```eval_rst 
.. admonition:: **Truora简介**

 Truora 是 `FISCO-BCOS <https://github.com/FISCO-BCOS/FISCO-BCOS>`_ 区块链平台的预言机服务解决方案，是在广泛调研的基础上针对 **联盟链** 场景设计的可信预言机服务。

 区块链愈发展，对链下数据的需求就会愈强烈，预言机的重要性也会愈发凸显。

 作为连接 `FISCO-BCOS` 联盟链和互联网的桥梁，Truora致力于解决互联网数据安全可信的上链，助力扩宽联盟链的应用场景和丰富联盟链的生态。
```
![](_images/oracle-frame.png)

## 项目定位
```eval_rst 
.. admonition:: **Truora项目定位**

 预言机体系有多种实现方式，在不同场景需要考量的功能、性能、安全性等级各有不同。
 
 Truora项目聚焦于面向FISCO BCOS底层平台提供一个预言机的参考实现。参考实现包括链上核心合约，示例合约，链下服务的java代码，测试和演示代码。
 
 Truora是开源开放的，Truora尚在快速迭代中， 暂不提供Docker类的部署，Web界面等周边。
  
 欢迎开发者获取代码，进行二次开发，插件化的扩展更多的数据源接入，并为开源项目做贡献。
```


## 主要特性

```eval_rst 
.. admonition:: **主要特性**

    - 链下 HTTPS API 数据获取
    - 支持多链多群组服务（必须同为 `ECDSA` 或 国密）
    - 支持VRF随机数生成
    - 支持国密
    - 支持集群部署
    - 支持中心化部署和去中心化部署
    - 支持多数据格式访问
    - 支持请求状态查询
    - 支持FISCO BCOS底层平台的2.x和3.x版本
```

## 应用场景
```eval_rst 
.. admonition:: **应用场景**
   
    - **游戏/预测市场方面:**
       获取链上安全的随机数，智能合约实现更公平游戏场景。
    - **物联网方面:**
       对于IoT应用，将传感器信息上链，智能合约验证并触发下一步的行为。
    - **供应链金融方面:**
       获取链下订单信息和汇率信息等。
```





  
