# 积分抽奖
  本项目结合VRF以及BAC001，实现一个区块链积分抽奖功能。
## 一、前提
* 已部署客户端的预言机业务合约（须继承Fiscooracleclient），并记录合约地址
* BAC001合约已部署，并记录地址
* 相关用户拥有足够的BAC001，并且允许本合约从自己的账户下转走部分BAC001（不低于抽奖合约指定的量）

## 二、函数说明

* 构造函数

  调用合约的构造函数部署抽奖合约，需要传入两个参数：
  - `randomOracle`: 已部署客户端的预言机业务合约的地址，用于获取随机数
  - `bac001Address`: BAC001合约的地址

* start_new_lottery

  由主持人添加所有参与者进来，并开启一个新的抽奖环节，需要传两个参数：

  - `_players`: 参与者的公钥地址数组
  - `_amount`: 本轮抽奖需要押注的bac001数量

* deposit

  参与者确认自己要参与抽奖，确认后会自动转数额为`constant_bac001_count`的bac001到合约账户下


* stop_deposit

  由主持人结束本轮抽奖的押注，结束后未押注的用户，不能再押注


* pickWinner

  确定胜出者，并将奖池（合约账户）下的所有bac001转给胜出者



## 三、使用说明
### 参与成员说明
* 主持人：`weiwei`
* 参与抽奖成员：`alice`、`bob`
* 提供随机数服务的`OracleCore`合约
* 用于与OracleCore交互并获取随机数结果的客户端实现`APISampleOracle`
* 用于抽奖质押的BAC001合约
* 提供抽奖服务的合约`LotteryBac001Oracle`

### 预准备
* 搭建预言机服务或者从服务供应商获取，得到`OracleCore`合约地址，如：`0xa60a49b75ee98a19939a786d57cd2a9802e4984b`
* 用`OracleCore`合约地址作为初始化参数，部署预言机客户端合约`APISampleOracle`，记录该客户端合约的地址，如：`0x89ad9c94646182534482c7f1277c61aed8c22883`
* 部署BAC001合约，给参与抽奖的人员转入足够的bac001,并记录该合约的地址，如：`0x470e30fbd66fdc02e0dbf08e0cdd9f6cdb068c8a`
* 主持人部署抽奖合约`LotteryBac001Oracle`,参数是以上得到的`APISampleOracle`和BAC001合约地址

### 抽奖步骤

* 主持人调用抽奖合约`LotteryBac001Oracle`的函数`start_new_lottery`
  - `_players`：本轮抽奖入所有参与者
  - `amount`： 本轮抽奖需要质押的bac001数量
 
* 所有参与者分别在bac001合约中调用`approve`函数,允许抽奖合约`LotteryBac001Oracle`从自己账户下转走部分bac001

* 参与者调用抽奖合约`LotteryBac001Oracle`的`deposit`函数确定参与抽奖，这一步操作后，参与者账户下的bac001将会自动转到抽奖合约地址中，额度为本轮抽奖指定的数量

* 主持人调用抽奖合约`LotteryBac001Oracle`的`stop_deposit`函数停止本轮抽奖，参与者不能继续质押了

* 主持人调用抽奖合约`LotteryBac001Oracle`的`pickWinner`函数，根据随机数计算出本轮获胜者，并将所有质押的bac001转给胜出者