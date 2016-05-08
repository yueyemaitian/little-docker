# little-docker
Study docker

#dockerfile_tomcat7
build an image with ubuntu14.04, jdk7, tomcat7
and reset mirror list to aliyun

#dockerfile
build an image with my little web 
build image: docker run --cap-add SYS_PTRACE -it --name little-web -p 8088:8080 little-web
you can access it by: http://aliyun-host-ip:8088/little-web/asyncServlet

