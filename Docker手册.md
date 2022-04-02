# Redis 容器

```powershell
docker run -d -p 6379:6379 --name redis redis:latest
```

其含义是：

**docker run** 表示 docker 运行一个软件

**-d** 表示在后台运行

**-p** 表示端口的暴露，如上 **-p 6379:6379** 表示将 docker 容器内的端口6379暴露到宿主机的6379端口上

**-name redis** 给此容器命名为redis

**redis:latest** 表示使用此软件源的最新版本



使用挂载本地目录，使用本地目录的设置

```powershell
docker run -p 6379:6379 --name redis -v C:/CentOS/redis-6.2.6:/etc/redis/redis.conf  -v C:/CentOS/redis-6.2.6/data:/data -d redis:6.2.6 redis-server /etc/redis/redis.conf --appendonly yes --port 637
```

```sh
docker run -d --privileged=true -p 6379:6379 -v /opt/redis/redis.conf:/etc/redis/redis.conf -v /opt/redis/data:/data --name redis redis:latest redis-server /etc/redis/redis.conf --appendonly yes
```





# RabbitMQ 容器

rabbitmq需要以下端口：

**15672** —— （if management plugin is enabled）

**15671** —— management 监听端口，前台浏览器的控制界面

**5672**, **5671** —— （AMQP 0-9-1 without and with TLS）

**4369** —— （epmd）epmd 代表 Erlang 端口映射守护进程

**25672** —— （Erlang distribution）



使用命令

```powershell
docker run -di --name rabbitmq -p 5672:5672 -p 5671:5671 -p 4369:4369 -p 15671:15671 -p 15672:15672 -p 25672:25672 rabbitmq:management
```



# Mysql 容器

```sh
docker run -p 3306:3306 --name mysql -v $PWD/conf:/etc/mysql/conf.d -v $PWD/logs:/logs -v $PWD/data:/var/lib/mysql -e MYSQL_ROOT_PASSWORD=w --privileged -d mysql
```

在/tmp/mysql/conf目录添加一个my.cnf文件，作为mysql的配置文件：

```sh
# 创建文件
touch /tmp/mysql/conf/my.cnf
```

文件的内容如下：

```ini
[mysqld]
skip-name-resolve
character_set_server=utf8
datadir=/var/lib/mysql
server-id=1000
```

配置修改后，必须重启容器：

```sh
docker restart mysql
```



# 目录挂载

## 三种挂载方式

+   `bind mount` —— 直接把宿主机目录映射到容器内，适合挂代码目录和配置文件。可挂到多个容器上
+   `volume` —— 由容器创建和管理，创建在宿主机，所以删除容器不会丢失，官方推荐，更高效。可挂到多个容器上
+   `tmpfs mount` —— 适合存储临时文件，存储于宿主机内存中。不可多容器共享

![image-20220223204103571](C:\Users\PC\AppData\Roaming\Typora\typora-user-images\image-20220223204103571.png)



## 挂载方法

**bind mount** 方式用绝对路径 `-v D: /code:/app`

**volume** 方式只需要一个名字 `-v db-data:/app`



示例：

```powershell
docker run -p 8080:8080 --name test-hello -v D:/code:/app -d test:v1
```





# 多容器通信

使得各个组件相互配合，要让他们在同一个网络中

## 创建虚拟网络方法

### 创建一个网络

```powershell
docker network create V-net1
```



### 运行 Redis 在创建的网络中

```powershell
docker run -d --name redis --network V-net1 --network-alias redisNT redis:latest
```



### 在其他容器内访问Redis

地址可以使用上一步设置的 redis网络 的别名 redisNT

```javascript
const redis = require('redis');
let rds = redis.createClient({url: "redis://redisNT:6379"});
rds.on('connect', ()=> console.log('redis connect ok'))
rds.connect();
```



### 让其他容器在创建的网络上运行

```powershell
docker run -it -p 8080:8080 --name test-hello --network V-net1 -v C:\Users\PC\Desktop\test:/app -d test:v1
```





# Docker Compose

通过docker-compose，可以方便地将多个服务集合到一起，一键运行



## docker-compose.yml

### 编写yml文件

要把项目依赖的多个服务集合在一起，需要编写一个 `docker-compose.yml` 文件，描述依赖哪些服务

```yaml
version: "3.7"

services:
  app:
    build: ./
    ports:
      - 80:8080
    volumes:
      - ./:/app
    environment:
      - TZ=Asia/Shanghai
  redis:
    image: redis:5.0.13
    volumes:
      - redis:/data
    environment:
      - TZ=Asia/Shanghai

volumes:
  redis:

```

容器默认的不是北京时间，增加 `TZ=Asia/Shanghai` 改为北京时间

集成后容器的名字为该文件处在目录的名称

`build: ./`  表示app这个服务在当前文件夹下建立

通过 `ports` 来设置端口映射

app 连接的网络别名为下面的 `redis`



### 执行文件

在 docker-compose.yml 文件所在目录，执行

```powershell
docker-compose up -d
```

即可将服务集合起来



## docker-compose 的其他命令

**查看运行状态：**`docker-compose ps`
**停止运行：**`docker-compose stop`
**重启：**`docker-compose restart`
**重启单个服务：**`docker-compose restart service-name`
**进入容器命令行：**`docker-compose exec service-name sh`
**查看容器运行log：**`docker-compose logs [service-name]`



# 安装CentOS

在终端中使用命令

```powershell
 docker run -itd --name centos --network host --privileged=true -v C:/CentOS:/app wanshubin/centos:r /sbin/init
```

启动 centos 后进入

```powershell
 docker exec -it centos /bin/bash
```



# 将容器打包为镜像并上传

自己打包的镜像一定要加上用户名前缀，否则不能上传

```powershell
docker commit -a "wanshubin" -m "配置好Redis的CentOS" centos wanshubin/centos:ready
```

commit之后会得到一串公钥，将其配置到docker中然后登录账户即可连接

将镜像上传到docker hub

```powershell
docker push wanshubin/centos:ready
```

