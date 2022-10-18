1. docker swarm集群需要新建标签
```
docker node ls

# docker node update --label-add role=标签名称  宿主机A节点名称

docker node update --label-add zookeeper=zoo1   prod-master
docker node update --label-add zookeeper=zoo2   prod-slaver2
docker node update --label-add zookeeper=zoo3   prod-slaver3

```

2. 部署zookeeper集群
```
docker stack deploy zookeeper --compose-file zookeeper-compose.yml
```