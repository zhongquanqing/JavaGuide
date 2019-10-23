# 第一章 Monogdb
## 1.1. Monogdb数据库迁移

###1.1.1.Liunx服务器monogdb数据库迁移
    1.root用户登录服务器；
    2.切换到monogdb的bin目录：cd /usr/local/mongodb，该目录按照实际目录进行调整；
    3.执行导出命令，执行命令前，需切换到monogdb的bin目录：mongodump -d mongodb -o /opt/mongo_ibase20181101
    备注：mongodump导出命令，mongodb是数据库名称（数据库名称要区分大小写），/opt/mongo_ibase20181101导出文件路径。
    4.执行导入命令，执行命令前，需切换到monogdb的bin目录（导入前，必须数据库已存在）：mongorestore -d mongodb /opt/mongo_ibase20181101
    备注：mongorestore导入命令，mongodb是数据库名称（数据库名称要区分大小写），/opt/mongo_ibase20181101导出文件路径。导入前，一定要确保mongodb已存在
    
### 1.1.2.Windows服务器monogdb数据库迁移
    1.administrator用户登录服务器；
    2.cmd进入dos环境；
    3.切换到monogdb的bin目录：切换到monogdb的bin目录，该目录按照实际目录进行调整；
    执行导出命令，执行命令前，需切换到monogdb的bin目录：mongodump -d ysl -o D:\opt\20190117
    备注：mongodump导出命令，ysl是数据库名称（数据库名称要区分大小写），D:\opt\20190117导出文件路径。
    4.执行导入命令，执行命令前，需切换到monogdb的bin目录（导入前，必须数据库已存在）：mongorestore -d ysl D:\opt\20190117
    备注：mongodump导出命令，ysl是数据库名称（数据库名称要区分大小写），D:\opt\20190117导出文件路径。
    

    