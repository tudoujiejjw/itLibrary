> 近几年Docker的流行，使越来越多的公司和程序员认识到它，那么docker到底是啥？
## 一、初识docker：
### 我记得在网络上看到一张图，大概是这么画的：
![初识docker及其搭建笔记](https://cdn.learnku.com/uploads/images/202007/15/66392/bgqBt2biJh.png!large)
以前我们要搭建一台服务器，基本都是花大量的成本去搭建一台物理服务器，然后再往里面堆大量的硬件+软件；
后来有了虚拟机技术，我们已经可以向专业的服务器供应商进行虚拟机（虚拟空间）的购买；
而容器的出现，让我们不止是“租房”，现在把房子租下来，我们可以更灵活地进行房间的划分和自定义，这样有什么好处呢？
1. 减少失误：我们可以提前设定好我们要的docker环境，减少运维重复搭建环境出现的失误及人力成本；
2. 更加轻量：我们只需要关注我们项目运行所需要的关键支持（比如centos、php、go、mysql等等），docker的镜像库将我们需要的系统环境的最小单位集成一个镜像文件，每次启动可以说是秒级的，同时也减少了许多客户机带有的、而我们搭建服务不需要的（比如IO、多客户端、各种系统支持等等，这些被选择性地抛弃或者依赖于客户机，而不需要每次搭建环境都需要包含进去）
3. 易迁移性：docker将依赖和应用代码都打包在Docker镜像中、方便迁移；

当然Docker还有不少优势，这里就不过多赘述，我们现在就开始docker的入门吧：

## 二、环境搭建
### 1、安装docker：
```
$ yum -y install docker
```

![初识docker及其搭建笔记](https://cdn.learnku.com/uploads/images/202007/15/66392/VoHGueVygc.png!large)
如出现“no more mirrors to try...”的错误，可能原因是不正当的删除造成的。
尝试：
```
$ yum clean all
$ yum makecache
$ yum -y update
```
### 2、启动服务
```
$ service docker start
```

![初识docker及其搭建笔记](https://cdn.learnku.com/uploads/images/202007/15/66392/uas2uKsE8n.png!large)
PS：顺道附上其他操作命令（初次安装有些不会用到，可以跳过）
docker pull [依赖包]
docker images #查看镜像
docker stop [容器名] #停止容器
docker restart [容器名] #重启showdoc容器
docker rm [容器名] #删除容器
doker rmi [镜像ID] #删除镜像（可以用images+rmi配合删除镜像，如下图：）

![初识docker及其搭建笔记](https://cdn.learnku.com/uploads/images/202007/15/66392/OU8jpqYtsf.png!large)

### 3、拉取指定版本的 CentOS 镜像，这里我们安装指定版本为例(centos8.2.2004)
- 3.1 打开docker的镜像库网址（https://hub.docker.com/_/centos?tab=tags&page=1）
- 3.2 找到最新的系统镜像版本

![初识docker及其搭建笔记](https://cdn.learnku.com/uploads/images/202007/15/66392/QBpoMrfK3s.png!large)

- 3.3 镜像拉取
```
$ docker pull centos:centos8.2.2004
```
- 3.4 拉取更多自定义的镜像
```
$ docker pull php:5.6-fpm-alpine3.8
$ docker pull nginx
$ docker pull mysql
```
- 3.5 查看拉取完的镜像

## 三、使用容器
### 1、运行容器
```
$ docker run -itd --name centos-one centos:centos8.2.2004

```


### 2、通过 docker ps 命令查看容器的运行信息
![docker 入门（初识 + 搭建 + 使用）](https://cdn.learnku.com/uploads/images/202007/20/66392/cUzG0JJ1zd.png!large)


本文对docker的初认识和基础命令就先写到这里；下一篇笔记写一下如何用docker打包出一套lnamp镜像并上传到镜像库吧，谢谢阅读~

参考文献：
《docker安装centos》：https://www.runoob.com/docker/docker-install-centos.html
《docker启动Ngnix》：https://www.jianshu.com/p/1eb3da3ceeea