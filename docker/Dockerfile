FROM ringo/scientific:7.1
MAINTAINER microhello@gmail.com


#USER root

# install dev tools
RUN yum clean all; \
    rpm --rebuilddb; \
    yum install -y curl which tar sudo openssh-server openssh-clients rsync net-tools unzip python
# update libselinux. see https://github.com/sequenceiq/hadoop-docker/issues/14
RUN yum update -y libselinux; \
    yum clean all

# JAVA

RUN curl -LO 'http://download.oracle.com/otn-pub/java/jdk/8u65-b17/jdk-8u65-linux-x64.rpm' -H 'Cookie: oraclelicense=accept-securebackup-cookie'
RUN rpm -i jdk-8u65-linux-x64.rpm
RUN rm -f jdk-8u65-linux-x64.rpm
ENV JAVA_HOME /usr/java/default
ENV PATH $PATH:$JAVA_HOME/bin

# Qconf Agent
RUN yum localinstall -y "https://github.com/Qihoo360/QConf/releases/download/1.1.0/QConf-1.1.0-3.el6.x86_64.rpm"; \
    yum clean all
ENV QCONF_HOME /usr/local/qconf
#todo: qconf config file
#COPY config_service/conf/ $QCONF_HOME/conf/
RUN /bin/sh $QCONF_HOME/bin/agent-cmd.sh start

# SPARK
ENV SPARK_VERSION 1.5.2
ENV HADOOP_VERSION 2.6
ENV SPARK_PACKAGE $SPARK_VERSION-bin-neuroten-spark
ENV SPARK_PACKAGE_FILENAME  spark-$SPARK_PACKAGE.tgz
ENV SPARK_HOME /opt/spark-$SPARK_PACKAGE
ENV PATH $PATH:$SPARK_HOME/bin
RUN curl -sL --retry 3 \
  "https://www.dropbox.com/s/f5do99eq5au5jmz/$SPARK_PACKAGE_FILENAME?dl=1" \
  | gunzip \
  | tar xz -C /opt/ \
  && ln -s $SPARK_HOME /opt/spark
RUN rm -f  SPARK_PACKAGE_FILENAME

ARG zookeeper_url=172.16.2.114:2181,172.16.2.115:2181
ENV SPARK_DAEMON_JAVA_OPTS "-Dspark.deploy.recoveryMode=ZOOKEEPER -Dspark.deploy.zookeeper.url=$zookeeper_url"
#todo: spark config file
#COPY spark/conf/ $SPARK_HOME/conf/

ARG node_role=master
ENTRYPOINT ["$SPARK_HOME/sbin/start-$node_role.sh"]

