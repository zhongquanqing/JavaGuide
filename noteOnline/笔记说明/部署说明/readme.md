* 文档利用了nginx代理转发静态资源，你也可以用任意的服务器来响应静态资源
1. 利用docker 安装 nginx 镜像
	```cmd
docker pull hub.c.163.com/library/nginx:latest
```
2.  启动docker实例：nginx
	```cmd
docker run --name {nginxServername} -v {yourStaticFileDir}:/usr/share/nginx/html:ro -p {expectPort}:{nginxPort:default80} -d nginx
```
egg:
	```cmd
docker run --name nginx-staticForNote -v /Notes:/usr/share/nginx/html:ro -p 3000:80 -d nginx
```
