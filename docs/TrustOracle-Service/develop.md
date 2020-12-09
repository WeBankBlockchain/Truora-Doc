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
 用户可以参考contracts/0.4/oracle/APIConsumer.sol合约实现自己的oracle合约。  
  必须继承FiscoOracleClient合约即可。实现__callback方法，以便oracle-service将结果回写。  
  在request方法中填入要获取的链下url。  
  url格式：  
     目前支持json和text/plain两种访问格式。并且链下API的url必须支持HTTPS访问。
   - plain(https://www.random.org/integers/?num=100&min=1&max=100&col=1&base=10&format=plain&rnd=new)
   - json(https://api.exchangerate-api.com/v4/latest/CNY).rates.JPY
    
   合约解析如下：  
```

pragma solidity ^0.6.0;

import "./FiscoOracleClient.sol";

contract APIConsumer is FiscoOracleClient {


    //指定处理请求的oracle
    address private oracleCoreAddress;
  

    // Multiply the result by 1000000000000000000 to remove decimals
    uint256 private timesAmount  = 10**18;

    mapping(bytes32=>int256) resultMap;

    int256 public result;
    string url;

    constructor(address oracleAddress) public {
        oracleCoreAddress = oracleAddress;
    }


    function request() public returns (bytes32 requestId)
    {

        // Set the URL to perform the GET request on
         url = "plain(https://www.random.org/integers/?num=100&min=1&max=100&col=1&base=10&format=plain&rnd=new)";

        // Sends the request
        return sendRequestTo(oracleCoreAddress, url, timesAmount);
    }

    /**
     * Receive the response in the form of uint256
     */
    function __callback(bytes32 _requestId, int256 _result) public override onlyOracleCoreInvoke(_requestId)
    {
        resultMap[_requestId]= _result;
        result = _result ;
    }


      function getResult()  public view  returns(int256){
         return result;
      }
}
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