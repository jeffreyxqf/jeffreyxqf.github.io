---
layout: post
title:  "在Mac环境下搭建Hadoop伪分布式集群"
date:  2018-01-27 09:12 +0800
tags: Hadoop 分布式 BigData
categories: Hadoop
---
最近尝试在mac上搭建Hadoop集群，发现资料不是很多，加之自己也是刚刚接触mac操作系统，对一些操作以及网络配置不是很熟悉，在搭建过程中遇到了一些困难。利用下班后的时间，花了2个晚上终于顺利的将集群搭建了起来，整理了这篇文章，希望可以对读者有所启发。

---

### 系统环境

       本机 ：  MacBook Pro  系统10.13
       V M ：  VMware FUsion 10.1.0
       Linux： Version 2.6.32-642
       Hadoop: Version 2.7.5
       JDK :   1.8.0_161





       
### 系统准备
#### VMware与Linux集群系统安装

在VMware上安装3台虚拟机，即一个master主机，2个slave。安装过程比较简单，在此就不详细叙述了。 不过需要注意的是，在安装虚拟机的过程中，启动时可能会出现了cannot find /dev/common 等类似的错误，Google了下，可能缘于VMware版本与mac操作系统不兼容导致的Linux安装不成功的问题，本人使用了最新版的VMware 10.1.0 版本 以及Linux 2.6版本安装成功。

##### 网络配置 

 nat模式 固定ip配置 
 
##### 1.本机操作
 VMware安装完成后，在本机终端找到其对应的网络配置：
 
 ![ ](https://jeffreyxqf.github.io/source/hadoop/VM%20Net.png)

 可以看到VMnet8 子网IP地址为：192.168.236.0
 
  获取getaway address ： 
  
 ![ ](https://jeffreyxqf.github.io/source/hadoop/VM%20Net.png)
 
  可以看到网关地址为： 192.168.236.2
  
##### 2.虚拟机操作 静态ip地址设置（在虚拟机上完成）：
  
  可以看到，master虚拟机ip地址设置成192.168.236.100
  Getaway与主机相同。 两个slaves ip地址分别设置为192.168.236.101，192.168.236.102.
  
  当然也可以使用界面配置，如： 
  
 ![ ](https://jeffreyxqf.github.io/source/hadoop/Linux net configuration.png)
  
##### 3.修改host文件 

   vim /etc/hosts
   
	   127.0.0.1	localhost
		255.255.255.255	broadcasthost
		::1             localhost
		192.168.236.100 master
		192.168.236.101 slave1
		192.168.236.102 slave2   
			
  主机虚拟机一样操作，让其能找到对方。。。
   
   测试 ： 
   
   ![ ](https://jeffreyxqf.github.io/source/hadoop/ping.png)
   
可以看到主机与虚拟机，虚拟机之间已实现了互联，自己摸索，不一定是最佳实践哈。。。
	
### JDK 的安装与配置

##### 1. 下载，上传，解压，安装 比较简单不再详述

##### 2. 配置环境变量
     vim /etc/profile 
     
	export JAVA_HOME=/usr/local/jdk1.8.0_161
	export CLASSPATH=$JAVA_HOME/lib:$JAVA_HOME/jre/lib
	export PATH=$PATH:$JAVA_HOME/bin:$JAVA_HOME/jre/bin    
	
##### 3. 重新加载/etc/profile,使配置立即生效
     source /etc/profile
     
##### 4. 测试
    echo $PATH
    java -version 
    ... 
 
### Hadoop 的安装与配置
#####  1. 下载
   我使用的是Hadoop 2.7.5 版本，可以直接从官网下载 ： 
    [http://www.apache.org/dyn/closer.cgi/hadoop/common/](http://www.apache.org/dyn/closer.cgi/hadoop/common/) ，当然也可以使用镜像下载。
    
附上自己的Hadoop： 

链接:[https://pan.baidu.com/s/1g3Bg5y8nvxiS2gkNTGksvg](https://pan.baidu.com/s/1g3Bg5y8nvxiS2gkNTGksvg) 密码:99c7
    
#####  2. 创建工作目录，方便管理NameNode/DataNode/以及零时文件
    /data/hdfs/name
    /data/hdfs/data
    /data/hdfs/tmp  
    
将下载好的Hadoop解压至/data目录 ： 

    tar -zxvf hadoop-2.7.5.tar.gz
  
#####   3. 配置环境变量 

	   vim /etc/profile
	   export HADOOP_HOME=/data/hadoop-2.7.5
	   export JAVA_HOME=/usr/local/jdk1.8.0_161
	   export CLASSPATH=$JAVA_HOME/lib:$JAVA_HOME/jre/lib
	   export PATH=$PATH:$JAVA_HOME/bin:$JAVA_HOME/jre/bin:$HADOOP_HOME/bin   
 
  source /etc/profile 使配置立即生效
  测试 hadoop 
  
  ![ ](https://jeffreyxqf.github.io/source/hadoop/hadoop test.png)
  
#####   4. Hadoop 配置
  
  进入Hadoop-2.7.5目录 ：  
  
     cd /data/hadoop-2.7.5/etc/hadoop
  
  修改文件 core-site.xml、hdfs-site.xml、mapred-site.xml、yarn-site.xml以及slaves文件。
  
######    a. 修改core-site.xml :  
 vim core-site.xml 
		   
		<?xml version="1.0" encoding="UTF-8"?>
		<?xml-stylesheet type="text/xsl" href="configuration.xsl">
		<configuration>
		<property>
		<name>hadoop.tmp.dir</name>
		<value>file:/data/hdfs/tmp</value>
		<description>A base for other temporary directories.</description>
		</property>
		<property>
		 <name>io.file.buffer.size</name>
		 <value>131072</value>
		</property>
		<property>
		 <name>fs.default.name</name>
		 <value>hdfs://master:9000</value>
		</property>
		<property>
		 <name>hadoop.proxyuser.root.hosts</name>
		 <value>*</value>
		</property>
		<property>
		 <name>hadoop.proxyuser.root.groups</name>
		 <value>*</value>
		</property>
		</configuration> 
		
   hadoop.tmp.dir 目录设置为上文创建的tmp目录。 
   
###### b. 修改vim hdfs-site.xml
   
	    <?xml version="1.0" encoding="UTF-8"?>
		 <?xml-stylesheet type="text/xsl" href="configuration.xsl"?>
				<configuration>
				<property>
				<name>dfs.replication</name>
				  <value>2</value>
				</property>
				<property>
				  <name>dfs.namenode.name.dir</name>
				  <value>file:/data/hdfs/name</value>
				  <final>true</final>
				</property>
				<property>
				  <name>dfs.datanode.data.dir</name>
				  <value>file:/data/hdfs/data</value>
				  <final>true</final>
				</property>
				<property>
				  <name>dfs.namenode.secondary.http-address</name>
				  <value>master:9001</value>
				</property>
				<property>
				  <name>dfs.webhdfs.enabled</name>
				  <value>true</value>
				</property>
				<property>
				  <name>dfs.permissions</name>
				  <value>false</value>
				</property>
		</configuration>  


######  c. 修改vim yarn-site.xml
		     
	    <?xml version="1.0"?>
		<configuration>
		
		<!-- Site specific YARN configuration properties -->
		<property>
		<name>yarn.resourcemanager.address</name>
		  <value>master:18040</value>
		</property>
		<property>
		  <name>yarn.resourcemanager.scheduler.address</name>
		  <value>master:18030</value>
		</property>
		<property>
		  <name>yarn.resourcemanager.webapp.address</name>
		  <value>master:18088</value>
		</property>
		<property>
		  <name>yarn.resourcemanager.resource-tracker.address</name>
		  <value>master:18025</value>
		</property>
		<property>
		  <name>yarn.resourcemanager.admin.address</name>
		  <value>master:18141</value>
		</property>
		<property>
		  <name>yarn.nodemanager.aux-services</name>
		  <value>mapreduce_shuffle</value>
		</property>
		<property>
		  <name>yarn.nodemanager.aux-services.mapreduce.shuffle.class</name>
		  <value>org.apache.hadoop.mapred.ShuffleHandler</value>
		</property>
		</configuration>

######   d. 修改vim mapred-site.xml
      
		<?xml version="1.0"?>
		<?xml-stylesheet type="text/xsl" href="configuration.xsl"?>
		<!-- Put site-specific property overrides in this file. -->
		<configuration>
		
		<property>
		  <name>mapreduce.framework.name</name>
		  <value>yarn</value>
		</property>
		
		</configuration>

######   e.  修改slaves主机名
     
    vim /data/hadoop-2.7.5/etc/hadoop/slaves
     
将其中的localhost删除改为slave1，slave2 。 
最后，将整个hadoop-2.7.5文件夹及其子文件夹复制到slave1和slave2的相同目录中。
     
#### 运行Hadoop

#####  1. 格式化NameNode
     
        hadoop namenode -format
     
 ![ ](https://jeffreyxqf.github.io/source/hadoop/dfs.png)
 
#####  2. 启动NameNode     
     
        /data/hadoop-2.7.5/sbin/hadoop-daemons.sh start datanode
	
#####  3. 启动DataNode
    
        /data/hadoop-2.7.1/sbin/hadoop-daemons.sh start datanode
	
当然1，2，3操作可以使用start-dfs.sh

 ![ ](https://jeffreyxqf.github.io/source/hadoop/start-all.png)

#####  4. 运行YARN
   
    /data/hadoop-2.7.1/sbin/start-all.sh
    
#####  5. 查看是否成功

	   jps
	   
	　　Master：
	　　SecondaryNameNode
	　　ResourceManager
	　　NameNode
	
	　　Slave：
	　　NodeManager
	　　DataNode

### 测试Hadoop

1. 查看集群状态
        
        /data/hadoop-2.7.5/bin/hdfs dfsadmin -report
   
   ![ ](https://jeffreyxqf.github.io/source/hadoop/report.png)
   
2. 测试yarn,访问管理界面

        master:18088/cluster
  
  ![ ](https://jeffreyxqf.github.io/source/hadoop/yarn.png)
    
3. 测试查看HDFS
    
        http://master:50070/dfshealth.html
 
 ![ ](https://jeffreyxqf.github.io/source/hadoop/dfs.png)
 
#### 安装Hadoop遇到的问题
    
######  1. JAVA_HOME未设置？

启动的时候报: JAVA_HOME is not set and could not be found

则需要/data/hadoop-2.7.1/etc/hadoop/hadoop-env.sh，添加JAVA_HOME路径

###### 2. slave node ， 运行 yarn 后jps node manager 没有启动 。 

Log ： 

![ ](https://jeffreyxqf.github.io/source/hadoop/node error.png)

解决 ： 

将yarn.nodemanager.aux-services项的值改为“mapreduce_shuffle”

###### 3. Stop Namenode/datanode 异常
 no xxx to stop

 ![ ](https://jeffreyxqf.github.io/source/hadoop/no ** stop.png)
 
 [http://blog.csdn.net/GYQJN/article/details/50805472](http://blog.csdn.net/GYQJN/article/details/50805472)




参考资料：

[docs.hortonworks.com](https://docs.hortonworks.com/HDPDocuments/HDP2/HDP-2.3.0/bk_installing_manually_book/content/ch_setting_up_hadoop_configuration_chapter.html)

[http://hadoop.apache.org/docs/r1.2.1/api/org/apache/hadoop/conf/Configuration.html](http://hadoop.apache.org/docs/r1.2.1/api/org/apache/hadoop/conf/Configuration.html)

[https://segmentfault.com/a/1190000009103629](https://segmentfault.com/a/1190000009103629)

[https://www.cnblogs.com/zishengY/p/6819160.html](https://www.cnblogs.com/zishengY/p/6819160.html)
