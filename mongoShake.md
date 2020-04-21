# MongoShake

## Setup Prerequisite

* Install [GoLang](golang.md)
* Install GCC (if error: # vendor/github.com/DataDog/zstd
exec: "gcc": executable file not found in $PATH)
  ```bash
  apt install gcc
  ```

## Setup Steps
Create a folder for download MongoShake location
```bash
mkdir /go/src/github.com/

cd go/src/github.com/
```
Clone the MongoShake to local
```bash
git clone https://github.com/alibaba/MongoShake.git
```

Install Go Vendor
```bash
cd go/src/github.com/MongoShake/src/vendor

GOPATH=`pwd`/../..

go get -u github.com/kardianos/govendor
govendor -version
govendor sync
```

Build MongoShake
```bash
cd go/src/github.com/MongoShake/

./build.sh
```

Run MongoShake
```bash
cd /go/src/github.com/MongoShake
./bin/collector.linux -conf=conf/collector.conf
```


## Sample configure
```bash
mongo_urls = mongodb://username:password@127.0.0.1:20040,127.0.0.1:20041
```

## MongoDb Sample configuration
```bash
# mongod.conf

# for documentation of all options, see:
#   http://docs.mongodb.org/manual/reference/configuration-options/

# Where and how to store data.
storage:
  dbPath: /var/data/db3/
  journal:
    enabled: true
#  engine:
#  mmapv1:
#  wiredTiger:

# where to write logging data.
systemLog:
  destination: file
  logAppend: true
  path: /var/log/mongodb/mongod3.log

# network interfaces
net:
  port: 22003
  bindIp: 127.0.0.1


# how the process runs
processManagement:
  timeZoneInfo: /usr/share/zoneinfo

security:
  authorization: "enabled"

#operationProfiling:



replication:
replSetName: "rs0" 

#sharding:

## Enterprise-Only Options:

#auditLog:

#snmp:


# mongod2.conf

# for documentation of all options, see:
#   http://docs.mongodb.org/manual/reference/configuration-options/

# Where and how to store data.
storage:
  dbPath: /var/data/db3/
  journal:
    enabled: true
#  engine:
#  mmapv1:
#  wiredTiger:

# where to write logging data.
systemLog:
  destination: file
  logAppend: true
  path: /var/log/mongodb/mongod3.log

# network interfaces
net:
  port: 22003
  bindIp: 127.0.0.1


# how the process runs
processManagement:
  timeZoneInfo: /usr/share/zoneinfo

security:
  authorization: "enabled"

#operationProfiling:



#replication:
#  replSetName: "rs0" 

#sharding:

## Enterprise-Only Options:

#auditLog:

#snmp:
```

## MongoShake Log
```bash
MongoShake-Log


root@dev-datacenter-1:~/go/src/github.com/MongoShake# ./bin/collector.linux -conf=conf/collector.conf -verbose
[09:36:51 UTC 2020/03/18] [WARN] (mongoshake/common.Welcome:189) 
______________________________
\                             \           _         ______ |
 \                             \        /   \___-=O'/|O'/__|
  \  MongoShake, Here we go !!  \_______\          / | /    )
  /                             /        '/-==__ _/__|/__=-|  -GM
 /        Alibaba Cloud        /         *             \ | |
/                             /                        (o)
------------------------------

if you have any problem, please visit https://github.com/alibaba/MongoShake/wiki/FAQ

[09:36:51 UTC 2020/03/18] [INFO] (mongoshake/common.NewMongoConn:89) New session to mongodb://127.0.0.1:22001 successfully
[09:36:51 UTC 2020/03/18] [INFO] (mongoshake/common.(*MongoConn).Close:94) Close session with mongodb://127.0.0.1:22001
[09:36:51 UTC 2020/03/18] [INFO] (mongoshake/common.NewMongoConn:89) New session to mongodb://127.0.0.1:22001 successfully
[09:36:51 UTC 2020/03/18] [INFO] (mongoshake/common.(*MongoConn).Close:94) Close session with mongodb://127.0.0.1:22001
[09:36:51 UTC 2020/03/18] [INFO] (mongoshake/collector.(*ReplicationCoordinator).Run:47) Collector startup. shard_by[collection] gids[[]]
[09:36:51 UTC 2020/03/18] [INFO] (mongoshake/collector.(*ReplicationCoordinator).Run:51) Collector configuration {"MongoUrls":["mongodb://127.0.0.1:22001"],"MongoCsUrl":"","MongoConnectMode":"primary","CollectorId":"mongoshake","CheckpointInterval":5000,"HTTPListenPort":9100,"SystemProfile":9200,"LogLevel":"info","LogDirectory":"","LogFileName":"collector.log","LogBuffer":true,"OplogGIDS":[],"ShardKey":"collection","SyncerReaderBufferTime":1,"WorkerNum":8,"WorkerOplogCompressor":"none","WorkerBatchQueueSize":64,"AdaptiveBatchingMaxSize":1024,"FetcherBufferCapacity":256,"Tunnel":"direct","TunnelAddress":["mongodb://127.0.0.1:22003"],"MasterQuorum":false,"ContextStorage":"database","ContextStorageUrl":"mongodb://127.0.0.1:22001","ContextStorageDB":"mongoshake","ContextStorageCollection":"ckpt_default","ContextStartPosition":0,"FilterNamespaceBlack":[],"FilterNamespaceWhite":[],"FilterPassSpecialDb":[],"SyncMode":"all","TransformNamespace":[],"DBRef":false,"MoveChunkEnable":false,"MoveChunkInterval":1000,"ReplayerDMLOnly":false,"ReplayerExecutor":1,"ReplayerExecutorUpsert":false,"ReplayerExecutorInsertOnDupUpdate":false,"ReplayerCollisionEnable":false,"ReplayerConflictWriteTo":"none","ReplayerDurable":true,"ReplayerCollectionDrop":true,"ReplayerCollectionParallel":6,"ReplayerDocumentParallel":8,"ReplayerDocumentBatchSize":256,"FilterOrphanDocument":false,"LogLevelOld":"","LogFileNameOld":"","LogBufferOld":false,"Version":"develop,b6f2496ca5389e9c7f9582a33a138888dc0914b2,release,go1.14,2020-03-16_06:25:04"}
[09:36:51 UTC 2020/03/18] [INFO] (mongoshake/common.NewMongoConn:89) New session to mongodb://127.0.0.1:22001 successfully
[09:36:51 UTC 2020/03/18] [INFO] (mongoshake/common.(*MongoConn).Close:94) Close session with mongodb://127.0.0.1:22001
[09:36:51 UTC 2020/03/18] [INFO] (mongoshake/common.NewMongoConn:89) New session to mongodb://127.0.0.1:22001 successfully
[09:36:51 UTC 2020/03/18] [INFO] (mongoshake/common.(*MongoConn).Close:94) Close session with mongodb://127.0.0.1:22001
[09:36:51 UTC 2020/03/18] [INFO] (mongoshake/common.NewMongoConn:89) New session to mongodb://127.0.0.1:22001 successfully
[09:36:51 UTC 2020/03/18] [INFO] (mongoshake/collector/docsyncer.LoadCheckpoint:58) LoadCheckpoint load replset[default-0] ackTs[Timestamp(1584351068, 1)]
[09:36:51 UTC 2020/03/18] [INFO] (mongoshake/common.(*MongoConn).Close:94) Close session with mongodb://127.0.0.1:22001
[09:36:51 UTC 2020/03/18] [INFO] (mongoshake/collector.(*ReplicationCoordinator).Run:66) start running with mode[all], fullBeginTs[1584524206]
[09:36:51 UTC 2020/03/18] [INFO] (mongoshake/common.NewMongoConn:89) New session to mongodb://127.0.0.1:22001 successfully
[09:36:51 UTC 2020/03/18] [INFO] (mongoshake/common.(*MongoConn).Close:94) Close session with mongodb://127.0.0.1:22001
[09:36:51 UTC 2020/03/18] [INFO] (mongoshake/common.NewMongoConn:89) New session to mongodb://127.0.0.1:22001 successfully
[09:36:51 UTC 2020/03/18] [INFO] (mongoshake/common.(*MongoConn).Close:94) Close session with mongodb://127.0.0.1:22001
[09:36:51 UTC 2020/03/18] [INFO] (mongoshake/common.NewMongoConn:89) New session to mongodb://127.0.0.1:22001 successfully
[09:36:51 UTC 2020/03/18] [INFO] (mongoshake/common.(*MongoConn).Close:94) Close session with mongodb://127.0.0.1:22001
[09:36:51 UTC 2020/03/18] [INFO] (mongoshake/common.NewMongoConn:89) New session to mongodb://127.0.0.1:22003 successfully
[09:36:51 UTC 2020/03/18] [INFO] (mongoshake/collector/docsyncer.IsShardingToSharding:38) replication from replica to replica
[09:36:51 UTC 2020/03/18] [INFO] (mongoshake/collector.(*ReplicationCoordinator).startDocumentReplication:326) document syncer rs0 begin replication for url=mongodb://127.0.0.1:22001
[09:36:51 UTC 2020/03/18] [INFO] (mongoshake/common.NewMongoConn:89) New session to mongodb://127.0.0.1:22001 successfully
[09:36:51 UTC 2020/03/18] [INFO] (mongoshake/common.(*MongoConn).Close:94) Close session with mongodb://127.0.0.1:22001
[09:36:51 UTC 2020/03/18] [INFO] (mongoshake/collector/docsyncer.(*DBSyncer).Start.func2:304) document syncer rs0 collExecutor-5 sync ns {test tb4} to {test tb4} begin
[09:36:51 UTC 2020/03/18] [INFO] (mongoshake/collector/docsyncer.(*DBSyncer).Start.func2:304) document syncer rs0 collExecutor-0 sync ns {testdb tb3} to {testdb tb3} begin
[09:36:51 UTC 2020/03/18] [INFO] (mongoshake/collector/docsyncer.(*DBSyncer).Start.func2:304) document syncer rs0 collExecutor-1 sync ns {testdb tb4} to {testdb tb4} begin
[09:36:51 UTC 2020/03/18] [INFO] (mongoshake/common.NewMongoConn:89) New session to mongodb://127.0.0.1:22003 successfully
[09:36:51 UTC 2020/03/18] [INFO] (mongoshake/common.NewMongoConn:89) New session to mongodb://127.0.0.1:22003 successfully
[09:36:51 UTC 2020/03/18] [INFO] (mongoshake/common.NewMongoConn:89) New session to mongodb://127.0.0.1:22003 successfully
[09:36:51 UTC 2020/03/18] [INFO] (mongoshake/common.NewMongoConn:89) New session to mongodb://127.0.0.1:22001 successfully
[09:36:51 UTC 2020/03/18] [INFO] (mongoshake/common.NewMongoConn:89) New session to mongodb://127.0.0.1:22001 successfully
[09:36:51 UTC 2020/03/18] [INFO] (mongoshake/common.NewMongoConn:89) New session to mongodb://127.0.0.1:22001 successfully
[09:36:52 UTC 2020/03/18] [INFO] (mongoshake/common.(*MongoConn).Close:94) Close session with mongodb://127.0.0.1:22003
[09:36:52 UTC 2020/03/18] [INFO] (mongoshake/common.(*MongoConn).Close:94) Close session with mongodb://127.0.0.1:22001
[09:36:52 UTC 2020/03/18] [INFO] (mongoshake/collector/docsyncer.(*DBSyncer).Start.func2:314) document syncer rs0 collExecutor-5 sync ns {test tb4} to {test tb4} successful. db syncer rs0 progress 33%
[09:36:52 UTC 2020/03/18] [INFO] (mongoshake/common.(*MongoConn).Close:94) Close session with mongodb://127.0.0.1:22003
[09:36:52 UTC 2020/03/18] [INFO] (mongoshake/common.(*MongoConn).Close:94) Close session with mongodb://127.0.0.1:22001
[09:36:52 UTC 2020/03/18] [INFO] (mongoshake/collector/docsyncer.(*DBSyncer).Start.func2:314) document syncer rs0 collExecutor-0 sync ns {testdb tb3} to {testdb tb3} successful. db syncer rs0 progress 66%
[09:36:52 UTC 2020/03/18] [INFO] (mongoshake/common.(*MongoConn).Close:94) Close session with mongodb://127.0.0.1:22003
[09:36:52 UTC 2020/03/18] [INFO] (mongoshake/common.(*MongoConn).Close:94) Close session with mongodb://127.0.0.1:22001
[09:36:52 UTC 2020/03/18] [INFO] (mongoshake/collector/docsyncer.(*DBSyncer).Start.func2:314) document syncer rs0 collExecutor-1 sync ns {testdb tb4} to {testdb tb4} successful. db syncer rs0 progress 100%
[09:36:52 UTC 2020/03/18] [INFO] (mongoshake/collector/docsyncer.StartIndexSync:160) document syncer sync index begin
[09:36:52 UTC 2020/03/18] [INFO] (mongoshake/collector/docsyncer.(*DBSyncer).Start.func2:319) document syncer rs0 collExecutor-1 finish
[09:36:52 UTC 2020/03/18] [INFO] (mongoshake/collector/docsyncer.(*DBSyncer).Start.func2:319) document syncer rs0 collExecutor-0 finish
[09:36:52 UTC 2020/03/18] [INFO] (mongoshake/collector/docsyncer.(*DBSyncer).Start.func2:319) document syncer rs0 collExecutor-5 finish
[09:36:52 UTC 2020/03/18] [INFO] (mongoshake/collector/docsyncer.(*DBSyncer).Start.func2:319) document syncer rs0 collExecutor-4 finish
[09:36:52 UTC 2020/03/18] [INFO] (mongoshake/collector/docsyncer.(*DBSyncer).Start.func2:319) document syncer rs0 collExecutor-3 finish
[09:36:52 UTC 2020/03/18] [INFO] (mongoshake/collector/docsyncer.(*DBSyncer).Start.func2:319) document syncer rs0 collExecutor-2 finish
[09:36:52 UTC 2020/03/18] [INFO] (mongoshake/common.NewMongoConn:89) New session to mongodb://127.0.0.1:22003 successfully
[09:36:52 UTC 2020/03/18] [INFO] (mongoshake/collector/docsyncer.StartIndexSync.func2:215) Create indexes for ns {testdb tb3} of dest mongodb finish
[09:36:52 UTC 2020/03/18] [INFO] (mongoshake/collector/docsyncer.StartIndexSync.func2:215) Create indexes for ns {test tb4} of dest mongodb finish
[09:36:52 UTC 2020/03/18] [INFO] (mongoshake/collector/docsyncer.StartIndexSync.func2:215) Create indexes for ns {testdb tb4} of dest mongodb finish
[09:36:52 UTC 2020/03/18] [INFO] (mongoshake/collector/docsyncer.StartIndexSync:225) document syncer sync index finish
[09:36:52 UTC 2020/03/18] [INFO] (mongoshake/common.(*MongoConn).Close:94) Close session with mongodb://127.0.0.1:22003
[09:36:52 UTC 2020/03/18] [INFO] (mongoshake/collector.(*ReplicationCoordinator).startDocumentReplication:352) try to set checkpoint with map[map[rs0:6805479644490366977]]
[09:36:52 UTC 2020/03/18] [INFO] (mongoshake/common.NewMongoConn:89) New session to mongodb://127.0.0.1:22001 successfully
[09:36:52 UTC 2020/03/18] [INFO] (mongoshake/common.(*MongoConn).Close:94) Close session with mongodb://127.0.0.1:22001
[09:36:52 UTC 2020/03/18] [INFO] (mongoshake/collector.(*ReplicationCoordinator).startDocumentReplication:358) document syncer sync end
[09:36:52 UTC 2020/03/18] [INFO] (mongoshake/common.(*MongoConn).Close:94) Close session with mongodb://127.0.0.1:22003
[09:36:52 UTC 2020/03/18] [INFO] (mongoshake/common.NewMongoConn:89) New session to mongodb://127.0.0.1:22001 successfully
[09:36:52 UTC 2020/03/18] [INFO] (mongoshake/common.(*MongoConn).Close:94) Close session with mongodb://127.0.0.1:22001
[09:36:52 UTC 2020/03/18] [INFO] (mongoshake/common.NewMongoConn:89) New session to mongodb://127.0.0.1:22001 successfully
[09:36:52 UTC 2020/03/18] [INFO] (mongoshake/common.(*MongoConn).Close:94) Close session with mongodb://127.0.0.1:22001
[09:36:52 UTC 2020/03/18] [INFO] (mongoshake/collector.(*ReplicationCoordinator).Run:79) ------------------------full sync done!------------------------
[09:36:52 UTC 2020/03/18] [INFO] (mongoshake/collector.(*ReplicationCoordinator).Run:81) bigOldTs[1584347870] fullBeginTs[1584524206] fullFinishTs[1584524212]
[09:36:52 UTC 2020/03/18] [INFO] (mongoshake/collector.(*ReplicationCoordinator).Run:90) finish full sync, start incr sync with timestamp: fullBeginTs[1584524206], fullFinishTs[1584524212]
[09:36:52 UTC 2020/03/18] [INFO] (mongoshake/common.NewMongoConn:89) New session to mongodb://127.0.0.1:22003 successfully
[09:36:52 UTC 2020/03/18] [INFO] (mongoshake/collector.(*Worker).startWorker:120) Collector-worker-0 start working with jobs batch queue. buffer capacity 64
[09:36:52 UTC 2020/03/18] [INFO] (mongoshake/common.NewMongoConn:89) New session to mongodb://127.0.0.1:22003 successfully
[09:36:52 UTC 2020/03/18] [INFO] (mongoshake/collector.(*Worker).startWorker:120) Collector-worker-1 start working with jobs batch queue. buffer capacity 64
[09:36:52 UTC 2020/03/18] [INFO] (mongoshake/common.NewMongoConn:89) New session to mongodb://127.0.0.1:22003 successfully
[09:36:52 UTC 2020/03/18] [INFO] (mongoshake/collector.(*Worker).startWorker:120) Collector-worker-2 start working with jobs batch queue. buffer capacity 64
[09:36:52 UTC 2020/03/18] [INFO] (mongoshake/common.NewMongoConn:89) New session to mongodb://127.0.0.1:22003 successfully
[09:36:52 UTC 2020/03/18] [INFO] (mongoshake/collector.(*Worker).startWorker:120) Collector-worker-3 start working with jobs batch queue. buffer capacity 64
[09:36:52 UTC 2020/03/18] [INFO] (mongoshake/common.NewMongoConn:89) New session to mongodb://127.0.0.1:22003 successfully
[09:36:52 UTC 2020/03/18] [INFO] (mongoshake/collector.(*Worker).startWorker:120) Collector-worker-4 start working with jobs batch queue. buffer capacity 64
[09:36:52 UTC 2020/03/18] [INFO] (mongoshake/common.NewMongoConn:89) New session to mongodb://127.0.0.1:22003 successfully
[09:36:52 UTC 2020/03/18] [INFO] (mongoshake/collector.(*Worker).startWorker:120) Collector-worker-5 start working with jobs batch queue. buffer capacity 64
[09:36:52 UTC 2020/03/18] [INFO] (mongoshake/common.NewMongoConn:89) New session to mongodb://127.0.0.1:22003 successfully
[09:36:52 UTC 2020/03/18] [INFO] (mongoshake/collector.(*Worker).startWorker:120) Collector-worker-6 start working with jobs batch queue. buffer capacity 64
[09:36:52 UTC 2020/03/18] [INFO] (mongoshake/common.NewMongoConn:89) New session to mongodb://127.0.0.1:22003 successfully
[09:36:52 UTC 2020/03/18] [INFO] (mongoshake/collector.(*Worker).startWorker:120) Collector-worker-7 start working with jobs batch queue. buffer capacity 64
[09:36:52 UTC 2020/03/18] [INFO] (mongoshake/common.NewMongoConn:89) New session to mongodb://127.0.0.1:22001 successfully
[09:36:52 UTC 2020/03/18] [EROR] (mongoshake/collector.(*CheckpointManager).Load:206) CheckpointManager load checkpoint unknown replset map[_id:ObjectIdHex("5e71ded2ddd2812eeefd1910") ackTs:6804736022442672129 name:default-0 syncTs:6804736022442672129]
[09:36:52 UTC 2020/03/18] [INFO] (mongoshake/collector.(*CheckpointManager).Load:214) CheckpointManager load checkpoint set replset[rs0] checkpoint to exist ackTs[Timestamp(1584524206, 1)] syncTs[Timestamp(1584524206, 1)]
[09:36:52 UTC 2020/03/18] [INFO] (mongoshake/collector.(*OplogSyncer).start:150) Poll oplog syncer start. ckpt_interval[5000ms], gid[[]], shard_key[collection]
[09:36:52 UTC 2020/03/18] [INFO] (mongoshake/common.NewMongoConn:89) New session to mongodb://127.0.0.1:22001 successfully
```



## Reference
[MongoShake Github](https://github.com/alibaba/MongoShake/wiki/MongoShake-Detailed-Documentation)

[Mongo上云迁移同步Mongoshake](https://blog.csdn.net/sinat_41780498/article/details/101445585)

[MongoShake最佳实践](https://my.oschina.net/u/1464083/blog/3114883)

[使用MongoShake实现MongoDB副本集间的单向同步](https://help.aliyun.com/document_detail/122621.html?spm=5176.10695662.1996646101.searchclickresult.61a7f68aETOT82)

[MongoShake——基于MongoDB的跨数据中心的数据复制平台](https://yq.aliyun.com/articles/603329)
