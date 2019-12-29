---
title: Canal数据同步
date: 2019-03-04 15:33:01
tags:
   - Canal
   - Mysql
categories:
   - Canal
---

> 背景：阿里巴巴B2B公司存在杭州和美国双机房部署，存在跨机房同步的业务需求，当时早期的数据同步业务， 主要是基于trigger的方式获取增量变更数据；
> 2010开始，阿里开始逐步尝试基于数据库的日志解析，获取增量变更进行同步，由此衍生出增量订阅&消费的业务；
> 定位：基于数据库增量日志准实时解析，提供增量数据订阅&消费（目前主要支持mysql）
<!-- more -->

## Mysql主从同步原理
- 架构图：
![图1](https://raw.githubusercontent.com/aikaiqiang/aikq-blog-comments/master/notepic/%E5%9B%BE%E7%89%871.jpg)
Mysql Slave同步原理：
a. I/O thread接受binlog
b. SQL thread执行变更

- Binlog Dump交互
![图2](https://raw.githubusercontent.com/aikaiqiang/aikq-blog-comments/master/notepic/%E5%9B%BE%E7%89%872.png)
![图3](https://raw.githubusercontent.com/aikaiqiang/aikq-blog-comments/master/notepic/%E5%9B%BE%E7%89%873.png)


* Mysql 的binlog [官链](https://dev.mysql.com/doc/internals/en/binary-log.html)
## Canal 使用
> canal的原理是基于mysql binlog技术，所以这里一定需要开启mysql的binlog写入功能，建议配置binlog模式为row;
### 安装Canal Server
- 下载安装包，解压到自定义目录即可；[下载地址](https://github.com/alibaba/canal/releases)
- 修改配置文件，进入canal目录下：
![图4](https://raw.githubusercontent.com/aikaiqiang/aikq-blog-comments/master/notepic/20190304150020.png)
应用参数：`vi conf/example/instance.properties`
```
#################################################
## mysql serverId , v1.0.26+ will autoGen
# canal.instance.mysql.slaveId=0
# enable gtid use true/false
canal.instance.gtidon=false
# position info / 需要改成自己的数据库信息
canal.instance.master.address=127.0.0.1:3306
canal.instance.master.journal.name=
canal.instance.master.position=
canal.instance.master.timestamp=
canal.instance.master.gtid=
# rds oss binlog
canal.instance.rds.accesskey=
canal.instance.rds.secretkey=
canal.instance.rds.instanceId=
# table meta tsdb info
canal.instance.tsdb.enable=true
#canal.instance.tsdb.url=jdbc:mysql://127.0.0.1:3306/canal_tsdb
#canal.instance.tsdb.dbUsername=canal
#canal.instance.tsdb.dbPassword=canal
#canal.instance.standby.address =
#canal.instance.standby.journal.name =
#canal.instance.standby.position =
#canal.instance.standby.timestamp =
#canal.instance.standby.gtid=
# username/password, 需要改成自己的数据库信息
canal.instance.dbUsername=canal
canal.instance.dbPassword=canal
canal.instance.connectionCharset = UTF-8
canal.instance.defaultDatabaseName =test
# enable druid Decrypt database password
canal.instance.enableDruid=false
#canal.instance.pwdPublicKey=MFwwDQYJKoZIhvcNAQEBBQADSwAwSAJBALK4BUxdDltRRE5/zXpVEVPUgunvscYFtEip3pmLlhrWpacX7y7GCMo2/JM6LeHmiiNdH1FWgGCpUfircSwlWKUCAwEAAQ==
# table regex
canal.instance.filter.regex=.*\\..*
# table black regex
canal.instance.filter.black.regex=
# mq config
canal.mq.topic=example
# dynamic topic route by table regex
#canal.mq.dynamicTopic=.*,mytest\\..*,mytest2.user
canal.mq.partition=0
# hash partition config
#canal.mq.partitionsNum=3
#canal.mq.partitionHash=test.table:id^name,.*\\..*
#################################################
```

- 启动canal
命令：`sh bin/startup.sh` #启动  `sh bin/stop.sh`# 停止

- 查看日志
命令：`tail -f logs/example/example.log`

### Canal 客户端使用
[参考地址](https://github.com/alibaba/canal/wiki/ClientExample)


## Canal HA模式
- Canal的ha分为两部分，canal server和canal client分别有对应的ha实现：
1. canal server:  为了减少对mysql dump的请求，不同server上的instance要求同一时间只能有一个处于running，其他的处于standby状态;
2. canal client: 为了保证有序性，一份instance同一时间只能由一个canal client进行get/ack/rollback操作，否则客户端接收无法保证有序
- HA机制的控制主要是依赖了zookeeper的几个特性，watcher和EPHEMERAL节点(和session生命周期绑定)
## Canal Kafka RocketMQ QuickStart


## 资料文件
Canal介绍PPT：[下载地址](https://raw.githubusercontent.com/aikaiqiang/aikq-blog-comments/master/canal%E4%BA%A7%E5%93%81%E4%BB%8B%E7%BB%8D.pptx)
