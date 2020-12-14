##############################################################
TrustOracle文档
##############################################################
.. image:: ../_static/images/logo/FISCO_BCOS_Logo.svg

.. admonition:: **简介**

  区块链是一个确定性的、封闭的系统环境，智能合约不管何时何地运行都必须是一致的结果，所以虚拟机（VM）不能让智能合约有 network call（网络调用），不然结果就是不确定的。
  智能合约不能直接获取到链外真实世界的数据，导致区块链与现实世界是割裂的。
  而如何将区块链和现实世界连接起来，就需要引入预言机服务，通过预言机将现实世界的数据输入到区块链上，为智能合约提供与外部世界的连接性。

  TrustOracle 是 `FISCO-BCOS <https://github.com/FISCO-BCOS/FISCO-BCOS>`_ 区块链平台的预言机服务解决方案，是在广泛调研的基础上针对 **联盟链** 场景设计的预言机服务。

  TrustOracle 服务主要由后端 Java 组件 `TrustOracle-Service <https://github.com/WeBankBlockchain/TrustOracle-Service>`_ 和前端 `Vue` 组件 `TrustOracle-Web <https://github.com/WeBankBlockchain/TrustOracle-Web>`_ 组成。

.. important::

   - 本技术文档只适用 FISCO BCOS 2.6+
   - FISCO BCOS 2.6+ 版本及兼容性说明 `这里 <https://fisco-bcos-documentation.readthedocs.io/zh_CN/latest/docs/change_log/index.html>`_

.. toctree::
   :maxdepth: 3
   
   ./TrustOracle-Service/index.md
   ./TrustOracle-Web/index.md
..

 
