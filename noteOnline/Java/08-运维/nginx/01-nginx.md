# 第一章   docker  nginx
tomcat ibase

## 1.1文档利用了nginx代理转发静态资源，你也可以用任意的服务器来响应静态资源

### 1.1.1利用docker 安装 nginx 镜像

```cmd
docker pull hub.c.163.com/library/nginx:latest
```

###  1.1.2启动docker实例：nginx

```cmd
docker run --name {nginxServername} -v {yourStaticFileDir}:/usr/share/nginx/html:ro -p {expectPort}:{nginxPort:default80} -d nginx
```
### egg:

```cmd
docker run --name nginx-staticForNote -v /Notes:/usr/share/nginx/html:ro -p 3000:80 -d nginx
docker run --name  my-custom-nginx-containe -p 3000:80 -d nginx
```

## 1.2 docker 镜像实例

### 1.2.1查看docker正在运行的容器

```cmd
    docker ps
``` 

### 1.2.2查看所有容器

```cmd
    docker ps -all
```

> 注意，docker仅仅只是为了方便nginx服务的启动，不代表docker仅仅只能启动nginx  

## 1.3 启动进程（重要）
### 1.3.2  docker 常用命令(ps为备注内容)
     systemctl  start docker  ps:启动docker容器
   
     docker ps -a  ps:查看所有docker进程
     docker start  进程号  ps:启动docker进程,103包括两个，一个是3000端口，一个是80端口
     docker stop 进程号    ps:停止docker进程
     docker rm `docker ps -a -q`  ps:清除所有容器
     
      egg：--挂载nginx
     docker run --name my-custom-nginx-container2  -v /home/wuyunlong/nginx-wechat-ibase/config-diy/nginx.conf:/etc/nginx/nginx.conf:ro -p 80:80 -d nginx
      
     docker run -v /home/axureHtml:/usr/share/nginx/html  -p 3000:80 --name axure-nginx -d nginx:latest

  
> 偶尔需要启动一下防火墙：systemctl restart firewalld  
  

## 1.4 Docker下nginx安装与配置挂载
    https://blog.csdn.net/qq_26641781/article/details/80883192


