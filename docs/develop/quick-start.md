# Trustoracle 开发教程

Trustoracle 预言机服务中有两个角色：

* **Trustoracle 服务运营方**

    服务运运营方需要部署 `Trustoracle-Service` 和 `Trustoracle-Web` 服务，并且部署预言机相关合约到链上，为预言机用户提供服务。

* **预言机用户**
    
    预言机用户需要根据自身业务，选择一个 Trustoracle 服务运营方，并编写预言机合约（需要从服务运营方处获取预言机相关合约的地址），使用服务运营方提供的预言机服务。
  

## 开发流程

预言机服务开发的流程：

1. 获取 预言机 相关合约地址
    * 选择一个 Trustoracle 服务运营方，并从运营方获取到 预言机 相关合约地址
    * 如果没有运营方，可以参考：[安装部署](../Trustoracle-Install/index.html) 自行搭建 `Trustoracle` 服务。部署完成后，可以通过 Trustoracle-Web 获取 预言机 相关合约地址，请参考：[查询系统合约地址](../Trustoracle-Web/outline.html#list_oracle_address)
    
2. 开发合约
    * 编写，调试合约

## 开发 Trustoracle 合约

### 获取链下 API 数据

 用户可以参考 [APISampleOracle.sol](https://github.com/WeBankBlockchain/Trustoracle-Service/blob/main/contracts/1.0/sol-0.6/oracle/FiscoOracleClient.sol) 合约实现自己的oracle业务合约。
  默认支持`solidity0.6`版本合约。 `solidity0.4` 和 `solidity0.5`均在 `Trustoracle-Service` 同级目录。合约解析如下：
  - 用户合约需继承FiscoOracleClient合约
   ```
    contract APISampleOracle is FiscoOracleClient
   ``` 
  - 构造函数需要传入 `OracleCore` 合约地址。即BSN预言机管理合约地址。 
   ```
      constructor(address oracleAddress) public {  
            oracleCoreAddress = oracleAddress;      
      }  
   ```       
  - 设定自己要访问的url。修改url变量赋值即可。  
  
   ```
      function request() public returns (bytes32)
        {
    
          // Set your URL
          // url = "plain(https://www.random.org/integers/?num=100&min=1&max=100&col=1&base=10&format=plain&rnd=new)";
             url = "json(https://api.exchangerate-api.com/v4/latest/CNY).rates.JPY";
             bytes32  requestId = oracleQuery(oracleCoreAddress, url, timesAmount);
             validIds[requestId] = true;
             return requestId;
              
        }
   ```
  - 必须实现 **__callback(bytes32 _requestId, int256 _result)** 方法，用于预言机服务回调获取的结果。  
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

## 业务合约参考

下面以一个简单抽奖合约为例，介绍下一个简单抽奖业务怎么使用 `Trustoracle` 预言机合约。
    
 抽奖合约[LotteryOracle.sol](https://github.com/WeBankBlockchain/Trustoracle-Service/blob/main/contracts/1.0/sol-0.6/oracle/LotteryOracle.sol) 实现了一个简单的抽奖逻辑，
 通过使用上述[APISampleOracle.sol](https://github.com/WeBankBlockchain/Trustoracle-Service/blob/main/contracts/1.0/sol-0.6/oracle/FiscoOracleClient.sol) 获取随机数结果。请保证 `APISampleOracle` 合约的url是获取获取随机数的url。
      默认支持`solidity0.6`版本合约。 `solidity0.4` 和 `solidity0.5`自行修改合约第一行的编译器版本即可。合约解析如下：
      
  - 构造函数需要传入获取随机数合约 `APISampleOracle` 地址。  
       ```
    
    contract LotteryOracle {
    
        enum LOTTERY_STATE { OPEN, CLOSED }
        LOTTERY_STATE public lottery_state;
        address[] public players;
        uint256 public lotteryId;
        APISampleOracle private oracle;
        bytes32  private requestId;
        event Winner(uint256  lotteryId, address winner ,int256 ramdomness);
    
    
        constructor(address randomOracle) public {
            oracle = APISampleOracle(randomOracle);
            lotteryId = 0;
            lottery_state = LOTTERY_STATE.CLOSED;
        }
       ```       
   - 开始抽奖函数需要传入参与者的地址。简单状态校验后，然后通过调用 `APISampleOracle` 的 `request` 函数获取随机数。
      
       ```
          function start_new_lottery(address[] memory _players) public {
                require(lottery_state == LOTTERY_STATE.CLOSED, "can't start a new lottery yet");
                lottery_state = LOTTERY_STATE.OPEN;
                players = _players;
                lotteryId++;
                requestId = oracle.request();
            }
       ```
     
   - 获取抽奖结果函数回返回中奖者地址。`pickWinner` 函数获取随机数结果，并对总参与人数取余，得出中奖者地址。  
      ```
       function pickWinner() public returns(address) {
              require(oracle.checkIdFulfilled(requestId) == false, " oracle query has not been fulfilled!");
      
              int256 randomness  = oracle.getById(requestId);
              uint256 index = uint256(randomness) % players.length;
              address winner = players[index];
              players = new address[](0);
              lottery_state = LOTTERY_STATE.CLOSED;
              emit Winner(lotteryId, winner, randomness);
              return winner;
          }
      ``` 
     
     
## fiscoOracleClient 合约解析

  - 抽象合约，__callback方法待实现。
```
function __callback(bytes32 requestId, int256 result) public {}
```

   - 发起oracle请求，`oracleQuery` 函数会传入相关参数并调用 `oracleCore` 合约的 `query`方法。
```  
  function oracleQuery(uint expiryTime, string memory datasource, address _oracle, string memory url, uint256 timesAmount, bool needProof) internal
  returns (bytes32 requestId) {
    // calculate the id;
    oracle = OracleCoreInterface(_oracle);
    int256 chainId;
    int256 groupId;
    ( chainId, groupId) = oracle.getChainIdAndGroupId();
    requestId = keccak256(abi.encodePacked(chainId, groupId, this, requestCount));
    pendingRequests[requestId] = _oracle;
    emit Requested(requestId);

    require(oracle.query(address(this),requestCount, url,timesAmount, expiryTime,needProof),"oracle-core invoke failed!");
    requestCount++;
    reqc[msg.sender]++;

    return requestId;
  }

```
  