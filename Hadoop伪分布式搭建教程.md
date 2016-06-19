# Hadoop伪分布式搭建教程

__作者：coreVK__
__版本：1.0.7__
__环境：`Ubuntu 16.04 LTS AMD64(64Bit)` 、`OpenJDK-9`、`Hadoop-1.2.1`、`SSH`__


##0. 请使用root进行所有操作

## 1. JAVA安装及配置长度

### 安装Java
安装`OpenJDK`(或自行安装`OracleJDK`)

```bash
> javac   #检查JDK是否可用
The program 'javac' can be found in the following packages:
 * default-jdk
 * ecj
 * gcj-4.9-jdk
 * openjdk-8-jdk-headless
 * gcj-4.8-jdk
 * gcj-5-jdk
 * openjdk-9-jdk
Try:  apt install <selected package>

> apt-get install openjdk-9-jdk
```

如果Ubuntu安装dpkg出现问题，如下处理并再次运行上面命令:

```bash
....
....
E:Sub-process /usr/bin/dpkg returned an error code(1)

> cd /var/lib/dpkg
> mv info infobak
> mkdir info
> mv ./info/* ./infobak
> rm -rf ./info 
> mv ./infobak ./info

> apt-get install openjdk-9-jdk
```
 

###添加Linux中Java的环境变量

JAVA安装路径在安装过程中会显示：`/usr/lib/jvm/java-9-openjdk-amd64/`

```bash
>  nano /etc/profile
#在文本中添加以下内容
export JAVA_HOME=/usr/lib/jvm/java-9-openjdk-amd64/
export JER_HOME=$JAVA_HOME/jre
export CLASSPATH=$JAVA_HOME/lib:$JRE_HOME/lib:$CLASSPATH 
export PATH=$JAVA_HOME/bin:$JRE_HOME/bin:$PATH
> source /etc/profile
> javac  # 检查Java是否可用
```
如果忘记JDK自动安装的路径

```bash
find / -name java
```

##2. Hadoop安装及配置
###下载Hadoop 1.2.1

在Linux软件包的库中没有Hadoop的包，所以我们需要使用wget从下载源中下载Hadoop包，并把安装包解压到/opt

```bash
> cd /opt
# 清华大学下载源：https://mirrors.tuna.tsinghua.edu.cn/apache/hadoop/common
> wget https://mirrors.tuna.tsinghua.edu.cn/apache/hadoop/common/hadoop-1.2.1/hadoop-1.2.1.tar.gz
> tar -zxvf hadoop-1.2.1.tar.gz
```


###配置Hadoop参数

```bash
> cd /opt/hadoop-1.2.1/conf
```

####配置hadoop-env.sh
```bash
> nano hadoop-env.sh  #在Hadoop中添加JDK路径
 export JAVA_HOME=/usr/lib/jvm/java-9-openjdk-amd64 
```
####配置core-site.xml
```bash
> nano core-site.xml
```
```vim
<configuration>
    <property>
        <name>hadoop.tmp.dir</name>
        <value>/hadoop</value>
    </property>
    <property>
        <name>dfs.name.dir</name>
        <value>/hadoop/name</value>
    </property>
    <property>
        <name>fs.default.name</name>
        <value>hdfs://localhost:9000</value>
    </property>
</configuration>
```

####配置hdfs-site.xml

```bash
> nano hdfs-site.xml
```

```vim
<configuration>
    <property>
        <name>dfs.data.dir</name>
        <value>/hadoop/data</value>
    </property>
</configuration>
```


####配置mapred-site.xml

```bash
> nano mapred-site.xml
```

```vim
<configuration>
    <property>
        <name>mapred.job.tracker</name>
        <value>localhost:9001</value>
    </property>
</configuration>
```

####配置/etc/profile

```bash
> nano /etc/profile
 export HADOOP_HOME=/opt/hadoop-1.2.1   
 #并在PATH中添加$HADOOP_HOME/bin:
> source /etc/profile
```

###初始化namenode

```bash
> hadoop namenode -format 
************************************************************
STARTUP_MSG: Starting NameNode
STARTUP_MSG:   host = mike-virtual-machine/127.0.1.1
STARTUP_MSG:   args = [-format]
STARTUP_MSG:   version = 1.2.1
STARTUP_MSG:   build = https://svn.apache.org/repos/asf/hadoop/common/branches/branch-1.2 -r 1503152; compiled by 'mattf' on Mon Jul 22 15:23:09 PDT 2013
STARTUP_MSG:   java = 9-internal
************************************************************/
16/06/18 11:36:16 INFO util.GSet: Computing capacity for map BlocksMap
16/06/18 11:36:16 INFO util.GSet: VM type       = 64-bit
16/06/18 11:36:16 INFO util.GSet: 2.0% max memory = 1048576000
16/06/18 11:36:16 INFO util.GSet: capacity      = 2^21 = 2097152 entries
16/06/18 11:36:16 INFO util.GSet: recommended=2097152, actual=2097152
16/06/18 11:36:17 INFO namenode.FSNamesystem: fsOwner=root
16/06/18 11:36:17 INFO namenode.FSNamesystem: supergroup=supergroup
16/06/18 11:36:17 INFO namenode.FSNamesystem: isPermissionEnabled=true
16/06/18 11:36:17 INFO namenode.FSNamesystem: dfs.block.invalidate.limit=100
16/06/18 11:36:17 INFO namenode.FSNamesystem: isAccessTokenEnabled=false accessKeyUpdateInterval=0 min(s), accessTokenLifetime=0 min(s)
16/06/18 11:36:17 INFO namenode.FSEditLog: dfs.namenode.edits.toleration.length = 0
16/06/18 11:36:17 INFO namenode.NameNode: Caching file names occuring more than 10 times 
16/06/18 11:36:17 INFO common.Storage: Image file /hadoop/dfs/name/current/fsimage of size 110 bytes saved in 0 seconds.
16/06/18 11:36:17 INFO namenode.FSEditLog: closing edit log: position=4, editlog=/hadoop/dfs/name/current/edits
16/06/18 11:36:17 INFO namenode.FSEditLog: close success: truncate to 4, editlog=/hadoop/dfs/name/current/edits
16/06/18 11:36:17 INFO common.Storage: Storage directory /hadoop/dfs/name has been successfully formatted.
16/06/18 11:36:17 INFO namenode.NameNode: SHUTDOWN_MSG: 
/************************************************************
SHUTDOWN_MSG: Shutting down NameNode at mike-virtual-machine/127.0.1.1
************************************************************/
```

####解决 Error: Config file not found: /usr/lib/jvm/java-9-openjdk-amd64/conf/management/management.properties

```bash
> cd /usr/lib/jvm/java-9-openjdk-amd64
> ln -s lib conf
> ls -la conf
 lrwxrwxrwx 1 root root 3 6月  16 17:24 conf -> lib
```


####解决 Warning: $HADOOP_HOME is deprecated

 在当前用户home/.bash_profile里增加一个环境变量

```vim
export HADOOP_HOME_WARN_SUPPRESS=1
```

####解决 localhost: ssh: connect to host localhost port 22: Connection refused

下载openssh-server

```bash
> ssh-agent  #启动ssh-agent
> apt-get install openssh-server
```

ssh配置


```bash
> cd /etc/ssh/ssh_config

    StrictHostKeyChecking no     # 修改为no
    UserKnownHostsFile /dev/null # 添加一行

> cd /etc/ssh/sshd_config

    PermitRootLogin prohibit-password # 改为 yes
    PasswordAuthentication prohibit-password # 取消注释

> reboot # 重启系统
> service sshd restart
> ssh localhost # 检查ssh服务
```

##3. 启动Hadoop服务
####启动并检查

```bash
> cd /opt/hadoop-1.2.1/bin
> ./start_all.sh
starting namenode, logging to /opt/hadoop-1.2.1/libexec/../logs/hadoop-root-namenode-mike-virtual-machine.out
localhost: Warning: Permanently added 'localhost' (ECDSA) to the list of known hosts.
root@localhost's password: 
localhost: 
localhost: starting datanode, logging to /opt/hadoop-1.2.1/libexec/../logs/hadoop-root-datanode-mike-virtual-machine.out
localhost: Warning: Permanently added 'localhost' (ECDSA) to the list of known hosts.
root@localhost's password: 
localhost: 
localhost: starting secondarynamenode, logging to /opt/hadoop-1.2.1/libexec/../logs/hadoop-root-secondarynamenode-mike-virtual-machine.out
starting jobtracker, logging to /opt/hadoop-1.2.1/libexec/../logs/hadoop-root-jobtracker-mike-virtual-machine.out
localhost: Warning: Permanently added 'localhost' (ECDSA) to the list of known hosts.
root@localhost's password: 
localhost: 
localhost: starting tasktracker, logging to /opt/hadoop-1.2.1/libexec/../logs/hadoop-root-tasktracker-mike-virtual-machine.out

> jps
 2531 DataNode
 2524 NameNode
 2678 SecondaryNameNode
 2778 JobTracker
 2940 TaskTracker
 3039 sun.tools.jps.Jps
```

##4. 其他问题

####解决/etc/profile失效
```bash
> cd ~
> ls -la
> nano .bashrc
# 添加环境变量
  export HADOOP_HOME_WARN_SUPPRESS=1
  export HADOOP_HOME=/opt/hadoop-1.2.1
  export JAVA_HOME=/usr/lib/jvm/java-9-openjdk-amd64/
  export JER_HOME=$JAVA_HOME/jre
  export CLASSPATH=$JAVA_HOME/lib:$JRE_HOME/lib:$CLASSPATH
  export PATH=$JAVA_HOME/bin:$JRE_HOME/bin:$HADOOP_HOME/bin:$PATH
> source .bashrc
```

