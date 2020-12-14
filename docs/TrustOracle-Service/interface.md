## Oracle-Service 接口文档

### OracleCore 合约地址查询接口

#### 接口描述

> 查询 OracleCore 合约地址，用户编写自定义合约时使用

#### 接口URL

**http://localhost:5012/Oracle-Service/oracle/address?chainId=1&groupId=1**

#### 调用方法

HTTP GET

#### 请求参数

**1）参数表**

| **参数名**   | **类型** | **必填**| **默认值**  | **说明**|
| ------ | -------------- | ------------ |:--------:| --------- |
| chainId | int  |  否    | 1      |  链编号      |
| groupId | int  |     否      | 1      | 群组编号       |


**2）数据格式**
无
#### 响应参数

**1）数据格式** 

```json
{
  "code": 0,
  "message": "success",
  "data": [
    {
      "chainId": 1,
      "group": 1,
       // OracleCore 合约地址
      "oracleCoreContractAddress": "0x5088ccf022a31bc8aec2bc50dd7a6af53b6da213",
      "fromBlock": "latest",
      "toBlock": "latest",
      "operator": "operator1",
      "url": "http://localhost:5000"
    }
  ],
  "totalCount": 0
}
```



### TrustOracle-Service 查询历史请求列表

#### 接口描述

> 查询所有的历史请求记录

#### 接口URL

**http://localhost:5012/Oracle-Service/history/list?pageNumber=1&pageSize=10&chainId=1&groupId=1&hideResult=false**

#### 调用方法

HTTP GET

#### 请求参数

**1）参数表**

| **参数名**   | **类型** | **必填**| **默认值**  | **说明**|
| ------ | -------------- | ------------ |:--------:| --------- |
| chainId | int  |  否    | 1      |  链编号      |
| groupId | int  |     否      | 1      | 群组编号       |
| hideResult | boolean  |否 | `true` | `true`: 不返回 `result` 结果；<br />`false`: 返回    |
| pageNumber | int  |     否      | 1      | 第几页     |
| pageSize | int  |     否      | 10，最大不超过 20    | 每页条数     |


**2）数据格式**
无
#### 响应参数

**1）数据格式** 

```json
{
  "code": 0,
  "message": "success",
  "data": [
    // 多条记录
    {
      "id": 1,
      //请求唯一编号
      //查询详情时使用的 requestId 字段
      "reqId": 
"0x9f32a5e56608fd730f7ef8bc42efdc53142753a77e46287246ed2a9a39ed1994",
      
      // 链编号和群组编号
      "chainId": 1,
      "groupId": 1,
      
      // OracleCore 合约的版本号
      "oracleVersion": 4000,
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
      
      // 放大倍数，TrustOracle-Service 会将 URL 返回的结果 乘以 该倍数后上传到链上
      // 防止小数
      "timesAmount": "1000000000000000000",
      
      "createTime": "2020-12-11 18:48:28",
      "modifyTime": "2020-12-11 18:48:31"
    },
    
    ......
  ],
  // 总行数
  "totalCount": 1
}
```


### TrustOracle-Service 查询单个请求详情

#### 接口描述

> 查询单个请求详情

#### 接口URL

**http://localhost:5012/Oracle-Service/history/query/{requestId}**

#### 调用方法

HTTP GET

#### 请求参数

**1）参数表**

| **参数名**   | **类型** | **必填**| **默认值**  | **说明**|
| ------ | -------------- | ------------ |:--------:| --------- |
| requestId | String  |  是    |   |  请求编号，添加到 URL 中    |

**注意：requestId 对应历史请求列表中返回记录的 `reqId` 字段**

**2）数据格式**
无
#### 响应参数

**1）数据格式** 

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
    
    // OracleCore 合约的版本号
    "oracleVersion": 4000,
    
    // URL 请求地址和数据响应格式
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
    
    // 放大倍数，TrustOracle-Service 会将 URL 返回的结果 乘以 该倍数后上传到链上
    // 防止小数
    "timesAmount": "1000000000000000000",
    
    // 额外字段
    "createTime": "2020-12-11 18:48:28",
    "modifyTime": "2020-12-11 18:48:31"
  },
  "totalCount": 0
}
```
