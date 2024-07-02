# Documet API  文档API

## Index API

*   增加文档，并指定文档id为1。
*   如果索引不存在，则创建索引。

```shell
curl -X PUT "localhost:9200/twitter/tweet/1?pretty" -d'
{
    "user" : "kimchy",
    "post_date" : "2009-11-15T14:12:12",
    "message" : "trying out Elasticsearch"
}
'
```

*   response

```json
{
    "_shards" : {
        "total" : 2,			 //需要执行的分片总数
        "failed" : 0,			 //执行失败的分片
        "successful" : 2	 //执行成功的分片
    },
    "_index" : "twitter",		
    "_type" : "tweet",
    "_id" : "1",				//文档id
    "_version" : 1,			//版本号，
    "created" : true,
    "result" : created
}
```

*   索引成功的情况下，successful 的值至少为1。

***

### Versioning (版本控制)

每当创建一个文档，或者更新一个文档时会返回一个版本号version。

当我们想再次更新某个文档时，如果想确保此文档在此之前没有被更新过，可以指定号上一次的版本号。

如果指定的版本号与当前文档的版本不一致，则执行失败。

#### 查看id为1的文档的版本号

```shell
curl localhost:9200/twitter/tweet/1
```

*   response

```json
{
    "_index": "twitter",
    "_type": "tweet",
    "_id": "1",
    "_version": 12,		//当前的版本号为12
    "found": true,
    "_source": {
        "user": "kimchy",
        "post_date": "2009-11-15T14:12:12",
        "message": "elasticsearch now has versioning support, double cool!"
    }
}
```

#### 指定版本号，更新文档

```shell
curl -XPUT "http://localhost:9200/twitter/tweet/1?version=12" -d '{
	"message":"我正在更新文档"
}'
```

*   response

```json
{
    "_index": "twitter",
    "_type": "tweet",
    "_id": "1",
    "_version": 13,			//版本号发生了更新，变为了13.
    "found": true,
    "_source": {
        "message": "我正在更新文档"
    }
}
```

如果这个时候再次执行以下语句，将会执行失败，因为版本号，发生了变更

```shell
curl -XPUT "http://localhost:9200/twitter/tweet/1?version=12" -d '{
	"message":"我又要更新文档了"
}'
```

*   response

```json
{
    "error": {
        "root_cause": [
            {
                "type": "version_conflict_engine_exception",
                "reason": "[tweet][1]: version conflict, current version [13] is different than the one provided [12]",
                "index_uuid": "eQN2FbMCQ8aDRhlcEagnew",
                "shard": "3",
                "index": "twitter"
            }
        ],
        "type": "version_conflict_engine_exception",
        "reason": "[tweet][1]: version conflict, current version [13] is different than the one provided [12]",
        "index_uuid": "eQN2FbMCQ8aDRhlcEagnew",
        "shard": "3",
        "index": "twitter"
    },
    "status": 409
}
```

version conflict, current version \[13] is different than the one provided \[12]"  由此可以知道，发生了版本冲突。

所以通过版本号，可以实现并发控制，如果有多个相同的操作请求，但只想让其中一个执行成功，其他则不执行，可以通过指定版本号的方式实现。

***

### Operation Type (操作类型)

当创建文档时，可以指定操作的类型op\_type=create,表示该操作为创建文档的操作，如果文档已经存在，则创建失败。

如果不指定op\_type,文档不存在就是创建文档。

> 注意，op\_type与索引无关，无论是否指定op\_type参数，如果索引不存在则创建索引。

```shell
curl -X PUT "localhost:9200/twitter/tweet/1?op_type=create&pretty"  -d'
{
    "user" : "kimchy",
    "post_date" : "2009-11-15T14:12:12",
    "message" : "trying out Elasticsearch"
}
'
```

*   response

```json
{
    "error": {
        "root_cause": [
            {
                "type": "version_conflict_engine_exception",
                "reason": "[tweet][1]: version conflict, document already exists (current version [16])",
                "index_uuid": "eQN2FbMCQ8aDRhlcEagnew",
                "shard": "3",
                "index": "twitter"
            }
        ],
        "type": "version_conflict_engine_exception",
        "reason": "[tweet][1]: version conflict, document already exists (current version [16])",
        "index_uuid": "eQN2FbMCQ8aDRhlcEagnew",
        "shard": "3",
        "index": "twitter"
    },
    "status": 409
}
```

由于文档已经存在，报错： document already exists 。

> 另外，create 选项也可以跟随在URL的最后，如下:

```json
curl -X PUT "localhost:9200/twitter/tweet/1/_create?pretty"  -d'
{
    "user" : "kimchy",
    "post_date" : "2009-11-15T14:12:12",
    "message" : "trying out Elasticsearch"
}
'
```

***

### Automatic ID Generation (自动生成ID)

在创建文档时，如果某有指定该文档的ID号，那么程序会自动的为该文档生成一个ID号。op\_type也将会被自动设置为create。

> 此时，使用的是POST请求，而不是PUT请求

```shell
curl -X POST "localhost:9200/twitter/tweet/?pretty" -d'
{
    "user" : "kimchy",
    "post_date" : "2009-11-15T14:12:12",
    "message" : "trying out Elasticsearch"
}
'
```

*   response

```json
{
    "_shards" : {
        "total" : 2,
        "failed" : 0,
        "successful" : 2
    },
    "_index" : "twitter",
    "_type" : "tweet",
    "_id" : "6a8ca01c-7896-48e9-81cc-9f70661fcb32",		//自动生产的ID号
    "_version" : 1,
    "created" : true,
    "result": "created"
}
```

***

### Routing （路由）

默认，新增的文档数据存在放哪一个分片上，是通过该文档ID的hash值去控制的。如果不想通过hash(ID)控制，可以指定路由参数，通过指定值去控制该文档的存储位置，也就是路由分配。比如routing=kimchy,则就是用过hash(kimchy)的值，去进行路由分配。

同理，当执行获取文档操作时，如果没有指定routing参数，则程序会默认使用该ID的hash值，通过计算后去到对应的分片上寻找该文档数据。

> 注意： routing=kimchy ，kimchy 和文档内容没有任何关系，是一个可随意指定的值。

```shell
curl -X POST "localhost:9200/twitter/tweet?routing=kimchy&pretty" -d'
{
    "user" : "kimchy",
    "post_date" : "2009-11-15T14:12:12",
    "message" : "trying out Elasticsearch"
}
'
```

*   response

```json
{
    "_index": "twitter",
    "_type": "tweet",
    "_id": "AY0sVY_JUZj-tqz1dZ31",
    "_version": 1,
    "result": "created",
    "_shards": {
        "total": 2,
        "successful": 1,
        "failed": 0
    },
    "created": true
}
```

查看该id的文档信息 **(错误示例)**

```shell
curl localhost:9200/twitter/tweet/AY0sVY_JUZj-tqz1dZ31
```

*   response

```json
{
    "_index": "twitter",
    "_type": "tweet",
    "_id": "AY0sVY_JUZj-tqz1dZ31",
    "found": false				//没找到
}
```

> 原因，没有指定routing,通过ID的hash 去找，当然找不到了。

查看该id的文档信息 **(正确示例)**

```shell
curl localhost:9200/twitter/tweet/AY0sVY_JUZj-tqz1dZ31?routing=kimchy
```

*   response

```json
{
    "_index": "twitter",
    "_type": "tweet",
    "_id": "AY0sVY_JUZj-tqz1dZ31",
    "_version": 1,
    "_routing": "kimchy",
    "found": true,
    "_source": {
        "user": "kimchy",
        "post_date": "2009-11-15T14:12:12",
        "message": "trying out Elasticsearch"
    }
}
```

成功获取该文档信息。

> 补充，还可以设置routing为必须的参数，设置之后如果索引操作没有携带routing将会操作失败！！

