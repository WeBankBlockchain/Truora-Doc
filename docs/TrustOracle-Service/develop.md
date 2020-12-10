# 开发指南

##  获取合约地址

```Bash
http://{deployIP}:{port}/Oracle-Service/oracle/address?chainId=1&groupId=1
    
# 示例：
curl "http://localhost:5012/Oracle-Service/oracle/address?chainId=1&groupId=1"
```

返回结果如下：

```json
{
  "code": 0,
  "message": "success",
  "data": [
    {
      "chainId": 1,
      "group": 1,
      // OracleCore 合约地址
      "oracleCoreContractAddress": "0x66c3631ced2c63379f2133180f70aedf1d728869",
      // VRF 合约地址
      "vrfContractAddress": "0x741a6ddfa69c6dbeee8ecea651f2748f80404009",
      "fromBlock": "latest",
      "toBlock": "latest",
      "operator": "operator",
      "url": "http://localhost"
    }
  ]
}
```

- 部署服务器IP和服务端口需对应修改，网络策略需开通
- 基于 WeBASE-Front 可视化控制台，可以开发智能合约，部署合约和发送交易，并查看交易和区块详情。还可以管理私钥，对节点健康度进行监控和统计

### 方式一：获取链下API数据
 用户可以参考[APISampleOracle.sol]()合约实现自己的oracle业务合约。 合约解析如下：       
  1. 用户合约需继承FiscoOracleClient合约
   ```
    contract APISampleOracle is FiscoOracleClient
   ``` 
  2. 构造函数需要传入指定的TrustOracle服务方地址。地址可以通过上述接口获取。  
   ```
      constructor(address oracleAddress) public {  
            oracleCoreAddress = oracleAddress;      
      }  
   ```       
  3.  设定自己要访问的url。修改url变量赋值即可。  
  
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
  4. 必须实现 **__callback(bytes32 _requestId, int256 _result)** 方法，用于TrustOracle预言机回调获取的结果。  
  5. **get()** 方法获取本次请求结果, 可自行修改此函数, 获取结果后进行自己业务逻辑的计算。  
  
     
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
    
   


### VRF获取可验证随机数
  
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
  
  
### 去中心化部署获取聚合结果



  latestAnswer()最新的聚合结果
  
  latestTimestamp() 最新一次聚合的时间戳
  
  latestRound()最新一次聚合的轮次号
  
  getAnswer(uint256 roundId) 通过轮次号获取历史结果
  
  getTimestamp(uint256 roundId)通过轮次号获取历史时间戳