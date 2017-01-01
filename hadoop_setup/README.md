# Install packages
sudo apt-get install ssh
sudo apt-get install rsync
sudo apt-get install openjdk-8-jdk

# Download and unzip hadoop
wget http://www-us.apache.org/dist/hadoop/common/hadoop-2.7.3/hadoop-2.7.3-src.tar.gz
sudo tar zxvf hadoop-2.7.3.tar.gz -C /usr/local
sudo mv /usr/local/hadoop-* /usr/local/hadoop

# Setup localhost ssh
ssh-keygen -f ~/.ssh/id_rsa -t rsa -P ""
cat ~/.ssh/id_rsa.pub >> ~/.ssh/authorized_keys

# Add ppaths to local environment
vi ~/.profile
###
export JAVA_HOME=/usr
export PATH=$PATH:$JAVA_HOME/bin
export HADOOP_HOME=/usr/local/hadoop
export PATH=$PATH:$HADOOP_HOME/bin
export HADOOP_CONF_DIR=/usr/local/hadoop/etc/hadoop
###
source ~/.profile

# Update hadoop-env.sh
sudo vi $HADOOP_CONF_DIR/hadoop-env.sh
# export JAVA_HOME=/usr

# Update core-site.xml
sudo vi $HADOOP_CONF_DIR/core-site.xml
###
<configuration>
  <property>
    <name>fs.defaultFS</name>
    <value>hdfs://localhost:9000</value>
  </property>
</configuration>
###

# Update yarn-site.xml
sudo vi /usr/local/hadoop/etc/hadoop/yarn-site.xml 
###
<configuration>
<!-- Site specific YARN configuration properties -->
  <property>
    <name>yarn.nodemanager.aux-services</name>
    <value>mapreduce_shuffle</value>
  </property> 
  <property>
    <name>yarn.nodemanager.aux-services.mapreduce.shuffle.class</name>
    <value>org.apache.hadoop.mapred.ShuffleHandler</value>
  </property>
  <property>
    <name>yarn.resourcemanager.hostname</name>
    <value>localhost</value>
  </property>

</configuration>
###

# Change hadoop permissions
sudo chown -R ubuntu $HADOOP_HOME

# Run Hadoop
hdfs namenode -format
$HADOOP_HOME/sbin/start-dfs.sh
$HADOOP_HOME/sbin/start-yarn.sh
$HADOOP_HOME/sbin/mr-jobhistory-daemon.sh start historyserver

