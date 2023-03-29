> 作为一名服务器开发者，平时学习、工作中难免需要配置自己的服务器环境、甚至不同的项目需要不同的自定义环境；Docker技术的诞生，可以很好地将我们需要的服务器环境打包起来，以避免重复配置或配置出错导致的各种服务器错误；本章就来实现并记录一下：如何将自定义的Docker容器打包成镜像，实现环境的移植和重复使用。
注：如果对docker还不太理解，或者一些基础命令还需要讲解的，请先看一下-》 Docker笔记1——入门（初识+搭建+使用）：https://learnku.com/articles/47254
虚拟机环境：centos7
> 
## 一、进入docker容器
```
//安装docker
$ yum -y install docker
//启动服务
$ systemctl start docker
//从docker镜像库拉取centos7镜像
$ docker pull centos:centos8.2.2004
//查看镜像
$ docker images
//运行容器
$ docker run -itd --name centos-one centos:centos8.2.2004
//查看容器
$ docker ps
//进入容器
$ docker exec -it [容器ID:57cb4276a2f9] /bin/sh
```
![Docker笔记2——保存自己的LNMP](https://cdn.learnku.com/uploads/images/202007/24/66392/cXrqljEAyT.png!large)
PS:如果到在这之前卡住了，请先参考：Docker笔记1——入门（初识+搭建+使用）：https://learnku.com/articles/47254

## 二、在容器里搭建lnmp
### 2.1、安装并启动nginx
```
//安装nginx
$ yum install nginx
//启动nginx，如果找不到自己的nginx启动脚本，可以用find / -name nginx
$ ./usr/sbin/nginx
//测试nginx
$ curl 127.0.0.1
```

![Docker笔记2——保存自己的LNMP](https://cdn.learnku.com/uploads/images/202007/24/66392/j71PS6s92Z.png!large)

![Docker笔记2——保存自己的LNMP](https://cdn.learnku.com/uploads/images/202007/24/66392/U1NcgGm03G.png!large)

### 2.2、安装PHP
```
//安装PHP
$ yum install php
```

![Docker笔记2——保存自己的LNMP](https://cdn.learnku.com/uploads/images/202007/24/66392/mb8LCT94ob.png!large)

### 2.3、准备一个php文件用作校验
```
$ cd /usr/share/nginx/html
$ echo "<?php phpinfo();" >> index.php
```
### 2.4、启动fpm
```
//找到fpm
$ find / -name php-fpm
//启动fpm
$ /usr/sbin/php-fpm
//查看进程
$ ps -aux | grep php-fpm
```

![Docker笔记2——保存自己的LNMP](https://cdn.learnku.com/uploads/images/202007/27/66392/LJl2XJt2Xe.png!large)

### 2.5、配置nginx关联fpm
```
//找到nginx.conf
$ find / -name nginx.conf
```
```
location ~ \.php(.*)$ {
	fastcgi_pass   127.0.0.1:9000;
}
```

![Docker笔记2——保存自己的LNMP](https://cdn.learnku.com/uploads/images/202007/27/66392/7z82dNHJVE.png!large)

```
// 重启nginx
$ ./usr/sbin/nginx -s reload
// 测试一下
$ curl 127.0.0.1:80/index.php
```

![Docker笔记2——保存自己的LNMP](https://cdn.learnku.com/uploads/images/202007/27/66392/IfjIgJ6P3m.png!large)

## 三、保存容器
```
// 退出容器
$ exit;
// 查看容器
$ docker ps
// 根据容器id生成镜像(-m 镜像名 -a 作者名 [容器id] [新容器标签])
$ docker commit -m 'my_lnmp' -a 'guoqf' 57cb4276a2f9 centos:guoqf_lnmp
// 用save命令将镜像保存
$ docker save centos:guoqf_lnmp > /root/guoqf_lnmp.tar
```

![Docker笔记2——保存自己的LNMP](https://cdn.learnku.com/uploads/images/202007/27/66392/wGHWJzwNGM.png!large)

![Docker笔记2——保存自己的LNMP](https://cdn.learnku.com/uploads/images/202007/28/66392/cngviDZpiU.png!large)



## 四、测试镜像
### 4.1、删除镜像并重启docker
```
// 删除镜像
$ docker rmi
// 重启docker服务
$ systemctl restart docker
```

![Docker笔记2——保存自己的LNMP](https://cdn.learnku.com/uploads/images/202007/28/66392/PEtbp6lNXr.png!large)


### 4.2、运行容器
```
// 导入镜像
$ docker load < gqf_lnmp.tar
// 运行容器
$ docker run -itd --name centos-4 centos/guoqf_lnmp:latest
// 进入容器
$ docker exec -it [容器ID] /bin/sh
```

### 4.3、重新启动fast-cgi、lnmp（命令上面有，这里就不再重复写了）

### 4.4、查看容器ip地址
```
$ ifconfig
$ exit
```

![Docker笔记2——保存自己的LNMP](https://cdn.learnku.com/uploads/images/202007/28/66392/3trundr4yI.png!large)

### 4.4、通过宿主机器访问服务

![Docker笔记2——保存自己的LNMP](https://cdn.learnku.com/uploads/images/202007/28/66392/5Jyqf1SliO.png!large)


### 4.5、通过外网IP访问服务

![Docker笔记2——保存自己的LNMP](https://cdn.learnku.com/uploads/images/202007/28/66392/cw2cMXYMBz.png!large)

这个时候发现docker的服务并不能直接暴露在外，经过一番了解后，docker的网络原理大致如下图：

![Docker笔记2——保存自己的LNMP](https://cdn.learnku.com/uploads/images/202007/28/66392/CHJSO5TwTF.png!large)
 查阅资料之后，发现我们可以在生成容器的时候，进行一个（宿主机与容器的）端口映射，也就是在run命令后面加 -p

## 五、端口映射
宿主机：
```
$ docker run -itd --name centos-4 -p 80:80 centos/guoqf_lnmp:latest
```

![Docker笔记2——保存自己的LNMP](https://cdn.learnku.com/uploads/images/202007/28/66392/0i1HIN5L97.png!large)

最后再试下访问：

![Docker笔记2——保存自己的LNMP](https://cdn.learnku.com/uploads/images/202007/28/66392/lhJAGi1XWs.png!large)

至此，我们已经学会了如何用docker搭建自己的项目运行环境并将环境保存起来，以便进行后续的移植和快速搭建；
这种方式是将镜像保存在自己的服务器中，隐秘性和安全性相对比较高，单机操作成本较低；
下一期我们再研究下如何用dockerfile的方式，将自己的镜像上传，以便应对服务器集群环境的快速搭建吧！~

## 参考文献&工具库：
1）[dockerHub](https://hub.docker.com/)
2）[教你如何修改运行中的docker容器的端口映射](https://www.cnblogs.com/shijf/p/10386193.html)
3) [Docker安装一个LNMP全过程](https://www.jianshu.com/p/243849fa218e)