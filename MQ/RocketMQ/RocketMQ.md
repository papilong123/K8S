# RocketMQ-4.2.0

## 介绍

RocketMQ是一款分布式消息系统，最初是由阿里巴巴消息中间件团队研发并大规模应用于生产系统，满足线上海量堆积的需求，在去年捐赠给Apache开源基金会，并列为孵化项目，2019年成功的正式成为了apache顶级项目；早期阿里曾经基于ActiveMQ研发的消息系统，随着业务消息的规模增大，瓶颈逐渐明显，后来也考虑过Kafka，但是因为在低延迟和高可靠性方面没有选择，最后才自主研发了RocketMQ，各方面的性能都比目前已有的消息队列要好，RocketMQ和Kafka在原理和概念上都非常相似，所以也经常被拿来对比；RocketMQ默认采用长轮询的拉模式，单机支持千万级别的消息堆积，可以非常好的应用在海量消息系统中，下面主要叙述一下RocketMQ的安装配置过程和一些相关的概念，因为rocketmq能够很方便的扩展到分布式集群，所以单机情况下也可以很好的说明，所以下面操作都在一台服务器上执行。

## 解压后目录结构![image-20210427203515238](C:\Users\a\AppData\Roaming\Typora\typora-user-images\image-20210427203515238.png)

```bash
unzip rocketmq-all-4.1.0-incubating-bin-release.zip
mv rocketmq-all-4.1.0-incubating /monchickey/
cd /monchickey/rocketmq-all-4.1.0-incubating/
```

```bash
nohup ./bin/mqnamesrv &
```

启动broker

```
nohup ./bin/mqbroker -n monchickey:9876 &
```

启动失败

![img](http://www.iigrowing.cn/wp-content/uploads/2019/06/0b13ffec86cbe8e3fcf3264d31c1eec0.png)

修改runbroker.sh，这里broker堆内存最大值和初始值都为8G，年轻代大小为4G，因为是测试环境所以xms和xmx都配置为4g，xmn配置为512m即可，配置完成后再次执行上面的命令启动即可，启动成功后日志位置同样是在家目录下，同样使用jps查看会看到BrokerStartup就是broker的进程

![img](http://www.iigrowing.cn/wp-content/uploads/2019/06/ac70462462855dc824ebd68c87b64cfe.png)

jps可以看到BrokerStartup就是broker的进程

![img](http://www.iigrowing.cn/wp-content/uploads/2019/06/c43e5f05fb39f6351d13c73f8f729b5d.png)





补充一下， 查看 broker 重要配置信息用

[mq@dev1 bin]$ sh mqbroker -m    #查看重要配置文件内容如下：
namesrvAddr=
brokerIP1=172.16.89.1
brokerName=dev1
brokerClusterName=DefaultCluster
brokerId=0
autoCreateTopicEnable=true
autoCreateSubscriptionGroup=true
msgTraceTopicName=RMQ_SYS_TRACE_TOPIC
traceTopicEnable=false
rejectTransactionMessage=false
fetchNamesrvAddrByAddressServer=false
transactionTimeOut=6000
transactionCheckMax=15
transactionCheckInterval=60000
aclEnable=false
storePathRootDir=/home/mq/store
storePathCommitLog=/home/mq/store/commitlog
flushIntervalCommitLog=500
commitIntervalCommitLog=200
flushCommitLogTimed=false
deleteWhen=04
fileReservedTime=72
maxTransferBytesOnMessageInMemory=262144
maxTransferCountOnMessageInMemory=32
maxTransferBytesOnMessageInDisk=65536
maxTransferCountOnMessageInDisk=8
accessMessageInMemoryMaxRatio=40
messageIndexEnable=true
messageIndexSafe=false
haMasterAddress=
brokerRole=ASYNC_MASTER
flushDiskType=ASYNC_FLUSH
cleanFileForciblyEnable=true
transientStorePoolEnable=false
flushDiskType=ASYNC_FLUSH
cleanFileForciblyEnable=true
transientStorePoolEnable=false

 

类似的：

sh mqbroker -p 打印全部的配置文件信息， 有了这些信息， 您就可以 到属性文件中进行设置



部署架构

![1](http://learn.lianglianglee.com/%E4%B8%93%E6%A0%8F/RocketMQ%20%E5%AE%9E%E6%88%98%E4%B8%8E%E8%BF%9B%E9%98%B6%EF%BC%88%E5%AE%8C%EF%BC%89/assets/20200726212547918.png)



**kafka的topic**里面有**分区和副本**的概念，使用之前都是根据数据量**手动创建**，对于rocketmq也一样，需要手动创建topic，rocketmq的topic中有队列(queue)的概念，也就是说一个节点上面可以有多个队列，这样能非常大的提高并发性，而kafka最多只能是一个分区一个进程消费，这样并发性限制非常大，并且单机分区数量不能过多，超过64个分区就出现明显的不稳定，但是rocketmq单机支持上万队列，所以并发性能非常好；类比一下，rocketmq中一个broker-name其实就相当于kafka broker中的一个partition，而rocketmq每一个slave就相当于kafka中的一个replication，这种情况，所以rocketmq的特点相当于**单个partition支持多队列**，大致的原理图如下：

![img](https://images2017.cnblogs.com/blog/734555/201711/734555-20171111001406903-142477586.png)