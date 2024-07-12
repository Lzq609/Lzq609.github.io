---
title: docker neo4j导入dump文件
date: 2020-12-18 15:00:00
tags: [docker,neo4j]
categories: [docker]
---
## docker neo4j导入dump文件

### 1、复制dump文件至neo4j的data目录下

~~~sh
docker cp /home/k8s/neo4j.dump neo4j:data
~~~

### 2、停止neo4j服务

~~~sh
docker stop neo4j
~~~

### 3、创建一个临时容器

~~~sh
# --rm 表示该退出容器时或容器命令执行完毕后，容器会自动删除
docker run --rm --name neo4j_temp -it -v /home/k8s/neo4j/data:/data neo4j /bin/bash
~~~

4、执行dump文件

~~~sh
neo4j-admin database load neo4j  --from-path=/data --overwrite-destination=true
~~~

5、退出容器

~~~sh
exit;
#重新启动neo4j服务
docker start neo4j
~~~
