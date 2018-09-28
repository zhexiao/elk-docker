#
# docker build -t logstash -f logstash/Dockerfile .
#
#

FROM ubuntu:xenial

ADD ./common/sources.list /etc/apt/sources.list
RUN apt-get update && apt-get upgrade

# JAVA环境
RUN apt-get install -y openjdk-8-jdk apt-transport-https

# 创建用户和用户组
RUN groupadd -r logstash && useradd -r -l -d /home/logstash -g logstash -m logstash

# 环境
WORKDIR /home/logstash
ADD ./common/logstash-6.4.1.tar.gz .
ADD ./logstash/test_log.conf /home/logstash/logstash-6.4.1/config/
RUN chown -R logstash:logstash logstash-6.4.1
USER logstash



