### 1.3 Docker组件

#### 1.3.1 Docker客户端和服务器

​	Docker是一个客户端-服务器（C/S）架构程序。Docker客户端只需要向Docker服务器或者守护进程发出请求，服务器或者守护进程将完成所有工作并返回结果。Docker提供了一个命令行工具Docker以及一整套RESTful API。你可以在同一台宿主机上运行Docker守护daemon进程和客户端，也可以从本地的Docker客户端连接到运行在另一台宿主机上的远程Docker守护进程。

![1539922226164](E:/daybyday/02Web/day75%20%20docker/%E8%AE%B2%E4%B9%89/images/1539922226164.png)

#### 1.3.2 Docker镜像

​	镜像是构建Docker的基石。用户基于镜像来运行自己的容器。镜像也是Docker生命周期中的“构建”部分。镜像是基于联合文件系统的一种层式结构，由一系列指令一步一步构建出来。例如：镜像可以是mysql tomcat redis 
添加一个文件；
执行一个命令；
打开一个窗口。
也可以将镜像当作容器的“源代码”。镜像体积很小，非常“便携”，易于分享、存储和更新。

#### 1.3.3 Registry（中央仓库）

Docker用Registry来保存用户构建的镜像。Registry分为公共和私有两种。Docker公司运营公共的Registry叫做Docker Hub。用户可以在Docker Hub注册账号，分享并保存自己的镜像（说明：在Docker Hub下载镜像巨慢，可以自己构建私有的Registry）。

#### 1.3.4 Docker容器

Docker可以帮助你构建和部署容器，你只需要把自己的应用程序或者服务打包放进容器即可。容器是基于镜像启动起来的，容器中可以运行一个或多个进程。我们可以认为，镜像是Docker生命周期中的构建或者打包阶段，而容器则是启动或者执行阶段。  容器基于镜像启动，一旦容器启动完成后，我们就可以登录到容器中安装自己需要的软件或者服务。

![1539922269261](E:/daybyday/02Web/day75%20%20docker/%E8%AE%B2%E4%B9%89/images/1539922269261.png)



所以Docker容器就是：
一个镜像格式；
一些列标准操作；
一个执行环境。
Docker借鉴了标准集装箱的概念。标准集装箱将货物运往世界各地，Docker将这个模型运用到自己的设计中，唯一不同的是：集装箱运输货物，而Docker运输软件。

​	和集装箱一样，Docker在执行上述操作时，并不关心容器中到底装了什么，它不管是web服务器，还是数据库，或者是应用程序服务器什么的。所有的容器都按照相同的方式将内容“装载”进去。
​	Docker也不关心你要把容器运到何方：我们可以在自己的笔记本中构建容器，上传到Registry，然后下载到一个物理的或者虚拟的服务器来测试，在把容器部署到具体的主机中。像标准集装箱一样，Docker容器方便替换，可以叠加，易于分发，并且尽量通用。
​	使用Docker，我们可以快速的构建一个应用程序服务器、一个消息总线、一套实用工具、一个持续集成（CI）测试环境或者任意一种应用程序、服务或工具。我们可以在本地构建一个完整的测试环境，也可以为生产或开发快速复制一套复杂的应用程序栈。



#### 1.3.5 理解图

![1539922943504](E:/daybyday/02Web/day75%20%20docker/%E8%AE%B2%E4%B9%89/images/1539922943504.png)



registry:中央注册中心

images:就是下载镜像文件

client:就是操作docker的客户端（命令）

containter:就是docker容器  需要运行在doker服务中

官方图如下:

![1577278469974](E:/daybyday/02Web/day75%20%20docker/%E8%AE%B2%E4%B9%89/images/1577278469974.png)

### 3.2列出镜像

列出docker下的当前docker服务所在的系统里面所有镜像：docker images

### 3.3搜索镜像

如果你需要从网络中查找需要的镜像，可以通过以下命令搜索: docker search 镜像名称

**拉取镜像**

```shell
docker pull centos:7
```

目前国内访问docker hub速度上有点尴尬，使用docker Mirror势在必行。

### 3.5 删除镜像

删除镜像方式1：根据仓库的名称（镜像的名称）来删除 还可以使用image_id来进行删除。

```
1、	docker rmi $IMAGE_ID：删除指定镜像
```

删除镜像方式2：

```
2、	docker rmi `docker images -q`：删除所有镜像
```

### 4.1查看容器

- 查看正在运行容器：

```shell
docker ps
```

- 查看所有的容器（启动过的历史容器）

```shell
docker ps –a
```

- 查看最后一次运行的容器：

```shell
docker ps -l
```

![1539939270559](E:/daybyday/02Web/day75%20%20docker/%E8%AE%B2%E4%B9%89/images/1539939270559.png)



- 查看停止的容器

```shell
docker ps -f status=exited
```

### 4.2 创建与启动容器

l  创建容器常用的参数说明：

l  创建容器命令：docker run

l  -i：表示运行容器

l  -t：表示容器启动后会进入其命令行。加入这两个参数后，容器创建就能登录进去。即分配一个伪终端。

l  --name :为创建的容器命名。

l  -v：表示目录映射关系（前者是宿主机目录，后者是映射到宿主机上的目录），可以使用多个－v做多个目录或文件映射。注意：最好做目录映射，在宿主机上做修改，然后共享到容器上。

l  -d：在run后面加上-d参数,则会创建一个守护式容器在后台运行（这样创建容器后不会自动登录容器，如果只加-i -t两个参数，创建后就会自动进去容器）。

l  -p：表示端口映射，前者是宿主机端口，后者是容器内的映射端口。可以使用多个－p做多个端口映射

#### 4.1.1创建交互式容器

创建一个交互式容器并取名为mycentos

```shell
docker run -it --name=mycentos centos:7 /bin/bash
```

退出当前容器：

```shell
exit
```

#### 4.1.2守护式容器

创建一个守护式容器：如果对于一个需要长期运行的容器来说，我们可以创建一个守护式容器

命令如下（容器名称不能重复）：

```shell
docker run -di --name=mycentos2 centos:7
```

### 4.3停止与启动容器

- 停止正在运行的容器：docker stop $CONTAINER_NAME/ID

```shell
docker stop mycentos2
```

- 启动已运行过的容器：docker start $CONTAINER_NAME/ID

```shell
docker start mycentos2
```

### 4.4文件拷贝

如果我们需要将文件拷贝到容器内可以使用cp命令:

```
docker cp 需要拷贝的文件或目录 容器名称:容器目录
```

也可以将文件从容器内拷贝出来

```
docker cp 容器名称:容器目录 需要拷贝的文件或目录
```

### 4.5目录挂载（映射）

我们可以在创建容器的时候，将宿主机的目录与容器内的目录进行映射，这样我们就可以通过修改宿主机某个目录的文件从而去影响容器里所对应的目录。

```
创建容器 添加-v参数 后边为   宿主机目录:容器目录
```

创建容器 并挂载宿主机目录 到容器中的目录下：

```shell
docker run -di -v /usr/local/myhtml:/usr/local/myhtml --name=mycentos3 centos:7
```

### 4.6查看容器IP地址

我们可以通过以下命令查看容器运行的各种数据:

```shell
docker inspect mycentos2
```

### 4.7删除容器

- 删除指定的容器： 这个命令只能删除已经关闭的容器，不能删除正在运行的容器

```
docker rm $CONTAINER_ID/NAME
```

- 删除所有的容器：

```shell
docker rm `docker ps -a -q`
```

### 5.1MySQL部署

#### 5.1.1拉取MySQL镜像

```shell
docker pull mysql
```

查看镜像：

![1539957969383](E:/daybyday/02Web/day75%20%20docker/%E8%AE%B2%E4%B9%89/images/1539957969383.png)



#### 5.1.2创建MySQL容器

```shell
docker run -di --name=pinyougou_mysql -p 33306:3306 -e MYSQL_ROOT_PASSWORD=123456 mysql:5.7
```

-p 代表端口映射，格式为  宿主机映射端口:容器运行端口
-e 代表添加环境变量  MYSQL_ROOT_PASSWORD是root用户的登陆密码



#### 5.1.3进入MySQL容器

- 进入容器中

```shell
docker exec -it pinyougou_mysql /bin/bash
```

- 登录mysql

```
mysql -u root -p

```

- 授权允许远程登录

```shell
GRANT ALL PRIVILEGES ON *.* TO 'root'@'%' IDENTIFIED BY '123456' WITH GRANT OPTION;
```

## 6.备份与迁移

### 6.1容器保存为镜像

我们可以通过以下命令将容器保存为镜像

```shell
docker commit pinyougou_nginx mynginx
```

pinyougou_nginx是容器名称
mynginx是新的镜像名称
此镜像的内容就是你当前容器的内容，接下来你可以用此镜像再次运行新的容器



### 6.2镜像备份

```shell
docker  save -o mynginx.tar mynginx
```

-o 输出到的文件

执行后，运行ls命令即可看到打成的tar包.



### 6.3镜像恢复与迁移

首先我们先删除掉mynginx镜像,然后执行命令进行恢复

```shell
docker load -i mynginx.tar
```

-i 输入的文件

执行后再次查看镜像，可以看到镜像已经恢复

再创建容器。