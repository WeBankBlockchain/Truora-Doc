# BAC001 合约规范

## 简介
 BAC001 是区块链上定义同质化资产的一种标准，可以用于在区块链发行同质化数字资产，数字权益，数字凭证等，比如积分，卡券，投票，并可以做相应增发，销毁，暂停合约，黑白名单等权限控制。此标准方便用户针对业务场景发行自己的区块链资产，屏蔽智能合约的开发，直接基于bac.jar进行Java开发即可。

## 四个基本元素

- description 

  此资产的具体描述

- shortName 

  资产简称

- minUnit 

  资产最小单位

- totalAmount 

  资产总数量

## 五个基本行为: 

- 发行

  调用合约的 deploy 方法，传入你初始化的四个元素即可，即在区块链上发行了你指定总量和名称的资产。

  - 其中 minUnit 和 totalAmount 不能为负数或小数

- 转账

  调用 send 方法即可实现转账，之后调用 balance 方法可以查看自己的资产余额

- 增发

  调用 issue 方法特定地址增发资产， 并可以通过 addIssuer 增加有权限增发资产的人，也可以通过renounceIssuer 方法移除增发权限

- 销毁

  调用 destory 以及 destoryFrom 销毁自己地址下资产和特定地址下的资产

- 暂停

  遇到紧急状况，你可以调用 suspend 方法，暂停合约，这样任何人都不能调用 send 函数。故障修复后，可以调用 unSuspend 方法解除暂停。也可以通过 addSuspender 和 renounceSuspender 相应增加和移除暂停者权限


## 接口说明

- <b>totalAmount()</b>

  返回资产总量

  - 这里的资产总量需要计算最小转账单位，所以实际返回值为   totalAmount * 10<sup>minUnit</sup> 

- <b>balance(address owner)</b>

  返回owner的帐户的资产余额

- <b>send(address to, uint256 value , bytes data)</b>

  将数量为value的资产转入地址 to 并触发 transfer 事件, data 是转账备注,to如果是合约地址，必须实现接收接口 BAC001Holder 才可以接收转账。

  - suspend 状态下无法进行此操作
  - 请避免 to 为自身进行操作

- <b>sendFrom(address from,address to,uint256 value，bytes  data))</b>

  将地址 from 中的 value 数量的资产转入地址 to ，并触发 transfer 事件，data 是转账备注。to如果是合约地址，必须实现接收接口 BAC001Holder 才可以接收转账。

  - 方法的调用者可以不为 from， 此时需要预先进行 approve 授权

  - from 不能为调用者自身地址，否则会报错
  - suspend 状态下无法执行此操作

- <b>batchSend( address[] to, uint256[]  values, bytes  data)</b>

  批量将自己账户下的资产转给 to 数组的地址， to 和 values 的个数要一致

  - suspend 状态下无法执行此操作

- <b>approve(address spender,uint256 value)</b>

  允许 spender 从自己账户提取限额 value 的资产

  - 此方法配合 sendfrom / safesendfrom 一起使用
  - 重复授权时，最终授权额度为最后一次授权的值

- <b>allowance(address owner, address spender)</b>

  返回 spender 可从 owner 提取的资产数量上限

  - 此方法配合 approve 一起使用

- <b>increaseAllowance(address spender, uint256 addedValue)</b>

  允许 spender 提取的资产上限在原有基础上增加 addedValue

  - 此方法配合 approve 使用

- <b>decreaseAllowance(address spender, uint256 subtractedValue)</b>

  允许 spender  提取的资产上限在原有基础上减少 subtractedValue

  - 此方法配合 approve 使用

- <b>minUnit()</b>

  资产最小单位

- <b>shortName()</b>

  资产简称

- <b>description()</b>

  资产描述

- <b>destory(uint256 value， bytes  data)</b>

  减少自己的资产，data 是转账备注

  - 调用时，value 值需要小于等于目前自己的资产总量

- <b>destroyFrom(address from, uint256 value， bytes  data)</b>

  减少地址 from 资产，data 是转账备注

  - 调用此方法时，需要配合 approve 进行使用

- <b>issue(address to, uint256 value，bytes  data)</b>

  给地址 to 增加数量为 value 的资产，data 是转账备注

- <b>isIssuer(address account)</b>

  检查 account 是否有增加资产的权限

- <b>addIssuer(address account)</b>

  使地址 account 拥有增加资产的权限

- <b>renounceIssuer()</b>

  移除增加资产的权限

- <b>suspend()</b>

  暂停合约

  - suspend 后无法进行 send / safesendfrom / sendfrom / safeBatchSend / approves 操作

- <b>unSuspend()</b>

  重启合约
  
- <b>suspended</b>

  判断合约是否处于暂停状态

- <b>isSuspender(address account)</b>

  是否有暂停合约权限

  - 配合 suspend 方法一起使用

- <b>addSuspender(address account)</b>

  增加暂停权限者

  - 配合 suspend 方法一起使用

- <b>renounceSuspender()</b>

  移除暂停权限

  - 配合 suspend / addSuspender 方法使用


### 样例代码
  参考测试案例在 **Truora-Service** 测试目录 [BAC001Test](https://github.com/WeBankBlockchain/Truora-Service/blob/dev/src/test/java/com/webank/oracle/test/transaction/bac/BAC001/BAC001Test.java) 下
```
 @Test
    public void testBAC001() throws Exception {

      BigInteger gasPrice = new BigInteger("1");
      BigInteger gasLimit = new BigInteger("2100000000");
      ContractGasProvider contractGasProvider = new StaticGasProvider(gasPrice,gasLimit);
      //根据私钥导入账户
      Credentials credentials = Credentials.create("b83261efa42895c38c6c2364ca878f43e77f3cddbc922bf57d0d48070f79feb6");
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

      // 部署合约 即发行资产 资产描述：fisco bcos car asset; 资产简称 TTT; 最小转账单位 1 ;发行总量 10000000;
        BAC001 bac001 = BAC001.deploy(web3j, credentials, contractGasProvider, "GDX car asset", "TTT",BigInteger.valueOf(1), BigInteger.valueOf(1000000)).send();
        String contractAddress = bac001.getContractAddress();
          //增加 发行者
        bac001.addIssuer(Alice).send();

       // 增发资产
        bac001.issue(Alice, new BigInteger("10000"),"increase 10000 asset  ".getBytes()).send();
        // 销毁资产, owner销毁自己的资产
        bac001.destroy(new BigInteger("10000"), "destroy 10000 asset".getBytes()).send();
       //转账 以及转账备注 Owner -> Alice
        bac001.send(Alice,new BigInteger("10000"),"dinner money".getBytes()).send();
      //Owner批准Bob可以从自己账户转走1000TTT
        bac001.approve(Bob, new BigInteger("10000")).send();
        //Bob开始转走Owner 1000TTT ，需要先根据credentialsBob 重新load合约；
        BAC001 bac001Bob = BAC001.load(contractAddress,web3j,credentialsBob,contractGasProvider);
        bac001Bob.sendFrom(Owner,Alice,new BigInteger("10000"),"dddd".getBytes()).send();
       //查询余额
       assertEquals( bac001.balance(Alice).send().toString(),"30000");
        assertEquals( bac001.balance(Owner).send().toString(),"9970000");

    }

}

```


