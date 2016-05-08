# little-docker
Study docker

#dockerfile_tomcat7
build an image with ubuntu14.04, jdk7, tomcat7  
and reset mirror list to aliyun  

#dockerfile_little-web
build image: docker build -f ./dockerfile_little-web -t little-web .   
run container: docker run --cap-add SYS_PTRACE -it --name little-web -p 8088:8080 little-web  
you can access it by: http://aliyun-host-ip:8088/little-web/asyncServlet
[docker端口映射参考](https://github.com/yueyemaitian/little-docker.git)  

