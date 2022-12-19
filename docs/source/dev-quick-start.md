# 应用合约和客户端解析

Truora 预言机服务中有两个角色：

* **Truora 服务运营方**

    服务运运营方需要部署 `Truora-Service` 服务，并且部署预言机相关合约到链上，为预言机用户提供服务。

* **预言机用户**
    
    预言机用户需要根据自身业务，选择一个 Truora 服务运营方，并编写预言机合约（需要从服务运营方处获取预言机相关合约的地址），使用服务运营方提供的预言机服务。
  

## 基本开发流程

预言机服务开发的流程：

1. 获取 预言机 相关合约地址
    * 选择一个 Truora 服务运营方，并从运营方获取到 预言机 相关合约地址
    * 如果没有运营方，可以参考：[安装部署](deploy.md) 自行搭建 Truora 服务。
	* 搭建完成后，从数据库里获取自动部署的预言机相关合约地址
    
2. 开发合约
    * 编写，调试合约

## 开发 Truora 合约



---------

 **建议重点参考的合约：**
 
 合约文件均在项目的`contracts`路径下

* `APISampleOracle.sol`: 用于从外部数据源获取uint256类型的结果。
* `APISampleOracleReturnString.sol`： 用于从外部数据源获取String类型的结果。
* `GeneralOracle.sol` : 合并了以上两个合约的功能，根据调用者指定的ReturnType，获取外部数据源，接口同时支持UINT256，String，Byte数组三种类型。


以上合约在项目里都有对应的java调用实现，包括`com.webank.truora.test`包和`com.webank.truora.dapps`包里的代码，可以参考。


---------

 **dapp和模拟数据源体验：**


在`com.webank.truora.restcontroller`包里，面向`GeneralOracle.sol`合约，实现了`DappGeneral`类，可供在浏览器里验证`GeneralOracle`合约的功能。

其URL的配置参见`application_dapps.yml`里的  GeneralOracle 段。模板提供的url，指向本地的测试桩服务。

模拟数据源服务代码见`com.webank.truora.restcontroller.SourceStub`，其目的是预置一些随机数和字符串的在线数据源，快速体验和验证预言机能力。

访问这些Restful接口的入口列表，可以在服务启动后，用浏览器或curl直接访问索引页查看列表：

```
http://localhost:5022/truora/index
```

Resful接口的说明[参见连接](https://truora.readthedocs.io/zh_CN/v3dev/Truora-Service/interface.html#dapp)

**localhost地址根据实际部署的IP或域名替换**

---------



### 获取链下 API 数据

用户可以参考 [APISampleOracle.sol](https://github.com/WeBankBlockchain/Truora-Service/blob/main/contracts/) 合约实现自己的oracle业务合约。

默认支持`solidity0.6`版本合约。 合约解析如下：

  - 用户合约需继承FiscoOracleClient合约
   ```
    contract APISampleOracle is FiscoOracleClient
   ``` 

  - 构造函数需要传入指定的Truora服务的 `OracleCore`合约 地址。地址可以通过前端界面或者后端接口获取。
   ```
      constructor(address oracleAddress) public {  
            oracleCoreAddress = oracleAddress;      
      }  
   ```       
  - 设定自己要访问的url。修改url变量赋值即可，并且指定需要返回值类型。   
    目前只支持单个返回值，返回值可以是 `string`,`int256`,`bytes`三种类型。   
    调用`request`需要指定返回值类型，默认类型是 `int256`，因为solidity不支持浮点数，返回 `int256` 类型需要指定放大倍数 `timesAmount`。    
    如果返回值是`string`,请参考[APISampleOracleReturnString.sol](https://github.com/WeBankBlockchain/Truora-Service/blob/main/contracts/)合约。


   ```
      function request() public returns (bytes32)
        {
          // default return type is INT256
          //returnType = ReturnType.STRING;


          // Set your URL
          // url = "plain(https://www.random.org/integers/?num=100&min=1&max=100&col=1&base=10&format=plain&rnd=new)";
             url = "json(https://api.exchangerate-api.com/v4/latest/CNY).rates.JPY";
             bytes32  requestId = oracleQuery(oracleCoreAddress, url, timesAmount);
             validIds[requestId] = true;
             return requestId;
              
        }
   ```

  - 必须实现 **__callback(bytes32 _requestId, bytes memory _result)** 方法，用于`Truora-Service`服务回调获取的结果。

  - **get()** 方法获取本次请求结果, 可自行修改此函数, 获取结果后进行自己业务逻辑的计算。  
  
----------

```eval_rst
.. admonition:: **URL格式规范**
    
    目前支持json和text/plain两种访问格式。并且链下API的url建议支持HTTPS访问(安全因素考虑)。  
    遵循jsonpath格式，子元素 用 "." 表示,数组用 "[]"表示。
     
    text/plain默认取第一行，也可指定数组下标取特定行。 jsonpath规范可以参考 `jsonpath <https://support.smartbear.com/alertsite/docs/monitors/api/endpoint/jsonpath.html>`_ 
    
     //获取链下随机数API
       plain(https://www.random.org/integers/?num=100&min=1&max=100&col=1&base=10&format=plain&rnd=new)
     //获取人民币对日元汇率API 
       json(https://api.exchangerate-api.com/v4/latest/CNY).rates.JPY  
     // 查询某城市某天最高温度  
       json(https://devapi.qweather.com/v7/weather/3d?location=101280601&key=90d8a8ee98ff495694dce72e96f53a18).daily[1].tempMax
``` 




### 获取VRF随机数

用户可以参考 [RandomNumberSampleVRF.sol](https://github.com/WeBankBlockchain/Truora-Service/blob/main/contracts/1.0/sol-0.6/oracle/simple-vrf/RandomNumberSampleVRF.sol) 合约实现自己的oracle业务合约,
  默认支持`solidity0.6`版本合约。 `solidity0.4` 在 `Truora-Service` 同级目录。合约解析如下：
  - 用户合约需继承 `VRFClient` 合约
   ```
    contract RandomNumberSampleVRF is VRFClient
   ``` 

  - 构造函数需要传入指定的Truora服务方的 `VRFCore`合约地址和公钥哈希值。地址和哈希值都可以通过前端界面或者后端接口获取。
   ```
      constructor(address _vrfCore, bytes32 _keyHash) public {
             vrfCoreAddress = _vrfCore;
             keyHash = _keyHash;
         }
   ```       
  - 设定自己提供的随机数种子值。 
  
   ```
       function getRandomNumber(uint256 userProvidedSeed) public returns (bytes32 ) {
            bytes32  requestId =  vrfQuery(vrfCoreAddress, keyHash, userProvidedSeed);
            validIds[requestId] = true;
            return requestId;
        }
   ```

  - 必须实现 **__callbackRandomness(bytes32 requestId, uint256 randomness)** 方法，用于 `Truora-Service`服务回调获取的结果。
  - **get()** 方法获取本次随机数请求结果, 可自行修改此函数, 获取结果后进行自己业务逻辑的计算。  
  
   

## 业务合约参考

下面以一个简单抽奖合约为例，介绍下一个简单抽奖业务怎么使用 Truora 预言机合约。
    
 抽奖合约[LotteryOracle.sol](https://github.com/WeBankBlockchain/Truora-Service/blob/main/contracts/1.0/sol-0.6/oracle/LotteryOracle.sol) 实现了一个简单的抽奖逻辑，
 通过使用上述[APISampleOracle.sol](https://github.com/WeBankBlockchain/Truora-Service/blob/main/contracts/1.0/sol-0.6/oracle/FiscoOracleClient.sol) 获取随机数结果。请保证 `APISampleOracle` 合约的url是获取获取随机数的url。
      默认支持`solidity0.6`版本合约。 `solidity0.4`自行修改合约第一行的编译器版本即可。合约解析如下：
      
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
     
 `V1.1.0`版本已加入通过VRF产生链上安全可验证随机数方案，用户也可参考
 [LotteryOracleUseVrf.sol](https://github.com/WeBankBlockchain/Truora-Service/blob/main/contracts/1.0/sol-0.6/oracle/simple-vrf/LotteryOracleUseVrf.sol) 抽奖逻辑大部分相同，只是获取随机数获取方式从 `api` 方式改成 `vrf` 方式。
     
## fiscoOracleClient 合约解析

  - 抽象合约，__callback方法待实现。
  
```
function __callback(bytes32 requestId, int256 result) public {}
```

   - 发起`oracle`请求，`oracleQuery` 函数会传入相关参数并调用 `oracleCore` 合约的 `query`方法。

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
  
## VRFClient 合约解析

  - 抽象合约，__callback方法待实现。
  
```
 function __callbackRandomness(bytes32 requestId, uint256 randomness) internal virtual;
```

   - 发起`oracle`随机数请求，`vrfQuery` 函数会传入相关参数并调用 `VRFCore` 合约的 `randomnessRequest`方法。
   为了保证用户提供的种子足够随机，`randomnessRequest`函数会把用户种子 （`_consumerSeed`）, 预言机服务方公钥哈希 （`_keyHash）`, 
   用户合约地址 （`_sender`） ,用户合约发送请求次数（`nonce`），区块哈希（`blockhash`）一起做哈希处理得出最终`VRF`随机数种子。  

```  
   function randomnessRequest(
      bytes32 _keyHash,
      uint256 _consumerSeed,
      address _sender) external returns(bool) {
      // record nonce
      uint256 nonce = nonces[_keyHash][_sender];
      // preseed
      uint256 preSeed = makeVRFInputSeed( _keyHash, _consumerSeed, _sender, nonce);
  
      bytes32 requestId = makeRequestId(chainId, groupId, _keyHash, preSeed);
      // Cryptographically guaranteed by preSeed including an increasing nonce
      assert(callbacks[requestId].callbackContract == address(0));
      callbacks[requestId].callbackContract = _sender;
      callbacks[requestId].seedAndBlockNum = keccak256(abi.encodePacked(
          preSeed, block.number));
      emit RandomnessRequest(address (this), _keyHash, preSeed, block.number,
        _sender, requestId, callbacks[requestId].seedAndBlockNum, _consumerSeed);
      nonces[_keyHash][_sender] = nonces[_keyHash][_sender].add(1);
      return true;
    }
```

  - VRF随机数验证逻辑可以参考 `VRFCore`的`getRandomnessFromProof` 方法。
  
