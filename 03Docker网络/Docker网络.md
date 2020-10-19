**docker0**
    输入ip addr或ifconfig,会看到docker0网卡。这是docker为我们生成的网卡，他用桥接或NAT的方式连接主机网卡。
    是容器与容器、容器与主机、容器与外网连接的桥梁，相当于一个路由器
**新建一个容器后，会发生什么？容器之间是怎么通信的？**
    docker会为容器新增一个成对的网卡，基于veth-pair技术，它是一对虚拟设备接口，一端连着容器，一端连着docker0
    容器启动时若不连接网络，都是指向docker0路由，docker会为容器分配一个可用IP。
    ![image-20201015212514503](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\image-20201015212514503.png)
    容器基于veth-pair生成的网卡与docker0通信，转发请求到其他容器或物理主机

**知识回顾：ip/16是什么意思？**
    192.168.0.1/16
    即ip前16位是固定死的，只有后16位是可变的（还得去掉回环地址与网关地址）
    同理192.168.0.1/24，代表只有后8位是可变的