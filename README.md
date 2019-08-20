# elk-docker
docker化管理ELK集群。

# 拉取官方镜像
### Elasticsearch
```
$ docker search elasticsearch
$ docker pull elasticsearch:6.4.3

# 测试
$ docker run -p 8200:9200 -p 8300:9300 -e "discovery.type=single-node" elasticsearch:6.4.3

$ curl localhost:8200
```

### Kibana
```
$ docker search kibana
$ docker pull kibana:6.4.3
```

# 系统配置
```
$ sudo vi /etc/sysctl.conf
'''
vm.max_map_count=262144
'''

$ sudo vi /etc/systemd/system.conf
'''
DefaultLimitMEMLOCK=infinity
'''

$ grep vm.max_map_count /etc/sysctl.conf
```

# Es+Kibana启动
### 单机启动
```
$ sudo mkdir -p /es/single_data
$ sudo chmod -R 777 /es/single_data

$ docker-compose -f docker-compose.single.yml up -d
```

### 集群启动
```
$ sudo mkdir -p /es/master1 /es/master2 /es/data1 /es/data2
$ sudo chmod -R 777 /es

$ docker-compose -f docker-compose.cluster.yml up -d
```

# Logstash启动
注意：启动logstash需要配合上面ES的单机启动（集群启动需要修改logstash.yml文件），强烈建议启动前查看一下default.conf。

```
$ cp logstash/default.conf.example logstash/default.conf
$ cp logstash/logstash.yml.example logstash/logstash.yml

$ docker build -t mylogstash -f Dockerfile-logstash .
$ docker-compose -f docker-compose.logstash.yml up -d
```

### 测试
```
$ python3 logstash/udp_client_test.py
```
打印：
```
mylogstash_1  | {
mylogstash_1  |           "tags" => [
mylogstash_1  |         [0] "src_ip_stats"
mylogstash_1  |     ],
mylogstash_1  |           "host" => "172.23.0.1",
mylogstash_1  |     "@timestamp" => 2019-08-20T09:36:32.284Z,
mylogstash_1  |           "name" => "zhexxx",
mylogstash_1  |            "msg" => "Hello World! 123444",
mylogstash_1  |       "@version" => "1"
mylogstash_1  | }
```