#
# docker build -t elasticsearch -f elasticsearch/Dockerfile .
#
# Q&A:
#  1. max virtual memory areas vm.max_map_count [65530] is too low, increase to at least [262144]
#  在host主机（非container）执行： sudo sysctl -w vm.max_map_count=262144
#

FROM ubuntu:xenial
EXPOSE 9200
EXPOSE 9300

ADD ./common/sources.list /etc/apt/sources.list
RUN apt-get update -y && apt-get upgrade -y

# JAVA环境
RUN apt-get install -y openjdk-8-jdk apt-transport-https

# 创建用户
RUN groupadd -r elasticsearch && useradd -r -l -d /home/elasticsearch -g elasticsearch -m elasticsearch

# elasticsearch环境
WORKDIR /home/elasticsearch
ADD ./common/elasticsearch-6.4.0.tar.gz .
ADD ./elasticsearch/elasticsearch.yml /home/elasticsearch/elasticsearch-6.4.0/config/elasticsearch.yml
RUN chown -R elasticsearch:elasticsearch elasticsearch-6.4.0
USER elasticsearch
RUN mkdir data

