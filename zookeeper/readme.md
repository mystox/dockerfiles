1. docker swarm集群需要新建标签
```
docker node ls

# docker node update --label-add role=标签名称  宿主机A节点名称

docker node update --label-add zookeeper=zoo1   prod-master
docker node update --label-add zookeeper=zoo2   prod-slaver1
docker node update --label-add zookeeper=zoo3   prod-slaver2

```

2. 部署zookeeper集群

```
mkdir -p /home/middleware/zookeeper/data
mkdir -p /home/middleware/zookeeper/datalog
docker stack deploy zookeeper -c zookeeper-compose-cluster.yml
```