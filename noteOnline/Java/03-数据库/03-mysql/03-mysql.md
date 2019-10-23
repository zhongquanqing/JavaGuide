# 第一章   POSTMAN开发工具安装
## 1.1第一步安装：谷歌助手安装

## 1.2.安装Postman Interceptor插件
    Interceptor主要就是用来做请求的拦截。
    
### 1.2.1  进入谷歌商店
![进入谷歌商店](image/03-mysql/01-扩展程序.png)

    注：其中的Filter requests参数可以自己配置，如：localhost:8081表示只拦截本地的8081
    端口的请求。默认的配置.*配置拦截所有请求，可以使用默认配置不用修改

## 1.3 登录系统
    Interceptor配置完后，在浏览器中正常登录被测试的系统(这很重要)。只需要按正常  
    的流程登录即可。
    
## 1.4 拦截器配置
    在Postman中启用Interceptor，如下图：

![进入谷歌商店](image/03-mysql/02-Interceptor.png)


    至此通过Postman发送需要登录或授权的URL请求时已配置完成

    

    