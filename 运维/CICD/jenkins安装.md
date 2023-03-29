jenkins安装

使用docker安装：
```shell
docker pull jenkins/jenkins:lts
```


启动命令
```shell
docker run -d --name jenkins -p 8080:8080 -v /Users/guoqingfeng/dev_tools/dnmp/www/jenkins_home:/home/jenkins_home jenkins/jenkins:lts
```

