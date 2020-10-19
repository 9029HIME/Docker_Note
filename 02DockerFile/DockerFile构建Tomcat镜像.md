**自己构建一个Tomcat镜像**
    dockerfile的命名最好是"Dockerfile",这样在docker build的时候就不用-f指定文件
**基本指令**
    FROM CENTOS
    MAINTAINER koujyungenn<koujyungenn@163.com>
    COPY readme.txt /root   #生成镜像的时候拷贝readme.txt到容器的/root目录下
    ADD /root/l1nux/jdk-8u261-linux-x64.tar.gz  /usr/local
    ADD /root/l1nux/apache-tomcat-9.0.39.tar.gz    /usr/local #生成镜像的时候将这些压缩包解压到/usr/local目录下，ADD命令会自动解压
    RUN yum -y install vim
    #配置环境变量
    ENV MYPATH /usr/local
    WORKDIR $MYPATH
    ENV JAVA_HOME /usr/local/jdk1.8.0_261   #为镜像配置JAVA_HOME环境变量
    ENV CLASSPATH $JAVA_HOME/lib/dt.jar:$JAVA_HOME/lib/tools.jar
    ENV CATALINA_HOME /usr/local/apache-tomcat-9.0.39
    ENV CATALINA_BASH /usr/local/apache-tomcat-9.0.39
    ENV PATH $PATH:$JAVA_HOME/bin:$CATALINA_HOME/lib:$CATALINA_HOME/bin
    EXPOSE 8080
    #希望容器启动后，tomcat就会自动启动。但是有一个问题，需要手动启动
    CMD /usr/local/apache-tomcat-8.5.59/bin/startup.sh && ls