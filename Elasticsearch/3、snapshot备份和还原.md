# 1、拍摄快照和还原

1. 指定一个备份路径作为备份仓库

```shell
curl -XPUT 'localhost:9292/_snapshot/labs_backup①?verify=false' \
-d '{
    "type": "fs",
    "settings": {
        "location": "/opt/data/elasticsearch-5.2.2/snapshots/labs_backup②",
        "compress": "true"
    }
}'
#verify=false 创建仓库时，不验证文件系统的状态和权限
#"type": "fs" 指定文件系统的状态，elasticsearch还支持其他的文件系统，比如Hadoop HDFS 等。
#location:    指定仓库的路径，但是此路径一定要包含于配置文件中repo.path的路径之下
#compress:true     创建快照时，压缩数据
```

① 

② 

建议：两者保持一致

1. 有了仓库之后我们就可以做备份了，如下我们在labs_backup仓库内创建一个名为shop_snapshot的快照

```shell
curl -XPUT 'localhost:9292/_snapshot/labs_backup①/shop_snapshot②? \
wait_for_completion=true&pretty' -d '{
    "indices":"shop",
    "ignore_unavailable": "true",
    "include_global_state": "false"
}'
#wait_for_completion=true    等快照创完成，才返回响应
#pretty        让返回的响应便于阅读
#indices        指定要创建快照的索引，有多个用逗号隔开。可以省略，则默认是所有的索引
#ignore_unavailable:true      表示，创建快照时，忽略掉不不可用的快照
#include_global_state:flase    表示忽略全局的快照状态
```

① 

② 

③

1. 测试删除索引

```shell
curl -X delete  'localhost:9292/_snapshot/labs_backup①/shop_snapshot'
```

1. 数据还原

```shell
curl -XPOST 'localhost:9292/_snapshot/labs_backup①/shop_snapshot②/_restore?\
wait_for_completion=true&pretty' \
-d '{
    "indices":"metadataengine"③,
    "ignore_unavailable": "true",
    "include_global_state": "false"
    }
'
#indices: 可以执行需要还原的索引，如果不指定就是还原当前仓库备份的所有的快照的内容
```

# 2、集群数据迁移，具体步骤

1. 指定一个备份路径作为备份仓库，原集群执行一次

```shell
curl -XPUT 'localhost:9292/_snapshot/labs_backup①?verify=false' -d '{
    "type": "fs",
    "settings": {
        "location": "/opt/data/elasticsearch-5.2.2/snapshots/labs_backup②",
        "compress": "true"
    }
}'
```

① 

② 

建议：两者保持一致

1. 有了仓库之后我们就可以做备份了，如下我们在labs_backup仓库内创建一个名为metadataengine_snapshot的快照，原集群内执行一次

```shell
curl -XPUT 'localhost:9292/_snapshot/labs_backup①/metadataengine_snapshot②? \
wait_for_completion=true&pretty' -d '{
    "indices":"metadataengine"③,
    "ignore_unavailable": "true",
    "include_global_state": "false"
}'
```

① 

② 

③

1. 备份之后，要把每台机器生成的备份数据发送给目标集群的其中一台机器做汇总，原集群每台机器执行一次

```shell
rsync -azP /opt/data/elasticsearch/elasticsearch-5.2.2/snapshots/labs_backup①   \
easyops@10.32.38.110②:/opt/data/elasticsearch-5.2.2/snapshots/
```

① 

② 

1. 把备份数据汇总到目标集群的一台机器之后，把此机器上的汇总后的备份发送给目标集群的其他所有机器，有几个机器发送几次，需要改一下IP

```
rsync -azP /opt/data/elasticsearch-5.2.2/snapshots/labs_backup①   \
easyops@10.32.40.40②:/opt/data/elasticsearch-5.2.2/snapshots/
```

①

②

1. 把从原集群得到的备份文件夹设置为目标集群的备份仓库，目标集群的每台机器执行

```shell
curl -XPUT 'localhost:9292/_snapshot/labs_backup①?verify=false' -d '{
    "type": "fs",
    "settings": {
        "location": "/opt/data/elasticsearch-5.2.2/snapshots/labs_backup②",
        "compress": "true"
    }
}'
```

①

②

建议：名称都与第1步保持一致