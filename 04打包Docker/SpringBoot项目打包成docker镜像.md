
#前提：先打包好jar包，jar包与DockerFile在同一文件夹内
FROM java:8
#将Dockerfile同目录下的jar文件复制到容器内根目录内，取名为app.jar
COPY *.jar /app.jar
#记得要CMD和[]有他妈的空格！！！！！！！！！如果没有这个CMD，容器会秒退
CMD ["--server.port=8080"]
EXPOSE 8080
#启动容器后运行启动springboot脚本 记得要ENTRYPOINT和[]有他妈的空格！！！！！！！！！
ENTRYPOINT["java","-jar","/app.jar"]

#CMD可以为ENTRYPOINT指定参数，这个dockerfile的镜像被运行后其实执行了：
    java -jar /app.jar --server.port=8080 