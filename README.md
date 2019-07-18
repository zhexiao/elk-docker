# elk-docker

# Elasticsearch
```
$ docker search elasticsearch
$ docker pull elasticsearch:6.4.3

# 测试
$ docker run -p 8200:9200 -p 8300:9300 -e "discovery.type=single-node" elasticsearch:6.4.3

$ curl localhost:8200
```

# Kibana
```
$ docker search kibana
$ docker pull kibana:6.4.3
```

# Logstash
```
$ docker search logstash
$ docker pull logstash:6.4.3
```

# 创建本地数据目录
```
$ sudo mkdir -p /var/lib/es
$ sudo chmod -R 777 /var/lib/es/
$ cd /var/lib/es/
$ mkdir -p master1 master2 data1 data2
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

# 启动
```
version: "3.4"

services:
  elasticsearch1:
    image: elasticsearch:6.4.3
    environment:
      - cluster.name=docker-cluster
      - bootstrap.memory_lock=true
      - node.name=node-master1
      - node.master=true
      - node.data=false
      - discovery.zen.minimum_master_nodes=2
      - "ES_JAVA_OPTS=-Xms1g -Xmx1g"
    volumes:
      - /var/lib/es/master1:/usr/share/elasticsearch/data
    networks:
      - esnet

  elasticsearch2:
    image: elasticsearch:6.4.3
    environment:
      - cluster.name=docker-cluster
      - bootstrap.memory_lock=true
      - node.name=node-master2
      - node.master=true
      - node.data=false
      - discovery.zen.minimum_master_nodes=2
      - "ES_JAVA_OPTS=-Xms1g -Xmx1g"
    volumes:
      - /var/lib/es/master2:/usr/share/elasticsearch/data
    networks:
      - esnet

  elasticsearch3:
    image: elasticsearch:6.4.3
    ports:
      - 9200:9200
    environment:
      - cluster.name=docker-cluster
      - bootstrap.memory_lock=true
      - node.name=node-data1
      - node.master=false
      - node.data=true
      - discovery.zen.minimum_master_nodes=2
      - discovery.zen.ping.unicast.hosts=elasticsearch1,elasticsearch2
      - "ES_JAVA_OPTS=-Xms1g -Xmx1g"
    volumes:
      - /var/lib/es/data1:/usr/share/elasticsearch/data
    networks:
      - esnet

  elasticsearch4:
    image: elasticsearch:6.4.3
    ports:
      - 9201:9200
    environment:
      - cluster.name=docker-cluster
      - bootstrap.memory_lock=true
      - node.name=node-data2
      - node.master=false
      - node.data=true
      - discovery.zen.minimum_master_nodes=2
      - discovery.zen.ping.unicast.hosts=elasticsearch1,elasticsearch2
      - "ES_JAVA_OPTS=-Xms1g -Xmx1g"
    volumes:
      - /var/lib/es/data2:/usr/share/elasticsearch/data
    networks:
      - esnet

  kibana:
    image: kibana:6.4.3
    ports:
      - 5601:5601
    environment:
      ELASTICSEARCH_URL: http://elasticsearch3:9200
    depends_on:
      - elasticsearch1
      - elasticsearch2
      - elasticsearch3
      - elasticsearch4
    networks:
      - esnet

networks:
  esnet:
```