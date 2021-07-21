# 区块链盲盒
  本项目结合 `Truora VRF`, 实现一个区块链盲盒功能，并支持盲盒互换。用户可以参考合约逻辑并做相应修改。
  
## 业务流程
  此业务是一个猫系列的区块链盲盒实现，有7中类型猫可供抽选，类型有 `"American Shorthair", "British Shorthair", "Japanese Bobtail", "Chinese Orange", "Russian Blue", "Persian", "Ragdoll"`；  
  每一个猫盲盒都是唯一的，用户提供幸运数字（随机数种子）抽取从猫系列中抽取自己的猫盲盒。  

  用户可以选择将所拥有的猫盲盒进行出售换取对应积分，支持定价出售和拍卖出售。

## 开发教程

### 参与成员说明
* Tom：部署合约`CatBlindbox`、`AuctionUnfixedPrice`、`BAC001`
* David：抽取从盲盒抽奖合约抽得一个猫，并在非定价拍卖合约中拍卖这个猫
* Alice、Bob：参与拍卖的成员
* RandomNumberSampleVRF：提供函数获取 `vrf` 随机数
* BAC001：拍卖猫时，指定的接收的ft类型
* CatBlindbox:提供盲盒猫抽奖的合约
* AuctionUnfixedPrice：该合约提供拍卖功能


### 预准备
* 搭建预言机服务或者从服务供应商获取，得到Truora服务方的 VRFCore合约地址和公钥哈希值
* 以`VRFCore`合约地址和公钥哈希值为构造函数参数，部署用于产生随机数的合约[RandomNumberSampleVRF](https://github.com/WeBankBlockchain/Truora-Service/blob/main/contracts/1.0/sol-0.6/oracle/simple-vrf/RandomNumberSampleVRF.sol)，并记录合约地址
* BAC001合约已部署，并记录地址;
* 相关用户（Alice、Bob）拥有足够的BAC001，并且允许本合约从自己的账户下转走部分BAC001（不低于拍卖合约指定的量）;
* 部署非定价拍卖合约`AuctionUnfixedPrice`
* 部署盲盒猫抽奖合约`CatBlindbox`



### 详细步骤

#### 步骤一：盲盒抽奖得到一只猫

   1. 由Tom部署盲盒猫抽奖合约`CatBlindbox`
   2. David调用该合约的函数`requestNewBlindboxCat`，并得到该函数返回的 `requestId`
   3. David传入`requestId`调用该合约的函数`generateBlindBoxCat`，得到一个随机生成的猫
   4. David调用函数`getCatInfo`,查询历次生成的猫信息


#### 步骤二：在非定价拍卖平台发布拍卖消息

   1. 由Tom部署非定价拍卖的合约`AuctionUnfixedPrice`
   2. David调用该合约的创建函数发布一笔拍卖信息



#### 步骤三：客户参与拍卖这个猫

   1. 由Tom部署积分合约
   2. Tom分别给客户（Alice、Bob）转一定数量的积分
   3. 所有参与拍卖的客户，需要调积分合约的`approve`函数，允许非定价拍卖合约从自己的账户下转走一定数量的积分
      - spender: 允许这个地址从本人账户下转走积分，此处填`AuctionUnfixedPrice`合约地址
      - value: 允许目标地址从自己账下转走积分的数量
   4. 客户（Alice、Bob）依次调非定价拍卖合约`AuctionUnfixedPrice`的函数`bid`参与拍卖竞价
   5. 拍卖活动到期后，商家（David）调非定价拍卖合约`AuctionUnfixedPrice`的函数 `executeSale`结束本轮竞价，本轮出价最高的用户将与商家达成交易。

