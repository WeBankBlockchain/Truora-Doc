# TrustOracle-Service 服务

[![License](https://img.shields.io/badge/license-Apache%202-4EB1BA.svg)](https://www.apache.org/licenses/LICENSE-2.0.html)    


```eval_rst
.. admonition:: **简介**

    `TrustOracle-Service <https://github.com/WeBankBlockchain/TrustOracle-Service>`_ 是 `TrustOracle` 的后台服务，主要分为链上部分和链下部分。链上部分主要是 `oracle` 相关合约，链下部分主要是 `Java` 服务，负责连接节点，监听合约的事件，采集结果并回写到 `oracle` 合约。

```

TrustOracle-Service 目前主要支持获取链下API和产生VRF随机数功能。

获取链下API原理图:
     
![api](../../images/oracle.png) 

获取VRF可验证随机数原理图:
![VRF](../../images/vrf.png)  

 
 VRF原理请参考[VRF](./backup/VRF.md);

   
   
  



```eval_rst
.. toctree::
   :maxdepth: 3

   outline.md
   deploy/index.md
   develop.md
   interface.md
   question.md
   appendix.md


```