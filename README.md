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
```
$ docker build -t mylogstash -f Dockerfile-logstash .
$ docker-compose -f docker-compose.logstash.yml up -d
```