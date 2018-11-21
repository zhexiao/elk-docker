#
# docker build -t logstash -f logstash/Dockerfile .
#

FROM ubuntu:xenial

ADD ./common/sources.list /etc/apt/sources.list
RUN apt-get -y update && apt-get -y update --fix-missing && apt-get -y update

# JAVA环境
RUN apt-get install -y openjdk-8-jdk apt-transport-https

# 创建用户和用户组
RUN groupadd -r logstash && useradd -r -l -d /home/logstash -g logstash -m logstash
