# 区块链盲盒`
  本项目结合 `Truora VRF`, `BAC`合约, 交易合约，实现一个区块链盲盒功能，并支持盲盒交易。用户可以参考合约逻辑并做相应修改。
  
## 业务流程
  此业务是一个猫系列的区块链盲盒实现，有7中类型猫可供抽选，类型有 `"American Shorthair", "British Shorthair", "Japanese Bobtail", "Chinese Orange", "Russian Blue", "Persian", "Ragdoll"`；  
  每一个猫盲盒是一个 `BAC002` 非同质化资产（NFT）。用户提供幸运数字（随机数种子）抽取从猫系列中抽取自己的猫盲盒，合约会给用户铸造相应的非同质化资产（NFT）。  

  用户可以选择将所拥有的猫盲盒（NFT资产）进行出售换取对应 `BAC001` 资产，支持定价出售和拍卖出售。

## 合约函数说明
###  盲盒合约

   * 构造函数
     
      调用合约的构造函数部署抽奖合约
      
      - randomOracle: 已部署客户端的VRF预言机业务合约的地址，用于获取随机数
    
   * requestNewBlindboxCat
     
      触发产生一个决定猫类型的随机数，该函数会返回请求编号 `requestId`
      
      - userProvidedSeed：随机数种子，作用于随机数的产生
      - name： 给猫起的名字
    
   * generateBlindBoxCat
   
      根据requestId对应的随机数确定猫的类型，并产生一个该类型的猫

      - requestId：上一步得的请求编号

   * getCatInfo
     
      根据猫的id查询猫的详细信息

      - catId：猫的编号（从0开始）
        

###  定价交易合约
    
   * createNFTAssetAuction
   
      发布一笔定价交易信息，需要以下参数：
      
      - _nft：被交易的nft的地址，跟`nftAssetId`组合能确定一个具体的nft
      - _nftAssetId：被交易的nft的编号，跟`nft`组合能确定一个具体的nft
      - _ftAssetAddress：本次交易愿意接收的BAC001合约地址
      - _price：拍卖价格（需要多少的ft）
      - _duration：本次交易到期时间（毫秒）
   
   * purchaseNFTAsset
   
      认购别人发布的nft
      
      - _nft: nft合约地址
      - _nftAssetId： nft编号
   
   * cancelAution
   
      取消之前发布的定价拍卖信息
      
      - _nft: nft合约地址
      - _nftAssetId： nft编号
   
   
   * getNftAssetAuctionDetails
   
      查询指定nft的价格
      
      - _nft: nft合约地址
      - _nftAssetId： nft编号
   
   
###  拍卖交易合约

   * createNFTAssetAuction
   
      商家发布非定价拍卖信息
      
      - _nft：被交易的nft的地址，跟`nftAssetId`组合能确定一个具体的nft
      - _nftAssetId：被交易的nft的编号，跟`nft`组合能确定一个具体的nft
      - _ftAssetAddress：本次交易愿意接收的BAC001合约地址
      - _price：拍卖价格（需要多少的ft）
      - _duration：本次交易到期时间（毫秒）
   
   
   * bid
   
     客户参与拍卖
     
      - _nft：想要拍取的nft的地址，跟`nftAssetId`组合能确定一个具体的nft
      - _nftAssetId：想要拍取的nft的编号，跟`nft`组合能确定一个具体的nft
      - _amount：自己当前愿意出的ft数额
   
   
   * executeSale

     拍卖活动到期后，商家可以确认生成最终交易，将于出价最高的客户达成交易
     
      - _nft：本轮被拍卖的nft的地址，跟`nftAssetId`组合能确定一个具体的nft
      - _nftAssetId：本轮被拍卖的nft的编号，跟`nft`组合能确定一个具体的nft

   
   * cancelAution

     拍卖到期之前活动，商家可以取消本次交易
     
      - _nft：本轮被拍卖的nft的地址，跟`nftAssetId`组合能确定一个具体的nft
      - _nftAssetId：本轮被拍卖的nft的编号，跟`nft`组合能确定一个具体的nft
     
     
   * getNftAssetAuctionDetails
   
      查询指定nft的价格
      
      - _nft: nft合约地址
      - _nftAssetId： nft编号



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
   2. David调用该合约的函数`createNFTAssetAuction`发布一笔拍卖信息
      - _nft：被交易的bac002的合约地址，即`CatBlindbox`合约地址(因为该合约继承了BAC002)
      - _nftAssetId：被交易的 BAC002 的编号，即`catId`(猫的编号)
      - _ftAssetAddress：本次交易愿意接收的ft合约地址
      - _price：拍卖价格（需要多少的BAC001）
      - _duration：本次交易到期时间（毫秒）



#### 步骤三：客户参与拍卖这个猫

   1. 由Tom部署ft合约`BAC001`
   2. Tom分别给客户（Alice、Bob）转一定额度的 `BAC001`
   3. 所有参与拍卖的客户，需要调BAC001合约的`approve`函数，允许非定价拍卖合约从自己的账户下转走一定额度的BAC001
      - spender: 允许这个地址从本人账下转走BAC001，此处填`AuctionUnfixedPrice`合约地址
      - value: 允许目标地址从自己账下转走BAC001的数量
   4. 客户（Alice、Bob）依次调非定价拍卖合约`AuctionUnfixedPrice`的函数`bid`参与拍卖竞价
   5. 拍卖活动到期后，商家（David）调非定价拍卖合约`AuctionUnfixedPrice`的函数 `executeSale`结束本轮竞价，本轮出价最高的用户将与商家达成交易。

