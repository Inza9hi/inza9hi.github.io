---
title: 基于OSS的Hadoop/Hive应用 
date: 2016-01-05 18:22:39
tags: 
- Hive 
- Hadoop 
- OSS
categories: 技术
---
[OSS](http://www.aliyun.com/product/oss/)是阿里云的对象存储，对应于AWS的S3和百度云的BOS。
## 技术选型
为什么要用对象存储而不用HDFS呢？
在云上搭建Hadoop集群的优势是云主机的高伸缩性和高可用性，而如果自建HDFS与优势相悖。事实上，AWS的S3甚至被Hadoop官方直接支持。
为什么不使用阿里官方的E-Mapreduce？
第一贵，一台最低主机一小时大概是4块钱，第二还在公测阶段，我自己跑了一个官方文档的group by，遇到很多问题，遇到问题还得自己登陆上去一一排查，还有我们机房可能是私有云。
## 安装
1 下载Jar包。
Aliyun官方GitHub上的SDK编译不过去，用官方Shade过的[sdk](https://help.aliyun.com/document_detail/emr/sdk/Aliyun-Spark-SDK.html?spm=5176.docemr/quick-start/prepare.6.130.H0Todk)我亲测有问题，一直报ClassNotFound，是不是因为是Maven Shade过的，所以没加载进去？

``` bash
wget http://emr-agent-pack.oss-cn-hangzhou.aliyuncs.com/emr-core/1.0.3/emr-core-1.0.3.jar

```

2 将下载的Jar加入classpath,直接放入$HADOOP_HOME/share/hadoop/common/lib,或者[修改Hadoop Classpath](http://stackoverflow.com/questions/28029134/how-can-i-access-s3-s3n-from-a-local-hadoop-2-6-installation)
3 修改Hadoop hdfs-site.xml

``` bash
<property>
    <name>fs.ossbfs.impl</name>
    <value>com.aliyun.fs.oss.blk.OssFileSystem</value>
  </property>
  <property>
    <name>fs.oss.impl</name>
    <value>com.aliyun.fs.oss.nat.NativeOssFileSystem</value>
  </property>
  <property>
    <name>fs.ossn.impl</name>
    <value>com.aliyun.fs.oss.nat.NativeOssFileSystem</value>
  </property>


```


4 注意：oss的endpoint分Internal和外部两种

