#
# docker build -t kibana -f kibana/Dockerfile .
#
#

FROM ubuntu:xenial
EXPOSE 5601

ADD ./common/sources.list /etc/apt/sources.list
RUN apt-get update -y && apt-get upgrade -y

# JAVA环境
RUN apt-get install -y openjdk-8-jdk apt-transport-https

# 创建用户
RUN groupadd -r kibana && useradd -r -l -d /home/kibana -g kibana -m kibana

# kibana环境
WORKDIR /home/kibana
ADD ./common/kibana-6.4.1-linux-x86_64.tar.gz .
ADD ./kibana/kibana.yml /home/kibana/kibana-6.4.1-linux-x86_64/config/kibana.yml
RUN chown -R kibana:kibana kibana-6.4.1-linux-x86_64
USER kibana



