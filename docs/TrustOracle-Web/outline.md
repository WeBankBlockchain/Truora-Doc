# 概要介绍

```eval_rst
.. admonition:: **简介**

 `TrustOracle-Web <https://github.com/WeBankBlockchain/TrustOracle-Web>`_ 是 TrustOracle 服务的前端组件。  
  主要支持请求详情和请求历史的查询。方便应用查询自己预言机请求的结果，如果失败，可以看到请求失败的原因。   
```

该项目是区块链中间件 **TrustOracle-Service 预言机** 的前端服务，基于 `vue-cli` 框架开发。

兼容浏览器 `IE9` 及以上，`360` 浏览器兼容版（`IE9` 内核），`360` 浏览器极速版，`Chrome` 浏览器。    

  具体功能如下：  

* **分页查询请求记录：**
    * 通过 **链 ID** 和 **群组 ID**，分页查询请求历史记录
    
* **查询单个请求详情：**
    * 根据 **请求 ID**，查询单个请求的详细信息：成功，失败（失败原理），处理时长等

* **查询系统合约地址：**
    * 查询 `OracleCore` 合约的地址，编写业务合约时使用





