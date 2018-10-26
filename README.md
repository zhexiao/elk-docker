# elk-docker
## 1. 下载包
创建镜像前，需要先下载对应的tar.gz包放到common目录下面。
- elasticsearch 6.4：https://artifacts.elastic.co/downloads/elasticsearch/elasticsearch-6.4.0.tar.gz
- kibana 6.4.1：https://artifacts.elastic.co/downloads/kibana/kibana-6.4.1-linux-x86_64.tar.gz
- logstash 6.4.1：https://artifacts.elastic.co/downloads/logstash/logstash-6.4.1.tar.gz

**如果你下载的版本与上面的不一致，需要对应修改Dockerfile和.env文件。**


## 2. 创建Elasticsearch和Kibana镜像
```
$ docker build -t elasticsearch -f elasticsearch/Dockerfile .
$ docker build -t kibana -f kibana/Dockerfile .
$ docker build -t logstash -f logstash/Dockerfile .
```

## 3. 修改.env文件
```
# 根据自身需求修改配置文件，比如IP地址，启动路径等
CLUSTER_NAME=es-cluster
ELASTICSEARCH_URL=http://192.168.71.188:9201
DISCOVERY_PING_UNICAST_HOSTS=192.168.71.188:9301,192.168.71.188:9302
```

## 4. 创建数据保存目录
因为在docker里面跑es，数据不能存在container里面，所以我们需要把host机器的路径映射到container里面。
```
# 创建本地目录
$ sudo mkdir -p /opt/elk/
$ cd /opt/elk
$ sudo mkdir -p elasticsearch/master1 elasticsearch/master2 elasticsearch/data1 elasticsearch/data2
$ sudo mkdir -p logstash/data logstash/config

$ sudo chmod -R 777 /opt/elk
```

### logstash配置
1. 需要导入的数据放在文件夹logstash/data下面
2. 把logstash目录下面的json和conf配置文件放在logstash/config下面

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
