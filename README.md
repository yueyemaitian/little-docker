# little-docker
Study docker

#dockerfile_tomcat7
build an image with ubuntu14.04, jdk7, tomcat7  
and reset mirror list to aliyun  
build image:  docker build -f ./dockerfile_tomcat7 -t ubuntu14.04_jdk7_tomcat7 .

#dockerfile_little-web
build image: docker build -f ./dockerfile_little-web -t little-web .   
run container, will start failed without --cap-add SYS_PTRACE: docker run --cap-add SYS_PTRACE -it --name little-web -p 8088:8080 little-web  
you can access it by: http://aliyun-host-ip:8088/little-web/asyncServlet
[docker端口映射参考](https://github.com/yueyemaitian/little-docker.git)  

#docker aliyun speed up
https://cr.console.aliyun.com/?spm=5176.1971733.2.28.f6VlND#/docker/booster

#有用的命令
#镜像和容器部分
1、列出无标签的镜像id
docker images -q --filter "dangling=true"
2、删除无标签的镜像
docker rmi $(docker images -q --filter "dangling=true")
3、强制停止并删除所有容器
docker rm -f $(docker ps -a -q)
#网络部分
4、添加网卡
brctl addbr br0
5、up网卡
ip link set br0 up
6、为br0设置ip地址，地址为当前机器eth0的地址，同时把eth0的地址删掉，然后把eth0连到网桥br0上；删除原来（到eth0的）默认路由规则，并添加默认路由规则到br0，via后边的ip，可以先通过ip route查看应该是什么；由于此命令会导致网络连接中断，如果是远程登录到机器上执行的，需要一步执行
ip addr add 172.16.30.131/24 dev br0; \
ip addr del 172.16.30.131/24 dev eth0; \
brctl addif br0 eth0; \
ip route del default; \
ip route add default via 172.16.30.2 dev br0
7、跑个容器，不用docker提供的网络（--net=none)，并获取容器id
docker run -itd --name my-ubuntu --net=none ubuntu:14.04 /bin/bash
cpid=$(docker inspect --format '{{.State.Pid}}' my-ubuntu)
8、把容器的net ns软连接到/var/run/netns下
ln -s /proc/$cpid/ns/net /var/run/netns/$cpid
9、添加网卡peer
ip link add veth-a type veth peer name veth-b
10、把虚拟网卡veth-a添加到br0，并启用
brctl addif br0 veth-a
ip link set veth-a up
11、把虚拟网卡veth-b添加到容器网络ns下
ip link set veth-b netns $cpid
12、修改容器中虚拟网卡veth-b的名称为eth0
ip netns exec $cpid ip link set veth-b name eth0
13、启用eth0
ip netns exec $cpid ip link set eth0 up
14、给容器分配ip地址，此地址跟宿主机在同一ip段
ip netns exec $cpid ip addr add 172.16.30.132/24 dev eth0
15、分配完地址后，设置默认路由（到底有啥用？）
ip netns exec $cpid ip route add default via 172.16.30.2
16、创建一个net ns，并启动lo网卡
ip netns add nsfirst
ip netns exec nsfirst ip link set dev lo up
17、创建虚拟网卡peer
ip link add veth-a type veth peer name veth-b
18、给veth-a设置ip，然后启动
ip addr add 10.0.0.139/24 dev veth-a
ip link set veth-a up
19、给veth-b分配ip
ip addr add 10.0.0.140/24 dev veth-b
20、把veth-b加入到一个network namespace
ip link set veth-b netns nsfirst
21、启动netns中的veth-b网卡
ip netns exec nsfirst ip link set veth-b up

