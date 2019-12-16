# 第一章 Monogdb
## 1.1. Monogdb数据库迁移

### 1.1.1.Liunx服务器monogdb数据库迁移
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
    
## 1.2. mongodb语句练习
### 1.2.1. 数据库练习脚本，将此脚本插入到mongodb数据库中，供下面语句的练习
    db.users.drop();
    var user1 = {
            "username" : "lison",
            "country" : "china",
            "address" : {
                    "aCode" : "411000",
                    "add" : "长沙"
            },
            "favorites" : {
                    "movies" : ["杀破狼2","战狼","雷神1"],
                    "cites" : ["长沙","深圳","上海"]
            },
            "age" : 18,
    	   "salary":NumberDecimal("18889.09"),
           "lenght" :1.79
    	    
    };
    var user2 = {
            "username" : "james",
            "country" : "English",
            "address" : {
                    "aCode" : "311000",
                    "add" : "地址"
            },
            "favorites" : {
                    "movies" : ["复仇者联盟","战狼","雷神1"],
                    "cites" : ["西安","东京","上海"]
            },
            "age" : 24,
           "salary":NumberDecimal("7889.09"),
           "lenght" :1.35
    };
    var user3 ={
            "username" : "deer",
            "country" : "japan",
            "address" : {
                    "aCode" : "411000",
                    "add" : "长沙"
            },
            "favorites" : {
                    "movies" : ["肉蒲团","一路向西","倩女幽魂"],
                    "cites" : ["东莞","深圳","东京"]
            },
            "age" : 22,
           "salary":NumberDecimal("6666.66"),
           "lenght" :1.85
    };
    var user4 =
    {
            "username" : "mark",
            "country" : "USA",
            "address" : {
                    "aCode" : "411000",
                    "add" : "长沙"
            },
            "favorites" : {
                    "movies" : ["蜘蛛侠","钢铁侠","蝙蝠侠"],
                    "cites" : ["青岛","东莞","上海"]
            },
            "age" : 20,
           "salary":NumberDecimal("6398.22"),
           "lenght" :1.77
    };
    
    var user5 =
    {
            "username" : "peter",
            "country" : "UK",
            "address" : {
                    "aCode" : "411000",
                    "add" : "TEST"
            },
            "favorites" : {
                    "movies" : ["蜘蛛侠","钢铁侠","蝙蝠侠"],
                    "cites" : ["青岛","东莞","上海"]
            },
           "salary":NumberDecimal("1969.88")
    };
    
    db.users.insert(user1);
    db.users.insert(user2);
    db.users.insert(user3);
    db.users.insert(user4);
    db.users.insert(user5);

### 1.2.2. 基础语句练习
     查询  
     1.查询喜欢的城市包含东莞和东京的user
     select * from users  where favorites.cites has "东莞"、"东京“
     db.users.find({ "favorites.cites" : { "$all" : [ "东莞" , "东京"]}})
     2.查询国籍为英国或者美国，名字中包含s的user
     db.users.find({ "$and" : [ { "username" : { "$regex" : ".*s.*"}} , { "$or" : [ { "country" : "English"} , { "country" : "USA"}]}]})
     修改 
     3.把lison的年龄修改为6岁
     update  users  set age=6 where username = lison' 
     db.users.updateMany({ "username" : "lison"},{ "$set" : { "age" : 6}},true)

     4.喜欢的城市包含东莞的人，给他喜欢的电影加入“小电影2”“小电影3”
     update users  set favorites.movies add "小电影2 ", "小电影3" where favorites.cites  has "东莞"
     db.users.updateMany({ "favorites.cites" : "东莞"}, { "$addToSet" : { "favorites.movies" : { "$each" : [ "小电影2 " , "小电影3"]}}},true)
     
     删除
     5.删除名字为lison的user
     delete from users where username = ‘lison’
     db.users.deleteMany({ "username" : "lison"} )

     6.删除年龄大于8小于25的user
     delete from users where age >8 and age <25
     db.users.deleteMany({"$and" : [ {"age" : {"$gt": 8}} , {"age" : {"$lt" : 25}}]})

### 1.2.3.查询选择器
|运算符类型|运算符|描述|
|:----:|:---:|:---:|
|青青河边草|2019年10月21日|2222
|青青河边草|

    