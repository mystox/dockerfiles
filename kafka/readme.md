1. docker swarm集群需要新建标签
```
docker node ls

# docker node update --label-add role=标签名称  宿主机A节点名称

docker node update --label-add kafka=kafka_group1  prod-master
docker node update --label-add kafka=kafka_group2   prod-slaver1
docker node update --label-add kafka=kafka_group3   prod-slaver2

```
2. 每台主机新建持久化目录 /home/middleware/kafka/data
```
mkdir -p /home/middleware/kafka/data
``` 
3. 部署zookeeper集群
```
docker stack deploy kafka --compose-file kafka-compose-cluster.yml
```