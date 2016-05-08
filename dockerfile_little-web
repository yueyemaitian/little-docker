FROM ubuntu14.04_jdk7_tomcat7:latest
RUN apt-get -y install wget
RUN wget http://059260.oss-cn-hangzhou.aliyuncs.com/little-web-1.0.0-SNAPSHOT.war -O /var/lib/tomcat7/webapps/little-web.war 
CMD service tomcat7 start && tail -200f /var/lib/tomcat7/logs/catalina.out
