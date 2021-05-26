# 结合Truora和BAC001实现抽奖抽奖功能

## 一、前提
* OracleCore合约已部署，并记录地址
* BAC001合约已部署，并记录地址
* 相关用户拥有足够的BAC001，并且允许BAC001合约从自己的账户下转走部分资产（不低于抽奖合约指定的量）

## 二、函数说明

* 构造函数

  调用合约的构造函数部署抽奖合约，需要传入两个参数：
  - `randomOracle`: OracleCore合约的地址，用于获取随机数
  - `bac001Address`: BAC001合约的地址
  
  
* add_players

  由主持人添加所有参与者进来，不被添加的用户不能参与抽奖，需要传一个参数：
  - `_players`: 参与者的公钥地址数组
  
* start_new_lottery
  
  开启一个新的抽奖环节，此时允许各个参与者确认参与抽奖
  
  
* constant_bac001_count

  查询每次参与参与抽奖需要投入多少bac001
  
  
* confirm_join

  参与者确认自己要参与抽奖，确认后会自动转数额为`constant_bac001_count`的bac001到合约账户下

* pickWinner

  确定胜出者，并将奖池（合约账户）下的所有bac001转给胜出者
  
  * winner
  
  每轮抽奖结束后，可以查询本轮的胜出者