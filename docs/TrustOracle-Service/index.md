# TrustOracle-Service 服务

[![License](https://img.shields.io/badge/license-Apache%202-4EB1BA.svg)](https://www.apache.org/licenses/LICENSE-2.0.html)


```eval_rst
.. admonition:: **简介**
  TrustOracle-Service预言机主要分为链上部分和链下部分。
  链上部分主要是oracle相关合约，链下部分主要是java服务，负责监听合约的事件，采集结果并回写到oracle合约。


```

```eval_rst
   原理图如下[原理图](../../images/oracle.png)
```

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
```


```eval_rst
.. admonition:: **应用场景**
 - **保险方面:**  
   航空延误险,农作物保险,天气险等，智能合约根据协议对保险产品作出是否理赔的决策。  
 - **金融方面:**  
   获取汇率或者资产价格，智能合约以此来确定参与各方的收益和损失，以及触发平仓交易。  
 - **游戏/预测市场方面:**   
   获取链上安全的随机数，智能合约以此来分配奖金。  
 - **物联网方面:**  
   对于IoT应用，将传感器信息上链，智能合约验证并触发下一步的行为。     
```


```eval_rst
.. toctree::
   :maxdepth: 3

   outline.md
   deploy/index.md
   develop.md
   question.md
   appendix.md

```