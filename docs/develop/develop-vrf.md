# 合约开发指南

## 获取链下API数据
 用户可以参考 [APISampleOracle.sol](https://github.com/WeBankBlockchain/Truora-Service/blob/main/contracts/1.0/sol-0.6/oracle/FiscoOracleClient.sol) 合约实现自己的oracle业务合约。 合约解析如下：
  - 用户合约需继承FiscoOracleClient合约
   ```
    contract APISampleOracle is FiscoOracleClient
   ``` 
  - 构造函数需要传入指定的Truora服务方地址。地址可以通过上述接口获取。
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
  - 必须实现 **__callback(bytes32 _requestId, int256 _result)** 方法，用于Truora-Service服务回调获取的结果。
  - **get()** 方法获取本次请求结果, 可自行修改此函数, 获取结果后进行自己业务逻辑的计算。  
  
     
   ***URL格式规范***
   目前支持json和text/plain两种访问格式。并且链下API的url必须支持HTTPS访问。
   遵循jsonpath格式，子元素 用 ***"."*** 表示；     
   text/plain默认取第一行；
  ``` 
     //获取链下随机数API
       plain(https://www.random.org/integers/?num=100&min=1&max=100&col=1&base=10&format=plain&rnd=new)
     //获取人民币对日元汇率API 
       json(https://api.exchangerate-api.com/v4/latest/CNY).rates.JPY
  ``` 
    

## VRF获取可验证随机数
  
  用户合约开发只需继承 [VRFConsumerBase](https://github.com/WeBankBlockchain/Truora-Service/blob/dev/contracts/0.4/sol-0.6/vrf/VRFConsumerBase.sol) 目录下合约即可。必须实现 `fulfillRandomness` 方法，以便 `oracle-service` 将结果回写。
 
  - 用户合约需继承 `FiscoOracleClient` 合约
   ```
    contract RandomNumberConsumer is VRFConsumerBase
   ``` 
  - 构造函数需要传入指定的 Truora 服务方的 `_keyHash` 和 `_coordinator` 地址 。上述值可以通过接口获得。
   ```
    constructor(address _coordinator, bytes32 _keyHash)
          VRFConsumerBase(
              _coordinator // VRF Coordinator
          )
   ```       
  - 设定自己的随机数种子。
  ```
  function getRandomNumber(uint256 userProvidedSeed) public returns (bytes32 requestId) {
         return requestRandomness(keyHash, userProvidedSeed);
     }
  ```
  - 必须实现 `fulfillRandomness` 覆写方法,用于Truora-Service服务回调获取的结果。
  ```
  function fulfillRandomness(bytes32 requestId, uint256 randomness) internal override {
        randomResult = randomness;
    }
  ```
  
  
