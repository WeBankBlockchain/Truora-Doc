# BAC002 合约规范

## 简介
 BAC002 是区块链上定义非同质化资产的一种标准，可以用于唯一性资产类型，如房产、汽车、道具、版权等。，并可以做相应增发，销毁，暂停合约，黑白名单等权限控制。此标准方便用户针对业务场景发行自己的区块链资产，屏蔽智能合约的开发，直接基于 bac.jar 进行 Java 开发即可
## 三个基本元素
- description

  资产的具体描述

- shortName

  资产简称

- assetId

  资产编号

 ## 五个基本行为
- 发行

  调用合约的 deploy 方法，传入 description 和 shortName，即在区块链上发行指定名称的资产

- 转账

  调用 safeSendFrom 方法实现转账，调用 balance 方法可以查看自己的资产数量

- 增发

  调用 issueWithAssetURI 方法向资产地址增发指定资产编号和资产描述链接信息的资产。另外，可以通过 addIssuer 增加 有权限增发资产的人，也可以通过 renounceIssuer 方法移除增发权限

- 销毁

  调用 destory 以及 destoryFrom 销毁自己地址下资产和特定地址下的资产

- 暂停

  遇到紧急状况，你可以调用 suspend 方法，暂停合约，这样任何人都不能调用 send 函数。故障修复后，可以调用 unSuspend 方法解除暂停。也可以通过 addSuspender 和 renounceSuspender 相应增加和移除暂停者权限


## 接口说明

- <b>shortName()</b>

  资产简称

- <b>description()</b>

  资产描述

- <b>balance(address owner)</b>

  返回 owner 的资产总数

- <b>totalSupply()</b>

  获得当前合约总的资产数目

- <b>ownerOf(uint256 assetId)</b>

  返回资产持有者的地址

- <b>approve(address to, uint256 assetId)</b>

  授予地址to具有指定资产的控制权

  - 此方法配合 getapproved 使用

- <b>getApproved(uint256 assetId)</b>

  获得资产授权的地址用户

  - 此方法配合 approve 使用，注意不要配合 setapprovealforall 方法使用

- <b>setApprovalForAll(address operator, bool approved)</b>

  授予地址operator具有自己所有资产的控制权

- <b>isApprovedForAll(address owner, address operator)</b>

  查询授权

- <b>sendFrom(address from, address to, uint256 assetId, bytes memory data)</b>

  安全转账，防止你转到错误的合约地址 ( to如果是合约地址，必须实现接收接口 BAC002Holder 才可以接收转账 )，并可以带转账备注

  - suspend 状态下无法执行此操作

- <b>batchSendFrom(address from, address[] to, uint256[] assetId, bytes memory data)</b>

  批量安全转账

  - suspend 状态下无法执行此操作
  - to 数组元素个数需要和 assetid 数组元素个数一致

- <b>issueWithAssetURI(address to, uint256 assetId, string memory assetURI, bytes data)</b>

  给地址 to 创建资产 assetId，data 是转账备注, assetURI  资产描述

- <b>isIssuer(address account)</b>

  检查account是否有增加资产的权限

- <b>addIssuer(address account)</b>

  使地址 account 拥有增加资产的权限

- <b>renounceIssuer()</b>

  移除增加资产的权限

- <b>suspend()</b>

  暂停合约

  - suspend 后无法进行 safesendfrom / sendfrom / safeBatchSendFrom 操作

- <b>unSuspend()</b>

  重启合约

  - 此方法配合 suspend 使用

- <b>isSuspender(address account)</b>

  是否有暂停合约权限

  - 此方法配合  addsuspender 使用

- <b>addSuspender(address account)</b>

  增加暂停权限者

  - 此方法配合 renouncesuspender / issuspender 放啊发使用

- <b>renounceSuspender()</b>

  移除暂停权限

- <b>destroy(uint256 assetId, bytes data)</b>

  减少自己的资产，data 是转账备注

  - 调用时，value 值需要小于等于目前自己的资产总量

- <b>assetOfOwnerByIndex(address owner, uint256 index)</b>

  根据索引 index 获取 owner 的资产 ID

- <b>assetByIndex(uint256 index)</b>

  根据索引  index 获取当前合约的资产 ID


 ## 样例代码

  ```
  @Test
  public void testBAC002() throws Exception {

  BigInteger gasPrice = new BigInteger("1");
  BigInteger gasLimit = new BigInteger("2100000000");
  ContractGasProvider contractGasProvider = new StaticGasProvider(gasPrice,gasLimit);
  Credentials credentials =
  Credentials.create("b83261efa42895c38c6c2364ca878f43e77f3cddbc922bf57d0d48070f79feb6");

  Credentials credentialsBob = Credentials.create("2");
  // 生成随机私钥使用下面方法；
  // Credentials credentialsBob =Credentials.create(Keys.createEcKeyPair());
  String Bob = "0x2b5ad5c4795c026514f8317c7a215e218dccd6cf";
  String Owner = "0x148947262ec5e21739fe3a931c29e8b84ee34a0f";
  String Alice = "0x1abc9fd9845cd5a0acefa72e4f40bcfd4136f864";

  // 获取spring配置文件，生成上下文
  ApplicationContext context = new ClassPathXmlApplicationContext("applicationContext.xml");

  Service service = context.getBean(Service.class);
  service.run();
  ChannelEthereumService channelEthereumService = new ChannelEthereumService();
  channelEthereumService.setChannelService(service);

  Web3j web3j = Web3j.build(channelEthereumService, service.getGroupId());

  // 部署合约 即发行资产 需要传入 资产描述和资产简称
  BAC002 bac002 = BAC002.deploy(web3j, credentials, contractGasProvider, "fisco bcos car asset", "TTT").send();
  String contractAddress = bac002.getContractAddress();
  //增加 发行者
  bac002.addIssuer(Alice).send();
  // 新建资产
  bac002.issueWithAssetURI(Alice,new BigInteger("1"),"this is a car information url is www.XXX.com","add for alice".getBytes()).send();
  bac002.issueWithAssetURI(Owner,new BigInteger("2"),"this is a house information url is www.XXX.com","add for Owner".getBytes()).send();
  bac002.issueWithAssetURI(Owner,new BigInteger("3"),"this is a house information url is www.XXX.com","add for Owner".getBytes()).send();

  assertEquals(bac002.ownerOf(new BigInteger("1")).send(),Alice);

  // 销毁资产, owner销毁自己的资产
  bac002.destroy(new BigInteger("2"), "destroy the house asset".getBytes()).send();

  bac002.approve(Bob,new BigInteger("3")).send();

  BAC002 bac002Bob = BAC002.load(contractAddress,web3j,credentialsBob,contractGasProvider);

  //转账 以及转账备注 Bob发交易Owner -> Alice
  bac002Bob.sendFrom(Owner, Alice, new BigInteger("3"),"sell house".getBytes()).send();

  //查询余额
  assertEquals( bac002.balance(Alice).send().toString(),"2");
  assertEquals( bac002.ownerOf(new BigInteger("3")).send(),Alice);

  }
  ```

