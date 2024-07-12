---
title: Docker
date: 2021-09-22 14:54:54
tags: docker
categories: docker
---

### 安装docker

安装命令

~~~linux
curl -fsSL https://get.docker.com | bash -s docker --mirror Aliyun
~~~

docker命令

~~~java
//启动docker
systemctl start docker
//检查docker版本
docker -v
//查看docker已有镜像
sudo docker images
//设置docker开机启动
sudo systemctl enable docke
~~~

#### 设置国内镜像

~~~sh
# 创建文件
sudo mkdir -p /etc/docker
# 修改配置, 设置镜像
sudo tee /etc/docker/daemon.json <<-'EOF'
{
  "registry-mirrors": ["https://vw9qapdy.mirror.aliyuncs.com"]
}
EOF
# 重启后台线程
sudo systemctl daemon-reload
# 重启docker
sudo systemctl restart docker
~~~

#### 安装容器

查找容器

~~~sh
docker search mysql
~~~

#### 安装mysql

~~~sh
# docker安装mysql
sudo docker pull mysql:5.7
 
# docker启动mysql
sudo docker run -p 3306:3306 --name mysql \
-v /mydata/mysql/log:/var/log/mysql \
-v /mydata/mysql/data:/var/lib/mysql \
-v /mydata/mysql/conf:/etc/mysql \
-e MYSQL_ROOT_PASSWORD=root   //设置密码
-d mysql:5.7
~~~

##### 配置mysql

~~~sh
# 进入挂载的mysql配置目录
cd /mydata/mysql/conf
 
# 修改配置文件 my.cnf
vi my.cnf
 
# 输入以下内容
# i(插入模式)
[client]
default-character-set=utf8
[mysql]
default-character-set=utf8
[mysqld]
init_connect='SET collation_connection = utf8_unicode_ci'
init_connect='SET NAMES utf8'
character-set-server=utf8
collation-server=utf8_unicode_ci
skip-character-set-client-handshake
skip-name-resolve
 
# Esc
# :wq
 
# docker重启mysql使配置生效
docker restart mysql
~~~

##### 进入容器

~~~sh
docker exec -it mysql bash
~~~

##### 登录mysql

~~~sh
mysql -uroot -proot
