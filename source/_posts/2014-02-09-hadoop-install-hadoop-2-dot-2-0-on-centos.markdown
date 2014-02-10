---
layout: post
title: "Hadoop : Install Hadoop 2.2.0 on CentOS"
date: 2014-02-09 20:12
comments: true
categories: Hadoop
---

Prerequisitive <br>
Download java from : http://www.oracle.com/technetwork/java/javase/downloads/jdk7-downloads-1880260.html <br>

```
tar xzf jdk-7u51-linux-x64.gz -C /usr/lib/jvm
```
Check java version and create link jdk
```
$java --version
...
$ln -s jdk1.7.0_51 jdk
```

Create User And Group <br>
```
$groupadd hadoop
$useradd -g hadoop hduser
$passwd hduser
$su hduser
```

Setup SSH Certificate <br>
```
$ssh-keygen -t rsa -P ''
...
Your identification has been saved in /home/hduser/.ssh/id_rsa.
Your public key has been saved in /home/hduser/.ssh/id_rsa.pub.
...
$cat ~/.ssh/id_rsa.pub >> ~/.ssh/authorized_keys
$ssh localhost
```

Install Hadoop <br>
Download hadoop tarball from: http://archive.apache.org/dist/hadoop/core/hadoop-2.2.0/
```
$sudo tar -xvzf hadoop-2.2.0.tar.gz -C /user/local
$cd /user/local
$sudo mv hadoop-2.2.0 hadoop
$sudo chown -R hduser:hadoop hadoop
```

Setup Hadoop Environment Variables
```
$cd ~
$vi .bashrc
```
Add following:
```
# Hadoop vairables
export JAVA_HOME=/usr/lib/jvm/jdk
export JRE_HOME=/usr/lib/jvm/jdk/jre
export HADOOP_INSTALL=/usr/local/hadoop
export PATH=$PATH:$HADOOP_INSTALL/bin
export PATH=$PATH:$HADOOP_INSTALL/sbin
export PATH=$PATH:$JRE_HOME/bin
export PATH=$PATH:$JAVA_HOME/bin
export HADOOP_MAPRED_HOME=$HADOOP_INSTALL
export HADOOP_COMMON_HOME=$HADOOP_INSTALL
export HADOOP_HDFS_HOME=$HADOOP_INSTALL
export YARN_HOME=$HADOOP_INSTALL
```

```
$cd /usr/local/hadoop/etc/hadoop
$vi hadoop-env.sh
#modify JAVA_HOME in file to below
export JAVA_HOME=/usr/lib/jvm/jdk
```

Relogin with hduser and check hadoop version
```
$hadoop version
Hadoop 2.2.0
Subversion https://svn.apache.org/repos/asf/hadoop/common -r 1529768
Compiled by hortonmu on 2013-10-07T06:28Z
Compiled with protoc 2.5.0
From source with checksum 79e53ce7994d1628b240f09af91e1af4
This command was run using /usr/local/hadoop/share/hadoop/common/hadoop-common-2.2.0.jar
```

Configure Hadoop
```
$cd /usr/local/hadoop/etc/hadoop
$vi core-site.xml
#paste following between <configuration> tag
<property>
<name>fs.default.name</name>
<value>hdfs://localhost:9000</value>
</property>
<property>
<name>dfs.permissions</name>
<value>false</value>
</property>
```
```
$vi yarn-site.xml
#paste following between <configuration> tag
<property>
<name>yarn.nodemanager.aux-services</name>
<value>mapreduce_shuffle</value>
</property>
<property>
<name>yarn.nodemanager.aux_services.mapreduce.shuffle.class</name>
<value>org.apache.hadoop.mapred.ShuffleHandler</value>
</property>
```
```
$cd ~
$mkdir -p mydata/hdfs/namenode
$mkdir -p mydata/hdfs/datanode
$cd /usr/local/hadoop/etc/hadoop
$vi hdfs-site.xml
#paste following between <configuration> tag
<property>
<name>dfs.replication</name>
<value>1</value>
</property>
<property>
<name>dfs.namenode.name.dir</name>
<value>file:/home/hduser/mydata/hdfs/namenode</value>
</property>
<property>
<name>dfs.datanode.data.dir</name>
<value>file:/home/hduser/mydata/hdfs/datanode</value>
</property>
```

Format Namenode (Onetime)<br>
```
$hadoop namenode -format
```

Start HDFS processes and Map-Reduce process
```
#HDFS
$hadoop-daemon.sh start namenode
$hadoop-daemon.sh start datanode
#Map-Reduce
$yarn-daemon.sh start resourcemanager
$yarn-daemon.sh start nodemanager
$mr-jobhistory-daemon.sh start historyserver
```

Verifying Installation <br>
```
$jps
1565 JobHistoryServer
1037 DataNode
960 NameNode
1180 ResourceManager
1420 NodeManager
15250 Jps
```

Run Hadoop Example <br>
```
$cd /usr/local/hadoop
hadoop jar ./share/hadoop/mapreduce/hadoop-mapreduce-examples-2.2.0.jar pi 2 5
Number of Maps  = 2
Samples per Map = 5
Java HotSpot(TM) 64-Bit Server VM warning: You have loaded library /usr/local/hadoop/lib/native/libhadoop.so.1.0.0 which might have disabled stack guard. The VM will try to fix the stack guard now.
It's highly recommended that you fix the library with 'execstack -c <libfile>', or link it with '-z noexecstack'.
14/02/09 14:14:01 WARN util.NativeCodeLoader: Unable to load native-hadoop library for your platform... using builtin-java classes where applicable
Wrote input for Map #0
Wrote input for Map #1

...
        File Input Format Counters
                Bytes Read=236
        File Output Format Counters
                Bytes Written=97
Job Finished in 11.952 seconds
Estimated value of Pi is 3.60000000000000000000
```

Source:<br> http://codesfusion.blogspot.com/2013/10/setup-hadoop-2x-220-on-ubuntu.html?m=1<br>
http://nextgenhadoop.blogspot.com/2013/10/steps-to-install-hadoop-220-stable.html<br>
http://tecadmin.net/steps-to-install-hadoop-on-centosrhel-6/# <br>







