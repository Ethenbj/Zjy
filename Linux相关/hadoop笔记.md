# hadoop笔记

### hadoop

* **HDFS 海量存储**
* **MapReduce 海量计算**

### yarn

* ResourceManager 统一调度和管理
* NodeManager 执行任务、领取任务
* ApplicationMaster 向RM申请资源，创建任务

### HDFS

* NameNode 管理
* SecondaryNameNode 协助NameNode（不是副本）
* DataNode 管理存储

### MapReduce

* mapper 拆分
* reducer 合并（累加）


### 查看网络

```python
vim /etc/sysconfig/network-scripts/ifcfg-ens33
# 修改网址为静态
BOOTPROTO="static"
IPADDR="192.168.1.107"
NETMASK="255.255.255.0"
GATEWAY="192.168.1.1"
DNS1="114.114.114.114"
```

#####修改hostname

```python
hostnamectl set-hostname node1
```

##### 修改hosts

```
vim /etc/hosts
192.168.1.107 node1
192.168.1.111 node2
192.168.1.112 node3
```

##### 安装vim

```
yum install -y vim
```

### 关闭防火墙

* systemctl status firewalld   查看
* systemctl stop firewalld   停止 （start开始）
* systemctl disable firewalld  不可用

##### 重启网络

service network restart

### 关闭selinux

* getenforce
* setenforce 0
* vim /etc/selinux/config
* reboot
* ctrl + shift + r 重连

### 免密

```
ssh-keygen 
ssh-copy-id
```

##### 本地免密

```
ssh-copy-id [本地主机名]
```



![ssh](C:\Users\zheng\Documents\Tencent Files\1841275006\FileRecv\ssh.PNG)![wordcount](C:\Users\zheng\Documents\Tencent Files\1841275006\FileRecv\wordcount.JPG)

### 安装sql数据库

1.安装mariadb.server

* yum install -y mariadb-server 

2.开机启用、启动mariadb、查看状态

* systemctl enable mariadb
* systemctl start mariadb

- systemctl status mariadb
- systemctl stop mariadb
- systemctl disabled mariadb

3.初始化mysql数据库

```
mysql_secure_installation

Enter current password for root (enter for none): (enter)
Change the root password? [Y/n] (y)
New password:  (qwe)
Re-enter new password: (qwe)
Remove anonymous users? [Y/n] (n)
Disallow root login remotely? [Y/n] (n)
Remove test database and access to it? [Y/n] (y)
Reload privilege tables now? [Y/n] (y)
```
4.链接测试

```
mysql -uroot -pqwe
```

### zookeeper安装

将jdk解压

```
tar -zxvf jdk-8u231-linux-x64.tar.gz -C /usr/local/src/
mv jdk1.8.0_231/ jdk
然后在复制到另外两台
scp -r /usr/local/src/jdk @node2:/usr/local/src/
scp -r /usr/local/src/jdk @node3:/usr/local/src/
```

将zookeeper解压

```
tar -zxvf zookeeper-3.4.5.tar.gz -C /usr/local/src
cd /usr/local/src
mv zookeeper-3.4.5/ zookeeper
cd zookeeper/conf
mv zoo_sample.cfg zoo.cfg

vim zoo.cfg
dataDir=/usr/local/src/zookeeper/data
mkdir /usr/local/src/zookeeper/data
echo 0 >> /usr/local/src/zookeeper/data/myid

server.0=node1:2888:3888
server.1=node2:2888:3888
server.2=node3:2888:3888
```

> 如果先解压麻烦就直接一波解压

```
mkdir -p /usr/local/src
tar -zxvf hadoop-2.6.0.tar.gz -C /usr/local/src
mv /usr/local/src/hadoop-2.6.0 /usr/local/src/hadoop

tar -zxvf apache-hive-1.1.0-bin.tar.gz -C /usr/local/src
mv /usr/local/src/apache-hive-1.1.0-bin /usr/local/src/hive

tar -zxvf hbase-1.2.0-bin.tar.gz -C /usr/local/src
mv /usr/local/src/hbase-1.2.0 /usr/local/src/hbase

tar -zxvf sqoop-1.4.7.bin__hadoop-2.6.0.tar.gz -C /usr/local/src
mv /usr/local/src/sqoop-1.4.7.bin__hadoop-2.6.0 /usr/local/src/sqoop

tar -zxvf jdk1.8.0_111.tar.gz -C /usr/local/src
mv /usr/local/src/jdk1.8.0_111 /usr/local/src/jdk

tar -zxvf zookeeper-3.4.5.tar.gz -C /usr/local/src
mv /usr/local/src/zookeeper-3.4.5 /usr/local/src/zookeeper
```

##### 环境变量

> 注意：别漏掉PATH=$PATH，否则 linux 环境会出问题

```python
vim /etc/profile

export JAVA_HOME=/usr/local/src/jdk
export HADOOP_HOME=/usr/local/src/hadoop
export HBASE_HOME=/usr/local/src/hbase
export ZOOKEEPER_HOME=/usr/local/src/zookeeper
export SPARK_HOME=/usr/local/src/spark
export SQOOP_HOME=/usr/local/src/sqoop
export HIVE_HOME=/usr/local/src/hive

export  # 放在PATH前面 
PATH=$PATH:$JAVA_HOME/bin:$ZOOKEEPER_HOME/bin:$HBASE_HOME/bin:$HIVE_HOME/bin:$HADOOP_HOME/bin:$HADOOP_HOME/sbin:$SQOOP_HOME/bin:$SPARK_HOME/bin:$SPARK_HOME/sbin                            
                                    
source /etc/profile(. /etc/profile) # 启动环境
```

复制到另外两台

```
scp -r /usr/local/src/zookeeper/ @192.168.1.111:/usr/local/src/
scp -r /usr/local/src/zookeeper/ @192.168.1.112:/usr/local/src/
```

复制配置好的zookeeper环境，复制之后再启动刚配好的环境

```
scp -r /etc/profile @node2:/etc/profile
scp -r /etc/profile @node3:/etc/profile
```

然后在另外两台设置节点

```
在node2中
echo 1 >> /usr/local/src/zookeeper/data/myid
在node3中
echo 2 >> /usr/local/src/zookeeper/data/myid
```

每个节点启动zookeeper(如果启动不了首先查看防火墙是否都关闭，其次就是java)【验证 ZooKeeper 服务，三台节点必须是 1 个 leader 2 个 follower 的状态才算配置正确】

```
zkServer.sh start
----------------
[root@node1 ~]# jps
7524 QuorumPeerMain
7599 Jps
[root@node2 ~]# zkServer.sh status
JMX enabled by default
Using config: /usr/local/src/zookeeper/bin/../conf/zoo.cfg
Mode: leader
```

### hadoop安装（集群）

将hadoop解压

```Python
tar -zxvf hadoop-2.6.0.tar.gz -C /usr/local/src/
 mv hadoop-2.6.0/ hadoop #改名
```

配置环境

修改hadoop-env.sh 

```python
vim hadoop-env.sh 
export JAVA_HOME=/usr/local/src/jdk
```

修改slaves

```
vim slaves
node3
```

修改core-site.xml

```python
vim core-site.xml

<configuration>
 <property>
        <!--指定zookeeper所在的节点-->
        <name>ha.zookeeper.quorum</name>
        <value>node1:2181,node2:2181,node3:2181</value>
 </property>
 <property>
        <name>fs.defaultFS</name>
        <value>hdfs://ns</value>
 </property>
 <property>
        <name>hadoop.tmp.dir</name>
        <value>file:/usr/local/src/hadoop/tmp</value>
 </property>
</configuration>
```

修改hdfs-site.xml

```python
vim hdfs-site.xml

<configuration>
 <property>
        <name>dfs.replication</name>
        <value>1</value>
 </property>

 <property>
        <!--配置逻辑名称-->
        <name>dfs.nameservices</name>
        <value>ns</value>
 </property>
 <property>
        <!--配置namenode的名称，多个可用逗号分割 -->
        <name>dfs.ha.namenodes.ns</name>
        <value>nn1,nn2</value>
 </property>
 <property>
        <name>dfs.namenode.rpc-address.ns.nn1</name>
        <value>node1:8020</value>
 </property>
 <property>
        <name>dfs.namenode.http-address.ns.nn1</name>
        <value>node1:50070</value>
 </property>
 <property>
        <name>dfs.namenode.rpc-address.ns.nn2</name>
        <value>node2:8020</value>
 </property>
 <property>
        <name>dfs.namenode.http-address.ns.nn2</name>
        <value>node2:50070</value>
 </property>

 <property>
        <name>dfs.namenode.shared.edits.dir</name>
        <value>qjournal://node1:8485;node2:8485;node3:8485/ns</value>
 </property>
 <property>
        <name>dfs.journalnode.edits.dir</name>
        <value>/usr/local/src/hadoop/hdfs/journal</value>
 </property>
 <property>
        <name>dfs.client.failover.proxy.provider.ns</name>
        <value>org.apache.hadoop.hdfs.server.namenode.ha.ConfiguredFailoverProxyProvider</value>
 </property>
 <property>
        <name>dfs.ha.fencing.methods</name>
        <value>
        sshfence
        shell(/bin/true)
        </value>
 </property>
 <property>
        <name>dfs.ha.fencing.ssh.private-key-files</name>
        <value>/root/.ssh/id_rsa</value>
 </property>
<property>
        <name>dfs.ha.automatic-failover.enabled</name>
        <value>true</value>
    </property>
 <property>
        <name>dfs.namenode.name.dir</name>
        <value>file:/usr/local/src/hadoop/tmp/dfs/name</value>
 </property>
 <property>
        <name>dfs.datanode.data.dir</name>
        <value>file:/usr/local/src/hadoop/tmp/dfs/data</value>
 </property>
</configuration>
```

修改mapred-site.xml

```python
mv mapred-site.xml.template mapred-site.xml
vim mapred-site.xml

<configuration>
 <property>
        <name>mapreduce.framework.name</name>
        <value>yarn</value>
 </property>
</configuration>
```

修改yarn-site.xml

```python
vim yarn-site.xml

<configuration>

<!-- Site specific YARN configuration properties -->
 <property>
        <name>yarn.resourcemanager.ha.enabled</name>
        <value>true</value>
 </property>
 <property>
        <name>yarn.resourcemanager.cluster-id</name>
        <value>yrc</value>
 </property>
 <property>
        <name>yarn.resourcemanager.ha.rm-ids</name>
        <value>rm1,rm2</value>
 </property>
 <property>
        <name>yarn.resourcemanager.hostname.rm1</name>
        <value>node1</value>
 </property>
 <property>
        <name>yarn.resourcemanager.hostname.rm2</name>
        <value>node2</value>
 </property>
 <property>
        <name>yarn.resourcemanager.zk-address</name>
        <value>node1:2181,node2:2181,node3:2181</value>
 </property>
 <property>
        <name>yarn.nodemanager.aux-services</name>
        <value>mapreduce_shuffle</value>
 </property>
</configuration>
```

先将hadoop文件拷贝到另外两台

```
scp -r /usr/local/src/hadoop/ root@node2:/usr/local/src/
scp -r /usr/local/src/hadoop/ root@node3:/usr/local/src/
```

##### HA模式启动服务

* 1、首先将每个zookeeper启动

```
zkServer.sh start
```

```
[root@node1 ~]# jps
2147 QuorumPeerMain
2277 Jps
```

* 2、每个节点先启动journalnode

```
hadoop-daemon.sh start journalnode
```

```
[root@node1 ~]# jps
7524 QuorumPeerMain
8078 JournalNode
11406 Jps
```

* 3、首先在主节点node1格式化namenode和journalnode目录

```python
hdfs namenode -format
```

```
19/11/20 14:17:08 INFO common.Storage: Storage directory /usr/local/src/hadoop/tmp/dfs/name has been successfully formatted.
19/11/20 14:17:09 INFO namenode.NNStorageRetentionManager: Going to retain 1 images with txid >= 0
19/11/20 14:17:09 INFO util.ExitUtil: Exiting with status 0
19/11/20 14:17:09 INFO namenode.NameNode: SHUTDOWN_MSG: 
/************************************************************
SHUTDOWN_MSG: Shutting down NameNode at node1/192.168.1.107
************************************************************/
```

* 4、在某一个 namenode 节点（hp1）执行如下命令，创建命名空间

```
hdfs zkfc -formatZK
```

```

19/11/20 14:34:28 INFO ha.ActiveStandbyElector: Successfully created /hadoop-ha/ns in ZK.
```

* 5、在主 namenode 节点（hp1）启动 namenode 进程

```
hadoop-daemon.sh start namenode
```

```
[root@node1 ~]# jps
11553 Jps
7524 QuorumPeerMain
11483 NameNode
8078 JournalNode
```

* 6、在node2设置备用namenode，从node1中copy过来，不在重新格式化，直接启动namenode

```
hdfs namenode -bootstrapStandby
```

```
19/11/20 14:25:47 INFO util.ExitUtil: Exiting with status 0
```

```
hadoop-daemon.sh start namenode
```

```
[root@node2 ~]# jps
7360 QuorumPeerMain
9426 Jps
9212 JournalNode
9356 NameNode
```

* 7、在两个 namenode 节点（hp1,hp2）都执行以下命令

```
hadoop-daemon.sh start zkfc
```

```
[root@node1 ~]# jps
7524 QuorumPeerMain
11483 NameNode
11981 Jps
8078 JournalNode
11918 DFSZKFailoverController
```

* 8、所有的DataNode节点都启动datanode

```
hadoop-daemon.sh start datanode
```

```
[root@node3 ~]# jps
12273 JournalNode
12355 DataNode
9412 QuorumPeerMain
12396 Jps
```

* 9、启动yarn（node1）

```
start-yarn.sh
```

```
[root@node1 ~]# jps
12113 ResourceManager
7524 QuorumPeerMain
11483 NameNode
8078 JournalNode
11918 DFSZKFailoverController
12367 Jps
```

* 10、查看namenode状态

```
hdfs haadmin -getServiceState nn1
hdfs haadmin -getServiceState nn2
```

```
[root@node1 ~]# hdfs haadmin -getServiceState nn1
active
[root@node1 ~]# hdfs haadmin -getServiceState nn2
standby
```

* 11、查看集群状态

> 如果Live datanodes不为0则集群成功

```
hdfs dfsadmin -report
```

```
Live datanodes (1)
```

12、web 页面访问 hadoop

> hp1 为对应的ip 地址

- hadoop 地址

[http://node1:50070](http://node1:50070)

- yarn 地址

[http://node1:8088](http://node1:8088/)

- hbase 地址

[http://node1:16010](http://node1:16010/)

### sqoop安装

1、解压sqoop安装包到指定路径

```python 
tar -zxvf sqoop-1.4.7.bin__hadoop-2.6.0 -C /usr/local/src
mv sqoop-1.4.7.bin__hadoop-2.6.0/ sqoop #改名
```

2、进入配置文件目录

```
 cd /usr/local/src/sqoop/conf/
```

3、复制sqoop-env-template.sh文件

```
cp sqoop-env-template.sh sqoop-env.sh
```

> 没用到 Hive 和 HBase 可以不用配置相关项，使用时会弹出警告

```
export HADOOP_COMMON_HOME=/usr/local/src/hadoop
export HADOOP_MAPRED_HOME=/usr/local/src/hadoop
export HBASE_HOME=/usr/local/src/hbase
export HIVE_HOME=/usr/local/src/hive
export ZOOCFGDIR=/usr/local/src/zookeeper/conf
export ZOOKEEPER_HOME=/usr/local/src/zookeeper
```

4、复制MySQL的jar包到指定位置

```
cp mysql-connector-java-5.1.46-bin.jar /usr/local/src/sqoop/lib/
```

5、验证

```
sqoop version
```

6、链接测试

```
sqoop list-databases --connect jdbc:mysql://node1:3306/ --username root --password 123
```

7、将 mysql 中数据导入到 hdfs 中

```
mysql -uroot -p123
create database hotels;
use hotels;
```

```
CREATE TABLE IF NOT EXISTS `hotel`(
   `id` INT UNSIGNED AUTO_INCREMENT,
   `name` VARCHAR(255) NOT NULL,
   `diamond` VARCHAR(255) NOT NULL,
   `last_order` VARCHAR(255) NOT NULL,
   `address` VARCHAR(255) NOT NULL,
   `score` VARCHAR(255) NOT NULL,
   `level` VARCHAR(255) NOT NULL,
   `recommend` VARCHAR(255) NOT NULL,
   `commend_people` VARCHAR(255) NOT NULL,
   `commend` VARCHAR(255) NOT NULL,
   PRIMARY KEY ( `id` )
)ENGINE=InnoDB DEFAULT CHARSET=utf8;
```

```
insert into hotel (name,diamond,last_order,address,score,level,recommend,commend_people,commend) values ('长沙华晨豪生大酒店','豪华型','最新预订：2分钟前','雨花区万家丽中路二段8号(长沙大道与万家丽中路交叉处、近高桥居然之家)。','4.7','很好','98%','903','早餐丰富')
```

```
sqoop import   \
--connect jdbc:mysql://hp1:3306/hotels   \
--username root  \
--password qwe   \
--table hotel   \
-m 1
```

### hbase安装

1、进入配置文件目录

```
cd /usr/local/src/hbase/conf/
```

2、编辑hbase-env.sh

```
hbase-env.sh

export JAVA_HOME=/usr/local/src/jdk
```

3、编辑hbase-site.xml

```
vim hbase-site.xml

<configuration>
 <property>
    <name>hbase.rootdir</name>
    <value>hdfs://node1:9000/hbase</value>
  </property>
  <property>
    <name>hbase.cluster.distributed</name>
    <value>true</value>
  </property>
  <property>
    <name>hbase.zookeeper.quorum</name>
    <value>node1,node2,node3</value>
  </property>
  <property>
    <name>hbase.zookeeper.property.dataDir</name>
    <value>/usr/local/src/zookeeper/data/</value>
  </property>
</configuration>
```

5、将hbase所有配置拷贝到其他所有节点

```
scp -r /usr/local/src/hbase/ node2:/usr/local/src/
scp -r /usr/local/src/hbase/ node3:/usr/local/src/
```

6、启动 hbase

> 启动 HBase 时需要确保 hdfs 已经启动，使用命令 hdfs dfsadmin -report 查看以下 HDFS 集群是否正常
>
> 如果正常，在 master 节点上执行以下命令启动 HBase 集群:

```
start-hbase.sh

[root@node1 ~]# jps
12113 ResourceManager
15665 Jps
7524 QuorumPeerMain
15477 HMaster
11483 NameNode
15420 HQuorumPeer
8078 JournalNode
11918 DFSZKFailoverController
15598 HRegionServer
```

7、测试

```
hbase shell
```

8、web 访问 hbase

```
http://hp1:16010
```

### hive安装

1、进入配置文件目录

```
cd /usr/local/src/hive/conf/
```

2、复制配置文件

```
cp hive-env.sh.template hive-env.sh
cp hive-default.xml.template hive-site.xml

# 创建文件夹，hive-site.xml 中配置需要
mkdir -p /usr/local/src/hive/tmp
```

3、编辑(vim hive-env.sh)

```
export JAVA_HOME=/usr/local/src/jdk
export HADOOP_HOME=/usr/local/src/hadoop
export HIVE_HOME=/usr/local/src/hive
export HIVE_CONF_DIR=/usr/local/src/hive/conf
```

4、编辑 （vim hive-site.xml）

> 注意修改数据库连接密码 qwe
>
> 没有的 property 则添加，有的则根据需求修改

```
<configuration>
    <property>
        <name>system:java.io.tmpdir</name>
        <value>/usr/local/src/hive/tmp</value>
    </property>
    <property>
        <name>javax.jdo.option.ConnectionURL</name>
        <value>jdbc:mysql://node1:3306/hive?createDatabaseIfNotExist=true</value>
    </property>
    <property>
        <name>javax.jdo.option.ConnectionDriverName</name>
        <value>com.mysql.jdbc.Driver</value>
    </property>
    <property>
        <name>javax.jdo.option.ConnectionUserName</name>
        <value>root</value>
    </property>
    <property>
        <name>javax.jdo.option.ConnectionPassword</name>
        <value>qwe</value>
    </property>
</configuration>
```

```
sed -i "s#\${system:java.io.tmpdir}/\${system:user.name}#/usr/local/src/hive/iotmp#g" /usr/local/src/hive/con
```

5、 复制 mysql 数据库驱动包到指定文件路径

```
cp mysql-connector-java-5.1.46-bin.jar /usr/local/src/hive/lib/
```

6、在 hdfs 中创建下面的目录 ，并赋予所有权限

```
hdfs dfs -mkdir -p /usr/local/src/hive/warehouse
hdfs dfs -mkdir -p /usr/local/src/hive/tmp
hdfs dfs -mkdir -p /usr/local/src/hive/log
hdfs dfs -chmod -R 777 /usr/local/src/hive/warehouse
hdfs dfs -chmod -R 777 /usr/local/src/hive/tmp
hdfs dfs -chmod -R 777 /usr/local/src/hive/log
```

7、初始化 hive

```
schematool -dbType mysql -initSchema

Metastore connection URL:     jdbc:mysql://hp:3306/hive?createDatabaseIfNotExist=true
Metastore Connection Driver :     com.mysql.jdbc.Driver
Metastore connection User:     root
Starting metastore schema initialization to 1.1.0
Initialization script hive-schema-1.1.0.mysql.sql
Initialization script completed
schemaTool completed
```

> 如果没有以上返回，则初始化失败，见常见问题

8、安装hive到此结束，进入hive命令行

```
hive

```

9、常见问题

[常见问题](http://www.itkeyword.com/doc/985187200597055x509)

> 因为版本原因，需要重新拷贝一个 jar 包

```
rm -rf /usr/local/src/hadoop/share/hadoop/yarn/lib/jline-0.9.94.jar

cp /usr/local/src/hive/lib/jline-2.12.jar /usr/local/src/hadoop/share/hadoop/yarn/lib/
```

> 初始化后如果想要再重启服务

```
hive --service metastore &
hive --service hiveserver2 &
```

> 这个在1.4.7中需要配置，否则在执行数据导入到hive时会报错
>
> ```
> Could not load org.apache.hadoop.hive.conf.HiveConf. Make sure HIVE_CONF_DIR is set correctly.
> ```

```
cp /usr/local/src/hive/lib/hive-exec-1.1.0.jar /usr/local/src/sqoop/lib/
```

### spark安装

##### 1、下载

```
curl -O https://www.apache.org/dyn/closer.lua/spark/spark-2.4.4/spark-2.4.4-bin-hadoop2.7.tgz

tar -zxvf spark-2.4.4-bin-hadoop2.7.tgz
```

##### 2、配置

```
cd spark-2.4.4-bin-hadoop2.7/conf
cp spark-env.sh.template spark-env.sh
cp spark-defaults.conf.template spark-defaults.conf
cp slaves.template slaves
```

###### spark-env.sh

```
export JAVA_HOME=/root/jdk1.8.0_221
export SPARK_MASTER_IP=192.168.0.128
```

###### slaves

```
node1
node2
```

###### spark-defaults.conf

```
spark.master                     spark://master:7077
spark.eventLog.enabled           true
spark.driver.cores               2
spark.driver.memory              2g
spark.executor.memory            7g
spark.rpc.message.maxSize        1024
```

##### 3、启动

```
/usr/local/src/spark-2.4.4-bin-hadoop2.7/sbin/start-all.sh

jps
```

## 使用spark

##### 1、使用示例代码

```
spark-submit  --class org.apache.spark.examples.JavaWordCount /usr/local/src/spark/examples/jars/spark-examples_2.11-2.4.4.jar  /usr/local/src/spark/README.md
```

```
spark-submit  --class org.apache.spark.examples.JavaSparkPi /usr/local/src/spark/examples/jars/spark-examples_2.11-2.4.4.jar  /usr/local/src/spark/README.md
```

```
spark-submit /usr/local/src/spark/examples/src/main/python/pi.py 10
```

```
cd spark-2.4.4-bin-hadoop2.7/
./bin/spark-shell

[root@master spark-2.4.4-bin-hadoop2.7]# ./bin/pyspark 
Python 2.7.5 (default, Oct 30 2018, 23:45:53) 
[GCC 4.8.5 20150623 (Red Hat 4.8.5-36)] on linux2
Type "help", "copyright", "credits" or "license" for more information.
19/10/14 14:36:04 WARN NativeCodeLoader: Unable to load native-hadoop library for your platform... using builtin-java classes where applicable
Using Spark's default log4j profile: org/apache/spark/log4j-defaults.properties
Setting default log level to "WARN".
To adjust logging level use sc.setLogLevel(newLevel). For SparkR, use setLogLevel(newLevel).
19/10/14 14:36:05 WARN Utils: Service 'SparkUI' could not bind on port 4040. Attempting port 4041.
Welcome to
      ____              __
     / __/__  ___ _____/ /__
    _\ \/ _ \/ _ `/ __/  '_/
   /__ / .__/\_,_/_/ /_/\_\   version 2.4.4
      /_/

Using Python version 2.7.5 (default, Oct 30 2018 23:45:53)
SparkSession available as 'spark'.
```

![pi](https://hlyani.github.io/imgs/pi.png)

###### vim /root/spark-2.4.4-bin-hadoop2.7/examples/src/main/python/pi.py

```
from __future__ import print_function

import sys
from random import random
from operator import add

from pyspark.sql import SparkSession


if __name__ == "__main__":
    """
        Usage: pi [partitions]
    """
    spark = SparkSession\
        .builder\
        .appName("PythonPi")\
        .getOrCreate()

    partitions = int(sys.argv[1]) if len(sys.argv) > 1 else 2
    n = 100000 * partitions

    def f(_):
        x = random() * 2 - 1
        y = random() * 2 - 1
        return 1 if x ** 2 + y ** 2 <= 1 else 0

    count = spark.sparkContext.parallelize(range(1, n + 1), partitions).map(f).reduce(add)
    print("Pi is roughly %f" % (4.0 * count / n))
    result = [4.0*count/n]
    spark.sparkContext.parallelize(result, 1).saveAsTextFile("/opt/hl")

    spark.stop()
textFile = spark.read.text("README.md")
textFile.count()
textFile.first()
linesWithSpark = textFile.filter(textFile.value.contains("Spark"))

hadoop dfsadmin -safemode leave

spark-submit --executor-memory 8G --num-executors 2 --master yarn --deploy-mode cluster --driver-memory 2G pie.py 10

yarn logs -applicationId application_1571131648061_0016
8080 spark
8088 yarn
50070 handoop
```

### 重启 hadoop 相关环境

1、启动各个节点（hp1、hp2、hp3）的zookeper服务

```
zkServer.sh start
```

```
zkServer.sh status
```

2、启动各个节点（hp1、hp2、hp3）的 journalnode 服务

```
hadoop-daemon.sh start journalnode
```

3、启动主节点（hp1）的 namenode 服务

```
hadoop-daemon.sh start namenode
```

4、启动备用节点（hp2）的 namenode 服务

```
hdfs namenode -bootstrapStandby
```

```
hadoop-daemon.sh start namenode
```

5、启动各个节点（hp1、hp2、hp3）的 datanode 服务

```
hadoop-daemon.sh start datanode
```

6、在两个 namenode 节点（hp1、hp2）分别启动zkfc服务

```
hadoop-daemon.sh start zkfc
```

7、启动 yarn 服务

```
start-yarn.sh
```