

### 1 获取链下API数据
 用户可以参考[APISampleOracle.sol]()合约实现自己的oracle业务合约。 合约解析如下：       
  - 用户合约需继承FiscoOracleClient合约
   ```
    contract APISampleOracle is FiscoOracleClient
   ``` 
  - 构造函数需要传入指定的TrustOracle服务方地址。地址可以通过上述接口获取。  
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
  - 必须实现 **__callback(bytes32 _requestId, int256 _result)** 方法，用于TrustOracle预言机回调获取的结果。  
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
    
   


###2 VRF获取可验证随机数
  
  用户合约开发只需继承VRFConsumerBase（contracts/0.4/oracle目录下）合约即可。必须实现fulfillRandomness方法，以便oracle-service将结果回写。

```
pragma solidity 0.6.6;

import "./VRFConsumerBase.sol";

contract RandomNumberConsumer is VRFConsumerBase {

    // hash of VRF Coordinator'pk
    bytes32 internal keyHash;
    uint256 public randomResult;

    // 填写 coordinator地址 和 oracleservice 的keyhash
    constructor(address _coordinator, bytes32 _keyHash)
        VRFConsumerBase(
            _coordinator // VRF Coordinator
        ) public
    {
         // keyHash = 0xeedf1a9c68b3f4a8b1a1032b2b5ad5c4795c026514f8317c7a215e218dccd6cf;
          keyHash = _keyHash;
    }

    /**
     * Requests randomness from a user-provided seed
     */
    function getRandomNumber(uint256 userProvidedSeed) public returns (bytes32 requestId) {
        return requestRandomness(keyHash, userProvidedSeed);
    }

    /**
     * Callback function used by VRF Coordinator
     */
    function fulfillRandomness(bytes32 requestId, uint256 randomness) internal override {
        randomResult = randomness;
    }
}
  ```
  
  
