# 镜像

## 1-镜像的命名

镜像的命名默认由${镜像名}:${版本}构成，如果不指定版本，则默认使用最新版

## 2-镜像的获取方式

![image](https://user-images.githubusercontent.com/48977889/167231300-11efddf5-686d-4463-9a39-90153971af20.png)

1. 通过编写Dockerfile构建镜像。
2. 通过docker pull方式，从镜像服务器或**私有镜像仓库**获取镜像。
3. 通过docker load方式从压缩包解压镜像。

## 3-镜像的压缩与导入

以nginx镜像为例：

![image](https://user-images.githubusercontent.com/48977889/167232070-25dc235f-d345-43e1-9881-7560502769f1.png)

# 容器

## 4-容器相关命令

![image](https://user-images.githubusercontent.com/48977889/167232660-534a67f7-dbe4-42bc-aed7-707192d658ab.png)

注意区分暂停和停止，暂停可以理解为挂起，保存容器的上下文，剥夺容器的CPU时间片直到被恢复为运行状态。

##  5-基于nginx镜像，启动一个nginx容器

![image](https://user-images.githubusercontent.com/48977889/167233334-9cf00727-1b7a-4f25-8085-2d493aaebad5.png)

docker run --name firstNginx -p 81:80 -d nginx

--name：为容器标识一个唯一的名称

-p：端口映射，左边是宿主机端口，右边是容器内的端口，即通过访问宿主机的81，从而访问到容器内的80

-d：后台运行

nginx：镜像名

当容器被运行后会生成一个唯一id，这个id和容器名一样是全局唯一的。

此时访问本机的81，就能将请求映射到容器内的80，即nginx服务：

![image](https://user-images.githubusercontent.com/48977889/167233397-82d0d4a4-4e98-499f-8927-387046b404ff.png)

也可以通过docker logs -f ${容器id}，来持续追踪容器内的日志，在这里是nginx的访问日志

![image](https://user-images.githubusercontent.com/48977889/167233424-9c747f57-7ab3-4c66-9530-c2033df98000.png)

## 6-进入容器内操作

知识点5可以看到，启动了容器后能成功使用，但是对于nginx这类应用我们需要手动修改配置文件，不能只停留在表面的使用。因此需要进入到容器内，手动操作容器里的内容。这里以修改nginx配置为例子：

![image-20220507103634076](markdown-img/01 镜像.assets/image-20220507103634076.png)

修改index.html内容，将标题改为Hello~World!，保存后再次访问本机81，会发现内容发生变化：

![image](https://user-images.githubusercontent.com/48977889/167235200-5d8e0959-9351-4b35-b0d4-085620073fb8.png)

docker exec -it firstNginx bash

-it：使用标准输入输出方式。

firstNginx：容器名，**这类用容器id也可以**。

bash：进入容器后使用bash交互。

## 7-容器的关闭、开启、删除

容器的关闭：

docker stop 容器名或容器id

容器的开启：

docker start 容器名或容器id

容器删除：

docker rm -f 容器名或容器id

需要注意，当容器关闭后，docker ps默认是查不出容器信息的，需要指定参数-a，即docker ps -a

容器在运行阶段是不允许删除的，需要将容器先关闭后删除，除非加上-f表示强制删除。

# 数据卷

![image](https://user-images.githubusercontent.com/48977889/167239139-8ee47c11-cdae-4702-ae00-8483564b6766.png)

## 8-数据卷的基本操作命令

docker volume create 数据卷名：新建一个数据卷。

docker volume inspect 数据卷名1 数据卷名2：查看一个或多个数据卷的信息，可以看到数据卷所在的宿主机路径。

docker volume ls：查看所有数据卷的信息。

docker volume prune：删除所有**没被挂载**的数据卷。

docker volume rm 数据卷名或ID：删除对应数据卷。

## 9-数据卷的挂载

知识点8只要针对数据卷的操作，但是数据卷被新建后还需要挂载到容器才能发挥它的作用：在创建容器的时候，通过-v参数挂载一个数据卷到容器的目录内。以修改nginx容器内html信息为例：

![image](https://user-images.githubusercontent.com/48977889/167239666-7ce1255c-4545-49f3-bf82-5ec5d852f22a.png)

新建了一个nginxHtml数据卷，通过docker inspect nginxHtml找到它在宿主机的挂载点，进去后发现是没有任何数据的，因为这个数据卷还没被容器挂载。然后新建一个nginx容器，通过-v nginxHtml:/usr/share/nginx/html这个参数将容器内usr/share/nginx/html这个路径挂载到nginxHtml这个数据卷内。这样就能在宿主机直接操作容器内的文件了。

**当然，如果没有新建过nginxHtml数据卷，在新建容器时挂载指定nginxHtml的话，docker会帮我们自动创建。**除了这种方式外，还有一种叫“匿名挂载”的方式，当然这个方式很少会用到，具体说明在第一次学Docker的笔记里说过了。

## 10-目录挂载

知识点9讲的是针对数据卷的容器挂载，可是数据卷是docker帮我们生成的一个目录，很多时候我们希望docker容器直接挂载到指定的目录下，这时就要用到目录挂载了。其实目录挂载和数据卷挂载的方式一样，只不过数据卷名换成宿主机的真实路径罢了：

![image](https://user-images.githubusercontent.com/48977889/167240083-a5d5149d-8514-40c6-9f24-ad4b459b42b8.png)

可以看到，通过目录挂载的方式创建容器，是不会有数据卷新增的。但是有个很奇怪的现象，当容器启动后，宿主机的挂载目录却没有任何内容，进入到容器内一看，/usr/share/nginx/html的内容也没了。

这是因为采用目录挂载的方式创建容器时，容器会将宿主机的挂载目录复制过去，遵循宿主机的内容。而采用数据卷挂载的话，是将容器内的目录内容拷贝到宿主机里数据卷所在的目录，是宿主机遵循容器的内容，这是需要注意的。

那么我如何知道这个容器属于数据卷挂载还是目录挂载呢？用docker inspect 容器id或容器名称，可以查看到容器的元数据，以下图1和图2分别是SecondNginx和PathNginx的元数据（节选）：

```json
{
"Mounts": [
            {
                "Type": "volume",
                "Name": "nginxHtml",
                "Source": "/var/lib/docker/volumes/nginxHtml/_data",
                "Destination": "/usr/share/nginx/html",
                "Driver": "local",
                "Mode": "z",
                "RW": true,
                "Propagation": ""
            }
        ]
}
```

```json
{
"Mounts": [
            {
                "Type": "bind",
                "Source": "/nginxHtml",
                "Destination": "/usr/share/nginx/html",
                "Mode": "",
                "RW": true,
                "Propagation": "rprivate"
            }
        ]
}
```

通过Mounts节点的type字段可以确定是数据卷挂载还是目录挂载。不管是哪种挂载，通过Source字段都能定位到宿主机被挂载的目录。
