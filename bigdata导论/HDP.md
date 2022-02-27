# HDP

### 全局代理

vim /etc/profile 全局

~/.bashrc 自己的账户

添加如下：

http_proxy=http:*//username:password@yourproxy:8080/* ftp_proxy=http:*//username:password@yourproxy:8080/* 
export http_proxy 
export ftp_proxy

source /etc/profile //环境变量

### 设置快捷键

/usr/bin/gnome-terminal

### 修改IP

vim /etc/sysconfig/network-scripts/ifcfg-ens33 再重启

ifconfig 查看

### 修改host名字

hostname 查看

vim /etc/hostname

### 安装软件仓库

yum install -y epel-release

### 关闭防火墙

systemctl stop firewalld

systemctl disable firewalld.service

### 配置用户权限

vim /etc/sudoers

lxy ALL=(ALL) NOPASSWD=ALL

### 建立了目录

/opt/module

/opt/software

 系统管理员经常使用chown命令，在将文件拷贝到另一个用户的名录下之后，让用户拥有使用该文件的权限。

sudo chown lxy:lxy module/ software/  

### 安装JDK和hadoop

官网下载

JDK https://www.oracle.com/java/technologies/downloads/

hadoop https://hadoop.apache.org/

解压到/opt/module

### 配置环境变量

sudo vim /etc/profile //查看代码

cd  /etc/profile.d

sudo vim my_env.sh //配置自己的

#JAVA_HOME
export JAVA_HOME="/opt/module/jdk-11.0.13"
export PATH="$PATH:$JAVA_HOME/bin"

source /etc/profile

#HADOOP_HOME
export HADOOP_HOME="/opt/module/hadoop-3.3.1"
export PATH="$PATH:$HADOOP_HOME/bin"
export PATH="$PATH:$HADOOP_HOME/sbin"

source /etc/profile

### 运行案例

##### 本地模式：

mkdir wcinput //创建目录
vim word.txt
cd ..
(插入：ctrl+l清空///空格后tab提示输入)
bin/hadoop jar share/hadoop/mapreduce/hadoop-mapreduce-examples-3.3.1.jar wordcount /wcinput ./wcoutput		//解释：运行bin里面的hadoop命令，命令代码在jar包里，jar包路径，jar包里的具体案例 输入数据 输出数据(不能存在)
cat part-r-00000 //查看文件

##### 完全分布式：

- 安全拷贝：scp -r $pdir/$fname $user@host:$host:$pdir/$fname

解释：命令 递归 要拷贝的文件路径/名称 目的地用户@主机：目的地路径/名称

scp -r jdk-11.0.13/ lxy@192.168.2.103:/opt/module/ 在102

scp -r lxy@192.168.2.102:/opt/module/hadoop-3.3.1 ./ 在103

scp -r lxy@192.168.2.102:/opt/module/* lxy@192.168.2.104:/opt/module/ 在103

- 远程同步工具：rsync -av $pdir/$fname $user@host:$host:$pdir/$fname

解释：命令 选项参数 同上

-a 归档拷贝 -v显示复制过程

删除操作： rm -rf wcinput/ wcoutput/

rsync -av hadoop-3.3.1/ lxy@192.168.2.103:/opt/module/hadoop-3.3.1/

echo $PATH 查看全局环境变量

- 集群分发脚本：xsync 要同步的文件名称

在/home/atguigu/bin目录下创建xsync文件

cd /home/lxy
mkdir bin
cd bin
vim xsync

```shell
#!/bin/bash

#1. 判断参数个数
if [ $# -lt 1 ]
then
    echo Not Enough Arguement!
    exit;
fi

#2. 遍历集群所有机器
for host in 192.168.2.102 192.168.2.103
do
    echo ====================  $host  ====================
    #3. 遍历所有目录，挨个发送

    for file in $@
    do
        #4. 判断文件是否存在
        if [ -e $file ]
            then
                #5. 获取父目录
                pdir=$(cd -P $(dirname $file); pwd)

                #6. 获取当前文件的名称
                fname=$(basename $file)
                ssh $host "mkdir -p $pdir"
                rsync -av $pdir/$fname $host:$pdir
            else
                echo $file does not exists!
        fi
    done
done
```



dirname 获取父目录

basename 获取文件名（忽略路径）

chmod +x xsync //修改脚本 xsync 具有执行权限（chmod 777 xsync）

试验：xsync ./bin

sudo ./bin/xsync /etc/profile.d/my_env.sh (如果用了sudo，那么xsync一定要给它的路径补全。)

source /etc/profile

- SSH免密登录

ls -al 查看全部文件（包括隐藏文件）

生成公钥和私钥：pwd（/home/lxy/.ssh）ssh-keygen -t rsa
就会生成两个文件id_rsa（私钥）、id_rsa.pub（公钥）

拷贝公钥：ssh-copy-id 192.168.2.103

root用户要重新配置

- 集群配置

配置core-site.xml：cd $HADOOP_HOME/etc/hadoop     vim core-site.xml

```xml
<?xml version="1.0" encoding="UTF-8"?>
<?xml-stylesheet type="text/xsl" href="configuration.xsl"?>

<configuration>
    <!-- 指定NameNode的地址 -->
    <property>
        <name>fs.defaultFS</name>
        <value>hdfs://192.168.2.102:8020</value>
    </property>

    <!-- 指定hadoop数据的存储目录 -->
    <property>
        <name>hadoop.tmp.dir</name>
        <value>/opt/module/hadoop-3.3.1/data</value>
    </property>

    <!-- 配置HDFS网页登录使用的静态用户为atguigu -->
    <property>
        <name>hadoop.http.staticuser.user</name>
        <value>lxy</value>
    </property>
</configuration>
```

配置hdfs-site.xml:vim hdfs-site.xml

```xml
<?xml version="1.0" encoding="UTF-8"?>
<?xml-stylesheet type="text/xsl" href="configuration.xsl"?>

<configuration>
	<!-- nn web端访问地址-->
	<property>
        <name>dfs.namenode.http-address</name>
        <value>192.168.2.102:9870</value>
    </property>
	<!-- 2nn web端访问地址-->
    <property>
        <name>dfs.namenode.secondary.http-address</name>
        <value>192.168.2.104:9868</value>
    </property>
</configuration>

```

配置yarn-site.xml:vim yarn-site.xml

```xml
<?xml version="1.0" encoding="UTF-8"?>
<?xml-stylesheet type="text/xsl" href="configuration.xsl"?>

<configuration>
    <!-- 指定MR走shuffle -->
    <property>
        <name>yarn.nodemanager.aux-services</name>
        <value>mapreduce_shuffle</value>
    </property>

    <!-- 指定ResourceManager的地址-->
    <property>
        <name>yarn.resourcemanager.hostname</name>
        <value>192.168.2.103</value>
    </property>

    <!-- 环境变量的继承 -->
    <property>
        <name>yarn.nodemanager.env-whitelist</name>      <value>JAVA_HOME,HADOOP_COMMON_HOME,HADOOP_HDFS_HOME,HADOOP_CONF_DIR,CLASSPATH_PREPEND_DISTCACHE,HADOOP_YARN_HOME,HADOOP_MAPRED_HOME</value>
    </property>
</configuration>

```

配置mapred-site.xml:vim mapred-site.xml

```xml
<?xml version="1.0" encoding="UTF-8"?>
<?xml-stylesheet type="text/xsl" href="configuration.xsl"?>

<configuration>
	<!-- 指定MapReduce程序运行在Yarn上 -->
    <property>
        <name>mapreduce.framework.name</name>
        <value>yarn</value>
    </property>
</configuration>

```

分发：xsync /opt/module/hadoop-3.3.1/etc/hadoop/

配置workers：vim workers再分发

在namenode上格式化：hdfs namenode -format

启动HDFS：sbin/start-dfs.sh

（jps查看目前情况）

在resourcemanager启动yarn：sbin/start-yarn.sh

Web端查看HDFS的NameNode

（a）浏览器中输入：http://192.168.2.102:9870

  (b）查看HDFS上存储的数据信息

Web端查看YARN的ResourceManager

（a）浏览器中输入：http://192.168.2.103:8088

（b）查看YARN上运行的Job信息



在集群：

hadoop fs -mkdir /input 建立文件夹

hadoop fs -put $HADOOP_HOME/wcinput/word.txt /input 上传本地文件

hadoop jar share/hadoop/mapreduce/hadoop-mapreduce-examples-3.3.1.jar wordcount /input /output 执行wordcount

出现故障：

第一步：杀死进程（停掉dfs和yarn）

第二步：删除所有的data和logs

第三步：格式化和启动

配置历史服务器：vim mapred-site.xml

```xml
<!-- 历史服务器端地址 -->
<property>
    <name>mapreduce.jobhistory.address</name>
    <value>192.168.2.102:10020</value>
</property>

<!-- 历史服务器web端地址 -->
<property>
    <name>mapreduce.jobhistory.webapp.address</name>
    <value>192.168.2.102:19888</value>
</property>
```

再分发：xsync $HADOOP_HOME/etc/hadoop/mapred-site.xml

启动历史服务器：mapred --daemon start historyserver

查看JobHistory：http://192.168.2.102:19888/jobhistory

配置日志：vim yarn-site.xml

```xml
<!-- 开启日志聚集功能 -->
<property>
    <name>yarn.log-aggregation-enable</name>
    <value>true</value>
</property>
<!-- 设置日志聚集服务器地址 -->
<property>  
    <name>yarn.log.server.url</name>  
    <value>http://192.168.2.102:19888/jobhistory/logs</value>
</property>
<!-- 设置日志保留时间为7天 -->
<property>
    <name>yarn.log-aggregation.retain-seconds</name>
    <value>604800</value>
</property>
```

再分发：xsync $HADOOP_HOME/etc/hadoop/yarn-site.xml



配置脚本：

再~/bin中：

```shell
#!/bin/bash

if [ $# -lt 1 ]
then
    echo "No Args Input..."
    exit ;
fi

case $1 in
"start")
        echo " =================== 启动 hadoop集群 ==================="

        echo " --------------- 启动 hdfs ---------------"
        ssh 192.168.2.102 "/opt/module/hadoop-3.3.1/sbin/start-dfs.sh"
        echo " --------------- 启动 yarn ---------------"
        ssh 192.168.2.103 "/opt/module/hadoop-3.3.1/sbin/start-yarn.sh"
        echo " --------------- 启动 historyserver ---------------"
        ssh 192.168.2.102 "/opt/module/hadoop-3.3.1/bin/mapred --daemon start historyserver"
;;
"stop")
        echo " =================== 关闭 hadoop集群 ==================="

        echo " --------------- 关闭 historyserver ---------------"
        ssh 192.168.2.102 "/opt/module/hadoop-3.3.1/bin/mapred --daemon stop historyserver"
        echo " --------------- 关闭 yarn ---------------"
        ssh 192.168.2.103 "/opt/module/hadoop-3.3.1/sbin/stop-yarn.sh"
        echo " --------------- 关闭 hdfs ---------------"
        ssh 192.168.2.102 "/opt/module/hadoop-3.3.1/sbin/stop-dfs.sh"
;;
*)
    echo "Input Args Error..."
;;
esac
```







somethingnew：bin/hadoop jar share/hadoop/mapreduce/hadoop-mapreduce-examples-3.3.1.jar randomtextwriter -Dmapreduce.randomtextwriter.totalbytes=10737418240 ~/Alldata/data1

bin/hadoop jar share/hadoop/mapreduce/hadoop-mapreduce-examples-3.3.1.jar wordcount ~/Alldata/data0 ~/Alloutput/data00



sed -i -r -e 's/\x00/a/g' -e 's/\x01/b/g' -e 's/\x02/c/g' -e 's/\x03/d/g' -e 's/\x04/e/g' -e 's/\x05/f/g' -e 's/\x06/g/g' -e 's/\x07/h/g' -e 's/\x08/i/g' -e 's/\x0A/j/g' -e 's/\x0B/k/g' -e 's/\x0C/l/g' -e 's/\x0D/m/g' -e 's/\x0E/n/g' -e 's/\x0F/o/g' -e 's/\x10/p/g' -e 's/\x11/q/g' -e 's/\x12/r/g' -e 's/\x13/s/g' -e 's/\x14/t/g' -e 's/\x15/u/g' -e 's/\x16/v/g' -e 's/\x17/w/g' -e 's/\x18/x/g' -e 's/\x19/y/g' -e 's/\x1A/z/g' -e 's/\x1B/a/g' -e 's/\x1C/b/g' -e 's/\x1D/c/g' -e 's/\x1E/d/g' -e 's/\x1F/e/g' -e 's/\x7F/f/g' -e 's/\x09//g'





 hadoop fs -mkdir /input

hadoop fs -put ~/Alldata/b /input

hadoop fs -put wcinput /input

hadoop jar share/hadoop/mapreduce/hadoop-mapreduce-examples-3.3.1.jar wordcount /input /output

