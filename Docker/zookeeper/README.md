ZooKeeprt
===
## 简介
* **Apache ZooKeeper** 是Apache软件基金会的一个软件项目，他为大型分布式计算提供开源的分布式配置服务、同步服务和命名注册。ZooKeeper曾经是Hadoop的一个子项目，但现在是一个独立的顶级项目。
> * 管理员文档：https://zookeeper.apache.org/doc/trunk/zookeeperAdmin.html

## Example:

    #运行一个单机版ZK
    docker run -d --restart always -p 2181:2181 -v /docker/zookeeper:/var/lib/zookeeper --hostname zookeeper --name zookeeper zookeeper

    #运行一个ZK集群
    docker run -d --restart always --privileged --network=mynetwork --ip=10.0.0.71 -v /docker/zookeeper1:/var/lib/zookeeper -e ZK_SERVER=10.0.0.71,10.0.0.72,10.0.0.73 -e VIP=10.0.0.70 --hostname zookeeper1 --name zookeeper1 zookeeper
    docker run -d --restart always --privileged --network=mynetwork --ip=10.0.0.72 -v /docker/zookeeper2:/var/lib/zookeeper -e ZK_SERVER=10.0.0.71,10.0.0.72,10.0.0.73 -e VIP=10.0.0.70 --hostname zookeeper2 --name zookeeper2 zookeeper
    docker run -d --restart always --privileged --network=mynetwork --ip=10.0.0.73 -v /docker/zookeeper3:/var/lib/zookeeper -e ZK_SERVER=10.0.0.71,10.0.0.72,10.0.0.73 -e VIP=10.0.0.70 --hostname zookeeper3 --name zookeeper3 zookeeper

## Run Defult Parameter
**协定：** []是默参数，<>是自定义参数

				docker run -d --restart always [--privileged] \\
				-v /docker/zookeeper:/var/lib/zookeeper \\
				-p 2181:2181 \\
				-e ZK_MEM=[1G] \\                                     默认内存大小1G
				-e ZK_SERVER=<"10.0.0.71,10.0.0.72,10.0.0.73"> \\     集群节点数建议大于或等于3
				-e VIP=<10.0.0.70> \\                                 leader IP Addr，需要 --privileged
				-e IPTABLES=<"192.168.10.0/24,10.0.0.0/24"> \\        防火墙(2181)，需要 --privileged
				--hostname zookeeper \\
				--name zookeeper zookeeper

## 补充
查看集群状态：

    /usr/local/zookeeper/bin/zkServer.sh status

Zookeeper中角色：

    领导者(Leader)：领导者负责进行投票的发起和决议，更新系统状态，处理写请求
    跟随者(Follwer)：Follower用于接收客户端的读写请求并向客户端返回结果，在选主过程中参与投票
    观察者（Observer）：观察者可以接收客户端的读写请求，并将写请求转发给Leader，但Observer节点不参与投票过程，只同步leader状态，Observer的目的是为了，扩展系统，提高读取速度。
    客户端(Client)： 执行读写请求的发起方
