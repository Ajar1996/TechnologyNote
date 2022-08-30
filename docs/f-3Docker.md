## Docker

### 常用命令

搜索镜像

```bash
docker search xxx  
```

下载镜像

```bash
docker pull java:lastest 
```

列出镜像

```bash
docker images 
```

指定名称删除镜像

```bash
docker rmi java:8  
```

强制删除

```bash
docker rmi -f java:8 
```

删除所有镜像

```bash
docker rmi -f $(docker images)  
```

新建并启动容器

```bash
docker run -p 80:80 --name nginx -d niginx:1.17.0
```

- -d ：后台运行
- - -name:指定容器名字
- -p : 指定映射端口

列出容器

```bash
docker ps 
docker ps -a 
```

停止容器

```bash
docker stop $ContainerName(或者id)
```

强制停止容器

```bash
docker kill $ContainerName(或者id)
```

启动容器

```bash
docker start $ContainerName(id)
```

进入容器

```bash
# 先查询出容器Pid
docker inspect  --format "{{.State.Pid}}" $ContainerName
# 根据容器pid进入容器：
nsenter --target "$pid" --mount  --uts --ipc --net --pid
```

删除容器

```bash
# 删除指定容器
docker rm $ContainerName(或者$ContainerId)
# 强制删除所有容器
docker rm -f $(docker ps -a -q)
```

查看日志

```bash
docker logs $ContainerName(id)
```

查看容器Ip地址

```bash
docker inspect --format '{{.Networksettings.IPAddress}}' $ContainerName(id)
```

同步宿主机时间到容器

```bash
docker cp /etc/localtime $ContainerName(id):/etc/
```

在宿主机查看docker使用cpu、内存、网络io情况

```bash
# 查看指定容器情况
docker stats $ContainerName(id）

docker stats -a
```

进入容器内部

```bash
docker exec -it $ContainerName /bin/bash
```

修改镜像存放位置

```bash
# 查看存放的位置
docker info | grep "Docker Root Dir"
# 关闭服务
systemctl stop docker
# 移动目录到目录路径
mv /var/lib/docker /mydata/docker
# 建立软连接
ln -s /mydata/docker /var/lib/docker
```

### 网络

Docker中的网桥类型

```bash
docker network ls
```



```bash
NETWORK ID          NAME                DRIVER              SCOPE
14b32fa46477        bridge              bridge              local
0dc5012bb939        host                host                local
295766cc4526        none                null                local
```

创建自定义网桥

```bash
docker network create -d bridge 网络名称
```

查看网络细节

```bash
docker network inspect  网络名
```

删除一个网络

```bash
docker network rm 网络名

#删除所有未使用到网络名
docker network prune 
```

运行多个容器指在指定网络中

- 启动容器时明确指定容器使用哪个网络

  ```bash
  docker run -d --network 网络名词
  ```

- 启动之后容器加入到某个网络中

  ```bash
  docker network connect 网络名 容器id(name)
  ```



### 数据卷

-v 宿主机路径:容器内路径



- 使用绝对路径

  `docker run -v /data/webapps:/usr/local/tomcat/webapps` 

- 使用别名方式（docker自身维护的数据卷）

  `docker run -v aaa:/usr/local/tomcat/webapps`



>  注意：
>
> ​	数据别名不存在时，会自动创建
>
> ​	第一次使用别名时将容器中的原始数据保留下来，使用绝对路径方式不会保留容器中的原始数据



- 查看所有docker维护的数据卷

  `docker volume ls`

- 查看数据卷详细内容

  `docker inspect 数据卷别名、网桥名词、容器名词`

  `docker network inspect 查看网桥详细`

  `docker volumes inspect 查看数据卷详细`

- 删除数据卷

  `docker volume rm 数据卷别名`

- 创建别名数据卷

  `docker volume create 数据卷别名`



### Dockerfile

镜像描述文件，通过Dockerfile文件构建一个属于自己镜像

`docker build -t aaa:1.0 .(指定Dockerfile文件位置)`



- FROM

构建镜像基于哪个镜像

- MAINTAINER

镜像维护者姓名或邮箱地址

- RUN

构建镜像时运行的指令

- CMD

运行容器时执行的shell环境

- VOLUME

指定容器挂载点到宿主机自动生成的目录或其他容器

- USER

为RUN、CMD、和 ENTRYPOINT 执行命令指定运行用户

- WORKDIR

为 RUN、CMD、ENTRYPOINT、COPY 和 ADD 设置工作目录，就是切换目录

- HEALTHCHECH

健康检查

- ARG

构建时指定的一些参数

- EXPOSE

声明容器的服务端口（仅仅是声明）

- ENV

设置容器环境变量

- ADD

拷贝文件或目录到容器中，如果是URL或压缩包便会自动下载或自动解压

- COPY

拷贝文件或目录到容器中，跟ADD类似，但不具备自动下载或解压的功能

- ENTRYPOINT

运行容器时执行的shell命令



### Docker-Compose

对 Docker 容器集群的快速编排，定义和运行多个 Docker 容器的应用

- 服务 (`service`)：一个应用的容器，实际上可以包括若干运行相同镜像的容器实例。
- 项目 (`project`)：由一组关联的应用容器组成的一个完整业务单元，在 `docker-compose.yml` 文件中定义。



#### yml模板

```yml
version: "3.8"		#指定本 yml 依从的 compose 哪个版本制定的

#管理一组服务
services:
  webapp:
    #build./ 	指定为构建镜像上下文路径context,其他都是默认值
    build:
      context: ./dir
      dockerfile: Dockerfile-alternate	#指定Dockerfile文件名称
      args:
        buildno: 1	#添加构建参数，这是只能在构建过程中访问的环境变量
      labels:	#设置构建镜像的标签
        - "com.example.description=Accounting webapp"
        - "com.example.department=Finance"
        - "com.example.label-with-empty-value"
      target: prod
    command: ["test.jar"] #覆盖容器启动之后的命令 类似于docker run images 覆盖命令
    networks:
      - ajar
    depends_on:     #解决容器启动先后问题
      - tomcat
      - mysql
    container_name: my-app-container #指定自定义容器名称，而不是生成的默认名称

  mysql:
    #指定使用的镜像
    image:  mysql:5.6
    ports:  #声明端口
      - "3306:3306"
    #添加环境变量
    #environment:
    #  - "MYSQL_ROOT_PASSWORD=root"
    #  MYSQL_ROOT_PASSWORD: root
    env_file: #环境变量文件的位置，内容MYSQL_ROOT_PASSWORD=root
      - ./.env
    networks: #声明网桥，不会自动创建
      - ajar
    volumes:
      - mysqlData:/var/lib/mysql
    restart: always #重启策略

#声明使用网桥
networks:
  ajar:
#声明别名数据卷
volumes:
  mysqlData:
```



#### Compose命令

如果没有特别的说明，命令对象将是项目，这意味着项目中所有的服务都会受到命令影响



##### 格式

`docker-compose [-f=<arg>...] [options] [COMMAND] [ARGS...]`



##### 命令选项

- `-f, --file FILE` 指定使用的 Compose 模板文件，默认为 `docker-compose.yml`，可以多次指定。
- `-p, --project-name NAME` 指定项目名称，默认将使用所在目录名称作为项目名
- `--verbose` 输出更多调试信息
- `-v, --version` 打印版本并退出



##### 命令使用说明

###### up

该命令十分强大，它将尝试自动完成包括构建镜像，重新创建服务，启动服务，并关联服务相关容器的一系列操作

```bash
#如果不输入服务id就是对整个项目操作
docker-compose up -d [服务id]
```



###### down

关闭所有容器，并移除网络

```bash
docker-compose down [服务id]	
```



###### config

验证Compose文件格式是否正确



###### exec

进入某个服务内部

```bash
docker-compose exec 服务id bash
```



###### images

列出 Compose 文件中包含的镜像。



###### logs

```bash
docker-compose logs [options] [SERVICE...]
```

查看服务容器的输出。默认情况下，docker-compose 将对不同的服务输出使用不同的颜色来区分。可以通过 `--no-color` 来关闭颜色。



###### ps

列出所有运行服务

```bash
docker-compose ps
```



###### restart

重启项目中的服务。

`docker-compose restart [options] [SERVICE...]`。

选项：

- `-t, --timeout TIMEOUT` 指定重启前停止容器的超时（默认为 10 秒）。



###### rm

删除所有（停止状态的）服务容器。推荐先执行 `docker-compose stop` 命令来停止容器

```bash
docker-compose rm [options] [SERVICE...]
```

选项：

- `-f, --force` 强制直接删除，包括非停止状态的容器。一般尽量不要使用该选项。
- `-v` 删除容器所挂载的数据卷。



###### top

查看各个服务容器内运行的进程



###### pause

暂停一个服务容器

```bash
docker-compose pause [SERVICE...]
```



###### unpause

恢复处于暂停状态中的服务

```bash
docker-compose unpause [SERVICE...]
```

