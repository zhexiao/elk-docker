# elk-docker
## 1. 下载包
创建镜像前，需要先下载对应的tar.gz包放到common目录下面。
- elasticsearch 6.4：https://artifacts.elastic.co/downloads/elasticsearch/elasticsearch-6.4.0.tar.gz
- kibana 6.4.1：https://artifacts.elastic.co/downloads/kibana/kibana-6.4.1-linux-x86_64.tar.gz

**如果你下载的版本与上面的不一致，需要对应修改Dockerfile和.env文件。**


## 2. 创建Elasticsearch和Kibana镜像
```
$ docker build -t elasticsearch -f elasticsearch/Dockerfile .
$ docker build -t kibana -f kibana/Dockerfile .
```

## 3. 修改.env文件
```
# 根据自身需求修改配置文件，比如IP地址，启动路径等
CLUSTER_NAME=es-cluster
ELASTICSEARCH_URL=http://192.168.71.188:9201
DISCOVERY_MINIMUM_MASTER_NODES=2
DISCOVERY_PING_UNICAST_HOSTS=192.168.71.188:9301,192.168.71.188:9302
ELASTICSEARCH_DATA_PATH=/home/elasticsearch/data
ELASTICSEARCH_START_CMD=/home/elasticsearch/elasticsearch-6.4.0/bin/elasticsearch
```

## 4. 创建数据保存目录
因为在docker里面跑es，数据不能存在container里面，所以我们需要把host机器的路径映射到container里面。
```
# 创建本地目录
$ mkdir -p /es/master1
$ mkdir -p /es/master2
$ mkdir -p /es/data1
$ mkdir -p /es/data2
$ sudo chmod -R 777 /es
```

elasticsearch.yml
```
path.data: /home/elasticsearch/data
```

.env
```
MASTER1_DATA_PATH=/es/master1
MASTER2_DATA_PATH=/es/master2
DATA1_DATA_PATH=/es/data1
DATA2_DATA_PATH=/es/data2
```

docker-compose.yml
```
...
volumes:
  - ${MASTER1_DATA_PATH}:${ELASTICSEARCH_DATA_PATH}
...
```

## 5. 启动
```
$ docker-compose up
```
如果启动出现错误：
max virtual memory areas vm.max_map_count [65530] is too low, increase to at least [262144]

解决方法：在host主机执行下面的操作
```
$ sudo vi /etc/sysctl.conf
"""
vm.max_map_count=524288
"""

$ sudo sysctl -p
```
