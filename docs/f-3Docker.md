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