eosd RPC Interface -- eosd RPC接口
---------------------

Describes how to interface with eosd over HTTP RPC. More...

eosd uses a REST RPC interface where plugins can register their own endpoints with the API server. This page will explain how to use some of the APIs to get information about the blockchain and send transactions.

描述如何通过 HTTP RPC 接口与eosd进行交互。详情见下文...


为了使 eosd 通过 REST RPC 接口能与外界进行交互，需要插件将他们自己的端点(`endpoint`)注册到API服务器上。此页将描述如何使用一些接口来获取区块链信息和发送交易。


Chain API Configuration -- 区块 API 配置
---

Before you can query eosd you must first enable an API plugin. To do this add the following line to
your config.ini

在与eosd交互之前，需要先开启 API 插件，在 config.ini 中加入如下行使其生效

```
plugin = eos::chain_api_plugin
```

By default an HTTP server will start on 127.0.0.1:8888; however, you can also change this with the following configureation line:

默认 HTTP服务器的IP地址和端口为 127.0.0.1:8888，你也可以在config.ini中对该配置行进行修改:

```
http-server-endpoint = 127.0.0.1:8888
```

get_info
----------

The get_info RPC command can be found at:

下面开始调用get_info RPC 接口:

```
curl http://127.0.0.1:8888/v1/chain/get_info
```

And it should return something like:

它将返回如下结果：

```
 {
 "head_block_num":449,
 "last_irreversible_block_num": 434,
 "head_block_id":"000001c1a0f072a5ca76831ac6c6e2988efcf162e953eb40046ec2ceca817a9f",
 "head_block_time":"2017-07-18T21:02:24",
 "head_block_producer":"initd",
 "recent_slots":"0000000000000000000000000000000000000000000000000000000000001111",
 "participation_rate":"0.06250000000000000"
 }

```

get_block
----------------

Example get_block Usage

get_block 接口使用示例：

```
curl http://localhost:8888/v1/chain/get_block -X POST -d '{"block_num_or_id":5}'
curl http://localhost:8888/v1/chain/get_block -X POST -d  '{"block_num_or_id":0000000445a9f27898383fd7de32835d5d6a978cc14ce40d9f327b5329de796b}'
```

Example get_block Result

get_block 接口返回结果：
 
```
{
 "previous": "0000000445a9f27898383fd7de32835d5d6a978cc14ce40d9f327b5329de796b",
 "timestamp": "2017-07-18T20:16:36",
 "transaction_merkle_root": "0000000000000000000000000000000000000000000000000000000000000000",
 "producer": "initf",
 "producer_changes": [ ],
 "producer_signature":  "204cb94b3186c3b4a7f88be4e9db9f8af2ffdb7ef0f27a065c8177a5fcfacf876f684e59c39fb009903c0c59220b147bb07f1144df1c65d26c57b534a76dd29073",
"cycles": [ ],
"id":"000000050c0175cbf218a70131ddc3c3fab8b6e954edef77e0bfe7c36b599b1d",
"block_num":5,
"refBlockPrefix":27728114
}

```

push_transaction
------------------

This method expects a transaction in JSON format and will attempt to apply it to the blockchain,
push_transaction

此方法把一个交易封装成 JSON 格式，进而把它提交到区块链。

Success Response
On success it will return HTTP 200 and the transaction ID.

成功返回

如果成功它将返回 HTTP 200状态码和对应的交易 ID。 

```
{
'transaction_id' : "..."
}
```
Just because the transaction is pushed locally does not mean that the transaction has been incorporated into a block.

因为这个交易只是在本地提交，所以并不意味交易已经被打包在区块中。

Error Response

错误返回

If an error occurs it will return either HTTP 400 (Invalid arguments) or 500 (Internal Server Error)

如果有错误发生，将返回 HTTP 400 状态码(参数非法) 或 500 状态码(服务器内部错误)

```
HTTP/1.1 500 Internal Server Error
Content-Length: 1466
...error message...
```

Example push_transaction Usage

push_transaction 示例

----------------------------------
This example assumes a transfer operation. The refBlockNum and refBlockPrefix are provided as a result of /v1/chain/get_block

这个例子构造了一个交易， refBlockNum 和 refBlockPrefix 的值由接口 /v1/chain/get_block 的返回结果得到。

```
curl http://localhost:8888/v1/chain/push_transaction -X POST -d '{"refBlockNum":"5","refBlockPrefix":"27728114","expiration":"2017-07-18T22:28:49","scope":["initb","initc"],"messages":[{"code":"currency","type":"transfer","recipients":["initb","initc"],"authorization":[{"account":"initb","permission":"active"}],"data":"c9252a0000000000050f14dc29000000d00700000000000008454f530000000000"}],"signatures":[],"authorizations":[]}'
```
