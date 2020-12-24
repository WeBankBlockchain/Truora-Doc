# 什么是 TrustOracle


## 为什么需要使用预言机

## 设计原则



## 整体架构


## 功能介绍


## 各子系统简介


 
.. admonition:: **简介**

  区块链是一个确定性的、封闭的系统环境，智能合约不管何时何地运行都必须是一致的结果，所以虚拟机（VM）不能让智能合约有网络调用，不然结果就是不确定的。
  智能合约不能直接获取到链外真实世界的数据，导致区块链与现实世界是割裂的,也极大的限制了区块链的应用场景。
  而如何将区块链和现实世界连接起来，就需要引入预言机服务，通过预言机将现实世界的数据输入到区块链上，为智能合约提供与外部世界的连接性。

   中国人民银行发布的《区块链能做什么？不能做什么？》报告中，是这样对预言机定义的：
  "区块链外信息写入区块链内的机制，一般被称为预言机 (oracle mechanism)。"

  TrustOracle 是 `FISCO-BCOS <https://github.com/FISCO-BCOS/FISCO-BCOS>`_ 区块链平台的预言机服务解决方案，是在广泛调研的基础上针对 **联盟链** 场景设计的可信预言机服务。

  作为连接 `FISCO-BCOS` 联盟链和互联网的桥梁，TrustOracle致力于解决互联网数据安全可信的上链，助力扩宽联盟链的应用场景和丰富联盟链生态。

  TrustOracle 服务主要由后端 `Java` 组件 `TrustOracle-Service <https://github.com/WeBankBlockchain/TrustOracle-Service>`_ 和前端 `Vue` 组件 `TrustOracle-Web <https://github.com/WeBankBlockchain/TrustOracle-Web>`_ 组成。



.. admonition:: **应用场景**

 - **游戏/预测市场方面:**
    获取链上安全的随机数，智能合约以此来分配奖金。
 - **物联网方面:**
    对于IoT应用，将传感器信息上链，智能合约验证并触发下一步的行为。


.. admonition:: **主要特性**

    - 链下 HTTPS API 数据获取
    - 支持多链多群组服务（必须同为 `ECDSA` 或 国密）
    - 支持VRF随机数生成
    - 支持国密
    - 支持集群部署
    - 支持中心化部署和去中心化部署
    - 支持多数据格式访问
    - 支持请求状态查询

.. important::

   - 本技术文档只适用 FISCO BCOS 2.6+
   - FISCO BCOS 2.6+ 版本及兼容性说明 `这里 <https://fisco-bcos-documentation.readthedocs.io/zh_CN/latest/docs/change_log/index.html>`_

