# coinsdo-wallet-mcp


## MCP服务
> 基于 [mcp-go](https://github.com/mark3labs/mcp-go) 开发，服务名称 `coinsdo/wallet-mcp-server`。

> ##### 数据路径
> Unix: `/Users/用户名/Library/Application Support/coinsdo/`

## 启动服务
```cmd
service              # 默认：stdio + prod
service -e test      # stdio + test
service -t http      # http + prod
```

```cmd
-e  运行环境 (test, prod) [默认: prod]
-t  传输方式 (stdio, http) [默认: stdio]
-p  HTTP 端口（仅 http 模式）[默认: 8080]
```

## 客户端调用

### 一、HTTP 模式

#### 1. 初始化，获取 Session ID

##### Request
```cmd

curl -i -X POST http://localhost:8080/mcp \
  -H "Content-Type: application/json" \
  -d '{
    "jsonrpc": "2.0",
    "id": 0,
    "method": "initialize",
    "params": {
      "protocolVersion": "2025-11-25",
      "clientInfo": {
        "name": "curl-client",
        "version": "1.0.0"
      }
    }
  }'
```

##### Response
```text
Header 响应头包含 Mcp-Session-Id
```
```json
{
  "Mcp-Session-Id": "mcp-session-49803ae2-5173-486b-9a4b-97458755ed42"
}
```
```json
{
  "jsonrpc": "2.0",
  "id": 0,
  "result": {
    "protocolVersion": "2025-11-25",
    "capabilities": {
      "logging": {},
      "tools": {
        "listChanged": true
      },
      "tasks": {
        "list": {},
        "cancel": {},
        "requests": {
          "tools": {
            "call": {}
          }
        }
      }
    },
    "serverInfo": {
      "name": "coinsdo/wallet-mcp-server",
      "version": "V1.0.0"
    }
  }
}
```

#### 2. 列出工具（携带 Session ID）
##### Request
```cmd
curl -X POST http://localhost:8080/mcp \
  -H "Content-Type: application/json" \
  -H "Mcp-Session-Id: mcp-session-49803ae2-5173-486b-9a4b-97458755ed42" \
  -d '{
    "jsonrpc": "2.0",
    "id": 1,
    "method": "tools/list",
    "params": {}
  }'
```
##### Response
```json
{
  "jsonrpc": "2.0",
  "id": 1,
  "result": {
    "tools": [
      {
        "annotations": {
          "readOnlyHint": false,
          "destructiveHint": true,
          "idempotentHint": false,
          "openWorldHint": true
        },
        "description": "User login or sign in",
        "inputSchema": {
          "type": "object",
          "properties": {
            "userPassword": {
              "type": "string",
              "description": "Required, User password"
            }
          },
          "required": [
            "userPassword"
          ],
          "additionalProperties": false
        },
        "name": "user_login",
        "outputSchema": {
          "additionalProperties": false,
          "properties": {
            "serviceName": {
              "description": "MCP service name",
              "type": "string"
            },
            "serviceVersion": {
              "description": "MCP service version",
              "type": "string"
            },
            "dbVersion": {
              "description": "Database version",
              "type": "string"
            }
          },
          "required": [
            "serviceName",
            "serviceVersion",
            "dbVersion"
          ],
          "type": "object"
        }
      },
      {},
      {},
      {}
    ]
  }
}
```


## Tool 工具

* Header 请求头需携带 Session ID
* 仅 status 不需登录可访问


### 1、查看状态
> status

#### Request
```json
{
  "jsonrpc": "2.0",
  "id": 1,
  "method": "tools/call",
  "params": {
    "name": "status"
  }
}
```

#### Response

| 字段              | 类型     | Null | 示例值                                                   | 描述                       |
|-----------------|--------|------|-------------------------------------------------------|--------------------------|
| transportType   | string | 否    | `stdio` `http`                                        | Transport type           |
| environmentName | string | 否    | `dev` `test` `prod`                                   | Environment name         |
| serviceName     | string | 否    | `coinsdo/wallet-mcp-server`                           | MPC钱包服务名称                |
| serviceVersion  | string | 否    | `V1.0.0`                                              | MPC钱包服务版本                |
| dbVersion       | string | 否    | `1.0.0+100`                                           | 当前数据库版本                  |
| latestDbVersion | string | 否    | `1.0.3+130`                                           | 最新数据库版本                  |
| mainBaseUrl     | string | 否    | `https://merchant.coinsdo.com/coinsdo/wallet`         | Mainnet api base url     |
| testBaseUrl     | string | 否    | `https://merchant.coinsdotest.com/coinsdo/wallet`     | Testnet api base url     |
| teeBaseUrl      | string | 否    | `https://merchant.coinsdotest.com/coinsdo/tee`        | Mpc service api base url |
| authBaseUrl     | string | 否    | `https://merchant.coinsdotest.com/coinsdo/coinwallet` | Abi service api base url |



```json
{
  "jsonrpc": "2.0",
  "id": 1,
  "result": {
    "content": [
      {
        "type": "text",
        "text": "{\"transportType\":\"http\",\"environmentName\":\"test\",\"serviceName\":\"coinsdo/wallet-mcp-server\",\"serviceVersion\":\"V1.0.0\",\"dbVersion\":\"2.0.30+1424\",\"latestDbVersion\":\"2.0.30+1424\",\"mainBaseUrl\":\"https://merchant.coinsdo.com/coinsdo/wallet\",\"testBaseUrl\":\"https://merchant.coinsdotest.com/coinsdo/wallet\",\"teeBaseUrl\":\"https://merchant.coinsdotest.com/coinsdo/tee\",\"authBaseUrl\":\"https://merchant.coinsdotest.com/coinsdo/coinwallet\"}"
      }
    ],
    "structuredContent": {
      "transportType": "http",
      "environmentName": "test",
      "serviceName": "coinsdo/wallet-mcp-server",
      "serviceVersion": "V1.0.0",
      "dbVersion": "2.0.30+1424",
      "latestDbVersion": "2.0.30+1424",
      "mainBaseUrl": "https://merchant.coinsdo.com/coinsdo/wallet",
      "testBaseUrl": "https://merchant.coinsdotest.com/coinsdo/wallet",
      "teeBaseUrl": "https://merchant.coinsdotest.com/coinsdo/tee",
      "authBaseUrl": "https://merchant.coinsdotest.com/coinsdo/coinwallet"
    }
  }
}
```

### 2、登录或注册
> user_login

#### Request
```json
{
  "jsonrpc": "2.0",
  "id": 1,
  "method": "tools/call",
  "params": {
    "name": "status",
    "arguments": {
      "password": "123456"
    }
  }
}
```

| 字段           | 类型     | 必填 | 示例值         | 描述   |
|--------------|--------|----|-------------|------|
| userPassword | string | 是  | `123456789` | 用户密码 |

#### Response

| 字段             | 类型     | Null | 示例值                         | 描述         |
|----------------|--------|------|-----------------------------|------------|
| serviceName    | string | 否    | `coinsdo/wallet-mcp-server` | MPC钱包服务名称  |
| serviceVersion | string | 否    | `V1.0.0`                    | MPC钱包服务版本号 |
| dbVersion      | string | 否    | `1.0.0+100`                 | DB数据库版本号   |



### 2、加入MPC钱包
> join_mpc_wallet

#### Request

| 字段             | 类型     | 必填 | 示例值         | 描述   |
|----------------|--------|----|-------------|------|
| invitationCode | string | 是  | `WERWSF`    | 邀请码  |
| custodianName  | string | 是  | `Hermes AI` | AI名称 |

#### Response

| 字段         | 类型     | Null | 示例值          | 描述   |
|------------|--------|------|--------------|------|
| walletId   | int    | 否    | `12`         | 钱包ID |
| walletName | string | 否    | `MPC-Wallet` | 钱包名称 |




### 3、离开MPC钱包
>leave_mpc_wallet

#### Request

| 字段       | 类型  | 必填 | 示例值  | 描述   |
|----------|-----|----|------|------|
| walletId | int | 是  | `12` | 钱包ID |

#### Response

| 字段         | 类型     | Null | 示例值          | 描述   |
|------------|--------|------|--------------|------|
| walletId   | int    | 否    | `12`         | 钱包ID |
| walletName | string | 否    | `MPC-Wallet` | 钱包名称 |





### 4、领取MPC钱包分片
> claim_key_shard

#### Request

| 字段       | 类型  | 必填 | 示例值  | 描述   |
|----------|-----|----|------|------|
| walletId | int | 是  | `12` | 钱包ID |

#### Response

| 字段         | 类型     | Null | 示例值          | 描述   |
|------------|--------|------|--------------|------|
| walletId   | int    | 否    | `12`         | 钱包ID |
| walletName | string | 否    | `MPC-Wallet` | 钱包名称 |



### 5、查询MPC钱包列表
> get_wallets

#### Response - []

| 字段         | 类型     | Null | 示例值          | 描述                          |
|------------|--------|------|--------------|-----------------------------|
| walletId   | int    | 否    | `12`         | 钱包ID                        |
| walletName | string | 否    | `MPC-Wallet` | 钱包名称                        |
| netType    | int    | 否    | `2`          | 网络类型, 1. Testnet 2. Mainnet |
| status     | string | 否    | `ONLINE`     | 钱包状态 `ONLINE`               |




### 6、获取MPC钱包信息
> get_wallet

#### Request

| 字段       | 类型  | 必填 | 示例值  | 描述   |
|----------|-----|----|------|------|
| walletId | int | 是  | `12` | 钱包ID |


#### Response

| 字段         | 类型     | Null | 示例值          | 描述                          |
|------------|--------|------|--------------|-----------------------------|
| walletId   | int    | 否    | `12`         | 钱包ID                        |
| walletName | string | 否    | `MPC-Wallet` | 钱包名称                        |
| netType    | int    | 否    | `2`          | 网络类型, 1. Testnet 2. Mainnet |
| status     | string | 否    | `ONLINE`     | 钱包状态 `ONLINE`               |






### 7、获取支持链列表
> get_chains

#### Request

| 字段       | 类型  | 必填 | 示例值 | 描述                        |
|----------|-----|----|-----|---------------------------|
| netType  | int | 是  | `2` | 网络类型, 1.Testnet 2.Mainnet |
| nftState | int | 否  | `0` | 是否支持NFT 0.All 1.支持 2.不支持  |


#### Response - []

| 字段              | 类型     | Null | 示例值                                                                        | 描述                          |
|-----------------|--------|------|----------------------------------------------------------------------------|-----------------------------|
| currencyId      | int    | 否    | `23`                                                                       | 链ID,也是链的主币ID                |
| netType         | int    | 否    | `2`                                                                        | 网络类型, 1. Testnet 2. Mainnet |
| baseSymbol      | string | 否    | `ETH` `POL` `BNB` `DOT` `ETH(ARB1)`                                        | 主币名称                        |
| abbreviation    | string | 否    | `ETH` `POL` `BSC` `DOT` `ARB1`                                             | 链名名称                        |
| networkFullName | string | 否    | `Ethereum` `Polygon` `Binance Smart Chain` `Polkadot Asset Hub` `Arbitrum` | 链名全称                        |



### 8、搜索支持链列表
> search_chains

#### Request - []

| 字段       | 类型     | 必填 | 示例值                                                | 描述                        |
|----------|--------|----|----------------------------------------------------|---------------------------|
| netType  | int    | 是  | `2`                                                | 网络类型, 1.Testnet 2.Mainnet |
| currency | string | 否  | `ETH` `Ethereum` `MATIC` `POL` `Polygon` `Binance` | 模糊搜索字符串                   |


#### Response
* 同 `7、获取支持链列表`







### 9、获取支持协议列表
> get_protocols

#### Request

| 字段       | 类型  | 必填 | 示例值 | 描述                        |
|----------|-----|----|-----|---------------------------|
| netType  | int | 是  | `2` | 网络类型, 1.Testnet 2.Mainnet |
| nftState | int | 否  | `0` | 是否支持NFT 0.All 1.支持 2.不支持  |


#### Response - []

| 字段              | 类型     | Null | 示例值                                                                        | 描述                          |
|-----------------|--------|------|----------------------------------------------------------------------------|-----------------------------|
| protocolId      | int    | 否    | `34`                                                                       | 协议ID                        |
| netType         | int    | 否    | `2`                                                                        | 网络类型, 1. Testnet 2. Mainnet |
| currency        | string | 否    | `ERC20`                                                                    | 协议名称                        |
| abbreviation    | string | 否    | `ETH` `POL` `BSC` `DOT` `ARB1`                                             | 链名名称                        |
| networkFullName | string | 否    | `Ethereum` `Polygon` `Binance Smart Chain` `Polkadot Asset Hub` `Arbitrum` | 链名全称                        |
| nftState        | int    | 否    | `1`                                                                        | 是否支持NFT 1.支持 2.不支持          |


### 10、搜索支持协议列表
> search_protocols

#### Request

| 字段       | 类型     | 必填 | 示例值                      | 描述                        |
|----------|--------|----|--------------------------|---------------------------|
| netType  | int    | 是  | `2`                      | 网络类型, 1.Testnet 2.Mainnet |
| currency | string | 否  | `ETH` `Ethereum` `ERC20` | 链名称或协议名称, 模糊搜索字符串         |


#### Response - []
* 同 `9、获取支持协议列表`







### 11、获取支持币种列表
> get_currencies

#### Request

| 字段       | 类型  | 必填 | 示例值 | 描述                        |
|----------|-----|----|-----|---------------------------|
| netType  | int | 是  | `2` | 网络类型, 1.Testnet 2.Mainnet |

#### Response - []

| 字段               | 类型     | 示例值                                                | 描述                        |
|------------------|--------|----------------------------------------------------|---------------------------|
| currencyId       | int    | `45`                                               | 币种ID                      |
| netType          | int    | `2`                                                | 网络类型, 1.Testnet 2.Mainnet |
| currencyType     | int    | `1` `3`                                            | 币种类型, 1.主币, 3.代币          |
| currency         | string | `ETH` `ERC20`                                      | 主币/代币.主币名称/协议名称           |
| flag             | string | `ETH` `0xdAC17F958D2ee523a2206206994597C13D831ec7` | 主币/代币.主币名称/合约地址           |
| currencyName     | string | `ETH` `USDT`                                       | 币种名称                      |
| currencyFullName | string | `ETH` `Tether USD`                                 | 币种全称                      |
| abbreviation     | string | `ETH`                                              | 链名名称                      |
| networkFullName  | string | `Ethereum`                                         | 链名全称                      |



### 12、搜索支持币种列表
> search_currencies

#### Request

| 字段           | 类型     | 必填 | 示例值              | 描述                        |
|--------------|--------|----|------------------|---------------------------|
| netType      | int    | 是  | `2`              | 网络类型, 1.Testnet 2.Mainnet |
| currency     | string | 否  | `ETH` `Ethereum` | 链名称或主币名称，最小长度2，模糊搜索       |
| flag         | string | 否  | `0xdAC17F`       | 合约地址，最小长度6，模糊搜索           |
| currencyName | string | 是  | `ETH` `USDT`     | 币种名称，最小长度2，模糊搜索           |

#### Response - []
* 同 `11、获取支持币种列表`



### 13、添加代币币种
> add_currency

#### Request

| 字段         | 类型     | 必填 | 示例值                                          | 描述        |
|------------|--------|----|----------------------------------------------|-----------|
| protocolId | int    | 是  | `34`                                         | 协议ID      |
| flag       | string | 是  | `0xdAC17F958D2ee523a2206206994597C13D831ec7` | 合约地址或代币ID |


#### Response
* 同 `11、获取支持币种列表`








### 14、获取NFT项目列表
> get_nft_project

#### Request

| 字段       | 类型  | 必填 | 示例值 | 描述                        |
|----------|-----|----|-----|---------------------------|
| netType  | int | 是  | `2` | 网络类型, 1.Testnet 2.Mainnet |

#### Response - []

| 字段               | 类型     | 示例值                                                | 描述                        |
|------------------|--------|----------------------------------------------------|---------------------------|
| currencyId       | int    | `45`                                               | 币种ID                      |
| netType          | int    | `2`                                                | 网络类型, 1.Testnet 2.Mainnet |
| currency         | string | `ETH` `ERC20`                                      | 主币/代币.主币名称/协议名称           |
| flag             | string | `ETH` `0xdAC17F958D2ee523a2206206994597C13D831ec7` | 主币/代币.主币名称/合约地址           |
| abbreviation     | string | `ETH`                                              | 链名名称                      |
| networkFullName  | string | `Ethereum`                                         | 链名全称                      |
| nftState         | int    | `2`                                                | 1.NFT项目 2.普通币种            |
| projectName      | string | `Blokyz`                                           | NFT项目名称                   |
| totalSupply      | string | `9996`                                             | NFT总供应量                   |



### 15、搜索NFT项目列表
> search_nft_project

#### Request

| 字段          | 类型     | 必填 | 示例值              | 描述                        |
|-------------|--------|----|------------------|---------------------------|
| netType     | int    | 是  | `2`              | 网络类型, 1.Testnet 2.Mainnet |
| currency    | string | 否  | `ETH` `Ethereum` | 链名称或主币名称，最小长度2，模糊搜索       |
| flag        | string | 否  | `0xdAC17F`       | 合约地址，最小长度6，模糊搜索           |
| projectName | string | 是  | `Blokyz`         | NFT项目名称，最小长度2，模糊搜索        |

#### Response - []
* 同 `14、获取NFT项目列表`



### 16、添加NFT项目
> add_nft_project

#### Request

| 字段         | 类型     | 必填 | 示例值                                          | 描述        |
|------------|--------|----|----------------------------------------------|-----------|
| protocolId | int    | 是  | `34`                                         | 协议ID      |
| flag       | string | 是  | `0x84270A00a77D45ae007D2b0897F05b872ad1704F` | 合约地址或代币ID |

#### Response
* 同 `14、获取NFT项目列表`









### 17、获取钱包地址列表
> get_addresses

#### Request

| 字段         | 类型  | 必填 | 示例值  | 描述              |
|------------|-----|----|------|-----------------|
| walletId   | int | 是  | `12` | 钱包ID            |
| chainId    | int | 否  | `23` | 链ID             |
| currencyId | int | 否  | `45` | 币种ID            |
| pageIndex  | int | 否  | `1`  | 页码，从1开始，默认1     |
| pageSize   | int | 否  | `20` | 每页条数，默认20，最大100 |

#### Response - []

| 字段               | 类型     | Null | 示例值                                                | 描述                          |
|------------------|--------|------|----------------------------------------------------|-----------------------------|
| addressId        | int    | 否    | `102`                                              | 地址ID                        |
| walletId         | int    | 否    | `12`                                               | 钱包名称                        |
| currencyId       | int    | 否    | `45`                                               | 钱包名称                        |
| addressStr       | String | 否    | `0x7412f0ef024d6be473703c677e502ba1f7c18e2c`       | 钱包地址, 或EOS和VAULTA链账户        |
| addressPath      | String | 否    | `m/44'/60'/0'/0/0`                                 | 钱包路径                        |
| netType          | int    | 否    | `2`                                                | 网络类型, 1. Testnet 2. Mainnet |
| currencyType     | int    | 否    | `1` `3`                                            | 币种类型, 1.主币, 3.代币            |
| currency         | string | 否    | `ETH` `ERC20`                                      | 主币/代币.主币名称/协议名称             |
| flag             | string | 否    | `ETH` `0xdAC17F958D2ee523a2206206994597C13D831ec7` | 主币/代币.主币名称/合约地址             |
| currencyName     | string | 否    | `ETH` `USDT`                                       | 币种名称                        |
| currencyFullName | string | 否    | `ETH` `Tether USD`                                 | 币种全称                        |
| abbreviation     | string | 否    | `ETH`                                              | 链名名称                        |
| networkFullName  | string | 否    | `Ethereum`                                         | 链名全称                        |



### 18、搜索地址列表
> search_addresses

#### Request

| 字段           | 类型     | 必填 | 示例值      | 描述                                |
|--------------|--------|----|----------|-----------------------------------|
| addressStr   | string | 是  | `c18e2c` | 钱包地址, 或EOS和VAULTA链账户, 最小长度6, 模糊搜索 |
| currencyId   | int    | 否  | `45`     | 币种ID                              |
| currencyType | int    | 否  | `1` `3`  | 币种类型, 0.默认全部 1.主币, 3.代币           |

#### Response - []
* 同 `17、获取钱包地址列表`




### 19、添加钱包地址
> add_address

#### Request

| 字段         | 类型  | 必填 | 示例值  | 描述              |
|------------|-----|----|------|-----------------|
| walletId   | int | 是  | `12` | 钱包ID            |
| currencyId | int | 否  | `45` | 币种ID            |

#### Response

| 字段      | 类型     | Null | 示例值 | 描述    |
|---------|--------|------|-----|-------|
| success | int    | 否    | `0` | 成功数量  |
| existed | int    | 否    | `0` | 已存在数量 |
| failed  | int    | 否    | `0` | 失败数量  |
| pending | String | 否    | `1` | 待审批数量 |









### 20、获取地址余额
> get_address_balance

#### Request

| 字段         | 类型  | 必填 | 示例值   | 描述   |
|------------|-----|----|-------|------|
| addressId  | int | 是  | `102` | 地址ID |

#### Response

| 字段           | 类型     | Null | 示例值                                          | 描述                          |
|--------------|--------|------|----------------------------------------------|-----------------------------|
| netType      | int    | 否    | `2`                                          | 网络类型, 1. Testnet 2. Mainnet |
| addressId    | int    | 是    | `102`                                        | 地址ID                        |
| addressStr   | int    | 是    | `0x7412f0ef024d6be473703c677e502ba1f7c18e2c` | 地址字符串                       |
| currencyType | int    | 否    | `1` `3`                                      | 币种类型, 1.主币, 3.代币            |
| currency     | string | 否    | `ETH` `ERC20`                                | 主币/代币.主币名称/协议名称             |
| currencyName | string | 否    | `ETH` `USDT`                                 | 币种名称                        |
| balance      | string | 否    | `1.435`                                      | 余额                          |


### 21、查询地址余额
> query_address_balance

#### Request

| 字段         | 类型     | 必填 | 示例值                                          | 描述                   |
|------------|--------|----|----------------------------------------------|----------------------|
| addressStr | string | 是  | `0x7412f0ef024d6be473703c677e502ba1f7c18e2c` | 钱包地址, 或EOS和VAULTA链账户 |
| currencyId | int    | 否  | `45`                                         | 币种ID                 |


#### Response

| 字段           | 类型     | Null | 示例值                                          | 描述                          |
|--------------|--------|------|----------------------------------------------|-----------------------------|
| netType      | int    | 否    | `2`                                          | 网络类型, 1. Testnet 2. Mainnet |
| addressId    | int    | 是    | `102`                                        | 地址ID, 如果地址未添加则值为0           |
| addressStr   | int    | 是    | `0x7412f0ef024d6be473703c677e502ba1f7c18e2c` | 地址字符串                       |
| currencyType | int    | 否    | `1` `3`                                      | 币种类型, 1.主币, 3.代币            |
| currency     | string | 否    | `ETH` `ERC20`                                | 主币/代币.主币名称/协议名称             |
| currencyName | string | 否    | `ETH` `USDT`                                 | 币种名称                        |
| balance      | string | 否    | `1.435`                                      | 余额                          |






### 22、发送普通交易
> send_transaction

#### Request

| 字段        | 类型     | 必填 | 示例值                                          | 描述                                                         |
|-----------|--------|----|----------------------------------------------|------------------------------------------------------------|
| addressId | int    | 是  | `102`                                        | 发送地址ID                                                     |
| toAddress | string | 是  | `0x7412f0ef024d6be473703c677e502ba1f7c18e2c` | 收币地址                                                       |
| amount    | string | 是  | `0.01`                                       | 发送数量                                                       |
| memo      | string | 否  | `123456`                                     | [xrp, eos, trx, trc20, sol, spl, algo, atom]有Memo、[xrp]需数字 |
| comment   | string | 否  | `send test`                                  | 交易说明                                                       |

#### Response

| 字段            | 类型     | Null | 示例值                                                                  | 描述                             |
|---------------|--------|------|----------------------------------------------------------------------|--------------------------------|
| netType       | int    | 否    | `2`                                                                  | 网络类型, 1. Testnet 2. Mainnet    |
| bundleId      | string | 否    | `1967806516108304386`                                                | 交易ID                           |
| status        | string | 否    | `PENDING_APPROVAL`                                                   | 交易状态                           |
| txHash        | string | 否    | `0x73562577af5d7b8b5fd86a4686acf4998d7a6689a7477c34d1b4dc40859b2a41` | 交易Hash，仅审批通过后有值                |
| amount        | string | 否    | `0.01`                                                               | 发送数量, 仅普通转币                    |
| toAddress     | string | 否    | `1967806516108304386`                                                | 收币地址                           |
| creatorName   | string | 否    | `tait`                                                               | 发起交易者                          |
| fromBundleId  | string | 否    | `1967806516108304385`                                                | 被加速的交易ID,如果有表示当前是加速记录          |
| retryBundleId | string | 否    | `1967806516108304387`                                                | 重试交易ID，如果有表示当前记录被重试发起新交易       |
| currencyType  | int    | 否    | `1` `3`                                                              | 币种类型, 1.主币, 3.代币               |
| currency      | string | 否    | `ETH` `ERC20`                                                        | 主币/代币.主币名称/协议名称                |
| flag          | string | 否    | `ETH` `0xdAC17F958D2ee523a2206206994597C13D831ec7`                   | 主币/代币.主币名称/合约地址                |
| currencyName  | string | 否    | `ETH` `USDT`                                                         | 币种名称                           |
| ProjectName   | string | 否    | `JJet`                                                               | NFT项目名称, 仅NFT交易                |
| TokenId       | string | 否    | `1`                                                                  | NFT Token ID, 仅NFT交易           |
| TokenAmount   | string | 否    | `10`                                                                 | NFT Token 发送数量, 仅ERC1155-NFT交易 |



### 23、发送NFT交易
> send_nft_transaction

#### Request

| 字段          | 类型     | 必填 | 示例值                                          | 描述        |
|-------------|--------|----|----------------------------------------------|-----------|
| addressId   | int    | 是  | `102`                                        | 发送地址ID    |
| projectId   | int    | 是  | `56`                                         | NFT项目ID   |
| toAddress   | string | 是  | `0x7412f0ef024d6be473703c677e502ba1f7c18e2c` | 收币地址      |
| tokenId     | string | 是  | `0.01`                                       | 发送数量      |
| tokenAmount | string | 否  | `10`                                         | 仅1155协议需要 |
| comment     | string | 否  | `test send`                                  | 交易说明      |

#### Response
* 同 `22、发送普通交易`





### 24、获取交易列表
> get_transactions

#### Request

| 字段        | 类型     | 必填 | 示例值                                                                                     | 描述              |
|-----------|--------|----|-----------------------------------------------------------------------------------------|-----------------|
| walletId  | int    | 是  | `12`                                                                                    | 钱包ID            |
| addressId | int    | 否  | `102`                                                                                   | 地址ID            |
| txType    | string | 否  | `TEE_CRYPTO_TRANSACTION` `TEE_CONTRACT_TRANSACTION_NFT` `TEE_CONTRACT_TRANSACTION_DAPP` | 交易类型            |
| status    | string | 否  | `PENDING_APPROVAL` `TIMEOUT` `APPROVED` `REJECTED` `ERROR` `REVOKED`                    | 交易状态            |
| pageIndex | int    | 否  | `1`                                                                                     | 页码，从1开始，默认1     |
| pageSize  | int    | 否  | `20`                                                                                    | 每页条数，默认20，最大100 |

#### Response - []

```text
{
   "transaction": transaction, // Transaction info
   "accelerates": accelerates  // Transaction accelerate data
}
```

交易信息: transaction
* 同 `22、发送普通交易`

加速记录: accelerates - []

| 字段       | 类型     | Null | 示例值                   | 描述   |
|----------|--------|------|-----------------------|------|
| bundleId | string | 否    | `1967806516108304386` | 交易ID |
| status   | string | 是    | `PENDING_APPROVAL`    | 交易状态 |



### 25、获取交易信息
> get_transaction

#### Request

| 字段       | 类型     | 必填 | 示例值                   | 描述   |
|----------|--------|----|-----------------------|------|
| bundleId | string | 否  | `1967806516108304386` | 交易ID |

#### Response

```text
{
   "transaction": transaction, // Transaction info
   "accelerates": accelerates  // Transaction accelerate data
}
```