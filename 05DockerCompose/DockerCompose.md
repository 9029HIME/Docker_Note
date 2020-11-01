# **DockerCompose介绍**

DockerCompose核心作用是容器编排，能够代替多个docker run来启动多个容器，还可以通过yml文件来配置各个启动前的关系，如先启动MySQL，Redis再启动Springboot项目。如果用以前的docker run需要将多个docker run命令统一写入一个shell文件里，会很麻烦。使用DockerCompose会方便很多

# DockerCompose下载

下载完后，放到/usr/local/bin下，分配该文件的x权限



# DockerCompose使用



# **DockerCompose使用**

docker-compose up -f {yml文件所在位置} . 

**如果不指定-f，默认找该路径下的docker-compose.yml**

使用docker-compose启动的多个服务（容器）会被视作一整个项目 ，默认容器名是compose-{服务名}-{副本数}整个项目共用一个网络，这个网络会被docker-compose创建出来，在同一个网络下容器互联，可以通过服务名代替ip地址访问。如{ip}:3306可以写为mysql:3306



# **DockerCompose三层说明**

> version: "3.3"		#第一层，DockerCompose版本
> services:				#第二层，这个DockerCompose所包含的服务（可以理解为容器启动后的东西）
>   web:						#服务名
>     build: .					#通过docker build方式构建镜像并启动，如果是.则代表build同目录下的Dockerfile
>     ports:						#端口
>   redis:						#服务名
>      image: "redis:alpine"	#通过docker pull方式拉取镜像启动

以官网的DockerCompose为例

具体key可以看docker官方文档，里面有许多key可以代替之前的docker命令。

