# Dokcer使用

## mysql8.0.16

1. 下载， 启动镜像

   ```shell
   docker pull mysql:8.0.16
   docker run -p 3306:3306 --name mysql -e MYSQL_ROOT_PASSWORD=123456  -d mysql:8.0.16
   ```

2. 拷贝这个容器的配置文件

   ```shell
   docker cp mysql:/etc/mysql /root/my_docker/mysql8.0.16/
   ```

3. 删除容器

   ```bash
   docker stop mysql
   docker rm mysql
   ```

4. 编写启动脚本

   ```shell
   #!/bin/sh
   docker run \
   -p 3308:3306 \ #主机3308→容器3306端口映射
   --name mysql \
   --privileged=true \ #挂载文件权限设置
   --restart unless-stopped \ #设置 开机后自动重启容器
   -v /root/my_docker/mysql8.0.16/mysql:/etc/mysql \ # 挂载配置文件
   -v /root/my_docker/mysql8.0.16/logs:/logs \	# 挂载日志
   -v /root/my_docker/mysql8.0.16/data:/var/lib/mysql \ # 挂载数据文件 持久化到主机
   -v /etc/localtime:/etc/localtime \ #容器时间与宿主机同步
   -e MYSQL_ROOT_PASSWORD=123456 \ # 设置密码
   -d mysql:8.0.16 # 后台启动mysql
   ```

5. 运行脚本

   ```shell
   sh 脚本名.sh
   ```

6. navicat连接测试

## nginx

```shell
# 下载镜像
docker pull nginx
# 复制配置
docker run -d --name nginx nginx


```



docker compose

```yaml
#compose版本
version: "3"  
 

services:
  yj_blog:
#微服务镜像  
    image: yj_blog:1.0
    container_name: yj_blog
    ports:
      - "7777:7777"
#数据卷
    volumes:
      - /app/yj_blog:/data/yj_blog
    networks: 
      - blog_network
    depends_on: 
      - redis
      - mysql
      - nginx


  yj_admin:
#微服务镜像
    image: yj_admin:1.0
    container_name: yj_admin
    ports:
      - "8989:8989"
#数据卷
    volumes:
      - /app/yj_admin:/data/yj_admin
    networks:
      - blog_network
    depends_on:
      - redis
      - mysql
      - nginx
     
#redis服务
  redis:
    image: redis:6.0.8
    ports:
      - "6379:6379"
    volumes:
      - /app/redis/redis.conf:/etc/redis/redis.conf
      - /app/redis/data:/data
    networks: 
      - blog_network
    command: redis-server /etc/redis/redis.conf
 
 #mysql服务
  mysql:
    image: mysql:8.0.19
    environment:
      MYSQL_ROOT_PASSWORD: 'xu.123456'
      MYSQL_ALLOW_EMPTY_PASSWORD: 'no'
      MYSQL_DATABASE: 'yj_blog'
      MYSQL_USER: 'root'
      MYSQL_PASSWORD: 'xu.123456'
    ports:
       - "3306:3306"
    volumes:
       - /app/mysql/db:/var/lib/mysql
       - /app/mysql/conf/my.cnf:/etc/my.cnf
       - /app/mysql/init:/docker-entrypoint-initdb.d
    networks:
      - blog_network
    command: --default-authentication-plugin=mysql_native_password #解决外部无法访问

 #nginx服务
  nginx:
    image: nginx:1.18.0
    ports:
      - "80:80"
      - "8093:8093"
      - "8094:8094"
    volumes:
      - /app/nginx/html:/usr/share/nginx/html
      - /app/nginx/logs:/var/log/nginx
      - /app/nginx/conf:/etc/nginx
    networks:
      - blog_network
    

 
 #创建自定义网络
networks: 
   blog_network: 

```

