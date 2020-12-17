# 合约开发指南

### 1 获取链下API数据
 用户可以参考 [APISampleOracle.sol](https://github.com/WeBankBlockchain/TrustOracle-Service/blob/dev/contracts/0.4/sol-0.6/oracle/FiscoOracleClient.sol) 合约实现自己的oracle业务合约。 合约解析如下：       
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
    
   目前支持json和text/plain两种访问格式。并且链下API的url必须支持HTTPS访问。
   遵循jsonpath格式，子元素 用 "." 表示,数组用 "[]"表示，目前只支持单个返回值；     
   text/plain默认取第一行。；  
  
     //获取链下随机数API
       plain(https://www.random.org/integers/?num=100&min=1&max=100&col=1&base=10&format=plain&rnd=new)
     //获取人民币对日元汇率API 
       json(https://api.exchangerate-api.com/v4/latest/CNY).rates.JPY
  
``` 
  
  
