# 第一章   docker  nginx
tomcat ibase

## 1.1文档利用了nginx代理转发静态资源，你也可以用任意的服务器来响应静态资源

## 利用docker 安装 nginx 镜像

```cmd
docker pull hub.c.163.com/library/nginx:latest
```

##  启动docker实例：nginx

```cmd
docker run --name {nginxServername} -v {yourStaticFileDir}:/usr/share/nginx/html:ro -p {expectPort}:{nginxPort:default80} -d nginx
```
## egg:

```cmd
docker run --name nginx-staticForNote -v /Notes:/usr/share/nginx/html:ro -p 3000:80 -d nginx
docker run --name  my-custom-nginx-containe -p 3000:80 -d nginx
```

systemctl restart firewalld

## docker 镜像实例

* 查看docker正在运行的容器

```cmd
    docker ps
``` 

* 查看所有容器

```cmd
    docker ps -all
```
##  docker 容器名称
* NoteNginx      在线文档nginx服务
* my-custom-nginx-container   80端口nginx服务

> 注意，docker仅仅只是为了方便nginx服务的启动，不代表docker仅仅只能启动nginx  

## 启动进程
*  启动docker容器
   systemctl  start docker

* docker ps -a    查看所有的进程
  docker start  进程号
  启动的进程包括两个，一个是3000端口，一个是80端口
   
  --挂载nginx
  docker run --name my-custom-nginx-container2  -v /home/wuyunlong/nginx-wechat-ibase/config-diy/nginx.conf:/etc/nginx/nginx.conf:ro -p 80:80 -d nginx

#### Docker下nginx安装与配置挂载
https://blog.csdn.net/qq_26641781/article/details/80883192