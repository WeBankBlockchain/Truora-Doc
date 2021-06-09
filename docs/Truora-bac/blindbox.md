# 区块链盲盒`
  本项目结合VRF以及BAC002，实现一个区块链盲盒功能。
  
## 主要功能

* requestNewBlindboxCat
  
  触发产生一个决定猫类型的随机数，该函数会返回请求编号requestId
  
  - userProvidedSeed：随机数种子，作用于随机数的产生
  - name： 给猫起的名字

* generateBlindBoxCat

  根据requestId对应的随机数确定猫的类型，并产生一个该类型的猫
  
  - requestId：上一步得的请求编号

* getCatInfo

  根据猫的id查询猫的详细信息
  
  - catId：猫的编号（从0开始）

## 使用说明


### 1、合约部署
* 部署用于产生随机数的合约[RandomNumberSampleVRF](https://github.com/WeBankBlockchain/Truora-Service/blob/main/contracts/1.0/sol-0.6/oracle/simple-vrf/RandomNumberSampleVRF.sol)，并记录合约地址
* 部署盲盒合约CatBlindbox,并记录合约地址

### 2、盲盒抽奖
* 调用函数`requestNewBlindboxCat`，生成一个随机数，并记录该接口的返回结果`requestId`
* 调用函数`generateBlindBoxCat`,这个函数将会根据上一步生成的随机数计算出猫的类型，最终返回这个新猫的信息
* 调用函数`getCatInfo`,查询历次生成的猫信息