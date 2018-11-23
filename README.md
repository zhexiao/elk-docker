# elk-docker
# 下载包
创建镜像前，需要先下载对应的tar.gz包。
- elasticsearch 6.4：https://artifacts.elastic.co/downloads/elasticsearch/elasticsearch-6.4.0.tar.gz
- kibana 6.4.1：https://artifacts.elastic.co/downloads/kibana/kibana-6.4.1-linux-x86_64.tar.gz
- logstash 6.4.1：https://artifacts.elastic.co/downloads/logstash/logstash-6.4.1.tar.gz

# elasticsearch
## 创建目录
```
$ sudo mkdir -p /opt/elasticsearch/data
$ cd /opt/elasticsearch/data
$ sudo mkdir -p master1 master2 data1 data2

$ sudo tar -zxvf elasticsearch-6.4.0.tar.gz -C /opt/elasticsearch/

# 这里如果不改777，则挂载到container里面就会缺少访问权限
$ sudo chmod -R 777 /opt/elasticsearch
```

## 创建image
```
$ cd elk-docker
$ docker build -t elasticsearch -f elasticsearch/Dockerfile .
```

## 配置文件
```
$ cp elasticsearch_data.yml.example elasticsearch_data1.yml
$ cp elasticsearch_data.yml.example elasticsearch_data2.yml

$ cp elasticsearch_master.yml.example elasticsearch_master1.yml
$ cp elasticsearch_master.yml.example elasticsearch_master2.yml
```
按需修改elasticsearch/conf里面的配置，需要注意的有：
1. cluster.name
2. node.name
3. http.port
4. transport.tcp.port
5. discovery.zen.ping.unicast.hosts
6. discovery.zen.minimum_master_nodes

## QA
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

# kibana
## 创建目录
```
$ sudo mkdir -p /opt/kibana

# 这里如果不改777，则挂载到container里面就会缺少访问权限
$ sudo chmod -R 777 /opt/kibana
$ tar -zxvf kibana-6.4.1-linux-x86_64.tar.gz -C /opt/kibana/
```

## 创建image
```
$ cd elk-docker
$ docker build -t kibana -f kibana/Dockerfile .
```

## 配置文件
```
$ cp kibana.yml.example kibana.yml
```
按需修改kibana/conf里面的配置，需要注意的有：
1. server.host
2. elasticsearch.url


# logstash
## 创建目录
```
$ sudo mkdir -p /opt/logstash

# 这里如果不改777，则挂载到container里面就会缺少访问权限
$ sudo chmod -R 777 /opt/logstash
$ tar -zxvf logstash-6.4.1.tar.gz -C /opt/logstash/

# 存放一些例如es的模板
$ cd logstash-6.4.1
$ mkdir tpl
```

## 创建image
```
$ cd elk-docker
$ docker build -t logstash -f logstash/Dockerfile .
```

## 配置文件
假设我们选择的配置文件是kafka_to_es
```
$ cp kafka_to_es.conf.example kafka_to_es.conf
```
1. 修改kakfa服务bootstrap_servers等配置
2. 修改Elasticsearch服务hosts等配置
3. 修改 docker-compose.yml里面的参数 &logstash-volumes 和  &logstash-run，确认配置文件名

## 插件安装
```
$ sudo apt-get install -y openjdk-8-jdk apt-transport-https
$ cd /opt/logstash/logstash-6.4.1

# 安装kafka input插件
$ ./bin/logstash-plugin install logstash-input-kafka

# 安装es output插件
$ ./bin/logstash-plugin install logstash-output-elasticsearch
```

# 部署
```
$ docker stack deploy -c docker-compose.yml elk_v1

# 移除部署
$ docker stack rm elk_v1
```