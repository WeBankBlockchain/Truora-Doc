# TrustOracle 开发教程

TrustOracle 预言机服务中有两个角色：

* **TrustOracle 服务运营方**

    服务运运营方需要部署 `TrustOracle-Service` 和 `TrustOracle-Web` 服务，并且部署 Oracle 相关合约到链上，为预言机用户提供服务。

* **预言机用户**
    
    预言机用户需要根据自身业务，选择一个 TrustOracle 服务运营方，并编写预言机合约（需要从服务运营方处获取 Oracle 相关合约的地址），使用服务运营方提供的预言机服务。
  

## 开发流程

预言机服务开发的流程：

1. 获取 Oracle 相关合约地址
    * 选择一个 TrustOracle 服务运营方，并从运营方获取到 Oracle 相关合约地址
    * 如果没有运营方，可以参考：[安装部署](./TrustOracle-Install/index.html) 自行搭建 `TrustOracle` 服务。部署完成后，可以通过 TrustOracle-Web 获取 Oracle 相关合约地址，请参考：[查询系统合约地址](../TrustOracle-Web/outline.html#list_oracle_address)
    
2. 开发合约
    * 编写，调试合约

## 开发 TrustOracle 合约

### 获取链下 API 数据

 用户可以参考 [APISampleOracle.sol](https://github.com/WeBankBlockchain/TrustOracle-Service/blob/dev/contracts/1.0/sol-0.6/oracle/FiscoOracleClient.sol) 合约实现自己的oracle业务合约。 合约解析如下：       
  - 用户合约需继承FiscoOracleClient合约
   ```
    contract APISampleOracle is FiscoOracleClient
   ``` 
  - 构造函数需要传入指定的`TrustOracle`服务的 `OracleCore`合约 地址。地址可以通过接口获取。  
   ```
      constructor(address oracleAddress) public {  
            oracleCoreAddress = oracleAddress;      
      }  
   ```       
  - 设定自己要访问的url。修改url变量赋值即可。  
  
   ```
      function request() public returns (bytes32 requestId)
        {
    
          // Set your URL
          // url = "plain(https://www.random.org/integers/?num=100&min=1&max=100&col=1&base=10&format=plain&rnd=new)";
             url = "json(https://api.exchangerate-api.com/v4/latest/CNY).rates.JPY";
             bytes32  requestId = oracleQuery(oracleCoreAddress, url, timesAmount);
             validIds[requestId] = true;
             return requestId;
              
        }
   ```
  - 必须实现 **__callback(bytes32 _requestId, int256 _result)** 方法，用于TrustOracle-Service服务回调获取的结果。  
  - **get()** 方法获取本次请求结果, 可自行修改此函数, 获取结果后进行自己业务逻辑的计算。  
  
----------
```eval_rst
.. admonition:: **URL格式规范**
    
   目前支持json和text/plain两种访问格式。并且链下API的url必须支持HTTPS访问(安全因素考虑)。  
   遵循jsonpath格式，子元素 用 "." 表示,数组用 "[]"表示，目前只支持单个返回值；     
   text/plain默认取第一行，也可指定数组下标取特定行。 jsonpath规范可以参考 `jsonpath <https://support.smartbear.com/alertsite/docs/monitors/api/endpoint/jsonpath.html>`_ 
     //获取链下随机数API
       plain(https://www.random.org/integers/?num=100&min=1&max=100&col=1&base=10&format=plain&rnd=new)
     //获取人民币对日元汇率API 
       json(https://api.exchangerate-api.com/v4/latest/CNY).rates.JPY  
     // 查询某城市某天最高温度  
       json(https://devapi.qweather.com/v7/weather/3d?location=101280601&key=90d8a8ee98ff495694dce72e96f53a18).daily[1].tempMax
``` 
  
  
