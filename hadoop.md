# Use following steps to setup hadoop cluster:

## Step1: Create "hadoop" user on all nodes
useradd hadoop 
passwd hadoop

NOTE: 
1. For ease, please use same password throughout
2. Make sure to put hadoop user in sudoers list. You would need root access only for this step. After this hadoop user would have sudoer's  permission.

## Step2: Extract the tar and set env variables
su hadoop
cd /home/hadoop
wget <path to location.. Currently getting uploaded>
tar -xzvf hadoop.tgz
cd /home/hadoop/hadoop
cat zephyx.sh >> /home/hadoop/.bashrc
source /home/hadoop/.bashrc

## Step3: Specify seperate hostnames on all nodes
sudo vi /etc/hosts
Enter the following lines in the /etc/hosts file.

192.168.1.xxx hadoop-master 
192.168.1.xxx hadoop-slave-1 
192.168.1.xxx hadoop-slave-2
...
192.168.1.xxx hadoop-slave-n

NOTE: The IP address put above are samples. Please use the IP address for given system


## Step3: On all nodes: Passwordless ssh configuration ###
su hadoop 
ssh-keygen -t rsa 
ssh-copy-id -i ~/.ssh/id_rsa.pub hadoop@hadoop-master 
ssh-copy-id -i ~/.ssh/id_rsa.pub hadoop@hadoop-slave-1 
ssh-copy-id -i ~/.ssh/id_rsa.pub hadoop@hadoop-slave-2
... 
ssh-copy-id -i ~/.ssh/id_rsa.pub hadoop@hadoop-slave-n
chmod 0600 ~/.ssh/authorized_keys


## Step4: Modify/Verify $HADOOP_HOME/etc/hadoop/core-site.xml fs default value on all nodes ###
core-site.xml:

<configuration>
   <property> 
      <name>fs.default.name</name> 
      <value>hdfs://hadoop-master:9000/</value> 
   </property> 
   <property> 
      <name>dfs.permissions</name> 
      <value>false</value> 
   </property> 
</configuration>

## Step5: Modify/Verify $HADOOP_HOME/etc/hadoop/hdfs-site.xml Namenode and Datanode locations on local filesystems on all nodes ###
hdfs-site.xml:

<configuration>
   <property> 
      <name>dfs.data.dir</name> 
      <value>/home/hadoop/dfs/data/datanode</value> 
      <final>true</final> 
   </property> 

   <property> 
      <name>dfs.name.dir</name> 
      <value>/home/hadoop/dfs/data/namenode</value> 
      <value>/opt/hadoop/hadoop/dfs/name</value> 
      <final>true</final> 
   </property> 

   <property> 
      <name>dfs.replication</name> 
      <value>1</value> 
   </property> 
</configuration>


## Step6: Format namenode
$HADOOP_HOME/bin/hadoop namenode -format

## Step7: Format Datanode ###
$HADOOP_HOME/bin/hadoop datanode -format

## Step8: Start hadoop
$HADOOP_HOME/bin/start-all.sh 

## Step9: Refresh the node mostly on master
$HADOOP_HOME/bin/hadoop dfsadmin -refreshNodes

## Step10: Verify the HDFS for Namenode and Datanode(s)
$HADOOP_HOME/bin/hadoop dfsadmin -report
