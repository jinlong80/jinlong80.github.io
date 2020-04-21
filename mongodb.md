# MongoDb

## Setup

Add the repo
```bash
sudo apt-key adv --keyserver hkp://keyserver.ubuntu.com:80 --recv 4B7C549A058F8B6B
```

For Ubuntu 18.04 LTS
```bash
echo "deb [ arch=amd64 ] https://repo.mongodb.org/apt/ubuntu bionic/mongodb-org/4.2 multiverse" | sudo tee /etc/apt/sources.list.d/mongodb.list
```

For Ubuntu 16.04 LTS
```bash
echo "deb [ arch=amd64,arm64 ] https://repo.mongodb.org/apt/ubuntu xenial/mongodb-org/4.2 multiverse" | sudo tee /etc/apt/sources.list.d/mongodb.list
```

Update package for local system
```bash
sudo apt-get update
```

Install
```bash
sudo apt install mongodb-org
```

## Command
* Enable
  ```bash
  sudo systemctl enable mongod
  ```

* Start
  ```bash
  sudo systemctl start mongod
  ```

* Check Status
  ```bash
  sudo systemctl status mongod
  ```
 
* Check Version
  ```bash
  mongod --version 
  ```

* Access Mongo without credential
  ```bash
  mongo --port 27017
  ```

* Enable Mongo access with password
  ```bash
  > use admin
  switched to db admin
  > db.createUser(
  ... {
  ... user: "user1",
  ... pwd: "testing",
  ... roles: [{role:"root", db:"admin"}]
  ... }
  ... )
  Successfully added user: {
          "user" : "user1",
          "roles" : [
                  {
                          "role" : “root”,
                          "db" : "admin"
                  }
          ]
  }
  ```

* Access Mongo with credential
  ```bash
  mongo --port 27017 -u "user1" --authenticationDatabase "admin" -p 
  testing
  ```

##  Adding firewall
```bash
sudo ufw app list
 
sudo ufw allow OpenSSH
 
sudo ufw allow 27017
 
sudo ufw enable
 
sudo ufw status

sudo netstat -plnt | egrep mongod
```

## Multiple Instance

Create database path
```bash
cd /var
mkdir data
cd data
mkdir db1 db2 db3
```

Change the Ownership this folder
```bash
chown mongodb:mongodb db1 db2 db3
```

Create new Config files for mongoDb
```bash
cp /etc/mongod.conf /etc/mongod2.conf
cp /etc/mongod.conf /etc/mongod3.conf
```

Modify the configs file 'mongod.conf'
```bash
dbPath /var/data/db1/ 
replSetName “rs0” 
port 27017 
Blind IP 0.0.0.0
```

'mongod2.conf'
```bash
dbPath /var/data/db2/
replSetName “rs0”
port 27018
Blind IP 0.0.0.0
```

'mongod3.conf'
```bash
dbPath /var/data/db3/
replSetName “rs0”
port 27019
Blind IP 0.0.0.0
```

Create two new services
```bash
cd /lib/systemd/system
cp mongod.service mongod3.service
cp mongod.service mongod2.service
```

Open each service in text editor and change the config file in ExecStart corresponding to service name.
'mongod.service'
```bash
ExecStart=/usr/bin/mongod --config /etc/mongod.conf
```

'mongod2.service'
```bash
ExecStart=/usr/bin/mongod --config /etc/mongod2.conf
```

'mongod3.service'
```bash
ExecStart=/usr/bin/mongod --config /etc/mongod3.conf
```

Deploy each process
```bash
sudo systemctl start mongod.service
sudo systemctl start mongod2.service
sudo systemctl start mongod3.service
```

Setting replication
Open a mongo shell and initiate a replication by running below configuration
```bash
rs.initiate( {
  _id : "rs0",
  members: [
      { _id: 0, host: "localhost:22001” },
      { _id: 1, host: "localhost:22002” },
      { _id: 2, host: "localhost:22003” }
  ]
})
```

To see the status of replication execute the given below command in shell
```bash
rs.status()   // you can see the status of replication
```

## Reference
[Install MongoDb on Ubuntu](https://tecadmin.net/install-mongodb-on-ubuntu/)

[Crate Multiple Instances of MongoDb](https://medium.com/@akshay2gud/creating-multiple-instances-of-mongodb-on-server-and-setting-replication-of-database-5ead59e1e4d4)

[Introduction to MongoDb Indexes](https://medium.com/swlh/introduction-to-mongodb-indexes-cdb216f54f80)

[Difference between Sharding and Replication](https://dba.stackexchange.com/questions/52632/difference-between-sharding-and-replication-on-mongodb)

[Sharding Replication and Cluster](https://medium.com/@tudip/mongodb-sharding-replication-and-clusters-d95a6595bd2c)

[MongoDB Hosting You Can Try for Free](https://studio3t.com/knowledge-base/articles/cheap-free-mongodb-hosting/)

[使用MongoDB开发过程常见错误分析，mongodb常见错误](http://www.htsjk.com/MongoDB/10896.html)

[Ubuntu 18.04配置MongoDB密码登录](https://www.ancii.com/acm5y3vnj/)

[MongoDB 3.0 常见集群的搭建(主从复制，副本集，分片....)](https://blog.csdn.net/canot/article/details/50739359 )

[Mongodb主从复制/ 副本集/分片集群介绍](https://www.cnblogs.com/kevingrace/p/5685486.html)

[Mongoose](https://www.digitalocean.com/community/tutorials/how-to-integrate-mongodb-with-your-node-application)