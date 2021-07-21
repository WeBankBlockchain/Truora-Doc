# 积分抽奖
  本项目结合 `Turora` 实现一个区块链积分抽奖的Sample，以演示随机数的使用方法，给开发者做为参考实现。
  有两种实现方案，基于 `Turora` 链下 `API` 方式获取随机数，以及基于 `Turora` 的`VRF`方式获取链下随机数。
  
  **体验此功能前，请阅读 [Truora 开发教程](https://truora.readthedocs.io/zh_CN/latest/docs/develop/quick-start.html#id2)**

## 业务流程
   1 主持人开始一轮抽奖，初始化此轮抽奖每个参与者需要的积分数量，以及初始化参与此次抽奖的用户（地址）；  

   2 参与抽奖的用户存入积分；   

   3 主持人关闭积分存入功能，并向预言机发起随机数请求；  

   4 主次人开奖。根据预言机获取的随机数，对参赛选手人数取余，得到中奖者的地址，将积分发送给中奖者。  


## 合约函数说明
   基于Truora API 方式的抽奖合约[LotteryBacOracle](https://github.com/WeBankBlockchain/Truora-Service/blob/dev/contracts/1.0/sol-0.6/BAC/lottery/LotteryBacOracle.sol)   

   基于Truora VRF 方式的抽奖合约[LotteryBacUseVrf](https://github.com/WeBankBlockchain/Truora-Service/blob/dev/contracts/1.0/sol-0.6/BAC/lotteryUseVrf/LotteryBacUseVrf.sol)   

   原理基本类似，我们以 `Truora API` 方式的抽奖合约作为讲解。
* 构造函数

  调用合约的构造函数部署抽奖合约，需要传入两个参数：
  - `randomOracle`: 已部署客户端的随机数合约的地址，用于获取随机数
  - `bac001Address`: BAC001合约的地址

* start_new_lottery

  由主持人添加所有参与者进来，并开启一个新的抽奖环节，需要传两个参数：

  - `_players`: 参与者的公钥地址数组
  - `_amount`: 本轮抽奖需要押注的bac001数量

* deposit

  参与者确认自己要参与抽奖，确认后会自动转数额为`amount`的bac001到合约账户下


* stop_deposit

  由主持人结束本轮抽奖的押注，结束后未押注的用户，不能再押注


* pickWinner

  确定胜出者，并将奖池（合约账户）下的所有bac001转给胜出者



## 开发教程

### 前提
* 已部署randomOracle合约，此合约提供随机数功能；
* BAC001合约已部署，并记录地址;
* 相关用户拥有足够的BAC001，并且允许本合约从自己的账户下转走部分BAC001（不低于抽奖合约指定的量）;

### 参与成员说明
* 主持人：`weiwei`
* 参与抽奖成员：`alice`、`bob`
* 提供随机数服务的`randomOracle`合约
* 用于抽奖质押的 `BAC001` 合约
* 提供抽奖服务的合约`LotteryBacOracle`

### 预准备
* 搭建预言机服务或者从服务供应商获取，得到`OracleCore`合约地址，如：`0xa60a49b75ee98a19939a786d57cd2a9802e4984b`
* 用`OracleCore`合约地址作为初始化参数，部署预言机客户端合约`APISampleOracle`，记录该客户端合约的地址，如：`0x89ad9c94646182534482c7f1277c61aed8c22883`
* 部署BAC001合约，给参与抽奖的人员转入足够的bac001,并记录该合约的地址，如：`0x470e30fbd66fdc02e0dbf08e0cdd9f6cdb068c8a`
* 主持人部署抽奖合约`LotteryBacOracle`,参数是以上得到的`APISampleOracle`和BAC001合约地址

### 抽奖步骤

* 主持人调用抽奖合约`LotteryBacOracle`的函数`start_new_lottery`。
  - `_players`：本轮抽奖入所有参与者
  - `amount`： 本轮抽奖需要质押的bac001数量
 
* 所有参与者分别在bac001合约中调用`approve`函数,允许抽奖合约`LotteryBacOracle`从自己账户下转走部分bac001。

* 参与者调用抽奖合约`LotteryBacOracle`的`deposit`函数确定参与抽奖，这一步操作后，参与者账户下的bac001将会自动转到抽奖合约地址中，额度为本轮抽奖指定的数量。

* 主持人调用抽奖合约`LotteryBacOracle`的`stop_deposit`函数停止本轮抽奖，参与者不能继续质押了。

* 主持人调用抽奖合约`LotteryBacOracle`的`pickWinner`函数，根据随机数计算出本轮获胜者，并将所有质押的bac001转给胜出者。