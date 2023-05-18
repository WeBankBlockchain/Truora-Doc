## Restful接口列表

访问这些Restful接口的入口列表，可以在服务启动后，用浏览器或curl直接访问索引页查看列表：


```
http://localhost:5022/truora/index
```

**（localhost地址根据实际部署的IP或域名替换）**

以下URL接口的访问方式如无特殊说明均为`GET`

<span id="server_version" />

### 查询 Truora 版本


```
http://localhost:5022/truora/version
```


#### 响应内容示例



```json
{
  "code": 0,
  "message": "success",
  // 当前 Truora-Service 版本
  "data": "v1.1.0",
  "totalCount": 0
}

```


<span id="list_group" />


### 列出连接的底层链和群组接口



```
http://localhost:5022/truora/chain/group/list
```


#### 响应内容示例


```json
{
  "code": 0,
  "message": "success",
  "data": [
    {
      "platform": "fiscobcos2",
      "chainId": "1",
      "groupIdList": [
        "1"
      ]
    },
    {
      "platform": "fiscobcos3",
      "chainId": "chain0",
      "groupIdList": [
        "group0"
      ]
    }
  ],
  "totalCount": 2
}


```

### Ping检测底层链和群组连通性接口

查询 Truora 服务所连接的底层链列表，并发起一次连通性检测(获取区块高度和基本信息）


```
http://localhost:5022/truora/chain/group/ping
```

#### 响应内容示例


```json
{
  "code": 0,
  "message": "success",
  "data": [
    {
      "name": "FISCOBCOS2_Chain1_Group1",
      "nodeVersion": {
        "Build Time": "20210201 10:03:03",
        "Build Type": "Linux/clang/Release",
		...
      },
      "BlockNumber": 3082
    },
    {
      "name": "FISCOBCOS3_chain0_group0",
      "groupInfo": {
	    "chainID": "chain0",
        "groupID": "group0",
        "genesisConfig": {
			...
      },
      "BlockNumber": 1805
    }
  ],
  "totalCount": 2
}


```

<span id="list_oracle_address" />

### OracleCore 合约地址查询接口


查询 OracleCore 合约地址，用户编写自定义合约时使用

for FISCOBCOS2:


```
http://localhost:5022/truora/oracle/address?chainId=1&groupId=1
```

for FISCOBCOS3


```
http://localhost:5022/truora/oracle/address?chainId=chain0&groupId=group0
```

下同，不单独分列。


#### 请求参数

| **参数名**   | **类型** | **必填**| **默认值**  | **说明**|
| ------ | -------------- | ------------ |:--------:| --------- |
| chainId | String  |  否    | 1 (for fisco bcos 2.x) or  chain0 (for fisco bcos3.x)  |  链编号      |
| groupId | String  |     否      | 1 (for fisco bcos 2.x) or group0 (for fisco bcos3.x)    | 群组编号       |


#### 响应内容示例


```json
{
  "code": 0,
  "message": "success",
  "data": [
    {
      // OracleCore 合约版本
      "oracleCoreVersion": "v1.1.0",
      // VRFCore 合约版本
      "vrfCoreVersion": "v1.1.0",
      // 链 ID
      "chainId": 1,
      // 群组 ID
      "group": 1,
      // OracleCore 合约地址
      "oracleCoreContractAddress": "0x1769c87d9889eee2352c9cfe61dd3ac22a80f7a7",
      // VRFCore 合约地址
      "vrfContractAddress": "0x28b2a1b9ead2e4cf6a7cff9372ee6da70152e484",
      "fromBlock": "latest",
      "toBlock": "latest"
    }
    .....
  ],
  "totalCount": 0
}

```



### 查询历史请求列表


```
http://localhost:5022/truora/history/list?pageNumber=1&pageSize=10&chainId=1&groupId=1&hideResult=false
```

HTTP GET

#### 请求参数

| **参数名**   | **类型** | **必填**| **默认值**  | **说明**|
| ------ | -------------- | ------------ |:--------:| --------- |
| chainId | String  |  否    | 1      |  链编号      |
| groupId | String  |     否      | 1      | 群组编号       |
| hideResult | boolean  |否 | `true` | `true`: 不返回 `result` 结果；<br />`false`: 返回    |
| pageNumber | int  |     否      | 1      | 第几页     |
| pageSize | int  |     否      | 10，最大不超过 20    | 每页条数     |

#### 响应内容示例



```json
{
  "code": 0,
  "message": "success",
  "data": [
    // 多条记录
    {// sourceType == 0 时，表示链下 API 获取数据
      "id": 1,
      //请求唯一编号
      //查询详情时使用的 requestId 字段
      "reqId": 
"0x9f32a5e56608fd730f7ef8bc42efdc53142753a77e46287246ed2a9a39ed1994",
      
      // 链编号和群组编号
      "chainId": 1,
      "groupId": 1,
      
      // Core 合约的版本号
      "oracleVersion": "v1.1.0",
      
      // 请求类型
      //  0 API 获取链下数据
      "sourceType": 0,
      
      // URL 请求求地址和数据响应格式
      "reqQuery": "json(https://api.exchangerate-api.com/v4/latest/CNY).rates.JPY",
      
      // 请求状态
      // 0 成功
      // 非 0，失败
      "reqStatus": 0,
      // 请求状态非 0 时的错误信息
      "error": "",
      
      // 发起调用的合约地址
      "userContract": "0x919c4e3c50a074dbd15e6a832bc146cd288cebf4",
      
      // 请求耗时（ms）
      "processTime": 2460,
      
      // hideResult 为 true，不返回该字段
      // hideResult 为 false，返回
      // URL 返回的结果
      "result": "15.962067",
      
      // 放大倍数，Truora-Service 会将 URL 返回的结果 乘以 该倍数后上传到链上
      // 防止小数
      "timesAmount": "1000000000000000000",
      
      "createTime": "2020-12-11 18:48:28",
      "modifyTime": "2020-12-11 18:48:31"
    },
    {// sourceType == 1 时，表示 VRF，获取链上随机数
      "id": 53,
      //请求唯一编号
      //查询详情时使用的 requestId 字段
      "reqId": "0x223c4a975b25b235b0f3e9830e1099cdfefeb7dcffa839b860fccd12a6cd0a51",
       // 链编号和群组编号
      "chainId": 1,
      "groupId": 1,
      
      // Core 合约的版本号
      "oracleVersion": "v1.1.0",
      
      //请求类型
      // 1 VRF 获取可验证随机数
      "sourceType": 1,
      "blockNumber": 146,
      "reqQuery": "",
      "needProof": false,
      
      // 请求状态
      // 0 成功
      // 非 0，失败
      "reqStatus": 0,
      // 请求状态非 0 时的错误信息
      "error": "",

      // 发起调用的合约地址
      "userContract": "0xdd48ff8868b76dab43bd13402840bcb8c5a82853",
      
      // 处理时长
      "processTime": 495,
      
      "createTime": "2021-03-22 16:28:56",
      "modifyTime": "2021-03-22 16:28:56",
      "inputSeed": "1",
      "actualSeed": "BF18046A6A8801B4ABED36169F93D1C0613768EC0EDCC827A31D973D3CBE98B4"
    },
    ......
  ],
  // 总行数
  "totalCount": 1
}

```


### 查询单个请求详情


```
http://localhost:5022/truora/history/query/{requestId}
```


#### 请求参数

| **参数名**   | **类型** | **必填**| **默认值**  | **说明**|
| ------ | -------------- | ------------ |:--------:| --------- |
| requestId | String  |  是    |   |  请求编号，添加到 URL 中    |

**注意：requestId 对应历史请求列表中返回记录的 `reqId` 字段**

#### 响应内容示例



```json
{
  "code": 0,
  "message": "success",
  "data": {
    "id": 1,
    //请求唯一编号
    //查询详情时使用的 requestId 字段
    "reqId": "0x9f32a5e56608fd730f7ef8bc42efdc53142753a77e46287246ed2a9a39ed1994",
    
    // 链编号和群组编号
    "chainId": 1,
    "groupId": 1,
    
    // Core 合约的版本号
    "oracleVersion": "v1.1.0",
    
    // 请求类型
    // 0 链下 API 获取数据
    // 1 VRF 获取可验证随机数
    "sourceType": 1,
    
    // sourceType == 0 时，表示：URL 请求地址和数据响应格式
    // sourceType == 1 时，不使用
    "reqQuery": "json(https://api.exchangerate-api.com/v4/latest/CNY).rates.JPY",

    // 请求状态
    // 0 成功
    // 非 0，失败
    "reqStatus": 0,
    // 请求状态非 0 时的错误信息
    "error": "",
    
    // 发起调用的合约地址
    "userContract": "0x919c4e3c50a074dbd15e6a832bc146cd288cebf4",
    
    // 请求耗时（ms） 
    "processTime": 2460,
    
    // hideResult 为 true，不返回该字段
    // hideResult 为 false，返回
    // sourceType == 0 时，表示：URL 返回的结果
    // sourceType == 1 时，表示：VRF 生成的随机数，0x 开头 16 进制
    "result": "15.962067",
    
    // 放大倍数，防止小数
    // sourceType == 0 时使用，Truora-Service 会将 URL 返回的结果 乘以 该倍数后上传到链上
    "timesAmount": "1000000000000000000",
    
    // sourceType == 1 时使用，VRF 生成的可验证随机数的证明
    "proof": "0211c851316725ffba00e7eae4a06327214e282803379a7521ade5bbd0dc3a4ba5c69f06f471fd2349e907ca28df48ba975fb0d6c1f18e8449dbc00b6216c73ed018bc59c45d906c9cab14ee7c9af7727c"
    // sourceType == 1 时使用，用户提供的随机数种子
    "inputSeed": "1",
    // sourceType == 1 时使用，生成随机数时使用的实际种子（包含块高 hash）
    "actualSeed": "1298E2D458FA77C078D42B4B3B54441D56E4A13ECDE436E31A4899BB0D1A3D74"
    
    // 额外字段
    "createTime": "2020-12-11 18:48:28",
    "modifyTime": "2020-12-11 18:48:31"
  },
  "totalCount": 0
}

```



### Dapp体验接口

示例Dapp，可体验获取一些预置数据源的结果，预置的URL参见配置文件`application_dapps.yml`,以及返回的响应数据

GeneralOracle是一个比较通用的示例合约，支持访问指定的外部数据源，接受`UINT256、STRING，BYTES`几种数据，客户端用`GeneralResult`对象封装返回值

合约代码在项目的`contracts`路径下可以获取。

JAVA客户端代码参见`com.webank.truora.dapps.GeneralOracleClient`



#### 部署GeneralOracle合约

```
http://localhost:5022/truora/dapps/deploy
```

##### 返回数据示例

其中的`dappContractAddress`字段值，可以用来后续调用

```
{
  "code": 105000,
  "message": "system exception",
  "data": [
    "chain0:group0",
    "oracleCoreAddress = 0x32e3bdfadee312f8f5e073e170915a5b00b20cb6",
    "dappContractAddress = 0xc058225ec31ed87b62e6a5846c7f72f9ac4e71e8"
  ],
  "totalCount": 3
}
```

#### 发起调用


```
http://localhost:5022/truora/dapps/get
```


--------

**支持的参数**

`url=[n]` : 访问配置文件`application_dapps.yml`里预置的多个URL里的第n个，不指定时默认选择首个。 

`address=[0x...]` : 使用上一小节 deploy 接口部署得到的合约地址`dappContractAddress`，如果不指定，且配置文件里也没配置合约地址，则每次调用均动态创建

`input=[字符串]` : 将字符串附加到请求数据源的url上，注意：仅对`http://localhost:5022/truora/source/text?input=sampletext`这个示例数据源生效。

以上参数均可以为空。完整的url示例:

```
http://localhost:5022/truora/dapps/get?url=1&address=0xc058225ec31ed87b62e6a5846c7f72f9ac4e71e8&input=sampletext
```

实际调用时注意替换

--------


可先访问这个URL，列出已经配置的数据源和相关参数都有哪几个：

```
http://localhost:5022/truora/dapps/list
```

示例，url=x对应上面列出来的数据源序号，从1开始 (不指定url参数则默认为0)：


```
http://localhost:5022/truora/dapps/get?url=1
```


```
http://localhost:5022/truora/dapps/get?url=2
```


```
http://localhost:5022/truora/dapps/get?url=3
```


```
http://localhost:5022/truora/dapps/get?url=4&input=sampletext
```


#### 返回数据示例


```
{
  "code": 0,
  "message": "success",
  "data": [
    "chain0:group0",
    "oracleCoreAddress = 0x32e3bdfadee312f8f5e073e170915a5b00b20cb6",
    "dappContractAddress = ",
    "url = http://localhost:5022/truora/source/rand",
    "retValue = [ Type : INT256 ; Valid : true ; Result : 3508 ]"
  ],
  "totalCount": 5
}

```

### 预置的数据源

这些数据源作为“桩”，供快速体验，不要用于真实的应用环境

#### 返回一个json，rates数组里包含模拟汇率

```
http://localhost:5022/truora/source/exchange
```

 对应真实的URL 
```
https://api.exchangerate-api.com/v4/latest/CNY
```

#### 返回多个整形随机数，分多行


```
http://localhost:5022/truora/source/rand
```

对应真实的URL
```
 https://www.random.org/integers/?num=10&min=1&max=1000&col=1&base=10&format=plain&rnd=new
 ```


####  返回示例字符串


**注意只有这个接口支持 input参数**

```
http://localhost:5022/truora/source/text?input=sampletext
```

