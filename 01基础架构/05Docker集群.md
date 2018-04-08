05Docker集群
===

Docker的集群目前主流的方案：

* [Swarm](https://docs.docker.com/swarm/)

* [Kubernetes](https://kubernetes.io/)

## Docker Swarm 集群

是用Swarm集群来部署一个无状态的服务：

目前有三台物理机node01、node02、node03，在node01上初始化Swarm：

```shell
docker swarm init --advertise-addr 192.168.0.10 # 你的IP地址
```
这个时候会创建一个swarm的manage节点，并输出一段join的命令样例。在其他的docker机器上运行上面输出的`docker swarm join`命令就可以加入集群了。

```shell
docker swarm join \
--token SWMTKN-1-2apg79ozshm0x9hgqgm7v3qo4ks6qcgqzqir5z03g6y90qolf8-***************** \
192.168.0.10:2377
```

若是忘记了init输出的密码和令牌，可以通过命令`docker swarm join-token worker`查看。

## 创建服务

在manager node上执行命令：

```shell
docker service create --name web_server --publish 8080:80 --replicas=2 192.168.0.10:60000/test/api:1.0
```
命令是在集群中创建一个叫做web_server的服务，并暴露8080端口出来

通过`docker service ls`可以查看目前集群中所有的服务

通过`ocker service ps [服务名]`可以查看指定的服务的所有容器[副本] 运行情况

## 服务的网络

默认情况下，如下创建的服务：

```shell
docker service create --name web_server --replicas=2 192.168.0.10:60000/test/api:1.0
```
此种创建的服务，只能在容器内访问，并不能在外部访问

若是新创建服务，加上` --publish 8080:80`则会映射并暴露8080到外部。

若是已经创建的服务，则执行：

```shell
docker service update --publish-add 8080:80 web_server
```

## 弹性伸缩service

若是我们要做负载均衡，就需要很多的节点，那么在swarm-manager执行：

```shell
docker service scale web_server=5
```

这样就可以将service中的副本数量增加且恒定到5个的数量

默认配置下 manager node 也是 worker node，所以 swarm-manager 上也运行了副本。如果不希望在 manager 上运行 service，可以执行如下命令：

```shell
docker node update --availability drain swarm-manager
```

## 不希望在 manager 上运行 service

默认配置下 manager node 也是 worker node，所以 swarm-manager 上也运行了副本。如果不希望在 manager 上运行 service，可以执行如下命令：

```shell
docker node update --availability drain swarm-manager
```