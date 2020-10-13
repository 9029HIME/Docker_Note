**DokcerFile是什么？**
    用来构建Docker Image的构建文件，可以通过DockerFile生成Docker Image，类似于Commit的效果，DockerFile
    本质是命令脚本。每一行命令都会在原有的基础上提交一个新的镜像层，即“每一行命令都是一层”
**DockerFile基本指令**
    #DockerFile的指令都是大写的
    FROM        #基础镜像，99%的镜像都基于scratch，相当于Object类
    MAINTANINER #作者
    RUN         #镜像构建的时候需要运行的命令
    ADD         #新增内容，比如在centos里增加tomcat
    WORKDIR     #工作目录，即镜像启动后默认进入的目录
    VOLUME      #设置挂载卷
    EXPOSE      #映射主机的端口
    CMD         #容器启动后会运行的命令，一个CMD只能有一个命令，如果有多个则运行最后一个
    ENTRYPOINT  #容器启动后会运行的命令，与CMD不同，可以在run的时候追加命令
        如定义了 CMD["ls","-a"]，容器运行后只会执行ls -a
        如定义了 CMD["ls","-a"]，可以通过docker run ................ -l的方式运行ls -al
    ONBUILD     #如果本镜像被其他镜像继承了，启动时就会运行ONBUILD的指令
    COPY        #类似ADD命令
    ENV         #构建的时候设置系统环境变量
**构建一个自己的centos，新增vim,ifconfig命令**
    FROM centos
    MAINTAINER koujyungenn<koujyungenn@gmail.com>
    ENV WORKPATH /usr/local         #配置一个叫WORKPATH的环境变量，值为路径/usr/local
    WORKDIR $WORKPATH               #配置工作目录为上面配置好的WORKPATH
    RUN yum -y install vim          #启动的时候运行 yum -y install vim 命令 
    RUN yum -y install net-tools    #启动的时候运行 yum -y install net-tools 命令
    EXPOSE 80                       #启动后默认映射到主机80端口
    CMD echo "-------end1-------"    #启动后运行该命令
    CMD echo "-------end2-------"
    ========================================================
    写完后，用docker build 命令构建dockerfile
    docker build -f [dockerfile文件路径] -t [镜像名称:版本] .     #注意最后有一个.
**查看镜像构建历史**
    可以使用docker history [镜像id] 查看这个镜像的构建过程